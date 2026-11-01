# A la web · canciones de un podcast en la cola: hecho, ya en producción

01-11-2026 · del servidor · contesta a `PETICION_PODCAST_EN_LA_COLA.md`

Hecho tal como lo pedíais, aditivo y ya en producción. El contrato está en el portal (`/docs`,
`EMISORA_WEB_INSTRUCCIONES.md` §2, «podcast»).

## Qué hay

1. **`podcast` en cada canción** de `/queue.json`, y de `ahora` y `siguientes` en `/emisora/onair.json`: el `id` del
   podcast programado al que pertenece, o `null`. Solo las de un podcast: varias canciones seguidas de un artista fuera
   de un podcast llevan `null`.

2. **Bloque `podcast` en `/emisora/onair.json`**, con la forma que proponíais. Aparece cuando la primera canción del
   podcast entra en `siguientes` y se va cuando acaba la última; el resto del tiempo es `null`.

```json
"podcast": {
  "id": "mvqw5yoy3zywz", "nombre": "Dry Cleaning", "artista": "Dry Cleaning",
  "canciones": [
    { "artist": "Dry Cleaning", "title": "Strong Feelings", "album": "New Long Leg", "year": "2021",
      "art": "https://listen.astra.fm/api/station/3/art/….jpg", "played_at": 1793390152, "duration": 239 }
  ] }
```

`canciones` son todas las del podcast, en el orden en que suenan y sin las partes habladas.

## Lo que hay que saber de las horas

`played_at` de cada canción del bloque es una **previsión**. AzuraCast solo da la hora de las pocas canciones que
tiene en cola; el resto la calcula el servidor desde una de ellas, sumando lo que va entre medias en la emisión, voces
incluidas. Los solapes de voz y canción y los fundidos la mueven unos segundos. Lo hemos medido con el podcast de
Dry Cleaning del 30-oct en Revolución Urbana: menos de 15 s de diferencia en las tres canciones. Si alguna vez no hay
con qué calcularla, viene `played_at: null`.

Para «AHORA, EN AIRE» y «1 de N», cruzad con `ahora`: la canción de `ahora` con el mismo `podcast` es la que suena, y
su posición en `canciones` da el número.

## Para probarlo

Hoy no hay ningún podcast programado: todos los que hay ya se han emitido, así que de momento el campo sale siempre
`null` y el bloque también. En cuanto Pablo programe el siguiente, lo veréis en producción desde que su primera
canción entre en la cola. Mientras tanto, el ejemplo de arriba es real: es el bloque que habría salido el 30-oct.

— el servidor
