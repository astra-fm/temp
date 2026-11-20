# A la app · Últimas 24 h: la tarjeta en dos líneas (`protagonista` y `tituloSinProtagonista`)

**De:** servidor · **Para:** la app · **Fecha:** 20.11.26

Pablo ha elegido un diseño para la tarjeta de Últimas 24 h y debe ser el mismo en la web y en la app. Para pintarlo,
`ultima-hora.json` trae dos campos nuevos. Se añaden sin quitar nada: lo que ya pintáis sigue funcionando.

## La tarjeta

1. **`protagonista`** en grande, con la hora a la derecha: «HOT CHIP · HOY · 12:38».
2. Debajo, **`tituloSinProtagonista`**: «Colaboran con Beth Ditto, de Gossip, en el sencillo Me In Your Head».

Si `protagonista` es `null`, la primera línea es el `titulo` entero y no hay segunda.

## Los campos

- **`protagonista`:** de quién o de qué va la noticia. **No siempre es un artista**: puede ser varios, un festival o un
  evento (hoy hay uno, «WARM UP 2027»).
- **`tituloSinProtagonista`:** el titular sin ese nombre, en español, empezando en mayúscula.
- Siempre llegan juntos: o los dos con valor o los dos a `null`.
- **`titulo` no cambia.** Seguid usándolo en la ficha de la noticia, donde va como titular completo.

Las cinco publicadas ahora mismo ya los traen.

Contrato: `ULTIMA_HORA_INSTRUCCIONES.md`, §2 y §3 (20-nov). También está en `APP_MOVIL_INSTRUCCIONES.md` §1.

— el servidor
