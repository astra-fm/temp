# Petición · el alta nueva: el perfil se valida con la música

**De:** el front de la web · **Para:** servidor · **Fecha:** 12.11.26 · Decisiones de Pablo, cerradas hoy.

Pablo ha rehecho el alta de cuentas. Artista y sello dejan de verificar el correo y de pasar por el formulario de Radar:
**se dan de alta mandando su música, y aprobar esa música es lo que valida el perfil**. El oyente no cambia. Os paso el
flujo entero, con sus errores, para que lo implementemos a la par. Antes de escribir código en la web necesito vuestro
contrato (rutas, campos, estados y códigos de error); decidme también lo que no os cuadre.

## 1 · Darse de alta

Un formulario con tres tipos de cuenta.

| Tipo | Pide | Después |
|---|---|---|
| **Artista** | correo, contraseña, nombre artístico y **una canción: archivo o enlace** | queda en espera, sin verificar el correo |
| **Sello** | correo, contraseña, nombre del sello, **nombre de un artista suyo**, **géneros del sello** (1 a 3, la lista de Radar) y **un audio de ese artista** | queda en espera, sin verificar el correo |
| **Oyente** | correo, contraseña y nombre | como hoy: verifica el correo (enlace de 24 h) y tiene su cuenta |

- **Canción por enlace (artista):** el alta avisa de que **no entrará en las rotaciones de los programas**. Si se aprueba,
  un enlace de Spotify se publica en su perfil con el mismo reproductor de los álbumes; Bandcamp, SoundCloud, YouTube y
  el resto, como enlace normal. Con archivo, nuestro reproductor y entra en rotación como siempre.
- **Sello:** ese artista queda creado en la lista del sello con el audio como su primera música. Los géneros del sello se
  guardan y se publican en su página.
- **Sin correo de acuse al darse de alta.** Al entrar en la cola, **alerta a Pablo en el Studio**.
- **Un oyente no puede pasar a artista ni a sello** desde su área: escribe a soporte. Desaparece `POST /perfil` para el
  oyente, o deja de usarlo la web.

## 2 · La espera

- Puede entrar con su contraseña. **Solo Mis datos activa**; Mi música (o Artistas) y Mi página pública, apagadas.
- La franja: «Tipo de cuenta: Artista» (o Sello) y «Perfil: en revisión». Sin fila de correo.
- Necesito en `/me` el estado de la solicitud y lo enviado (título, tipo de canción, fecha) para pintarlo.

## 3 · La decisión de la redacción

- **Aprueba:** el perfil queda validado. Sale un **correo de aprobación con un enlace de acceso de un solo uso**. Abrirlo
  **verifica el correo y activa el área** con las tres pestañas y todos los campos.
- **No aprueba:** sale un **correo amable** que explica que su música no está en la línea de la radio y que su cuenta
  sigue como cuenta de oyente, con sus suscripciones. No se publica nada. Si cambia su música, escribe a soporte.
- **Entra con su contraseña sin haber abierto el enlace:** el área le avisa de que abra el correo y le deja **reenviarlo**.
  Es la verificación del correo: sin ella, alguien podría usar un perfil aprobado con un correo que no es suyo.
- **Enlace caducado o ya usado:** el mismo aviso con «reenviar». ⚠️ **La caducidad está por decidir**; proponed una.

## 4 · La página pública

- Se publica cuando el perfil está aprobado **y tiene foto y bio** (en el sello, imagen y «Sobre el sello»). Vale para el
  artista, para el sello y **para los artistas que crea un sello**.
- Mientras falte algo, la página no sale y Mi página pública dice qué falta: necesito en `/me` (y en `GET /roster/:id`)
  qué le falta para publicarse.
- Lo demás de §5.9 sigue igual: los datos se publican al guardar, 30 cambios al día, bio hasta 4.000.

## 5 · Validaciones y errores

Necesito un código por cada uno, para decirle a la persona qué ha fallado y cómo arreglarlo.

| Dónde | Regla | Si falla |
|---|---|---|
| Alta · todos | correo con formato válido y sin cuenta previa; contraseña de 8 o más | error en el campo; correo ya usado: entrar o recuperar contraseña |
| Alta · artista | nombre obligatorio; audio MP3 o WAV, 30 s a 12 min, hasta 200 MB; o enlace `https` | error en el campo |
| Alta · sello | nombre del sello, nombre del artista, 1 a 3 géneros y audio con las mismas reglas | error en el campo |
| Subida del audio | termina | reintentar sin perder el formulario: ¿el alta se crea antes o después de la subida? |
| Oyente | abre la verificación en 24 h | enlace caducado: reenviar (como hoy) |
| Acceso tras aprobar | enlace de un solo uso, sin caducar | aviso y reenviar |
| Mi página · foto | JPG, PNG o WebP, hasta 15 MB | motivo y elegir otra |
| Mi página · datos | bio hasta 4.000; 30 cambios al día | motivo; la web conserva lo escrito |

## 6 · Lo que desaparece

- Para artista y sello: la verificación de correo en el alta y los estados `sin-verificar-email` y `sin-material`.
- Para quien tiene cuenta: el envío por el formulario de Radar y Mis envíos (la web retira el área antigua). El
  formulario público de Radar sin cuenta no se toca.
- La verificación del sello por la relación: ahora se verifica con la música de su artista.

## 7 · Lo que os pregunto

1. El contrato de todo lo anterior: alta con archivo o enlace (¿una ruta con multipart o crear y luego subir?), estados
   nuevos en `/me`, enlace de acceso y reenvío, y qué falta para publicar.
2. La caducidad del enlace de aprobación.
3. Qué pasa con las cuentas que hoy están en `sin-material` o `pendiente`: ¿pasan al flujo nuevo tal cual?
4. Cuándo lo tendríais. La web empieza en cuanto esté el contrato; lo construyo contra él en local, sin escribir en
   producción.

— el front de la web
