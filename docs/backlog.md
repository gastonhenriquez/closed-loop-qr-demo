# Backlog SAFe · Closed Loop QR

> Backlog derivado de la [especificación funcional](spec.md), con estructura SAFe: Epic → Features → Stories y Enablers. Las estimaciones (story points y WSJF) quedan para el equipo en el PI Planning. Versión importable: [`backlog.csv`](backlog.csv) (formato jerárquico de Azure DevOps, adaptable a Jira).

## Resumen

| Epic | Features | User Stories | Enablers | NFRs | Preguntas abiertas |
|---|---|---|---|---|---|
| 1 | 11 | 27 | 8 | 9 | 10 |

## EP-01 · Pagos con QR en circuito cerrado para emisor regional

**Epic hypothesis statement**

**Para** emisores regionales de tarjetas (comercios regionales, municipios, cooperativas y mutuales) **que** necesitan que sus tarjetahabientes paguen en comercios sin POS, **la** solución Closed Loop QR **es una** plataforma de pagos con QR sobre la red propia del emisor **que** permite autorizar, registrar y operar cada pago de punta a punta. **A diferencia de** aceptar a través de una red internacional o un adquirente externo, **esta solución** reduce el costo por transacción, mantiene en el emisor el dato completo de cada compra y reutiliza el autorizador existente.

**Resultados de negocio esperados**

* Tarjetahabientes del emisor pagan con QR en comercios adheridos sin plástico ni POS
* El emisor opera comercios, usuarios, planes de cuotas y QR desde un portal propio
* La plataforma se replica para otros emisores con branding propio (white label)

**Indicadores adelantados (leading indicators)**

* Tasa de aprobación de autorizaciones QR (código 00 sobre total)
* Tiempo de respuesta de autorización (percentil 95)
* Comercios con al menos una transacción QR en la semana
* Tarjetahabientes con onboarding completo y tarjeta dada de alta
* Proporción de pagos en Modo 1 (precargado) vs Modo 2 (monto libre)

**MVP**

App Pagos (Android + iOS) con onboarding, alta de tarjeta y pago en ambos modos; App Cobros; Portal con perfiles Procesador, Emisor y Comercio; integración con autorizador y sistema de emisión.

**Fuera de alcance**

* Traductor ISO 8583 (alcance separado)
* Notificaciones push (fase 2)
* Infraestructura de servidores

## Mapa de Features

| ID | Feature | Historias |
|---|---|---|
| FT-01 | Onboarding e identidad del tarjetahabiente | US-01, US-02, US-03 |
| FT-02 | Alta de tarjeta sin datos sensibles en el dispositivo | US-04, US-05 |
| FT-03 | Pago con QR: operación precargada (Modo 1) | US-06, US-07, US-08 |
| FT-04 | Pago con QR: monto libre (Modo 2) | US-09, US-10 |
| FT-05 | Resultado del pago e historial del tarjetahabiente | US-11, US-12 |
| FT-06 | Cobros del comercio (App Cobros y Portal perfil Comercio) | US-13, US-14, US-15, US-16, US-17, US-18 |
| FT-07 | Administración del emisor (Portal perfil Emisor) | US-19, US-20, US-21, US-22, US-23, US-24, EN-08 |
| FT-08 | Administración del procesador (Portal perfil Procesador) | US-25, US-26, EN-01 |
| FT-09 | Integración con el autorizador del emisor | EN-02, EN-03, EN-04, EN-05 |
| FT-10 | Integración con el sistema de emisión | EN-06 |
| FT-11 | White label y publicación | US-27, EN-07 |

## FT-01 · Onboarding e identidad del tarjetahabiente

**Benefit hypothesis:** Si el tarjetahabiente valida su identidad con biometría y documento al registrarse, el emisor reduce el riesgo de fraude de identidad y habilita el alta de tarjeta solo para personas verificadas.

**Criterios de aceptación de la Feature**

* Un usuario sin verificación aprobada no puede dar de alta una tarjeta
* El flujo completo de registro funciona en Android e iOS

### US-01 · Registro de usuario

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero registrarme con mis datos básicos, para poder usar la app de pagos del emisor.

**Criterios de aceptación**

1. Dado que abro la app por primera vez, cuando completo nombre, DNI, email y contraseña válidos y confirmo, entonces se crea mi usuario en estado 'pendiente de verificación'.
2. Dado que dejo un campo obligatorio vacío o con formato inválido (DNI o email), cuando intento continuar, entonces veo el error junto al campo y no se crea el usuario.
3. Dado que el email ya está registrado, cuando intento registrarme, entonces veo un mensaje que me ofrece iniciar sesión o recuperar la contraseña.
4. Dado que ingreso una contraseña que no cumple la política definida, cuando intento continuar, entonces veo los requisitos que faltan.

