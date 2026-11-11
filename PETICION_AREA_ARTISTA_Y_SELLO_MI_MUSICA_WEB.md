# Petición · Área de artista y sello rediseñada: lo que falta en el servidor

**De:** el front de la web · **Para:** servidor · **Fecha:** 11.11.26 · Pedido por Pablo.

Pablo ha aprobado hoy un rediseño del área del artista, que la web tiene que adaptar también al sello. Contrastado con
`CUENTAS_WEB_INSTRUCCIONES.md` y `CANAL_ABIERTO_WEB_INSTRUCCIONES.md` leídos hoy a las 12:15. Todavía no hemos
construido nada: antes necesitamos saber qué podéis darnos y cómo.

## Qué cambia

- **Fuera la columna lateral.** El área pasa a tres pestañas: **Mis datos · Mi música · Mi página pública**. Debajo va
  una franja con cuatro estados: tipo de cuenta, correo, perfil y página pública.
- **«Ficha» pasa a llamarse «página»** en todos los textos del área. Es solo texto: no hace falta renombrar nada en el
  contrato.
- **Mis envíos y Tu canción se unen en Mi música.** El artista elige **single, EP o álbum**, sube el archivo o la
  carpeta con un título y la casilla de derechos, y ve una lista de lo que ha subido. Cada material sale con su estado
  (**pendiente de validación** o **validado · en tu página**), sus canciones (número, archivo, duración y botón de
  escuchar), y se puede **borrar entero o canción a canción** (en EP y álbum).
- **Tu música en la red**: los enlaces de Spotify, Bandcamp, YouTube, SoundCloud e Instagram, con su propio botón de
  enviar. **La web propia** sale de ahí y pasa a Mi página pública como «Mi web».
- **Mi página pública**: imagen (Cambiar imagen, como ahora), **nombre artístico**, ciudad, país, bio, mi web y
  conciertos.
- **Mis datos**: un solo «Guardar cambios» para nombre, suscripciones y contraseña. Lo resolvemos con las llamadas que
  ya existen (`PUT /me` y `POST /password`), así que ahí no os pedimos nada.

## Lo que necesitamos

### 1. Material: single, EP y álbum con varias canciones

Hoy solo existe `POST /ficha/audio`, que admite **una** canción y en la que la última sustituye a la anterior. El
diseño necesita **varios materiales**, cada uno con **varias canciones**. Proponemos esto, pero decidid vosotros la
forma:

- **Crear el material**: `{tipo: "single"|"ep"|"album", titulo, derechos: true}` → `id`.
- **Subir cada canción a ese id**, una petición por archivo, con el nombre original y el orden. Mismas reglas que
  `ficha/audio`: MP3 o WAV, de 30 s a 12 min, una sola pista, hasta 200 MB, y los mismos códigos de error. **Un
  archivo que falla no anula el material**: el diseño dice «el resto se sube igual».
- **Cerrar el material** cuando acaban las subidas (o decidnos que el servidor lo da por completo solo), para que
  entre en la cola de la redacción como **una** pieza y no canción a canción.
- **¿Hay mínimos y máximos de canciones** por tipo? Por ejemplo, single = 1, EP de 2 a 7, álbum 8 o más. Si los hay,
  los validamos también en el navegador.
- **¿El límite de 20 subidas por hora** cuenta cada canción? Un álbum de 12 temas se come más de la mitad.

### 2. La lista de material, dentro de `cuenta`

Algo como `cuenta.musica.items[]` con:

`{id, tipo, titulo, estado: "pendiente"|"validado", subidoEl, duracion, canciones: [{id, n, archivo, duracion, url}]}`

- **`url` para escuchar también lo pendiente.** El artista quiere oír lo que ha subido antes de que se valide. Como un
  `<audio>` no puede mandar el token Bearer, necesitamos una URL firmada o con caducidad, o lo que veáis mejor.
- **¿Hay arte por material?** El diseño pinta un cuadrado de 96 con las iniciales del título, y los diálogos no piden
  portada. Si existe la portada (la de un envío de Radar, por ejemplo), mandadla y la pintamos.
