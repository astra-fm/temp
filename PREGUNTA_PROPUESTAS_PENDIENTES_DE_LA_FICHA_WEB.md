# Pregunta · ¿Pueden convivir varias propuestas pendientes en la ficha?

**De:** el front de la web · **Para:** servidor · **Fecha:** 10.11.26

Sobre `PETICION_CANCION_EN_EL_AREA_DE_ARTISTA_WEB.md`.

## Qué pasa

Con la petición, Mi ficha pública puede generar tres propuestas por caminos distintos:

| Camino | Campos de la propuesta |
|---|---|
| `POST /cuentas/ficha/proponer` | los que se hayan tocado: `bio`, `ciudad`, `pais`, `foto`, `enlaces`, `conciertos` |
| `POST /cuentas/ficha/audio` | `["audio"]` |
| `POST /cuentas/ficha/foto` | `["foto"]` |

Hoy la pantalla da por hecho que hay **una sola** propuesta pendiente: se queda con la primera de
`cuenta.ficha.propuestas` con `estado:"pendiente"`. Si hay más, las otras no se marcarían como En revisión.

La petición solo dice que una canción nueva sustituye a la canción que estuviera en revisión.

## Qué necesitamos saber

1. Si un artista tiene una propuesta de texto pendiente y sube una canción, **¿quedan las dos pendientes en
   `propuestas`, o una sustituye a la otra?** Y lo mismo con `ficha/foto` y con un nuevo `ficha/proponer`.
2. **¿Una foto subida sustituye a la foto en revisión**, tanto si esa llegó por archivo como si llegó por enlace dentro
   de un lote de `ficha/proponer`? Si el lote llevaba más campos, ¿qué pasa con ellos?
3. ¿La lista de las diez últimas propuestas sigue siendo de diez con esto? Si conviven, una pendiente antigua podría
   quedarse fuera.

— el front de la web
