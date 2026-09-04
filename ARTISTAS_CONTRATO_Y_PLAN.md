# Sección "Artistas" — Contrato de datos y plan de implementación

> Documento de diseño para mover la gestión editorial de artistas (bio + imagen) desde el
> *manager* en cdmon (WordPress headless) al **dashboard de voces**, que corre en el mismo
> servidor que AzuraCast. Referencia central del proyecto: [`.claude/SKILL.md`](../../.claude/SKILL.md).
>
> **Estado:** Fase 1 ✅ · Sonda cobertura (§8.1) ✅ · **Fase 2 (CRUD) ✅** · **Fase 3 (UI/form) ✅** ·
> **Fase 4 (mobile/web) ✅** · **Fase 5 (migración WP) ✅** · _última actualización: 2026-07-22_

---

## 1. Contexto y objetivo

Hoy la parte editorial de artistas se gestiona en el **manager** (cdmon, servidor distinto),
que guarda bio + imagen en **WordPress headless**. Para listar artistas, el manager llama a
AzuraCast por internet (`GET /api/station/3/files`, ~18 MB, ~15k archivos). Esa llamada remota
es el cuello de botella: lenta y difícil de mantener.

**Objetivo:** crear un apartado **Artistas** dentro del dashboard de voces
(`/studio/artistas.html`). Como el microservicio ([`riot/voices-microservice.cjs`](../../riot/voices-microservice.cjs))
está en el mismo servidor que AzuraCast, el listado se sirve desde disco local
([`riot/cache/metadata.json`](../../riot/cache/metadata.json), ya cacheado <24h) → rápido.

### Decisiones cerradas con el usuario (2026-07-01)

| Tema | Decisión |
|------|----------|
| **WordPress** | Se **retira para artistas**. El dashboard de voces pasa a ser fuente única de bio+imagen. WP solo interviene en la migración inicial. |
| **Imágenes** | Disco local `api/artists/images/[slug].ext`, servidas por el nginx estático :8080 (ya existe). ~0,5 GB para 2255 artistas; el servidor tiene 41 GB libres. |
| **Consumo** | **Mobile y web** leen la capa editorial como JSON estático desde :8080 (mismo patrón que `collections.json`). |

---

## 2. Modelo de datos: tres capas separadas

El dato de un artista hoy está mezclado entre tres orígenes. El diseño los separa de forma
explícita para que cada capa tenga un dueño claro:

| Capa | Contenido | Origen | Dónde vive | ¿Editable? |
|------|-----------|--------|-----------|-----------|
| **Identidad** | nombre, slug, nº de tracks en la biblioteca | AzuraCast (`metadata.json` local) | derivado → `api/artists/index.json` | no (derivado) |
| **Factual** | país, año de formación, tipo, género | MusicBrainz | [`riot/artistDatabase.json`](../../riot/artistDatabase.json) *(ya existe, 2255 artistas)* | no (auto) |
| **Editorial** | **bio, imagen** | Manual (antes WordPress) | **`api/artists/editorial.json` + `images/` (NUEVO)** | **sí (dashboard)** |

> La capa **Editorial** es lo único nuevo que se persiste. Identidad y Factual ya existen; solo
> se leen y se cruzan por `slug`.

---

## 3. El slug canónico

Clave estable que une las tres capas. Se deriva del nombre del artista con la función ya
existente `normalizeArtistName()` de [`riot/lib/artistInfo.js`](../../riot/lib/artistInfo.js) para
garantizar coherencia con el resto del sistema:

```
"Café Tacvba"      → "cafe-tacvba"
"Soda Stereo"      → "soda-stereo"
"AC/DC"            → "ac-dc"
```

Reglas: minúsculas · sin acentos/diacríticos · espacios y símbolos → `-` · colapsar `-`
repetidos · recortar `-` de los extremos.

> ⚠️ `artistDatabase.json` tiene hoy claves inconsistentes (ej. `"1915"`, `"Dave 202"`). El
> `slug` es la clave canónica nueva; el índice mantendrá un mapa `slug → displayName` para no
> depender de las claves heredadas.

---

## 4. Contrato de datos

### Convenciones (heredadas del sistema actual)

- **Timestamps:** ISO-8601 UTC (`"2026-07-01T17:00:00.000Z"`), campo `updatedAt`.
- **Respuestas del microservicio (:3001):** envoltorio `{ "success": true, "data": … }` en
  éxito; `{ "success": false, "error": "…" }` en error (patrón de los endpoints actuales).
- **Ficheros estáticos (:8080):** JSON plano con `updatedAt` en la raíz (patrón `collections.json`).

---

### 4.1 `api/artists/index.json` — listado (derivado, lo consume la UI y mobile/web)

Generado a partir de `metadata.json` + `artistDatabase.json` + `editorial.json`. Es la vista de
listado: rápida, cacheable, sin datos pesados (la bio completa NO va aquí).

```json
{
  "updatedAt": "2026-07-01T17:00:00.000Z",
  "total": 2255,
  "artists": [
    {
      "slug": "soda-stereo",
      "displayName": "Soda Stereo",
      "trackCount": 42,
      "country": "AR",
      "type": "banda",
      "hasBio": true,
      "hasImage": true,
      "image": "/artists/images/soda-stereo.jpg"
    }
  ]
}
```

| Campo | Tipo | Origen | Notas |
|-------|------|--------|-------|
| `slug` | string | derivado | clave canónica |
| `displayName` | string | metadata / editorial | nombre para mostrar |
| `trackCount` | number | `metadata.json` | nº de tracks en la biblioteca |
| `country` | string\|null | `artistDatabase.json` | código o nombre de país |
| `type` | string\|null | `artistDatabase.json` | `typeEs` (banda/solista…) |
| `hasBio` | boolean | `editorial.json` | badge "sin bio" en la UI |
| `hasImage` | boolean | `editorial.json` | badge "sin imagen" |
| `image` | string\|null | `editorial.json` | ruta relativa servida por :8080 |

