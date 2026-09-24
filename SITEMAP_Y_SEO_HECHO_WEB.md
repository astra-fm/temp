# Al servidor · El sitemap lo hace la web, y lo demás del informe ya está desplegado

24-sep-2026 · del front de la web · respuesta a `PETICION_INDEXACION_GOOGLE_WEB.md`

Va punto por punto, en vuestro orden. Todo lo de abajo está **en producción** desde hoy.

## 1. Cuatro versiones respondían 200 · hecho

En el `.htaccess`, antes que ninguna otra regla, incluida la de los ficheros que existen:

```apache
RewriteCond %{HTTPS} !=on
RewriteCond %{HTTP:X-Forwarded-Proto} !=https
RewriteRule ^ https://astra.fm%{REQUEST_URI} [R=301,L]

RewriteCond %{HTTP_HOST} ^www\.astra\.fm$ [NC]
RewriteRule ^ https://astra.fm%{REQUEST_URI} [R=301,L]
```

Las dos condiciones del primero van juntas a propósito: detrás de un proxy que termina el TLS,
`%{HTTPS}` dice `off` aunque la petición llegara por https, y con una sola condición se entraba en
un bucle de redirecciones.

Y `og:url` ya dice `https://astra.fm/…` en `index.html`, en `tv-og.php` y en `butaca-og.php`.

## 2. El sitemap · lo generamos nosotros, no hace falta que lo montéis

**Lo hace la web**, con un PHP en el mismo patrón que `butaca-og.php`. Gracias por ofreceros; no
os lo pasamos porque el reparto natural es este: vosotros publicáis el contenido y nosotros
sabemos qué forma tiene cada dirección, que es lo que cambia cuando tocamos el router.

`https://astra.fm/sitemap.xml` lo sirve ahora `sitemap.php`, que lee en el momento:

| Contrato | Qué saca | Direcciones |
|---|---|---|
| `actualidad.json` | `posts`, la clave es el slug | 96 |
| `programas.json` | `programs`, la clave es el slug | 9 |
| `radar/artistas.json` | `artistas[].artistSlug` | 6 |
| `tv.json` | `videos`, slug del contrato o derivado del `name` | 44 |
| `secuencias.json` | `secuencias[].slug` | 7 |

Más las 12 secciones fijas. **174 en total**, frente a las 6 de antes.

No es un fichero del build a propósito: vosotros publicáis una noticia y aparece en
`actualidad.json` sin que la web despliegue nada, así que un mapa escrito al compilar nace
caducado. Ese era justo el problema del anterior, con `lastmod` de febrero en septiembre.

Colchón de 10 minutos por contrato y una hora para el mapa montado, con copia de reserva en disco:
si un contrato no responde, se publica el resto del mapa con la última copia buena. Nunca un 500,
la misma regla de oro que `butaca-og.php`.

Fuera quedan las redirecciones del router, el área de cuenta, `/verificar-correo`,
`/restablecer-contrasena`, `/darse-de-baja`, `/preview` y `/ds-preview`.

### Dos cosas que os tocan a vosotros

**Los borradores.** `actualidad.json` manda hoy 98 `posts` y **dos van en `status: "draft"`**. Los
dejamos fuera del sitemap, pero siguen viajando en el contrato y la web los serviría si alguien
diera con su dirección. Si los borradores no son para publicarse, lo natural es que no salgan del
servidor.

**`tv.json` no manda `slug`.** La web lo deriva del `name` con su propia función, y el PHP del
sitemap tiene que calcar esa función para no anunciar direcciones distintas de las que la web
enlaza. Funciona —las 51 piezas coinciden, comprobado ejecutando las dos— pero es frágil: el día
que alguien corrija una errata en un `name`, la dirección cambia sola y la vieja queda muerta en
Google. `secuencias.json` ya manda `slug` desde la rev. 3 y ahí no existe el problema. **Si podéis
añadir `slug` a `tv.json`, desaparece.**

## 3. Soft 404 · hecho

`/actualidad/:slug`, `/shows/:id` y `/radar/artista/:slug` ponen `noindex` cuando no encuentran su
dato, en vez de servir la página vacía con 200.

De `/artist/:name` no hacía falta el `noindex`: esa ruta **ya no existe** y ahora es un 301 a
`/radar`. Ver el punto 6.

## 4. Las 12 alternativas con canónica · sin tocar

De acuerdo con vuestro análisis. Salen del sitemap con este cambio.

## 5. Robots · retiramos dos líneas

`/radio/artistas` y `/riot/artistas` **ya no están en el `robots.txt`**. El razonamiento es el
vuestro llevado un paso más: ya no son rutas del router y caen en «no existe», que lleva `noindex`.
Bloqueadas en el robots, Google no puede leer ese `noindex` y las deja colgadas como «bloqueadas»
en el informe. Sin el bloqueo, las lee y las retira. Quedan `/admin/` y `/api/`.

## 6. WordPress · apagado del todo

Va más lejos de lo que pedíais, porque Pablo decidió cerrarlo entero.

- **Los ficheros y las bases de datos están borrados**, hoy, por Pablo.
- `/astraless…` devuelve **410**, no 301. Un 301 a la portada mantendría vivas mil direcciones que
  no llevan a nada equivalente. La regla va **antes** de la que sirve los ficheros que existen, que
  si no se los comía.
- De la web se ha ido la vista que lo llamaba y todo lo que colgaba de ella: 10.241 líneas.
- Las rutas antiguas van a su sitio: `/musica` → `/radio`, `/artist/:name` → `/radar`.
- `/wordpress` devolvía **500** por una regla que apuntaba a un fichero inexistente. Retirada.

Comprobado antes de tocar nada: ninguno de vuestros siete contratos ni el índice de artistas
apuntaba a una imagen suya, y de las ocho secciones de la web solo `/musica` lo llamaba.

**Un aviso que os toca:** `api/analytics/public-news-api.php` sigue devolviendo noticias con
`imagen` e `imagen_medium` apuntando a `astraless/wp-content/uploads/…`, que desde hoy no existen.
Ese endpoint solo lo lee el manager, que no está en uso, así que no corre prisa. Pero está roto.

## 7. Rastreada sin indexar · de acuerdo, y no ahora

Coincidimos en el diagnóstico y en que no es urgente. `tv-og.php` y `butaca-og.php` ya demuestran
que el patrón funciona. Cuando toque, lo hablamos: si acabamos prerenderizando las rutas públicas,
lo que necesitaremos de vosotros es un título y una descripción por elemento en los contratos, y
`actualidad.json` ya trae `metaTitle` y `metaDescription`.

## Para cerrar

Pablo puede pulsar **Validar corrección** en Soft 404, en las dos filas de Duplicada y en Página
con redirección, y reenviar el sitemap.

Si veis algo raro en las 174 direcciones, decidlo y lo miramos.

— el front de la web
