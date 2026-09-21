# Al servidor · lo de anoche: el camino está sano, y ya se podrá ver qué falla

21-sep-2026 · de la web · respuesta a `ALTAS_SIGUEN_FALLANDO_WEB.md`

Vamos por partes, empezando por lo que hemos podido comprobar y no suponer.

## 1 · El camino de producción está sano ahora mismo

Hemos hecho **un alta real contra producción** desde un navegador limpio, con una cuenta que nos ha
autorizado Pablo para pruebas, y **con este navegador excluido de la analítica** (`?no-medir=1`) para
no ensuciaros el embudo que estáis investigando. Resultado:

```
OPTIONS /cuentas/register   →  204   (directa, sin service worker)
POST    /cuentas/register   →  201   (directa, sin service worker)
GET     /cuentas/me         →  200
```

Sesión creada y área abierta. O sea: **CORS, preflight, alta y verificación funcionan**, y el service
worker ya no está en medio —lo confirma el propio navegador: las respuestas no vienen de él—.

Confirmado también lo que decís en vuestra comprobación: el `service-worker.js` publicado lleva el
arreglo.

## 2 · Vuestra pregunta del service worker viejo: teníais razón en preguntarlo

**El relevo ya estaba resuelto, pero el aviso no.** El nuestro hace `skipWaiting()` al instalarse y
`clients.claim()` al activarse, así que en cuanto el navegador descubre uno nuevo, toma el mando sin
esperar a que se cierren pestañas.

El problema es **cuándo lo descubre**: el navegador solo busca versión nueva al **cargar un
documento**, y esta web es de una sola página. Moverse por ella no carga nada. Una pestaña abierta
desde antes de las 11:48 podía seguir con el de la mañana sin enterarse.

**Arreglado hoy**: la web pregunta por una versión nueva **al volver a la pestaña**, con freno de diez
minutos. Con eso, el plazo máximo para que alguien siga con el viejo pasa de «hasta que recargue» a
«hasta que vuelva a mirar la pestaña».

Así que sí: **esa sesión de las 23:13 pudo estar con el viejo**, y no podemos descartarlo ni
confirmarlo, porque de aquella versión el error no dejaba rastro.

## 3 · Vuestro §1: no, no podemos recuperar esa sesión

Seamos claros para no haceros perder tiempo: **la web no tiene registro propio de errores**. Lo que
os dijimos ayer —«lo apuntamos con `console.error`»— solo sirve si alguien nos presta su navegador, y
la consola de quien no consigue darse de alta no la vamos a ver nunca. Fue una respuesta pobre por
nuestra parte.

**Lo hemos corregido donde sí lo vais a ver vosotros:** `alta_error` lleva desde hoy una propiedad
más, `detalle`:

| `detalle` | Qué significa |
|---|---|
| `sin_respuesta` | La petición ni llegó a contestar: red, CORS, un intermediario que la corta. **No llegó a vosotros.** |
| `http_502`, `http_404`… | Contestó algo que no esperábamos. El código es el que vino. |

⚠️ **Los cuatro valores de `motivo` no cambian** —`correo_registrado`, `limite`, `datos`, `otro`—, así
que vuestro embudo y vuestra tabla siguen igual. `detalle` va al lado, y solo dice códigos: ni correo,
ni nombre, ni nada de la persona.

Con esto, **el próximo `otro` viene con apellido**, y cruzándolo con vuestro registro nuevo de intentos
se cierra solo: si vosotros tenéis línea y nosotros `http_XXX`, es respuesta vuestra; si vosotros no
tenéis línea y nosotros `sin_respuesta`, murió por el camino.

## 4 · Qué versión estaba servida a las 23:13

La de ayer a las 11:48, la del arreglo. Lo que no sabemos es **cuál estaba corriendo en ese
navegador**, que es distinto, y es justo lo del punto 2.

## Sobre las cifras

Lo del embudo lo hemos leído bien: **11 aperturas, 8 eligiendo tipo y 1 cuenta en 30 días**. Con esa
proporción, cada fallo pesa. No lo tratamos como ruido.

## Desplegado

21-sep-2026. `detalle` y el relevo del service worker ya están en producción.

A partir de ahora, cada `alta_error` que os llegue con `motivo: otro` trae su `detalle` al lado. En
cuanto veáis el primero, cruzadlo con vuestro registro de intentos y nos decís: con eso se cierra.

— el front de la web
