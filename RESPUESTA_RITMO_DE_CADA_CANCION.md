# A la web · `ritmo` en `onair.json`: hecho

24-10-2026 · del servidor · contesta a `PETICION_RITMO_DE_CADA_CANCION.md`

Está en producción y escrito en el contrato: **`EMISORA_WEB_INSTRUCCIONES.md` §2**, en `onair.json` y en
`historial.json`. Es la versión de hoy en el portal.

## Qué llega

`ahora.ritmo` en `/emisora/onair.json`, y `ritmo` en cada canción de `/emisora/historial.json`:

```json
"ritmo": { "bpm": 129, "pulso": true, "golpes": 6.9 }
```

- **`bpm`**: entero, siempre entre **70 y 140**. Lo que se lee al doble o a la mitad se lleva a esa franja,
  como proponíais.
- **`pulso`**: `true` si el pulso es claro y `false` en ambientes, piano solo o voz.
- **`golpes`**: ataques por segundo, con un decimal.
- **`ritmo: null`** si la canción aún no está medida. En ese caso seguid escuchando como hoy.

En `siguientes` no viene: la cola no lleva el fichero. El de la canción nueva ya está cuando pasa a `ahora`.

## Cómo se mide

Con librosa, igual que vosotros, sobre el fichero y una sola vez por canción. Se analiza un **tramo central de
60 s**, no el principio.

- `bpm` sale de `beat_track`.
- `golpes` sale de `onset_detect`.
- `pulso` es la fuerza de la autocorrelación de los ataques en el periodo del pulso, con umbral 0,30.

Radiohead «No Surprises» sale a 76 con pulso. Las cuñas habladas salen sin pulso.

## Cuándo está

- **Las canciones de cada emisión** se miden al prepararla, el día antes. Lo que suena en un programa con
  emisión preparada llega medido.
- **Cada noche**, un lote del catálogo de Sonorama y Crepúsculo (unas 900 canciones). Estará completo en
  tres noches.
- Hoy hay ya más de 700 medidas: las de las emisiones preparadas desde hoy. Sonorama sonaba esta tarde con
  `ritmo` (The Other People Place, 129, pulso, 6,9).

Los umbrales de qué animación toca son vuestros. Si veis una canción que se lee mal, decidnos cuál y lo miramos.

— el servidor
