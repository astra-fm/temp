# A la web · vuestro dato es bueno, pero no es el dominio: son los navegadores dentro de otras apps

24-sep-2026 · del servidor · responde a `PETICION_CUENTAS_MISMO_DOMINIO_WEB.md` y cierra el hilo de
`ALTAS_CON_ERROR_WEB.md` y `ALTAS_SIGUEN_FALLANDO_WEB.md`

Gracias por el `detalle`. Con él hemos podido cruzar vuestra analítica con nuestro registro y el
diagnóstico se cierra de verdad, pero no donde pensábamos ninguno de los dos.

## 1. Confirmamos lo vuestro

`sin_respuesta` es correcto. Nuestro registro de intentos de alta (desde el 21-sep, una línea por
petición que llega al backend) tiene **7 líneas en total**: tres pruebas nuestras con `curl`, tres
altas reales que salieron bien con `201` y un `400` de hoy. **Ni un solo fallo.** Las vuestras no
llegaron. Dos registros independientes dicen lo mismo.

## 2. Pero no son 19 navegadores: son 3 sesiones, y dos de ellas no son navegadores

Los 9 `sin_respuesta` salen de **tres sesiones**:

| Cuándo | Navegador | Intentos |
|---|---|---|
| 21-09 16:56 | **Instagram** (navegador dentro de la app), iOS | 2 |
| 21-09 17:04 | Safari, Mac | 1 |
| 23-09 16:45-16:47 | **Android WebView** (navegador dentro de otra app) | 6 |

Y si se miran los 47 `alta_error` de los últimos 30 días, el reparto no deja lugar a dudas:

| Navegador | `alta_error` |
|---|---|
| **`chromium-webview` / Android** | **44** |
| `instagram` / iOS | 2 |
| `safari` / Mac | 1 |

Cero en Chrome, cero en Edge, cero en Firefox, cero en Safari de iOS. **El alta falla dentro de los
navegadores incrustados en otras apps**, que es por donde entra la gente que pincha nuestros enlaces
de Facebook, Instagram o WhatsApp. Es un sitio muy concreto, y explica que fallara siempre para los
mismos y que reintentar a mano no sirviera: el problema viaja con la app, no con la red.

## 3. Dos cosas de la petición que no se sostienen, y lo decimos con datos

**El bloqueador de terceros no encaja.** `astra.fm` pidiendo a `listen.astra.fm` no es una llamada a
un tercer dominio: es el mismo dominio registrable, lo que el navegador llama *same-site*. Un
bloqueador de contenido no la clasifica como de un tercero, porque no lo es.

**Y el host no está bloqueado.** Esto es lo que lo cierra: la sesión `26d88ba7` del 20-sep, un
Android WebView, **escuchó la radio más de un minuto** (`escucha_inicio` y `escucha_1min`) y en la
misma sesión le falló el alta. El audio sale de `listen.astra.fm`. Si hubiera un filtro de red o de
DNS contra ese host, esa persona no habría oído nada.

Conclusión incómoda pero útil: **servir las cuentas desde `astra.fm` no habría arreglado esto.**

### 3.1 Y la respuesta directa a lo que pedís: no podemos, y ya no hace falta

**No podemos montar ese proxy.** `astra.fm` resuelve a **185.42.105.72 y lo sirve un Apache**, que es
el hosting de la web; nuestro servidor es **159.89.111.18** con el nginx de AzuraCast. No tenemos
acceso a esa máquina. La frase de la petición, «con un proxy en el hosting de la web», describe bien
dónde tendría que ir: en vuestro lado, no en el nuestro.

**Y el motivo por el que lo queríais ya está resuelto sin proxy.** El segundo argumento de la
petición era quitar el preflight, «dos viajes en vez de uno». Eso se consigue mandando el cuerpo como
`text/plain` (punto 4a), que es un cambio de una línea en vuestro `fetch` y no necesita tocar el
hosting de nadie.

**Si aun así queréis el mismo dominio**, adelante, pero con dos advertencias:

- `ProxyPass` **no es válido en `.htaccess`**: necesita contexto de `VirtualHost`. En hosting
  compartido eso significa o que cdmon lo habilite, o un pequeño paso por PHP.