---

### 4.2 `api/artists/editorial.json` — capa editorial (fuente única, lo escribe el dashboard)

Objeto indexado por `slug`. Es lo que sustituye a WordPress.

Esquema alineado 1:1 con los campos ACF de WordPress (ver §7), incluidos los **créditos de
fuente** de bio e imagen, que son dato editorial y hay que preservar.

```json
{
  "updatedAt": "2026-07-01T17:00:00.000Z",
  "artists": {
    "nightcrawl": {
      "slug": "nightcrawl",
      "displayName": "NightCrawl",
      "genero": "",
      "bio": {
        "text": "Nightcrawlers es un proyecto musical montado por el productor, DJ y vocalista John Reid...",
        "source": "https://www.last.fm/music/NightCrawl",
        "sourceName": "Last.fm"
      },
      "image": {
        "path": "/artists/images/nightcrawl.jpg",
        "width": 800,
        "height": 800,
        "source": "https://www.nightcrawlermusic.com/biography/",
        "sourceName": "Nightcrawlermusic",
        "updatedAt": "2026-07-01T17:00:00.000Z"
      },
      "origin": "wordpress-migrado",
      "wpPostId": 4135,
      "priority": false,
      "createdAt": "2026-07-01T17:00:00.000Z",
      "updatedAt": "2026-07-01T17:00:00.000Z"
    }
  }
}
```

| Campo | Tipo | Origen WP (ACF) | Notas |
|-------|------|-----------------|-------|
| `slug` | string | — | clave canónica (== clave del objeto) |
| `displayName` | string | `title.rendered` / `acf.nombre` | nombre editorial |
| `genero` | string | `acf.genero` | suele venir vacío |
| `priority` | boolean | — | **añadido 22-jul-2026.** Si `true`, esta bio/imagen ganan a TheAudioDB/Last.fm en la web (ver §8). Por defecto `false` (fallback normal, posición 3). Checkbox en `artistas.html` → "Priorizar esta ficha". |
| `bio.text` | string | `acf.biografia` | texto (puede traer HTML/comillas tipográficas; ver §7) |
| `bio.source` | string\|null | `acf.fuente` | URL de la fuente de la bio |
| `bio.sourceName` | string\|null | `acf.nombre_fuente` | crédito legible (ej. "Last.fm") |
| `image.path` | string\|null | *(descargada de `featured_media`)* | ruta local servida por :8080; `null` si no hay |
| `image.width/height` | number | — | dimensiones tras optimizar |
| `image.source` | string\|null | `acf.fuente_imagen_home` | URL de la fuente de la imagen |
| `image.sourceName` | string\|null | `acf.nombre_imagen_home` | crédito legible de la imagen |
| `origin` | enum | — | `manual` \| `wordpress-migrado` \| `musicbrainz` — trazabilidad |
| `wpPostId` | number\|null | `id` | id del post WP de origen (solo migrados) |
| `createdAt` / `updatedAt` | ISO | — | auditoría |

> Los clientes que hoy pintan el crédito ("Bio: Last.fm") siguen teniendo los mismos campos:
> `bio.source`/`bio.sourceName` ≡ `acf.fuente`/`acf.nombre_fuente`.

---

### 4.3 Endpoints del microservicio (:3001, tras nginx `/studio/api/`)

Siguen el patrón exacto de los endpoints de voces/plantillas ya existentes.

| Método | Ruta | Descripción | Respuesta |
|--------|------|-------------|-----------|
| `GET` | `/api/artists` | Listado (sirve/regenera `index.json`). Query opcional `?q=`, `?missing=bio\|image`. | `{ success, data: { total, artists:[…] } }` |
| `GET` | `/api/artists/:slug` | Ficha completa (identidad + factual + editorial fusionadas). | `{ success, data: { …artista completo } }` |
| `PUT` | `/api/artists/:slug` | Crea/actualiza bio y `displayName`. Body: `{ displayName?, bio }`. Escribe `editorial.json`. | `{ success, data }` |
| `POST` | `/api/artists/:slug/image` | Sube imagen (multipart). Optimiza y guarda en `images/`. | `{ success, data: { image } }` |
| `DELETE` | `/api/artists/:slug/image` | Borra la imagen y limpia la referencia. | `{ success }` |
| `POST` | `/api/artists/:slug/enrich` | Consulta fuentes externas (MusicBrainz vía `getMusicBrainzInfo`) y devuelve sugerencia de bio/imagen sin guardarla. | `{ success, data: { suggestion } }` |
| `POST` | `/api/artists/reindex` | Regenera `index.json` desde `metadata.json`. | `{ success, data: { total } }` |

**Ejemplo `PUT /api/artists/nightcrawl`**
```json
// request
{
  "displayName": "NightCrawl",
  "genero": "",
  "bio": {
    "text": "Nightcrawlers es un proyecto musical...",
    "source": "https://www.last.fm/music/NightCrawl",
    "sourceName": "Last.fm"
  }
}
// response
{ "success": true, "data": { "slug": "nightcrawl", "updatedAt": "2026-07-01T17:00:00.000Z" } }
```

> La imagen y sus créditos (`image.source`/`image.sourceName`) se envían por
> `POST /api/artists/:slug/image` (multipart + campos de crédito), no por este PUT.

---

### 4.4 Imágenes

- **Ubicación:** `/opt/astra-rotation/api/artists/images/[slug].[ext]`.
- **Servidas por:** nginx estático :8080 → `http://<host>:8080/artists/images/[slug].jpg`
  (el nginx ya sirve `/opt/astra-rotation/api/` como raíz; no hay que tocar su config).
