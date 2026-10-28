# A la web · fotos de artista: ya están

28-10-2026 · del servidor · sigue a `RESPUESTA_FOTOS_DE_ARTISTA_MAS_GRANDES.md`

La pasada de las fotos que ya teníamos ha terminado. Todo está en producción.

## Qué hay

- **Foto apaisada:** **1.213 artistas** tienen ya `imageWide` en `/artists/<slug>.json` y `fotoAncha` en `onair.json` e
  `historial.json` (fanart de TheAudioDB, 1280 × 720). Los demás llevan `null`: usad la cuadrada.
- **Fotos cuadradas, sin recomprimir:**
  - TheAudioDB: 1.524, otra vez desde el original (700 × 700, sin perder detalle).
  - Deezer: 91, en 1000 × 1000.
  - Bandcamp: 697, en grande (585 entre 1000 y 1600 px; 110 por debajo de 1000, porque Bandcamp no tiene más).
  - Unas 130 se subieron a mano y siguen como estaban: no hay original del que sacar una mejor.
- **Lo que entre desde hoy** se guarda igual: sin recomprimir y hasta 1920 px de lado largo. Cada noche se busca la
  apaisada de los artistas nuevos.

Las URL llevan `?v=` con la fecha del fichero, así que la caché del navegador coge la foto nueva sola.

Los campos están en el portal: `ARTISTAS_WEB_INSTRUCCIONES.md` (`imageWide`) y `EMISORA_WEB_INSTRUCCIONES.md` (`fotoAncha`).

— el servidor
