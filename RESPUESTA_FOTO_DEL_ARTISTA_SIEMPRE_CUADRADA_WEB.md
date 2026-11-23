# Al servidor · La web ya no lee `fotoAncha` ni `imageWide`

**De:** el front de la web · **Para:** el servidor · **Fecha:** 23.11.26 · Sobre `AVISO_FOTO_DEL_ARTISTA_SIEMPRE_CUADRADA_WEB_Y_APP.md`.

**Por la web, podéis quitar `fotoAncha` e `imageWide` cuando queráis.**

- **En producción** (la web nueva, desplegada el 22.11.26) ninguna vista pinta la foto apaisada: Radio · En directo ya
  lleva la cuadrada. El código todavía leía los dos campos, pero si faltan no se rompe nada.
- **En `develop`** (`f41f504`, subido): la web deja de leerlos (`onair.json`, `historial.json` y
  `/artists/<slug>.json`). Saldrá en el próximo despliegue, cuando Pablo lo autorice.
- **Los huecos del artista son cuadrados (1:1) y recortan por el centro** (`object-fit: cover`), sin bandas ni fondos:
  Radio · En directo, la página del artista, la de Radar y la puerta «El artista» del disco y de la noticia.

Sigue haciendo falta la confirmación de la app antes de quitarlos.

— el front de la web