- **Procesado al subir:** redimensionar a máx. 800×800, convertir a JPG (o WebP), calidad ~85.
  Objetivo ~150–300 KB/imagen.
- **Nomenclatura:** el `slug` es el nombre de fichero → idempotente, una imagen por artista.

---

### 4.5 Contrato estático para mobile y web (:8080)

El nginx estático ya publica `/opt/astra-rotation/api/`, así que estos recursos quedan expuestos
**automáticamente** al escribirse:

```
http://<host>:8080/artists/index.json          # listado (§4.1)
http://<host>:8080/artists/editorial.json       # capa editorial completa (§4.2)
http://<host>:8080/artists/images/[slug].jpg     # imágenes (§4.4)
```

**Espejo HTTPS para la web (2026-07-15):** los mismos recursos se publican en
`https://listen.astra.fm/artists/*` (conf `artists.conf` en el contenedor →
`/etc/nginx/azuracast.conf.d/`, copia host en la raíz del repo; proxy a `172.18.0.1:8080`,
sin auth, oculta el `no-store` del backend y pone `max-age=300` para JSON / `86400` para
imágenes). Motivo: una web HTTPS no puede leer el `:8080` (HTTP → contenido mixto). La app
móvil sigue usando `:8080` directo. Instrucciones de consumo para el agente de la web:
[`ARTISTAS_WEB_INSTRUCCIONES.md`](ARTISTAS_WEB_INSTRUCCIONES.md).

Mobile y web consumen **los mismos ficheros**. No se expone el microservicio :3001 a los
clientes: la edición es interna (dashboard), la lectura es estática (CDN-like, cacheable).

---

## 5. Plan de implementación por fases

| Fase | Entregable | Toca | Riesgo |
|------|-----------|------|--------|
| **0. Slug + esquema** | `normalizeArtistName()` como slug canónico; congelar esquemas de §4. | — | nulo |
| **1. Listado rápido** ✅ | `GET /api/artists` + `POST /api/artists/reindex`, derivando de `metadata.json`. Cachea `index.json`. **HECHO (2026-07-02)** — 2516 artistas, desplegado en :3001. | microservicio | bajo (solo lectura) |
| **2. CRUD editorial** ✅ | `GET/PUT /api/artists/:slug`, `POST/DELETE .../image` (ffmpeg, sin deps nuevas), `GET .../image` (stream same-origin). **HECHO (2026-07-02)**, desplegado. Bio texto plano + créditos + género → `editorial.json`; imagen optimizada JPG ≤800px → `images/`. | microservicio | bajo |
| **3. UI** ✅ | Formulario editable en [`artistas.html`](artistas.html): textarea bio + créditos + género + subir/importar/quitar imagen; botones "Usar esta bio/imagen" desde la sonda. **HECHO (2026-07-02)**, desplegado. | frontend | bajo |
| **4. Publicación mobile/web** ✅ | Verificar exposición de `api/artists/*` por :8080. **VERIFICADO (2026-07-15)** — `:8080/artists/index.json`, `editorial.json` e `images/1915.jpg` responden 200 sin cambios en nginx. | nginx (verificación) | nulo |
| **5. Migración WP→local** ✅ | **HECHA (2026-07-22)**. `riot/scripts/migrateArtistsFromWordPress.js`. 306 matches, 302 migrados (189 bio + 237 imagen), 4 saltados por curación manual previa, 0 errores. | script + microservicio | medio (ejecutado y verificado) |

⭐ La **Fase 1** entrega ya la ganancia de rendimiento y no toca nada existente.

### Cabecera y navegación (implementado)

Lógica de la cabecera compartida ([`nav-component.js`](nav-component.js)):

```
Sin página:      Astra FM │ Voces ▾   Artistas   Automatización
Página activa:   Astra FM — Plantillas │ Voces ▾   Artistas   Automatización
```

- Marca **Astra FM** (antes "Astra Radio").
- Breadcrumb con el **nombre de la página activa** (Plantillas / Configuración / Pronunciaciones /
  Podcast / Artistas), separado por `│` de la nav.
- **Artistas** es ítem de **primer nivel** (hermano del dropdown Voces y de Automatización), no
  dentro del desplegable.

### Puntos de anclaje en el código

- Microservicio: [`riot/voices-microservice.cjs`](../../riot/voices-microservice.cjs) — patrón de
  `PUT /api/voices/config/:programId` (~línea 162) y del mapa de artistas de
  `/api/podcasts/catalog` (~línea 907).
- Normalización / MusicBrainz: [`riot/lib/artistInfo.js`](../../riot/lib/artistInfo.js)
  (`normalizeArtistName`, `getMusicBrainzInfo`, `loadArtistDatabase`).
- Base factual existente: [`riot/artistDatabase.json`](../../riot/artistDatabase.json).
- Caché de identidad: [`riot/cache/metadata.json`](../../riot/cache/metadata.json).
- Despliegue del HTML al contenedor:
  `docker cp studio/artistas.html azuracast:/var/azuracast/www/web/studio/artistas.html`.
- Reinicio del microservicio: `sudo systemctl restart astra-voices`.

---

## 6. Consideraciones operativas

- **Capacidad:** 41 GB libres; imágenes ~0,5 GB (2255 artistas). No es limitación.
- **Refresco del listado:** `index.json` depende de `metadata.json`, que refrescan los cron de
  rotación (<24h). Para artistas nuevos al instante → `POST /api/artists/reindex`.
- **Autenticación:** ya cubierta — todo `/studio/*` pasa por `auth_request` contra el PHP
  de AzuraCast. No hay que añadir auth nueva.
