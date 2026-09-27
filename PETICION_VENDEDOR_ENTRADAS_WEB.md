# Quién vende las entradas de cada concierto: un campo `vendedor` en `conciertos.json`

27-09-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 27.09.26

Contrato leído el 27.09.26: `CONCIERTOS_WEB_INSTRUCCIONES.md`, versión del 22/09/2026 14:53. Lo que
pedimos aquí no está en él.

## Por qué

**Decisión de Pablo del 27.09.26:** en el carrusel de conciertos de la portada, la celda de comprar
entradas cambia según dónde se vendan:

| Si las entradas se venden… | La web pinta |
|---|---|
| en **Ticketmaster** | el botón oficial de Ticketmaster (su distintivo sobre su azul) |
| en **cualquier otro sitio** | el botón genérico «Comprar entradas» que ya está en producción |

El enlace es el mismo en los dos casos: el campo `entradas` de siempre. Lo que falta es saber
**quién vende**, y hoy el concierto no lo dice.

## Por qué no lo deducimos del enlace

Lo hemos mirado en `https://listen.astra.fm/conciertos.json` a día de hoy: 8 conciertos, todos con
`entradas`. Los dos de Ticketmaster (Placebo y Mercury Rev) **no apuntan a `ticketmaster.es`**, sino
a un enlace de afiliado:

```
https://ticketmaster.evyy.net/c/6638448/1958952/23886?u=https%3A%2F%2Fwww.ticketmaster.es%2Fevent%2F…
```

Podríamos reconocer `evyy.net` o desenvolver el `u=`, pero sería adivinar desde el navegador:

- si cambia el programa de afiliación o alguien pega el enlace directo, la regla se rompe sin
  avisar;
- una sala puede vender por Ticketmaster desde su propia web, y eso el dominio no lo cuenta;
- la app tendría que repetir la misma adivinanza por su cuenta.

Quien lo sabe es la redacción al dar de alta el concierto, o el servidor al guardar el enlace.
Preferimos que se decida en un solo sitio.

## La petición

Un campo nuevo en cada fila de `conciertos` (y, si os cuadra, también en `festivales`):

```json
{
  "artista": "Placebo",
  "fecha": "2026-10-03",
  "entradas": "https://ticketmaster.evyy.net/c/…",
  "vendedor": "ticketmaster",
  …
}
```

- `vendedor`: **`"ticketmaster"` o `null`**. `null` significa «otro sitio o no se sabe», y la web
  pinta el botón genérico.
- Si pensáis añadir más vendedores con botón propio, mejor como **lista cerrada de valores en
  minúsculas** que como texto libre, y que el contrato diga cuáles hay. Un valor que la web no
  conozca lo tratará como `null`.
- Con `entradas: null`, `vendedor` debería ser `null` también: sin enlace, la celda no se pinta.
- Cómo se rellena lo decidís vosotros: una casilla en el Studio, deducirlo del enlace al guardar, o
  las dos cosas. A nosotros solo nos importa que llegue en el JSON.

Cuando esté, os pedimos que lo anotéis en `CONCIERTOS_WEB_INSTRUCCIONES.md` §2.

## Qué hace la web mientras tanto

Monta los dos botones ya, con el genérico en todos los conciertos, que es lo que se ve hoy. El de
Ticketmaster aparecerá solo en cuanto llegue `vendedor: "ticketmaster"`, sin otro despliegue.

— el front de la web
