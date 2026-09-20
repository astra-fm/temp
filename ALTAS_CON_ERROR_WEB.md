# A la web · 36 altas fallidas y no sabemos por qué

20-sep-2026 · del servidor · **pedimos que lo miréis de vuestro lado**

Contrato de referencia: `https://listen.astra.fm/docs/ANALITICA_WEB_INSTRUCCIONES.md` («El alta, paso a
paso» y la tabla de eventos) y `CUENTAS_WEB_INSTRUCCIONES.md` §2.

## Lo que vemos

Últimos 30 días en Stats, todo del alta:

| Evento | Total |
|---|---|
| `alta_abierta` | 9 |
| `alta_iniciada` | 7 (las 7 con `tipo: artista`) |
| `alta_completada` | 1 |
| `alta_error` | **36**, todos con `motivo: otro` |

Los 36 errores salen de **cuatro sesiones distintas** y siguen ocurriendo. Horas de Madrid:

- 19-sep: 15:58-16:11 (17 errores, una sesión) y 21:40-21:41 (5 errores, otra sesión).
- 19-sep 17:22: **un alta sí se completó** (artista real, cuenta creada y correo verificado un minuto
  después). Es decir, no es una caída continua.
- 20-sep: 09:26-09:34 (9 errores, una sesión que además probó `tipo: oyente` y `tipo: artista`) y 10:06
  (1 error, otra sesión).

## Por qué os lo pasamos

Según el contrato, `motivo: otro` es justo lo que **no** es 400, 409 ni 429: «cualquier otra respuesta o
fallo de red». Con los 36 en `otro` no podemos saber si la gente se topa con «ya hay una cuenta con ese
correo», con el límite de intentos, con datos mal escritos o con un fallo de verdad.

Lo que hemos descartado en el servidor, medido hoy:

- El microservicio lleva desde el 19-sep a las 12:46 sin reiniciarse ni caerse, y no hay 5xx en sus
  registros.
- CORS correcto: `OPTIONS` y `POST` a `https://listen.astra.fm/cuentas/register` con `Origin:
  https://astra.fm` y con `https://www.astra.fm` responden con sus cabeceras.
- `POST /cuentas/register` funciona: dos altas reales de prueba hoy, ambas `201`, con su correo de
  verificación, su verificación y el área abriéndose como toca.
- Los errores previstos devuelven lo pactado: `400 {"ok":false,"error":"Escribe un correo válido"}`,
  `409 {"ok":false,"error":"Ya hay una cuenta con ese correo — entra o recupera la contraseña"}`.

Por eso sospechamos que el fallo, o al menos la etiqueta, está de vuestro lado. Dos hipótesis, sin
descartar otras:

1. **El motivo no se está mapeando** y todo error cae en `otro`. Si es eso, lo más probable es que
   detrás haya muchos `409`: tenemos **25 cuentas migradas de la app antigua** que nunca han entrado, y
   su gente puede estar intentando registrarse otra vez con el correo que ya tiene cuenta.
2. **La llamada falla antes de llegar** (error de red, URL equivocada, respuesta no JSON, un
   `catch` que envuelve varios pasos del alta). Ahí el servidor no ve nada.

## Qué os pedimos

1. **Mandad el `motivo` real**, como dice la tabla: `correo_registrado` (409), `limite` (429), `datos`
   (400), y dejad `otro` solo para lo que de verdad no encaje. Con eso, en un día sabemos qué pasa.
2. Cuando el motivo sea un fallo inesperado, apuntad en vuestro lado el **código HTTP y el cuerpo** que
   recibisteis (no hace falta que viaje a la analítica: nos vale con que podáis mirarlo).
3. **Si es `409`, no es un error para el usuario**: ofrecedle entrar o restablecer la contraseña con ese
   mismo correo, que es lo que necesita quien viene de la app antigua (recordad que el login devuelve
   `debeRestablecer: true` para esas cuentas).
4. Confirmadnos a qué URL exacta hacéis el `POST` del alta y si veis el código de estado de la respuesta.

Con las horas de arriba podéis mirar vuestros propios registros: son sesiones concretas y recientes.

Gracias.

— el servidor
