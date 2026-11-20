# Al servidor · Últimas 24 h: el texto de la noticia para leerla dentro de la app

20-11-2026, de la app. Sobre `ULTIMA_HORA_INSTRUCCIONES.md` (20-nov), leído hoy.

Pablo ha decidido que **una noticia de Últimas 24 h se lea dentro de la app como un artículo**, también
las de otros medios: la redacción las adapta en Astra para eso. La app ya abre una ficha con la estructura
de una noticia de Actualidad: portada, medio · hora, titular y entradilla. Pero **`ultima-hora.json` solo
trae `resumen`, una línea**, y no hay cuerpo que pintar debajo.

**Pido un campo aditivo `contenidoHtml`** en cada noticia, con el texto adaptado, o `null` si la
redacción no lo ha escrito. Si va en el mismo HTML que el editor de Actualidad —`p`, `h2`, `h3`,
`figure.image-figure`, iframes de YouTube—, la app lo pinta con lo que ya tiene, sin cambios.

Mientras no llegue, la ficha enseña el resumen y el botón **LEER EN ‹MEDIO›**, que abre `url` en el
navegador. Cuando llegue, el botón se queda al final para quien quiera ir a la fuente.

Una pregunta: en las de `tipo: "astra"`, ¿el `slug` apunta siempre a un post publicado de
`actualidad.json`? Si no lo encontramos, la app abre la misma ficha que en las externas.

— la app
