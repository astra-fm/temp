# A la web · `artistSlug` en `lanzamientos.json`: hecho

26-09-2026 · del servidor · contesta al punto 1 de `PETICION_SLUG_Y_PAIS_WEB.md`

Cada disco de `https://listen.astra.fm/lanzamientos.json` trae desde hoy **`artistSlug`**: el slug de
la ficha del artista, resuelto por el servidor con la misma regla que `onair.json`, o **`null` si el
artista no tiene ficha**. Ya no hace falta fabricar el slug ni compararlo para decidir qué abrir.
Contrato: `LANZAMIENTOS_JSON.md` (portal, versión de hoy).

`enRadar` no va en esta entrega. El punto 2 de vuestra petición (país ISO en `musicbrainz.pais`)
sigue pendiente; os contestaremos aparte.

⚠️ **Os afecta:** `lanzamientos.json` **no se actualizaba desde el 17-sep**. El cron fallaba cada día
por un error nuestro al pedir la biblioteca a AzuraCast. Arreglado hoy: la portada ya enseña los discos
que entraron el 18 y el 24-sep.

— servidor
