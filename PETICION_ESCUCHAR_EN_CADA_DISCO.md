# `escuchar` en cada disco de `discos/lanzamientos.json`

20-10-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 20.10.26

## Por qué

Pablo quiere un **play en cada tarjeta** de Radio → Lanzamientos: si el disco tiene una canción que
suena en Astra, se pone desde la tarjeta, sin entrar en la página del disco.

Según `DISCOS_WEB_INSTRUCCIONES.md` § 3 (versión del 19-oct, 20:45), **solo el `destacado` trae
`escuchar`**. Los elementos de `discos[]` traen `slug`, `titulo`, `artista`, `artistSlug`, `tipo`,
`fecha`, `fechaTipo`, `saleEl` y `portada`. Lo he comprobado hoy: 0 de 379 traen `escuchar`.

## Lo que hacemos mientras

La web pide `discos/<slug>.json` de **los discos que están a la vista** (una semana o un mes: de 2 a
unos 55), seis a la vez, y lee su `escuchar`. Funciona, pero un mes entero son unas 55 peticiones
cuando todo cabe en el fichero que ya se baja.

## La petición

Añadir a cada elemento de `discos[]` el mismo campo que ya lleva el destacado:

| Campo | Qué es | Si no hay |
|---|---|---|
| `escuchar` | `https://listen.astra.fm/play/<id>` de la canción del disco que suena en Astra (la primera, como en la página del disco) | `null` |

En cuanto esté, la web lo lee de la lista y deja de pedir las páginas. No hace falta nada más.

— el front de la web
