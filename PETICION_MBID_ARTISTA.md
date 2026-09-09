# Petición al servidor · un identificador del artista en el now playing

**De:** la web · **Fecha:** 09.09.26

Todo lo medido aquí es de hoy, leído de `listen.astra.fm` al escribir esto.

## El problema, con un ejemplo que se entiende solo

La ficha de artista de la sección Radio saca sus datos duros —origen, años en activo, sello— de
**TheAudioDB**, y los busca **por el nombre del artista**, que es lo único que tenemos:

```
GET https://www.theaudiodb.com/api/v1/json/2/search.php?s=<nombre>
```

Esa búsqueda **devuelve una sola entrada por nombre y no desambigua**. Comprobado hoy: `Nirvana`
devuelve la banda de Washington, 1988–1994. Pero existe también **Nirvana, la banda británica de
1965**. Si sonara la británica, la ficha pintaría los años de la de Cobain **sin que nada avise**.

Pablo vio hoy una barra que decía «En activo 1956–2004» y no le cuadraba. No podemos comprobar si
está bien o mal, y esa es exactamente la cuestión: **con el nombre como única llave, no hay forma
de saberlo.**

## Lo que pedimos

**El identificador de MusicBrainz del artista (`mbid`) junto al tema que suena.** Es un UUID
estable y es lo que convierte una búsqueda por nombre en una consulta exacta: TheAudioDB tiene
ruta propia para él —`artist-mb.php?i=<mbid>`—, así que en cuanto llegue, el emparejamiento deja
de ser una apuesta.

Dónde nos serviría, por orden de utilidad:

1. **En el now playing**, con el resto de la canción. Es donde se decide qué ficha se pinta.
2. **En `/artists/<slug>.json` y en `editorial.json`**, como campo más de la ficha editorial. Ahí
   ya hay curación humana, así que es el sitio natural para guardarlo una vez y no volver a
   adivinarlo.

## Lo que ya llega, para que no lo busquéis dos veces

Hoy el objeto `song` del now playing trae: `album`, `art`, `artist`, `custom_fields`, `genre`,
`id`, `isrc`, `lyrics`, `text`, `title`.

- **No hay ningún identificador de artista.** Ni MBID, ni id de Spotify, ni nada estable: solo el
  nombre.
- **`isrc` existe pero viene vacío** —comprobado ahora mismo, cadena en blanco—. Si se rellenara
  también valdría: desde el ISRC se llega a la grabación y de ahí al artista, aunque son dos saltos
  en vez de uno.
- **`custom_fields` ya trae `descripcion_album`, `new_releases` y `year`**, así que si es más fácil
  meterlo ahí que tocar el esquema, por nosotros perfecto.
- La ficha editorial —`/artists/<slug>.json`— trae `bio`, `conciertos`, `displayName`, `genero`,
  `image`, `links` y `slug`. Tampoco hay identificador.

## Lo que NO pedimos

- Ninguna fuente nueva ni ninguna petición extra: es un campo en lo que ya mandáis.
- Que resolváis vosotros los datos de TheAudioDB. Eso lo hace la web; solo nos falta la llave.
- Nada urgente. Mientras no llegue, la ficha sigue funcionando como hoy: acierta con los nombres
  sin homónimo y puede equivocarse con el resto, sin forma de distinguirlo.

## Un apunte por si ayuda a decidir

Si el MBID no está en el catálogo y habría que ir a buscarlo, **es más barato hacerlo una vez en la
ficha editorial que en cada canción**: son ~2.600 artistas en `editorial.json` y el dato no cambia
nunca. El now playing sería lo ideal, pero la ficha editorial ya resolvería la mayoría de los
casos que nos importan, que son los artistas curados.

— agente de la web
