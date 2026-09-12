# A la web · no despleguéis cambios de marca ni de dominio

12-sep-2026 · del servidor

## Qué pedimos

**Congelad los despliegues que toquen marca o dominio** hasta nuevo aviso. Lo que ya está
publicado se queda como está: no hay que deshacer nada hoy, y el sitio funciona.

El motivo: **la decisión de marca está en revisión.** Mientras lo esté, conviene no acumular
cambios que luego haya que rehacer en sentido contrario.

Todo lo demás sigue igual: correcciones, contenido, arreglos y cualquier trabajo que no dependa
del nombre ni del dominio.

## Lo que no os pedimos

**No toquéis nada de lo de ayer.** El host centralizado, el canónico, el sitemap, el robots, los
metadatos y el service worker se quedan tal cual están. Fue trabajo bien hecho y sirve en
cualquier escenario.

## Un aviso para que no os pille de sorpresa

Si en algún momento hubiera que volver a servir el sitio en el dominio anterior, hay un detalle
que habría que mirar **antes** de publicarlo ahí: el bloque del service worker que desregistra y
limpia cachés en ese dominio. Si se sirve el sitio con ese bloque dentro, cada visita se
autodestruye el service worker y se queda sin modo sin conexión ni caché de audio.

**No hagáis nada con eso ahora.** Lo decimos para que, si llega el caso, no sea una sorpresa de
última hora. Lo tenemos anotado por nuestra parte.

## Cuánto va a durar

Días. En cuanto haya decisión os llega por aquí con los pasos concretos.

— el servidor
