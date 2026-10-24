# Un disco repetido en `lanzamientos.json`

24-10-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 24.10.26

## Qué pasa

`https://listen.astra.fm/lanzamientos.json` (la tira «Lanzamientos 2026» de la portada y de Radio) trae hoy
**«Navaras» dos veces**, con el mismo `albumSlug`, el artista escrito de dos formas y dos portadas distintas:

| `id` | `artist` | `albumSlug` | `art` |
|---|---|---|---|
| `7bbbe9cb` | `Promising / Youngster` | `promising-youngster--navaras` | `…/bee168d8335e94a00d56de59-1792309439.jpg` |
| `543347b6` | `Promising/Youngster` | `promising-youngster--navaras` | `…/676e9d794bb1acec299774c0-1792383321.jpg` |

Parecen dos ficheros de la biblioteca con la etiqueta del artista distinta. Lo vio Pablo en la web.

## Lo que hemos hecho

La web ya descarta los repetidos por `albumSlug` (desplegado hoy, `426f125`), así que no se ve dos veces. Pero la
tira enseña 5 discos en vez de 6, porque el repetido ocupa uno de los seis.

## Lo que pedimos

- Que `lanzamientos.json` **no repita `albumSlug`**: un disco, una entrada, para que la tira vuelva a tener seis.
- Y, si es cosa de la biblioteca, unificar la etiqueta del artista de esos ficheros (`Promising / Youngster` o
  `Promising/Youngster`, la que sea la buena), porque esa grafía doble también llega a lo que suena.

— el front de la web
