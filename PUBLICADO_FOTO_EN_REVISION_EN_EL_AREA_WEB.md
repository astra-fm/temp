# Publicado · La foto en revisión en el área de artista

**De:** el front de la web · **Para:** servidor · **Fecha:** 10.11.26

Sobre `PETICION_FOTO_EN_REVISION_EN_EL_AREA_WEB.md`. Está en producción.

1. **Al subir la foto** sale su miniatura (el archivo local) con «En revisión · La redacción la revisará antes de
   publicarla». Al recargar, «Foto en revisión».
2. **El campo del enlace arranca vacío.** La foto publicada se sigue viendo en la columna «Cómo se ve hoy».
3. **«En revisión» por campo**, uniendo los `campos` de todas las pendientes. La marca de la foto estaba arriba del bloque
   «Quién sois» y parecía del bloque entero; ahora va junto a la foto. Con solo la foto pendiente, la bio no se marca.
4. **Vuestra pregunta:** antes el formulario solo mandaba `foto` si el campo era distinto de `cuenta.ficha.foto`, así que
   en la prueba de Pablo no se reenvió. Pero si el artista vaciaba el campo, mandaba `foto: null`. Ahora `foto` solo
   viaja si el artista pega un enlace. Comprobado contra un servidor simulado: subir la foto y cambiar después la bio
   manda `{"bio": "…"}` y nada más.

## Una cosa nueva: «Quitar la foto»

Pablo ha pedido poder quitar la foto publicada. Es un botón junto a la foto; con él marcado, «Enviar cambios» manda
`foto: null` en `POST /cuentas/ficha/proponer`, que en el contrato es «petición de vaciarlo». Si el artista pega un
enlace, gana el enlace y el botón se desactiva.

**Una duda:** si hay una foto subida en revisión y llega `foto: null`, ¿la sustituye («gana la última») o convive con
ella? Si la sustituye, quien sube una foto y luego pide quitar la publicada pierde la que acaba de subir. Decidnos qué
hace hoy y, si hace falta, lo ajustamos aquí.

No se ha hecho ninguna subida real desde la web después de este cambio.

— el front de la web
