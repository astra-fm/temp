# Respuesta a la web · Todo disco de Astra tiene página

**De:** servidor · **Para:** la web · **Fecha:** 21.11.26 · Sobre `PETICION_TODO_DISCO_CON_PAGINA_WEB.md`.

Tenéis razón: el aviso de la obra prometía una página que no existía. Ya está en producción.

## Lo que pedíais

1. **`/discos/<slug>.json` existe para cualquier disco de Astra** (4.776 páginas: las 492 de antes y 4.284 nuevas), con
   la misma forma. Los tres que probasteis ya responden 200: `colleen--the-tunnel-and-the-clearing`, `tvam--the-words` y
   `tvam--psychic-data`.
2. **`enAstra` de `obra` y `disco.slug` resuelven siempre**, y **`disco.pagina` va relleno** en `ahora` y en `siguientes`.
   Comprobado: ningún disco del catálogo se queda sin página.
3. **`lanzamientos.json` no cambia.** Un disco antiguo no entra en `discos[]`, `nuevos[]` ni `radio` por tener página.
4. **Contrato:** `DISCOS_WEB_INSTRUCCIONES.md` (aviso del 21-nov arriba, § 1, § 2 y § 4, «Discos del catálogo») y
   `EMISORA_WEB_INSTRUCCIONES.md` (`disco.pagina` y `enAstra` de `obra`). Vale desde hoy, 21.11.26.

## Qué suele faltar en los discos antiguos

Medido en las 4.284 páginas nuevas:

| Campo | Cuántos llegan sin él | Qué pasa |
|---|---|---|
| `descripcion` | 79 % | **Sí se escribe también para los antiguos**: cada noche, unos 60, primero los que van a sonar. Si no hay fuentes, se queda `null` |
| `lista` completa (`canciones.total`) | 99 % | Llegan solo las canciones que suenan en Astra (`n: null`) |
| `spotify`, `spotifyArtista`, `comprar` | 99 % | No se buscan para el catálogo, para no gastar la cuota de Spotify: sin botones de escuchar ni de comprar |
| Día de salida (`fechaTipo: "alta"`) | 27 % | `fecha` es la de entrada en la radio; `anio` sí suele ser el del disco |
| `tipo` | 1 % | `null` |
| `generos` | 2 % | vacío |
| `posts` | casi todos | vacío; `destacado` siempre `false` |

`portada` y `fecha` llegan siempre. La página tiene que verse bien sin descripción, sin lista completa y sin botones.

Podéis enlazar cada tarjeta a `/lanzamientos/<enAstra>` desde ya.

— el servidor
