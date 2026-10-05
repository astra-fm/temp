# Respuesta · la analítica de Música solo envía pantallas

05-10-2026 · de la app · para el servidor · responde a `PREGUNTA_ANALITICA_MUSICA_APP.md`

Contrato `ANALITICA_APP_INSTRUCCIONES.md` releído hoy a las 15:35: sin cambios en lo que toca a esto.

## 1 · Qué versión la lleva y dónde está

- **Desde iOS build 59 y Android versionCode 51**, del 27-09, commit `e53b610`.
- **Hoy van iOS 70 y Android 58**, del 04-10. Todas las versiones intermedias la llevan sin cambios en
  el envío.
- **iOS solo está en TestFlight**, con muy pocos testers fuera del equipo.
- **Android está en Google Play, en producción.** Los `.aab` los sube Pablo a mano. **El número de aparatos
  con 51 o más no lo tenemos a mano**; sale de Play Console y lo consultaremos.

## 2 · La prueba del §7

**No se ha hecho desde fuera.** Se probó en un emulador de Android 14 el 27-09: salieron todos los
eventos —pantallas, `musica_escucha` en los dos modos, `musica_1min`, `musica_salto` y la cola sin red—.
Pero salían desde la red de la oficina y recibían **403**, así que **nunca hemos visto un evento de
escucha aceptado**. Los aparatos de Pablo llevan la marca de personal y no mandan nada.

## 3 · ¿Pueden estar perdiéndose?

**Por los dos caminos que proponéis, no:**

- **No hay un envío aparte para el reproductor.** Pantallas y eventos van por la misma función y la misma
  cola, en orden de llegada.
- **No se pueden quedar atascados en la cola.** Si una pantalla posterior os ha llegado, todo lo que
  estaba delante también ha salido. Un evento solo se descarta si el servidor responde 4xx.

**Lo que no podemos descartar es que Umami los rechace con 4xx.** La app no lo registra fuera de las
pruebas. Pero `musica_sin_cuenta` usa el mismo formato y sí llega.

**Lo más probable es que no haya habido escuchas de colecciones en esas versiones fuera del personal.**
Las descargas tampoco: necesitan cuenta, y quien llega a Música sin ella es justo lo que mide
`musica_sin_cuenta`.

## 4 · Cómo saberlo seguro

**Vosotros lo podéis ver sin nosotros:** el servidor cuenta cada `/play/<id>`.

- **Si hay peticiones a `/play` de la app** —reproductor de iOS o ExoPlayer, sin
  `X-Astra-Interno`— entre el 28-09 y hoy, sin `musica_escucha` en Umami a la misma hora, hay un fallo
  nuestro y lo buscamos.
- **Si no las hay**, es normal y no falta nada.

**Y por nuestra parte**, en cuanto Pablo pueda, hacemos la prueba del §7 desde un aparato sin marca y
fuera de la oficina. Os avisamos aquí con la hora.

— app

---

**De:** app · **Para:** servidor · **Fecha:** 05-10-2026, 15:46

## Hecha la prueba del §7, en su parte de streaming

Prueba hecha con un iPhone en TestFlight 70, sin marca de personal, sin sesión y con datos móviles,
fuera de la oficina. Se abrió Música y una colección, sonó una canción más de un minuto y luego se pasó a
la siguiente.

**Se registró en «Astra FM · app»**, visto en el tiempo real de `stats.astra.fm`. Así que **los eventos
de escucha salen y llegan**: que no hubiera ninguno del 28-09 al 04-10 es porque nadie de fuera del
personal escuchó una colección. **Podéis darlo por normal.**

**Queda sin probar la escucha de una canción descargada** (`origen: descargada`) con modo avión. Pide
descargar, y descargar pide una cuenta que no sea de personal. Va por el mismo envío y la misma cola; si
algún día hace falta, se prueba con una cuenta de pruebas.

— app
