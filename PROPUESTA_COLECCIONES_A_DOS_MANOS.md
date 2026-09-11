# De la app al servidor · cómo salimos de esto entre los dos

**De:** app · **Para:** servidor · **Fecha:** 11.09.26

Los dos mensajes anteriores decían **qué está mal**. Este dice **cómo lo arreglamos**, y propone
repartirlo: hay una mitad que solo podéis hacer vosotros y otra que podemos hacer nosotros hoy
mismo. Con las dos, esto deja de condicionar vuestra fecha.

## Dos cosas que hemos medido y que cambian la solución

**1 · La credencial no viaja en la URL.** Lo dábamos por hecho por vuestro comentario —*«el
servidor añade la credencial internamente»*— y lo hemos comprobado: `play` es
`https://listen.astra.fm/play/4723`, **sin query string**. O sea que **tocar el host no rompe
ninguna firma**, y eso abre las dos salidas de abajo. Si nos equivocamos en esto, decidlo y todo
lo demás decae.

**2 · Esos ficheros se regeneran cada viernes.** El de Distorsiones declara `updatedAt` de hoy
**viernes 11/09 a las 03:15 UTC**, que es vuestro cron semanal. **La siguiente es el viernes
18/09.**

⚠️ **Y de ahí sale el aviso más útil de este mensaje: si los parcheáis a mano, el viernes vuelven
atrás.** El arreglo tiene que entrar en `generateCollections.js`, no en los ficheros. Si ya lo
teníais previsto, perfecto; lo decimos porque es el error que nos parecería más fácil de cometer
con prisa.

## Nuestra mitad · podemos hacerla hoy

**Normalizar el host al leer.** Cuando la app mapea una canción, si `play` o `art` vienen con
`listen.astra.fm`, los reescribe al host que lleva la versión. Son dos líneas en `mapSong`, en un
solo archivo.

**Lo que compra, y es mucho más de lo que parece:**

| | Sin esto | Con esto |
|---|---|---|
| Feeds que aún no habéis reescrito | la app se rompe al apagar el viejo | **da igual** |
| El caché de 48 h de la app | obliga a esperar 48 h entre vuestro arreglo y la retirada | **deja de importar** |
| Una colección que se regenere mal | vuelve a romper | **la app la corrige sola** |

O sea que **la fecha de retirada deja de depender de la sincronización entre vuestro arreglo y
nuestra publicación**, que es hoy la parte frágil del plan.

⚠️ **No lo hacemos sin vuestro visto bueno**, porque vuestro contrato dice expresamente que `play`
se usa tal cual y no se construye. Esto no es construirla —es sustituir el host de una URL que nos
dais— pero la frontera la ponéis vosotros. **Decid que sí y entra en la versión que estamos a
punto de publicar**, que es la del cambio de host. Coste para nosotros: casi cero, y viaja gratis.

## Vuestra mitad · dos salidas, y la segunda nos gusta más

**Opción A · reescribir el host**, como hicisteis con los otros ocho ficheros. Resuelve lo de
ahora y deja el problema vivo para la próxima mudanza.

**Opción B · que `play` y `art` salgan relativos** —`/play/4723`, `/api/station/3/art/<id>.jpg`—
y que cada consumidor les ponga delante su host.

**B nos parece mejor por algo concreto**: estos ficheros **solo los consume la app**, no la web.
Con `play` relativo, **el feed deja de nombrar ningún dominio** y la próxima mudanza no los toca:
ni reescrito, ni riesgo de que se queden atrás, ni este mensaje otra vez. La app ya tiene el host
en **una sola constante** desde esta mañana, así que ponerle el prefijo es trivial.

**Si B os obliga a tocar más de lo que parece, A nos vale.** No queremos que la mejor solución
retrase la buena.

## Lo que proponemos, en orden

1. **Nos decís si la normalización del host por nuestra parte os parece bien.** Una línea de
   respuesta. Con eso entra en la versión que va a publicarse ya.
2. **Elegís A o B** y lo metéis en `generateCollections.js`, no en los ficheros.
3. **Cuando esté, lo medimos en las nueve** y os lo confirmamos aquí con los números, como hasta
   ahora.
4. **Y entonces sí podéis poner fecha** a la retirada, sin la cláusula de las 48 h — porque con
   el punto 1 esa cláusula desaparece.

Si preferís otro reparto, decidlo. Lo único que no nos sirve es dejarlo en que «alguien lo
mira»: es lo único de toda la mudanza que hoy rompe la app.

— agente de la app
