# Aviso · El sello gestiona a sus artistas, en producción

**De:** servidor · **Para:** el front de la web · **Fecha:** 11.11.26 · Decisiones de Pablo del 11-nov.

Sobre la parte del sello de `PETICION_AREA_ARTISTA_Y_SELLO_MI_MUSICA_WEB.md`. **La fase 2 está en producción.** Contrato:
`https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` → **§5.6**. Aquí, la respuesta punto por punto.

## S1 · Escribir en nombre de un artista

Mismas rutas del artista con **`?artista=<id del roster>`**: `POST /musica`, `POST /ficha/proponer` y `POST /ficha/foto`.
Las rutas de un material ya creado (`/musica/:id/canciones`, `/cerrar`, los `DELETE`) no lo necesitan: el material es de la
cuenta que lo subió.

- **Solo un sello `verificado`**, y solo con los artistas **`verificado`** de su roster. Un sello en `pendiente` no gestiona
  artistas (sí puede proponerlos y proponer sus datos, como hasta ahora).
- **Artista sin página** (`ficha: null`): puede recibir música; al validarse su primer material, su página nace. Proponer
  cambios de la página necesita que exista: `409 sin-ficha`.
- **Errores**: `403 sello-no-verificado` y `404 artista-no-encontrado` (el id no es un artista verificado de su roster).

## S2 · Leer cada artista

- **Rejilla**: cada item de `roster.items` trae ya `foto`, `ciudad`, `pais` y **`pendientes`** (materiales esperando
  validación). Con eso pintáis la tarjeta y la marca «N PENDIENTE» sin abrir nada.
- **Gestión**: `GET /cuentas/roster/:id` → `{ok, artista: {id, nombre, slug, estado, ficha, musica}}`, con la misma forma que
  `cuenta.ficha` y `cuenta.musica` del artista.

## S3 · Datos nuevos del sello

En `POST /sello/proponer`: `sobre` (texto), `ciudad` y `pais` por separado (**`lugar` se mantiene**) e **imagen** por enlace en
`foto`. Por archivo: `POST /cuentas/sello/foto`, con las mismas reglas y códigos que `ficha/foto`. Una sola imagen en revisión
por sello: gana la última. `cuenta.sello` trae además `foto`, `sobre`, `ciudad` y `pais`.

## S4 · Página pública del sello

Sigue sin existir. Es la fase 3: os escribiremos cuando la empecemos.

## S5 · Artista y sello a la vez

- **Las dos propuestas conviven** en la cola de la redacción, cada una con `remitente` (`artista` o `sello`, y
  `selloNombre`). No se sustituyen, salvo la foto y la canción: solo hay una en revisión por artista, venga de quien venga.
- **Cada uno ve lo del otro**: el artista ve en `cuenta.ficha.propuestas` y en `cuenta.musica.items` lo que propuso o subió
  su sello, y el sello ve lo del artista en `GET /roster/:id`. Cada item lleva `remitente` y, en la música, `puedeBorrar`:
  cada material lo borra quien lo subió.
- **Quitarle el permiso al sello**: más adelante, no ahora.

## S6

Ya contestado en `AVISO_MI_MUSICA_LISTO_WEB.md`: `409 perfil-incompatible`.

## S7 · Borrar la cuenta del sello

Una corrección al texto del diseño: **se borra del todo a los 30 días, no a los 90**. Hasta entonces, entrar de nuevo la
recupera. Al darse de baja, su **material sin validar sale de la cola** al momento; lo validado, las páginas de sus artistas
y lo editorial se quedan.

## Dependencia 8 · Límite

El de un sello es el triple que el de un artista: 30 materiales al día y 180 canciones por hora.

Lo hemos probado de punta a punta con cuentas temporales: los permisos y sus errores, subir y validar música para un artista
sin página, que la página nace con el slug del artista (nunca del sello), las propuestas con remitente, los datos e imagen del
sello y la baja. Nada de la prueba ha quedado publicado.

— el servidor
