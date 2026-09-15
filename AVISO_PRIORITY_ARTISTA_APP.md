# A la app · la ficha con `priority` manda sobre TheAudioDB y Last.fm

15-sep-2026 · del servidor · amplía `APP_MOVIL_INSTRUCCIONES.md`

**Contrato:** `https://listen.astra.fm/docs/APP_MOVIL_INSTRUCCIONES.md`, apartado de
`/artists/<slug>.json` (nota del 15-sep). La regla completa es la de la web:
`ARTISTAS_WEB_INSTRUCCIONES.md` §2.1.

## Por qué os escribo

En la web ha salido un fallo: para **ASTRO**, la redacción tiene marcada su ficha con
`priority: true` y una foto propia del grupo chileno, pero la web enseña la foto de **ASTRO, el grupo
de K-pop de Seúl**, que es lo primero que devuelve TheAudioDB para ese nombre. La bio salía bien;
la foto no miraba `priority`. Hay 34 artistas priorizados, 24 con foto propia.

Vuestro contrato no mencionaba `priority`, así que no sabemos si la app puede tener el mismo
problema. No vemos vuestro código: **comprobadlo, por favor.**

## La regla

La ficha `/artists/<slug>.json` trae ahora documentados `priority`, `imageSource` e
`imageSourceName` (ya viajaban desde el 9-sep).

- **Si la app solo usa la ficha** (bio e `image` de `/artists/<slug>.json`, sin servicios
  externos): no hay nada que cambiar. Decídnoslo y cerramos.
- **Si completa con TheAudioDB, Last.fm u otro servicio**, mirad `priority` **antes** de llamarlos:
  - `priority: true` y el campo con valor → usar el de la ficha, sin llamar al servicio.
  - `priority: true` pero el campo vacío → ese campo sigue vuestro orden de siempre.
  - `priority` falso o ausente → sin cambios.
- El crédito de la foto es `imageSourceName` (con enlace a `imageSource`), no el nombre del servicio.

## Cómo comprobarlo

La ficha de **ASTRO** (`/artists/astro.json`) debe enseñar la foto
`https://listen.astra.fm/artists/images/astro.jpg?v=…` (cuatro personas sentadas sobre maderas,
cielo azul), crédito «Quema su cabeza», país Chile y el Facebook del grupo. Esos datos se corrigieron
hoy en el servidor: antes traía país, año y enlaces de un artista japonés homónimo.

Respuesta: un `RESPUESTA_PRIORITY_ARTISTA_APP.md` aquí, diga lo que diga (aunque sea «no aplica»).

— el servidor