- **¿Puede rechazarse un material?** El diseño solo tiene dos estados. Si la redacción puede descartarlo, decidnos
  cómo llega, para pintarlo «sin drama», como las propuestas.

### 3. Borrar

- **Borrar un material entero**, y **una canción de un EP o un álbum**. Un single no se borra por canción: se borra el
  single.
- **¿Una canción borrada de un EP ya validado vuelve a la redacción, o sale de la página sin más?** Diseño lo tiene
  abierto. Nos basta con saber qué hace el servidor.

### 4. Cómo encaja con lo que ya hay

- **`cuenta.ficha.audio`**, la canción subida desde Tu canción: ¿pasa a ser un single dentro de la lista? ¿Retiráis
  `ficha/audio` o convive?
- **Los envíos de Radar** (`cuenta.envios`, con `recibido`/`escuchado`/`parrilla` y `faltan`): Mis envíos desaparece
  del área. ¿Esos envíos tienen que salir en la lista de Mi música? Si es así, ¿cómo pasan sus tres estados a
  pendiente/validado? Diseño también tiene abierto qué pasa con el formulario de Radar (`/cuenta/envios/nuevo`), así
  que de momento solo os pedimos los datos.
- **Con `ficha.estado: "sin-ficha"`**, ¿el artista verificado puede subir material? Hoy `ficha/audio` responde
  `409 sin-ficha`.

### 5. Nombre artístico en Mi página pública

`POST /ficha/proponer` no admite `nombre`. ¿Lo podéis añadir como un campo propuesto más? El valor publicado ya llega
en `ficha.publicada.nombre`.

### 6. Enlaces por separado de la página

Mi música manda solo las cinco redes, y Mi página pública manda solo `web`. Entendemos que `enlaces` con algunas claves
está permitido, porque «las claves no enviadas no se tocan». Confirmadnos que cada envío deja intactas las claves que
no lleva.

### 7. El aviso de la pestaña Mi música

La pestaña lleva una marca cuando hay **material pendiente de validación**. Lo sacamos de la lista del punto 2: no hace
falta un campo aparte.

## El sello · actualizado a las 13:30

⚠️ **Esta sección sustituye a la de esta mañana** («Nuestra música · Nuestro roster»): diseño ha dibujado ya el área
del sello y Pablo la ha aprobado. Contrastado otra vez con `CUENTAS_WEB_INSTRUCCIONES.md` a las 13:20 (sin cambios
desde la lectura de las 12:15).

### La lógica nueva

**El sello gestiona a sus artistas como si fuera cada uno de ellos.** Ya no hay una lista de envíos del sello separada
del roster: la música, los enlaces, los conciertos y los datos de un artista se gestionan **dentro de ese artista**.

- **Pestañas: Mis datos · Artistas · Mi página pública.** Sin columna lateral, como el artista.
- **Franja**: tipo de cuenta · correo · perfil del sello · artistas en Astra (`roster.artistas`, ya existe).
- **Mis datos**: persona de contacto y cargo (los privados de `PUT /sello`), suscripciones y contraseña, con un solo
  «Guardar cambios». Con lo que hay nos basta.
- **Artistas** sustituye a Nuestro roster y a Nuestros envíos. Es una rejilla de tarjetas, una por artista del roster:
  foto, nombre, ciudad · país, estado y, sobre la foto, «N PENDIENTE» si tiene material sin validar. Tres estados:
  **página publicada** (`verificado` + `ficha: "publicada"`), **verificado · sin página todavía** (`verificado` +
  `ficha: null`) y **propuesto · en revisión**. Los propuestos no se abren. «Añadir artista» sigue siendo
  `POST /roster/proponer`.
- **Al pulsar una tarjeta**, en la misma pestaña, la **gestión de ese artista**: los mismos bloques y diálogos que el
  área del artista (puntos 1 a 6 de arriba), en este orden: **su música** (material con canciones, subir y borrar),
  **su música en la red** (cinco enlaces), **próximos conciertos**, y **su imagen y sus datos** (nombre, ciudad, país,
  bio y su web), con «Enviar cambios a la redacción». Y un enlace a su página pública con el `slug`.
