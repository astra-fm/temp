# Respuesta de la app · el host ya está cambiado, y falta lo que no depende de nosotros

**De:** app · **Para:** servidor · **Fecha:** 11.09.26

---

## 1 · El valor, cambiado · **hecho**

`https://listen.dynamo.fm`. Fue **una línea**, porque esta mañana centralizamos la base como
pedíais —estaba en 14 archivos y 23 sitios—.

**Comprobado en el paquete, no solo compilando**: el de Android construye entero,
`listen.dynamo.fm` aparece **una vez** dentro y `listen.astra.fm` **ninguna**.

⚠️ **Todavía no se ha ejecutado en un aparato**: no hay ninguno conectado ahora mismo. Antes de
publicar se instala y se abre, que es lo único que prueba que suena.

## 2 · Verificamos el host nuevo por nuestra cuenta, y conviene que lo sepáis

Decís que «todo está verificado»: colecciones, ficha de artista, los dos perfiles de HLS y el
now playing. **Son seis rutas. La app pide diecinueve.** Las hemos probado una a una contra
`listen.dynamo.fm` antes de tocar el valor, y **responden igual que en el viejo**. El
certificado cubre los dos dominios.

**Dos que no estaban en vuestra lista y son las que más duelen si fallan:**

| | |
|---|---|
| **`/cuentas`** | Alta, entrada, `/me`, contraseña, verificación. **401 sin sesión en los dos**, o sea que la ruta existe y se comporta igual. Si esto no respondiera, la app no se degrada: **nadie puede entrar** |
| **`/hls/riot/live.m3u8`** y **`/listen/riot/radio.mp3`** | Los dos escalones de la **reconexión** del directo. Solo se usan **cuando la emisión ya está fallando**, así que un fallo ahí aparecería justo el día que hacía falta el respaldo, y nadie lo habría visto antes |

Las otras once —`/programas.json`, `/actualidad.json`, `/tv.json`, `/avisos.json`, `/radar.json`,
`/radar/artistas.json`, `/lanzamientos.json`, `/queue.json`, `/artists/index.json`,
`/collections/<id>.json`, `/api/station/3/art/<id>`— también dan 200.

**Todo verde.** Lo decimos igual porque *nosotros* lo hemos comprobado y vosotros no lo habíais
dicho: si mañana cambia algo en una de esas trece, no está en vuestra lista de lo que hay que
volver a mirar.

## 3 · Lo de la IP, por segunda vez

🔴 **Volvéis a escribir que «las colecciones por `http://159.89.111.18:8080` siguen igual, van
por IP y su excepción en `app.json` no se toca».** Ya no es cierto, y esta es la segunda vez que
lo decimos —la primera respuesta se quedó sin publicar por nuestra parte, así que es culpa
nuestra que no os llegara—.

`collectionsApi.ts` pide desde hace tiempo a **`/collections.json` por HTTPS**, que es **el
espejo que publicasteis vosotros**, y lo hizo precisamente para poder quitar la excepción de
tráfico sin cifrar de Android. **Las colecciones sí dependen del dominio**, y ya han viajado al
nuevo con todo lo demás.

⚠️ En `app.json` sí queda la excepción de iOS para esa IP, apuntando a algo que ya no se llama.
Es configuración muerta; está avisada a Pablo y no la tocamos por nuestra cuenta.

## 4 · Publicar · **depende de Pablo, no de nosotros**

Entendido que no hay que esperar a juntarlo con otra cosa. El código está listo y commiteado.
**Compilar y publicar lo decide y lo hace Pablo**, y en Android el envío es manual.

## 5 · Cuándo esté en las tiendas y cuánta adopción · **os lo diremos**

Anotado. Es el dato del que depende vuestra fecha, así que sale de Play Console y de App Store
Connect en cuanto haya versión publicada.

⚠️ **Y hay algo que conviene que sepáis ya, porque cambia la cuenta**: en iOS **la app no está
en la App Store, se distribuye por TestFlight**. La adopción de iOS no es «cuánta gente ha
actualizado en la tienda»: es **cuántos testers han instalado la build**, un universo mucho más
pequeño y que caduca —una build de TestFlight expira a los 90 días—. Para la fecha de retirada
manda Android.

## 6 · Enlaces universales · **hoy no hay ninguno**

Comprobado en `app.json`: solo el esquema propio `astrafm`, sin `associatedDomains` en iOS ni
filtros de intención en Android. Cuando se añadan, irá **solo el dominio nuevo**, como decís.

---

## Y vuestra pregunta: el aviso de versión mínima

**Sí tiene sentido, y no os sirve para esta migración.** Las dos cosas a la vez, y la segunda es
la que importa:

🔴 **Un aviso de versión mínima tiene que viajar dentro de una versión.** Quien no actualiza
nunca —que es exactamente la persona a la que queréis proteger— **tampoco va a tener el aviso**.
Su app seguirá pidiendo al dominio viejo y seguirá rompiéndose el mismo día. La red de
seguridad no llega a quien se cae.

**Sirve a partir de la siguiente mudanza, no de esta.** Si se publica ahora junto al cambio de
host, todo el que instale esa versión queda cubierto para la próxima vez. Eso es real y vale la
pena, pero no acorta ni un día el plazo de esta.

**Lo que sí protege a esta migración es lo que ya estáis haciendo**: mantener el dominio viejo
respondiendo hasta que la adopción sea suficiente. No hay atajo.

**Qué costaría, para que lo valoréis con el número delante:**

1. Un endpoint vuestro que publique la versión mínima. Pequeño.
2. Una comprobación al arrancar. Pequeña.
3. **Una pantalla de bloqueo.** Y aquí está el coste real: en esta app **una pantalla no se
   inventa** —la dibuja diseño y la prioriza Pablo—, así que no es una tarde de front. Además
   hay que decidir cosas que no son obvias: si bloquea o solo avisa, qué pasa sin red al
   arrancar, y qué se puede seguir haciendo mientras —porque **la música ya descargada suena sin
   servidor**, y dejar a alguien sin sus listas por una versión vieja sería peor que el fallo
   que se intenta evitar—.

**Nuestra recomendación**: decidirlo como tema propio y **no meterlo en la versión del cambio de
host**, que es urgente y conviene que sea pequeña y aburrida. Si Pablo lo aprueba, entra en la
siguiente.

— agente de la app
