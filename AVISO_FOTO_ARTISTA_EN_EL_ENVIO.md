# A la web · formulario de Radar: portada y foto del artista, obligatorias

01-11-2026 · del servidor · decisión de Pablo

Desde hoy el formulario de envío (`/cuenta/envios/nuevo`) pide **dos imágenes obligatorias**: la **portada** del
lanzamiento, que ya existía como opcional, y una **foto del artista o de la banda**, que es nueva. El servidor ya
está en producción; falta el formulario. El contrato completo está en el portal (`/docs`,
`CANAL_ABIERTO_WEB_INSTRUCCIONES.md` §4, y `CUENTAS_WEB_INSTRUCCIONES.md` §2 para el área).

## Qué cambia en el formulario

1. **Foto del artista, campo nuevo.** Se sube después del paso 1, como la portada:
   `POST https://listen.astra.fm/canal-abierto/submit/{id}/foto`, imagen en crudo, hasta 15 MB. No se recorta: se
   guarda con su proporción. Que el campo deje claro que es una foto del artista o de la banda, no la portada.
2. **La foto no siempre hace falta.** Si el artista ya tiene foto en su ficha (su segundo envío), no se le pide.
   Para saberlo, cuando el nombre del artista esté escrito:
   `GET https://listen.astra.fm/canal-abierto/submit/foto-artista?artist=<nombre>` → `{data:{necesaria, foto}}`.
3. **La portada pasa a obligatoria.** Mismo endpoint de siempre (`/submit/{id}/cover`).
4. **No se deja enviar sin ellas.** Orden tras el `201`: portada, foto, audio. Si una subida falla, se reintenta al
   mismo id, sin repetir el paso 1.

Cada respuesta (el `201` del paso 1 y las dos subidas de imagen) trae ahora `faltan`, con lo que queda por subir:
`["cover", "foto"]`, `["foto"]` o `[]`.

## Qué cambia en el área

Mientras a un envío le falte alguna imagen, no pasa a revisión: la solicitud de artista sigue en `sin-material`. En
`cuenta.envios.items` cada envío trae `faltan` (casi siempre vacío) y, si falta algo, su `id` y un `detalle` que lo
dice. Pintad ahí un botón para subir lo que falta, contra los mismos dos endpoints. Los envíos anteriores a hoy llegan
con `faltan: []`.

## Lo que pasa después

La redacción ve la foto en el buzón junto a la portada. Cuando la canción entra en programación, la foto pasa a la
ficha pública del artista si su ficha no tenía ninguna. Para la web no cambia nada: sale en el mismo campo `foto` de
siempre.

— el servidor
