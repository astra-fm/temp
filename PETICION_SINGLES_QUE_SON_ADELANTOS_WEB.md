# Petición de la web · tres singles que son adelantos llegan como `single`

7-nov-2026 · del front de la web · sobre `CONTRATO_LANZAMIENTOS_WEB.md` y `DISCOS_WEB_INSTRUCCIONES.md` §3c y §3d

## Qué pasa

Pablo ve en la portada tres singles **cuyo álbum aún no ha salido** pintados como singles sueltos, sin el chip
ADELANTO. La web no decide la clase: pinta el chip solo con `clase: "adelanto"`. Y en `discos/lanzamientos.json`
(leído hoy, 7-nov por la noche) los tres llegan así, en `radio.meses`, sin `anuncia`:

| Disco | Dónde está | `clase` | `anuncia` |
|---|---|---|---|
| `sebadoh--beauty-of-the-ride` | `meses["2026-10"]` | `single` | `null` |
| `fujiya-miyagi--no-green-light` | `meses["2026-10"]` | `single` | `null` |
| `placebo--helen-keller` | `meses["2026-10"]` | `single` | `null` |

En `discos/<slug>.json` de Sebadoh y Placebo, igual: `clase: "single"`, `anuncia: null`. La `descripcion` de Sebadoh
dice que adelanta la reedición de *Harmacy*, que sale el 20 de noviembre.

## Lo que esperamos, según el contrato

Pablo lo resume así: un single cuyo álbum no ha salido es un **adelanto** y lleva chip; cuando el álbum sale, el single
sale de Lanzamientos; un single que solo existe como single no lleva chip. Es la regla del §3d. Para estos tres:

- `clase: "adelanto"`, con `cancion` y `anuncia` (`{titulo, tipo, fecha, slug}`; `fecha` puede ser `null`);
- fuera de `meses` y dentro de `radio.proximamente` (y de `nuevos[]` si Pablo los marca), con `destino` a la página
  del disco que anuncian.

## La pregunta

¿De dónde saca el servidor que un single anuncia un disco? Si es un dato que Pablo tiene que rellenar en Studio →
Álbumes (el disco al que pertenece el single), decidnos cuál para que se lo digamos. Si lo deduce el servidor, estos
tres casos no lo están pillando.

En la web no hay nada que cambiar: en cuanto llegue `clase: "adelanto"`, el chip sale en el carrusel, en la pestaña y
en la página del disco.

— el front de la web
