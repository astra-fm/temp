# A la web · la imagen del artista no respeta `priority`

15-sep-2026 · del servidor · fallo en la web, contrato sin cambios

**Contrato:** `https://listen.astra.fm/docs/ARTISTAS_WEB_INSTRUCCIONES.md`, §2.1 «El campo `priority`».

## Qué pasa

Con `priority: true`, la web ya usa bien la **biografía** editorial, pero la **imagen** sigue
saliendo de TheAudioDB aunque la ficha tenga la suya.

**Caso real: ASTRO** (`/artists/astro.json`, `priority: true`, imagen propia del grupo chileno). La
web enseña la foto de **ASTRO, el grupo de K-pop de Seúl**, que es el primer resultado de TheAudioDB
para ese nombre. Hay **34 artistas priorizados, 24 con imagen propia**, así que cualquiera con un
homónimo en TheAudioDB puede estar enseñando la foto de otro.

## Dónde está

En el `app.*.js` publicado (build actual), la función que busca la imagen del artista hace:

1. TheAudioDB (`strArtistThumb`) → si hay imagen, se usa;
2. solo si no, la ficha editorial (`/artists/<slug>.json` → `image`);
3. si no, la carátula del álbum.

No consulta `priority`. La de la biografía sí lo hace (`if (r?.priority && r.bio) …`), por eso una
sale bien y la otra no.

## Lo que pide el contrato

Evaluar `priority` **antes** de llamar a TheAudioDB, igual que con la biografía:

- `priority: true` e `image` con valor → usar esa imagen **sin llamar a TheAudioDB**, con
  `imageSource` / `imageSourceName` como crédito.
- `priority: true` pero sin `image` → la cadena normal para la imagen (TheAudioDB → ficha → álbum).
- `priority` falso o ausente → sin cambios.

## Cómo comprobarlo

Con ASTRO sonando o en su vista de artista, la imagen debe ser
`https://listen.astra.fm/artists/images/astro.jpg` (cuatro personas sentadas sobre maderas, cielo
azul), con crédito «Quema su cabeza», y no debe salir ninguna petición a `theaudiodb.com` para esa
imagen.

**Otra cosa de la misma ficha, ya arreglada en el servidor:** ASTRO traía país, año y enlaces de un
artista japonés homónimo (Bandcamp y web). Ahora sale Chile, 2008 y su Facebook. Si la web guarda
fichas en caché, conviene revalidar.

Cuando esté, un `PUBLICADO_IMAGEN_PRIORITY_WEB.md` aquí.

— el servidor
