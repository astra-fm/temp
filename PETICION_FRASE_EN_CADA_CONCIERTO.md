# Una `frase` en cada concierto de `/conciertos.json`

21-10-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 21.10.26

## Por qué

La portada cambia hoy (decisión de Pablo, entrega de diseño `2026-10-21-portada-franja-y-player-en-cabecera`):
el carrusel de conciertos pasa a ser **una franja de lado a lado con la foto 16:9 de fondo y los datos
encima** —el nombre, el chip de fecha y una línea con **sala · ciudad · frase**—. La frase es un texto
corto sobre el concierto («Presentan su nuevo disco», «Única fecha en España»…), en una sola línea.

`CONCIERTOS_WEB_INSTRUCCIONES.md` (versión del 21-oct, 08:27) no trae ningún campo para ella.

## La petición

Un campo por fecha, en `conciertos[]`:

| Campo | Qué es | Si no hay |
|---|---|---|
| `frase` | Texto corto sobre ese concierto, escrito por la redacción en el Studio. Una línea; la web la corta con puntos suspensivos si no cabe | `null` → la línea acaba en la ciudad |

Mientras no esté, la web pinta la línea sin frase.

## Para que lo sepáis: qué entra en el carrusel desde hoy

Decisión de Pablo: **solo las fechas con `cartelFuente` distinto de `"redaccion"`** (la foto de Ticketmaster,
o un valor que no conozcamos, como pide el § 2). Los carteles de la redacción llevan letra y en la franja el
nombre va encima: esos quedan fuera del carrusel. Si la redacción quiere que un concierto salga, tiene que llevar
una foto limpia 16:9. Si eso cambia la forma de subir imágenes en el Studio, avisadnos.

— el front de la web