**Notas:** Política de contraseña a definir (ver PA-05).

### US-02 · Validación de identidad biométrica y documental

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero validar mi identidad con una selfie y mi DNI, para que el emisor me habilite a operar.

**Criterios de aceptación**

1. Dado que estoy registrado y pendiente de verificación, cuando completo la captura facial con prueba de vida y la validación de DNI, entonces mi usuario pasa a 'verificado'.
2. Dado que la prueba de vida o la validación documental fallan, cuando recibo el resultado, entonces veo el motivo en lenguaje simple y la opción de reintentar.
3. Dado que superé la cantidad máxima de reintentos, cuando intento de nuevo, entonces el sistema me indica cómo contactar al emisor.
4. Dado cualquier resultado, cuando termina la verificación, entonces el estado y el identificador de verificación del proveedor quedan asociados a mi usuario.

**Dependencias:** Proveedor de KYC contratado por el emisor (supuesto 8). SDK disponible.

**Notas:** Cantidad máxima de reintentos a definir (PA-05).

### US-03 · Login y recuperación de contraseña

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero ingresar con email y contraseña y poder recuperarla, para acceder a la app de forma segura.

**Criterios de aceptación**

1. Dado que tengo usuario verificado, cuando ingreso email y contraseña correctos, entonces accedo al home.
2. Dado que ingreso credenciales incorrectas, cuando confirmo, entonces veo un mensaje genérico que no revela si el email existe.
3. Dado que olvidé mi contraseña, cuando solicito recuperarla, entonces recibo un email con un enlace de un solo uso y con vencimiento.
4. Dado que uso el enlace válido, cuando defino una contraseña nueva que cumple la política, entonces puedo ingresar con ella y el enlace queda invalidado.

## FT-02 · Alta de tarjeta sin datos sensibles en el dispositivo

**Benefit hypothesis:** Si la app valida la tarjeta contra el autorizador y nunca persiste el PAN completo ni el CVV, la app queda fuera del alcance de tokenización PCI DSS y baja el costo de cumplimiento.

**Criterios de aceptación de la Feature**

* El PAN completo no se persiste ni se registra en logs en ningún componente
* Solo tarjetas validadas por el autorizador quedan habilitadas para pagar

### US-04 · Alta de tarjeta validada por el autorizador

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente verificado, quiero dar de alta mi tarjeta del emisor, para poder pagar con QR.

**Criterios de aceptación**

1. Dado que estoy verificado, cuando ingreso los 16 dígitos de mi tarjeta, entonces la app envía al autorizador DNI + últimos 4 dígitos para validarla.
2. Dado que el autorizador responde 'válida', cuando termina la validación, entonces la tarjeta queda habilitada y se muestra con sus últimos 4 dígitos.
3. Dado cualquier resultado, cuando termina el alta, entonces el PAN completo no queda persistido en el dispositivo, en el backend ni en logs.
4. Dado el flujo de alta, cuando se muestra el formulario, entonces el CVV no se solicita.

**Dependencias:** EN-02 (SP de validación de tarjetahabiente).

**Notas:** Ver PA-01: el SP de autorización requiere primeros 8 + últimos 4 dígitos; definir dónde se guarda el BIN de 8.

### US-05 · Tarjeta inválida en el alta

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero saber por qué no se pudo dar de alta mi tarjeta, para resolverlo.

**Criterios de aceptación**

1. Dado que el autorizador responde 'inválida', cuando termina la validación, entonces veo un mensaje que me sugiere revisar los datos o contactar al emisor y la tarjeta no queda habilitada.
2. Dado que el autorizador no responde dentro del SLA, cuando vence el tiempo, entonces veo un mensaje de reintento y no se crea la tarjeta.

**Dependencias:** EN-02.

## FT-03 · Pago con QR: operación precargada (Modo 1)

**Benefit hypothesis:** Si el comercio precarga monto y plan, el tarjetahabiente paga solo confirmando, lo que reduce errores de monto y tiempo en caja.

**Criterios de aceptación de la Feature**

* Un pago precargado se completa sin que el tarjetahabiente ingrese datos
* Una operación cancelada o vencida nunca se cobra

