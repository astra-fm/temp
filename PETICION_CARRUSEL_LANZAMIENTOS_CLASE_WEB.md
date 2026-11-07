# Petición a la web: el carrusel de nuevos lanzamientos, con álbum, EP o adelanto

**De:** servidor · **Para:** web · **7-nov-2026**

## Qué pide Pablo

Hoy el carrusel de «Nuevos lanzamientos» enseña todos los discos igual. Pablo quiere que se vea si cada uno es un
**álbum**, un **EP** o un **adelanto** (un single que adelanta el disco). Además, a partir de ahora él decide qué discos
entran en el carrusel: los marca como «Nuevo» en el Studio al publicarlos.

## Qué manda ya el servidor (en producción)

En `https://listen.astra.fm/discos/lanzamientos.json`:

- **`nuevos[]`**: los discos del carrusel, solo los marcados por Pablo y del más reciente al más antiguo. Cada uno lleva
  las mismas claves que `discos[]` y además `post` (`{slug, titulo}` del último post publicado, o `null`).
- **`clase`** en cada disco (`discos[]`, `nuevos[]`, `destacado` y la página `discos/<slug>.json`): `"album"`, `"ep"`
  o `"adelanto"`, o `null` si aún no se sabe. **Álbum y EP van separados.**
- **`nuevo`** (booleano) en cada disco: si está marcado para el carrusel.

En `actualidad.json`, los posts de `tipo: "lanzamientos"` creados desde el 7-nov llevan también `clase`, con los mismos
valores. Los de antes no la llevan: tratadlos como «Lanzamiento» a secas. La publicación de un adelanto es más corta
que la de un álbum o un EP.

```json
"nuevos": [
  { "slug": "the-datsuns--moonstruck-man", "titulo": "Moonstruck Man", "artista": "The Datsuns",
    "tipo": "single", "clase": "adelanto", "nuevo": true, "fecha": "2026-11-02", "saleEl": "2026-11-13",
    "portada": "https://…", "spotify": "https://…", "comprar": { "url": "https://…", "tienda": "Bandcamp" },
    "post": { "slug": "…", "titulo": "…" } }
]
```

## Qué pedimos a la web

1. **El carrusel** sale de **`nuevos[]`**. Si viene vacío, que no se enseñe o que muestre lo de antes, como prefiráis.
2. **Cada tarjeta** lleva su rótulo según `clase`: **«Álbum»**, **«EP»** o **«Adelanto»** (los textos que quiere Pablo).
   Con `clase: null`, sin rótulo.
3. **En los posts de Lanzamientos**, el mismo rótulo si traen `clase`.

`nuevos[]` acaba de estrenarse y Pablo aún no ha marcado ningún disco. Mientras tanto viene vacío; se llenará en
cuanto publique los primeros desde el Studio.

Contrato completo: `https://listen.astra.fm/docs` → `DISCOS_WEB_INSTRUCCIONES.md`, §3c.
Cuando esté, contestad con `PUBLICADO_CARRUSEL_LANZAMIENTOS_CLASE_WEB.md`.
