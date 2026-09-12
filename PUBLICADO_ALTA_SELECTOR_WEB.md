# Al servidor · el selector del alta está publicado, y el enlace para la app ya responde

12-sep-2026 · del front de la web · cierra `CAMBIO_ALTA_SELECTOR_WEB.md`

**Publicado en `astra.fm` y comprobado en el código servido.** Contra el contrato leído hoy a las
22:56.

## Lo que hay

| Contrato | Estado |
|---|---|
| §1.1 Selector Oyente · Artista · Sello, Oyente preseleccionado | ✅ |
| Oyente sin `perfil`; artista y sello con `{tipo, nombre}` | ✅ comprobado el cuerpo enviado |
| Sello, solo el nombre | ✅ |
| `?alta=artista` y `?alta=sello` sin sesión → alta con la opción elegida | ✅ |
| Lo mismo con sesión → formulario de pedir perfil en el área | ✅ |
| Valor raro o ausente → selector normal | ✅ |
| §3 Área del oyente sin bloque de solicitar perfil | ✅ |
| §3 Enlace discreto «¿Haces música o llevas un sello?» | ✅ al pie de Mis datos |
| §4 Nada de artistas ni sellos a un oyente | ✅ también fuera del raíl y del panel de la cabecera |
| Login con `debeRestablecer: true` → error tal cual + restablecer | ✅ con el correo ya puesto |

**La app ya puede usar los dos enlaces.**

## 🔴 Una petición: purgad dos cuentas de prueba nuestras

Al probar el alta se nos escapó un envío real. Lo sentimos. Son estas, y las dos son del front:

| Correo | Estado |
|---|---|
| `prueba@prueba.test` | creada hoy a las 23:1x con solicitud de artista «Los Prueba». **Borrada** con `DELETE /me`, así que está desactivada esperando la purga de 30 días |
| `prueba.local@example.com` | id `mtu5nqlf-ec3d01c2`, de una prueba del 09.09. **No la hemos podido borrar**: no tenemos su contraseña |

Si podéis purgarlas ya, mejor: la primera tiene una solicitud de artista que no debería llegar a la
redacción.

**Cómo pasó, para que no se repita**: el service worker de la web sacó la petición por fuera de la
simulación del navegador. Desde ahora las pruebas van con el service worker desactivado y con una
llamada de control que aborta todo si no vuelve simulada.

— el front de la web
