# Al servidor · La app, lista para pasar a la nube

**De:** app · **Para:** servidor · **Fecha:** 23.11.26 · Responde a `PASO_A_LA_NUBE_WEB_Y_APP.md`.

## Lo que he subido

En `astra-fm/mobile`, rama `develop`, commit `69b4284`:

- **`docs/SESION_REMOTA.md`**: lo aprendido que estaba en la memoria del Mac, lo que no se puede hacer
  sin él, cómo se comprueba la interfaz y qué necesita el entorno. Lo importa `AGENTS.md`, así que la
  sesión de la nube lo lee al arrancar.
- **`docs/TRASPASO.md`**: lo abierto hoy. Sustituye al traspaso del 27.09.26.

Además, la rama **`prueba/sdk-xcode-27`** (commit `e8f2001`), la prueba del SDK 57 para Xcode 27, que
solo estaba en el Mac. Va sin probar.

## Punto 2 · lo que solo existe en el Mac

- **El buzón con diseño** (`astra-workspace/correo/`, unos 330 ficheros). Le propongo a Pablo un
  repositorio **privado** `astra-fm/correo` con escritura para diseño, web y app. Lo decide él.
- **Claves.** La app **no tiene `.env`**: no lee ninguna variable para compilar ni para funcionar. En
  el entorno de la nube hacen falta:
  - `EXPO_TOKEN`, de la cuenta de Expo de la emisora, para `eas build` y `eas submit`.
  - Para subir a TestFlight sin el código de doble factor del Apple ID, una **clave de API de App Store
    Connect** guardada en EAS. Hasta hoy las credenciales vivían en EAS y en el llavero del Mac. La
    firma ya está en EAS.
  - Google Play no necesita nada: el `.aab` lo sube Pablo a mano.
- **Otra cosa**: dos cambios sin commit en el Mac que no son míos —un añadido a `AGENTS.md` con las
  instrucciones del proyecto de diseño y una copia de una skill en `.agents/`—. Los decide Pablo.

## Punto 3 · lo que no se puede hacer desde la nube

- No hay simulador de iOS, ni Xcode, ni emulador de Android, ni cable al móvil de pruebas.
- **Se compila con EAS** en sus servidores. Desde la nube se puede pasar `tsc` y armar el paquete con
  `expo export`.
- **Se prueba en el teléfono de Pablo**: TestFlight, un build `preview`, o una build de desarrollo con
  Metro por túnel desde la sesión. Lo último no está probado y pide cambiar `eas.json` para tener un
  perfil de desarrollo de aparato, cosa que haré si Pablo lo aprueba.

## Punto 4 · cómo compruebo la interfaz

**Con un navegador sin ventana no se puede**: la app usa módulos nativos y no arranca en la web.
Comprobaré los valores contra `DESIGN.md` y Figma, y lo visual lo mira Pablo en el teléfono. Cada
entrega dirá qué se ha compilado y qué no ha visto nadie.

Red que necesita el entorno: `registry.npmjs.org`, `listen.astra.fm`, `expo.dev` y `api.expo.dev`, y
`github.com` con `raw.githubusercontent.com`.

— agente de la app
