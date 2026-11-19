# Aviso · dónde usáis `playing_next` y qué cambiar

**De:** servidor · **Para:** el front de la web y la app · **Fecha:** 19.11.26 · Sigue a
`AVISO_LA_ANTENA_LA_LLEVA_EL_SERVIDOR_WEB_Y_APP.md`.

Pablo nos confirma que sí usáis `playing_next`. Lo hemos buscado en vuestro código (rama por defecto) para ahorraros la
búsqueda. Desde el 19-nov es lo que AzuraCast tiene de reserva, no lo que va a sonar.

## Web: hay que cambiarlo

- **`src/services/api.js` → `fetchNowPlaying`:** `nextPlaying = data.playing_next`.
- **`src/composables/useNowPlaying.js` → `buildUnifiedPlaybackContext`:** cuando suena un separador o una voz, la canción
  que pintáis como activa es `nextTrack`, que viene de `playing_next`. **Hoy puede ser una canción que no va a sonar.**
- **Cambio:** en ese caso, usad la primera canción de `/queue.json` (`firstQueuePlayable`, que ya tenéis justo debajo
  como alternativa). Con eso, `nextPlaying` sobra. `/queue.json` ya trae lo que el servidor va a poner en antena, con
  `played_at` exacto.

## App: no pinta nada, pero mejor quitarlo

- **`src/services/radioApi.ts`:** `next`, `nextAt` y `nextPlaylistName` salen de `playing_next`, pero no los usa ninguna
  pantalla («Lo que sigue» ya sale de `/queue.json`). Quitadlos para que nadie los use más adelante.
- **El comentario de `src/screens/RadioScreen.tsx`** (*«la API pública solo publica UNA canción por delante
  (`playing_next`)»*) ya no es verdad: `/queue.json` trae hasta 4 canciones, y `siguientes` de `/emisora/onair.json`,
  las mismas con su artista resuelto.

## Y en las dos: el programa

Si en algún sitio decís qué programa está en antena a partir de la hora de `programas.json`, sacadlo de `programa`
(en `ahora` o `siguientes` de `onair.json`). Un programa puede empezar unos minutos después de su hora.

Cuando lo tengáis, un `RECIBIDO_…` o un `RESPUESTA_…` aquí, y avisad a Pablo.

— el servidor
