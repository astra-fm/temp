# A la web · Google no indexa 38 páginas de astra.fm: qué lo causa y qué os pedimos

24-sep-2026 · del servidor · a petición de Pablo, a partir del informe de Search Console

Pablo nos pasó el resumen de **Páginas no indexadas** de Search Console. Solo trae los recuentos
por motivo, no las URL, así que lo de abajo sale de medir `astra.fm` en vivo hoy y de leer vuestro
`.htaccess`, `public/sitemap.xml` y el bundle publicado (`app.c129a6f9.js`). Todo se arregla en
vuestro lado: `astra.fm` es vuestro Apache (185.42.105.72) y nosotros no tenemos acceso.

| Motivo en Search Console | Páginas |
|---|---|
| Página alternativa con etiqueta canónica adecuada | 12 |
| Rastreada: actualmente sin indexar | 9 |
| Soft 404 | 8 |
| Bloqueada por robots.txt | 5 |
| Duplicada: Google ha elegido una versión canónica diferente a la del usuario | 2 |
| Duplicada: el usuario no ha indicado ninguna versión canónica | 1 |
| Página con redirección | 1 |

## 1. Cuatro versiones de la web responden 200 · **lo primero**

```
http://astra.fm/        200
http://www.astra.fm/    200
https://www.astra.fm/   200
https://astra.fm/       200
```

Ninguna redirige. El `.htaccess` no tiene reglas de HTTPS ni de `www`. Además la portada se
contradice: `<link rel="canonical" href="https://astra.fm/">` pero
`<meta property="og:url" content="https://www.astra.fm/">`.

Esto explica las dos **Duplicadas** (3 páginas).

**Os pedimos:**
- En el `.htaccess`, un 301 de `http://` y de `www.` a `https://astra.fm` con la misma ruta, antes
  de las reglas de la SPA.
- Que `og:url` diga `https://astra.fm/…` como la canónica, en el `index.html` y en
  `tv-og.php` / `butaca-og.php` si también la ponen.

## 2. El sitemap está desfasado

`public/sitemap.xml` tiene 6 URL con `lastmod` de febrero y marzo. Dos de ellas son redirecciones
del router (`/shows` → `/radio/shows`, `/tv` → `/butaca`) y faltan casi todas las páginas con
contenido: `/radio/shows`, `/butaca`, `/radar`, `/musica`, las noticias (`/actualidad/:slug`), los
programas (`/shows/:id`), los artistas (`/artist/:name`, `/radar/artista/:slug`) y las piezas de
Butaca.

**Os pedimos** generarlo en el build (o con un PHP como `tv-og.php`) a partir de los contratos que
ya leéis: `actualidad.json`, `programas.json`, el índice de artistas y `tv.json`. Solo URL finales,
nunca las que redirigen. Si os viene mejor que lo generemos nosotros y lo sirvamos en un JSON o en
un XML, decidlo y lo montamos.

## 3. Soft 404: las fichas que no existen responden como si existieran

Cualquier ruta devuelve el mismo `index.html` con 200 (lo esperable en una SPA). Vuestra vista
`NoExisteView` ya pone `robots: noindex`, bien. Pero las fichas no lo ponen cuando el contenido no
existe: lo hemos buscado en los trozos `actualidad-detail`, `show-detail` y `radio`, y no aparece
ningún `noindex`. Una noticia borrada, un programa retirado o una URL antigua sale en Google como
página vacía con 200. Eso es un Soft 404 (8).

**Os pedimos:** cuando `/actualidad/:slug`, `/shows/:id`, `/artist/:name` o
`/radar/artista/:slug` no encuentren su dato, que monten `NoExisteView` o pongan `noindex`. Si
conocéis URL antiguas con equivalente (de WordPress, por ejemplo), mejor un 301 en el `.htaccess`.

## 4. Las 12 alternativas con canónica: están bien

Son las rutas que el router redirige (`/shows`, `/tv`, `/riot`, `/riot/shows`, `/riot/tv`,
`/radio/tv`, `/canales/riot`, `/demos`, `/canal-abierto`…). Google las ve como copias de su
destino porque el `afterEach` reescribe la canónica. No hay que tocar nada. Bajarán cuando salgan
del sitemap (punto 2) y dejen de enlazarse.

## 5. Bloqueadas por robots.txt (5): lo esperable

Vuestro `robots.txt` cierra `/admin/`, `/api/`, `/radio/artistas` y `/riot/artistas`. Si la
propiedad de Search Console es de dominio, también cuenta `listen.astra.fm`, que cerramos a
propósito. Solo os pedimos confirmar que `/radio/artistas` y `/riot/artistas` siguen haciendo falta
en el robots: ya no son rutas del router y caen en `no-existe`.

## 6. La redirección (1) y un WordPress que sigue publicado

`https://astra.fm/astraless/` devuelve 301 a `https://www.astra.fm/astraless/`, y ahí responde
**el WordPress antiguo, entero e indexable** (`<title>Astra Radio – Radio y Arte Visual</title>`,
feeds y `wp-json` abiertos). WordPress está retirado del sistema desde el 4-sep. Os pedimos que
`/astraless/` deje de servirse: un 301 a la portada o a la sección equivalente, o un 410.
Coordinadlo con lo que quede de `/darse-de-baja`.

## 7. Rastreada, sin indexar (9): lo de fondo

Sin JS, Google ve la misma página de 5.445 bytes en todas las URL, con título y canónica de la
portada. Primero renderiza y después decide, y con contenido que parece igual muchas se quedan
fuera. Los puntos 1-3 ya ayudan. La solución de fondo es que cada URL llegue con su `<title>`, su
descripción y su canónica en el HTML, como ya hacéis con `tv-og.php` y `butaca-og.php`, o
prerenderizando las rutas públicas. No es urgente. Contadnos qué os parece.

## Para cerrar

Cuando esté, decídselo a Pablo para que pulse **Validar corrección** en cada motivo de Search
Console y reenvíe el sitemap. Si queréis la lista exacta de URL por motivo, Pablo puede exportarla
desde cada fila del informe.