- **Backups:** `editorial.json` es el dato de valor (trabajo editorial). Incluir en el backup
  mensual junto al resto de `api/`.
- ⚠️ **Systemd sandbox (trampa resuelta 2026-07-02):** el unit `astra-voices` tiene
  `ProtectSystem=strict`, así que por defecto **solo `riot/` era escribible** y `/tmp` era
  read-only (`PrivateTmp=false`). Para que la sección Artistas pueda escribir se añadió el drop-in
  `/etc/systemd/system/astra-voices.service.d/10-readwrite.conf` con
  `ReadWritePaths=/opt/astra-rotation/api`. Además, **los temporales de subida de imagen NO van a
  `/tmp`** (read-only) sino dentro de `api/artists/images/._upload_*` (área ya escribible).
- **Servir imágenes al dashboard:** `GET /api/artists/:slug/image` hace stream same-origin (https)
  para evitar mixed-content; el fichero también queda en `api/artists/images/` para :8080 (mobile/web).

---

## 7. Contrato WordPress de origen (resuelto) y mapeo de migración

Los artistas son **posts de WordPress**. Los clientes lo consultan en 2 llamadas:

```
# 1) buscar el post por nombre (search) y filtrar por título EXACTO normalizado
GET /astraless/wp-json/wp/v2/posts?_fields=id,title,content,acf,featured_media&search=<artista>&per_page=5
# 2) pedir el post completo con la imagen embebida (search no soporta _embed)
GET /astraless/wp-json/wp/v2/posts/<id>?_embed
```

**Matching:** no se usa el primer resultado; se normaliza (`NFD` → quitar diacríticos → minúsculas
→ trim) y se exige **coincidencia exacta de título** para evitar falsos positivos
(ej. "Amon Tobin" ≠ "Amon Amarth"). Nuestro `slug` deriva del mismo nombre con
`normalizeArtistName()`, así que la correlación de migración es **por nombre de artista**.

### Mapeo de campos WP → `editorial.json`

| Dato | Ruta en el JSON de WP | Campo destino |
|------|-----------------------|---------------|
| Nombre | `title.rendered` (o `acf.nombre`) | `displayName` |
| Biografía | `acf.biografia` | `bio.text` |
| Fuente bio (URL) | `acf.fuente` | `bio.source` |
| Crédito bio | `acf.nombre_fuente` | `bio.sourceName` |
| Imagen (URL) | `_embedded["wp:featuredmedia"][0].source_url` | *descargar* → `image.path` |
| Fuente imagen (URL) | `acf.fuente_imagen_home` | `image.source` |
| Crédito imagen | `acf.nombre_imagen_home` | `image.sourceName` |
| Género | `acf.genero` | `genero` |
| Id post | `id` | `wpPostId` |

**Extracción mínima (referencia del código cliente):**
```js
const bio    = post.acf?.biografia || "";
const imagen = post._embedded?.["wp:featuredmedia"]?.[0]?.source_url || null;
```

### Estrategia de migración (Fase 5) — EJECUTADA ✅ (2026-07-22)

En vez de 2 llamadas por artista, **paginar todos los posts** con `?_embed&per_page=100` y
mapear por título normalizado contra nuestra lista de artistas (`index.json`). Por cada match:
descargar `source_url` → optimizar → `images/[slug].jpg`; volcar bio + créditos + género a
`editorial.json` con `origin: "wordpress-migrado"` y `wpPostId`.

> ✅ **Formato de la bio verificado sobre datos reales:** `acf.biografia` viene como **texto
> plano** con `\n\n` como separador de párrafo (no HTML). Se conservó tal cual, sin sanitizar.

**Resultado (script `riot/scripts/migrateArtistsFromWordPress.js`, detalle en §10 Paso 10):**
1.052 posts en WordPress · 306 artistas locales con match exacto · 302 migrados (189 con bio,
237 con imagen) · 4 saltados por tener ya `origin:"manual"` (curación humana respetada) · 0
errores. `editorial.json` pasó de 13 a 315 artistas.

---

## 8. Cadena de prioridad de fuentes — **Opción A (decidida)**

**Orden de alimentación de la web (DEFINITIVO, confirmado por el usuario 2026-07-02):**

1. **TheAudioDB** — bio + imagen
2. **Last.fm** — bio (sin imagen usable: placeholder desde ~2019)
3. **Servidor** = capa editorial local (`editorial.json`) — **fallback curado**
4. **Carátula de álbum** (AzuraCast) — último recurso, solo imagen

```
BIO:    TheAudioDB  ▶  Last.fm  ▶  Servidor (editorial.json)
IMAGEN: TheAudioDB  ▶  Servidor (editorial.json)  ▶  carátula de álbum (AzuraCast)
```

> **Spotify descartado** (da problemas). El **servidor/editorial es fallback (posición 3) por
> defecto, NO máxima prioridad**: la ficha manual solo se usa cuando TheAudioDB y Last.fm no
> cubren al artista. Por eso la sonda de cobertura (§8.1) es útil: evita curar a mano lo que ya
> aportan los servicios.
>
> **Excepción — campo `priority` (añadido 22-jul-2026):** si el usuario marca "Priorizar esta
> ficha" en `artistas.html` para un artista concreto (p. ej. porque la bio de Last.fm es
> genérica o está mal), `editorial.artists[slug].priority = true` y esa bio/imagen **pasan a
> mostrarse primero**, saltándose TheAudioDB/Last.fm solo para ese artista — el resto de la
> biblioteca sigue con la cadena normal. Detalle del campo en §4.2; instrucciones de
> implementación para el agente de la web en `ARTISTAS_WEB_INSTRUCCIONES.md` §2.1. Motivo: un
> usuario reportó que, tras curar y guardar una bio, la web seguía mostrando la de Last.fm — no
> era un bug (el guardado funcionaba bien), sino el diseño de fallback puro; se decidió añadir
> esta vía de override en vez de invertir la cadena entera.

