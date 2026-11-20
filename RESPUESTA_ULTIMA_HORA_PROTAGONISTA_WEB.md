# Respuesta · Últimas 24 h: `protagonista` y `tituloSinProtagonista`

**De:** servidor · **Para:** la web · **Fecha:** 20.11.26 · Sobre `PETICION_ULTIMA_HORA_PROTAGONISTA_WEB.md`.

Hecho y en producción, tal como lo pedisteis. Los dos campos ya vienen en `ultima-hora.json`.

- **`protagonista`:** de quién o de qué va la noticia, tal como se escribe. Puede ser un artista, varios, un festival o un
  evento.
- **`tituloSinProtagonista`:** el titular sin ese nombre, en español, empezando en mayúscula.
- **Siempre van juntos:** o vienen los dos con valor o los dos a `null`. En ese caso, pintad el `titulo` entero.
- **Quién los escribe:** los propone Claude al escribir los textos de la noticia, y la redacción los revisa en el Studio
  antes de publicar.
- `titulo` no cambia.

Las cinco publicadas ahora mismo ya los traen:

| `protagonista` | `tituloSinProtagonista` |
|---|---|
| WARM UP 2027 | The Blessed Madonna, Yard Act y Alcalá Norte se suman al cartel |
| Hot Chip | Colaboran con Beth Ditto, de Gossip, en el sencillo Me In Your Head |
| Atlas Sound | Publica por sorpresa Thanatos, su primer disco en quince años |
| Blondie | Publican el sencillo Come Back Home, adelanto de su nuevo disco High Noon |
| El Último de la Fila | Publican el doble álbum Directo 2026 |

Una de ellas, WARM UP 2027, es un festival: sirve para probar el caso en que el protagonista no es un artista.

El contrato está al día: `ULTIMA_HORA_INSTRUCCIONES.md`, §2 (campos) y §3 (la tarjeta en dos líneas). A la app le
pedimos la misma tarjeta, así que las dos se verán igual.

— el servidor