### US-06 · Escanear QR y recuperar operación precargada

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero escanear el QR del comercio y ver la operación que el comercio ya cargó, para pagar sin ingresar datos.

**Criterios de aceptación**

1. Dado que el comercio precargó una operación vigente en esa terminal, cuando escaneo su QR estático, entonces la app consulta al portal por la terminal y muestra comercio, monto y plan de cuotas.
2. Dado que escaneo un QR que no corresponde a un comercio adherido, cuando la app lo lee, entonces veo un mensaje de QR no válido y no se inicia ningún pago.
3. Dado cualquier escaneo, cuando la app lee el QR, entonces los datos leídos no se persisten en el dispositivo.

**Dependencias:** US-13 (precarga del comercio).

### US-07 · Confirmar pago precargado

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero confirmar el pago precargado, para pagar mi compra.

**Criterios de aceptación**

1. Dado que veo una operación precargada vigente, cuando presiono pagar, entonces se envía al autorizador terminal + BIN + últimos 4 + monto + plan.
2. Dado que el autorizador aprueba (00), cuando recibo la respuesta, entonces veo 'pago aprobado' con el número de autorización y la operación precargada queda consumida.
3. Dado que la operación ya fue pagada, cuando otro usuario escanea el mismo QR, entonces esa operación no se ofrece de nuevo.

**Dependencias:** EN-03, EN-05.

### US-08 · Paso automático a monto libre si la precarga venció

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero poder pagar igual si la operación precargada se canceló o venció, para no quedar trabado en la caja.

**Criterios de aceptación**

1. Dado que la operación precargada fue cancelada por el comercio, cuando escaneo el QR, entonces la app entra automáticamente en Modo 2.
2. Dado que pasaron más de 10 minutos desde la precarga sin escaneo, cuando escaneo el QR, entonces la operación figura vencida y la app entra en Modo 2.
3. Dado que la operación venció mientras la estoy viendo, cuando presiono pagar, entonces el pago no se envía al autorizador y paso a Modo 2.

**Dependencias:** US-09, US-14.

## FT-04 · Pago con QR: monto libre (Modo 2)

**Benefit hypothesis:** Si el tarjetahabiente puede pagar con monto libre eligiendo entre los planes vigentes del comercio, el comercio acepta pagos aunque no haya precargado la operación, con un único QR estático.

**Criterios de aceptación de la Feature**

* Solo se ofrecen planes de cuotas vigentes para ese comercio
* El pago en Modo 2 usa el mismo circuito de autorización que el Modo 1

### US-09 · Ver planes de cuotas vigentes del comercio

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero ver los planes de cuotas vigentes del comercio al escanear su QR, para elegir cómo pagar.

**Criterios de aceptación**

1. Dado que no hay operación precargada vigente, cuando escaneo el QR, entonces la app consulta en línea los planes vigentes de ese comercio y los muestra en una lista.
2. Dado que la consulta de planes falla o supera el SLA, cuando vence el tiempo, entonces veo un mensaje de reintento y no puedo confirmar el pago.
3. Dado que el comercio no tiene planes vigentes, cuando escaneo el QR, entonces veo un mensaje de que el comercio no acepta pagos en este momento.

**Dependencias:** EN-06.

### US-10 · Pagar con monto libre

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero ingresar el monto y elegir el plan de cuotas, para pagar cuando el comercio no precargó la operación.

**Criterios de aceptación**

1. Dado que veo los planes vigentes, cuando ingreso un monto mayor a cero, elijo un plan y confirmo, entonces se envía la autorización con terminal + BIN + últimos 4 + monto + plan.
2. Dado que no elegí plan o el monto es inválido, cuando intento pagar, entonces el botón de pago está deshabilitado y veo qué falta.
3. Dado que el autorizador aprueba, cuando recibo la respuesta, entonces veo 'pago aprobado' con número de autorización.

**Dependencias:** EN-03, EN-05, US-09.

## FT-05 · Resultado del pago e historial del tarjetahabiente

**Benefit hypothesis:** Si el resultado de cada pago es claro y queda en un historial, bajan las consultas a soporte y los pagos duplicados por reintento.

**Criterios de aceptación de la Feature**

* Cada código de respuesta tiene un mensaje y una acción definidos
* El historial muestra todas las transacciones propias en orden cronológico

### US-11 · Resultado del pago según código de respuesta

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como tarjetahabiente, quiero entender el resultado de mi pago, para saber si tengo que hacer algo más.

**Criterios de aceptación**

