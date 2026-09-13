# A la web y a la app · verificar el correo pasa a ser obligatorio para todos

13-sep-2026 · del servidor · **cambio de contrato, afecta a los dos**

## La regla

Decisión de Pablo: **oyente, artista o sello, todos verifican el correo para que su cuenta esté
activa.** Sustituye a lo que decían vuestros contratos, que el alta de oyente no pedía
verificación y que el correo de verificación nunca salía solo.

Contratos actualizados en el portal: `CUENTAS_WEB_INSTRUCCIONES.md` §1 y §2, y
`CUENTAS_APP_INSTRUCCIONES.md` §4.

## Qué cambia en el servidor (ya en producción)

**1 · El alta envía sola la verificación.** Ya no hace falta que la pidáis tras registrar.
`POST /cuentas/request-verify` queda solo para **reenviar**.

**2 · Una cuenta sin verificar inicia sesión, pero solo puede ver su aviso.** Estos responden
`403` con `debeVerificar: true` y un `error` listo para pintar:

```
PUT  /cuentas/me
POST /cuentas/password
POST /cuentas/perfil
```

Siempre permitido sin verificar: `GET /me`, reenviar, verificar, restablecer contraseña y
`DELETE /me`.

**3 · Restablecer la contraseña verifica el correo**, porque el enlace llega al buzón. Es lo que
permite entrar a las cuentas migradas de WordPress sin pedirles un segundo paso.

**4 · Enviar música por Radar no depende de nada de esto.** El formulario sigue siendo público.

## Qué tenéis que construir

**Tras el alta**, en vez de ir al área completa: «Te hemos enviado un enlace a <correo>. Ábrelo
para activar tu cuenta.»

**El área de una cuenta sin verificar solo enseña eso**, con dos acciones: **reenviar el enlace**
y **borrar la cuenta**. Sin datos, sin suscripciones, sin perfil.

**Decidid con `cuenta.emailVerificado`**, que ya viene en todas las respuestas. No esperéis al
`403` para darle forma a la pantalla: el `403` es la red por si algo llama igualmente.

**Al volver del enlace de verificación**, recargad `GET /me`: el área completa aparece sola.

## Probado

De extremo a extremo contra producción, con una cuenta desechable que ya está borrada: el correo
del alta y el de reenvío llegaron al buzón, los tres bloqueos responden `403`, los permitidos
responden `200`, y restablecer la contraseña verifica la cuenta y la desbloquea.

— el servidor
