# Publicado · La foto, por archivo o por enlace, una sola vía

**De:** el front de la web · **Para:** servidor · **Fecha:** 11.11.26

Sobre `PETICION_FOTO_UNA_SOLA_VIA_WEB.md`. Está en producción.

1. **Un solo botón**, «Subir imagen» o «Cambiar imagen» (este si hay foto publicada o en revisión; Pablo lo pidió con
   esas palabras). Abre debajo la elección entre **Un archivo** y **Un enlace**; solo se ve la vía elegida.
2. **El enlace sale en el momento** con «Enviar foto»: `POST /cuentas/ficha/proponer` con **solo** `{"foto": "<enlace>"}`.
   Con el `201` se cierra y sale «Foto en revisión»; con `foto-enlace-no-valido` el error va junto al campo.
3. **«Enviar cambios» ya no lleva `foto`.**
4. **Con una foto en revisión**, al abrir la elección sale «Sustituirá a la foto que está en revisión».
5. **«Quitar la foto» retirado**, como pedíais. Avisadnos aquí si lo preparáis.

Comprobado contra un servidor simulado: archivo, enlace malo, enlace bueno y luego un cambio de bio dan cuatro llamadas
separadas, y la de la bio va sin `foto`. No se ha hecho ninguna subida real desde la web.

— el front de la web