1. Dado que el autorizador responde 00, cuando termina el pago, entonces veo 'aprobado' y el número de autorización.
2. Dado que responde 51, cuando termina el pago, entonces veo 'fondos insuficientes' y la opción de volver.
3. Dado que responde 12, cuando termina el pago, entonces veo 'tarjeta inválida' y la sugerencia de contactar al emisor.
4. Dado que responde 68 o no hay respuesta dentro del SLA, cuando termina el tiempo, entonces veo 'no pudimos confirmar el pago', se me indica revisar el historial antes de reintentar y el botón de reintento no genera un segundo cobro.

**Dependencias:** EN-05.

### US-12 · Historial de transacciones del tarjetahabiente

`User Story` · Prioridad: **Should** · Story points: _a estimar_

Como tarjetahabiente, quiero ver mis transacciones, para controlar mis gastos.

**Criterios de aceptación**

1. Dado que tengo transacciones, cuando abro el historial, entonces veo todas en orden cronológico descendente con fecha, comercio, monto, plan, estado y número de autorización.
2. Dado que una transacción fue revertida, cuando la veo en el historial, entonces figura con estado 'revertida'.
3. Dado que no tengo transacciones, cuando abro el historial, entonces veo un estado vacío explicativo.

## FT-06 · Cobros del comercio (App Cobros y Portal perfil Comercio)

**Benefit hypothesis:** Si el comercio puede precargar, cancelar, revertir y consultar sus operaciones desde el celular, no necesita POS ni soporte del emisor para operar.

**Criterios de aceptación de la Feature**

* Las funciones del comercio están disponibles en App Cobros y en el Portal con el mismo comportamiento
* El comercio solo ve y opera sus propias transacciones y terminales

### US-13 · Precargar operación

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como comercio, quiero cargar monto y plan de cuotas en mi terminal, para que el cliente solo tenga que confirmar.

**Criterios de aceptación**

1. Dado que estoy logueado en App Cobros o Portal, cuando ingreso monto mayor a cero, elijo un plan vigente y confirmo, entonces la operación queda pendiente asociada a mi terminal con vencimiento a los 10 minutos.
2. Dado que ya tengo una operación pendiente en esa terminal, cuando intento cargar otra, entonces el sistema me pide cancelar la anterior primero.
3. Dado que pasan 10 minutos sin escaneo, cuando vence el plazo, entonces la operación pasa a 'vencida' automáticamente.

**Notas:** Ver PA-03: la regla de una sola operación pendiente por terminal es una propuesta a validar.

### US-14 · Cancelar operación precargada

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como comercio, quiero cancelar una operación precargada, para corregir un monto o un plan mal cargado.

**Criterios de aceptación**

1. Dado que tengo una operación pendiente no escaneada, cuando la cancelo, entonces pasa a 'cancelada' y ya no se ofrece al escanear el QR.
2. Dado que la operación ya fue pagada, cuando intento cancelarla, entonces el sistema me ofrece la reversa en su lugar.

### US-15 · Ver y descargar mis QR estáticos

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como comercio, quiero ver y descargar los QR de mis terminales, para imprimirlos y exhibirlos.

**Criterios de aceptación**

1. Dado que mi comercio tiene terminales con QR generado, cuando abro 'Mis QR', entonces veo un QR por terminal con su identificador.
2. Dado que elijo un QR, cuando lo descargo, entonces obtengo una imagen apta para impresión.

**Dependencias:** US-22.

### US-16 · Tablero de transacciones del comercio

`User Story` · Prioridad: **Should** · Story points: _a estimar_

Como comercio, quiero ver mis ventas QR y POS web, para controlar lo que cobré.

**Criterios de aceptación**

1. Dado que tengo transacciones, cuando abro el tablero, entonces veo las operaciones QR en tiempo real y las de POS web con su fecha de corte.
2. Dado que filtro por fecha, terminal o estado, cuando aplico el filtro, entonces el listado y los totales se recalculan.
3. Dado que soy usuario de un comercio, cuando consulto el tablero, entonces no veo transacciones de otros comercios.

**Dependencias:** EN-01.

### US-17 · Reversa de transacción por el comercio

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como comercio, quiero revertir una venta, para devolverle el dinero al cliente cuando corresponde.

**Criterios de aceptación**

1. Dado que tengo una transacción aprobada dentro de la ventana permitida, cuando solicito la reversa y la confirmo, entonces se envía al autorizador y la transacción pasa a 'revertida'.
2. Dado que la transacción ya fue revertida, cuando intento revertirla otra vez, entonces el sistema lo impide.
3. Dado cualquier reversa, cuando se ejecuta, entonces queda auditada con usuario, fecha y motivo.

