# A la app · el alta cambia un poco: un reintento ya no da 409

24-sep-2026 · del servidor · **cambio de contrato, aditivo**. Nada de lo que tenéis hoy deja de
funcionar. Contrato: `https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` §2 y §2.1 (el
documento de endpoints es común; el vuestro, `CUENTAS_APP_INSTRUCCIONES.md`, sigue vigente tal cual).

## Por qué os llega esto

Veníamos de un problema de la web: altas que fallaban sin dejar rastro. Medido, resultó ser cosa de
los navegadores incrustados en otras apps (Instagram, Android WebView), de donde salían 44 de los 47
fallos. **A vosotros eso no os afecta**, sois una app nativa y no pasáis por CORS ni por service
workers. Pero uno de los arreglos toca `POST /cuentas/register`, que también es vuestro, y no
queremos que os lo encontréis sin avisar.

## Lo único que os cambia: el alta es idempotente durante dos minutos

Antes: dar de alta un correo que ya existe devolvía siempre `409`.

Ahora: si el **mismo correo** llega con la **misma contraseña** dentro de los **dos primeros minutos**
desde que se creó la cuenta, se devuelve **`201` con su token**, el mismo que se habría devuelto la
primera vez. Pasada la ventana, o con otra contraseña, `409` como siempre.

**Qué implica para vosotros**

- Si tenéis (o añadís) un reintento del alta cuando la petición se queda sin respuesta, **ya es
  seguro**: no va a crear dos cuentas ni a decirle «ya hay una cuenta con ese correo» a quien la
  acaba de crear. Ese era el caso malo: el alta entró, la respuesta se perdió en un túnel o en un
  cambio de wifi a datos, y el usuario veía un error habiendo tenido éxito.
- Si no reintentáis, **no tenéis que hacer nada**: el comportamiento que veis no cambia.
- ⚠️ **No os apoyéis en el `409` para decidir si un correo está libre.** Nunca fue para eso, pero
  ahora menos: en esa ventana de dos minutos un correo ocupado puede contestar `201`. Para saber si
  alguien tiene cuenta, el camino sigue siendo intentar entrar u ofrecer restablecer contraseña, como
  dice vuestro contrato en la nota de arriba.

## Lo demás, para que sepáis que existe y que no es para vosotros

- **`Content-Type: text/plain` aceptado** en toda la rama `/cuentas`. Es para que la web se ahorre el
  preflight de CORS, que en un navegador incrustado es justo lo que falla. **Una app nativa no tiene
  preflight**, así que aquí no ganáis nada: seguid con `application/json`.
- **`POST /cuentas/register-form`**, que acepta un `<form>` normal y responde con una redirección.
  Es la red de seguridad de la web para cuando su `fetch` no sale del navegador. **No lo uséis**: no
  devuelve token, a propósito, porque acabaría en el historial del navegador.
- **Registro de accesos de `/cuentas/`** en nuestro nginx. Interno. Si alguna vez nos decís «mandamos
  el alta y no pasó nada», ahora podemos responder con certeza si llegó o no, también para la app.

## Y una pregunta, que nos vendría bien

¿Vuestro alta reintenta cuando se queda sin respuesta? En móvil pasa más que en sobremesa (un túnel,
el paso de wifi a datos, la pantalla que se apaga a mitad de la petición). Si no lo hacéis y os
interesa, el patrón que le hemos propuesto a la web sirve igual: **un solo reintento, solo cuando no
hubo respuesta de ninguna clase**, nunca cuando contestamos algo, aunque sea un `500`. Con la
idempotencia de arriba, ya no tiene efectos secundarios.

Gracias.

— el servidor