**Decisión (2026-07-01): Opción A.** El store local `editorial.json` **ocupa exactamente el papel
que tenía WordPress**: una **capa curada de fallback** que rellena los huecos de los artistas que
los servicios no cubren. **No** sustituye ni reordena la cadena de servicios.

- Los clientes siguen tirando de los servicios externos con normalidad; solo se **cambia el origen
  del fallback**: en vez de llamar a WordPress (remoto, en cdmon) consultan `editorial.json` local.
- El store se va poblando de forma incremental (migración WP + altas manuales) sin romper nada.
- Efecto colateral positivo: para los artistas con ficha editorial **y** sin imagen en los servicios,
  web y mobile pasan a mostrar **la misma imagen local**, eliminando la inconsistencia actual
  (hoy mobile cae a carátula de álbum aunque WP tenga imagen).

> **Matiz a decidir más adelante (no bloquea):** si en el futuro se quiere que la **edición manual
> del dashboard tenga prioridad sobre los servicios** (curaduría gana), sería un cambio de orden en
> el cliente. Por ahora se **preserva el comportamiento actual**: servicios primero, editorial como
> fallback.

> La **Opción B** (pre-resolución server-side de toda la cadena vía `/enrich`) queda **descartada
> por ahora**; el endpoint `/enrich` se mantiene solo como ayuda de sugerencia al editar, no como
> resolvedor automático.

### 8.1 Sonda de cobertura en servicios — Opción 1 (implementada 2026-07-02)

Para saber, **por artista y bajo demanda**, si ya hay bio/imagen en los servicios (y así no añadirla
a mano innecesariamente):

**`GET /api/artists/:slug/coverage[?name=<nombre>]`** — consulta en vivo Last.fm y TheAudioDB:

```json
{
  "success": true,
  "data": {
    "slug": "soda-stereo", "name": "Soda Stereo", "checkedAt": "…",
    "bio":   { "covered": true },
    "image": { "covered": true },
    "sources": {
      "lastfm":     { "configured": false },
      "theaudiodb": { "configured": true, "matched": "Soda Stereo", "exactMatch": true,
                      "hasBio": true, "hasImage": true, "image": "https://…", "bioPreview": "…" }
    }
  }
}
```

- **Servicios:** Last.fm (`artist.getinfo`, solo bio) + TheAudioDB (`search.php`, bio+imagen). Spotify no.
- **Heurística "bio real":** texto no vacío ≥ 40 chars tras quitar tags y el enlace "Read more" de Last.fm.
- **Matching:** compara nombre normalizado; expone `matched` + `exactMatch` para avisar de posibles
  falsos positivos (otro artista con nombre parecido). **Es un indicador, no verdad absoluta.**
- **UI:** botón "Verificar en servicios" en la ficha (`artistas.html`) → muestra cobertura por
  servicio, preview de bio, miniatura de imagen y aviso si el match no es exacto.

**Configuración de claves (sin hardcodear — por entorno del systemd unit):**
```bash
sudo systemctl edit astra-voices      # añadir:
# [Service]
# Environment=LASTFM_API_KEY=<clave>
# Environment=THEAUDIODB_KEY=<clave-patreon>
sudo systemctl restart astra-voices
```
Sin `LASTFM_API_KEY` → Last.fm sale "no configurado" (la bio se cubre solo con TheAudioDB).
Sin `THEAUDIODB_KEY` → usa la clave **free pública `123`** (limitada; docs: theaudiodb.com/free_music_api).
Base URL: `https://www.theaudiodb.com/api/v1/json/{KEY}/search.php?s=<artista>`.

> **Pendiente para fidelidad total:** replicar el matching/umbral exactos de los clientes
> (`useNowPlaying`/`api.js`) para que "cubierto" en el dashboard coincida con lo que ve el usuario.

---

## 9. Nota: renombrado futuro del dashboard

El dashboard nació como "**voces**" pero ya abarca plantillas, pronunciaciones, podcasts,
automatización y —con esto— artistas. En algún momento **se renombrará** a un nombre más amplio
(el "manager" editorial local). Implicaciones a tener en cuenta cuando se haga:

- Ruta pública `/voces-admin/` y prefijo de API `/voces-admin/api/` (nginx + `auth_request`).
- Systemd unit `astra-voices`, WorkingDir y `voices-microservice.cjs`.
- `nav-component.js`, títulos/subtítulos de las páginas.
- Rutas estáticas mobile/web (`/artists/*` en :8080 no dependen del nombre → no se ven afectadas).

> No bloquea este trabajo: la sección Artistas se construye bajo la ruta y nombres actuales; el
> renombrado se aborda como tarea transversal aparte para no acoplarlo a esta entrega.

**Renombrado INICIADO (2026-07-02) → `/studio/`.** Se creó el namespace canónico `/studio/`
(nginx `studio.conf`, alias al mismo directorio físico `web/studio/`, API `/studio/api/*` →
:3001). Las páginas y `nav-component.js` ya referencian `/studio/`. `/automation/` → **301** →
`/studio/automation.html` (deja de ser ruta aparte). `/voces-admin/` sigue vivo como **legacy**
(mismo dir). Así Artistas y Automatización son hermanas bajo `/studio/`, no hijas de "voces".
Confs guardadas en host: `/opt/astra-rotation/studio.conf` y `automation.conf`.
**Redirect `/voces-admin/` → 301 → `/studio/` HECHO (2026-07-02)** — preservando subruta; el API
legacy `/voces-admin/api/` se mantiene proxeando por compat. Verificado que el vínculo
dashboard↔motor es por ficheros (no URL), así que no afecta al motor. **Pendiente (opcional):**
renombrar el directorio físico `web/studio/` → `web/studio/`.