**Dependencias:** EN-03 (operación de reversa en el autorizador).

**Notas:** Ventana de reversa y operación del autorizador a definir (PA-04).

### US-18 · Paridad App Cobros y Portal perfil Comercio

`User Story` · Prioridad: **Should** · Story points: _a estimar_

Como comercio, quiero hacer lo mismo desde la app o desde el portal web, para operar desde donde me resulte más cómodo.

**Criterios de aceptación**

1. Dado cualquiera de las historias US-13 a US-17, cuando se ejecuta desde App Cobros o desde el Portal, entonces el resultado y las reglas son los mismos.
2. Dado que una operación se precarga en la app, cuando abro el portal, entonces la veo con el mismo estado.

## FT-07 · Administración del emisor (Portal perfil Emisor)

**Benefit hypothesis:** Si el emisor administra sus comercios, usuarios, planes y QR en forma autónoma, puede escalar la red de comercios sin intervención del procesador.

**Criterios de aceptación de la Feature**

* El emisor solo ve y opera datos propios
* Todo alta, baja o modificación queda auditada con usuario y fecha

### US-19 · ABM de comercios

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como administrador del emisor, quiero dar de alta, modificar y dar de baja comercios y sus terminales, para gestionar mi red de aceptación.

**Criterios de aceptación**

1. Dado que soy administrador del emisor, cuando doy de alta un comercio con sus datos y al menos una terminal, entonces queda activo y asociado a mi emisor.
2. Dado que doy de baja un comercio, cuando confirmo, entonces sus QR dejan de aceptar pagos y su historial se conserva.
3. Dado cualquier cambio, cuando se guarda, entonces queda auditado.

### US-20 · ABM de usuarios de comercios

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como administrador del emisor, quiero gestionar los usuarios de mis comercios, para controlar quién cobra y quién revierte.

**Criterios de aceptación**

1. Dado un comercio activo, cuando creo un usuario con email, entonces recibe una invitación para definir su contraseña.
2. Dado que deshabilito un usuario, cuando intenta ingresar, entonces el acceso es rechazado.

**Dependencias:** EN-08.

### US-21 · ABM de planes de cuotas

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como administrador del emisor, quiero gestionar los planes de cuotas por comercio, para definir las condiciones de financiación.

**Criterios de aceptación**

1. Dado un comercio activo, cuando asigno un plan con cantidad de cuotas y vigencia, entonces el plan se ofrece en Modo 1 y Modo 2 dentro de su vigencia.
2. Dado que un plan vence o lo desactivo, cuando un tarjetahabiente escanea el QR, entonces ese plan no se ofrece.

**Notas:** Ver PA-02: definir si el maestro de planes vive en el portal o en el sistema de emisión.

### US-22 · Generar y descargar QR por comercio y terminal

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como administrador del emisor, quiero generar los QR estáticos de cada terminal, para entregarlos a los comercios.

**Criterios de aceptación**

1. Dado un comercio con terminales, cuando genero el QR de una terminal, entonces el QR codifica los datos del comercio y el ID de terminal.
2. Dado un QR generado, cuando lo descargo, entonces obtengo una imagen apta para impresión.
3. Dado que regenero el QR de una terminal, cuando confirmo, entonces el anterior deja de ser válido.

**Notas:** Contenido exacto del QR a confirmar (PA-06).

### US-23 · Tablero del emisor

`User Story` · Prioridad: **Should** · Story points: _a estimar_

Como administrador del emisor, quiero ver las transacciones de todos mis comercios, para monitorear la operación.

**Criterios de aceptación**

1. Dado que tengo comercios con operaciones, cuando abro el tablero, entonces veo QR en tiempo real y POS web con su fecha de corte, con filtros por comercio, fecha y estado.
2. Dado que soy administrador de un emisor, cuando consulto, entonces no veo datos de otros emisores.

**Dependencias:** EN-01.

### US-24 · Reversa de transacciones de mis comercios

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como administrador del emisor, quiero revertir transacciones de mis comercios, para resolver reclamos.

**Criterios de aceptación**

1. Dado una transacción aprobada de uno de mis comercios dentro de la ventana permitida, cuando la revierto, entonces pasa a 'revertida' y queda auditada.
2. Dado una transacción de otro emisor, cuando intento revertirla, entonces el sistema lo impide.

**Dependencias:** US-17.

### EN-08 · Perfiles y permisos del portal

