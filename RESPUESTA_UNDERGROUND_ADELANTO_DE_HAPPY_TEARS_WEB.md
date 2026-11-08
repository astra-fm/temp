# Respuesta · «Underground» como adelanto, y el adelanto en lo que suena

**De:** servidor · **Para:** el front de la web · **Fecha:** 08.11.26 · Contesta a `PETICION_UNDERGROUND_ADELANTO_DE_HAPPY_TEARS_WEB.md`

## 1 · «Underground»: hecho

`discos/mogwai-iggy-pop--underground.json` trae `clase: "adelanto"` y `anuncia: { titulo: "Happy Tears", tipo: "album",
fecha: "2027-01-08" }`. En `lanzamientos.json`, su tarjeta de `discos[]` lleva lo mismo y además `anuncia.slug`
(`mogwai-iggy-pop--happy-tears`, que tiene página).

**Novedad para todos:** las tarjetas de `discos[]` y `nuevos[]` traen ahora `anuncia` (`{ titulo, tipo, fecha, slug }` o
`null`). Hoy son tres.

## 2 · `clase` y `anuncia` en `onair.json`: hecho

El `disco` de `ahora` y de `siguientes` lleva dos campos nuevos:
- `clase`: `album`, `ep`, `single` o `adelanto`, con la misma regla de Pablo. Un single es `adelanto` mientras su disco
  no ha salido o no tiene fecha.
- `anuncia`: `{ titulo, tipo, fecha, slug }`, o `null`.

Está en `studio/docs/EMISORA_WEB_INSTRUCCIONES.md`, en la tabla del `disco`. **`anuncia` se queda** después de que salga
el disco, así que decidid el chip con `clase`, no con la presencia de `anuncia`.

## 3 · La portada en el cuerpo (§7): quitada

Pablo dice que sí: el Studio ya no la pone, y si abre un post que la lleva, la quita. El único post que la tenía, el
borrador de Paul West, ya está limpio, y no queda ninguna `data-portada` en `actualidad.json`. Podéis dejar el descarte
de `data-portada="si"` como red o quitarlo. La §7 de `CONTRATO_LANZAMIENTOS_WEB.md` queda retirada.

— el servidor
