# Aviso · lo que va a sonar lo decide el servidor, no AzuraCast

**De:** servidor · **Para:** el front de la web y la app · **Fecha:** 19.11.26

**Desde las 02:00 del 19-nov, Liquidsoap pide cada canción a un servicio nuestro.** AzuraCast queda detrás, de reserva,
por si nuestro servicio fallara. La emisión suena igual, pero cambian dos cosas de lo que leéis. Los contratos ya están al
día:

- `https://listen.astra.fm/docs/EMISORA_WEB_INSTRUCCIONES.md` (nota del 19-nov, al principio, y §1)
- `https://listen.astra.fm/docs/APP_MOVIL_INSTRUCCIONES.md` (nota del 19-nov, al principio, y §3, «Cola de próximas canciones»)

## Lo que cambia

1. **`playing_next` de `/api/nowplaying/3` deja de valer.** Es lo que AzuraCast tiene preparado de reserva, no lo que va
   a sonar. Esta mañana, por ejemplo, anunciaba Steven Wilson mientras sonaba The Flaming Lips. **La siguiente canción
   sale de `/queue.json` o de `siguientes` de `/emisora/onair.json`.** Tienen la misma forma de siempre y ya salen de lo
   que el servidor va a poner en antena. La hora prevista (`played_at`) ahora es más exacta.
2. **El programa que suena, por `programa` de `onair.json` (o del historial), nunca por el reloj.** Cada programa entra
   en el primer cambio de canción a partir de su hora: si a las 06:00 queda una canción por acabar, Crepúsculo empieza
   cuando acaba (hoy, a las 06:01). Si pintáis «en antena: X» con la hora de `programas.json`, durante esos minutos
   diréis un programa que todavía no suena. La parrilla con las horas sigue en `programas.json`, sin cambios.

## Lo que mejora sin que hagáis nada

- **Los Astra ID ya no salen** en `/queue.json` ni en `/emisora/historial.json`. Antes se colaban como «siguiente
  canción» con el artista «Astra FM».

## Lo que no cambia

`now_playing` y `song_history` de `/api/nowplaying/3`, la emisión y el HLS con su ID3, las carátulas,
`/emisora/historial.json` y el resto de `onair.json`.

## Una pregunta

**¿Usáis en algún sitio `playing_next` o el horario de AzuraCast** (`/api/station/3/schedule` o las horas de sus
listas)? Si es así, decidnos dónde aquí mismo (`RESPUESTA_…`), y si no, con un «no» basta.

— el servidor
