# Petición · «Underground» es un adelanto de «Happy Tears», y el adelanto en lo que suena

**De:** el front de la web · **Para:** servidor · **Fecha:** 08.11.26 · Pedido por Pablo.

## Qué pasa

Sonó Mogwai, Iggy Pop «Underground» y en Radio · En directo no salió ningún chip. El disco
(`discos/mogwai-iggy-pop--underground.json`, leído el 08.11.26) llega como **`tipo: "single"`, `fecha: "2026-09-30"`,
`fechaTipo: "salida"`, `saleEl: null`**, sin `clase` ni `anuncia`. Pero es un **adelanto**: su propia `descripcion` dice
que anuncia «Happy Tears», el recopilatorio de Mogwai que sale el **8 de enero de 2027** por Rock Action.

La regla de Pablo: un single cuyo disco aún no ha salido es un adelanto y lleva el chip ADELANTO hasta que sale el disco.

## Lo que pedimos

1. **Marcar «Underground» como adelanto de «Happy Tears»**: `clase: "adelanto"` y
   `anuncia: { "titulo": "Happy Tears", "tipo": "album", "fecha": "2027-01-08", "slug": … }`, como en §2.2 de
   `CONTRATO_LANZAMIENTOS_WEB.md`, en su ficha y donde salga en `lanzamientos.json`. Si el tipo del recopilatorio no es
   `album`, el que corresponda.
2. **`clase` y `anuncia` también en el `disco` de `emisora/onair.json`** (`ahora` y `siguientes`), con la misma forma.
   Hoy ese `disco` no los trae, y es de donde la web saca el chip de lo que suena.

## Lo que hace la web

- Ya lee `anuncia` del `disco` de `onair.json` y, si no lo trae, el de `lanzamientos.json`. Mientras `anuncia.fecha`
  sea futura, pinta **ADELANTO** con esa fecha en el tooltip. En cuanto llegue, sale solo.
- Aparte, y por decisión de Pablo, **NUEVO LANZAMIENTO dura ahora 60 días desde la salida** (antes 30). Con eso,
  «Underground» ya lleva el chip NUEVO hasta el 28.11; con `anuncia`, pasará a ADELANTO.

— el front de la web
