# A la web · `imagen` en conciertos pasa a ser la imagen del concierto

22-sep-2026 · del servidor · **cambio de contrato, ya desplegado** · continúa `RESPUESTA_CARTEL_RUTA_PROPIA_WEB.md`

Decisión de Pablo, con sus palabras: **«Conciertos es el nombre que lleva el apartado y la imagen es de un
concierto, no de un artista»**. Así que en `/conciertos.json`:

| Campo | Antes | Ahora |
|---|---|---|
| `imagen` | la foto del artista | **la imagen del concierto** (la que diseña la redacción), o `null` |
| `cartel` | la imagen del concierto | **el mismo valor que `imagen`**, como alias temporal |

**La foto del artista ya no viaja en esta lista.** Ni como respaldo cuando no hay imagen del concierto:
ahí llega `null` y decidís qué pintar —hueco, tarjeta tipográfica, lo que mejor os venga—, pero **no la
saquéis de la ficha del artista**. Conciertos y Artistas son apartados distintos y no se relacionan: una
imagen de concierto caduca con su fecha y no tiene por qué parecerse al artista que suena en la radio.

## Qué tenéis que hacer

**Nada urgente.** `cartel` sigue llegando con el mismo valor, así que lo que tenéis desplegado funciona
igual. Cuando os venga bien, leed `imagen` y avisadnos: el día que nos digáis que ya no leéis `cartel`,
lo retiramos.

## Cómo queda hoy

Las 8 fechas vigentes tienen su imagen de concierto, todas bajo `/conciertos/images/`. Ninguna fila lleva
ya una foto de artista.

**Lo demás no cambia:** proporción original, sin recorte y sin reescalar, extensión variable (`jpg`,
`png`, `webp`) —usad la URL tal cual—, versionada, y los festivales siguen con su `imagen` de siempre.

Contrato actualizado en el portal.

— el servidor
