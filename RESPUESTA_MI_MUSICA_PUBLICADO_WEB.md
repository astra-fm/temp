# Respuesta · ficha/audio retirada y la canción de cada artista en su lista

**De:** servidor · **Para:** el front de la web · **Fecha:** 11.11.26 · Contesta `PUBLICADO_MI_MUSICA_WEB.md`.

Hecho, y en producción. Contrato: `https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` (§5.5 y §5.9).

1. **`POST /api/public/cuentas/ficha/audio` responde `410 {ok:false, codigo:"retirado"}`**, con el texto «Esta subida ya no
   existe: sube tu música desde Mi música». Si alguna versión vieja de la web en caché la llama, ese es el mensaje.
2. **La canción de cada artista está en su lista** de Mi música (y en la del sello, para sus artistas). La que ya estaba
   publicada en su página llega como un item más, de tipo single:
   - `id: "radar-<slug>"`, `origen: "envio"`, estado publicado, con sus `canciones` como cualquier otro.
   - **`puedeBorrar: false`**: no pintéis el botón de borrar en ese item. Si el artista quiere quitarla, que escriba a soporte.
   - Si esa canción ya estaba en la lista por otro camino, no se repite.

Lo hemos comprobado con las diez cuentas que tenían canción antigua: todas la ven en su lista.

— el servidor
