# Respuesta: lo que le faltaba a la ficha del artista para la página

30-09-2026 · del servidor · para el front de la web

Respuesta a `PETICION_FICHA_ARTISTA_PAGINA_WEB.md`. Está todo en producción en `/artists/<slug>.json` y
anotado en `ARTISTAS_WEB_INSTRUCCIONES.md`, apartado **2.2e**. Todo es aditivo: nada de lo que ya
usáis cambia.

## Lo que pedisteis

| Petición | Campo | Nota |
|---|---|---|
| 1 · Estilo, sello y miembros | `estilo`, `sello`, `miembros` | De TheAudioDB con los mismos filtros de homónimo que `onair.json`. `miembros` es número y en solistas va siempre a `null` |
| 2 · Trayectoria resuelta | `trayectoriaResuelta` | Clave nueva, con la misma forma que en `onair.json`. `trayectoria` se queda como estaba para no romper la app |
| 3 · Tipo | `tipo` | Lista cerrada: `banda`, `solista`, `orquesta`, `coro` o `null` |
| 4 · Nacimiento y muerte | `nacimiento`, `fallecimiento` | Solo solistas confirmados con confianza alta. Precisión `AAAA`, `AAAA-MM` o `AAAA-MM-DD` |
| 5 · En qué programas suena | `suenaEn: [{slug, nombre}]` | `slug` es la clave de `programas.json` (`alta_fidelidad`, `ruido`, `hcr`...), la misma que `programme` en Radar, no `alta-fidelidad`. Se actualiza una vez al día |
| 6 · Marcas por disco | `discos[].nuevo`, `discos[].entroEn`, `discos[].efemeride` | Criterio de `nuevo`: la primera canción del disco entró en Astra hace 30 días o menos (hoy, unos 300 discos de 4.600). `efemeride: {anios}` solo el día de una efeméride aprobada |

## Un cambio que os afecta: EN ACTIVO en solistas

Tenéis razón en que EN ACTIVO nunca es el nacimiento. Al revisarlo vimos que `onair.json` sí lo hacía
en los solistas sin años en la ficha: MusicBrainz y TheAudioDB dan el nacimiento como inicio. Desde
hoy, en solistas, los años de la trayectoria solo salen de la ficha, tanto en `trayectoriaResuelta`
como en `onair.json`. Si no hay años en la ficha, `desde` llega a `null` y la celda no se pinta.

## Dos cosas más de hoy que sirven para la página

- **Radar o catálogo** (apartado 2.2d): `origen` es `radar` o `catalogo`, y `radar` trae sus
  lanzamientos con el tema para escuchar. Un artista de Radar que aún no suena ya tiene ficha; antes
  daba 404.
- **Discos** (apartado 2.2c): `discos[].anioDe` dice si el año es el de la primera edición o el de
  nuestra copia, que puede ser una reedición.

## Ejemplos para probar

- `placebo`: banda, estilo, sello, 3 miembros y trayectoria resuelta de MusicBrainz.
- `david-bowie`: solista, nacimiento y fallecimiento, dos discos con `nuevo: true`.
- `daniel-avery`: solista con nacimiento, suena en Sonorama.
- `sweet-mapache`: artista de Radar.

— el servidor
