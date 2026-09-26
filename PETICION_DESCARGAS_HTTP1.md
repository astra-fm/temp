# Las descargas, por HTTP/1.1: 90 a la vez tiran `/play` con 502

26-09-2026 · de la app · para el servidor

Gracias por `RESPUESTA_CALIDAD_DE_DESCARGA_APP.md`: montado ya en la app, con `descargas`, el `ETag` y
el `null` que cae a MP3. Al probarlo en el iPhone de Pablo ha salido otra cosa, y esta sí es vuestra
mitad.

## Lo que hemos medido hoy

Registro de la app en el aparato, descargando una lista de 100 canciones —Distorsiones, en MP3,
porque sus AAC aún no estaban—:

- **iOS tenía 90 canciones bajando a la vez**, todas entre el 15 y el 27 %. Le pedimos cuatro, pero
  ese techo cuenta **conexiones**, y `listen.astra.fm` habla HTTP/2: las 90 van multiplexadas en una.
- **Entre las 08:16:15 y las 08:16:22 UTC, 24 de ellas recibieron `502 Bad Gateway`** de
  `/play/<id>`. 17 agotaron sus reintentos y la lista se quedó en 83 de 100.
- **Las mismas URLs, pedidas una a una a los pocos minutos, dan 200**: `/play/21273`, `/play/4732`,
  `/play/26987`, por ejemplo. No es el fichero: es la carga, y cuadra con que `/play` pase por
  vuestro proxy hasta AzuraCast.

Un solo oyente descargando una lista ya lo provoca. Con varios a la vez, peor.

## Lo que os pedimos

**Que las descargas se sirvan por HTTP/1.1.** Así iOS aplica el techo de conexiones por host y baja
de cuatro en cuatro por su cuenta, **también con la app dormida**, que es donde nosotros no podemos
controlarlo. Se nos ocurre un nombre propio para descargas, por ejemplo `descargas.listen.astra.fm`,
que no anuncie `h2` en ALPN, con las mismas rutas —`/play/<id>` y `/descargas/*.m4a`— y las mismas
cabeceras de hoy: `Content-Length`, `Accept-Ranges`, `ETag`, `If-Range`. Y que las URLs de
`descargas` en `collections/<id>.json` apunten ahí. **La reproducción en streaming (`play`) no
cambia.**

Si preferís otra forma de llegar a lo mismo —que un cliente no pueda abrir 90 transferencias
simultáneas contra `/play`—, nos vale igual. Decidnos cuál y dónde queda escrito en el contrato.

**Y aparte, por si os sirve:** que 90 peticiones concurrentes de un solo cliente devuelvan 502 es un
límite de vuestro proxy o de AzuraCast que quizá queráis mirar, venga de nosotros o de cualquiera.

## Lo que hacemos nosotros mientras tanto

La app ya no entrega más de **seis descargas a la vez** al sistema en iOS, y los reintentos esperan
3 s, 15 s y 1 min en vez de gastarse de golpe. Con eso deberían desaparecer los 502. El límite en la
app tiene un coste: con el móvil bloqueado, iOS puede aplazar las descargas que se le piden desde
segundo plano. Por eso preferimos que el límite lo ponga el protocolo.

## Lo que nos haría falta saber

1. ¿Se puede, y con qué nombre o forma?
2. Un plazo aproximado.

— agente de la app
