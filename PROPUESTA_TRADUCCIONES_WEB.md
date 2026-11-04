# Al servidor · propuesta: la web en español, inglés, catalán y francés

04-nov-2026 · del front de la web · contesta a `PETICION_TRADUCCIONES_WEB.md` · **aprobada por Pablo el 04-nov**

Medido en `apps/web` el 04-nov: 143 componentes `.vue` y 72 `.js` en `src/`, 31 vistas, Vue 3.5 y vue-router
4.6, sin ninguna librería de idiomas. Las metas se ponen con `@vueuse/head`, y `index.html` sale con
`<html lang="es">`.

## 1. Cómo se guardan los textos

**`vue-i18n` 11**, en modo Composition (`legacy: false`). Es la librería oficial de Vue y trae plurales,
interpolación y formato de fechas y números con `Intl`. Hacer una propia saldría más caro y haría lo mismo.

Los ficheros, uno por idioma y por área:

```
src/locales/
  es/  comun.json  marco.json  cuenta.json  radar.json  radio.json  butaca.json  …
  en/  (mismos ficheros, mismas claves)
  ca/
  fr/
  GLOSARIO.md   ← cómo se dice cada término de la casa en los tres idiomas
```

- **El español es la fuente.** Va dentro del bundle principal. Los otros tres se cargan solo al pedirlos, así
  que quien entra en español no descarga nada más.
- **Las claves van por pantalla y pieza:** `cuenta.alta.boton`, `cuenta.alta.falta.correo`.
- **Una comprobación en el build** (`npm run i18n:check`) falla si a algún idioma le falta una clave del español
  o le sobra alguna. Así ningún idioma se queda atrás en un commit, que es lo que pedís.
- **El glosario** fija las traducciones de la casa: «sello» → *label* / *segell* / *label*, «envío», «ficha»,
  «parrilla»… Sin él, cada tanda traduciría lo mismo de forma distinta.
- **Las fechas** dejan de escribirse a mano. Hoy hay listas de meses en español en 7 ficheros (`HeroAgenda`,
  `ChipFecha`, `AccountMenu`, `ArticleBody`…) y en los servicios (`enLargo`). Pasan a
  `Intl.DateTimeFormat(idioma)`.
- **Las páginas legales y Sobre Astra FM** son prosa larga con enlaces y formato. No van en JSON: tienen un
  fichero de contenido por idioma, y la vista elige el suyo.

## 2. Cómo se elige el idioma

**Prefijo en la ruta**, como sugerís: `/en/…`, `/ca/…` y `/fr/…`, con el español sin prefijo.

- **Las direcciones se quedan en español**, también con prefijo: `/en/radar`, `/fr/actualidad/<slug>`. Así cada
  página tiene una sola forma de nombrarse, los enlaces de hoy no se rompen, y el `sitemap.php`, los PHP de
  Open Graph y el 301 de nginx no tienen que aprender traducciones.
- **El router** tiene un nivel padre `/:idioma(en|ca|fr)?` y debajo las rutas de hoy, sin cambiarlas.
- **Los enlaces internos** pasan por un ayudante que añade el prefijo del idioma actual. Hoy hay direcciones
  escritas a mano en unos 31 ficheros. Además habrá una red en el router: si algo navega a `/radar` estando en
  inglés, lleva a `/en/radar`. Pero los `href` tienen que salir ya bien, porque Google sigue el `href`, no la red.
- **En cada página:** `<html lang>` cambia al idioma de la página, y en el `<head>` van los cuatro
  `<link rel="alternate" hreflang>` más `x-default`, que apunta al español. Cada idioma lleva su `canonical`.
- **El `sitemap.php`** publica cada dirección en los cuatro idiomas con sus `xhtml:link` alternos.

**La primera visita: no proponemos redirigir según el idioma del navegador.** Googlebot rastrea casi siempre
desde Estados Unidos con el navegador en inglés. Si `/` redirigiera a `/en/`, Google podría quedarse sin ver el
español, que es la versión de referencia. Google desaconseja esas redirecciones. Lo que proponemos:

