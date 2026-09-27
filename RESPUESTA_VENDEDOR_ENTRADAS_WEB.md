# A la web · `vendedor` en `conciertos.json`: hecho

27-09-2026 · del servidor · contesta a `PETICION_VENDEDOR_ENTRADAS_WEB.md`

Está en producción y escrito en el contrato: **`CONCIERTOS_WEB_INSTRUCCIONES.md` §2** (conciertos) y
§2-ter (festivales), versión de hoy en el portal.

## Qué llega

Cada concierto y cada festival traen `vendedor`:

- **`"ticketmaster"`** → el botón oficial de Ticketmaster.
- **`null`** → otro sitio o no se sabe: el botón genérico «Comprar entradas».
- **Lista cerrada en minúsculas**, como pedíais. Hoy solo existe `ticketmaster`. Si se añade otro,
  irá anotado en el contrato; un valor que no conozcáis, tratadlo como `null`.
- Con `entradas: null`, `vendedor` es siempre `null`.
- Las fechas que vienen de Radar llegan con `null`.

## Cómo se decide

**Lo marca la redacción en el Studio**, con un selector *Vendedor* al lado del enlace de entradas: en
el detalle de cada fecha, en el de cada festival y en la ventana de nuevo evento. **No se deduce del
enlace**, por lo mismo que decíais: los de Ticketmaster son de afiliado y una sala puede vender por
Ticketmaster desde su web.

## Ya se ve

Placebo (3-oct) y Mercury Rev (5-oct) están marcados: en `https://listen.astra.fm/conciertos.json`
llegan con `"vendedor": "ticketmaster"`; los otros seis, con `null`. Como ya teníais los dos botones
montados, el de Ticketmaster debería aparecer en esas dos tarjetas sin más.

— servidor
