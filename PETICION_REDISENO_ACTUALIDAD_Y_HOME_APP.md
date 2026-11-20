# Al servidor · el rediseño de la app del 20-nov: dos cosas de `actualidad.json` y una de `programas.json`

20-11-2026, de la app. Pablo ha aprobado hoy el rediseño de Home, Radio, Actualidad y Butaca. He
cruzado lo que pide con los contratos, bajados hoy a las 09:05 UTC: `APP_MOVIL_INSTRUCCIONES.md`
(19-nov), `ACTUALIDAD_WEB_INSTRUCCIONES.md`, `CONCIERTOS_WEB_INSTRUCCIONES.md` (20-nov),
`EMISORA_WEB_INSTRUCCIONES.md` y `DISCOS_WEB_INSTRUCCIONES.md`.

**Casi todo ya está.** El carrusel de conciertos sale de `conciertos.json` con `cartelCuadrado`, `frase`
y `vendedor`. La Radio expandida sale de `onair.json`: `ahora.programa`, `disco`, `artista.foto.credito`
y `siguientes[0]` con `played_at` y la foto. Butaca, de `tv.json` y `secuencias.json`. Faltan tres
cosas.

## 1 · `fecha` de `actualidad.json`: ¿en qué zona horaria está?

La app pasa a tener una placa **«Últimas 24 h»**, con las piezas publicadas en las últimas 24 horas y la
hora de cada una. Para eso necesito saber qué hora es `fecha` (`"YYYY-MM-DD HH:MM:SS"`, sin zona).

El contrato dice «hora del servidor de astra.fm». En los posts del Studio coincide al segundo con
`createdAt`, que va en UTC (`2026-11-19 10:33:18` ↔ `2026-11-19T10:33:18.889Z`). En los migrados del
manager no hay con qué compararla.

**Pido una de dos cosas:**
- que el contrato diga en qué zona va `fecha`, y si la de los migrados es otra; o
- mejor, un campo aditivo **`publicadoEn`** en ISO con zona (`2026-11-19T10:33:18Z`), en todos los
  publicados.

Mientras tanto, la app trata `fecha` como UTC.

## 2 · `clase` en los posts: que figure en el contrato de Actualidad

En Home, la tarjeta de un lanzamiento que es un adelanto lleva el chip **ADELANTO**. Lo saco de `clase`,
que ya traen los posts de `lanzamientos` desde el 7-nov: hoy hay 12 `album`, 11 `adelanto` y 2 `single`.
Las otras 91 piezas de lanzamientos no lo traen y van sin chip.

Ese campo solo está escrito en `DISCOS_WEB_INSTRUCCIONES.md` §3c. **Pido que figure también en
`ACTUALIDAD_WEB_INSTRUCCIONES.md` y en §1 de `APP_MOVIL_INSTRUCCIONES.md`**, con sus valores y con la
garantía de que todo post nuevo de lanzamientos lo trae. Es la fuente que lee la app, y no quiero
depender de un campo que no está en su contrato.

## 3 · Una imagen apaisada de cada programa (pregunta)

El bloque «A continuación» de Home pasa a llevar **la imagen del programa a todo el ancho, en 16:9**.
`programas.json` solo trae `imagenDestacada`, que es cuadrada, y recortarla corta la cara del residente.

¿Existe, o podéis publicar, una versión apaisada por programa? Por ejemplo, **`imagenApaisada`**: una
ruta relativa como las otras, o `null`. Diseño todavía no ha decidido si se hace. Mientras no llegue, la
app recorta la cuadrada.

— la app
