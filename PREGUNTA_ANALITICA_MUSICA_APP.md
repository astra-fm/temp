# A la app · la analítica de Música llega, pero solo pantallas

05-10-2026 · del servidor · sigue a `HECHO_ANALITICA_MUSICA_APP.md` y `RESPUESTA_ANALITICA_MUSICA_APP.md`

**Contrato:** `https://listen.astra.fm/docs/ANALITICA_APP_INSTRUCCIONES.md` (sin cambios desde el 27-09).

## Qué vemos en «Astra FM · app» (Umami, últimos 14 días)

| Día | Pantallas (`/musica`, `/musica/<id>`) | Eventos |
|---|---|---|
| 28-09 | 20 (8 sesiones) | `musica_sin_cuenta` × 2 |
| 01-10 | 5 (3 sesiones) | `musica_sin_cuenta` × 2 |
| 02-10 | 13 (4 sesiones) | `musica_sin_cuenta` × 2 |
| 04-10 | 2 (2 sesiones) | — |

Son 46 registros desde el primero (28-09, 12:10 hora de Barcelona).

**El envío funciona:** llegan pantallas y avisos de cuenta, fuera de la oficina y sin marca de personal.

**No ha llegado nunca ningún otro evento del contrato:**
- `musica_escucha`, `musica_1min`, `musica_completa`, `musica_salto`;
- `coleccion_descarga`, `coleccion_descargada`;
- ningún error, cancelación ni borrado.

## Qué necesitamos saber

1. **Qué versión lleva la analítica y dónde está.** ¿Solo en TestFlight y en las pruebas de Android, o
   también publicada en App Store y Google Play? ¿Cuántos aparatos la tienen, más o menos?
2. **Si se hizo la prueba del §7** desde un aparato sin marca de personal y fuera de la oficina. ¿Sonó alguna
   canción de una colección?
3. **Si hubo escuchas en esos días,** ¿podría ser que los eventos de escucha no salgan? Por ejemplo:
   - porque el reproductor los manda desde otro sitio que el de las pantallas;
   - o porque se quedan en la cola sin red y no se vacían.

   Si no hubo ninguna escucha fuera de la oficina, decídnoslo y lo damos por normal.

Respuesta: un `RESPUESTA_PREGUNTA_ANALITICA_MUSICA_APP.md` aquí, aunque sea «no hubo escuchas».

— servidor
