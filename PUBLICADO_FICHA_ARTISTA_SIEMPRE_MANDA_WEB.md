# A la app y al servidor · la web ya aplica «la ficha manda siempre»

15-sep-2026 · del front de la web · contesta a `DECISION_FICHA_ARTISTA_SIEMPRE_MANDA.md`

**En producción desde hoy.** Web y app enseñan ya la misma foto y la misma bio.

## Qué hace la web

| Caso | Foto | Bio |
|---|---|---|
| La ficha tiene el dato | el de la ficha, sin llamar a nadie | el de la ficha, sin llamar a nadie |
| La ficha existe pero le falta ese dato | TheAudioDB → carátula | TheAudioDB → Last.fm |
| El artista no tiene ficha (404) | TheAudioDB → carátula | TheAudioDB → Last.fm |
| La ficha no llega (red o 5xx) | `image` de `index.json`, sin salir fuera | ninguna hasta que llegue la ficha |

- `priority` ya no cambia el orden de foto y bio.
- **Ficha que no llega:** la web distingue el 404 del fallo, y el fallo no se guarda, así que el
  refresco la vuelve a pedir. `index.json` solo se baja en ese caso, una vez por vista. Si tampoco
  llega, se sigue como sin ficha.
- **Trayectoria y enlaces, sin cambios**, como pedía la decisión. Un matiz: mientras la bio espera
  a la ficha, la trayectoria tampoco sale a TheAudioDB, para no pintar datos de un homónimo.

## Cómo lo hemos comprobado

En el navegador, reescribiendo `nowplaying`:

- **Soda Stereo** (sin `priority`, foto en la ficha y en TheAudioDB): foto y bio de la ficha.
- **Beach House** (ficha solo con bio): bio de la ficha, foto de TheAudioDB, sin llamar a Last.fm.
- **ASTRO con la ficha forzada a fallar:** foto de `index.json`, sin bio, sin llamar a TheAudioDB ni
  a Last.fm.
- **ASTRO normal:** foto y bio de la ficha, «Chile · En activo desde 2008 · Chilean band».

## Al servidor

Pendiente vuestro, según la decisión: actualizar `ARTISTAS_WEB_INSTRUCCIONES.md` §2.1 y la nota del
15-sep de `APP_MOVIL_INSTRUCCIONES.md`. Sigue abierto lo de los relacionados de ASTRO
(`PUBLICADO_IMAGEN_PRIORITY_WEB.md`).

— el front de la web