- **Mi página pública** es la **página del sello**: imagen del sello, nombre, ciudad, país, «Sobre el sello» y web, y
  la lista «Artistas en vuestra página» (los confirmados por la redacción, cada uno con su enlace a la gestión). Pie:
  «3 de 4 aparecen hoy. El orden lo pone la redacción.».
- La pestaña Artistas lleva la marca de aviso cuando **algún** artista tiene material pendiente.

### Lo que necesitamos para el sello

**S1. Escribir en nombre de un artista del roster.** Todo lo que el artista hace sobre su página (puntos 1 a 6 de
arriba, más `ficha/proponer`, `ficha/foto` y lo que salga del material) tiene que poder hacerlo el sello **apuntando a
un artista** de su roster. Nos vale el mismo endpoint con `?artista=<id>` o rutas `/roster/<id>/…`; decidid vosotros.
Y decidnos:
  - ¿Solo con el artista `verificado`? ¿Y con `ficha: null` (verificado sin página): puede subir música?
  - ¿Con el sello en `pendiente`, o solo `verificado`?
  - Los códigos de error cuando el artista no es de su roster (¿`403`? ¿`404`?).

**S2. Leer los datos de cada artista del roster.** Hoy cada item trae `{id, nombre, slug, estado, ficha}`. Para la
tarjeta y la gestión necesitamos, por artista, lo mismo que trae `cuenta.ficha` al artista: `foto`, `publicada`
(nombre, ciudad, país, bio, enlaces, conciertos), `propuestas`, `audio` mientras exista, y su lista de material del
punto 2 con los pendientes. Puede ir dentro de cada item de `roster.items` o en un `GET` por artista si pesa demasiado
en `/me`; si va aparte, mandad al menos `foto`, `ciudad`, `pais` y el número de pendientes en el item para pintar la
rejilla sin abrir cada uno.

**S3. Campos nuevos del sello (decisión de Pablo).** En `sello/proponer` y en `cuenta.sello`:
  - **Imagen del sello**: por archivo y por enlace, como la foto del artista (`ficha/foto` y `foto` en
    `ficha/proponer`, con la misma regla de una sola en revisión).
  - **«Sobre el sello»**: texto, como la bio.
  - **Ciudad y país por separado.** Hoy es un solo `lugar`. ¿Lo partís, o mantenéis `lugar` y añadís los dos?

**S4. La página pública del sello.** La web **no tiene** página de sello: no existe ninguna ruta pública para él. El
diseño la da por supuesta («la página del sello sale en Astra cuando la redacción confirma vuestro roster»). ¿Existe en
el catálogo o la está pensando alguien? ¿Qué datos publicaría y en qué dirección? Y **el orden** de los artistas en
esa página, que «pone la redacción»: ¿llega como orden de `roster.items`, o con un campo?

**S5. Quién gana cuando el artista también tiene cuenta.** Un artista del roster puede tener su propia cuenta de
artista y proponer cambios a la vez que su sello. ¿Las dos propuestas conviven en la cola de la redacción? ¿El artista
ve en su área lo que propuso el sello, y al revés? ¿Puede el artista quitar a su sello el permiso de gestionarlo?

**S6. Una cuenta no puede ser artista y sello a la vez** ⚠️ *decisión de Pablo, 11.11.26, sustituye a la pregunta
anterior*. En el alta se elige una cosa u otra, y así tiene que seguir después. Hoy el contrato documenta
`roles: ["artista","sello"]` como posible (§ roles) y `POST /cuentas/perfil` solo responde `409` si se pide **el
mismo** tipo otra vez. Os pedimos:
  - Que `POST /cuentas/perfil` (y el alta con `perfil`) **rechace pedir el otro tipo** si la cuenta ya tiene
    solicitud de artista o de sello en cualquier estado que no sea `descartado`. Decidnos el código (¿`409` con un
    `codigo` propio?) para escribir el mensaje.
  - Que **retiréis `["artista","sello"]`** de los valores de `roles` en el contrato.
  - **¿Hay hoy alguna cuenta con los dos?** Si la hay, decidnos cuál y qué hacéis con ella. Mientras, la web le
    enseña el área del sello y nada del artista.
  - Un artista que quiera aparecer en un sello no se pide sello: se lo añade su sello con `roster/proponer`.

