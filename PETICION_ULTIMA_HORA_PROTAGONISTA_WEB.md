# Petición · Últimas 24 h: de quién es la noticia y el titular sin su nombre

**De:** el front de la web · **Para:** servidor · **Fecha:** 20.11.26 · Sobre `ULTIMA_HORA_INSTRUCCIONES.md` (leído el 20.11.26 a las 15:51).

Pablo ha elegido el diseño de Últimas 24 h en la portada de la web. Cada noticia se pinta en dos líneas:

1. **De quién es la noticia**, en grande, con la hora a la derecha: «HOT CHIP · HOY · 12:38».
2. Debajo, **el titular sin ese nombre**: «Colaboran con Beth Ditto, de Gossip, en el sencillo Me In Your Head».

Hoy `ultima-hora.json` solo trae el `titulo` entero. Separarlo en la web no es fiable: en «El Último de la Fila publican
el doble álbum Directo 2026» no hay forma segura de saber dónde acaba el nombre.

## Lo que pedimos

Dos campos nuevos en cada noticia:

| Campo | Qué es | Ejemplo |
|---|---|---|
| `protagonista` | De quién o de qué va la noticia, tal como se escribe. **No siempre es un artista**: puede ser un artista, varios, un festival o el nombre de un evento. | «Hot Chip» · «Hot Chip y Beth Ditto» · «Primavera Sound» · «Premios Grammy 2027» |
| `tituloSinProtagonista` | El titular sin ese nombre, empezando en mayúscula y leyéndose bien solo. En español, como `titulo`. | «Colaboran con Beth Ditto, de Gossip, en el sencillo Me In Your Head» |

- Que los dos los rellene Claude al adaptar la noticia y los revise la redacción en el Studio, como el resto.
- Si alguna noticia no tiene protagonista claro, `protagonista: null` y `tituloSinProtagonista: null`: la web pinta
  entonces el `titulo` entero en su lugar.
- `titulo` se queda como está: la página de la noticia y la app lo siguen usando.
- Las cuatro de prueba de hoy (Hot Chip, Atlas Sound, Blondie y El Último de la Fila), si podéis, ya con ellos.

Cuando estén, avisadnos en el contrato. Mientras tanto la web pinta el `titulo` entero.

— el front de la web
