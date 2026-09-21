# A la web ← del front · Butaca: ya tenéis ruta, `og:` y `og-check`

21-sep-2026 · de la web · responde a `AVISO_SECUENCIAS_WEB.md` y a
`SECUENCIAS_WEB_INSTRUCCIONES.md` §4

Desplegado hace un momento. Esto desbloquea lo único que os frenaba para publicar secuencias en
Facebook y en Bluesky.

## 1 · La ruta y el parámetro

**El enlace del post**, el que lleva el título legible y el que preferimos:

```
https://astra.fm/butaca/secuencia/<slug>
https://astra.fm/butaca/videoclip/<slug>
```

**Y con el id, que también funciona** y es lo que tenéis siempre a mano:

```
https://astra.fm/butaca?secuencia=<id>
https://astra.fm/butaca?video=<id>
```

⚠️ Las dos rutas de detalle **aceptan el slug y el id indistintamente**, a propósito: el slug sale del
`name` y **cambia si la redacción reescribe el título**, mientras que el id no cambia nunca. Si
publicáis con el id, el post sigue llevando a su sitio aunque después se edite el título.

**Y por eso una petición**: si algún día podéis publicar un `slug` propio por secuencia, como ya prevé
el contrato de TV, lo usamos y dejamos de derivarlo nosotros. No corre prisa.

## 2 · Los `og:` por pieza — resueltos en servidor

`public/butaca-og.php`, con la regla de reescritura para todo `/butaca`. Devuelve el mismo
`index.html` con `og:` y `twitter:` reescritos, así que la aplicación arranca igual y no hay que
distinguir crawlers de personas. Mismo patrón que `tv-og.php`, que lleva funcionando desde el 29-ago.

Lo que pone, con los campos que pedís en el §4.2:

| Meta | De dónde sale |
|---|---|
| `og:title` | `name` |
| `og:description` | `scene`, y si no hay, `description` |
| `og:image` | `thumbnail_url`, tal cual |
| `og:url` | la ruta legible de la pieza |

## 3 · El `og-check`

```
https://astra.fm/butaca-og.php?secuencia=<id>&og-check=1
https://astra.fm/butaca-og.php?video=<id>&og-check=1
```

Misma forma que el de TV: `{listo, encontrado, titulo, imagen, tarjeta}`, más un bloque `servidor` con
el estado del hosting —`curl`, `allow_url_fopen`, si el JSON se descargó y cuántas piezas hay—. Ese
bloque está ahí por experiencia: con TV, un `allow_url_fopen` apagado se veía desde fuera como una
página correcta con las metas del sitio, indistinguible de que la regla del `.htaccess` no funcionara.

🔴 **Comprobadlo con una secuencia real antes de publicar la primera.** Aquí da `listo: true` y tarjeta
grande con vuestros datos de producción, pero **aquí no es vuestro hosting**: la llamada la hacéis
vosotros y conviene ver la respuesta desde ahí.

## 4 · Lo que ya está probado de nuestro lado

Con PHP de verdad y `secuencias.json` de producción:

- `og-check` de una secuencia: `listo: true`, tarjeta **grande**.
- Las metas reescritas con su título, su escena y su miniatura.
- Búsqueda **por id y por slug**, y **los cinco slugs que genera la web los encuentra el PHP** —son
  dos generadores distintos, JavaScript y PHP, y si divergen el enlace compartido sale con el logo
  genérico—.
- Un videoclip: `listo: true`, 44 piezas.
- Una pieza inexistente: **HTTP 200** con las metas del sitio. Nunca un 500.

## 5 · Vuestras otras preguntas

**Los avisos con `pantalla` desconocida no nos rompen nada**, confirmado mirando el código y el feed:
hay 49 avisos, 5 de tipo `secuencia`, y los 5 traen `secuenciaId` en vez de `slug`. Nuestro filtro
descarta los que no traen `slug`, así que hoy los ignora en silencio. Cuando enganchemos la pantalla
leeremos ese `secuenciaId`.

**Un solo fichero en vez de dos**: hoy no nos hace falta. `tv.json` y `secuencias.json` tienen esquemas
distintos y los consumimos por separado sin fricción. Si algún día os simplifica a vosotros, se habla.

**Y una corrección nuestra, por si os sirve**: teníamos la duración de los videoclips descartada en el
código con la nota «el contrato la publica vacía». Ya no es cierto —llega en las 44—, así que la hemos
recuperado. Gracias por mantenerla.

— el front de la web
