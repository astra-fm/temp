# Actualidad (Noticias / Lanzamientos / Historias) — Contrato para el front

Instrucciones para el **agente de la web** que va a **consumir y pintar** el contenido
de Actualidad de astra.fm (noticias, lanzamientos, historias). **Solo lectura.**

> Documento entregado por el usuario (25-ago-2026), guardado aquí como referencia autoritativa
> del esquema real de Actualidad — usado también por `riot/scripts/migrateActualidadFromManager.js`
> y la pestaña `/studio/actualidad.html` del dashboard interno. Ver `.claude/SKILL.md` →
> "Sistema de Actualidad".

---

## 1. Arquitectura

- **Manager (astra.fm)** — crea/edita/publica el contenido (lado escritura, no te concierne).
- **Tu front** — **lee y pinta** los items `published` (lado lectura, sin auth).
- Almacenamiento: tabla propia en el servidor de astra.fm.
- API: endpoint `news-api.php` (mismo patrón que `portfolio-api.php`).

---

## 2. Endpoint

```
Base URL:  https://astra.fm/api/analytics/news-api.php
```

- **Sin autenticación** para leer. Devuelve por defecto **todo** (published y draft); filtra tú por `status=published` para mostrar solo lo público.
- **CORS abierto** (`Access-Control-Allow-Origin: *`) — puedes hacer fetch desde cualquier dominio, cliente o servidor (SSR/prerender).

---

## 3. Acciones

| Método | Acción | Notas |
|---|---|---|
| `GET` | `?action=list[&category=&status=&search=&limit=]` | Listado |
| `GET` | `?action=get&id={id}` | Un item por **id** |

**Filtros de `list`:**
- `category` = `lanzamientos` \| `noticias` \| `historias`
- `status` = `published` \| `draft` (para el front público usa **siempre `status=published`**)
- `search` = texto (busca en `title` y `excerpt`)
- `limit` = entero
- Orden: `COALESCE(published_at, updated_at) DESC` (más reciente primero)

> ⚠️ **`get` por slug NO está soportado** (solo por `id`). Para resolver una ruta `/{slug}`:
> pide `?action=list&status=published` (cada item trae su `slug`) y **haz match por `slug`** en el cliente,
> o usa `search`. Alternativa: guarda un mapa slug→id al construir el listado.

---

## 4. Envelope de respuesta

```json
{
  "success": true,
  "data":  [ /* array de items | item | null */ ],
  "message": null,
  "timestamp": "2026-07-25 08:00:00"
}
```
Errores → `success: false` + `message` + código HTTP.

---

## 5. Categorías

Tres, fijas. (Conteo actual orientativo: lanzamientos ~79, noticias ~9, historias ~5.)

| `category` | Etiqueta sugerida |
|---|---|
| `lanzamientos` | Lanzamientos |
| `noticias` | Noticias |
| `historias` | Historias |

---

## 6. Contrato de campos (por item)

| Campo | Tipo | Uso en el front |
|---|---|---|
| `id` | int | Clave; para `?action=get&id=` |
| `title` | string | Título card / detalle |
| `slug` | string | URL `/actualidad/{slug}` (resuélvela por list+match) |
| `excerpt` | string | Resumen en la card |
| `content` | HTML | **Cuerpo del artículo** (ver §7) |
| `content_html` | HTML \| null | Alternativo; normalmente usar `content` |
| `category` | `lanzamientos`\|`noticias`\|`historias` | Badge/filtro/color |
| `status` | `published`\|`draft` | **Mostrar solo `published`** |
| `featured_image_url` | string (URL) | Portada / thumbnail |
| `featured_image_media_id` | int \| null | Ref del media en WP (no imprescindible) |
| `published_at` | datetime (`YYYY-MM-DD HH:MM:SS`) | Fecha; ordena la lista |
| `created_at` / `updated_at` | datetime | Metadatos |
| `author_id` | int \| null | Autor (opcional) |
| `meta_title` / `meta_description` | string | SEO (`<title>` / meta description) |
| `views_count` / `visits` | int | Contador de vistas (opcional) |
| `featured_flux` / `featured_riot` | 0/1 | Flags de "destacado" internos de astra — **puedes ignorarlos** |
| `unique_visitors`, `total_social_shares/likes/comments`, `platforms` | — | Analítica/redes internas — **ignóralos para pintar** |

> **No hay `reading_time`.** Si lo quieres mostrar, calcúlalo del `content`
> (p. ej. `max(1, round(palabras / 200))` tras quitar el HTML).

### 6.1 Tamaños de imagen (WordPress media)

> 📍 **Dónde viven las imágenes de Actualidad:** en el propio **WordPress de astra.fm**
> (`https://astra.fm/astraless/wp-content/uploads/...`). **No** están en otro servidor.
> (Ojo: eso es distinto del *Portfolio*, cuyas imágenes sí se guardan en pablogoris.com/cms.)

