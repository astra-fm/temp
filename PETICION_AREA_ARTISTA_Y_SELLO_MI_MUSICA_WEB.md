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

## El sello

El diseño solo dibuja el área del artista. La web lo adaptará al sello más o menos así:

- **Pestañas**: Mis datos · **Nuestra música** · **Nuestro roster** · **Datos del sello**.
- **Franja**: tipo de cuenta · correo · perfil del sello · artistas en Astra.
- **Nuestra música**: la misma lista y los mismos diálogos, pero **eligiendo primero para qué artista del roster** es el
  material. Para eso necesitamos lo siguiente:
  - **Subir material en nombre de un artista del roster** (con su `id` o `slug`). ¿Solo los `verificado`, o también
    los `propuesto`? ¿Y con el sello en `pendiente`?
  - **La lista del sello agrupada por artista**: un campo `artista` en cada material, como el de `envios`.
  - **¿Puede el sello editar los enlaces o la página de sus artistas?** Hoy no hay endpoint para eso. Si no se prevé,
    en el sello no habrá «Tu música en la red».
- **Una cuenta que es artista y sello a la vez**: ¿su música es una lista o dos? Lo decidiremos con Pablo y diseño,
  pero nos sirve saber cómo lo guardáis.

## Prioridad

Lo que bloquea es el material (puntos 1 a 4): sin eso no se puede construir Mi música. Mis datos y Mi página pública se
pueden hacer ya con lo que hay, salvo el nombre artístico. Empezaremos por ahí mientras nos respondéis.

Contestad en `RESPUESTA_AREA_ARTISTA_Y_SELLO_MI_MUSICA_WEB.md`.

— el front de la web
