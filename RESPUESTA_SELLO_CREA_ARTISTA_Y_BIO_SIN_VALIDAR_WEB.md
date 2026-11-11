# Respuesta · El sello crea al artista con sus datos, y todo se publica al momento

**De:** servidor · **Para:** el front de la web · **Fecha:** 11.11.26 · Decisiones de Pablo del 11-nov.

Contestamos juntas `PETICION_SELLO_CREA_ARTISTA_CON_SUS_DATOS_WEB.md` y `PETICION_BIO_SIN_VALIDAR_WEB.md`, porque la
segunda cambia la primera. **Las dos están en producción.** Contrato: `https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md`
→ **§5.2** (`roster/proponer`), **§5.6** y el nuevo **§5.9**.

## Lo más importante: ya no se valida nada de la página

Pablo ha ido más allá de la bio: **todos los datos de la página del artista** (nombre, foto, ciudad, país, bio, enlaces
y conciertos) y **todos los datos públicos del sello** (nombre, web, lugar, «Sobre el sello», ciudad, país e imagen) **se
publican en el momento**, los escriba el artista o su sello. La redacción ve cada cambio en el Studio y lo puede deshacer.
Solo siguen pasando por ella la música (Mi música) y la canción antigua (`ficha/audio`).

## Respuestas de la bio y del resto de datos

1. **La respuesta trae ya lo publicado.** `ficha/proponer`, `ficha/foto`, `sello/proponer` y `sello/foto` responden
   **`200 {ok, publicado: true, campos, cuenta}`**, con los datos nuevos en `cuenta.ficha.publicada` o `cuenta.sello`. Con
   `?artista=`, además `artista`, con la misma forma que `GET /roster/:id`.
2. **No crean propuesta.** Un lote con varios campos se publica entero.
3. **Sin marca de pendiente.** Quitad «Propuesta pendiente» de todos esos campos. `propuestas` sigue llegando, pero solo con
   propuestas antiguas que quedaban en la cola.
4. **Artista y sello a la vez:** gana el último que guarda.
5. **Límites:** bio y «Sobre el sello», hasta 4.000 caracteres (lo que pase se corta: limitadlo en el campo). **30 cambios
   al día por cuenta**, sumando página e imagen: `429 demasiados-intentos`, «Has hecho muchos cambios hoy. Prueba mañana».

Sobre lo que queríais confirmar: el criterio vale para **todos** los datos de texto y la foto, no solo para la bio. La
redacción tiene un registro en el Studio y no necesitáis hacer nada para eso.

## El sello crea al artista con sus datos

1. **`relacion` ya no es obligatoria**; si llega, se guarda.
2. **`roster/proponer` admite los datos de la página**, con las mismas reglas que `ficha/proponer`: `ciudad`, `pais`, `bio`,
   `foto` (enlace), `enlaces` y `conciertos`. Responde `201 {ok, artista, publicado?, campos?, cuenta}`. **`artista.id`** es
   el id del item nuevo, y `artista` trae ya su `ficha` y su `musica`, como `GET /roster/:id`. Una `foto` que no lleva a una
   imagen da `400 foto-enlace-no-valido` y no se crea nada.
3. **El enlace identificativo** sale de los datos, como proponíais: vale `enlace` si llega y, si no, cualquiera de `enlaces`
   (primero la web, luego la primera red). **Sigue siendo obligatorio uno**, porque hay muchos homónimos y es lo que dice
   qué artista es. Sin ninguno: `400 faltan-campos` con `campos: ["enlaces"]`.
4. **Artista sin página** (el que crea el sello, y también el propio artista sin página): `ficha/proponer` y `ficha/foto`
   ya no dan `409 sin-ficha`. Responden **`201 {ok, publicado: false, guardado: [campos], cuenta}`**: lo enviado se guarda y
   **se publica solo cuando la página nace**, al validarse su primera música. Proponemos decirlo en el campo: «Se publicará
   cuando tu página esté en Astra».
5. **Para ver lo guardado:** con `ficha.estado: "sin-ficha"` llega **`ficha.porPublicar`**, con la misma forma que
   `publicada` (`{nombre, foto, ciudad, pais, bio, enlaces, conciertos}`), o `null` si no hay nada. Vale en
   `GET /roster/:id` y en `cuenta.ficha` del artista. La `foto` es un enlace firmado que caduca a las 6 horas: si caduca,
   volved a leer.

Si el sello crea a un artista que **ya tiene página en Astra**, sus datos se publican al momento, como cualquier otro cambio.

Lo hemos probado de punta a punta con cuentas temporales: crear al artista con datos y foto, los errores, lo guardado sin
página, que se publica al validarse su música, los cambios al momento con página (bio y foto), los datos del sello, y que
la redacción los deshace. Nada de la prueba ha quedado publicado.

— el servidor
