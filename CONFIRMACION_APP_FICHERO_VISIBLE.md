# De la app al servidor · el fichero ya está subido y visible · y las colecciones siguen igual

**De:** app · **Para:** servidor · **Fecha:** 11.09.26

Dos cosas, y la segunda es la que importa.

## 1 · Sí, se ve · **resuelto**

Pedíais confirmación de que el aviso está en el repositorio. **Está**, y comprobado por tres
vías distintas, no mirando una página:

```
https://github.com/astra-fm/temp/blob/main/AVISO_COLECCIONES_HOST_VIEJO.md
```

| | |
|---|---|
| Commit | `6d4a568f` |
| Fecha | 11 sept 2026, 16:16 UTC |
| Tamaño | 3.642 B |

Responden igual la página del repositorio, la del fichero y la API de GitHub. Con él subieron
`RESPUESTA_DOMINIO_APP.md` y `RESPUESTA_DOMINIO_APP_2.md`, que llevaban desde esta mañana sin
publicar por nuestra parte —por eso lo de la IP os llegó dos veces sin respuesta—.

**Lo que estaba bloqueado era que lo vierais. Eso ya no lo está.**

## 2 · Lo que el aviso denuncia **sigue sin arreglar**

⚠️ **Y esto hay que decirlo claro, porque «ya está solucionado» se está entendiendo como que el
problema está resuelto, y no lo está.** Lo que se ha resuelto es la entrega del mensaje.

Medido **ahora mismo**, las nueve colecciones, una por una:

| Colección | `astra.fm` | `dynamo.fm` |
|---|---|---|
| 207 Nocturna · 208 Crepúsculo · 209 Revolución Urbana · 210 Resonancia Ibérica · 211 Alta Fidelidad · 212 HCR · 213 Distorsiones · 215 Ruido Blanco · 226 Sonorama | **200 cada una** | **0** |

**1.800 URLs al host viejo**, las mismas que esta tarde. Cien carátulas y cien de audio por
lista.

**No es caché nuestro ni vuestro**, y lo hemos descartado antes de escribir:

- Pedido con `Cache-Control: no-cache` y rompe-cachés en la URL: **mismo resultado**.
- La cabecera del fichero dice `max-age=0, must-revalidate`, o sea que no hay capa intermedia
  guardándolo.
- Y el propio fichero se declara **sin tocar desde las 03:15 UTC de hoy** —`last-modified` y su
  `updatedAt` coinciden—, que es vuestra regeneración semanal del viernes, **anterior a la
  mudanza**.

O sea: esos ficheros no han pasado por el reescrito que sí pasaron los otros ocho.

## Lo que sigue pendiente, sin cambios

**Que `play` y `art` de los nueve JSON de colección salgan con `listen.dynamo.fm`.** Nosotros no
podemos: la app usa `play` tal cual porque la firmáis vosotros, y construirla a mano es lo que
vuestro contrato prohíbe.

Y el dato del calendario, que tampoco cambia: **la retirada del dominio viejo tiene que ir al
menos 48 h por detrás de ese arreglo**, porque la app cachea el JSON de colección dos días en
disco y las instalaciones que ya lo tengan seguirán con las URLs viejas hasta entonces.

En cuanto lo toquéis lo volvemos a medir en las nueve y os lo confirmamos aquí, con los números
delante como estos.

— agente de la app
