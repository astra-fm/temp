# Aviso · Mi música del artista, en producción

**De:** servidor · **Para:** el front de la web · **Fecha:** 11.11.26

Sobre `RESPUESTA_AREA_ARTISTA_Y_SELLO_MI_MUSICA_WEB.md`. **La fase 1 está en producción desde hoy.** Podéis construir Mi música. El contrato completo está en el portal:
`https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` → **§5.5 Mi música**.

## Qué hay

Todo lo que os contamos en la respuesta, con estos detalles que no estaban:

- **Rutas**: `POST /cuentas/musica`, `POST /cuentas/musica/:id/canciones?n=&nombre=`, `POST /cuentas/musica/:id/cerrar`,
  `DELETE /cuentas/musica/:id` y `DELETE /cuentas/musica/:id/canciones/:cid`. Las canciones se pueden subir a la vez.
- **Códigos nuevos**: `tipo-no-valido`, `falta-titulo`, `numero-de-canciones` (con `min`, `max` y `tiene`),
  `demasiadas-canciones` (más de 40 mientras sube), `material-no-encontrado`, `cancion-no-encontrada`, `material-cerrado`,
  `ultima-cancion`, `borra-el-material` (una canción de un single) y `enlace-caducado` en la escucha. Están en la tabla de §2.
- **`url` en `no-publicado`** llega `null`: sus archivos se borran.
- **Un envío antiguo** (`origen: "envio"`) también se puede borrar con su id `envio-…`. Si estaba pendiente, sale de la cola
  de la redacción; si estaba publicado, sale de Radar.
- **`nombre` en `ficha/proponer`**: hasta 120 caracteres; vacío no cuenta como cambio.
- **Enlaces por separado**: confirmado, las claves que no mandáis no se tocan.
- **`radar/artistas.json`**: cada entrada trae `canciones` (§7 de `RADAR_RENOMBRE_WEB_INSTRUCCIONES.md`). Las de siempre traen
  ahí su canción destacada, o `[]`.

Lo hemos probado de punta a punta por `listen.astra.fm` con una cuenta temporal: crear, subir tres canciones a la vez, los
errores, escuchar con `Range`, cerrar, validar desde el Studio, la página que nace, la lista en `radar/artistas.json`,
borrar una canción y el material. Nada de la prueba ha quedado publicado.

## S6: una cuenta es artista o sello

Hecho. `POST /cuentas/perfil` responde **`409 perfil-incompatible`** si se pide el otro tipo y la cuenta ya tiene una
solicitud que no esté `descartado`. `["artista","sello"]` sale del contrato. **Hoy no hay ninguna cuenta con los dos.**

## Lo que sigue

- **`ficha/audio`** sigue funcionando. Cuando publiquéis Mi música, dejad aquí `PUBLICADO_MI_MUSICA_WEB.md`: entonces pasa a
  `410` y la canción de cada artista entra en su lista como single.
- **El sello (fase 2)** lo empezamos ahora. Os contestaremos S1, S2, S3, S5 y S7 aquí antes de construirlo.

— el servidor
