# A la web · fotos de artista más grandes: de acuerdo, en marcha

28-10-2026 · del servidor · contesta a `PETICION_FOTOS_DE_ARTISTA_MAS_GRANDES.md`

Gracias por medirlo. Tenéis razón en todo: el servidor reducía cada foto a 800 px como máximo y la volvía a comprimir
siempre, aunque ya fuera un JPEG que no había que tocar. Por eso Hole pesa la mitad que el original con las mismas
medidas.

## Qué cambia

1. **Sin recomprimir.** Si la foto mide 1920 px o menos de lado largo y ya es JPEG, se guarda el archivo original tal
   cual. Si es más grande o viene en otro formato, se reduce a 1920 px con calidad alta. Vale para todo lo que se
   guarde desde hoy (importación de cada noche y fotos subidas desde el Studio).

2. **Dos fotos por artista.** La foto de la ficha sigue siendo **cuadrada**: la usan también la app, las portadas de
   redes y los vídeos, que recortan en vertical, y una apaisada perdería gente por los lados. La apaisada va en un
   **campo nuevo**, solo para vosotros:

   | Dónde | Lo de siempre | Nuevo (aditivo) |
   |---|---|---|
   | `/artists/<slug>.json` | `image`, `imageSource`, `imageSourceName` | `imageWide`, `imageWideSource`, `imageWideSourceName` |
   | `onair.json` e `historial.json` | `foto: { url, de, credito, enlace }` | `fotoAncha: { url, credito, enlace, ancho, alto }` |

   `imageWide` y `fotoAncha` son `null` cuando el artista no tiene foto apaisada; entonces seguid con la cuadrada.
   La apaisada sale del **fanart de TheAudioDB** (1280 × 720) y solo se guarda si TheAudioDB confirma que es el mismo
   artista que la ficha (su foto cuadrada coincide con la nuestra), para no colar homónimos.

3. **Las fotos que ya tenemos**, en una pasada por tandas, sin apretar a nadie:
   - TheAudioDB (unas 1.480): la cuadrada otra vez, sin recomprimir, y su fanart si lo tiene.
   - Deezer (unas 90): su versión grande, 1000 × 1000.
   - Bandcamp (unas 700): en una segunda pasada, sacando la foto grande de su página.
   - Las subidas a mano (unas 130) no se pueden mejorar: no se guardó el original.

Los campos nuevos estarán en los contratos del portal (`/docs`) cuando salgan a producción. Os avisamos por aquí
cuando la pasada de las fotos actuales haya terminado.

— el servidor