---

## 10. Registro de implementación paso a paso

Cada paso realizado, en orden, con **qué se hizo · archivos · endpoints · cómo probar · cómo desplegar**.
Todos los endpoints cuelgan de `/studio/api/…` (nginx → microservicio `:3001`). Despliegue del
microservicio: `sudo systemctl restart astra-voices`. Despliegue de páginas:
`docker cp studio/<archivo> azuracast:/var/azuracast/www/web/studio/<archivo>`.

### Paso 1 — Listado rápido (Fase 1) ✅
- **Qué:** listar los ~2.516 artistas de la biblioteca derivándolos **localmente** de
  `riot/cache/metadata.json` (18 MB, ya cacheado <24h), cruzados con `riot/artistDatabase.json`
  (país/tipo) y `api/artists/editorial.json` (hasBio/hasImage). Sustituye la lenta llamada remota
  que hacía el manager de cdmon a AzuraCast.
- **Archivos:** `riot/voices-microservice.cjs` (bloque "ARTISTAS — Fase 1"). Funciones
  `buildArtistsIndex()`, `normalizeArtistName()` (réplica del de `riot/lib/artistInfo.js`, que es
  ESM), `artistSlug()`. Escribe `api/artists/index.json`.
- **Endpoints:** `GET /api/artists?q=&missing=bio|image` · `POST /api/artists/reindex`.
- **Probar:** `curl :3001/api/artists?q=soda%20stereo` · `curl -X POST :3001/api/artists/reindex`.

### Paso 2 — Sonda de cobertura en servicios (Opción 1) ✅
- **Qué:** saber por artista si **ya hay bio/imagen** en los servicios y no curar a mano lo que ya existe.
- **Archivos:** `riot/voices-microservice.cjs` — `probeLastfm()`, `probeTheAudioDB()`, endpoint coverage.
- **Endpoint:** `GET /api/artists/:slug/coverage[?name=][&full=1]` (con `full=1` devuelve `bioFull`
  para pre-rellenar). Ver §8.1.
- **Claves (sin hardcodear):** `LASTFM_API_KEY` (drop-in systemd) y `THEAUDIODB_KEY` (default free `123`).
- **Probar:** `curl ":3001/api/artists/soda-stereo/coverage?name=Soda%20Stereo"`.

### Paso 3 — Cabecera/navegación + página ✅
- **Qué:** marca **Astra FM**, breadcrumb con página activa, **Artistas** como ítem de primer nivel.
- **Archivos:** `studio/nav-component.js` (marca + `divider` + ítem Artistas) ·
  `studio/artistas.html` (página nueva).

### Paso 4 — CRUD editorial (Fase 2) ✅
- **Qué:** crear/editar bio (**texto plano**) + créditos + género, y subir/importar/optimizar imagen.
- **Archivos:** `riot/voices-microservice.cjs` (bloque "Fase 2: CRUD editorial"). Helpers
  `_loadEditorial()`/`_saveEditorial()`, `_touchIndexEntry()` (actualiza badges en `index.json` sin
  reindex), `_saveArtistImage()` (ffmpeg → JPG ≤800px). Escribe `api/artists/editorial.json` +
  `api/artists/images/[slug].jpg`.
- **Endpoints:** `GET /api/artists/:slug` · `PUT /api/artists/:slug` ·
  `POST /api/artists/:slug/image` (raw `image/*` **o** JSON `{url}`) · `DELETE /api/artists/:slug/image` ·
  `GET /api/artists/:slug/image` (stream same-origin, evita mixed-content).
- **Probar:** `curl -X PUT :3001/api/artists/<slug> -H 'Content-Type: application/json' -d '{"bio":{"text":"…"}}'`.

### Paso 5 — Formulario de edición (Fase 3) ✅
- **Qué:** ficha editable en `artistas.html`: textarea bio + fuente/crédito + género + subir/importar/
  quitar imagen; botones **"Usar esta bio / Usar esta imagen"** que rellenan desde la sonda (TheAudioDB
  prioritaria). Guarda con `PUT`, refresca badges en caliente.

### Paso 6 — Infra systemd (trampa resuelta) ✅
- **Qué:** el unit `astra-voices` (`ProtectSystem=strict`) dejaba `api/` y `/tmp` de solo lectura.
- **Solución:** drop-in `/etc/systemd/system/astra-voices.service.d/10-readwrite.conf` →
  `ReadWritePaths=/opt/astra-rotation/api`; temporales de imagen en `api/artists/images/._upload_*`
  (no `/tmp`). Ver §6.

### Paso 7 — Fase 4 verificada ✅ (2026-07-15)
- `:8080/artists/index.json`, `:8080/artists/editorial.json` y `:8080/artists/images/1915.jpg`
  responden 200. No hizo falta tocar nginx (ya servía `api/` como raíz estática).

### Paso 8 — Espejo HTTPS + reindex automático + instrucciones web ✅ (2026-07-15)
- **Logs analizados (14 días):** nadie consumía `/artists/*` (solo curls de verificación);
  el único consumidor real del :8080 es la app móvil (`collections.json`).
- **Espejo HTTPS** `https://listen.astra.fm/artists/*` desplegado (ver §4.5): `artists.conf`
  → `docker cp` a `azuracast:/etc/nginx/azuracast.conf.d/` + `nginx -t` + reload. Verificado
  200 en los 3 recursos con CORS `*` y `Cache-Control` sanos.
- **Reindex:** lanzado a mano (2516 → **2664** artistas) + **cron diario 04:15 UTC**
  (`POST :3001/api/artists/reindex`, log en `riot/logs/artists-reindex.log`). `metadata.json`
  es caché de 24h refrescada oportunistamente por las rotaciones → no hay "momento fijo" al
  que engancharse; el índice va como mucho <24h por detrás.
