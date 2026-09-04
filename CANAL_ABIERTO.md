# Canal abierto — arquitectura (documento de la web, archivado 26-ago-2026)

> Documento aportado por el usuario (escrito por el agente de la web) que define la sección
> **«Canal abierto»** y pide la parte de servidor. Archivado aquí como referencia — el original
> vive en el repo de la web. Lo pedido en su §4 se implementó el mismo día: backend CRUD,
> `api/canal-abierto.json`, espejo `https://listen.astra.fm/canal-abierto.json`, dashboard
> `/studio/canal-abierto.html` y contrato `CANAL_ABIERTO_WEB_INSTRUCCIONES.md`.

## Lo esencial

- **Nombre cerrado: «Canal abierto»** (decisión de Pablo, 26-08-26). Sustituye a «Demos», que
  sugería material sin terminar cuando lo que se pide es un disco.
- **Qué es:** el catálogo de lo que entra por el buzón de la radio. La página junta la
  **convocatoria** («mándanos tu disco», el componente `SendRecord` ya existente, hoy un
  `mailto:info@astra.fm`) con el **escaparate** (las fichas de lo que llegó). Cumple la promesa
  del buzón: «con eso ya tenéis ficha en la web».
- **Ruta web:** `/canal-abierto`, ficha en `/canal-abierto/:slug`. **Sin entrada de menú** en
  la web (decisión de Pablo): se llega por la puerta de la portada (que hoy miente con «Ver
  demos» → `/actualidad`), por el buzón de «Sobre Astra FM» y por el pie.

## Modelo de datos (§3 del original, resumido)

Reglas: cada campo obligatorio de más es una banda menos (lo mínimo = lo que el buzón ya pide;
la diferencia la pone la redacción al escucharlo), y nada de campos que lleguen siempre vacíos.

- **Mínimo (banda):** `slug` (derivado artista+título), `artist`, `title`, `city`, `format`
  (`lp|ep|single|maqueta`), `cover` (cuadrada, ≤800px, servida ya optimizada, `null` si no
  hay), `description` (dos frases), `arrivedAt` (`YYYY-MM-DD` **sin hora**), `status`
  (`published|draft`).
- **Lo que hace útil la sección (redacción/archivo):** `genres[]` (🔴 el campo que convierte la
  lista en sección navegable — **mismo vocabulario que `generos` de `programas.json`**, array
  porque casi nada es de un solo género), `year`, `label` («Autoeditado» vale), `country`,
  `featuredTrack`, `links[]` `{label,url}`, `listen` (url escuchable o null).
- **Lo que cierra el círculo (la promesa «si entra, entra en la parrilla»):** `onAir` (bool),
  `programme` (⚠️ la **clave** de `programas.json` — `hcr`, `romanticos` —, no el nombre
  visible: buscar por nombre falla en silencio justo con Nocturna), `firstPlayedAt`.
- Apuntado sin defender: `soundsLike[]` (2-3 referencias) — decidir cuando haya ~20 fichas.
- Extras del ejemplo §4.1: `gigs[]` `{date,venue,city}` (próximas fechas) y `next` (string,
  «qué viene después»).

## Lo pedido al servidor (§4) — cumplido

`canal-abierto.json` con el patrón de las otras tres fuentes (CORS, max-age 300), array
`entradas` **ya ordenado por `arrivedAt` desc**, gestionado desde `/studio/canal-abierto.html`.
Las 4 lecciones de las integraciones anteriores, incorporadas: fecha sin hora pegada; imagen ya
a su tamaño (cuadrada ≤800px); ruta relativa y `null` explícito; sin campos siempre vacíos.

## Fases de la web y pendientes (suyos)

- **F1** — página con solo la convocatoria (borra `src/data/demos.js`, la puerta deja de
  mentir). No necesita servidor. **F2** — el escaparate cuando haya datos en
  `canal-abierto.json`.
- Sin decidir (web): si se publica todo lo que llega o solo lo que entra en parrilla; el
  formulario (el `mailto` es a propósito hasta entonces — con este backend, un formulario que
  escriba directo al buzón del estudio «deja de ser descabellado»); si la tabbar móvil lo
  incluye.
