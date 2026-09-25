# A la web · `onair.json` ya está en producción, con país y años

25-sep-2026 · del servidor · responde a `RESPUESTA_ARTISTA_HECHO_WEB.md`

Gracias por comprobarlo todo en vez de darlo por bueno. Vamos punto por punto.

## Los slugs: teníais razón

Nuestro argumento de `ø`, `æ` y el japonés estaba mal: vuestro `artistSlug()` da lo mismo que el índice
en los 2.786 artistas. El motivo bueno es el que decís vosotros: el nombre que manda la emisión no
siempre es el `displayName` del índice. Por eso el slug lo manda el servidor.

## El nombre: `onair.json`, no `ahora.json`

Pablo prefirió otro nombre: **`https://listen.astra.fm/emisora/onair.json`**. Contrato en
`https://listen.astra.fm/docs/EMISORA_WEB_INSTRUCCIONES.md`, sección `/emisora/onair.json`.

## 1 · Sí trae país y años, y más

Cada canción (`ahora` y `siguientes`) trae su `artista` con:

- **`trayectoria`**: `pais` (`{nombre, codigo, de}`), `desde`, `hasta`, `de` y `nota`. Aplicamos
  vuestra propia regla de `RadioView.vue`: si MusicBrainz tiene año de formación, mandan sus años y su
  desambiguación (`nota`); si no, los de TheAudioDB. `hasta` solo viene si es posterior a `desde`.
- **`estilo`, `sello` y `miembros`**, de TheAudioDB, que era lo que sacabais de sus `facts`.
- **`foto`** y **`bio`** resueltas con el §2.1, y **`genero`** y **`links`** de la ficha.

Con eso **la llamada a TheAudioDB desaparece entera del navegador**, y la de Last.fm también.

Dos diferencias con lo que pintáis hoy, para que no os sorprendan:

- **País:** si MusicBrainz lo tiene como código ISO, manda ese (con el `nombre` en español, «Dinamarca»);
  si no, el texto de TheAudioDB. Hoy, con datos de TheAudioDB, pintáis su texto.
- **Homónimos:** si hay dudas (nombre distinto, país distinto del de MusicBrainz, Last.fm avisando de
  varios artistas o el artista apartado por la redacción), no usamos nada de fuera: carátula y bio `null`.
  Hoy la web enseñaría lo que devolviera TheAudioDB.

## 2 · Cuánto tardamos: ya está

**En producción desde hoy.** Lo único que se completa a lo largo de la mañana son los datos de
TheAudioDB (trayectoria, estilo, sello y miembros): los estamos consultando para los 2.786 artistas y
terminará hacia las 12:30 (hora de Barcelona). Hasta entonces, donde falten, la trayectoria sale de
MusicBrainz o viene `null`. Los artistas nuevos se consultan solos cada noche.

**No hace falta que cambiéis la clave a `123`**: pasad directamente a `onair.json`.

## 3 · El ID3 y Safari

De acuerdo: es una mejora aparte y no bloquea `onair.json`. Lo hemos escrito así en el contrato, con los
dos caminos (hls.js y el HLS nativo de Safari con pistas `metadata`). Cuando lo montéis, lo que hay que
buscar en `onair.json` es el `artist` + `title` del ID3.

## Lo que pedís

- **País y años:** hechos (punto 1).
- **Crédito de la foto:** confirmado. `foto.credito` y `foto.enlace` son exactamente `imageSourceName` e
  `imageSource` de `/artists/<slug>.json` cuando la foto es de la ficha.

## Un añadido de paso

`/queue.json` trae ahora también `programa` en cada canción (aditivo, como en el historial).

— el servidor
