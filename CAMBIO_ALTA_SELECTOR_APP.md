# A la app · el alta empieza por un selector, y Artista y Sello abren la web

12-sep-2026 · del servidor · **cambio de contrato**

## Qué cambia

Decisión de Pablo: **que el alta y el área del oyente queden limpios.** El alta empieza por un
selector de tres opciones, igual que en la web.

**El servidor no cambia.** Contrato actualizado en el portal:

`https://listen.astra.fm/docs/CUENTAS_APP_INSTRUCCIONES.md` → §4.

## Lo que hay que construir

**1 · Selector como primer paso**, con Oyente preseleccionado.

**2 · Qué hace cada opción:**

| Opción | Qué pasa |
|---|---|
| **Oyente** | alta dentro de la app: correo, contraseña y 3 consentimientos (el `dispositivo` lo añadís vosotros) |
| **Artista** | abre en el navegador `https://astra.fm/cuenta?alta=artista` |
| **Sello** | abre en el navegador `https://astra.fm/cuenta?alta=sello` |

La web abre el alta con esa opción ya elegida, así que el usuario no escoge dos veces.

⚠️ **La regla del 2-sep sigue en pie**: la app **nunca crea perfiles**. No mandéis `perfil` en
`register` ni llaméis a `POST /cuentas/perfil`. El recorrido de artista y sello es de la web,
porque es donde se envía la música.

**3 · Área de oyente limpia** si `cuenta.perfiles` viene vacío: sin nada de artistas ni sellos.
Como mucho, un enlace discreto «¿Haces música o llevas un sello?» que abre la misma URL de la web.
Si la cuenta trae una solicitud hecha en la web, se sigue mostrando su estado en consulta, como
hasta ahora.

## Un aviso aparte, y es una corrección

Vuestro contrato decía que las cuentas migradas desde WordPress validan su primer login contra
WordPress. **Ya no**: se cortó el 11-sep. Esas cuentas reciben ahora un `401` con
`debeRestablecer: true`. Pintad el `error` tal cual y llevad al usuario a restablecer la
contraseña. El campo es aditivo.

## Una dependencia con la web

El enlace profundo `?alta=artista|sello` lo tiene que construir la web. Hasta que lo tengan, esas
dos URLs abren el selector normal, que funciona igual pero obliga a elegir otra vez. No bloquea
vuestra parte.

— el servidor
