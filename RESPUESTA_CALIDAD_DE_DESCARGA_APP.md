# A la app · `ETag` en `/play` y versiones AAC para descargar: hecho

26-09-2026 · del servidor · contesta a `PETICION_CALIDAD_DE_DESCARGA.md`

Todo está en producción y escrito en el contrato: **`APP_MOVIL_INSTRUCCIONES.md`, §3**, sección
«Descargar canciones: calidad» (portal, versión de hoy). Lo de abajo es el resumen, no la referencia.

## Vuestras tres preguntas

1. **¿Se puede, y con qué URL?** Sí. Hemos elegido campos por canción en `collections/<id>.json`,
   no un parámetro en `/play`: así cada versión es un fichero estático y lleva el tamaño.
2. **¿Alguna razón para no dar `ETag` en `/play`?** Ninguna. AzuraCast lo daba y nuestro proxy lo
   tiraba. Ya pasa.
3. **Plazo:** el `ETag` y los enlaces, ya. Las versiones AAC de las 896 canciones de las nueve
   colecciones se están generando ahora y terminan hoy hacia las **11:35** (hora de Barcelona).
   Pablo puede probar desde esa hora.

## Petición 1 · `ETag` en `/play/<id>`

Comprobado en vivo con `/play/8143`:

| Petición | Respuesta |
|---|---|
| Sin `Range` | 200, con `ETag` y `Last-Modified` |
| `Range` + `If-Range` con el ETag vigente | 206, solo el trozo pedido |
| `Range` + `If-Range` con un ETag viejo | 200, la canción entera |
| `If-None-Match` con el ETag vigente | 304 |

El `ETag` es el del fichero en AzuraCast: no cambia mientras el fichero sea el mismo.

## Petición 2 · Versiones AAC

Cada canción trae ahora `descargas`, con tres entradas; cada una es `{ url, bytes }` o `null`:

```json
"descargas": {
  "mp3":    { "url": "https://listen.astra.fm/play/11343", "bytes": 8989704 },
  "aac160": { "url": "https://listen.astra.fm/descargas/11343-1770226977.aac160.m4a", "bytes": 4531999 },
  "aac96":  { "url": "https://listen.astra.fm/descargas/11343-1770226977.aac96.m4a", "bytes": 2744578 }
}
```

Vuestras cuatro condiciones:

1. **Ficheros ya generados**, servidos como estáticos. `Content-Length` desde la primera respuesta.
2. **AAC en MP4** (`.m4a`, `Content-Type: audio/mp4`), con `faststart`.
3. **`Content-Length`, `Accept-Ranges`, `ETag`, `Last-Modified` e `If-Range`**: probado, 206 con el
   ETag vigente y 200 con uno viejo. Además `Cache-Control: immutable`: la URL cambia si cambia el
   original, así que la misma URL es siempre el mismo fichero.
4. **Sin cambios para lo publicado:** `play` y `/play/<id>` siguen dando el MP3 de siempre.

El extra también está: **`bytes` es el tamaño real** de cada versión, MP3 incluido.

### Lo que tenéis que tener en cuenta

- **Una entrada puede venir `null`.** Las versiones se generan cuando una canción entra en una
  colección, en la pasada diaria de las 05:15 (Barcelona). Los viernes, cuando se rehacen las
  colecciones, las canciones nuevas tardan unas horas en tener su AAC. **Si la calidad elegida viene
  `null`, bajad `mp3`** y que la pantalla no diga 160 si ha bajado el MP3.
- **Cuando una versión llega, el JSON de la colección cambia (otro ETag) pero `updatedAt` no.**
  `updatedAt` sigue queriendo decir «la lista es otra»; no lo uséis para detectar versiones nuevas.
- **Las versiones de las canciones que salen de todas las colecciones se borran** (404). Lo que ya
  está descargado en el móvil no se ve afectado.
- **El MP3 no es siempre de 320:** 884 de 895 lo son; el resto va de 128 a 224. Para decir cuánto
  ocupa, usad `bytes`, no el bitrate.
- **El AAC 96 sale del codificador AAC de serie de ffmpeg.** Suena correcto para una opción ligera;
  si al escucharlo en el iPhone no convence, decídnoslo y lo revisamos.

## Y lo de ayer: `artistSlug` en `lanzamientos.json`

Hecho (vuestro `RESPUESTA_ONAIR_APP.md`, punto 2). Cada disco trae `artistSlug`: el slug de la ficha,
resuelto con la misma regla que `onair.json`, o `null` si el artista no tiene ficha. Ya podéis
quitar `artists/index.json` de la app del todo. Contrato: `LANZAMIENTOS_JSON.md` y §3 de
`APP_MOVIL_INSTRUCCIONES.md`.

⚠️ **Os afecta:** `lanzamientos.json` **no se actualizaba desde el 17-sep**: el cron fallaba cada día
por un error nuestro al pedir la biblioteca a AzuraCast. Arreglado hoy. Si en la app veíais siempre
los mismos seis discos, era eso.

— servidor
