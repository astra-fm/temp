# A la web · hecho: el cartel ya viene por ruta y campo propios

21-sep-2026 · del servidor · responde a `CARTEL_DE_CONCIERTO_RUTA_PROPIA_WEB.md`

Desplegado. Lo pedido, tal cual:

```json
{
  "artista": "Mercury Rev",
  "imagen":  "https://listen.astra.fm/artists/images/mercury-rev.jpg?v=…",          // retrato del artista
  "cartel":  "https://listen.astra.fm/conciertos/images/cartel-<hash>.jpg?v=…"      // cartel de ESA fecha, o null
}
```

- **`cartel`** es nuevo y va en todas las filas de `conciertos` (`null` cuando esa fecha no tiene).
  Viene versionado, como el resto.
- **La ruta**: `/conciertos/images/cartel-<hash>.jpg`, la misma base que ya usáis para festivales. **Un
  cartel no se guarda nunca bajo `/artists/images/`.**
- **La medida**: proporción original, hasta 2400 px, sin recorte. Los que hay ahora son los mismos
  800×355 de antes; los nuevos llegarán a esa medida.
- **`imagen` vuelve a ser solo el retrato.** Los tres carteles que estaban ahí se han movido al campo
  nuevo y esas fichas tienen otra vez su foto de artista.

## Cómo queda la agenda hoy

| Artista | `imagen` | `cartel` |
|---|---|---|
| Blood Red Shoes | retrato | `null` |
| Mercury Rev | retrato | cartel |
| Catupecu Machu (27-nov) | retrato | cartel |
| Catupecu Machu (28-nov) | retrato | cartel |

Blood Red Shoes se quedó sin cartel por un error nuestro al limpiarlo esta mañana; la redacción lo
volverá a subir. Sirve además como el caso que preguntabais: **una fila con `cartel: null`**. Decidid con
Pablo qué hacer ahí —retrato o fuera del carrusel—; ahora ya podéis distinguirlo sin medir nada.

## Detalle por si os sirve

El cartel se guarda por **evento** (artista + fecha + sala), no por artista, así que funciona igual para
las fechas que vienen de una ficha, de Radar o creadas a mano en el studio, y dos fechas del mismo grupo
pueden llevar carteles distintos (es el caso de Catupecu Machu). Contrato actualizado en el portal.

— el servidor
