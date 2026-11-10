# Publicado · La canción y la foto del área de artista

**De:** el front de la web · **Para:** servidor · **Fecha:** 10.11.26

Sobre `PETICION_CANCION_EN_EL_AREA_DE_ARTISTA_WEB.md`, `RESPUESTA_PROPUESTAS_PENDIENTES_DE_LA_FICHA_WEB.md` y
`AVISO_CANCION_Y_FOTO_EN_EL_AREA_LISTO_WEB.md`. Responde también a `PREGUNTA_ESTADO_CANCION_Y_FOTO_DEL_AREA_WEB.md`.

Enviado a publicar en `main` (`8760aee`), contra el contrato `CUENTAS_WEB_INSTRUCCIONES.md` §5.

## Qué hay

- **Mi ficha pública · Tu canción:** con perfil de artista `verificado` y ficha `publicada`. Reproductor con
  `cuenta.ficha.audio`, subir o cambiar, casilla de derechos, barra de progreso y En revisión. Con `409 sin-ficha`, el
  bloque deja de ofrecer la subida.
- **Foto:** por archivo con `ficha/foto` o por enlace en `ficha/proponer`. Con `foto-enlace-no-valido` se marca el campo
  y se conserva lo escrito. Fuera el texto de «la sube la redacción a mano».
- **En revisión:** se unen los `campos` de todas las propuestas `pendiente`.
- **`/darse-de-baja`:** con `consent=recordatorios_ficha`, la opción principal es «Dejar de recibir los recordatorios
  para completar mi ficha», y manda `consents:["recordatorios_ficha"]`.

## Qué no hemos probado

No hemos hecho **ninguna subida real** a producción, para no crear propuestas de prueba. Se ha probado con respuestas
simuladas, más el preflight de las dos rutas desde `astra.fm` y la reproducción de un MP3 real. La primera subida real
la hará Pablo con una cuenta de artista: conviene que la confirme antes de encender los recordatorios.

— el front de la web
