# Analítica de Música · lo que dice /play (respuesta del servidor)

05-10-2026 · del servidor · para la app · responde a `RESPUESTA_PREGUNTA_ANALITICA_MUSICA_APP.md`

Gracias. Hemos hecho la comprobación que proponíais: cada `/play/<id>` que el servidor cuenta, frente a lo que llegó a
«Astra FM · app» en Umami.

## Lo que hay

**Escuchas contadas en `/play`** (sin `X-Astra-Interno`, fuera de «No medir»), del 28-09 a hoy:

| Cuándo (hora de Barcelona) | Sistema | Aparatos | Qué |
|---|---|---|---|
| 28-09, 13:24–13:25 | iOS | 1 | 4 canciones de la colección 213, saltando cada 10–18 s |
| 28-09, 13:56 | sin identificar (el User-Agent no dice ni iOS ni Android) | 1 | 1 canción de la 211 |
| 29-09, 08:08 | iOS | 1 | 1 canción de la 215 |

**Desde el 29-09 a las 08:08, ninguna.** De Android, ninguna nunca.

**En Umami, todo lo que ha llegado de la app es de Android.** Todas las sesiones dicen «Android OS». De iOS no ha llegado
nunca nada, ni pantallas.

## Lo que sacamos

- **Android: cuadra con vuestra lectura.** Llegan pantallas y `musica_sin_cuenta`, y no hay ningún `/play` de Android. Nadie
  ha escuchado una colección desde Android, así que no falta ningún `musica_escucha`. Nada que buscar.
- **iOS: hay escuchas en el servidor y ningún evento en Umami.** Puede ser una de dos cosas:
  1. **Esos iPhone llevaban una build anterior a la 59**, sin analítica (y sin `X-Astra-Interno`, que por eso se
     contaron). Es lo más probable: el 28-09 por la mañana era muy pronto para tener la 59.
  2. **La analítica no sale en iOS.** Como de iOS no ha llegado jamás ni una pantalla, no lo podemos descartar desde aquí.

## Lo que os pedimos

- ¿Sabéis **qué build de iOS tenían los testers** el 28-09 y el 29-09?
- En la prueba del §7, **probad también un iPhone con la build actual (70)** desde fuera de la oficina y sin marca de
  personal. Abrid Música, una colección y una canción. Con eso sale de dudas si iOS envía.

Avisad aquí con la hora de la prueba y lo miramos en Studio → Stats en el momento.

— servidor
