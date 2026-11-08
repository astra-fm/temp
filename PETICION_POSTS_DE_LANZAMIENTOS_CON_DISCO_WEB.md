# Petición · Cada post de Lanzamientos con su disco, y el título del vídeo

**De:** el front de la web · **Para:** servidor · **Fecha:** 08.11.26 · Medido contra `actualidad.json` el 08.11.26.

## Para qué

Desde hoy la web pinta los posts de tipo `lanzamientos` con plantilla propia (diseño, `Template/Articulo-Reset ·
lanzamiento`, aprobado por Pablo el 08.11.26). Bajo el titular va la **ficha del disco**: portada, tipo, canciones,
fecha, disco, artista, Spotify, compra y «Ver el disco». Todo sale de `discos/<discoSlug>.json`. Sin `discoSlug` la
ficha no se pinta, y el post se queda sin disco.

## 1 · `discoSlug` en todos los posts de Lanzamientos

De 94 posts `lanzamientos` publicados, **20 no traen `discoSlug`**:

| Fecha | Post | `artistSlug` |
|---|---|---|
| 2026-11-03 | `tom-morello-reune-sus-distintas-etapas-en-everyone-gets-everything-they-want` | — |
| 2026-09-19 | `liars-convierte-la-contaminacion-de-kalamazoo-en-tension-industrial` | `liars` |
| 2026-08-22 | `then-comes-silence-afronta-su-propio-judgment-day` | `then-comes-silence` |
| 2026-08-12 | `sr-chinarro-y-los-deseos-que-todava-quedan-1786519653` | `sr-chinarro` |
| 2026-08-12 | `blonde-redhead-entre-la-prdida-el-ruido-y-la-permanencia` | `blonde-redhead` |
| 2026-05-24 | `korn-retoma-su-actividad-discogrfica-con-reward-the-scars` | `korn` |
| 2026-04-09 | `osees-y-su-identidad-cambiante-aterrizan-en-cara-maluco` | `osees` |
| 2026-04-04 | `nuevo-ep-de-u2-con-el-nombre-de-easter-lily` | `u2` |
| 2026-03-31 | `flea-se-reinventa-desde-el-jazz-en-honora` | `flea` |
| 2026-03-26 | `iceage-reactivan-su-pulso-con-star-y-abren-una-nueva-fase-sin-perder-tensin` | `iceage` |
| 2026-03-13 | `the-notwist-adelantan-nueva-etapa-con-el-single-x-ray` | `the-notwist` |
| 2026-03-06 | `satriani-y-vai-unen-fuerzas-en-satchvai-virtuosismo-qumica-y-rock-instrumental` | `joe-satriani` |
| 2026-03-06 | `nine-inch-nails-expande-el-universo-de-tron-con-divergence` | `nine-inch-nails` |
| 2026-02-24 | `clawfinger-before-we-all-die` | `clawfinger` |
| 2026-02-03 | `violet-grohl-y-un-ep-breve-directo-y-sin-herencias-forzadas` | `violet-grohl` |
| 2026-02-03 | `savages-cuando-la-furia-aprende-a-respirar` | `savages` |
| 2026-01-23 | `arctic-monkeys-regresan-tras-cuatro-aos-y-nos-traen-nuevo-single` | `arctic-monkeys` |
| 2026-01-05 | `apparat-an-echo-skips-a-name` | `apparat` |
| 2026-01-02 | `mindtality-chroma-link` | — |
| 2025-12-07 | `oneohtrix-point-never-tranquilizer` | `oneohtrix-point-never` |

Y uno lo trae, pero su ficha da **404**: `crippled-black-phoenix--colder-and-colder` (post `crippled-black-phoenix-presenta-colder-and-colder-adelanto-de-su-nuevo-lbum-sceaduhelm`). Los otros 71 dan 200.

**Lo que pedimos:** que todo post de Lanzamientos publicado traiga un `discoSlug` con su `discos/<slug>.json`. Si para
alguno de estos el disco no tiene ficha, que se la haga el servidor. Si alguno no es de un disco, decídnoslo y lo
dejamos sin ficha.

## 2 · El título del vídeo (opcional)

El diseño pone bajo el vídeo del post su título, por ejemplo «KINGS OF LEON — «MY WHOLE WORLD» · VÍDEO OFICIAL». No
llega en el post: hoy la web lo pide al oEmbed de YouTube y pinta lo que devuelve («Kings of Leon - My Whole World»),
con el formato de cada canal. Si el Studio puede guardar un título para el vídeo, nos vale un campo opcional, por
ejemplo `videoTitulo` (texto), y la web lo prefiere al de YouTube. Sin él seguimos como ahora.

## Lo que hace la web mientras

Sin `discoSlug` o con 404, el post va sin ficha: titular, texto con la foto del artista y vídeo. Nada se rompe; en
cuanto llegue el campo, la ficha aparece sola.

— el front de la web
