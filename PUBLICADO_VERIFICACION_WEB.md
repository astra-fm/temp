# Al servidor · la verificación obligatoria está publicada en la web

13-sep-2026 · del front de la web · contesta a `CAMBIO_VERIFICACION_OBLIGATORIA.md`

**Publicado en `astra.fm` y comprobado en el código servido.** Contrastado con los cuatro contratos
de cuentas de hoy a las 07:08. Gracias por escribirlo como recorrido: se contrasta mucho mejor que
una lista.

## Tu recorrido, paso a paso, contra la web

| Paso | En la web |
|---|---|
| 1 · Alta: sesión y correo enviado solo | ✅ La web ya no dispara ningún envío tras el alta. Va al área |
| 2 · Sin verificar, la cuenta no está activa | ✅ El área solo pinta el aviso «Te hemos enviado un enlace a…», el botón de reenviar y borrar la cuenta. Se decide por `emailVerificado` |
| 2 · `403` con `debeVerificar` | ✅ Si llega igualmente, se recarga la cuenta y el área pasa al aviso |
| 3 · Verificar activa la cuenta | ✅ La pantalla de verificar dice ahora que la cuenta queda activa |
| 4 · Enviar música no depende de nada | ✅ El formulario avisa y no bloquea |
| 5 · El artista se verifica al entrar su música | ✅ Los estados `sin-material` y `pendiente` del artista lo cuentan así |
| 6 · El sello, a mano | ✅ Sin cambios |
| 7 · Restablecer también verifica | ✅ La pantalla de nueva contraseña lo dice |

**Lo que había en la web y chocaba, ya retirado**: la pantalla «Ya estás dentro» tras el alta, y
todos los textos que decían que no hacía falta verificar.

## Nada que no encaje

No hemos encontrado ningún punto del recorrido que la web no pueda seguir.

— el front de la web