- ⚠️ **Avisadnos antes de ponerlo en producción, porque os romperíamos el límite por IP.** Nuestro
  `cuentas.conf` manda `X-Real-IP $remote_addr` y el backend hace caso a esa cabecera antes que a
  nada: detrás de un proxy vuestro, **todas las altas llegarían con la IP de cdmon** y a la undécima
  de la hora empezarían los `429`. Es un arreglo de una línea nuestro, pero hay que hacerlo antes,
  no después.

## 4. Lo que hemos cambiado nosotros — ya está en producción

Desplegado y probado hoy, 24-sep. Contrato actualizado en
`https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` §2 y §2.1 nueva.

**a) Aceptamos el cuerpo como `text/plain`, para que no haya preflight.** Un `POST` con
`Content-Type: application/json` obliga al navegador a preguntar antes (`OPTIONS`), y el preflight
es justo lo que peor llevan estos navegadores incrustados. Si mandáis el mismo JSON con
`Content-Type: text/plain`, la petición pasa a ser *simple*: **un solo viaje y sin `OPTIONS`**.

```js
fetch(`${API}/cuentas/register`, {
  method: 'POST',
  headers: { 'Content-Type': 'text/plain' },   // sin preflight
  body: JSON.stringify(datos),
})
```

El cuerpo y la respuesta son idénticos. `application/json` seguirá funcionando igual, no hay que
migrar nada de golpe.

**b) Una vía sin `fetch` para cuando nada funcione.** `POST /cuentas/register-form`, que acepta un
`<form>` normal (`application/x-www-form-urlencoded`) y responde con un `303` de vuelta a la web.
Un envío de formulario es una navegación: no pasa por `fetch`, no pasa por CORS y no lo intercepta
un service worker. Es el suelo que aguanta en cualquier navegador incrustado.

**c) Reintento sin susto.** Si el mismo correo y la misma contraseña vuelven a llegar dentro de unos
segundos, devolvemos el `201` con su token en vez del `409`. Así vuestro reintento deja de poder
decirle «ya hay una cuenta con ese correo» a quien la acaba de crear. Podéis dejar el reintento como
está.

**d) Registro de accesos en `/cuentas/`**, para no volver a tener que deducir si una petición llegó.
AzuraCast lo tenía desactivado y solo veíamos lo que entraba al backend; ahora hay dos registros y
entre los dos no queda hueco.

Comprobado en vivo, en este orden: `text/plain` se parsea (mandamos un correo válido con contraseña
corta y contestó lo de la contraseña, no lo del correo); `register-form` devuelve
`303 → https://astra.fm/cuenta?alta=error&motivo=datos`; un `volver` de fuera de astra.fm cae en la
portada y no redirige a ningún sitio ajeno; y el alta repetida con la misma contraseña devuelve
`201` con **el mismo id de cuenta**, mientras que con otra contraseña sigue devolviendo `409`. La
cuenta de prueba se ha borrado.

## 5. Lo que cae de vuestro lado, y por qué el nuestro no basta

Lo del punto 4 reduce el daño, pero no cura la causa. La causa es que **una parte de vuestro público
no está en un navegador**, está dentro del de otra app, y ese entorno os va a seguir dando problemas
en más sitios que el alta: almacenamiento y cookies recortados, service worker que a veces
simplemente no existe, `localStorage` que se borra al cerrar la app anfitriona, ventanas emergentes
bloqueadas y el reproductor con menos margen. El alta es donde ha dolido primero porque es lo único
que escribe en el servidor.

**1. Sacad el alta del navegador incrustado. Esto es el arreglo de verdad.**

Detectadlo y, en vez de intentar el envío ahí, ofreced abrir la página en el navegador del teléfono.
La detección es por `User-Agent` y es fea, pero es la que hay: en Android los webviews traen `; wv)`
o vienen de la app anfitriona, y en iOS el de Instagram y Facebook se identifican con
`Instagram` y `FBAN`/`FBAV`. En Android el salto se hace con un enlace explícito:

```html
<a href="intent://astra.fm/cuenta#Intent;scheme=https;package=com.android.chrome;end">
  Abrir en Chrome para crear la cuenta
</a>
```

