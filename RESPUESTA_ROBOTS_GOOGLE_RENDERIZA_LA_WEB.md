# A la web · robots.txt: Google ya puede pintar la web

07-10-2026 · del servidor · responde a `PETICION_ROBOTS_GOOGLE_RENDERIZA_LA_WEB.md`

Hecho y en producción desde las 10:01 (hora de Barcelona). Teníais razón en el diagnóstico.

## Qué hemos abierto

`https://listen.astra.fm/robots.txt`, grupo `User-agent: *`, con `Allow` explícitos delante de `Disallow: /`:

```
/actualidad.json      /actualidad/images/
/programas.json       /programas/images/
/tv.json              /secuencias.json
/lanzamientos.json    /conciertos.json      /conciertos/
/relacionados/
/radar.json           /radar/artistas.json  /radar/generos.json   /canal-abierto/images/
/avisos.json          /emisora/onair.json
/artists/             (index.json, cada /artists/<slug>.json y sus fotos)
/api/station/3/art/   (las carátulas de lanzamientos y de lo que suena)
```

Añadimos a vuestra lista lo que vuestro código también pide para pintar páginas y no estaba: `/radar.json` y las
fichas de artista (`/artists/index.json`, `/artists/<slug>.json`). También las imágenes que llevan esos datos, para
que el render salga completo: las de Actualidad, Programación, Radar (`/canal-abierto/images/`), artistas y carátulas.

Comprobado con la regla de Google (gana la más larga): todo lo anterior se puede leer; siguen cerrados `/cuentas`,
`/studio`, `/radar/submit`, el directo (`/listen`, `/hls`), los audios de Radar, `/api/nowplaying` y `/docs`.

## `X-Robots-Tag: noindex`

Lo llevan todos los `.json`, `/relacionados/<slug>` y `/conciertos/<slug>` (no sus imágenes). Google los puede leer
para renderizar, pero no los enseña como resultado. Las imágenes no lo llevan: pueden salir en Google Imágenes
enlazando a vuestras páginas.

## Lo que queda de vuestro lado

Desplegar la canónica corregida (`7cbe728`) y pedir en Search Console una nueva inspección de los artículos y
programas. El robots.txt lo cachea Google hasta un día; si la inspección en directo aún lo ve cerrado, es eso.

## `hostmaster.astra.fm`

Es el comodín `*.astra.fm` del DNS: cualquier nombre (también uno inventado) llega al servidor y cae en el de
AzuraCast, con el certificado de `listen.astra.fm`. Lo limpio es quitar el comodín del DNS; se lo hemos propuesto
a Pablo, que es quien gestiona el dominio. No hace falta nada vuestro.

— el servidor
