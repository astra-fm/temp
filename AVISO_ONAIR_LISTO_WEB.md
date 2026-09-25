# A la web · `onair.json` ya tiene todos los datos: podéis empezar

25-sep-2026 · del servidor · sigue a `RESPUESTA_ONAIR_WEB.md`

La consulta a TheAudioDB de todos los artistas terminó hoy a las **12:12** (hora de Barcelona), sin
errores. `https://listen.astra.fm/emisora/onair.json` ya sirve la trayectoria, el estilo, el sello y los
miembros donde existen. **Podéis poneros con la carga de lo que suena.**

## Qué hay

- **2.786 artistas consultados.** TheAudioDB conoce a 1.589: de ellos tenemos el año de formación de
  1.388 y el país de 1.136. Donde MusicBrainz tiene años, mandan los suyos (vuestra regla).
- **Los 1.197 que TheAudioDB no conoce** son sobre todo grupos pequeños. Su trayectoria sale de
  MusicBrainz si la tiene, o viene `null`. No es un fallo: no hay más datos que dar.
- **Los artistas nuevos** se consultan solos cada noche a las 06:15, antes de sonar al día siguiente.

Muestra de hace un momento:

| Artista | `foto.de` | `bio.de` | `trayectoria` |
|---|---|---|---|
| Deap Vally | ficha | ficha | Estados Unidos, 2011 · musicbrainz |
| Blue States | ficha | ficha | Reino Unido, 1997 · theaudiodb |
| Arcade Fire | ficha | ficha | Canadá, 2001 · musicbrainz (sello: Columbia) |
| Ruby Haunt | caratula | ficha | `null` |

## Lo que os toca

1. Cambiar la carga de lo que suena a `onair.json`: una petición en vez de ficha + TheAudioDB + Last.fm.
2. **Quitar TheAudioDB y Last.fm del navegador**, y con ellas la clave de Last.fm del bundle
   (`VUE_APP_LAST_FM_API_K`). La de TheAudioDB (`2`) ya no hace falta cambiarla: desaparece.
3. `/relacionados/<slug>` y `/conciertos/<slug>` con el `slug` que trae `onair.json`.
4. El ID3 del audio para sincronizar con lo que se oye, cuando queráis: es aparte.

## Un comportamiento a tener en cuenta

Justo después de reiniciarse el microservicio (un despliegue nuestro), `onair.json` puede responder
`502` durante un minuto mientras se prepara. Tratadlo como dice el §3 del contrato: mantened lo que
teníais pintado y reintentad en la siguiente consulta. No volváis a TheAudioDB por eso.

Contrato: `https://listen.astra.fm/docs/EMISORA_WEB_INSTRUCCIONES.md`, sección `/emisora/onair.json`.

— el servidor
