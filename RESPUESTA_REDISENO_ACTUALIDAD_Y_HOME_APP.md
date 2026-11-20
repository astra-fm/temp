# Respuesta · el rediseño de la app: Últimas 24 h, `clase` e imagen apaisada de los programas

**De:** servidor · **Para:** la app · **Fecha:** 20.11.26 · Sobre `PETICION_REDISENO_ACTUALIDAD_Y_HOME_APP.md`.

Las tres cosas están hechas y en producción. Una cambia lo que planteabais: el carrusel de Últimas 24 h ya no sale de
`actualidad.json`.

## 1. Últimas 24 h: endpoint propio, `ultima-hora.json`

Pablo ha decidido que el carrusel lo lleve la redacción. Publica cada noticia desde Studio → Actualidad → Última hora,
marcando el destino «Web y app». Son noticias que están pasando: de otros medios o de Astra. Cada una sale durante
24 horas desde que se publica y luego desaparece sola. Pablo puede retirarla antes.

- `GET https://listen.astra.fm/ultima-hora.json`: público, se revalida con ETag (`max-age=0, must-revalidate`).
- Las noticias vienen ya filtradas a las últimas 24 horas, la más reciente primero. Si viene vacío, no pintéis el carrusel.
- Cada tarjeta trae `titulo` y `resumen` (siempre en español), `imagen` (URL absoluta o `null`), `medio`, `url` y
  `publicadoEn` / `caducaEn` en ISO UTC con `Z`.
- Al tocarla: `tipo: "externa"` abre `url` en el navegador. `tipo: "astra"` abre el post dentro, con `slug` de
  `actualidad.json`.

Contrato completo: `ULTIMA_HORA_INSTRUCCIONES.md` (portal, 20-nov). También está enlazado desde
`APP_MOVIL_INSTRUCCIONES.md` §1.

De paso, la respuesta a vuestra pregunta sobre `fecha` de `actualidad.json`: va en **UTC**, como habíais deducido. En las
91 piezas migradas del manager antiguo (las que traen `managerId`, todas anteriores al 26-ago) no se puede asegurar la
zona. Ya está escrito en `ACTUALIDAD_WEB_INSTRUCCIONES.md` y en `APP_MOVIL_INSTRUCCIONES.md` §1. No añadimos
`publicadoEn` a `actualidad.json`: para el carrusel ya no hace falta.

## 2. `clase` en los contratos de Actualidad y de la app

Ya figura en `ACTUALIDAD_WEB_INSTRUCCIONES.md` §2 y en `APP_MOVIL_INSTRUCCIONES.md` §1.

- **Valores:** `"album"`, `"ep"`, `"single"` o `"adelanto"`. Son cuatro: `single` es un single suelto, y `adelanto` es el
  single que anuncia un disco que aún no ha salido. Hoy hay 12 `album`, 11 `adelanto` y 2 `single`.
- **Cuándo viene:** la traen siempre los posts de `lanzamientos` publicados desde Studio → Álbumes desde el 7-nov, que es
  el camino normal. No la llevan los anteriores ni uno de lanzamientos escrito a mano en Actualidad: tratadlos como
  «Lanzamiento» a secas, sin chip.

## 3. Imagen apaisada de cada programa: `imagenApaisada` en `programas.json`

- Es un campo nuevo: una ruta relativa (`/programas/images/<slug>_apaisada-<marca>.jpg`, prefijad
  `https://listen.astra.fm`) o `null`.
- Pablo la sube en Studio → Programación, en la ficha de cada programa: 16:9, hasta 1920 de ancho y sin recortar.
- **Hoy los nueve programas vienen a `null`:** seguid recortando la cuadrada hasta que las suba.
- Cada subida cambia el nombre del fichero, así que usad la ruta tal cual llega.
- Las imágenes cuadrada (`imagenDestacada`) y del agente también cambian ahora de nombre al resubirlas: la regla es la
  misma.

Contrato: `APP_MOVIL_INSTRUCCIONES.md` §2 y `PROGRAMAS_WEB_INSTRUCCIONES.md`.

— el servidor
