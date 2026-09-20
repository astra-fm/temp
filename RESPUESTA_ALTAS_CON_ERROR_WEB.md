# Al servidor · las 36 altas: era nuestro service worker

20-sep-2026 · de la web · respuesta a `ALTAS_CON_ERROR_WEB.md`

Teníais razón en sospechar de nuestro lado. Lo hemos encontrado y ya está arreglado en `develop`.

## Lo que pasaba

**Vuestra hipótesis 2, y con nombre y apellidos.** La petición no llegaba a vosotros, pero no por un
fallo de red suelto: **el service worker de la web estaba en medio del alta**.

Su comodín «Network First» agarraba **todas** las peticiones —no solo las de caché— incluido el
`POST /cuentas/register`, y envolvía el `fetch` en un `catch` que devuelve **un 404 inventado**:

```js
event.respondWith(
  fetch(event.request).catch(() =>
    caches.match(event.request).then(c => c || new Response('Not found', { status: 404 })))
);
```

O sea que cuando la red fallaba por cualquier motivo —un momento sin cobertura, un bloqueador, una
VPN— lo que le llegaba a nuestro código **no era un error de red sino un 404 falso**. De ahí las tres
cosas que veíais:

- el `motivo` acababa en `otro`, porque 404 no es 400, 409 ni 429;
- vosotros no veíais nada, porque la petición nunca salió;
- y a la persona le salía «Error 404» en la pantalla del alta, que no explica nada.

Que se repitiera 17 veces en 13 minutos en una sesión encaja: quien lo tiene roto lo tiene roto todo
el rato, y vuelve a intentarlo.

## Lo que hemos hecho

**1 · El service worker ya no toca el alta.** Ni una petición que no sea `GET`, ni nada bajo
`/cuentas`. Un envío no se cachea nunca, y `GET /cuentas/me` es quién eres: servirlo desde una caché
sería enseñar la sesión anterior.

**2 · Vuestro §1, el mapeo del motivo: ya estaba bien.** 409 → `correo_registrado`, 429 → `limite`,
400 → `datos`, y `otro` solo para lo demás. Lo hemos vuelto a comprobar con cada código simulado. Los
36 `otro` no eran 409 disfrazados: eran peticiones que no llegaron a responder.

**3 · Vuestro §2, el código y el cuerpo apuntados.** Hecho, con un detalle que casi nos come: iba por
`console.warn` y **nuestro `main.js` silencia `warn` en toda la web**, así que no se habría visto
justo donde hace falta. Va por `console.error`. Queda la ruta y el fallo cuando no responde, y el
código con su cuerpo cuando la respuesta no es una de las previstas. Sin correo ni nada de la persona.

**4 · Vuestro §3, el 409 con salida.** Ahora, cuando el correo ya tiene cuenta, se ofrece **«Entrar
con este correo»** —que cambia de modo conservando lo escrito— y **«No me sé la contraseña»**. Con lo
de las 25 cuentas migradas, esa era la pieza que faltaba.

## Vuestro §4: a qué URL llamamos

`POST https://listen.astra.fm/cuentas/register`, JSON, sin cookies, con `Authorization: Bearer` solo
donde el contrato lo pide. En desarrollo sale por el proxy local a esa misma URL. Y sí, leemos el
código de estado: es lo que alimenta el `motivo`.

## Lo que no podemos saber todavía

Los 36 ya ocurrieron y no dejaron rastro, así que **no podemos deciros cuántos eran gente real y
cuántos ruido**. Con el arreglo, lo que pase a partir de ahora sí se va a poder distinguir: si vuelve
a haber `otro`, tendremos el código y el cuerpo en el navegador de quien lo sufra.

Os avisamos cuando esté desplegado.

— el front de la web
