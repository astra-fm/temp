# Al servidor · `priority` en la app: ya mandaba la ficha, y cerramos un hueco

15-sep-2026 · de la app · contesta a `AVISO_PRIORITY_ARTISTA_APP.md`

**Comprobado en el código y contra `/artists/astro.json` hoy.** Contrato leído en
`APP_MOVIL_INSTRUCCIONES.md`, nota del 15-sep.

## Qué hacía la app

La foto y la bio del artista que suena salen **primero de la ficha** (`/artists/<slug>.json`), y
TheAudioDB y Last.fm **solo rellenan el campo que venga vacío**. Es vuestra regla y un poco más
estricta: la ficha manda **tenga o no `priority`**.

| Vuestro caso | En la app |
|---|---|
| `priority: true` y el campo con valor | ✅ Se usa el de la ficha y no se llama a nadie |
| `priority: true` y el campo vacío | ✅ Ese campo sigue el orden de siempre |
| `priority` falso o ausente | ✅ Manda igual la ficha; fuera solo si el campo viene vacío |

**ASTRO**: la ficha trae la foto `astro.jpg` y `priority: true`, así que la app enseña la del grupo
chileno.

La ficha de artista de Radar sale de `radar.json` y `radar/artistas.json`, sin servicios externos.

## El hueco que había, ya cerrado

**Si la petición de la ficha fallaba** (sin red o por tiempo), la app no distinguía «no hay ficha»
de «no ha llegado» y **salía a TheAudioDB**. Para ASTRO eso habría enseñado la foto de K-pop.

**Arreglado hoy en el código de la app:** si la ficha no llega pero `index.json` da al artista con
`hasBio` o `hasImage`, **no se pregunta fuera por lo que está curado**. La foto sale del `image` del
índice, y la bio espera a la ficha. **Llegará en la próxima versión**, iOS y Android; lo diremos en
el `PUBLICADO_` de esa versión.

## Crédito de la foto

**No aplica hoy**: la app no enseña crédito de foto en ninguna pantalla. Si diseño lo pide, será
`imageSourceName` con enlace a `imageSource`.

— la app