**S7. Borrar la cuenta del sello.** El texto del diseño dice: se van las suscripciones, los datos de contacto y el
vínculo con el roster («las páginas de vuestros artistas dejan de gestionarse desde aquí»); se quedan la página, el
material ya publicado y lo editorial; la cuenta se desactiva al momento y se borra a los 90 días. Confirmad que es
eso lo que hace el servidor, sobre todo qué pasa con el material **pendiente** que subió el sello.

## Dependencias que este rediseño puede tocar

Lo que vemos desde la web. Decidnos si alguna os afecta y si nos falta alguna.

1. **Direcciones del área.** Cada pestaña tendrá su dirección y desaparecen `/cuenta/envios`, `/cuenta/roster`,
   `/cuenta/sello` y `/cuenta/ficha`. Las viejas redirigirán a la pestaña nueva, pero **si algún correo que mandáis
   (acuse de envío, propuesta aplicada, artista verificado…) enlaza a una de ellas**, decidnos cuáles para mantener la
   redirección o que cambiéis el enlace. Las nuevas os las pasamos cuando estén cerradas.
2. **El formulario de Radar** (`/cuenta/envios/nuevo`, contrato `CANAL_ABIERTO_WEB_INSTRUCCIONES.md` §4) y su
   «Puerta 2», que llama a `roster/proponer`. Mis envíos desaparece; diseño aún no ha decidido si los diálogos de subida
   lo sustituyen o lo preceden. Si lo sustituyen, **¿un material subido desde el área entra en la misma cola que un
   envío de Radar** (escuchado, en parrilla) o en otra? Y lo que la redacción ve hoy de un envío (géneros, contexto,
   imágenes) dejaría de llegar.
3. **`cuenta.envios`** y su agrupación por el texto libre `artista`: con la música colgando de cada artista ya no la
   usaríamos para agrupar. ¿Los envíos antiguos se migran a la lista de material de cada artista, o se quedan como
   historial?
4. **`cuenta.ficha.audio` y `ficha/audio`** (Tu canción, 10.11): pasan a ser un single dentro de la lista (punto 4 de
   arriba).
5. **La app.** Lee `cuenta.ficha`, `cuenta.roster` y `cuenta.sello` y remite a la web para editar (§5.4). Si cambia la
   forma de esos bloques (S2, S3) o aparece `cuenta.musica`, la app tiene que enterarse. Nosotros no le escribimos:
   avisadla vosotros si lo veis necesario.
6. **La cola de la redacción** (`ARQUITECTURA_DASHBOARD.md`): las propuestas y el material que mande un sello por un
   artista tienen que decir **quién las mandó**, para que la redacción sepa si viene del artista o de su sello.
7. **La página pública del artista** (`radar/artistas.json`, ficha por `slug`): lo validado tiene que acabar ahí,
   incluidas las canciones de un EP o álbum. ¿La página pública pinta hoy más de una canción por artista?
8. **El límite de subidas** (punto 1): un sello con cuatro artistas sube por todos desde una sola cuenta. ¿Cuenta por
   cuenta o por artista?

## Prioridad

Lo que bloquea es el material (puntos 1 a 4 y S1, S2): sin eso no se pueden construir Mi música ni la gestión de un
artista del sello. Mis datos se puede hacer ya con lo que hay, en artista y en sello. Empezaremos por ahí mientras nos
respondéis.

Contestad en `RESPUESTA_AREA_ARTISTA_Y_SELLO_MI_MUSICA_WEB.md`.

— el front de la web
