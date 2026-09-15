# Al servidor · el embudo del alta ya manda `alta_abierta` y `alta_error`

15-sep-2026 · del front de la web · responde a `CAMBIO_EMBUDO_ALTA_WEB.md`

Publicado en producción hoy. Contrato leído antes de escribir: `ANALITICA_WEB_INSTRUCCIONES.md`,
§5 y «El alta, paso a paso», con la cabecera del 15-sep.

## Lo que sale ahora

- **`alta_abierta`.** Se manda al llegar a la pantalla del alta sin sesión, y cuentan las dos
  formas de llegar: la página que nace en «crear» —`?crear=1` o `?alta=`— y el cambio desde
  «entrar» con la tarjeta de arriba. Contar solo la primera dejaba `alta_iniciada` sin apertura
  delante. `desde` dice `enlace` solo con `?alta=artista|sello`; `?crear=1` es un atajo de esta
  web y cuenta como `web`.
- **`alta_error`.** En el fallo de `POST /cuentas/register`. `409` → `correo_registrado`,
  `429` → `limite`, `400` → `datos`, y cualquier otra respuesta o la red caída → `otro`.
- **El selector entra sin ninguna opción marcada**, que es lo que pedisteis el 13-sep y se había
  quedado sin hacer. Va en su propio acuse, `PUBLICADO_SELECTOR_SIN_OPCION_WEB.md`.

## Qué comprobamos

Medido en el navegador con un Umami simulado que apunta lo que recibe, y con `/cuentas/*`
interceptado y contestado en local: ninguna cuenta real tocada, ninguna visita falsa en el panel.

Lo que pedíais saber, primero:

- **`?alta=artista` manda los dos, y en ese orden:** `alta_abierta` con `desde: enlace` y después
  `alta_iniciada` con `tipo: artista`.
- **Un correo ya registrado** deja `alta_error` con `{tipo, motivo: correo_registrado}` y nada más:
  ni el correo, ni el mensaje del error, ni nada tecleado.

Y el resto del recorrido:

| Caso | Qué sale |
|---|---|
| `?crear=1` | solo `alta_abierta` `{desde: web}` |
| `/cuenta` a secas, que abre en «entrar» | nada, hasta pulsar «Crear cuenta» |
| Se pulsa «Crear cuenta» | `alta_abierta` `{desde: web}` |
| Se elige Oyente y luego Sello | **una sola** `alta_iniciada` `{tipo: oyente}` |
| `429` · `400` · `500` · red caída | `limite` · `datos` · `otro` · `otro` |

## Una cosa que decidimos nosotros, por si no es lo que queríais

El contrato dice que una apertura es «cada vez que alguien llega a la pantalla del alta».
En la web, `/cuenta` es **una sola pantalla con varios momentos**: abre en «entrar» salvo que llegue
`?crear=1` o `?alta=`, y desde ahí se pasa a «crear» sin navegar. Hemos contado ese paso como una
apertura, porque si no, quien entra por «entrar» y decide crear cuenta mandaría `alta_iniciada`
sin `alta_abierta` delante y el embudo volvería a salir descuadrado.

El efecto secundario: quien va y viene entre «entrar» y «crear» suma varias aperturas. Si preferís
contar solo la llegada a la página, lo cambiamos en un momento.

## Lo que falta, y no es vuestro

La otra mitad de la decisión del 13-sep: el alta **en dos pasos**, con el selector primero y el
formulario solo después de elegir. Hoy el selector entra vacío, pero sigue debajo de los datos de
acceso. Eso mueve la pantalla, así que va por diseño.

— el front de la web
