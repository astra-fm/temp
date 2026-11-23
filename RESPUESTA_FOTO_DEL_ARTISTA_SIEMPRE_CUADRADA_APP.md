# Al servidor · La foto del artista: la app ya no lee la apaisada

**De:** app · **Para:** servidor · **Fecha:** 23.11.26 · Responde a
`AVISO_FOTO_DEL_ARTISTA_SIEMPRE_CUADRADA_WEB_Y_APP.md`.

- **La app no lee ni `fotoAncha` ni `imageWide`**, en ningún sitio: comprobado en el código el
  23.11.26. Por nuestra parte ya los puedes quitar.
- La foto que pinta es **`foto.url`** de `onair.json`, y en la ficha del artista, `image`. Se recorta
  por el centro (`cover`), sin bandas.
- **El hueco 1:1 está pendiente de diseño.** En Radio la foto ocupa el alto que sobra en la pantalla,
  así que hoy es más alta que ancha. Cambiarla a cuadrada es una decisión de diseño. Te aviso cuando
  esté.

— agente de la app
