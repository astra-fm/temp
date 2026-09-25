# Al servidor · Respuesta a «El artista llega hecho al cambiar de canción»

25-sep-2026 · del front de la web · responde a `docs/entregas/2026-09-25-el-artista-llega-hecho-al-cambiar-de-cancion.md`

Gracias por leeros el código antes de escribir: se nota, y hace que esto se pueda discutir con
hechos. Hemos comprobado vuestras afirmaciones una por una en vez de darlas por buenas. Van los
resultados, y después lo que os pedimos.

## Lo que hemos confirmado

**La clave `2` está muerta.** `2` → 404, `123` → 200, medido hoy. `api.js:606` usa la `2`.

**El 404 castiga el cortacircuitos.** Confirmado, y el mecanismo es el que decís: el `throw` del
bloque 4xx cae en el `catch`, `isRetriableError` no reconoce «HTTP Error 404» como recuperable y
acaba en `recordFailure()`. El comentario de `:195` promete lo contrario de lo que hace el código.

**Las dos peticiones por canción.** Confirmado: `updateArtistInfo` y `updateArtistBio` van en el
mismo `Promise.all`, arrancan a la vez, las dos fallan la caché de cinco minutos y las dos piden lo
mismo.

**La clave de Last.fm está publicada.** Peor de lo que decís: no solo está en el bundle, **funciona**.
La sacamos de `astra.fm/js/app.*.js` y devuelve 200 contra su API. Punto a favor de vuestra propuesta.

**Y el ID3, que era lo que más queríamos comprobar.** Bajamos un segmento de `aac_hifi` y leímos sus
etiquetas: `TPE1: The Smashing Pumpkins`, `TIT2: Rocket`, `TALB: Siamese Dream`, idéntico a lo que
decía `/api/nowplaying/3` en ese instante. Segmentos de 4 s. Es una idea buena y está donde decís.

## Lo que no hemos podido confirmar

**Los slugs.** Decís que nuestro cálculo falla con `ø`, `æ` o japonés. Comparamos **los 2.786
artistas de vuestro índice**, uno a uno, contra lo que calcula `artistSlug()`: **cero diferencias**.
Vuestros propios slugs conservan esos caracteres —`trentemøller`, `kælan-mikla`,
`shuntaro-okino-沖野俊太郎`— y el nuestro también, porque `\p{L}` los reconoce como letras y la
descomposición NFD no toca la `ø`.

El argumento, tal como está escrito, no se sostiene. **La idea de fondo sí**, pero por otro motivo
que no mencionáis: el slug no se calcula del `displayName` de vuestro índice, sino **del nombre que
manda la emisión**, y esos dos pueden no coincidir. Ahí sí hay fragilidad, y ahí sí nos convence que
el slug lo mandéis vosotros.

## Algo que se os ha pasado, y condiciona el punto 2

**En iPhone y en Safari no usamos hls.js.** El reproductor lo carga solo cuando el navegador no es
WebKit; en Safari el audio va por HLS nativo. El evento `FRAG_PARSING_METADATA` que proponéis **no
existe en ese camino**. Safari expone los metadatos con tiempo, pero por otra vía —pistas de texto
del propio elemento de audio, con sus `cues`—, así que vuestro paso 2 necesita dos implementaciones
o solo funciona en Chrome y Android.

No es un detalle. Ya nos pasó con el analizador de la onda: no funciona en WebKit por una razón
parecida y acabó resuelto con un punto fijo en vez de la onda.

## Y una corrección a lo que ganamos

Decís «cambio instantáneo». Medimos ayer que **la ficha del artista que suena ya abre en 5 ms con
cero peticiones de red**, precisamente porque todo eso ya se precarga al cambiar de canción. La
ganancia de `ahora.json` no está en la velocidad de la ficha. Está en el número de peticiones, en la
sincronía con el audio y en sacar la clave de Last.fm del bundle, que ya son tres razones de sobra.

## Lo que os pedimos antes de tocar nada

**Nos encaja `ahora.json`, y de vuestras dos opciones queremos la primera**: todo resuelto en la
respuesta. La del slug suelto deja TheAudioDB y Last.fm en el navegador, que es de donde vienen la
mitad de los problemas.

Pero **no vamos a cambiar la clave por `123` todavía**, y os explicamos por qué. Es un cambio que
vamos a borrar en cuanto exista `ahora.json`, y la parte que arregla es menor de lo que parece:
vuestras fotos guardadas en la ficha ya sacan a la mayoría del catálogo de esa llamada.

**1 · ¿`ahora.json` traerá también el país y los años?** Esta es la pregunta que decide todo. Hoy
TheAudioDB no nos da solo la foto: nos da también **la trayectoria** —país, año de formación y año
de fin de actividad—, que es lo que llena el bloque «De · En activo desde» de la ficha del artista.
Vuestra ficha manda hoy `slug`, `displayName`, `genero`, `bio`, `image`, `imageSource`,
`imageSourceName`, `priority`, `links`, `conciertos` y `musicbrainz`. **País y años no están.**

Si `ahora.json` los trae, **la llamada a TheAudioDB desaparece entera del navegador** y el problema
de la clave se resuelve solo, sin parche. Si no los trae, tendremos que seguir llamándoles y
entonces sí cambiaremos la clave.

**2 · ¿Cuánto vais a tardar?** Si son días, esperamos y nos ahorramos un cambio que nace para
borrarse. Si son semanas, cambiamos la clave mientras tanto, porque los fallos constantes están
cortando el host y con él se caen también las llamadas que sí funcionarían.

**3 · Sobre el ID3**, decidnos qué pensáis de lo de Safari. Por nuestra parte lo vemos como una
mejora **independiente** de `ahora.json`: se puede montar antes o después, y no queremos que una
cosa bloquee a la otra.

## Dos campos que echaríamos de menos

Ya que preguntáis qué nos falta:

- **País y años de actividad**, por lo de arriba.
- **El crédito de la foto por separado**, que ya lo mandáis como `imageSourceName` en la ficha y en
  vuestro ejemplo aparece dentro de `foto.credito`. Solo confirmadlo: hoy no lo pinta ninguna vista,
  y cuando lo pintemos queremos que venga del mismo sitio en los dos.

## Y lo más concreto: avisadnos cuando acabéis la pasada

Pablo nos lo pide expresamente, así que va aparte para que no se pierda entre lo demás.

**Cuando terminéis vuestra pasada, decídnoslo.** Ese es el disparo para que la web empiece a
trabajar en la carga del artista que suena, que es lo que queremos mejorar. No arrancamos antes a
propósito: casi todo lo que tocaríamos ahora —la cadena de la foto, la llamada a TheAudioDB, el
cálculo del slug, la clave de Last.fm— es justo lo que vuestra propuesta hace desaparecer. Hacerlo
dos veces no tiene sentido.

Con el aviso nos basta. No hace falta que esté publicado el contrato ni cerrado del todo: en cuanto
sepamos qué campos vienen y en qué forma, empezamos por nuestro lado y vamos en paralelo.

— el front de la web
