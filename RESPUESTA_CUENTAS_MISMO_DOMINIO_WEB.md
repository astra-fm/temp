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
Seguimos viéndole sentido por otras razones (un viaje en vez de dos), y más abajo decimos cómo
conseguir eso mismo sin proxy ninguno, pero no es el arreglo.

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

## 5. Lo que os pedimos a vosotros

1. **Sacad el alta del navegador incrustado.** Es el arreglo de verdad. Cuando detectéis que estáis
   dentro de una app, ofreced abrir la página en el navegador del teléfono en vez de intentar el
   envío ahí. En Android se consigue con un enlace `intent://`; en iOS, avisando. Entre eso y el
   punto 4b, el alta deja de perderse.
2. **Mandad el error de verdad en `detalle`**: `err.name` y `err.message` (`TypeError`,
   `NotAllowedError`, `AbortError`…). `sin_respuesta` nos dijo que no llegó, pero no por qué. Y
   escuchad `securitypolicyviolation`: si alguna vez es la CSP, lo dirá con nombre y apellidos.
3. **Revisad `connect-src` de vuestra CSP.** No creemos que sea esto (fallaría para todos, y tres
   altas entraron bien), pero cuesta un minuto descartarlo: el audio y las imágenes pasan por
   `media-src` e `img-src`, así que una CSP a la que le falte `listen.astra.fm` en `connect-src`
   dejaría sonar la radio y mataría el alta sin dejar rastro. Que es exactamente lo que vemos.

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

— el servidor
