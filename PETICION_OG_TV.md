# Petición de la web al dashboard: comprobar la tarjeta OG antes de publicar en redes

> **Estado (29-ago-2026): IMPLEMENTADO.**
> - El dashboard (`studio/tv.html`) comprueba `tv-og.php?video=<id>&og-check=1` **a través
>   del microservicio** (`GET /api/tv/:id/og-check` — tv-og.php no envía CORS para
>   listen.astra.fm, así que se consulta servidor a servidor) antes de publicar en Facebook,
>   con hasta 6 reintentos (~18 s) mientras la caché de la web no vea el vídeo.
>   `listo:false+encontrado:false` → no publica y avisa; `encontrado:true+listo:false` →
>   pide rellenar la portada; `tarjeta:"pequeña"` → confirm para publicar así o elegir
>   maxresdefault; respuesta sin `listo` (versión vieja de tv-og.php) o sin respuesta →
>   confirm explícito. Si el gate frena, no se publica en NINGUNA red (evita duplicar
>   Bluesky en el reintento).
> - **Pre-scrape server-side**: antes del post a FB, el microservicio hace
>   `POST graph.facebook.com/v24.0/?id=<url>&scrape=true` con token de app
>   (facebookAppId|facebookAppSecret de config.js), mejor esfuerzo.
> - **Aviso de id**: al eliminar un vídeo con posts publicados, el confirm avisa de que un
>   vídeo recreado tendrá id nuevo y los posts dejarán de apuntar a él.
> - Bluesky no pasa por el gate por sí mismo: su tarjeta la construye nuestro servidor.

A continuación, el contrato original escrito por el agente de la web (29-ago-2026):

---

## El problema

Cuando se publica un vídeo en Facebook, la tarjeta del post se construye con los
metadatos Open Graph de `astra.fm/tv?video=<id>`. Eso ya funciona: la web sirve
el fotograma del vídeo (§6.2 del contrato de TV, resuelto el 29.08.26).

Pero hay una ventana en la que sale mal: **si el vídeo todavía no está en
`tv.json` cuando Facebook lee la página**, la web no tiene de dónde sacar el
fotograma y devuelve la imagen genérica del sitio. Y el daño es permanente,
porque **Facebook fija la tarjeta al publicar**: no se arregla luego, hay que
borrar el post y volver a publicarlo.

## Lo que piden

Comprobar antes de publicar:

```
GET https://astra.fm/tv-og.php?video=<id>&og-check=1
```

| respuesta | qué significa | qué hacer |
| --- | --- | --- |
| `listo: true`, `tarjeta: "grande"` | saldrá el fotograma a tamaño completo | publicar |
| `listo: true`, `tarjeta: "pequeña"` | fotograma en tarjeta reducida (portada 480×360) | publicable; mejor elegir portada `maxresdefault` |
| `listo: false`, `encontrado: false` | ese id todavía no está en `tv.json` (caché de la web) | **no publicar aún**: esperar y repetir |
| `listo: false`, `encontrado: true` | el vídeo está pero sin portada | rellenar `thumbnail_url` en el dashboard |

El bloque `servidor` de la respuesta es diagnóstico de la web (mandárselo tal
cual si algo no cuadra).

**Pre-scrape recomendado** (solo desde servidor, necesita token de app):

```
POST https://graph.facebook.com/v21.0/?id=<url>&scrape=true&access_token=<token>
```

**Aviso**: al borrar y recrear un vídeo su `id` cambia (verificado 29-08:
«Interpol - Toni» pasó de `mte0n4jw-1144e1` a `mte0zrl4-d03fc9`); los posts ya
publicados quedan apuntando al id viejo (la web no se rompe: abre la sección y
enseña el más reciente).
