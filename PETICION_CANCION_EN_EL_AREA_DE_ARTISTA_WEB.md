# Petición · Subir la canción y la foto desde el área de artista

**De:** servidor · **Para:** el front de la web · **Fecha:** 10.11.26 · Pedido por Pablo.

## Por qué

La ficha pública de un artista de Radar tiene un reproductor con su canción, pero hoy el artista no puede añadirla
después de enviar su música: `POST /cuentas/ficha/proponer` admite bio, ciudad, país, foto, enlaces y conciertos, y
ningún archivo. Vamos a recordar por correo a cada artista lo que le falta en la ficha, y la canción es una de esas
cosas, así que necesita poder subirla.

**Estado del servidor:** lo de abajo es el contrato que vamos a montar; **todavía no está en producción**. Podéis hacer
la pantalla ya, pero no la publiquéis hasta que avisemos aquí, en `temp`.

## Qué pedimos

En **Mi ficha pública**, solo con el perfil de artista `verificado` y ficha `publicada`, un bloque **Tu canción**:

1. Si ya tiene una, reproductor con esa canción y botón **Cambiar**. Si no, botón **Subir canción**.
2. Selector de **un solo archivo**: `accept=".mp3,.wav,audio/mpeg,audio/wav"`, sin `multiple`.
3. Casilla obligatoria, sin marcar: «Confirmo que tengo los derechos de esta canción y autorizo a Astra FM a publicarla
   en mi ficha».
4. Barra de progreso, porque un WAV puede tardar.
5. Al terminar, el estado **En revisión**: nada se publica sin que lo vea la redacción, igual que el resto de la ficha.

Textos en los cuatro idiomas de la web.

## Qué archivo vale

| Regla | Límite | Código si no se cumple |
|---|---|---|
| Formato | MP3 o WAV, nada más (ni FLAC, ni M4A, ni ZIP, ni vídeo) | `formato-no-admitido` |
| Una canción | Entre 30 segundos y 12 minutos | `demasiado-corta` · `demasiado-larga` |
| Una sola pista | Sin capítulos en el archivo | `varias-pistas` |
| Tamaño | 200 MB | `archivo-demasiado-grande` (413) |
| Audio legible | Debe poder decodificarse | `audio-no-valido` |

**Antes de subir, comprobad en el navegador** la extensión, el tamaño y la duración (cargando los metadatos en un
`<audio>`), para no hacer esperar a nadie por un archivo que va a fallar. Si el navegador no sabe leer la duración, subid
igualmente.

**Quien decide es el servidor:** mira el contenido real del archivo, no la extensión ni el `Content-Type`, y mide la
duración. Pintad siempre su respuesta. Doce minutos es el corte porque ninguna de las 6.948 canciones de la radio pasa
de diez; un disco entero unido en un archivo lo supera. Si alguien sube dos temas unidos que quepan en ese tiempo, lo
detecta la redacción al escucharlo.

Texto sugerido para `demasiado-larga`: «Sube una sola canción, no un disco entero. Como máximo, 12 minutos».

## Contrato

```
POST https://listen.astra.fm/cuentas/ficha/audio?derechos=1
Authorization: Bearer <token>
Content-Type: audio/mpeg | audio/wav
<el archivo tal cual en el cuerpo>
```

| Respuesta | Cuándo |
|---|---|
| `201 {ok, propuesta:{id, estado:"pendiente", campos:["audio"]}, cuenta}` | Recibida, en revisión |
| `400 {ok:false, codigo, error}` | Cualquier código de la tabla anterior, o `faltan-derechos` |
| `401 sesion-no-valida` · `403 debeVerificar` · `403 artista-no-verificado` · `409 sin-ficha` | Como en `ficha/proponer` |
| `413 archivo-demasiado-grande` · `429 demasiados-intentos` | Tamaño o demasiados intentos |

`cuenta.ficha` añade `audio`, la URL pública de la canción o `null`. La propuesta en revisión llega en la misma lista de
propuestas de la ficha, con `campos:["audio"]`. Subir otra sustituye a la que estuviera en revisión.

## La foto: subir archivo o pegar enlace

*Añadido el 10.11.26.* Hoy la foto de la ficha solo admite un enlace, y el servidor no guardaba la imagen: un enlace
caducó antes de que nadie la copiara y otro llevaba a una página en lugar de a una imagen. Pablo quiere **las dos
opciones** en **Mi ficha pública**:

1. **Subir un archivo**: un solo archivo, `accept=".jpg,.jpeg,.png,.webp,image/jpeg,image/png,image/webp"`, hasta 15 MB.
2. **Pegar un enlace**: el campo actual, que debe llevar directamente a la imagen, no a un álbum ni a una galería.

Con cualquiera de las dos, la foto queda **En revisión** hasta que la vea la redacción.

```
POST https://listen.astra.fm/cuentas/ficha/foto
Authorization: Bearer <token>
Content-Type: image/jpeg | image/png | image/webp
<la imagen tal cual en el cuerpo>
```

Responde como la canción: `201 {ok, propuesta:{id, estado:"pendiente", campos:["foto"]}, cuenta}`. Errores:
`formato-no-admitido`, `imagen-no-valida`, `archivo-demasiado-grande` (413), además de los de sesión y perfil.

El enlace sigue yendo en `foto` dentro de `POST /cuentas/ficha/proponer`. **Cambio:** el servidor descargará la imagen al
recibirla y guardará su copia. Si el enlace no da una imagen, responde `400 foto-enlace-no-valido` y no guarda nada de
esa propuesta; conservad lo que el artista haya escrito en el formulario y marcad el campo.

Texto sugerido: «Ese enlace no lleva a una imagen. Pega el enlace directo a la foto o sube el archivo».

## Una cosa más en /darse-de-baja

Los recordatorios de la ficha llevarán su propio enlace de baja:

`https://astra.fm/darse-de-baja?email=…&token=…&consent=recordatorios_ficha`

Cuando llegue ese `consent`, la opción principal debe ser «Dejar de recibir los recordatorios para completar mi ficha».
Enviad `POST /comunicacion/unsubscribe` con `consents:["recordatorios_ficha"]`, como con las otras listas. Solo apaga
esos recordatorios: ni borra la cuenta ni afecta a las demás listas.

Cuando lo tengáis, dejad aquí `PUBLICADO_CANCION_EN_EL_AREA_DE_ARTISTA_WEB.md` y avisad a Pablo.

— el servidor
