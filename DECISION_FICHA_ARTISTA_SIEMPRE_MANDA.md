# A la web y al servidor · decisión de Pablo: la ficha del artista manda siempre, dato a dato

15-sep-2026 · de la app, **con la decisión aprobada por Pablo hoy** · contesta a
`ALINEACION_ARTISTA_WEB_A_APP.md`

## La decisión

**La ficha manda en cada dato y lo que le falte se busca fuera. `priority` deja de cambiar el
orden.** Decidido por Pablo el 15.09.26, después de ver los casos. Su criterio: que TheAudioDB dé un
dato erróneo es poco probable, así que **rellenar lo que falta con TheAudioDB está bien**. Lo que no
tiene sentido es que un dato curado por la redacción pierda frente a uno de fuera.

| Caso | Foto | Bio |
|---|---|---|
| La ficha tiene el dato | **el de la ficha**, sin llamar a nadie | **el de la ficha**, sin llamar a nadie |
| La ficha existe pero le falta ese dato | TheAudioDB → carátula | TheAudioDB → Last.fm |
| El artista no tiene ficha | TheAudioDB → carátula | TheAudioDB → Last.fm |
| La ficha existe pero no llega (fallo de red) | `image` de `index.json`, sin salir fuera | ninguna hasta que llegue la ficha |

Con el índice de hoy: 151 artistas con foto y bio en la ficha, 74 solo con bio, 117 solo con foto y
2.383 sin ficha.

## Qué cambia en cada lado

- **App:** nada. Ya funciona así, y el caso sin red está en el código desde hoy (llega en la próxima
  versión).
- **Web:** en las fichas **sin** `priority`, pasar de «TheAudioDB → ficha» a «ficha → TheAudioDB», en
  foto y en bio. Y cubrir el caso de la ficha que no llega, si os parece como la app, con `index.json`.
  **La trayectoria y los enlaces no entran en esta decisión**: seguid como los tenéis.
- **Servidor:** actualizar `ARTISTAS_WEB_INSTRUCCIONES.md` §2.1, que hoy dice que la ficha solo
  rellena, y la nota del 15-sep de `APP_MOVIL_INSTRUCCIONES.md`, para que los dos contratos digan lo
  mismo. `priority` puede seguir existiendo en la ficha, pero ya no decide el orden de foto y bio.

## Lo que queda fuera

- **Crédito de la foto:** sigue sin enseñarse, en web y en app.
- **Relacionados de ASTRO** con grupos de K-pop: es del servidor, y la app no los usa.

— la app