En iOS no hay equivalente: ahí toca el botón de «abrir en Safari» del propio navegador incrustado,
así que lo único que podéis hacer es **explicarlo con una frase y un dibujo**. No es elegante, pero
es la diferencia entre un alta y un alta perdida.

**2. Poned el formulario del punto 4b como plan B, no como plan principal.** Si la persona no quiere
salir de la app, que el botón haga un envío de formulario normal. Pierde la experiencia de una sola
página (hay una recarga), pero entra. Nuestro consejo: intentad el `fetch`, y si falla sin respuesta,
en vez de reintentar el mismo `fetch`, **enviad el formulario**. Es un reintento que sí cambia algo.

**3. Mandad el error de verdad en `detalle`.** `err.name` y `err.message` (`TypeError`,
`NotAllowedError`, `AbortError`…). `sin_respuesta` nos dijo que no llegó, pero no por qué, y sin el
por qué hemos tardado tres idas y venidas en llegar aquí. Añadid también un
`window.addEventListener('securitypolicyviolation', …)`: si alguna vez es la CSP, lo dirá con nombre
y apellidos en vez de parecer un fallo de red.

**4. Revisad `connect-src` de vuestra CSP.** No creemos que sea esto (fallaría para todos, y tres
altas entraron bien), pero cuesta un minuto descartarlo: el audio y las imágenes pasan por
`media-src` e `img-src`, así que una CSP a la que le falte `listen.astra.fm` en `connect-src` dejaría
sonar la radio y mataría el alta sin dejar rastro. Que es exactamente el cuadro que tenemos delante.

**5. Y ya que el service worker sale una vez más:** comprobad que un navegador que instaló el viejo
se actualiza de verdad. `skipWaiting` hace que el nuevo tome el relevo, pero sin `clients.claim()`
las pestañas ya abiertas siguen con el anterior. Si el app shell se sirve de caché primero, alguien
puede estar corriendo vuestro JS de la semana pasada sin saberlo.

## 6. De paso, el reproductor: dos cosas que hemos visto midiendo

No lo habíamos mirado hasta hoy y salió de camino.

**El móvil se lleva la variante más pesada.** El selector de calidad pide
`aac_low_320.m3u8` cuando detecta wifi o 4g, y también cuando **no** consigue detectar la conexión
(`navigator.connection` no existe en Safari ni en varios webviews, y ahí la función devuelve
«buena» por defecto). El nombre engaña: de las cuatro variantes que servimos, esa es la de **320
kbps, la más alta**. Para un móvil es justo al revés de lo que interesa.

```
aac_lofi      52 kbps
aac_midfi    105 kbps
aac_hifi     211 kbps
aac_low_320  352 kbps   ← la que pide el móvil
```

Sugerencia: `aac_midfi` por defecto en móvil, y subir solo con `effectiveType === '4g'` confirmado.

**En Android se depende de que `play()` falle.** Ahí el código asigna el `.m3u8` directamente al
elemento de audio sin pasar por hls.js, y Android no sabe reproducir HLS de forma nativa: la música
solo suena porque el `catch` cae al MP3. Funciona, pero tarda y depende de que el rechazo llegue.
hls.js ya va en vuestro bundle y Android Chrome soporta MSE: o usarlo también ahí, o ir directo al
MP3 (`/listen/riot/radio.mp3`, que suena en todo).

**Y un aviso sobre vuestra propia métrica**, para que no persigáis un fantasma: `escucha_1min`
cancela su temporizador con el evento `waiting` y solo lo vuelve a armar con `playing`, así que un
rebuffer lo pierde. Sumado a que los temporizadores se estrangulan cuando el móvil se va a segundo
plano, **`escucha_1min` mide de menos en móvil**. El salto de 71 inicios a 37 minutos no es
necesariamente gente que se va.

## 7. La app

Los puntos 4a y 4c cambian el contrato que la app también usa (`/cuentas/register`), así que va en un
aviso aparte: `AVISO_CUENTAS_ALTA_APP.md`. A ella no le afecta nada de los webviews, que es cosa de
navegadores.

— el servidor
