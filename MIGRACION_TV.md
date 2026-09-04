# Migrar TV a otro servidor — instrucciones (documento del usuario, 26-ago-2026)

> Documento aportado por el usuario con el contrato del sistema TV **original del manager de
> astra.fm**. Se conserva como referencia de la fuente. La migración ya se ejecutó ese mismo
> día: los vídeos viven ahora en `api/tv.json` de este servidor (espejo público
> `https://listen.astra.fm/tv.json`, contrato para la web en `TV_WEB_INSTRUCCIONES.md`).
> **Nota:** solo se migró el canal `riot` (18 filas de `flux` descartadas — el canal ya no
> existe en el sistema, decisión del usuario).

Cómo mover la sección **TV** (gestión de vídeos) a otro servidor. TV es de las más sencillas de
migrar: son **vídeos externos** (YouTube/Vimeo) + metadatos en una tabla MySQL. **No hay
archivos de vídeo ni imágenes que copiar** (el vídeo vive en YouTube/Vimeo y el `thumbnail_url`
apunta a YouTube).

## 1. Qué compone TV (en el manager)

- **Escritura (manager, astra.fm):** `views/media/TVList.vue`, `components/tv/TVTable.vue`
  (CRUD), `components/tv/TVModal.vue` (formulario), `stores/tv.store.js`.
- **Lectura (web pública):** `apps/web/src/services/tvVideos.js`,
  `apps/web/src/views/ChannelTVView.vue`.
- **Backend:** `tv-videos-api.php` (en `https://astra.fm/api/analytics/`), tabla MySQL
  `tv_videos` (57 filas a 26-ago-2026: 39 riot + 18 flux).
- Los desplegables del modal (canal/programa/artista/canción) salían de **AzuraCast +
  WordPress** de la radio (`azuracast.service.js`).

## 2. Esquema de la tabla `tv_videos`

`id` (BIGINT AI PK), `name`, `playlist` (nombre del programa), `artist_name`, `song_title`,
`canal` (`riot`|`flux`), `url` (YouTube/Vimeo), `source` (`youtube`|`vimeo`), `description`,
`duration` (string, suele vacío), `thumbnail_url` (derivado, p.ej.
`https://img.youtube.com/vi/{id}/maxresdefault.jpg`), `published_at` (DATE — ⚠️ hay filas con
`0000-00-00`), `created_at`/`updated_at`.

## 3. Contrato del API original (`tv-videos-api.php`)

Envelope `{success, data, message, timestamp}`, CORS `*`.

| Método | Acción |
|---|---|
| GET | `?action=list[&canal=riot\|flux]` |
| POST | `?action=create` (body JSON) |
| POST/PUT | `?action=update&id={id}` |
| DELETE | `?action=delete&id={id}` |

## 4. Cómo se resolvió en este servidor (lo realmente hecho)

En vez de clonar el PHP+MySQL, se siguió el patrón de la casa (igual que Actualidad):

- **Almacenamiento:** `api/tv.json` (campos con los mismos nombres snake_case del manager;
  `published_at = '0000-00-00'` normalizado a `null`).
- **Migración:** `riot/scripts/migrateTvFromManager.js` (`--dry-run`; no destructivo: conserva
  siempre los vídeos con `origin !== "manager-migrado"`; solo canal riot).
- **CRUD:** endpoints `GET/POST /api/tv`, `PUT/DELETE /api/tv/:id` en
  `riot/voices-microservice.cjs` (deriva `source` y `thumbnail_url` de la URL de YouTube).
- **Dashboard:** `studio/tv.html` — los desplegables salen de **este servidor**
  (`programas.json` para el programa, índice local de artistas), no de WordPress.
- **Espejo público:** `tv.conf` → `https://listen.astra.fm/tv.json` (max-age 300, CORS *).
- **Contrato para la web:** `TV_WEB_INSTRUCCIONES.md`.

## 5. Gotchas heredados (siguen aplicando)

- `published_at` venía como `0000-00-00` en las 57 filas → aquí es `null`; ordenar por
  `published_at || created_at`.
- `duration` es string y suele venir vacío.
- El vídeo y el thumbnail siguen viviendo en YouTube: si un vídeo se borra de YouTube, la
  entrada queda huérfana (no hay copia local).
