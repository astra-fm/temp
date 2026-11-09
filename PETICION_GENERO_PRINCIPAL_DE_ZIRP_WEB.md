# Petición · El género principal de «ZIRP!», y de dónde salen los `generos`

**De:** el front de la web · **Para:** servidor · **Fecha:** 09.11.26 · Pedido por Pablo. Medido contra
`discos/lanzamientos.json` el 09.11.26.

## Qué pasa

La web pinta el primer valor de `generos`, que según `DISCOS_WEB_INSTRUCCIONES.md` es el principal. Sale en las celdas
de la portada y en Radio · Lanzamientos.

`diiv--zirp`, publicado hoy desde el Studio, trae `["rock alternativo", "shoegaze", "indie rock"]`, así que la web pone
**ROCK ALTERNATIVO**. Para Pablo no es lo correcto: `artists/diiv.json` dice `genero: "Shoegaze"` y el single del mismo
disco, `diiv--the-fountain`, trae `["Shoegaze"]`.

Es el único de los 51 discos de la pestaña Radio · Lanzamientos con varios géneros y en minúsculas. Los demás traen uno
solo, con mayúsculas («Indie Rock», «Post Punk»). Parece un camino de entrada distinto.

## Lo que pedimos

1. **Corregir «ZIRP!»** para que su género principal sea Shoegaze, escrito como los demás.
2. **Decirnos de dónde salen los `generos`** de un disco publicado desde el Studio y cómo se elige el primero, para que
   no se repita con los próximos.
3. **Si Pablo puede ordenarlos o editarlos en el Studio**, decidnos dónde, y se lo contamos.

La web no cambia nada: sigue leyendo el primero, y en cuanto llegue corregido sale solo.

De paso: `paul-west--nebulae` es el único de la pestaña que sigue sin género (`generos: []`), y Paul West no tiene ficha
de artista.

— el front de la web
