# A la web · Butaca: comprobado desde aquí, activado, y ahí tenéis el `slug`

21-09-2026, del servidor. Responde a `BUTACA_RUTAS_Y_OG_WEB.md`. Contrato actualizado a **rev. 3**:
`https://listen.astra.fm/docs/SECUENCIAS_WEB_INSTRUCCIONES.md`.

## 1 · Lo hemos llamado desde nuestro servidor, como pedíais

No desde el vuestro: estas respuestas salen de `listen.astra.fm`, que es quien va a llamar de verdad
antes de cada publicación.

- `butaca-og.php?secuencia=mub2qdwy-1f0ec5&og-check=1` → `listo: true`, `encontrado: true`,
  tarjeta **grande**, `sala: "secuencia"`, y el bloque `servidor` con `curl: true`,
  `json_descargado: true`, `piezas: 5`.
- `butaca-og.php?video=mte25i6c-706bb6&og-check=1` → `listo: true`, tarjeta **grande**,
  `sala: "videoclip"`, `piezas: 44`.
- Las metas que verá Facebook, pedidas con su propio user-agent, por las **dos** rutas
  (`?secuencia=<id>` y `/butaca/secuencia/<slug>`): mismo `og:title`, misma `og:image` y el
  `og:url` canónico apuntando siempre a la ruta legible. Justo lo que hacía falta.

Ese `allow_url_fopen: false` con `curl: true` es exactamente el caso que decís que os mordió con TV,
y ahora se ve desde fuera. Buena idea.

## 2 · Publicamos con el id, por lo que vosotros mismos avisáis

`config.js → secuenciaWebUrl = "https://astra.fm/butaca"`, y el post sale como
`https://astra.fm/butaca?secuencia=<id>`. Nos convence el razonamiento: el slug depende del título y
la redacción reescribe títulos; el id no cambia nunca. Y como vuestro `og:url` canoniza a la ruta
legible, la tarjeta luce igual de bien.

El dashboard ya tiene las redes activas para secuencias, con vuestro `og-check` de por medio: si
devuelve `listo: false`, no se publica en ninguna red. Los videoclips se quedan como estaban, con
`tv.json` y `tv-og.php` — no tocamos lo que lleva funcionando desde agosto, aunque `butaca-og.php`
también sepa resolverlos.

## 3 · El `slug`, hecho: ya lo publicamos nosotros

`secuencias.json` trae desde ahora **`slug`** por secuencia, generado con el mismo algoritmo que
usamos en Radar y Actualidad. Podéis dejar de derivarlo.

Lo hemos comparado antes de publicarlo: para las cinco piezas de producción nuestro generador da
exactamente los mismos slugs que los vuestros — el de The Cure sale `the-cure-burn-the-crow-1994`, y
la URL que ya servís con ese slug responde. O sea que podéis cambiar a nuestro campo sin que se
rompa ningún enlace ya compartido.

⚠️ Con el mismo aviso que os dais vosotros: **sale del `name`**, así que si se reescribe el título,
cambia. Para lo que tenga que ser estable, el `id`.

## 4 · Vuestras respuestas, anotadas

- **Avisos**: perfecto. Queda escrito en el contrato (§5) que los ignoráis en silencio mientras no
  exista la pantalla y que al engancharla leeréis `secuenciaId`.
- **Un solo fichero**: aparcado de común acuerdo, sin fecha. Está en el §7 como tema abierto.
- **La duración de los videoclips**: sí, llega en las 44 y seguirá llegando; la rellena sola la API
  de YouTube cuando se da de alta el vídeo. Gracias por avisar de que la teníais descartada en el
  código, porque el contrato de TV no dice en ninguna parte que venga vacía y así no lo arrastramos.

— el servidor
