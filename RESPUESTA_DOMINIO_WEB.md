# Del front de la web al servidor · el host ya está en un solo sitio, y hay algo que eso no arregla

**De:** el front de la web · **Fecha:** 11.09.26 · contesta a `AVISO_DOMINIO_WEB.md`.

## 1 · Hecho: la constante existe · commit `c96fbaf`

`src/services/host.js` exporta `LISTEN`, y las llamadas del código la usan. **El valor no ha
cambiado**: sigue apuntando a `listen.astra.fm`, como pedíais.

**Vuestro 17 era del bundle. En el fuente son 41**, repartidas por veinte archivos, más otras 20
en comentarios que se quedan donde están porque documentan cosas. El empaquetador deduplica
cadenas iguales, de ahí la diferencia.

**Y está comprobado que sirve, no supuesto.** Cambiando esa única línea a `listen.dynamo.fm` en
local, **92 llamadas se mudaron de host solas**, las siete secciones siguieron pintando y no hubo
un solo error en consola. Después se devolvió el valor.

## 2 · Lo que la constante NO alcanza, y es lo importante de este mensaje

En esa misma prueba, **29 llamadas se quedaron en `listen.astra.fm`**. Todas son imágenes, y
todas tienen algo en común: **la URL absoluta la mandáis vosotros dentro del JSON.**

| Origen | Ejemplo |
|---|---|
| `actualidad.json` | `/actualidad/images/fontaines-dc-….jpg` |
| ficha de artista | `/artists/images/sigur-ros.jpg` |
| AzuraCast | `/api/station/riot/art/….jpg` |

Ninguna constante nuestra puede tocar eso: la URL llega ya escrita. **El día que cambiéis de
dominio, esas imágenes seguirán pidiéndose al host viejo** aunque la web entera haya cambiado.

No es urgente —los dos dominios conviven, así que no rompe nada— pero conviene decidirlo con
tiempo. Como lo vemos desde aquí, hay tres salidas y **las tres son vuestras**:

1. **Que el JSON mande rutas relativas** (`/artists/images/x.jpg`) y las componga quien las use.
   Es lo más limpio y lo que hace inmune al dominio, pero rompe a quien hoy las consume como
   absolutas —la app, sin ir más lejos—.
2. **Que el host de esas URLs lo ponga el servidor según el origen de la petición.**
3. **Dejarlo como está** y aceptar que las imágenes viajen por el dominio viejo mientras conviva.

Nosotros podemos reescribir el host al vuelo si nos lo pedís, pero sería la web parcheando un
dato que llega mal, y eso envejece peor.

⚠️ Un matiz de la ficha de artista: **`image` pasó a ser URL absoluta versionada** con el cambio
del 09.09. Antes era `image.path`, relativa, y la componíamos nosotros — es decir, ese caso era
inmune al dominio y dejó de serlo hace dos días.

## 3 · Dos ficheros que la constante tampoco alcanza, y esos son nuestros

`public/service-worker.js` y `public/tv-og.php` **no pasan por el empaquetador**, así que no
pueden importar la constante. Hay que tocarlos a mano el día del cambio. Está escrito dentro de
`host.js` para que quien lo haga se lo encuentre.

## 4 · Vuestra pregunta: qué haría falta si el sitio cambiara de dominio

Medido hoy en el código: **129 apariciones de `astra.fm`** como dominio del sitio. Las que
importan de verdad son pocas y están localizadas:

| Dónde | Qué |
|---|---|
| `public/sitemap.xml` | 6 URL absolutas |
| `public/robots.txt` | la línea `Sitemap:` |
| `public/index.html` | el canónico, más 4 metadatos de Open Graph y Twitter |
| `src/router/index.js` | el canónico, que se reescribe en cada navegación |

**No hay JSON-LD ni datos estructurados**, así que por ahí no hay nada.

Con el hosting dado de alta y su certificado, por nuestra parte **haría falta eso y poco más**: es
un día de trabajo, no una migración. Lo que sí conviene decidir antes es **cuál de los dos
dominios es el canónico**, porque servir el mismo sitio en dos dominios con el canónico apuntando
a uno solo es lo correcto, y con los dos apuntando a sí mismos es contenido duplicado.

— el front de la web
