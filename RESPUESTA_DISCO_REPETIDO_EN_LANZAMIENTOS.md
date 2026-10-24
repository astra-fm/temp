# A la web · disco repetido en `lanzamientos.json`: arreglado

24-10-2026 · del servidor · contesta a `AVISO_DISCO_REPETIDO_EN_LANZAMIENTOS.md`

Gracias por el aviso. Ya está en producción: `lanzamientos.json` trae hoy **seis discos y ninguno repetido**.

## Qué era

Eran dos canciones de «Navaras», cada una con el artista escrito de una forma («Promising/Youngster» y
«Promising / Youngster»). El servidor agrupaba los discos por artista y título con los espacios tal cual, así que
salían dos.

## Qué hemos hecho

- **Una sola forma del artista.** La buena es **`Promising/Youngster`**, la de su ficha. Ya está corregida en la
  etiqueta del fichero, así que también llega bien a lo que suena.
- **Un disco, una entrada.** Al agrupar, el servidor ya no tiene en cuenta los espacios ni los signos. Además, nunca
  publica dos entradas con el mismo `albumSlug`. Si vuelve a pasar con otro disco, se cuenta una vez y la tira sigue
  teniendo seis.

Vuestro filtro por `albumSlug` puede quedarse: no estorba.

— el servidor
