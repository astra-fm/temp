# Contrato para la web: lanzamientos (Radio → Lanzamientos, carrusel y posts)

**De:** servidor · **Para:** web · **7-nov-2026** · **En producción.**

Sustituye a `PETICION_CARRUSEL_LANZAMIENTOS_CLASE_WEB.md` y a `PETICION_RADIO_LANZAMIENTOS_PERIODOS_WEB.md`, ambas de hoy:
lo que vale es este documento. El contrato completo y permanente está en `https://listen.astra.fm/docs` →
`DISCOS_WEB_INSTRUCCIONES.md`, §3c y §3d.

## 0. La idea en tres frases

1. **En la web solo sale lo que Pablo publica**: un disco aparece cuando tiene un post de Lanzamientos publicado, esté o no
   en la radio. Los que no están en la radio se escuchan con Spotify.
2. **El servidor ya calcula toda la lógica** (periodos, recuentos, qué va en cada mes, Próximamente, destacado, periodo por
   defecto). La web **solo pinta**: no rehagáis ninguna regla.
3. **Hay dos fechas y no se mezclan**: `salida`/`anio` son las del disco, y son las que se enseñan; `publicado` es la del
   post, y solo ordena la pestaña. Nunca se enseña `publicado` como fecha del disco.

## 1. Dónde está

`GET https://listen.astra.fm/discos/lanzamientos.json` (público, sin clave). Se regenera cada noche y al momento cuando
Pablo publica o cambia algo en el Studio. Basta con cargarlo al abrir la pestaña.

| Bloque | Para qué |
|---|---|
| `radio` | **La pestaña Radio → Lanzamientos** (§2) |
| `nuevos[]` | **El carrusel de nuevos lanzamientos** de la portada (§3) |
| `discos[]`, `destacado` | La rejilla de antes. Se mantienen mientras la web no cambie a `radio` |

La página de cada disco está en `https://listen.astra.fm/discos/<slug>.json` (§4).

## 2. La pestaña: `radio`

```json
"radio": {
  "hoy": "2026-11-07",
  "periodos": [
    { "clave": "proximamente", "nombre": "Próximamente", "n": 0 },
    { "clave": "2026-11", "nombre": "Noviembre", "anio": 2026, "n": 0 },
    { "clave": "2026-10", "nombre": "Octubre", "anio": 2026, "n": 5 },
    "… hasta seis meses …"
  ],
  "porDefecto": "2026-10",
  "destacado": null,
  "proximamente": [ "…adelantos (§2.2)…" ],
  "meses": { "2026-10": [ "…tarjetas (§2.1)…" ], "2026-09": [ "…" ] }
}
```

**Cómo pintarla:**

- **Siete periodos**, en el orden en que vienen: Próximamente y los seis meses de calendario contando el actual. Cada uno
  con su recuento `n`. **Con `n: 0`, el periodo va apagado y no se abre.** No hay nada anterior: lo antiguo vive en la
  ficha del artista y en la página del disco.
- **Al entrar**, abrid el periodo de `porDefecto`: el mes en curso o, si tiene 0, el más reciente con lanzamientos. Puede
  ser `null` si no hay ninguno.
- **El contenido de un mes** es `meses[clave]` y el de Próximamente es `proximamente`. Ya vienen ordenados.
- **El destacado** (`destacado`, o `null`) va arriba. Ya está quitado de los periodos: no lo repitáis en la rejilla.

### 2.1 Tarjeta de un disco (lo que hay en `meses` y en `destacado`)

Ejemplo real:

```json
{
  "slug": "sebadoh--beauty-of-the-ride",
  "destino": "sebadoh--beauty-of-the-ride",
  "titulo": "Beauty of the Ride",
  "artista": "Sebadoh",
  "artistSlug": "sebadoh",
  "tipo": "single",
  "clase": "single",
  "salida": "2026-09-24",
  "anio": 2026,
  "publicado": "2026-10-26",
  "entrada": "2026-11-01",
  "enRadio": true,
  "portada": "https://listen.astra.fm/api/station/3/art/…jpg",
  "generos": ["Lo-Fi"],
  "spotify": "https://open.spotify.com/album/7AKey4tzcKfELCEtOb2DDp",
  "comprar": null,
  "post": { "slug": "sebadoh-recupera-beauty-of-the-ride-por-los-30-anos-de-harmacy", "titulo": "…" }
}
```

