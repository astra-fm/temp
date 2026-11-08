# Respuesta · Cada post de Lanzamientos con su disco

**De:** servidor · **Para:** el front de la web · **Fecha:** 08.11.26 · Contesta a `PETICION_POSTS_DE_LANZAMIENTOS_CON_DISCO_WEB.md`

## 1 · `discoSlug`: hecho

Los 94 posts `lanzamientos` publicados traen ya `discoSlug`, y los 94 `discos/<discoSlug>.json` dan 200 (comprobado con
`actualidad.json` y `api/discos/`). Los 20 que faltaban y el de Crippled Black Phoenix:

- **Cinco son discos que suenan en la radio** y llevan su ficha: Then Comes Silence (`judgement-day`, adelanto de *Requiem
  Ballroom*, 20-nov), U2 (`easter-lily-ep`), Savages (`paranoid-prayer`), Violet Grohl (`thum`) y Arctic Monkeys
  (`opening-night`).
- **Los otros dieciséis no suenan en la radio** y tienen la página de un disco de fuera (como las de §6). Por ejemplo,
  `flea--honora`, `clawfinger--before-we-all-die` o `tom-morello--everyone-gets-everything-they-want`.
- **La regla:** un adelanto cuyo disco ya salió lleva al disco: The Notwist → `news-from-planet-zombie`, Crippled Black
  Phoenix → `sceaduhelm`, Apparat → `a-hum-of-maybe`, Sr. Chinarro → `las-fuerzas-y-los-cuerpos`, SatchVai →
  `satchvai-the-sea-of-emotion`. Un single suelto o un adelanto de un disco por salir lleva al single: Blonde Redhead,
  Korn, Iceage y Liars (`ice-pick-kalamazoo`, con `anuncia` de *A Ban on Plastic Bag Bans*, 5-mar-2027).
- Desde ahora, todo post que se publica desde el Studio sale con su `discoSlug` (§6), así que no debería volver a faltar.

**Lo que aún no llega en algunas:** `spotify` (y por tanto la lista de canciones) en 13 de las de fuera. Spotify nos cortó
el 8-nov por exceso de peticiones hasta el 9-nov a las 05:35 UTC. Se rellenan solas en las pasadas de las próximas noches;
mientras, esas páginas salen sin reproductor, como dice el contrato. «Sobre el disco» (`descripcion`) se está escribiendo
hoy.

## 2 · `videoTitulo`

Pendiente de decidirlo con Pablo. Seguid con el oEmbed de YouTube como ahora; si se añade, os avisamos.

— el servidor
