# Al servidor · el robots.txt impide a Google renderizar la web

07-10-2026 · del front de la web · lo pide Pablo · sigue a `RESPUESTA_ROBOTS_Y_CANAL.md`

**De:** el front de la web · **Fecha:** 07.10.26

## Qué pasa

Search Console no indexa los artículos ni los programas de `astra.fm`. En la inspección de
`https://astra.fm/actualidad/gum-convierte-la-ansiedad-digital-en-paisaje-sonoro-en-celluloid`
(último rastreo 02.10.26), Google elige como canónica `https://astra.fm/legal/cookies`: ha visto
el artículo vacío y lo ha agrupado con otra página casi sin contenido. Con `/shows/ruido` pasa
lo mismo.

La causa principal está en `https://listen.astra.fm/robots.txt` (leído hoy, 07.10.26):

```
User-agent: *
Disallow: /
Allow: /public/
```

La web es una SPA: el contenido lo pide el navegador a `listen.astra.fm`. Googlebot renderiza la
página igual que un navegador, pero **respeta el robots.txt también para esas peticiones**. Al
pedir `actualidad.json` lo tiene prohibido, así que renderiza la página sin artículo.

Había un segundo fallo, de nuestro lado: `index.html` llevaba una canónica fija a la portada.
Ya está corregido en `develop` (`7cbe728`) y sale con el próximo despliegue. Sin vuestro cambio
no basta.

## Lo que pedimos

**No es abrir `listen.astra.fm` a los buscadores**: entendemos que lo queréis fuera, y sigue
fuera. Es dejar que el robot lea las rutas públicas de lectura con las que se pinta la web, que
son estas:

```
/actualidad.json
/programas.json
/tv.json
/secuencias.json
/lanzamientos.json
/conciertos.json
/conciertos/<slug>
/relacionados/<slug>
/radar/artistas.json
/radar/generos.json
/avisos.json
/emisora/onair.json
```

Con `Allow` explícitos en el robots.txt que servís desde nginx: Google aplica la regla más
específica, así que `Allow: /actualidad.json` gana a `Disallow: /`. Si además queréis que esos
JSON no salgan nunca como resultado de búsqueda, una cabecera `X-Robots-Tag: noindex` en ellos lo
impide sin estorbar el renderizado.

**`/cuentas` y todo lo privado, cerrado como está.**

Si alguna de estas rutas se ha movido o falta alguna, la lista correcta es la vuestra. Esta sale
de lo que pide el código de la web hoy.

## Y una menor: `hostmaster.astra.fm`

Search Console aún guarda `https://hostmaster.astra.fm/radio` y `/flux/tv`. Ese nombre resuelve
a `159.89.111.18`, pero por https presenta el certificado de `listen.astra.fm` (no coincide) y
por http devuelve 404. Lo limpio es quitarlo del DNS o redirigirlo con un 301 a `astra.fm` con un
certificado válido. No corre prisa: Google acabará descartándolas.

`www.astra.fm` está bien: redirige con 301 a `astra.fm`.

— el front de la web
