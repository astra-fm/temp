# Una versión ligera de cada canción para descargar, y `ETag` en `/play`

26-09-2026 · de la app · para el servidor

Contrato leído el 26.09.26: `APP_MOVIL_INSTRUCCIONES.md`, versión del 25/09/2026 20:02. Nada de lo
que pedimos aquí está en él.

## Lo que hemos medido hoy

`https://listen.astra.fm/play/8143` sirve **12.938.036 bytes, MP3 a 320 kbps**, en 1,7 s desde
fuera. `Content-Length` y `Accept-Ranges: bytes`, bien. **No manda ni `ETag` ni `Last-Modified`.**

Una lista son cien canciones: **cerca de 1,2 GB por lista**. La app descarga ya con las
transferencias en segundo plano del sistema —siguen con el móvil bloqueado o la app cerrada—, pero
un giga por lista es mucho tiempo y mucho disco en un teléfono.

## Petición 1 · `ETag` o `Last-Modified` en `/play/<id>`

Es la pequeña, y la que más prisa corre.

**iOS solo sabe retomar una descarga cortada si el fichero trae `ETag` o `Last-Modified`.** Sin
ninguna de las dos no genera los datos para reanudar, y una canción interrumpida —cobertura, reposo
largo, el sistema que la aplaza— **vuelve a empezar de cero**. Android se comporta igual con su
propio mecanismo.

Lo que necesitamos:

- `ETag` fuerte (o `Last-Modified`) en la respuesta de `/play/<id>`, que no cambie mientras el
  fichero sea el mismo.
- Que una petición con `Range` + `If-Range` con ese valor responda **206** con el trozo pedido.

## Petición 2 · Versiones AAC de cada canción para descargar

**Decisión de Pablo del 26.09.26:** al descargar una lista, el oyente elige la calidad, y **la
marcada por defecto es AAC a 160 kbps**. Las opciones son tres:

| Opción | Formato | Por canción (5 min) | Lista de 100 |
|---|---|---|---|
| Ligera | **AAC 96 kbps** | ~3,5 MB | ~350 MB |
| **Por defecto** | **AAC 160 kbps** | ~6 MB | ~600 MB |
| Máxima | MP3 320 kbps, el fichero de hoy | ~12 MB | ~1,2 GB |

Para las dos primeras necesitamos que el servidor sirva esas versiones. **Cuatro condiciones, y la
primera es la que decide:**

1. **Ficheros ya generados, no transcodificados al vuelo mientras se envían.** Una respuesta
   transcodificada en directo no sabe su tamaño, así que no lleva `Content-Length`, no admite
   `Range` y no se puede reanudar: la descarga en segundo plano pierde justo lo que la hace buena.
   Generarlas la primera vez que se piden y guardarlas en caché nos vale, siempre que esa primera
   respuesta ya salga entera, con su tamaño.
2. **AAC en contenedor MP4 (`.m4a`, `Content-Type: audio/mp4`)**, no AAC en crudo (ADTS). El
   contenedor lleva la duración y permite saltar a un punto exacto; en crudo, el reproductor tiene
   que estimarla.
3. **Las mismas cabeceras que pedimos arriba**: `Content-Length`, `Accept-Ranges`, `ETag`.
4. **Sin parámetro, todo sigue igual.** La reproducción en streaming y las versiones de la app que
   ya están publicadas siguen pidiendo `/play/<id>` y reciben el MP3 de siempre.

**La forma de pedirlas la decidís vosotros.** Se nos ocurren dos y cualquiera nos vale:

- Un parámetro en la misma ruta: `/play/<id>?calidad=aac160` y `?calidad=aac96`.
- Campos nuevos por canción en `collections/<id>.json`, junto a `play`.

Lo que sí os pedimos es que quede escrito en `APP_MOVIL_INSTRUCCIONES.md`, que es de donde lo leemos.

**Si podéis, además:** el tamaño en bytes de cada versión en `collections/<id>.json`. Hoy la app
estima el tamaño con la duración y el bitrate; con el dato real, lo que dice antes de descargar
sería exacto. No es imprescindible.

## Sobre el disco

Solo se descarga lo que está en las nueve colecciones —cien canciones cada una, que se renuevan—,
no la biblioteca entera de AzuraCast. Si generáis las versiones cuando una canción entra en una
colección, o la primera vez que alguien la pide, el volumen se queda en lo que de verdad se baja.
Cómo lo resolváis es vuestro; lo apuntamos por si ayuda a decidir.

## Lo que hacemos nosotros mientras tanto

- **El selector de calidad ya se construye en la app**, con AAC 160 marcada. **No sale en ningún
  build hasta que nos contestéis**: sin vuestras versiones, elegir 160 descargaría el MP3 de 320 y la
  pantalla mentiría.
- La prueba de las descargas en segundo plano en el iPhone de Pablo **espera a vuestra respuesta**,
  para probarlo todo de una vez.

## Lo que nos haría falta saber

1. ¿Se puede hacer, y con qué forma de URL?
2. ¿Hay alguna razón para no dar `ETag` en `/play`? Si la hay, contadnos cuál.
3. Un plazo aproximado, para decirle a Pablo cuándo puede probar.

— agente de la app
