# Del front de la web al servidor · los dos JSON ya están limpios · nada que pedir

**De:** el front de la web · **Fecha:** 11.09.26

Íbamos a escribir para pediros que `actualidad.json` y `lanzamientos.json` dejaran de nombrar el
host viejo. **Lo medimos otra vez antes de mandarlo y ya está hecho**, así que esto es un cierre y
no una petición.

## Lo comprobado ahora mismo

Los ocho ficheros que consume la web, limpios:

| | |
|---|---|
| `actualidad.json` | **99 apariciones, todas `listen.dynamo.fm`** |
| `lanzamientos.json` | limpio |
| `conciertos.json` · `programas.json` · `tv.json` | limpios |
| `radar.json` · `avisos.json` · `collections.json` | limpios |

Y el detalle que lo confirma del todo: **pidiendo `actualidad.json` por el host viejo también
vuelve con el nuevo**. Es decir, no depende de por dónde entre la petición: el contenido está
reescrito.

Las 202 imágenes incrustadas en el cuerpo de los artículos —las que decíais que ibais a
regenerar— vienen ya con el dominio nuevo.

## Con esto, por parte de la web no queda nada apuntando al dominio que se retira

Lo repasamos entero: el sitio, el servicio, los dos ficheros que no pasan por el empaquetador y el
proxy de desarrollo. Y ahora vuestros JSON. **Cero referencias al host viejo en lo que la web pide
o sirve**, salvo las cuatro líneas del service worker que existen justo para apagarse en el
dominio viejo, y que se borrarán cuando deje de servir.

## Lo único que sigue en marcha, y es de Pablo

**La redirección de `astra.fm` a `dynamo.fm`**, 301 y conservando la ruta. Ya está configurada en
el panel; el proveedor dice que tarda alrededor de una hora en hacerse efectiva. Cuando entre lo
comprobamos —código, ruta, `www` y que no encadene saltos— y os lo decimos.

Gracias por la velocidad con esto: lo señalamos hace tres horas.

— el front de la web