| Campo | Qué es | Cómo se usa |
|---|---|---|
| `clase` | `album`, `ep`, `single` o `adelanto` | Rótulo: **«Álbum»**, **«EP»**, **«Single»**, **«Adelanto»**. Con `null`, sin rótulo |
| `salida`, `anio` | Fecha de salida **del disco** (`AAAA-MM-DD` o `AAAA-MM`) y su año | **La fecha que se enseña** |
| `publicado` | Fecha del post | Solo ordena. **No la enseñéis como fecha del disco** |
| `entrada` | Cuándo entró en la radio, o `null` si no está | Opcional |
| `enRadio` | Si suena en Astra | Opcional, por ejemplo para una marca |
| `destino` | Slug de la página a la que lleva la tarjeta | **Toda tarjeta lleva a `discos/<destino>.json`** |
| `generos` | Géneros (puede ser `[]`) | — |
| `spotify`, `comprar` | Enlace de Spotify y de compra (`{url, tienda}`), o `null` | Para la página (§4) |
| `post` | El post publicado (`{slug, titulo}`) | «Leer» en Actualidad |

### 2.2 Tarjeta de un adelanto (lo que hay en `proximamente` y en `destacado` si es un adelanto)

Lleva lo mismo que una tarjeta de disco y además:

```json
"clase": "adelanto",
"cancion": "Moonstruck Man",
"anuncia": { "titulo": "Moonstruck Man", "tipo": "album", "fecha": "2026-11-13", "slug": "the-datsuns--moonstruck-man" }
```

- `cancion` es el título de la canción adelanto. `anuncia` es el disco que anuncia: nombre, tipo (`album` o `ep`) y su
  fecha de salida.
- Su `destino` es **la página del disco que anuncia** (`anuncia.slug`). Si ese disco aún no tiene página, el servidor se
  la crea, con `adelantos[]`.
- En `proximamente` van ordenados por `anuncia.fecha`, del más cercano al más lejano. Desde esa fecha, el adelanto
  desaparece y el disco entra en su mes.

## 3. El carrusel de nuevos lanzamientos: `nuevos[]`

- Son los discos que Pablo marca «Nuevo en la web» al publicarlos, del más reciente al más antiguo.
- Cada uno trae las claves de las tarjetas de siempre (`slug`, `titulo`, `artista`, `tipo`, `portada`, `spotify`,
  `comprar`…) y además **`clase`** y **`post`**.
- Rótulo según `clase`, igual que en §2.1.
- Si `nuevos[]` viene vacío, no enseñéis el carrusel, o caed a lo de antes, a vuestro criterio. Hoy viene vacío: Pablo
  aún no ha marcado ninguno.

## 4. La página del disco (`discos/<slug>.json`) y los posts

- **Escuchar: solo con Spotify.** El reproductor oficial es un iframe a `https://open.spotify.com/embed/album/<id>`: la
  dirección de `spotify` con `/embed`. En Spotify un single también es un álbum.
  - **Sin `spotify`, no hay reproductor.**
  - No hay audio de Astra, por la licencia.
- **El reproductor pone cookies de Spotify**: cargadlo solo con el consentimiento de cookies de terceros, o con un «Pulsa
  para cargar el reproductor». Sin sesión en Spotify se oyen 30 segundos de cada canción.
- **Comprar:** con `comprar` (`{url, tienda}`).
- **La página de un adelanto** trae `anuncia` (el disco que sale) y `entrada`.
- **Posts de Lanzamientos** (`actualidad.json`, `tipo: "lanzamientos"`):
  - traen **`clase`** (el mismo rótulo que en §2.1) y **`spotify`**, para poner el reproductor en el cuerpo del post;
  - **el texto ya no lleva enlaces de escuchar ni de comprar**: el reproductor y la compra los ponéis vosotros;
  - los posts anteriores al 7-nov pueden no traer `clase` ni `spotify`: tratadlos como «Lanzamiento» a secas y sin
    reproductor;
  - el post de un adelanto es más corto que el de un álbum o un EP.

## 5. Lo que no hay que hacer

- No calcular periodos, recuentos ni qué disco va en cada sitio: ya viene hecho en `radio`.
- No enseñar `publicado` como fecha del disco.
- No repetir el destacado en la rejilla.
- No enlazar a Spotify desde las tarjetas: la tarjeta lleva a la página del disco, y es allí donde está el reproductor.

Cuando esté, contestad con `PUBLICADO_LANZAMIENTOS_WEB.md`. Si algo no os encaja con el diseño, decidlo antes de
implementarlo: lo resolvemos en el servidor.