`Enabler` · Prioridad: **Must** · Story points: _a estimar_

Como plataforma, necesito controlar el acceso por perfil (Procesador, Emisor, Comercio), para que cada usuario vea y opere solo lo que le corresponde.

**Criterios de aceptación**

1. Dado un usuario de perfil Comercio, cuando accede, entonces solo ve datos de su comercio.
2. Dado un usuario de perfil Emisor, cuando accede, entonces solo ve datos de su emisor y sus comercios.
3. Dado un usuario de perfil Procesador, cuando accede, entonces ve todos los emisores.
4. Dado un intento de acceso a un recurso de otro perfil o entidad, cuando se ejecuta, entonces es rechazado y queda registrado.

## FT-08 · Administración del procesador (Portal perfil Procesador)

**Benefit hypothesis:** Si el procesador gestiona emisores y ve un tablero consolidado, puede operar la plataforma para varios emisores desde un solo lugar.

**Criterios de aceptación de la Feature**

* El tablero consolida transacciones QR y POS web de todos los emisores
* Los datos de POS web indican su fecha de corte

### US-25 · ABM de emisores

`User Story` · Prioridad: **Must** · Story points: _a estimar_

Como administrador del procesador, quiero dar de alta y gestionar emisores, para sumar nuevas entidades a la plataforma.

**Criterios de aceptación**

1. Dado que soy superadministrador, cuando doy de alta un emisor con sus datos, BINs y branding, entonces queda disponible para operar.
2. Dado que deshabilito un emisor, cuando sus usuarios intentan operar, entonces el acceso es rechazado y el historial se conserva.

**Dependencias:** US-27.

### US-26 · Tablero consolidado del procesador

`User Story` · Prioridad: **Should** · Story points: _a estimar_

Como administrador del procesador, quiero un tablero con las transacciones de todos los emisores, para monitorear la plataforma completa.

**Criterios de aceptación**

1. Dado que soy superadministrador, cuando abro el tablero, entonces veo transacciones QR y POS web de todos los emisores, con filtro por emisor.
2. Dado que los datos de POS web vienen de la sincronización diaria, cuando los veo, entonces se indica la fecha y hora del último corte.

**Dependencias:** EN-01.

### EN-01 · Sincronización diaria de transacciones POS web

`Enabler` · Prioridad: **Must** · Story points: _a estimar_

Como plataforma, necesito importar una vez por día las transacciones de POS web, para mostrar en los tableros la operación completa de cada comercio.

**Criterios de aceptación**

1. Dado el horario de corte configurado, cuando corre el proceso, entonces se importan las transacciones POS web del día anterior sin duplicar las ya importadas.
2. Dado que el proceso falla, cuando termina, entonces queda registrado el error y se genera una alerta.
3. Dado un proceso exitoso, cuando termina, entonces se registra la fecha y hora de corte que muestran los tableros.

**Notas:** Ver PA-07: fuente de la consulta (autorizador o traductor).

## FT-09 · Integración con el autorizador del emisor

**Benefit hypothesis:** Si la plataforma reutiliza el autorizador existente vía stored procedures, el emisor lanza pagos QR sin reemplazar su core de autorización.

**Criterios de aceptación de la Feature**

* Autorización con SLA menor a 1 segundo bajo 60 a 70 conexiones simultáneas
* Ruteo correcto por BIN entre las dos instancias del autorizador

### EN-02 · Integración: validación de tarjetahabiente

`Enabler` · Prioridad: **Must** · Story points: _a estimar_

Como plataforma, necesito invocar el stored procedure de validación del autorizador, para dar de alta tarjetas en forma segura.

**Criterios de aceptación**

1. Dado DNI + últimos 4 dígitos, cuando se invoca el SP, entonces la respuesta 'válida' o 'inválida' se mapea al resultado del alta.
2. Dado que el SP no responde dentro del SLA, cuando vence el tiempo, entonces se informa error recuperable y no se crea la tarjeta.
3. Dado cualquier invocación, cuando se registra en logs, entonces no se registran dígitos de la tarjeta más allá de los últimos 4.

**Dependencias:** SP expuesto por el emisor antes del desarrollo (supuesto 1). Bloqueante de FT-02.

### EN-03 · Integración: autorización de pago

`Enabler` · Prioridad: **Must** · Story points: _a estimar_

Como plataforma, necesito invocar el stored procedure de autorización, para aprobar o rechazar cada pago.

**Criterios de aceptación**

