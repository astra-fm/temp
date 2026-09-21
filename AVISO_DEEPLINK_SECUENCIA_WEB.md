# A la web · El enlace de Facebook cae en la home de Butaca: el router no lee el parámetro

21-09-2026, del servidor. Responde a `BUTACA_RUTAS_Y_OG_WEB.md` §1 y a
`SECUENCIAS_WEB_INSTRUCCIONES.md` §4.1.

Pablo publicó en Facebook la secuencia de Fight Club. La tarjeta salió perfecta, pero al pulsarla se
aterriza en **la home de Butaca**, no en la pieza. El enlace del post es el que acordamos:

```
https://astra.fm/butaca?secuencia=mub219ki-381195&fbclid=…
```

## Lo que hemos medido

**Vuestro lado servidor está bien.** Pedidas las tres formas desde aquí, `butaca-og.php` devuelve
las metas correctas de la pieza en todas, **también con el `fbclid` pegado detrás**:

| URL pedida | `og:title` | `og:url` |
|---|---|---|
| `/butaca?secuencia=<id>` | Pixies - Where Is My Mind? · Fight Club (1992) | `/butaca/secuencia/pixies-where-is-my-mind-fight-club-1992` |
| `/butaca?secuencia=<id>&fbclid=…` | igual | igual |
| `/butaca/secuencia/<slug>` | igual | igual |

**El que no mira el parámetro es el router.** En `app.210320bf.js` están las cuatro rutas de la
sección —`/butaca`, `/butaca/secuencias`, `/butaca/secuencia/:pieza`, `/butaca/videoclip/:pieza`— y
**ni una sola lectura del query string en todo el bundle**: cero coincidencias de `route.query` y de
`searchParams`. Con eso, `/butaca?secuencia=<id>` solo puede casar con `/butaca`, que es justo la
home que se ve.

Dicho de otro modo: el `og` resuelve por id y por slug, como contasteis, pero esa equivalencia vive
en el PHP, no en la aplicación.

## Qué os pedimos, y por qué en ese orden

1. **Leer el parámetro en el router y llevar a la pieza** (`?secuencia=<id>` y `?video=<id>`,
   ignorando cualquier otro parámetro que venga detrás — Facebook siempre añade `fbclid`). Es el
   §4.1 del contrato y lo que dijisteis que ya funcionaba.
   **Esto es lo importante**: el post de Fight Club ya está publicado con esa URL y no se puede
   cambiar. Si lo arregláis vosotros, ese post y los que ya salgan empiezan a funcionar solos.
2. Como parche mientras tanto, decidnos si **`/butaca/secuencia/<id>`** —el id en la ruta, no el
   slug— resuelve en el router. Si es que sí, cambiamos la URL que publicamos en un minuto y los
   posts nuevos van bien desde hoy. Seguiríamos usando el id y no el slug, por lo que vosotros
   mismos avisasteis: el slug cambia si se reescribe el título.

No hay prisa esta tarde: hasta que una de las dos esté, la radio no publica más secuencias.

— el servidor
