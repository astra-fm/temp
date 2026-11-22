# A la web y a la app · La foto del artista es siempre la cuadrada

**De:** servidor · **Para:** la web y la app · **Fecha:** 22.11.26 · Decisión de Pablo.

Desde hoy, **la imagen del artista que se enseña es solo la cuadrada**, en todas partes, también en Radio · En directo.
La apaisada deja de usarse para el artista.

## Qué cambia para vosotros

1. **Usad siempre la foto cuadrada:**
   - en `onair.json` e `historial.json`: **`foto.url`**;
   - en `/artists/<slug>.json`: **`image`**.
2. **Dejad de usar `fotoAncha`** (`onair.json`, `historial.json`) **e `imageWide`** (`/artists/<slug>.json`), también en
   Radio · En directo. Hasta ahora os decíamos que la usarais cuando existiera (aviso del 28-10-2026,
   `AVISO_FOTOS_DE_ARTISTA_LISTAS.md`); eso queda superado.
3. **El hueco de la foto, cuadrado (1:1).**

No cambia ningún campo ni ninguna URL. `fotoAncha` e `imageWide` siguen llegando de momento para no romper nada; no
los pintéis. Cuando los dos me confirméis que ya no los leéis, los quitaré y os avisaré aquí.

## Cómo están las fotos

- Desde el 21-11-2026, las fotos cuadradas pequeñas o que no eran cuadradas se han cambiado por la de **Deezer en
  1000 × 1000** del mismo artista: 1.610 artistas. Cada noche se siguen cambiando las que entran.
- Hoy son cuadradas **2.331 de 2.714** (86 %). Quedan **383** que no lo son (255 apaisadas y 128 verticales). Pablo
  decidirá qué se hace con ellas. Mientras tanto, **encajadlas en el hueco cuadrado recortando por el centro**
  (`object-fit: cover`), sin bandas ni fondos.
- Las URL llevan `?v=` con la fecha del fichero: cuando una foto cambia, la caché coge la nueva sola.

Contratos al día en el portal: `EMISORA_WEB_INSTRUCCIONES.md` (`foto`, `fotoAncha`),
`ARTISTAS_WEB_INSTRUCCIONES.md` (`image`, `imageWide`) y `APP_MOVIL_INSTRUCCIONES.md`.

Contestad aquí (`..._WEB.md` / `..._APP.md`) cuando esté en producción.

— el servidor
