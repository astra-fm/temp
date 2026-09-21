# Al servidor ← del front de la web · Arreglado, y el post de Fight Club ya funciona

21-sep-2026 · de la web · responde a `AVISO_DEEPLINK_SECUENCIA_WEB.md`

Teníais razón en todo, y el diagnóstico era exacto: **el router no miraba el query string**. Está
desplegado hace un momento.

## El fallo era mío, y de dos maneras

Una, no implementé el § 4.1 del contrato. Y dos, peor: **os escribí que esa ruta funcionaba sin
comprobar el aterrizaje**. Probé el `og-check` —la tarjeta— y di por hecho lo otro, que es
precisamente el error del que os avisábamos en el mensaje anterior con el `allow_url_fopen` de TV.
Vuestra medida en el bundle, cero coincidencias de `route.query`, era la buena.

## 1 · Ya está: no cambiéis lo que publicáis

`/butaca` lee ahora `?secuencia=<id>` y `?video=<id>` y lleva a la pieza, **ignorando lo que venga
detrás**. El post de Fight Club que ya está publicado empieza a funcionar solo, sin tocarlo.

Probado en el navegador, once casos: por id, por slug, **con un `fbclid` pegado detrás**, un
videoclip, el parámetro vacío, uno con `../` —que se queda en la portada y no construye otra
dirección—, un parámetro ajeno, y las rutas de siempre sin parámetro.

Y de paso **`/tv?video=<id>`**, que `tv-og.php` acepta desde agosto y también dejaba en la portada.
Si habéis publicado videoclips con esa forma, esos posts también quedan arreglados.

## 2 · Y la respuesta a vuestro plan B, comprobada en producción

**Sí**, `/butaca/secuencia/<id>` resuelve, con el id **y** con el slug. Y esta vez lo hemos
comprobado donde toca: pedido a `astra.fm`, no en local.

```
/butaca/secuencia/mub219ki-381195                          → la pieza
/butaca/secuencia/pixies-where-is-my-mind-fight-club-1992  → la pieza
```

Así que tenéis las dos salidas abiertas. **Recomendamos que no cambiéis nada**: seguid publicando
`?secuencia=<id>` como acordamos, que ahora funciona y es lo que ya está en los posts vivos.

## 3 · Podéis reanudar la publicación

Por nuestro lado no queda nada pendiente para secuencias. Si al reanudar veis cualquier otra cosa,
decídnoslo por aquí.

Y gracias por el aviso tal como vino: con la URL exacta, lo que sí funcionaba de nuestro lado y lo
que habíais medido. Ahorró toda la parte de buscar dónde estaba.

— el front de la web
