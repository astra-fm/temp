# Fotos de artista más grandes y sin volver a comprimir

28-10-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 28.10.26

## Qué pasa

Pablo ve las fotos de artista borrosas en **Radio · En directo**. La web pinta tal cual el `foto.url` de
`onair.json` (y `image` de `/artists/<slug>.json`), sin tocarlo. El problema llega con el archivo.

Medido el 28.10.26 sobre los 39 artistas distintos del `historial.json` de ese día y del `onair.json`:

| Fuente (`credito`) | Artistas | Medida en `listen.astra.fm/artists/images/` |
|---|---|---|
| TheAudioDB | 31 | 700 × 700 |
| Bandcamp, Deezer, prensa, sin crédito | 8 | 800 × 800 u 800 × 533 |

**Ninguna pasa de 800.** El hueco de la foto en Radio mide unos **880 × 650 px CSS**, es decir, unos **1760 × 1300**
en pantalla Retina. Una foto de 700 se ve ampliada dos veces y media.

Y además **se vuelve a comprimir**. Hole, por ejemplo:

| | Medida | Peso |
|---|---|---|
| Original, `https://r2.theaudiodb.com/images/media/artist/thumb/it04rh1761087735.jpg` | 700 × 700 | 98 KB |
| Copia nuestra, `https://listen.astra.fm/artists/images/hole.jpg` | 700 × 700 | 48 KB |

Mismas medidas y la mitad de peso: la copia pierde detalle respecto del original.

## Qué pedimos

1. **No recomprimir al guardar.** Si la foto no hay que reducirla, guardar el archivo original tal cual; si hay que
   reducirla, con calidad JPEG alta (90 o más).
2. **Preferir la fuente más grande** para la foto de la ficha:
   - En TheAudioDB, el **fanart** (`strArtistFanart`, `strArtistFanart2`… ) mide **1280 × 720** y es apaisado,
     como el hueco de Radio. El `strArtistThumb` se queda en 700 × 700. Hole, por ejemplo, tiene cuatro fanart.
   - En Bandcamp y Deezer, pedir el tamaño original o el mayor que den (Deezer `cover_xl`/`picture_xl`, Bandcamp
     `_0` o `_10`), no la versión de 800.
3. Como orientación, **1600 px de lado largo o más** se ve nítido en Radio. Si la mejor foto que hay es más pequeña,
   mejor esa que nada: no hace falta descartar ninguna.

La web no necesita cambios: sigue pintando `foto.url`. Si cambiáis el formato (apaisado en vez de cuadrado), el
encuadre lo ajusta la web midiendo la foto.

— el front de la web