- **Instrucciones para la web:** [`ARTISTAS_WEB_INSTRUCCIONES.md`](ARTISTAS_WEB_INSTRUCCIONES.md)
  (endpoints, esquema, algoritmo de slug, código de ejemplo, reglas de la cadena de fallback).

### Paso 9 — Campo `priority`: override editorial sobre TheAudioDB/Last.fm ✅ (2026-07-22)
- **Motivo:** el usuario reportó que, tras curar y guardar una bio, la web seguía mostrando la
  de Last.fm. Diagnóstico (leyendo el código, no de memoria): **no era un bug** — el guardado en
  `artistas.html`/`PUT /api/artists/:slug` funcionaba bien; era el diseño de fallback puro (§8):
  la ficha editorial es posición 3, nunca gana a TheAudioDB/Last.fm si estos cubren al artista.
- **Qué:** campo `priority` (boolean) por artista en `editorial.json`. Si `true`, esa bio/imagen
  se muestran **antes** que TheAudioDB/Last.fm para ese artista concreto (el resto de la
  biblioteca sigue con la cadena normal). Por campo: si `priority:true` pero falta la imagen,
  la imagen sigue su cadena normal (TheAudioDB → carátula) — no es todo-o-nada.
- **Archivos:** `riot/voices-microservice.cjs` — `GET /api/artists/:slug` devuelve `priority`;
  `PUT /api/artists/:slug` acepta y persiste `priority` en `ed.artists[slug]`. `studio/artistas.html`
  — checkbox "Priorizar esta ficha sobre TheAudioDB / Last.fm" en la Ficha editorial, badge
  "★ Prioritaria" en la lista lateral, se envía en el `PUT` de "Guardar ficha".
- **Probar:** `curl -X PUT :3001/api/artists/<slug> -H 'Content-Type: application/json' -d '{"priority":true}'`
  luego `curl :3001/api/artists/<slug>` y comprobar `"priority":true` con `bio`/`image` intactos.
- **Web:** instrucciones actualizadas en `ARTISTAS_WEB_INSTRUCCIONES.md` §2.1 (algoritmo
  completo, código de ejemplo) — pendiente que el agente de la web lo implemente.

### Paso 10 — Fase 5: migración WP→local ejecutada, con hotfix ✅ (2026-07-22)
- **Qué:** `riot/scripts/migrateArtistsFromWordPress.js` (nuevo). Pagina WordPress (`?_embed&per_page=100`,
  11 páginas, 1.052 posts), empareja por título exacto normalizado contra `index.json`, y reutiliza
  los endpoints existentes del dashboard (`PUT /api/artists/:slug` + `POST /api/artists/:slug/image`)
  para escribir — sin duplicar la lógica de guardado/optimizado de imagen.
- **Verificado contra WordPress real antes de construir:** contrato de §7 (campos ACF, imagen
  embebida, paginación) responde igual que lo documentado; bio confirmada como texto plano.
- **Salvaguarda:** cualquier artista con `origin:"manual"` en `editorial.json` se salta por
  completo (ni bio ni imagen) — protege la curación humana. Probado con `--dry-run` antes de
  ejecutar en real (mismo resultado: 306 matches, 4 saltados, 0 errores).
- **🐛 Bug detectado y corregido en la propia ejecución:** para que el script pudiera marcar
  `origin:"wordpress-migrado"` + `wpPostId`, se extendió `PUT /api/artists/:slug` a aceptar esos
  dos campos opcionales (antes solo bio/imagen/género/priority) — el dashboard nunca los envía,
  así que las ediciones manuales no cambian de comportamiento. **Pero se olvidó extender también
  `POST /api/artists/:slug/image`**, que es el único endpoint llamado para artistas migrados que
  solo tenían imagen (sin bio) — esas 113 entradas quedaron con `origin:"manual"` por el valor
  por defecto del endpoint. Detectado comparando el conteo de `origin:"manual"` antes/después
  (13 → 126, un salto no esperado). **Fix:** (1) se extendió también el endpoint de imagen para
  aceptar `origin`/`wpPostId`; (2) se corrigió el propio script para llamar siempre a `PUT` (aunque
  no haya bio) antes de la imagen, fijando el origen correcto desde el principio; (3) script
  puntual de reparación (`fix_mistagged_origin.mjs`, no versionado — uso único) corrigió las 113
  entradas ya escritas sin tocar su bio/imagen, cruzando de nuevo contra WordPress por nombre.
- **Resultado final verificado:** `editorial.json` 13 → **315** artistas. `origin` distribuido en
  **13 manual** (los originales, intactos) + **302 wordpress-migrado** (189 con bio, 237 con
  imagen). `index.json` ya refleja los badges (`hasBio`/`hasImage`) sin reindex manual — cada
  `PUT`/`POST .../image` llama a `_touchIndexEntry()`. Espejo HTTPS (`listen.astra.fm/artists/*`)
  sirve los 315 de inmediato. Bio de muestra (Almendra, Yeah Yeah Yeahs) confirmada como texto
  plano limpio, sin HTML ni problemas de encoding.
- **Probar:** `curl :3001/api/artists/almendra` → `origin:"wordpress-migrado"`, `wpPostId:3886`.

### Paso 11 — Consumo web del campo `priority` CONFIRMADO en producción ✅ (2026-07-23)
- Caso real: se activó `priority:true` en "The Fall" con una bio alternativa a la de Last.fm.
  Diagnóstico del bundle de producción (`www.astra.fm/js/app.*.js`, Vue): la lógica de prioridad
  está bien implementada — cuando `priority && bio`, la función que resuelve la bio del artista
  actual salta correctamente TheAudioDB/Last.fm.
