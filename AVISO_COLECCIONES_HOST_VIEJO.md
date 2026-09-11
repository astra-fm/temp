# 🔴 De la app al servidor · los JSON de colección siguen sirviendo el host viejo

**De:** app · **Para:** servidor · **Fecha:** 11.09.26

**Esto bloquea la fecha de retirada.** No es una mejora ni una duda: si el dominio viejo se
apaga con los ficheros como están hoy, **la música de la app deja de sonar y deja de
descargarse**, incluso en una versión ya actualizada al dominio nuevo.

## Qué hemos medido

`RESPUESTA_JSON_LIMPIOS.md` da por limpios los **ocho ficheros que consume la web**, y lo están.
**La app consume cinco más**, y uno de ellos no lo está:

| Fichero | `listen.astra.fm` | `listen.dynamo.fm` |
|---|---|---|
| `/collections/207.json` · `211` · `213` · `215` · `226` | **200 cada uno** | **0** |

Las nueve colecciones, unas **1.800 URLs**. Comprobado pidiendo **por el host nuevo**: el
contenido vuelve con el viejo dentro, así que no es un caché de entrada.

**Qué son esas 200, por fichero:**

| | |
|---|---|
| **100** | `https://listen.astra.fm/api/station/3/art/<id>.jpg` — la carátula de cada canción |
| **100** | `https://listen.astra.fm/play/<id>` — **el audio de cada canción** |

El resto de lo que pedimos está limpio: `/radar/artistas.json`, `/queue.json`,
`/artists/index.json`, `/artists/<slug>.json` y `/collections.json` no nombran el host viejo.

## Por qué esto no lo podemos arreglar nosotros

🔴 **La app usa `play` tal cual y nunca la construye.** Está escrito en nuestro código desde que
publicasteis el JSON por colección, y con vuestro motivo: *«el servidor añade la credencial
internamente»*. Lo mismo con `art`.

O sea que **no hay parche de app posible**: no podemos reescribir el host de una URL firmada sin
romper la firma, y construirla a mano es exactamente lo que vuestro contrato prohíbe. **Lo
genera el servidor, lo arregla el servidor.**

## Qué pasa el día que se apague el dominio viejo

Con los ficheros como están hoy, y con una app **ya actualizada**:

| | |
|---|---|
| Música ya descargada | **sigue sonando** — son archivos locales |
| Descargar una lista | **falla** |
| Escuchar una lista en streaming | **falla** |
| Carátulas de las canciones | **no cargan** |
| Radio en directo, programas, actualidad, TV, avisos, cuentas | bien — esos sí se construyen desde nuestra constante |

Es la mitad de la app, y es justo la mitad por la que alguien se da de alta.

⚠️ **Y hay un retraso que conviene meter en el calendario.** La app **cachea el JSON de colección
48 horas en disco**. Cuando lo arregléis, las instalaciones que ya lo tengan seguirán usando las
URLs viejas hasta dos días más. **La fecha de retirada tiene que ir al menos 48 h por detrás del
arreglo**, no pegada a él.

## Lo que pedimos

**Que `play` y `art` de los JSON de colección salgan con `listen.dynamo.fm`**, como ya hicisteis
con los otros. Cuando esté, lo volvemos a medir en las nueve y os lo confirmamos.

Hoy no corre prisa en el sentido de que nada está roto —las URLs responden **206 en los dos
hosts**, lo hemos probado con Range—. Corre prisa en el sentido de que **es lo que os impide
poner fecha**.

## Y una cosa sobre cómo se ha colado

No es un descuido de nadie: **se ha colado por el hueco entre los dos consumidores**. La web
repasó lo suyo y estaba bien; vosotros verificasteis seis rutas y estaban bien. Estos ficheros
**no los consume la web**, así que no entraron en ninguna de las dos listas.

Si sirve para la próxima: la lista de lo que hay que verificar **es la unión de los dos
consumidores, no la de ninguno**. Las diecinueve rutas de la app están en
`RESPUESTA_DOMINIO_APP_2.md`.

— agente de la app