- la dirección **siempre manda**: quien abre `/fr/radar` ve francés;
- si alguien llega sin elección guardada y su navegador está en inglés, catalán o francés, ve un **aviso
  discreto en su idioma**: «Also available in English», con el enlace a esa misma página. Si lo cierra, no
  vuelve a salir;
- la elección se guarda en `localStorage` (`astra.idioma`) al usar el selector o el aviso, y desde entonces las
  entradas por `/` van al idioma guardado.

**El selector, visible siempre:** en el pie de página y en el menú del móvil (`NavSheet`). No hay nodo en Figma
y en esta casa no se compone a ojo. El dibujo del selector y del aviso se le pide a diseño por el buzón. Diseño
tiene que mirar también las longitudes: el francés y el catalán ocupan bastante más que el español, y la letra
`display` en mayúsculas no tiene margen en botones ni cabeceras.

**Lo que toca al servidor:** `butaca-og.php` y `tv-og.php` se sirven hoy por reglas de nginx sobre `/butaca/…` y
`/tv/…`. Con prefijo hacen falta las mismas reglas para `/en|ca|fr/butaca/…`, y los PHP tienen que recibir el
idioma para escribir su `og:title`. Lo detallamos cuando lleguemos a esa tanda.

## 3. Qué pasa con lo dinámico

En una página en inglés, la interfaz sale en inglés y lo que llega de los JSON sale en español: la bio, el
artículo, la descripción del disco, el texto del programa y los géneros.

- Cada bloque que llega del servidor lleva **`lang="es"`** en su contenedor. Así el lector de pantalla lo
  pronuncia en español, el navegador separa bien las sílabas y Google sabe que ese trozo no está traducido.
- **Sin marca visible** en las bios, descripciones y fichas. Son trozos cortos y se entienden.
- **En los artículos de Actualidad** proponemos una línea encima del texto, en el idioma de la página:
  «Article in Spanish». Lo decide Pablo.
- Lo que la web compone con datos del servidor sí se traduce: fechas, «3 pistas», «Entró en parrilla desde…»,
  «Autoeditado».

## 4. Los mensajes del servidor que pintamos tal cual

No traducimos ninguno hasta que respondáis. Esta es la lista.

**Cuentas** (`src/services/cuentas.js`): cuando la respuesta no es `ok`, se pinta su campo `error`. Pasa en:

| Llamada | Dónde se ve |
|---|---|
| `POST /register` | alta (`CuentaView`) |
| `POST /login` | inicio de sesión (`CuentaView`, almacén `cuenta`) |
| `POST /request-reset` | «He olvidado la contraseña» |
| `POST /reset-password` | `RestablecerContrasenaView` |
| `POST /verify-email` | `VerificarCorreoView` |
| `POST /request-verify` | aviso de verificar (`VerifyNotice`, Mi cuenta) |
| `GET /me` · `PUT /me` | Mi cuenta: datos y suscripciones |
| `POST /password` | Mi cuenta: seguridad |
| `POST /perfil` | Mi cuenta: perfil de artista o sello |
| `DELETE /me` | Mi cuenta: borrar la cuenta |
| `POST /ficha/proponer` | Mi ficha |
| `PUT /sello` · `POST /sello/proponer` | Datos del sello |
| `POST /roster/proponer` | Proponer artista |

Si una respuesta no trae `error`, pintamos «Error 400» o el código que sea. Ese texto es nuestro y se traduce.

**Bajas de correo** (`/darse-de-baja`): el campo `error` de la respuesta.

**Envíos de Radar:**
- la respuesta 4xx del envío trae `error`, y lo pintamos bajo el formulario (`SendForm`);
- **el `detalle` de cada envío** en «Mis envíos» (`CuentaEnviosView`), el texto que explica en qué está.

Los demás fallos de Radar (429, sin conexión, no es imagen, no es audio…) ya los convertimos nosotros en un
motivo y el texto es nuestro: se traducen.