`featured_image_url` que devuelve la API es siempre el tamaño **`full`** (el original). Las imágenes viven en la biblioteca de medios de WordPress, que genera **varios tamaños** para servir la imagen adecuada según el contexto (responsive).

**Cómo obtener los tamaños de una imagen:** consulta el media por su `featured_image_media_id`
(o el `data-media-id` de las imágenes dentro del `content`):

```
GET https://astra.fm/astraless/wp-json/wp/v2/media/{media_id}
→ media_details.sizes  →  cada tamaño: { source_url, width, height }
```

**Tamaños estándar de WordPress:**

| Nombre | Dimensiones | Uso típico |
|---|---|---|
| `thumbnail` | 150×150 (recortado cuadrado) | Miniaturas, avatares |
| `medium` | ≤ 300×300 | Cards pequeñas |
| `medium_large` | ≤ 768 de ancho | Cards / listados |
| `large` | ≤ 1024×1024 | Contenido/detalle |
| `full` | original | Hero / máxima calidad (= `featured_image_url`) |

> ⚠️ **Solo existen los tamaños menores que el original.** Ej.: una imagen de 700×700 solo
> tiene `thumbnail`, `medium` y `full` (no `medium_large` ni `large`). **No adivines la lista**:
> lee `media_details.sizes` (es la fuente de verdad por imagen).

**Patrón de URL** (informativo): WordPress añade `-{ancho}x{alto}` antes de la extensión;
`full` no lleva sufijo.

```
full          → .../then-comes-silence.jpg
medium (300)  → .../then-comes-silence-300x300.jpg
thumbnail     → .../then-comes-silence-150x150.jpg
```

**Recomendación de render:**
- **Card/listado:** `medium` o `medium_large`.
- **Detalle/hero:** `large` o `full`.
- Para responsive real, construye un `srcset` con los tamaños presentes en `media_details.sizes`
  y deja que el navegador elija.
- Las **imágenes dentro del `content`** también son media de WP (llevan `data-media-id`) — mismo
  mecanismo si quieres servir tamaños alternativos; si no, se pintan con la URL que ya trae el `<img>`.

> CORS: las imágenes (`<img src>`) no necesitan CORS. Si vas a **leer `media_details` por fetch
> cross-domain** desde el cliente, aplica el CORS de ese WordPress (normalmente OK); si no, hazlo
> en servidor/SSR/prerender.

---

## 7. Cómo pintar el `content` (importante)

`content` es **HTML enriquecido** producido por un editor. Trae estructuras que conviene
post-procesar para que se vean bien y sean responsive:

- **Imágenes con pie/fuente:** `<figure class="image-figure"><img data-media-id data-source><figcaption>…<a class="image-source">Fuente</a></figcaption></figure>`.
  Algunas imágenes antiguas vienen como `<img data-caption="…">` sin `figure` — conviértelas a `figure/figcaption`.
- **YouTube:** `<div data-youtube-video><iframe …></iframe></div>` — envolver para ratio 16:9 responsive.
- **Embeds** (Instagram, TikTok, Spotify…): `<iframe>` — contenedor responsive.
- **Tablas:** envolver en un contenedor con scroll horizontal en móvil.

**Referencia canónica de render** (ya implementado en el propio astra.fm):
`apps/web/src/components/RadarArticle.vue` — replica ese procesado (`processImageCaptions`,
`processContentTables`, `processEmbeddedVideos`) y sus estilos. Es la mejor plantilla.

---

## 8. Rutas públicas (referencia de astra.fm)

- Archivo/listado: `/actualidad`
- Detalle: `/actualidad/{slug}`

Tu front puede usar las rutas que quiera; solo respeta que el **detalle se resuelve por `slug`**
(que obtienes del listado).

---

## 9. Ejemplos (curl)

```bash
BASE="https://astra.fm/api/analytics/news-api.php"

# Listado público (todas las categorías, solo publicados)
curl -s "$BASE?action=list&status=published" | jq

# Solo lanzamientos, 10 más recientes
curl -s "$BASE?action=list&status=published&category=lanzamientos&limit=10" | jq

# Buscar por texto
curl -s "$BASE?action=list&status=published&search=grunge" | jq

# Un item por id
curl -s "$BASE?action=get&id=110" | jq

# Resolver un slug → pide el listado y filtra por slug en el cliente
curl -s "$BASE?action=list&status=published" | jq '.data[] | select(.slug=="mi-slug")'
```

---

## 10. Checklist de integración

1. Listado por categoría con `status=published`, ordenado por `published_at` desc.
2. Card: `featured_image_url` (o placeholder), badge de `category`, `title`, `excerpt`, fecha.
3. Detalle por `slug` (resuelto vía listado) — render de `content` con el procesado de §7 + SEO (`meta_title`/`meta_description`).
4. (Opcional) `reading_time` calculado, filtros por categoría.
