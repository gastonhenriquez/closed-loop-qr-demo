# Closed Loop QR · Tarjeta Regional

**Demo en vivo:** https://gastonhenriquez.github.io/closed-loop-qr-demo/

Prototipo animado de un pago con QR en **circuito cerrado**: tarjetahabiente, comercio y emisor operan sobre la red propia del emisor, sin red internacional ni adquirente externo. Basado en un proyecto real, anonimizado. Todos los datos son ficticios.

## El problema

Un emisor regional de tarjetas (comercios regionales, municipios, cooperativas, mutuales) necesita que sus tarjetahabientes paguen en comercios que no tienen POS. Una red internacional o un adquirente externo agregan costo por transacción, demoran la acreditación y le quitan al emisor el dato de cada compra.

## La solución

| Paso | Actor | Qué pasa |
|---|---|---|
| 1 | Comercio | Precarga monto y plan de cuotas asociado a su QR estático |
| 2 | Tarjetahabiente | Escanea el QR desde la app del emisor |
| 3 | Tarjetahabiente | Ve comercio, monto y cuotas, y confirma |
| 4 | Emisor | El autorizador valida tarjeta, límite y reglas en menos de 1 segundo |
| 5 | Todos | Ambas apps muestran el mismo número de autorización; el back office registra la venta |
| 6 | Emisor | Calcula arancel, neto y fecha de pago (T+1) y concilia contra lo autorizado |

## Decisiones de producto

* **Circuito cerrado:** menor costo por transacción, acreditación previsible al comercio y datos completos de cada compra.
* **Fuera de scope PCI por diseño:** la app guarda solo los últimos 4 dígitos y nunca el CVV; la autorización viaja con BIN + últimos 4. El PAN completo no se reconstruye en ningún momento.
* **QR estático con dos modos:** si el comercio precargó la operación, el cliente solo confirma; si no (o venció a los 10 minutos), paga con monto libre eligiendo entre los planes de cuotas vigentes, consultados en tiempo real. Un solo QR impreso sirve para siempre.
* **Integrar, no reemplazar:** el autorizador y el sistema de emisión existentes se integran como están (stored procedures, ruteo por BIN entre dos versiones del autorizador). El riesgo del proyecto está en esas dependencias, y por eso figuran como supuestos bloqueantes.
* **Casos negativos desde el diseño:** códigos `00` / `51` / `12` / `68`, y el monitor muestra un rechazo por saldo, no solo el camino feliz.
* **Liquidación y conciliación visibles:** el back office muestra arancel, neto y estado de conciliación por comercio, que es donde suelen aparecer los descuadres.

Especificación completa: [`docs/spec.md`](docs/spec.md).

## Backlog listo para desarrollo

La spec bajada a un backlog con estructura **SAFe**: 1 Epic con hypothesis statement, 11 Features con benefit hypothesis, 27 User Stories y 8 Enablers con criterios de aceptación en formato Dado / Cuando / Entonces, NFRs, Definition of Done y 10 preguntas abiertas detectadas al refinar el alcance.

* Para leer: [`docs/backlog.md`](docs/backlog.md)
* Para importar en Azure DevOps o Jira: [`docs/backlog.csv`](docs/backlog.csv)

## Cómo se construyó

Documento de alcance → prototipo con Claude → iteración sobre la lógica de negocio → revisión de consistencia contra la spec. Detalle en [`docs/proceso.md`](docs/proceso.md).

El objetivo es validar el flujo con negocio e ingeniería **antes** de escribir código productivo: lo que llega a desarrollo ya fue visto y aprobado.

## Cómo verlo

* Online: link de arriba.
* Local: abrir `index.html`. Un único archivo, sin dependencias ni build.
* ◀ ▶ para navegar los pasos, espacio para pausar. Con `#record` en la URL se ocultan los controles para grabar.

---

### English summary

Animated prototype of a **closed loop QR card payment** for a regional card issuer: the merchant preloads an amount on a static QR, the cardholder pays from the issuer's app, and the issuer authorizes, settles (T+1) and reconciles within its own network, with no card scheme or third-party acquirer. Key design choices: out of PCI tokenization scope by storing only the last 4 digits, a static QR with preloaded and open-amount modes (10-minute timeout), and integrating the issuer's existing authorizer instead of replacing it. Built with Claude from a real (anonymized) scope document. All data is fictitious.
