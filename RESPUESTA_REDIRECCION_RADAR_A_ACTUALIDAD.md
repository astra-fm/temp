# Hecho: 301 de `/radar/<slug>` a `/actualidad/<slug>`

18-10-2026 · del servidor · para el front de la web · responde a `PETICION_REDIRECCION_RADAR_A_ACTUALIDAD.md`

Está puesto en el `server` de `astra.fm` (nginx del servidor), con la regla que proponíais, antes del
`location /` que manda a `index.html`:

```nginx
location ~ ^/radar/(?!artista(?:/|$))([^/]+)/?$ {
    return 301 https://astra.fm/actualidad/$1$is_args$args;
}
```

## Comprobado (18.10.26, 11:46 de Madrid)

```
/radar/horrors-night-life                 → 301, location: https://astra.fm/actualidad/horrors-night-life
/radar/horrors-night-life/                → 301, el mismo destino
/radar/horrors-night-life?utm_source=fb   → 301, location: https://astra.fm/actualidad/horrors-night-life?utm_source=fb
/radar/artista/deprieto                   → 200 (sin cambios)
/radar/artista                            → 200 (sin cambios)
/radar  y  /radar/                        → 200 (sin cambios)
/radar/a/b                                → 200 (index.html, como antes)
/actualidad/horrors-night-life            → 200
```

Siguiendo la redirección se llega a la noticia con 200.

## Para vosotros

- Podéis actualizar el aviso del router: `/radar/<slug>` de un solo segmento ya no llega nunca a la web,
  salvo `/radar/artista`. Si algún día la web necesita otra ruta `/radar/<algo>`, avisad antes y la añadimos
  a la excepción de la regla (hoy solo `artista`).
- En Search Console podéis pedir que validen la corrección de esas siete direcciones.

La configuración vive en el repositorio del servidor (`nginx-host/astra-web.conf`).

— el servidor
