# Saber qué canciones de la cola son de un podcast programado

01-11-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 01.11.26

## Para qué

Pablo ha decidido (con diseño, 01.11.26) que en **Radio · Lo que sigue** las canciones de un **podcast
programado** se plieguen en una sola fila con un chip PODCAST: el artista, «N canciones», VER LAS N, y al
abrirla, cada canción con su hora. Si el podcast está sonando: AHORA, EN AIRE y «1 de N · <canción>».

**Solo las de un podcast programado.** Varias canciones seguidas de un mismo artista fuera de un podcast
siguen sueltas, así que la web no puede deducirlo por el artista.

Hoy no hay ningún dato público que lo diga: `queue.json`, `onair.json` e `historial.json` no marcan el
podcast (todas llegan con `playlist: rotation_engine_*`), y `queue.json` trae como mucho 4 canciones, así
que tampoco se puede saber cuántas tiene.

## Qué pedimos (aditivo)

1. **En cada canción** de `queue.json`, y de `ahora` y `siguientes` en `/emisora/onair.json`, un campo
   **`podcast`**: el `id` del podcast al que pertenece, o `null`.

2. **En `/emisora/onair.json`, un bloque `podcast`** mientras haya uno en marcha o a la vista (desde que su
   primera canción entra en `siguientes` hasta que acaba la última), y `null` el resto del tiempo:

```json
"podcast": {
  "id": "…",
  "nombre": "…",
  "artista": "The Cure",
  "canciones": [
    { "artist": "The Cure", "title": "…", "album": "…", "year": "1985",
      "art": "https://listen.astra.fm/api/station/riot/art/….jpg",
      "played_at": 1793520000, "duration": 241.3 }
  ]
}
```

- `canciones`: **todas** las del podcast, en el orden en que suenan, con `played_at` previsto (el mismo
  epoch que la cola) y `duration`. Sin las secciones habladas: solo las canciones, como en la cola.
- La web pinta con esto «N canciones», las horas de cada una y cuál está sonando (cruzándolo con `ahora`).

Si os encaja mejor otra forma (por ejemplo, el bloque en `queue.json`), adelante: decidnos cuál y la web
se adapta. Lo único imprescindible es **qué canciones son del podcast** y **cuántas son en total**.

Mientras tanto la web prepara la fila y la prueba en local simulando el dato; en producción no cambia nada
hasta que llegue.

— el front de la web
