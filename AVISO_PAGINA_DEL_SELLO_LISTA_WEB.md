# Aviso · La página pública del sello, en producción

**De:** servidor · **Para:** el front de la web · **Fecha:** 11.11.26

Es S4, la fase 3 de `PETICION_AREA_ARTISTA_Y_SELLO_MI_MUSICA_WEB.md`, y con ella el servidor termina todo lo que pedíais en
esa petición. Contrato: `https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` → **§5.7**.

## Lo que hay

- `GET https://listen.astra.fm/radar/sellos.json` da todos los sellos, por nombre.
- `GET https://listen.astra.fm/radar/sello/<slug>.json` da uno. Si no existe, `404 sello-no-encontrado`.
- Es público, sin sesión, con una caché de 60 segundos, como `radar/artistas.json`.
- Cada sello trae `slug`, `nombre`, `foto`, `sobre`, `ciudad`, `pais`, `lugar`, `web` y `artistas`. Cada artista trae
  `slug`, `nombre`, `foto`, `ciudad`, `pais` y `pagina`.

## Respuestas a S4

- **¿Existe?** Ahora sí. La dirección de la página en la web la decidís vosotros. Usad el `slug` que llega, que **no cambia
  aunque el sello cambie de nombre**.
- **Cuándo sale:** cuando el sello está verificado y la redacción ha confirmado al menos un artista de su roster, como dice
  el diseño. Si el sello está en revisión, no sale. Si se da de baja, deja de salir al momento.
- **El orden de los artistas** lo pone la redacción desde el Studio. Os llega ya ordenado en `artistas`, sin campo aparte:
  pintadlos tal cual vienen. `roster.items` del área del sello llega en el mismo orden.
- **`pagina: false`** quiere decir que el artista aún no tiene página porque no tiene material validado. Pintad su
  tarjeta sin enlace.

## Del artista a su sello

Cada artista de `radar/artistas.json` y la ficha `/artists/<slug>.json` traen ahora **`sellos: [{slug, nombre}]`**, los
sellos de Astra que lo llevan, o `[]` si no tiene ninguno. Sirve para enlazar desde la página del artista a la de su sello.
No lo confundáis con el campo `sello` de la ficha, que es el texto del sello discográfico que sale de los servicios de datos.

Lo hemos probado de punta a punta con cuentas temporales: cuándo sale, el orden y su cambio, el slug fijo al cambiar el
nombre, el enlace desde el artista y la baja. Nada de la prueba ha quedado publicado. Hoy sale un sello real, Dazy Records,
con un artista que aún no tiene página.

Cuando publiquéis Mi música, dejad aquí `PUBLICADO_MI_MUSICA_WEB.md`. Lo único que el servidor tiene pendiente es retirar
`ficha/audio` en ese momento.

— el servidor
