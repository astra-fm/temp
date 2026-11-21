# Petición de la web · Todo disco de Astra tiene página

**De:** la web · **Para:** el servidor · **Fecha:** 21.11.26 · Sobre `OBRA_DEL_ARTISTA_SOLO_ASTRA_WEB.md`.

## Qué pasa

Vuestro aviso de hoy dice que cada trabajo de `obra` «abre su ficha en Astra» con `enAstra`. Pero hoy solo tienen
página (`/discos/<slug>.json`) los discos que están en Lanzamientos: lo dice `EMISORA_WEB_INSTRUCCIONES.md` en
`disco.pagina` («solo tienen página los discos de este año que salen en Radio → Lanzamientos»). El resto da 404:

- `colleen--the-tunnel-and-the-clearing` (el `disco` de lo que sonaba hace un rato, con `pagina: null`): 404.
- De vuestro ejemplo de TVAM, `tvam--ruins` sí tiene página, pero las otras dos que probamos no.

Así, en la tira de discos de Radio · En directo, un disco que no está en Lanzamientos no lleva a ningún sitio.

## Lo que decidió Pablo

**Todo disco de Astra tiene página** (decisión de hace tiempo, recordada hoy, 21.11.26). Y esa página tiene **el mismo
tratamiento que la de un lanzamiento nuevo**: la web la pinta con la misma vista que ya usa para Lanzamientos.

## Qué os pedimos

1. **`/discos/<slug>.json` para cualquier disco del catálogo de Astra**, no solo para los de Lanzamientos, con la misma
   forma que ya tiene (`titulo`, `artista`, `artistSlug`, `tipo`/`clase`, `anio`, `fecha`, `portada`, `descripcion`,
   canciones, compra, etc.). Lo que no tengáis de un disco antiguo, a `null`, y decidnos cuáles suelen faltar.
2. **Que `enAstra` de `obra` y `slug` de `disco` resuelvan siempre** a esa página, y `disco.pagina` vaya relleno.
3. **Que un disco antiguo no entre en la lista de `lanzamientos.json`** por tener página: la lista sigue siendo la de
   ahora.
4. Contad en el contrato desde cuándo vale y si la `descripcion` se escribe también para los antiguos (a la vista,
   «mismo tratamiento» incluye su texto).

La web enlazará cada tarjeta a `/lanzamientos/<enAstra>` en cuanto nos confirméis; hasta entonces las que no tienen
página se quedan sin enlace, para no llevar a un «no está».

— el front de la web
