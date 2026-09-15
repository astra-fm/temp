# Al servidor · la imagen del artista ya respeta `priority`

15-sep-2026 · del front de la web · respuesta a `FALLO_IMAGEN_PRIORITY_WEB.md`

**Contrato:** `https://listen.astra.fm/docs/ARTISTAS_WEB_INSTRUCCIONES.md`, §2.1 y §2.2.

## Qué hemos cambiado

Está en producción desde hoy. La web lee la ficha antes de buscar la imagen, y la resuelve **campo a campo**, igual que la biografía:

- `priority: true` e `image` con valor → esa imagen, con `imageSource` / `imageSourceName` como crédito y **sin llamar a TheAudioDB**.
- `priority: true` sin `image` → la cadena normal: TheAudioDB → ficha → carátula.
- `priority` falso o ausente → sin cambios.

## Otro fallo del mismo caso, ya arreglado

Si la biografía priorizada evitaba TheAudioDB, los datos de «Trayectoria» (origen, años, género, sello, miembros) **se quedaban los del artista que sonó antes**. Ahora se vacían, y la web pinta lo que trae `musicbrainz`: país y años con su desambiguación.

## Cómo lo hemos comprobado

En local, reescribiendo la respuesta de `nowplaying` para que suene ASTRO:

- Imagen `https://listen.astra.fm/artists/images/astro.jpg`. Ninguna búsqueda de ASTRO en `theaudiodb.com`.
- «Trayectoria»: **Chile · En activo desde 2008 · Chilean band**.
- Al pasar de New Order a ASTRO no queda nada de New Order en la ficha.

## Una cosa vuestra: los relacionados de ASTRO

`GET /relacionados/astro` devuelve **Pentagon (100 %), VAV (74 %) y Shaun (41 %)**, que son artistas de K-pop. Tiene pinta de ser el mismo homónimo, esta vez por `artist.getSimilar` de Last.fm buscando por nombre. La web los pinta tal cual, como pide el §8. Si tenéis forma de atarlo al artista correcto, o de no servirlo cuando la ficha está priorizada, nos ayudaría.

— el front de la web
