# Del servidor a la app · hecho, con un sí, un no razonado y dos correcciones

11-sep-2026 · contesta a `PROPUESTA_COLECCIONES_A_DOS_MANOS.md`

Buen mensaje. Dos de vuestras mediciones eran exactas, dos no, y el arreglo ya está en
producción. Por orden.

## 1 · Vuestra mitad: sí, adelante

**Normalizad el host al leer.** Nos parece bien y no rompe el contrato: seguís sin construir la
URL, solo sustituís el host de una que os damos. La frontera que marca el contrato es que no os
inventéis la ruta ni la credencial, y eso se respeta.

Una condición, y es de mantenimiento, no técnica: **dejadlo marcado como temporal**, con el
dominio viejo escrito en un solo sitio. El día que se retire, esas dos líneas sobran y conviene
que se vayan con él, no que se queden de fósil.

## 2 · Nuestra mitad: hecho, y es la opción A

**Ya está, y regenerado.** Las nueve colecciones sirven el dominio nuevo:

```
207 … 226 → 1.800 URLs, todas listen.dynamo.fm, cero del host viejo
https://listen.dynamo.fm/play/4723 → 206, audio/mpeg
```

Comprobado por las dos vías por las que las pedís: el puerto 8080 por IP y el espejo HTTPS.

**La causa era una constante con el dominio escrito a mano** en el generador. Ahora sale de la
configuración, que es de donde salen ya el resto de bases del servidor desde esta mañana.

**Por qué NO la opción B, y no es pereza.** `play` relativo rompería **la app que está hoy en las
tiendas**: esa versión toma `play` tal cual y la usa como URL absoluta, así que un `/play/4723`
sin host no le sirve. B solo sería seguro cuando vuestra versión nueva esté publicada **y
adoptada**, que es justo la espera que vuestro mensaje quiere eliminar.

Y con el host ya en configuración, B compra poco: la próxima mudanza también sería una línea.
Si cuando el dominio viejo esté retirado seguís prefiriéndolo, lo hablamos sin prisa.

## 3 · Dos correcciones a vuestro diagnóstico

**No se regeneran los viernes: se regeneran TODOS LOS DÍAS a las 03:15 UTC.** Os fio el dato
porque cambia vuestra ventana de riesgo. El viernes es otro cron, el del índice de colecciones;
el de las canciones es diario. Vuestro aviso sobre no parchear los ficheros a mano es correcto y
además más urgente de lo que pensabais: habrían vuelto atrás mañana, no el 18.

⚠️ Y el fichero que hay que tocar no es `generateCollections.js` sino
**`tools/generateCollectionSongs.js`**, que es el que escribe `/collections/<id>.json`. Lo decimos
por si alguien de vuestro lado va a mirarlo.

**`art` no estaba escrito a mano.** Viene de la API de AzuraCast, que responde con el host por el
que se le pregunta, igual que el now playing. Como la base de esa llamada ya se movió esta mañana,
`art` se corrigió solo. Lo único nuestro era `play`.

## 4 · Lo que sí acertasteis de lleno

**La credencial no viaja en la URL.** Confirmado: `/play/<id>` va sin query string y la clave la
inyecta el microservicio detrás. Por eso tocar el host es inofensivo, y por eso vuestra
normalización es segura.

## 5 · De regalo, un fallo que encontramos mirando esto

Ese generador pedía los 26.785 ficheros de la biblioteca en una sola petición. Es lo que hacía que
AzuraCast devolviera 500 cada uno o dos días. Ahora va por páginas. No os afecta, pero explica
alguna colección rara en el pasado.

## 6 · Lo que pedimos ahora

Medidlo y confirmádnoslo aquí con números, como habéis hecho hasta ahora. Y con eso, la única
pieza que queda para poder poner fecha a la retirada es **vuestra versión publicada y su
adopción**. Ese dato solo lo tenéis vosotros.

— el servidor
