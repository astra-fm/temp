# Petición · La foto en revisión no se ve en el área de artista

**De:** servidor · **Para:** el front de la web · **Fecha:** 10.11.26 · Pedido por Pablo.

Sobre `PUBLICADO_CANCION_EN_EL_AREA_DE_ARTISTA_WEB.md`. Pablo ha probado el área con una ficha real, en «Mi ficha
pública». En el servidor todo llegó bien: la canción (MP3, 3:42, con los derechos confirmados) y la foto subida como
archivo quedaron en revisión. La canción se entiende bien en pantalla («En revisión · La redacción la escuchará antes de
publicarla»). Con la foto, el artista se queda sin saber si ha funcionado.

## Lo que vio

1. Sube una foto con «O sube un archivo». La barra de progreso sale un segundo y desaparece.
2. No aparece ningún mensaje ni se ve la foto que ha subido.
3. El campo **Foto** sigue mostrando el enlace de la foto publicada
   (`https://listen.astra.fm/artists/images/<slug>.jpg`), como si no hubiera pasado nada.
4. La única señal es la etiqueta «En revisión» de arriba del bloque «Quién sois», que vale para el bloque entero y no
   dice qué está en revisión.

## Qué pedimos

1. **Respuesta al subir.** Al recibir el `201`, enseñad junto al campo la foto que acaba de subir (podéis usar el archivo
   local, con `URL.createObjectURL`) con «En revisión» y un texto corto, igual que en la canción: «La redacción la revisará
   antes de publicarla». Si se recarga la página, basta con «Foto en revisión» (el servidor no da la imagen pendiente al
   artista).
2. **El campo del enlace, vacío si hay una foto en revisión.** Que no muestre la dirección de la foto publicada como si
   fuera lo último enviado. Si queréis enseñar la publicada, que sea aparte y con su nombre: «Foto actual».
3. **El «En revisión» de cada campo.** Ya lo pedíamos en `RESPUESTA_PROPUESTAS_PENDIENTES_DE_LA_FICHA_WEB.md`: unid los
   `campos` de todas las propuestas pendientes y marcad cada campo, no el bloque entero. Si solo está la foto, la bio no
   debería aparecer en revisión.
4. **Enviad solo los campos que el artista ha cambiado** en `POST /cuentas/ficha/proponer`. Esto es lo importante: si
   `foto` llega con un enlace, el servidor lo toma como una foto nueva, la descarga y **sustituye a la que esté en
   revisión** (gana la última). Si el artista sube un archivo y luego corrige la bio, y el formulario reenvía el campo
   Foto con la dirección de la foto publicada, se pierde la foto que acaba de subir y vuelve a revisión la que ya
   teníamos. Decidnos si hoy el formulario manda `foto` cuando no ha cambiado.

## Cómo comprobarlo

Con una cuenta de artista vinculada a una ficha: subir una foto por archivo, ver la foto y «En revisión» enseguida, cambiar
solo la bio y guardar. En el servidor deben quedar dos propuestas: la foto (archivo) y la bio, sin un `foto` nuevo.

Cuando esté publicado, dejad `PUBLICADO_FOTO_EN_REVISION_EN_EL_AREA_WEB.md`.

— el servidor
