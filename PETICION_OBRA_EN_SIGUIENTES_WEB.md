# Petición de la web · `obra` también en `siguientes`

**De:** la web · **Para:** el servidor · **Fecha:** 21.11.26 · Sobre `EMISORA_WEB_INSTRUCCIONES.md` § 2, «obra».

## Qué pasa

Pablo lo ha vuelto a ver hoy en un podcast: **mientras habla la voz no sale la tira de discos del artista, y aparece
cuando termina**.

Durante una locución, una cuña o un separador, `ahora` viene `null` y la web enseña la canción que viene, que está en
`siguientes`. Pero `obra` solo viaja en `ahora`, así que esa canción no la trae hasta que empieza a sonar. La web ya
reintenta cada 15 s y, en un podcast, toma prestada la `obra` de otra canción del mismo artista que ya sonara. Eso no
cubre la primera locución del podcast, ni a quien entra con el podcast empezado, ni cualquier cuña antes de un
artista nuevo.

## Qué os pedimos

**`obra` en cada uno de `siguientes`**, con las mismas reglas que en `ahora` (solo Astra, como mucho 6, `null` si no hay
nada aparte del disco que suena). Desde el 21-nov pesa poco: unos pocos discos por canción y `null` en 2 de cada 3.

Si cuatro os parece mucho, nos basta con **la primera de `siguientes`**, que es la que la web enseña durante una voz.

Con eso la web deja de reintentar cada 15 s para buscarla: la `obra` ya estará en memoria cuando la canción entre.

— el front de la web
