# Al servidor · hecho: el programa por `onair.json` y fuera `playing_next`

19-11-2026, de la app. Contesta a `AVISO_LA_ANTENA_LA_LLEVA_EL_SERVIDOR_WEB_Y_APP.md` y a
`AVISO_PLAYING_NEXT_DONDE_CAMBIARLO_WEB_Y_APP.md`. `APP_MOVIL_INSTRUCCIONES.md` y
`EMISORA_WEB_INSTRUCCIONES.md` bajados hoy, con la nota del 19-nov.

## Vuestra pregunta

- **`playing_next`: lo leíamos y no lo pintábamos.** Era lo que visteis en `radioApi.ts`. **Ya no se
  lee.**
- **El horario de AzuraCast: no.** No llamamos a `/api/station/3/schedule` ni usamos las horas de sus
  listas. Pero **el programa en antena sí lo decíamos por el reloj**, con las horas de
  `programas.json`. Eso era lo que avisabais en el punto 2.

## Punto por punto

| Punto | Estado |
|---|---|
| Quitar `next`, `nextAt` y `nextPlaylistName` de `fetchNowPlaying` | **hecho** |
| El comentario caducado de `RadioScreen.tsx` | **hecho** · ahora dice que «Lo que sigue» sale de `/queue.json` |
| El programa en antena, por `programa` de `onair.json` | **hecho** · ver debajo |

El programa sale de `ahora.programa` y se busca en `programas.json` por nombre, sin tildes ni
mayúsculas. Lo usan:

- Home: el programa del hero y «A continuación».
- Radio: el programa en antena.
- Shows: el carril, la regla del día, la parrilla y el timeline.
- La ficha de programa: EN DIRECTO.

En Shows, el estado de cada programa (sonó, en antena, viene) se cuenta desde el que suena, no desde la
hora. Con una cuña, `ahora` viene vacío y nos quedamos con el último programa conocido. **El reloj solo
queda de respaldo** mientras no ha contestado `onair.json`.

Commit `6ca128f` en `develop`. Probado en el emulador de Android forzando otro programa: todo sigue a
`onair.json` y no a la hora. **Todavía no está publicado** en TestFlight ni en Play.

— la app
