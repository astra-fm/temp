# A la web y a la app · la ficha manda siempre: contratos actualizados

15-sep-2026 · del servidor · responde a `DECISION_FICHA_ARTISTA_SIEMPRE_MANDA.md`, `ALINEACION_ARTISTA_WEB_A_APP.md` y `PUBLICADO_IMAGEN_PRIORITY_WEB.md`

Recibida la decisión de Pablo. Gracias a los dos por cruzar cómo lo resolvía cada uno: así se vio.

## Contratos, ya iguales

- **`ARTISTAS_WEB_INSTRUCCIONES.md` §2.1**, reescrito: la ficha manda en cada dato, lo que falta se
  busca fuera, `priority` ya no ordena, y la fila de «la ficha no llega» con `index.json`. También la
  cabecera, las notas del §2 y el ejemplo de código del §4.
- **`APP_MOVIL_INSTRUCCIONES.md`**, nota del 15-sep: la misma regla, con referencia al §2.1.
- Trayectoria (§2.2) y enlaces (§7) no cambian. Crédito de la foto: sigue sin enseñarse.

Leedlos en el portal; esto es solo el aviso.

## Qué queda por hacer

- **Web:** en las fichas sin `priority`, pasar a «ficha → TheAudioDB» en foto y bio, y cubrir la
  ficha que no llega con `index.json`, como la app.
- **App:** nada nuevo; el caso sin red va en vuestra próxima versión.

## Relacionados de ASTRO, arreglado

`/relacionados/astro` ya **no devuelve grupos de K-pop**. Last.fm tiene una sola página «Astro» para
al menos cinco artistas y sus similares salen del más escuchado. Ahora el servidor pregunta por el
MBID cuando lo tiene y, si la página mezcla homónimos y ningún similar está en nuestro catálogo,
devuelve `related` vacío (§8: no pintar el bloque). Nirvana y New Order siguen con los suyos.
Recalculadas también otras 320 respuestas en caché que no tenían ningún artista de casa.

## En el Studio

Quitada la casilla «Priorizar esta ficha»: con la regla nueva no hacía nada. El campo `priority`
sigue viajando en la ficha; ignoradlo.

Web: cuando esté lo de las fichas sin `priority`, un `PUBLICADO_FICHA_MANDA_WEB.md` aquí.

— el servidor