- **Matiz encontrado (no es bug):** esa función solo se ejecuta al **cambiar de canción**, no en
  un timer propio — si el artista editado ya está sonando, el cambio no se ve hasta la siguiente
  vez que suene o hasta recargar la página. Confirmado por el usuario: recarga completa → se vio
  la bio editada. Sugerencia documentada para el agente de la web en
  `ARTISTAS_WEB_INSTRUCCIONES.md` §6 (re-resolver cada 60-90s mientras el mismo artista siga
  sonando), no bloqueante.

### Paso 12 — Botón "Traducir a español" (DeepL) ✅ HECHO, clave instalada y probada (2026-07-24)
- **Motivo:** bios importadas de Last.fm (o pegadas a mano) suelen venir en inglés; hacía falta
  traducir sin salir del dashboard.
- **Qué:** `POST /api/translate` en `riot/voices-microservice.cjs` — recibe `{text}`, llama a la
  API de DeepL (`target_lang=ES`, `source_lang` sin fijar → autodetección, no siempre es inglés),
  devuelve `{text, detectedSourceLang}`. **No persiste nada**: el botón solo rellena el textarea
  de Biografía en `artistas.html`; guardar sigue siendo un paso aparte y explícito ("Guardar
  ficha"), igual que "Usar esta bio" de la sonda de cobertura.
- **Selección de host por sufijo de clave:** las claves del plan Free de DeepL terminan en `:fx`
  y usan `api-free.deepl.com`; las de pago usan `api.deepl.com`. Se detecta solo, sin campo de
  configuración aparte — pegar la clave en `DEEPL_API_KEY` basta sea cual sea el plan.
  DeepL API Free: 500.000 caracteres/mes gratis según su Centro de Ayuda oficial (no se pudo
  confirmar de antemano si el alta pedía tarjeta; el usuario ya tiene la clave, plan Free
  confirmado por el sufijo `:fx`).
- **Sin clave configurada, falla con claridad:** `503 {"error":"DEEPL_API_KEY no configurada..."}`
  — no rompe el resto del microservicio (probado antes de instalar la clave).
- **Desplegado:** `artistas.html` con el botón, `docker cp` hecho y verificado en el contenedor.
- **Clave instalada (2026-07-24):** `DEEPL_API_KEY` añadida a
  `/etc/systemd/system/astra-voices.service.d/override.conf` (mismo fichero que `LASTFM_API_KEY`,
  permisos `600`) → `systemctl daemon-reload && systemctl restart astra-voices`. Probado con una
  bio real en inglés (The Fall) → traducción correcta y natural, `detectedSourceLang:"EN"`.
  Verificado que la clave no quedó en ningún fichero versionado del repo.

### Paso 13 — Fix: "Fuente de la biografía/imagen" no se rellenaba con "Usar esta X" ✅ (2026-07-24)
- **Reportado por el usuario:** al pulsar "Usar esta bio" (Last.fm/TheAudioDB), se rellenaba
  "Crédito de la biografía" pero **no** "Fuente de la biografía (URL)".
- **Causa 1 (frontend, `artistas.html`):** la línea que debía rellenar `bioSource` era un no-op —
  `el('bioSource').value = el('bioSource').value || ''` (se asignaba a sí misma). Arreglado para
  usar `s.url` de la cobertura. Mismo hueco en imagen: `importImage()` nunca enviaba el campo
  `source` al backend — arreglado con fallback a la propia URL de la imagen si no se pasa una
  explícita.
- **Causa 2 (backend, sonda de cobertura):** ninguna de las dos funciones (`probeLastfm`,
  `probeTheAudioDB`) devolvía una URL de origen. Añadido `url` a ambas: Last.fm expone
  `artist.url` (página canónica, ej. `last.fm/music/Soda+Stereo`); TheAudioDB no tiene "página de
  la bio" propia, así que se usa `strWebsite` (web oficial del artista) como mejor referencia
  disponible, con `https://` añadido si falta.
- **🐛 Bug propio detectado en la implementación de esta corrección:** al añadir `const url` para
  la web de TheAudioDB, sombreé sin querer la `const url` ya existente en el mismo bloque `try`
  (la URL de la petición HTTP a TheAudioDB) — por *temporal dead zone* de JS, cualquier referencia
  a `url` dentro de ese bloque pasó a resolver la variable nueva (aún sin inicializar en el punto
  del `fetch`), rompiendo la sonda con `"Cannot access 'url' before initialization"`. Detectado
  probando el endpoint real tras el cambio (no dando el código por bueno solo porque compilaba).
  Corregido renombrando a `siteUrl`.
- **Verificado end-to-end:** `GET /api/artists/soda-stereo/coverage?name=Soda%20Stereo` →
  `lastfm.url` y `theaudiodb.url` ambos presentes, sin errores; resto del microservicio sin
  regresión.

### Pendiente
- **Contenido editorial:** 315 artistas con ficha (302 migrados + 13 curados a mano) de ~2664
  listados. `priority:true` ya probado y funcionando en producción (The Fall); el resto de casos
  de uso son decisión editorial caso por caso.
- **Consumo web (bio/imagen base + `priority`) — CONFIRMADOS en producción** (2026-07-17 y
  2026-07-23 respectivamente). Con 315 artistas ahora cubiertos (antes 13), debería notarse más
  tráfico a `images/*.jpg` en los logs de `:8080`.
- **Refresco en directo del `priority`** — mejora de UX no bloqueante, pendiente en el lado web
  (§6 de `ARTISTAS_WEB_INSTRUCCIONES.md`), no depende de este repo.
- **Traducción DeepL** — HECHO y probado en producción (Paso 12).
