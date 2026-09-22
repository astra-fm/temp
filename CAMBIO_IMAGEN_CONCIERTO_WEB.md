# A la web · `cartel` es el nombre, en conciertos y en festivales

22-sep-2026 · del servidor · **sustituye a `CAMBIO_IMAGEN_CONCIERTO_WEB.md`, que os mandamos hace un rato
y estaba equivocado** (decía que el campo pasaba a llamarse `imagen`: olvidadlo).

Pablo lo ha dejado así: **«imagen» es demasiado general, y «cartel» identifica claramente un cartel de
concierto.** Un solo nombre para la misma cosa en las dos listas.

## Conciertos

- **`cartel`**: el cartel de esa fecha, bajo `/conciertos/images/`, o `null`. Es lo que ya teníais
  implementado desde ayer: **no tenéis que cambiar nada**.
- **`imagen` deja de enviarse.** La foto del artista no viaja en esta lista. Si un concierto no tiene
  cartel, llega `null` y decidís qué pintar, pero no lo saquéis de la ficha del artista: Conciertos y
  Artistas son apartados distintos y no se relacionan.

## Festivales

Aquí sí cambian dos nombres, y esto sí os toca:

| Campo | Antes | Ahora |
|---|---|---|
| `cartel` | texto con los artistas destacados | **la imagen del cartel** (como en conciertos) |
| `artistas` | — | **el texto con los artistas destacados** (antes era `cartel`) |
| `imagen` | la imagen del cartel | ya no se envía |

**Ahora mismo no hay ningún festival publicado**, así que podéis adaptarlo con calma: la lista viene
vacía y no se rompe nada por el camino.

## Lo que no cambia

Proporción original, sin recorte y sin reescalar; la extensión varía (`jpg`, `png`, `webp`) y hay que
usar la URL tal cual; versionada con `?v=`.

Contrato actualizado en el portal, con los ejemplos de JSON al día.

— el servidor
