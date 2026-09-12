# Al servidor · la web está preparada para volver a Astra FM. Vuestro turno

12-sep-2026 · del front de la web · responde a `DECISION_VUELTA_WEB.md`

**Paso 1 de vuestra secuencia hecho: preparado y sin publicar.** El despliegue está compilado y
verificado en local; no hemos tocado producción y no la tocamos hasta que nos digáis.

## Vuestros cuatro puntos

**1 · La constante del host, de vuelta.** `LISTEN = "https://listen.astra.fm"`. Y teníais razón en
lo que decíais: fue **una línea**. Ese trabajo de ayer se pagó solo al día siguiente.

**2 · Canónico, sitemap, robots y sociales**, de vuelta al dominio anterior. Comprobado en el
compilado: `dist/index.html` sale con `astra.fm` en el canónico y `www.astra.fm` en las cuatro
etiquetas de Open Graph y Twitter; `sitemap.xml` y `robots.txt`, cero menciones al dominio nuevo.

**3 · 🔴 El bloque del service worker: invertido, no borrado.** Como sugeríais. La lista de dominios
retirados decía `astra.fm` y ahora dice `dynamo.fm`. Verificado en el fichero compilado, que es el
que se sirve:

```
const DOMINIOS_RETIRADOS = ['dynamo.fm', 'www.dynamo.fm'];
```

Gracias por avisarlo dos veces. Era exactamente el que se olvida: no lo ve el compilador, no lo ve
el navegador en local, y el síntoma habría sido intermitente.

**Un matiz que sí hemos dejado**: el service worker sigue cacheando audio de **los dos** hosts,
`listen.astra.fm` y `listen.dynamo.fm`. Puede haber pestañas abiertas con el paquete de ayer
pidiendo por el host nuevo, y si quitáramos ese, se quedarían sin buffer. No estorba y desaparece
solo cuando esas pestañas se recarguen.

**4 · Preparado y avisando. No publicamos.** Quedamos a la espera de vuestros pasos 2 y 3.

## Lo que además vuelve, por encargo de Pablo

**La marca entera**: la palabra y el símbolo de la cabecera, el rótulo de la pestaña, el logotipo y
los títulos de `/about`. No queda nada diciendo Dynamo en lo que se ve.

Los dos componentes del dibujo se quedan en el repositorio **sin usar y anotados**, porque esta
decisión ya ha ido y vuelto una vez.

## ⚠️ Y un hallazgo que no es del cambio, pero sale con él

Al medir la cabecera ya revertida, **el último destino de la navegación se corta en una franja
estrecha de anchos**. Medido en el navegador, con el programa que sonaba al medir —«Haciendo Cosas
Raras · 08–12»—:

| ancho | qué pasa |
|---|---|
| 1439 y por debajo | cabe de sobra: los botones de tienda ya han salido y la navegación gana 256 px |
| **1441 a 1452** | **«Sobre Astra FM» se sale 12,25 px y lo recorta el borde** |
| 1455 y por encima | cabe |

**No lo provoca la vuelta**, y por eso os lo contamos en vez de arreglarlo: el rótulo «Sobre Astra
FM» es el que había antes del lunes, y la celda que se come el sitio es la del **nombre del programa
en emisión**, que crece con lo que esté sonando. Con un nombre corto no pasa; con este, sí.

Va a diseño, que ya tiene anotada la salida —que los botones de tienda salgan a un ancho mayor—. Lo
decimos aquí solo para que no os llegue como una regresión del despliegue.

## Estado

Rama preparada, compilada y verificada. **Esperando vuestro paso 2.**

— el front de la web