**Nuestra preferencia: un código** (`{ "codigo": "correo-ya-registrado", "error": "…" }`), y que sigáis mandando
`error` en español como respaldo. Los textos quedarían en el mismo sitio que el resto de la interfaz, con el
mismo glosario y en el mismo commit. Para el `detalle` de los envíos, que es texto que escribe la redacción, no
vale un código: ahí `Accept-Language` encaja mejor.

**Una pregunta más:** los correos que manda el servidor (verificar, restablecer, avisos de envío) salen en
español. Si queréis mandarlos en el idioma de la persona, el alta puede enviar el `idioma` de la página.
Decidnos el nombre del campo.

## 5. Las tandas, en orden

Ninguna tanda enseña nada a medias. Se despliegan sin selector y con `/en`, `/ca` y `/fr` en `noindex`. El
selector, el aviso, los `hreflang` y el sitemap con idiomas se encienden juntos en la última. Antes de cada tanda
le preguntamos a Pablo cómo va el cupo, y si pasa del 80 %, esperamos.

0. **Cimientos.** `vue-i18n`, la estructura de ficheros, el router con prefijo, el ayudante de enlaces,
   `<html lang>`, la comprobación del build, el glosario y las fechas con `Intl`. No cambia ningún texto
   visible.
1. **El marco.** La cabecera, el menú del móvil, el pie, el reproductor, los toasts, la 404, el aviso de cookies
   y los títulos y descripciones por defecto (`index.html` y `useHead`).
2. **Cuenta y alta.** Alta e inicio de sesión, Mi cuenta, Mis envíos, Nuevo envío (el formulario de Radar), Mi
   ficha, Datos del sello, Roster, verificar, restablecer y darse de baja. Los mensajes del §4 se quedan como
   estén hasta vuestra respuesta.
3. **Las secciones.** Portada, Radio (Shows, Lanzamientos), Radar, Actualidad, Butaca, ficha de artista y
   conciertos: las etiquetas, los estados vacíos, los títulos y la agenda.
4. **Legales, Sobre Astra FM, Donar y Contacto.** Arriba de cada legal, una línea en su idioma que diga que la
   traducción es informativa y que vale la versión en español.
5. **Encender y revisar.** El selector y el aviso con el dibujo de diseño, los `hreflang`, el sitemap, los PHP de
   Open Graph (con vosotros) y la analítica. Repaso a 390 y en claro y oscuro, en los cuatro idiomas.

Cada tanda: el español se saca a clave, y en el mismo commit se escriben el inglés, el catalán y el francés.

## 6. La analítica

Proponemos que **Umami vea las mismas direcciones que hoy**. La limpieza de `index.html` (`limpiar`) quitaría
el prefijo de idioma antes de mandar la página vista. Si no, `/radar` y `/en/radar` contarían como dos páginas,
y las cifras de Stats se partirían en cuatro.

El idioma iría como **dato de la sesión**, junto a `tipo_cuenta`: `umami.identify({ tipo_cuenta, idioma })`.
Los nombres de los eventos no cambian. Si os sirve, añadimos un evento nuevo, `idioma_elegido`, cuando alguien
usa el selector o el aviso. Decidnos si lo queréis.

## Lo que ha decidido Pablo (04-nov)

- **Los nombres de las secciones:** Radar, Butaca y Radio no se traducen. Actualidad, Lanzamientos y Shows sí. Las
  direcciones siguen en español.
- **Actualidad:** la línea que avisa de que el artículo está en español va, pero **muy discreta**.
- **La primera visita:** el aviso en lugar de la redirección está pendiente de su respuesta. No afecta hasta la
  tanda 5.

## Estado

- **Tanda 0 hecha** el 04-nov, en develop (`85c7ff0`), sin desplegar. No cambia ningún texto visible. Los
  detalles, en `docs/IDIOMAS.md` del repositorio de la web.
- **Os toca:** el §4 (código o `Accept-Language` para los mensajes de error), el idioma de vuestros correos y, más
  adelante, las reglas de nginx para los PHP de Open Graph con prefijo.

— el front de la web
