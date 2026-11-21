# A la web · Radio · En directo: la obra del artista, solo lo que está en Astra

**De:** servidor · **Para:** la web · **Fecha:** 21.11.26

Ya en producción. Decidido por Pablo el 21.11.26. El contrato manda y se lee en el momento:
`https://listen.astra.fm/docs/EMISORA_WEB_INSTRUCCIONES.md`, aviso del 21-nov arriba y § 2, «obra».

## Qué problema resuelve
La tira de trabajos del artista que suena enseñaba demasiado: 16 discos de media por artista, más de la mitad
singles sueltos, y solo el 9 % estaba en Astra. El resto llevaba a Deezer o a ninguna parte. Pablo: «no contribuyen
con nada para el oyente y no llevan a ningún lugar» y «no los mandaría a Deezer, solo lo que tenemos en Astra».

## Qué cambia en `/emisora/onair.json` → `ahora.obra` (en producción desde hoy)
Los campos son los mismos; cambia lo que traen:

- **Solo lo que está en Astra.** `discografia`, `singlesDelDisco.singles` y `colaboraciones` solo llevan discos de
  nuestro catálogo, así que **`enAstra` va siempre relleno**: cada trabajo abre su ficha en Astra.
- **Como mucho 6** en `discografia` (y 6 en `colaboraciones`), del más nuevo al más antiguo, **con el disco que suena
  siempre dentro** aunque sea antiguo. `total` sigue diciendo cuántos tiene en Astra.
- **`enlace` siempre `null`.** Se mantiene para no romper nada; no pintéis enlaces a Deezer ni a MusicBrainz.
- **`obra: null` cuando no hay nada aparte del disco que suena** (ese ya va en `disco`). Pasa con unos 2 de cada 3
  artistas, que en Astra solo tienen un disco.

## Qué tiene que hacer la web
1. **Si `obra` es `null`, la tira no sale**, ni su título ni su hueco.
2. **Cada tarjeta enlaza a su ficha en Astra** con `enAstra`. Quitad cualquier enlace o icono de salida a Deezer o
   MusicBrainz.
3. **Nada de "ver más" ni de contador de lo que falta** fuera de Astra: lo que llega es todo lo que hay que enseñar.
4. Lo demás sigue igual: `sonando` marca el disco de lo que suena, `nuevo` y `tipo` separan «Nuevo álbum», «Nuevo single»
   y «Materiales ya publicados», y `portada` puede venir `null` en una colaboración (fondo con título y año).

## Cómo comprobarlo
- `https://listen.astra.fm/emisora/onair.json` → `ahora.obra`. Hoy a las 07:05 sonaba TVAM: `Ruins`, `The Words`,
  `Psychic Data`…, todos con `enAstra` y `enlace: null`.
- Con datos de hoy: de 2.069 artistas, 1.316 dan `obra: null`; el resto, entre 2 y 6 trabajos.
