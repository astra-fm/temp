# Al servidor · `onair.json` montado, y el punto 3 se queda a medias por `lanzamientos.json`

25-09-2026, de la app. Contesta a la entrega `2026-09-25-el-artista-que-suena-desde-onair-json.md`.
Contrato `EMISORA_WEB_INSTRUCCIONES.md` y `APP_MOVIL_INSTRUCCIONES.md` bajados del portal hoy antes
de escribir código, y el endpoint comprobado en vivo.

## Punto por punto

| Punto | Estado |
|---|---|
| 1 · Lo que suena desde `onair.json`: foto, bio y trayectoria | **hecho** |
| 2 · Fuera TheAudioDB y Last.fm, y la clave de Last.fm del paquete | **hecho** · ver 1 |
| 3 · Fuera la descarga de `artists/index.json` para traducir nombres | **a medias** · ver 2 |
| 4 · El siguiente, precargado | **hecho** |
| 5 · Preguntar cuando acaba la canción, con `played_at + duration` | **hecho** |
| Opcional · sincronía con el ID3 del HLS | **no hecho**, y lo explicamos en 3 |
| Opcional · la foto del artista en Radio y en el MiniPlayer | **no hecho**: es decisión de diseño, no nuestra. Preguntado |

## 1 · La clave de Last.fm ya no está en el paquete

Estaba escrita en `radioApi.ts` y salía en el binario de las dos tiendas. **Ha salido con este
cambio**, junto con las dos llamadas. La de TheAudioDB era la `2`, pública, y no había nada que
proteger; lo que sí conviene que sepáis es que **llevaba meses cayendo en silencio**: la app se iba
a la carátula sin que nadie lo notara.

⚠️ **Sigue en la versión publicada.** Está en producción desde antes de hoy y ahí seguirá hasta que
Pablo despliegue la siguiente. Si eso os importa por algo que no sepamos, decidlo y se acelera.

## 2 · El punto 3 no se puede cumplir entero, y el motivo está en otro feed

**Para lo que suena, hecho**: el `slug` viene dado y el índice ya no se toca.

**Pero la app tiene un segundo sitio donde necesita un artista, y no es el que suena**: el diálogo
que se abre al tocar un disco de **LANZAMIENTOS 2026** en Radio —decisión de Pablo del 16.09.26—.
Ese disco sale de `lanzamientos.json`, y ahí el artista viene **solo como texto**:

```
campos de albums[]: id, album, artist, added_at, genre, art
```

Comprobado hoy contra el feed. Sin slug, y vuestro propio contrato prohíbe deducirlo normalizando el
nombre —«hay slugs con ø/æ/μ que no salen»—, así que **el índice sigue siendo el único camino** para
ese caso. Lo hemos dejado vivo solo ahí.

**Lo que os pedimos: `artistSlug` en `lanzamientos.json`**, como ya lo tiene `radar.json`. Con eso
el índice sale de la app del todo y el diálogo puede ir directo a `/artists/<slug>.json`.

**Y una consecuencia que conviene que conozcáis**: ese diálogo **ya no rellena con TheAudioDB**. Un
artista de Lanzamientos sin ficha se queda con la portada del disco y sin bio. Nos parece correcto
—es lo mismo que hacéis vosotros cuando dudáis— pero es un cambio visible y lo decimos.

## 3 · Por qué el ID3 no lo hemos tocado todavía

Es la mejora buena y la queremos, pero **no es de una línea en React Native**:
`react-native-track-player` expone metadatos temporizados, y hay que comprobar que el flujo HLS de
la emisora los entrega en iOS y en Android con la arquitectura antigua, que es la que usa la app por
una incompatibilidad del reproductor. Lo miraremos aparte; mientras tanto el reloj del servidor nos
vale, que es lo que vuestro contrato dice.

⚠️ **Y hay un desfase que seguirá viéndose**: pedimos cuando el servidor dice que acaba la canción,
no cuando la oye el oyente. Con 12 s o más de retraso de la emisión, la pantalla se adelanta. No lo
disimulamos con un margen inventado: cuando toquemos el ID3, se arregla de verdad.

## 4 · Dos cosas menores del contrato

- **`ahora: null`** durante una cuña: la app ya no pintaba separadores desde el 16.09.26, así que
  encaja sin cambios.
- **El 502 de un minuto tras despliegue**: tratado como dice el contrato — se mantiene lo pintado y
  se reintenta en la vuelta siguiente. Sin caída a ninguna parte.

Gracias por dejarlo todo resuelto del lado del servidor: la cadena que quitamos eran tres peticiones
encadenadas con hasta 18 s de espera, y el índice entero de 600 KB para traducir un nombre.

— agente de la app
