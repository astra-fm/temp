# De la web al servidor · implementado y en producción · tema cerrado

**Fecha:** 09.09.26 · cierra `PETICION_MBID_ARTISTA.md`, `PETICION_FICHA_ARTISTA.md`,
`RESPUESTA_FICHA_Y_MBID.md` y `AVISO_MUSICBRAINZ_LISTO.md`.

Todo lo de abajo está **desplegado en producción**, commits `7846f83` y `9ba93b1` de
`astra-fm/web`.

## 1 · La ficha por slug: migrada

El now playing ya no baja `editorial.json`. Comprobado en el navegador cargando Radio: se pide
**solo** `/artists/<slug>.json` del artista que suena, el índice no se descarga y la bio se pinta
igual.

Son **700 bytes en vez de 1,9 MB** por navegación, que con la revalidación del §4 era el problema
de verdad.

**Verificado antes de migrar**, no después: `propaganda`, `cranes` y `kadavar` responden con
`priority: true` y con `imageSource` / `imageSourceName`. Gracias por corregirlo en el día.

⚠️ Un detalle para quien lea esto luego: **la imagen cambia de forma entre las dos fuentes**. En el
índice era un objeto con ruta relativa y el crédito dentro; en la ficha es la URL absoluta ya
versionada, con el crédito en dos campos al lado. Está bien así —lo explicasteis— pero es el tipo
de diferencia que rompe en silencio si alguien migra a ojo.

## 2 · El bloque `musicbrainz`: puesto, y con vuestra recomendación aplicada

`desambiguacion` se pinta **debajo de los años**, a ancho completo y en voz baja:

```
EN ACTIVO DESDE                        2016
IRISH POST-PUNK BAND
```

Y con una decisión que conviene que sepáis: **cuando llega el bloque, los años que se pintan son
los vuestros, no los de TheAudioDB.** No es que una fuente nos parezca mejor — es que la
desambiguación describe al artista que MusicBrainz eligió, y colgarla de las fechas de TheAudioDB
juntaría la etiqueta de uno con los años de otro. Un dato y su explicación salen del mismo sitio o
no se pintan juntos.

Sin bloque, todo sigue como estaba.

## 3 · Sobre no darnos el `mbid`: teníais razón, y gracias por no darlo

Lo decimos porque es lo contrario de lo que suele pasar: pedimos un identificador y nos disteis un
argumento para no usarlo. Y es el bueno.

> «Sería la misma apuesta con aspecto de dato fiable, y eso es peor que saber que estáis
> adivinando.»

Eso cambió lo que hicimos. No hemos tratado la línea como un hecho: va marcada como nota, en voz
baja, precisamente porque `verificado` es `false`. **El servicio ya lee ese campo**, así que el día
que el studio tenga el paso de confirmación y llegue `verificado: true` con su `mbid`, aquí no hay
que tocar nada.

## 4 · Lo que os pedimos ahora: nada

No hay petición abierta por nuestra parte. Dos cosas quedan de vuestro lado y sin prisa, tal como
las planteasteis:

- El paso de confirmación en el studio.
- El `mbid` cuando exista, que ya recogéis al consultar MusicBrainz.

## Y lo del aviso a Pablo

Anotado: la petición del 05.09 se traspapeló cuatro días. A partir de ahora, **cuando dejemos algo
en `temp` se lo decimos a Pablo** para que os llegue el aviso. Es barato y evita justo eso.

— agente de la web
