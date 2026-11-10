# Petición · La foto, por archivo o por enlace, pero una sola vía

**De:** servidor · **Para:** el front de la web · **Fecha:** 10.11.26 · Pedido por Pablo.

Sobre `PUBLICADO_FOTO_EN_REVISION_EN_EL_AREA_WEB.md`. Gracias: la foto en revisión ya se entiende. Pablo ha seguido
probando y pide cambiar cómo se elige la foto.

## Lo que pide Pablo

Hoy el bloque enseña a la vez el campo del enlace y «O sube un archivo», y se pueden usar los dos uno detrás de otro. Pablo
lo quiere **como una elección: o una cosa o la otra.**

1. **Un solo botón, «Añadir foto»** (o «Cambiar foto» si ya hay una en revisión), que ofrezca las dos opciones: **subir un
   archivo** o **pegar un enlace**. El artista escoge una, y la otra no se ve mientras tanto.
2. **El enlace se envía en ese momento**, como el archivo: con su propio botón («Enviar foto»), en una llamada a
   `POST /cuentas/ficha/proponer` que lleve **solo** `{"foto": "<enlace>"}`. Así las dos vías se comportan igual: al
   recibir el `201` sale «Foto en revisión», y si el enlace no vale, el error (`foto-enlace-no-valido`) sale junto a la foto.
   Hoy, si el enlace va en el mismo «Enviar cambios» que la bio y no vale, el servidor rechaza el envío entero y **la bio
   tampoco se guarda**.
3. **«Enviar cambios» ya no lleva `foto`.** Se queda para la bio, la ciudad, el país y los enlaces.
4. Si ya hay una foto en revisión y el artista elige otra, avisad antes de enviar: «Sustituirá a la foto que está en
   revisión». Es lo que hace el servidor: gana la última, venga por archivo o por enlace.

## Vuestra duda: `foto: null`

Lo que hace hoy el servidor:

- **Sustituye a la foto en revisión.** Un `foto: null` cuenta como una foto nueva y retira la que estuviera pendiente,
  también si era un archivo subido. Es justo el caso que temíais: se pierde la foto que acaba de subir.
- **Al aprobarlo, no quita la foto publicada.** El servidor solo cambia la foto de la ficha cuando hay una imagen nueva.
  Un `foto: null` aprobado no hace nada.

O sea, hoy «Quitar la foto» no cumple lo que promete y además puede borrar la foto en revisión. **Ocultad el botón** hasta
que lo confirmemos con Pablo y lo preparemos en el servidor; os avisaremos aquí.

Cuando esté publicado, dejad `PUBLICADO_FOTO_UNA_SOLA_VIA_WEB.md`.

— el servidor
