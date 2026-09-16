# Al servidor · dos cosas para la ficha del artista en Radio

16-sep-2026 · del front de la web

Pablo ha aprobado que al pulsar un disco de **Lanzamientos 2026** se abra la ficha del artista de
rotación en un diálogo. El de Radar no cambia: sigue teniendo su página. Lo estamos montando, y nos
faltan dos cosas vuestras. Contratos releídos hoy: `ARTISTAS_WEB_INSTRUCCIONES.md` (10:02),
`RADAR_RENOMBRE_WEB_INSTRUCCIONES.md` (10:06), `LANZAMIENTOS_JSON.md` y
`EMISORA_WEB_INSTRUCCIONES.md` (09:20).

## 1 · Saber si un artista es de Radar, desde `lanzamientos.json`

La regla de producto es: **si el artista está en Radar, la celda navega a su página; si no, abre el
diálogo.** Pero `lanzamientos.json` solo trae `artist` como texto, así que tenemos que fabricar el
slug y compararlo con los `artistSlug` de `radar/artistas.json`.

Eso funciona hoy —vuestros tres artistas de Radar salen `gran-fenwick`, `catch92` y `luca-wilding`,
y el algoritmo del §3 da lo mismo—, pero **es una apuesta**: los dos slugs los calcula cada lado por
su cuenta y en ningún contrato dice que sean el mismo algoritmo. Si un día no coinciden, abriríamos
un diálogo a un artista que tiene página.

**Lo que os pedimos, cualquiera de las dos:**

- **Mejor:** que `lanzamientos.json` traiga `artistSlug` ya hecho en cada disco, y si podéis, un
  `enRadar: true|false`. Con eso no fabricamos nada.
- **O:** confirmar por escrito en el contrato que el `artistSlug` de Radar y el slug del §3 de
  artistas son el mismo algoritmo, y que lo seguirán siendo.

Mientras tanto lo montamos comparando por slug, y lo dejamos dicho en el código.

## 2 · `musicbrainz.pais` no siempre es un país

La ficha se lee así: `pais` en código ISO, y la web lo traduce («CL» → Chile). Pero en una muestra al
azar de 40 fichas, **25 traían código ISO, 5 una ciudad** («London», «Boston», «Perth», «Rennes») y
9 no traían el bloque. De los seis artistas de Lanzamientos, dos traen ciudad: «Manchester» y
«Los Angeles».

No es grave —pintamos el texto tal cual cuando no es un código— pero el dato deja de ser
comparable, y lo estábamos usando para saber si TheAudioDB habla del artista correcto. **¿Podéis
normalizarlo a ISO, o mandar la ciudad en su propio campo?**

## Y gracias por el género

`genero` ya llega relleno: 39 de 40 fichas de la muestra, cuando ayer estaba vacío en todas. La web
lo publicó anoche y lo pinta en cuanto lo encuentra.

— el front de la web
