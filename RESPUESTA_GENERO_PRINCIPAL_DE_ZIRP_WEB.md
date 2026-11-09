# Respuesta · El género principal de «ZIRP!» y de dónde salen los `generos`

**De:** servidor · **Para:** el front de la web · **Fecha:** 09.11.26 · Responde a `PETICION_GENERO_PRINCIPAL_DE_ZIRP_WEB.md`.

## 1. «ZIRP!» ya está corregido

`diiv--zirp` trae `["Shoegaze"]`, en `discos/lanzamientos.json` y en `discos/diiv--zirp.json`. Comprobado en producción.
Las 51 tarjetas de Radio · Lanzamientos llevan ya un solo género y con mayúsculas. No tenéis que cambiar nada.

## 2. De dónde salen los `generos`

| Disco | Origen |
|---|---|
| Suena en Astra | El género de la ficha del artista (`genero`) |
| Publicado desde el Studio, aún no suena, con ficha de artista | El género de la ficha del artista (**desde hoy**) |
| Publicado desde el Studio, aún no suena, sin ficha de artista | Las etiquetas de MusicBrainz, de más a menos votadas, si las hay; si no, `[]` |
| Próximamente | Las etiquetas de MusicBrainz, y si no hay, el género de la ficha |

El fallo de «ZIRP!» era ese segundo caso: tomaba las etiquetas de MusicBrainz aunque DIIV tiene ficha, y el primero era
el más votado («alternative rock»). No se repetirá con otros artistas con ficha.

Las tarjetas «Próximamente» pueden seguir llegando con varios géneros y en minúsculas, como dice el contrato (§2b).

Un cambio pequeño en las páginas: los discos publicados que aún no suenan traen ahora en `discos/<slug>.json` el mismo
género que su tarjeta (antes `[]`). Los que ya suenan siguen con `[]` en su página. Contrato al día en
`DISCOS_WEB_INSTRUCCIONES.md`, §2b.

## 3. Dónde lo edita Pablo

No hay edición ni orden de géneros por disco. Lo que Pablo puede cambiar es el género del artista, en
**Studio → Artistas → ficha → Género**, y llega a sus discos en la siguiente regeneración de la web (cada noche, o al
guardar una ficha de disco en el Studio).

## Paul West

`paul-west--nebulae` sigue con `generos: []` porque no tiene ficha de artista y MusicBrainz no le da etiquetas. Se
llenará solo si Pablo le crea la ficha con su género.

— el servidor
