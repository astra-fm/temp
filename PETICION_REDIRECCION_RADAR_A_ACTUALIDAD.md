# Un 301 en nginx: las noticias antiguas de `/radar/<slug>` a `/actualidad/<slug>`

18-10-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 18.10.26

## Por qué

Hasta finales de agosto la sección de noticias se llamaba Radar y cada noticia vivía en
`https://astra.fm/radar/<slug>`. El 31.08.26 pasó a llamarse Actualidad (`/actualidad/<slug>`, **mismo
slug**) y el nombre Radar pasó al buzón de artistas.

Las direcciones viejas siguen circulando: Search Console de astra.fm enseña siete (cinco como
«Soft 404» y dos como «Rastreada: actualmente sin indexar»), y habrá más en redes y en otras webs.
Hoy todas responden **200 con la página de «no existe»** de la web, aunque la noticia está publicada.
Para Google eso es un Soft 404 y lo que valiera la dirección vieja se pierde.

Entonces no se redirigieron porque el buzón usaba `/radar/<slug>` para su ficha por trabajo. Esa ruta
se retiró el 01.09.26: la ficha vive ahora en `/radar/artista/<slug>`. **`/radar/<slug>` ya no es de
nadie en la web**, y la redirección no choca con nada.

Tiene que ser un 301 del servidor: un redirect en el router de la web es JavaScript, y ni Google lo
trata como 301 ni Facebook o WhatsApp lo ven.

## La petición

En el `server` de `astra.fm`:

- `/radar/<slug>` y `/radar/<slug>/` → **301** a `https://astra.fm/actualidad/<slug>`, con la query si
  la hay.
- **Sin tocar** `/radar` a secas ni `/radar/artista/<slug>`, que son el buzón y siguen siendo rutas
  de la web.
- Solo un segmento: algo como `/radar/a/b` sigue yendo a `index.html` como ahora.

Una forma de hacerlo, por si sirve (antes del `location /` que manda a `index.html`):

```nginx
location ~ ^/radar/(?!artista(?:/|$))([^/]+)/?$ {
    return 301 https://astra.fm/actualidad/$1$is_args$args;
}
```

Un slug que nunca fue noticia acaba en `/actualidad/<slug>` con la página de «no existe», igual que
hoy, así que no empeora nada.

## Para comprobarlo

```
curl -sI https://astra.fm/radar/horrors-night-life        → 301, location: https://astra.fm/actualidad/horrors-night-life
curl -sI https://astra.fm/radar/horrors-night-life/       → 301, el mismo destino
curl -sI https://astra.fm/radar/artista/deprieto          → 200 (sin cambios)
curl -sI https://astra.fm/radar                           → 200 (sin cambios)
```

Las cuatro dan 200 hoy (medido el 18.10.26).

## En la web

Nada que desplegar. Cuando nos confirméis que está, actualizamos el aviso del router de la web
(el que explica por qué se quitaron los redirects el 31.08) para que nadie vuelva a usar
`/radar/<slug>` para otra cosa sin quitar antes la regla.

— el front de la web