1. Dado terminal + BIN + últimos 4 + monto + plan, cuando se invoca el SP, entonces se obtiene código de respuesta y número de autorización.
2. Dado los códigos 00, 51, 12 y 68, cuando se reciben, entonces se mapean a los estados aprobado, sin fondos, inválida y timeout.
3. Dado un código no contemplado, cuando se recibe, entonces se trata como rechazo genérico y se registra para análisis.

**Dependencias:** SP expuesto por el emisor antes del desarrollo (supuesto 1).

### EN-04 · Ruteo por BIN entre instancias del autorizador

`Enabler` · Prioridad: **Must** · Story points: _a estimar_

Como plataforma, necesito enrutar cada operación a la instancia correcta del autorizador según el BIN, para operar durante la convivencia de las dos versiones.

**Criterios de aceptación**

1. Dado un BIN configurado para la instancia actual, cuando se envía la operación, entonces llega a esa instancia.
2. Dado un BIN configurado para la instancia nueva, cuando se envía la operación, entonces llega a esa instancia.
3. Dado un BIN sin configuración, cuando se envía la operación, entonces se rechaza con error controlado y alerta.
4. Dado un cambio en la tabla de ruteo, cuando se guarda, entonces aplica sin redeploy y queda auditado.

**Notas:** La tabla de ruteo es un parámetro, no código.

### EN-05 · Idempotencia y manejo de timeout

`Enabler` · Prioridad: **Must** · Story points: _a estimar_

Como plataforma, necesito que un reintento no genere un segundo cobro, para proteger al tarjetahabiente ante timeouts.

**Criterios de aceptación**

1. Dado un pago con identificador único de operación, cuando la app reintenta por timeout, entonces el portal no envía una segunda autorización si la primera fue aprobada.
2. Dado un código 68 o falta de respuesta, cuando vence el SLA, entonces la operación queda en estado 'a confirmar' y se resuelve consultando el estado antes de cualquier reintento.
3. Dado una operación 'a confirmar', cuando se resuelve, entonces el tarjetahabiente y el comercio ven el estado final.

**Notas:** Ver PA-08: el autorizador debe permitir consultar el estado de una operación.

## FT-10 · Integración con el sistema de emisión

**Benefit hypothesis:** Si los planes de cuotas se consultan en línea al sistema de emisión, el tarjetahabiente siempre ve las condiciones vigentes del comercio.

**Criterios de aceptación de la Feature**

* La consulta de planes responde en menos de 1 segundo
* Si la consulta falla, el pago en Modo 2 no se habilita y se informa al usuario

### EN-06 · Integración: consulta de planes vigentes

`Enabler` · Prioridad: **Must** · Story points: _a estimar_

Como plataforma, necesito consultar en línea los planes de cuotas vigentes por comercio, para ofrecerlos en el Modo 2.

**Criterios de aceptación**

1. Dado un comercio, cuando se consulta el sistema de emisión, entonces se obtiene la lista de planes vigentes en menos de 1 segundo.
2. Dado que la consulta falla o supera el SLA, cuando vence el tiempo, entonces se devuelve error recuperable a la app.

**Dependencias:** Endpoint expuesto por el emisor (supuesto 2). Bloqueante de FT-04.

**Notas:** Protocolo a confirmar (PA-07).

## FT-11 · White label y publicación

**Benefit hypothesis:** Si la app se configura con la marca de cada emisor, la misma plataforma se vende a varios emisores sin desarrollo adicional.

**Criterios de aceptación de la Feature**

* Logo, colores y nombre de producto se configuran por emisor
* Las apps quedan publicadas en Google Play y App Store bajo la cuenta del emisor

### US-27 · Branding por emisor

`User Story` · Prioridad: **Should** · Story points: _a estimar_

Como emisor, quiero que la app tenga mi logo, colores y nombre de producto, para que mis clientes la reconozcan como propia.

**Criterios de aceptación**

1. Dado un emisor con branding cargado, cuando se genera su versión de la app, entonces muestra su logo, paleta y nombre de producto.
2. Dado que falta algún elemento de branding, cuando se intenta generar la versión, entonces el proceso lo informa y no publica.

**Dependencias:** Branding provisto por el emisor (supuesto 6).

### EN-07 · Publicación en tiendas

`Enabler` · Prioridad: **Must** · Story points: _a estimar_

Como emisor, necesito publicar las apps en Google Play y App Store bajo mi cuenta, para distribuirlas a mis clientes.

**Criterios de aceptación**

