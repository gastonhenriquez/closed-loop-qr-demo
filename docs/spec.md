# Especificación funcional (versión pública)

> Adaptación anonimizada de un documento de alcance real. Se omitieron cliente, personas, proveedores, identificadores de tarjeta y datos comerciales.

## Contexto

Un emisor regional de tarjetas, con autorizador propio, atiende a comercios regionales, municipios, cooperativas y mutuales. El objetivo es que el tarjetahabiente pague con QR en comercios adheridos y que el emisor gestione esos pagos de punta a punta, sin red internacional ni adquirente externo.

## Componentes

| Componente | Tipo |
|---|---|
| App Pagos para el tarjetahabiente (Android + iOS) | A desarrollar |
| App Cobros para el comercio (Android + iOS) | A desarrollar |
| Portal de Administración (backend + web) | A desarrollar |
| Autorizador del emisor | Sistema existente, integración por stored procedures |
| Sistema de emisión (planes de cuotas) | Sistema existente del emisor |
| KYC / biometría | Servicio externo vía SDK |

## App Pagos (tarjetahabiente)

**Onboarding e identidad**
* Registro con datos básicos, validación biométrica con prueba de vida y validación documental
* Login con email y contraseña, recuperación por email

**Alta de tarjeta**
* El usuario ingresa su tarjeta; la app valida con el autorizador enviando DNI + últimos 4 dígitos
* La app guarda **solo los últimos 4 dígitos**; el CVV no se pide ni se guarda
* Decisión de diseño: al no almacenar PAN completo ni CVV, la app queda fuera del alcance de tokenización PCI DSS. El PAN completo no se reconstruye en memoria en ningún momento

**Pago con QR estático, dos modos**
* **Modo 1, operación precargada:** el comercio cargó monto y plan de cuotas; la app los muestra y el usuario confirma
* **Modo 2, monto libre:** no hay operación pendiente; la app consulta en tiempo real los planes de cuotas vigentes del comercio, el usuario ingresa monto, elige plan y confirma
* Si la operación precargada se cancela o vence (timeout de 10 minutos, estándar de mercado para QR), la app pasa automáticamente a Modo 2
* Los datos leídos del QR no se persisten
* Historial de transacciones propias

## App Cobros y Portal (comercio)

* Dashboard de transacciones (QR y POS web)
* Visualización y descarga de sus QR estáticos
* Precarga de operación (monto + plan de cuotas) asociada a su terminal
* Cancelación de la operación precargada antes del escaneo
* Reversa de sus transacciones

## Portal de Administración (perfiles)

* **Procesador (superadmin):** alta de emisores, dashboard consolidado de todos los emisores, sincronización diaria de transacciones POS web
* **Emisor:** ABM de comercios, usuarios y planes de cuotas; generación de QR por comercio y terminal; dashboard propio; reversas
* **Comercio:** ver sección anterior

## Integraciones

* **Autorizador:** stored procedures, 60 a 70 conexiones simultáneas, SLA menor a 1 segundo. Dos instancias del autorizador (versión actual y nueva): el portal rutea por BIN
  * Validación de tarjetahabiente: DNI + últimos 4 → válida / inválida
  * Autorización: BIN + últimos 4 + monto + plan de cuotas → código de respuesta + número de autorización
* **Sistema de emisión:** consulta online de planes de cuotas vigentes por comercio (menos de 1 segundo)

## Flujo de pago

| # | Actor | Acción |
|---|---|---|
| 1 | Comercio | (Modo 1) Precarga monto + plan; la operación queda pendiente 10 minutos |
| 2 | Tarjetahabiente | Escanea el QR estático (comercio + terminal) |
| 3 | App → Portal | ¿Hay operación precargada para esta terminal? |
| 4 | Portal → App | Sí: devuelve monto y plan. No: consulta planes al sistema de emisión y la app pide monto y plan |
| 5 | Tarjetahabiente | Confirma el pago |
| 6 | Portal → Autorizador | Envía terminal + BIN + últimos 4 + monto + plan |
| 7 | Autorizador → App | Código de respuesta y número de autorización |

**Códigos de respuesta manejados:** `00` aprobada · `51` sin fondos · `12` inválida · `68` timeout

## Fuera de alcance del MVP

* Traductor ISO 8583 (alcance separado)
* Notificaciones push (fase 2)
* Infraestructura de servidores

## Supuestos y dependencias críticas

* El autorizador expone los dos stored procedures antes de iniciar desarrollo; sin el de validación, el onboarding queda bloqueado
* El sistema de emisión expone la consulta de planes; sin ella, el Modo 2 queda bloqueado
* Volumen inicial estimado: hasta 400 transacciones por día
* Branding por entidad emisora (white label)
