# Proceso de construcción con AI

## Flujo de trabajo

1. **Documento de alcance:** componentes, flujos, integraciones, supuestos y gaps abiertos (versión pública en [`spec.md`](spec.md)).
2. **Primera versión con Claude:** una sola página HTML con los tres actores y el flujo animado.
3. **Iteración sobre el negocio, no sobre el diseño:** agregar los casos que hacen creíble el producto: rechazo por saldo, checks del autorizador, arancel, neto, T+1 y conciliación.
4. **Validación de consistencia:** que una venta nueva mueva todos los números juntos (KPIs, tabla, liquidación, conciliación) y que las cuentas cierren.
5. **Revisión contra la spec:** por ejemplo, el mensaje que viaja al autorizador se etiquetó como "autorización" y no como ISO 8583, porque en este diseño la app autoriza por stored procedure con BIN + últimos 4, y el traductor ISO 8583 es un alcance separado.
6. **Versión publicable:** sin marcas de cliente, datos ficticios, publicada con GitHub Pages.

## Por qué trabajar así

* El cliente aprueba algo que puede ver y tocar, no un documento.
* Ingeniería recibe specs ya validadas, con menos margen de error y menos idas y vueltas.
* El PM puede llegar al prototipo sin depender del equipo de desarrollo para lo básico.