1. Dado un build aprobado, cuando se entrega, entonces incluye el binario firmado y el material para la ficha de la tienda.
2. Dado el proceso de publicación, cuando el emisor lo ejecuta, entonces cuenta con asistencia para la configuración y la publicación.

**Dependencias:** Cuentas de desarrollador del emisor.

## Requisitos no funcionales (NFR)

Aplican como restricciones a todas las historias del backlog.

| ID | Categoría | Requisito |
|---|---|---|
| NFR-01 | Rendimiento | Autorización de pago con respuesta menor a 1 segundo (p95) con 60 a 70 conexiones simultáneas al autorizador. |
| NFR-02 | Rendimiento | Consulta de planes vigentes menor a 1 segundo. |
| NFR-03 | Capacidad | Volumen inicial estimado de hasta 400 transacciones por día, con margen para crecer sin cambios de arquitectura. |
| NFR-04 | Seguridad | El PAN completo no se persiste, no se reconstruye en memoria y no se registra en logs en ningún componente. El CVV nunca se solicita. |
| NFR-05 | Seguridad | Comunicación cifrada en tránsito entre app, portal e integraciones. |
| NFR-06 | Auditoría | Toda alta, baja, modificación y reversa queda registrada con usuario, fecha, hora y valores anteriores. |
| NFR-07 | Disponibilidad | Errores de integraciones externas se manejan con mensajes recuperables; ningún error deja una operación en estado ambiguo sin resolución posterior. |
| NFR-08 | Plataformas | Apps nativas Android e iOS; portal web responsive. |
| NFR-09 | Configuración | Timeouts, ventanas de reversa, tabla de ruteo por BIN y planes se gestionan como parámetros, sin redeploy. |

## Definition of Done

* [ ] Criterios de aceptación cumplidos y demostrados al Product Owner
* [ ] Código revisado por al menos otro desarrollador
* [ ] Pruebas unitarias y de integración automatizadas para la historia
* [ ] Casos negativos de los criterios de aceptación probados
* [ ] Sin datos de tarjeta más allá de los últimos 4 dígitos en logs (verificado)
* [ ] NFRs aplicables verificados
* [ ] Documentación de API actualizada si cambió un contrato
* [ ] Desplegado en el ambiente de pruebas del emisor

## Preguntas abiertas para refinamiento

Inconsistencias o vacíos detectados al bajar el alcance a historias. Conviene resolverlos antes del PI Planning.

| ID | Tema | Detalle | Impacta |
|---|---|---|---|
| PA-01 | Datos de tarjeta guardados | El alcance indica que la app guarda solo los últimos 4 dígitos, pero el SP de autorización requiere los primeros 8 (BIN) + últimos 4. Definir dónde se guarda el BIN de 8 (app o backend) sin salir del alcance PCI definido. | US-04, EN-03 |
| PA-02 | Maestro de planes de cuotas | El portal incluye ABM de planes para el emisor, pero la integración dice que los planes se administran en el sistema de emisión. Definir una única fuente de verdad. | US-21, EN-06 |
| PA-03 | Contenido del QR en Modo 1 | El alcance lista monto y plan como datos del QR en Modo 1, pero el QR es estático y el flujo los obtiene del portal. Confirmar que el QR solo lleva comercio + terminal. Validar también la regla de una operación pendiente por terminal. | US-06, US-13 |
| PA-04 | Reversas | Definir ventana de tiempo permitida, si hay reversas parciales y qué operación del autorizador se usa (no está entre los SPs listados). | US-17, US-24 |
| PA-05 | Políticas de seguridad del usuario | Definir política de contraseña, cantidad de reintentos de verificación biométrica y bloqueo por intentos fallidos. | US-01 a US-03 |
| PA-06 | Datos identificatorios del comercio en el QR | Marcado 'a confirmar' en el alcance. | US-22 |
| PA-07 | Protocolo con el sistema de emisión y fuente de POS web | El método de integración está 'a confirmar'; la consulta de POS web se menciona contra el traductor y contra el autorizador. | EN-01, EN-06 |
| PA-08 | Consulta de estado de operación | Para resolver timeouts sin doble cobro, el autorizador debe permitir consultar el estado de una operación. No figura entre los SPs listados. | EN-05 |
| PA-09 | DNI en la autorización | El flujo indica enviar 'DNI PAN' a confirmar. Definir si el DNI viaja en la autorización. | EN-03 |
| PA-10 | Confirmación del pago | El alcance no exige autenticación adicional al confirmar el pago (biometría o PIN). Definir si se requiere. | US-07, US-10 |
