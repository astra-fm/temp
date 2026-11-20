# A la web · Últimas 24 h (carrusel de noticias) e imagen apaisada de los programas

**De:** servidor · **Para:** la web · **Fecha:** 20.11.26

Dos cosas nuevas, ya en producción. La app ya las usa.

## 1. Últimas 24 h: `ultima-hora.json`

Pablo quiere el carrusel de noticias «Últimas 24 h» también en la web. Usa el mismo endpoint y el mismo contrato que la
app.

- `GET https://listen.astra.fm/ultima-hora.json`: público, sin clave, con CORS abierto. Se revalida con ETag
  (`max-age=0, must-revalidate`), así que pedidlo cada vez que se pinte la portada.
- La redacción publica cada noticia desde el Studio. Sale durante 24 horas y luego desaparece sola, aunque la redacción
  puede retirarla antes. Las noticias llegan ya filtradas y la más reciente va primero. **Si `noticias` viene vacío, no
  pintéis el carrusel.**
- **Tarjeta:** `imagen` (URL absoluta o `null`), `medio`, la hora (`publicadoEn`, en UTC, que se enseña en hora de
  Madrid), `titulo` y `resumen`.
- **Al tocarla se lee dentro, como un artículo**, también las de otros medios (decisión de Pablo):
  1. portada;
  2. medio · hora;
  3. `titulo`;
  4. `resumen` como entradilla;
  5. `contenidoHtml` como cuerpo (solo `<p>` y algún `<br>`);
  6. al final, la fuente: `fuente.medio` enlazado a `fuente.url`, que abre la noticia original.
- `tipo: "astra"` con `slug`: podéis llevar al post de Actualidad. Hoy todas son externas.

Ahora mismo hay cuatro publicadas, para probar: Hot Chip, Atlas Sound, Blondie y El Último de la Fila.

Contrato completo: `https://listen.astra.fm/docs/ULTIMA_HORA_INSTRUCCIONES.md`.

## 2. `imagenApaisada` en `programas.json`

Cada programa tiene ahora una imagen en 16:9, de hasta 1920 de ancho, que sube Pablo en el Studio. **Ya están las
nueve.**

- Es una ruta relativa (`/programas/images/<slug>_apaisada-<marca>.jpg`): poned delante `https://listen.astra.fm`.
- El nombre cambia en cada subida, así que usad la ruta tal cual llega.
- Es opcional: si algún diseño de la web pide el programa en apaisado, usad esta en vez de recortar la cuadrada.

Contrato: `https://listen.astra.fm/docs/PROGRAMAS_WEB_INSTRUCCIONES.md`.

— el servidor
