# Las respuestas de error de listen.astra.fm, sin cabecera CORS: se ven como «bloqueado por CORS»

08-10-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 08.10.26

## Qué vio Pablo

Al cargar Radio, la consola enseñó tres errores de CORS a la vez (hacia las 10:49, hora de Madrid):

```
Access to fetch at 'https://listen.astra.fm/avisos.json?fuente=actualidad' from origin 'https://astra.fm'
has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present…
Access to fetch at 'https://listen.astra.fm/queue.json' from origin 'https://astra.fm' … (dos veces)
```

Y la ficha de lo que suena tardó cerca de un minuto en llenarse.

## Lo que hemos medido

- **El CORS está bien.** `avisos.json`, `queue.json` y `onair.json` responden ahora con
  `access-control-allow-origin: https://astra.fm` (y con `https://www.astra.fm` si se pide desde ahí).
  Tres cargas seguidas de `astra.fm/radio` en un navegador limpio: 23 peticiones, ningún error.
- **Las respuestas de error no llevan la cabecera.** Pidiendo un fichero que no existe
  (`/no-existe.json` con `Origin: https://astra.fm`) llega un 404 **sin**
  `access-control-allow-origin`. Así que cuando el servicio de detrás falla un momento —un 502 de nginx
  mientras se reinicia, por ejemplo—, el navegador no enseña «502»: enseña «bloqueado por CORS», y no
  hay manera de saber desde fuera qué pasó ni cuándo.

Creemos que lo de las 10:49 fue eso: un fallo momentáneo, que Chrome disfrazó de error de CORS.

## La petición

Que nginx añada las cabeceras CORS **también en las respuestas de error** de `listen.astra.fm` —con
`add_header … always`—, o que las respuestas de error de la aplicación las lleven. Así un fallo se lee
como lo que es (502, 504…) y se puede cruzar con vuestros registros.

Y si tenéis registro de reinicios o 5xx de hoy hacia las 08:49 UTC en `queue.json` y `avisos.json`,
nos ayudaría saberlo para confirmar la causa.

## Lo que ya hemos hecho en la web

Medido: un corte de solo 5 s en `/api/nowplaying` al abrir Radio dejaba la ficha vacía 31 s, porque
el siguiente intento era el refresco de 30 s. Desde hoy, en `develop`: `onair.json` pinta la ficha si
llega antes que AzuraCast, y un fallo se reintenta a los 2, 4 y 8 s. Con `nowplaying` caído del todo,
la ficha sale en medio segundo con vuestro `onair.json`.

— el front de la web
