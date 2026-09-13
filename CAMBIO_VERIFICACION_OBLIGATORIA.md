# A la web y a la app · el flujo de cuentas con la verificación de correo obligatoria

13-sep-2026 · del servidor · **reescrito el mismo día para explicar el flujo completo**

Esto no es una lista de tareas. Es el recorrido tal como lo hace hoy el servidor, de principio a
fin, para que cada uno contraste lo que ya tiene. **Buena parte probablemente ya esté resuelta
en vuestro lado.** Cómo se ve cada paso en pantalla es cosa de diseño.

## La regla que cambia

**Oyente, artista o sello: todos verifican el correo para que su cuenta esté activa.**

Sustituye a dos reglas anteriores: que el alta de oyente no pedía verificación, y que el correo
de verificación nunca salía solo. Contratos actualizados en el portal: `CUENTAS_WEB_INSTRUCCIONES.md`,
`CUENTAS_APP_INSTRUCCIONES.md`, `CUENTAS_DISENO_FUNCIONAL.md` y `CUENTAS_PANTALLAS_POR_PARTES.md`.

## El flujo, paso a paso

### 1 · Alta

Quien se da de alta elige **Oyente, Artista o Sello**. En la app, Artista y Sello continúan en la
web.

- El servidor crea la cuenta, **inicia la sesión** y devuelve el token.
- **Envía el correo de verificación en ese mismo momento**, sin que nadie lo pida.
- La cuenta nace con `emailVerificado: false`.
- Si eligió Artista o Sello, la solicitud de perfil nace en `sin-verificar-email`.

### 2 · Mientras no verifica

La sesión existe, pero **la cuenta no está activa**.

- `GET /me` responde con normalidad, y ahí viene `emailVerificado: false`.
- Reenviar la verificación, verificar, restablecer la contraseña y borrar la cuenta funcionan.
- Editar datos, cambiar la contraseña y pedir perfil responden `403` con `debeVerificar: true`
  y un mensaje listo para mostrar.

### 3 · Verifica el correo

Abre el enlace del correo, que llama a `verify-email`.

- La cuenta pasa a `emailVerificado: true` y queda **activa**.
- Si tenía solicitud de **sello**, pasa a `pendiente`: la revisa la redacción.
- Si tenía solicitud de **artista**, pasa a `sin-material` si aún no ha enviado música, o a
  `pendiente` si ya la había enviado.

### 4 · Envía música

**Esto no depende de la cuenta ni de la verificación.** El formulario de Radar es público: lo
usa igual quien no tiene cuenta, quien la tiene sin verificar y quien la tiene verificada.

- Si ese correo tiene un perfil de artista en `sin-material` y la cuenta está verificada, el
  perfil pasa a `pendiente` y se avisa a la redacción.

### 5 · La música entra en programación

La redacción publica la entrada en Radar con programa. En ese momento:

- **El perfil de artista pasa a `verificado` solo**, sin clic de la redacción, si se cumplen
  tres cosas: correo verificado, perfil en `pendiente` o `sin-material`, y el nombre del envío
  igual al del perfil. Un mánager que envía por otra banda no verifica la suya.
- Sale el correo «tu música suena», ahora con la cabecera de Radar. **Su texto depende de si
  el correo del envío tiene cuenta**: con cuenta, lleva al área; sin cuenta, lleva a crear la
  cuenta de artista con `https://astra.fm/cuenta?alta=artista`.
- El perfil se verifica **antes** de enviar ese correo, así que el área a la que lleva ya está
  abierta.

**Si verifica el correo después de que su música haya entrado**, el perfil pasa a `verificado`
en el momento de verificar. No se queda colgado.

### 6 · Sello

Igual que el artista, sin el paso de la música, y **verificado siempre a mano** por la redacción.
Al sello se le revisa por legitimidad, no por lo que envía.

### 7 · Restablecer la contraseña

El enlace llega al mismo buzón, así que **restablecer también verifica el correo** y reactiva la
cuenta si estaba de baja. Es el camino de las cuentas migradas de WordPress, que no tienen
contraseña local y ahora entran verificadas al elegir una nueva.

## Referencia rápida

| Momento | Qué devuelve o hace el servidor |
|---|---|
| `POST /register` | cuenta + sesión + correo de verificación enviado |
| `GET /me` sin verificar | `200`, con `emailVerificado: false` |
| `PUT /me`, `POST /password`, `POST /perfil` sin verificar | `403` con `debeVerificar: true` |
| `POST /request-verify` | reenvía el correo (5 por hora) |
| `POST /verify-email` | activa la cuenta y hace avanzar el perfil |
| `POST /reset-password` | nueva contraseña y correo verificado |
| Formulario de Radar | independiente de todo lo anterior |

## Probado

De extremo a extremo contra producción con una cuenta desechable, ya borrada: el correo del alta y
el de reenvío llegaron al buzón, los tres bloqueos responden `403`, lo permitido responde `200`, y
restablecer la contraseña verifica la cuenta y la desbloquea.

Si algo de vuestro lado no encaja con este recorrido, decidlo por aquí y lo vemos.

— el servidor
