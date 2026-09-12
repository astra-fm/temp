# A la web · decisión tomada: volvemos a Astra FM. Vuestros pasos y el orden

12-sep-2026 · del servidor · sustituye a `CONGELACION_WEB.md`

## La decisión

**Se vuelve a Astra FM**, nombre y dominio. Se levanta la congelación para este trabajo
concreto, que es justo el contrario del de ayer.

Sentimos el viaje de ida y vuelta. Lo que hicisteis ayer estuvo bien hecho y rápido, y nada de
ello fue en balde: el host centralizado en una constante es lo que hace que esto sea hoy una
línea y no una cacería.

**Por nuestra parte ya está hecho el nombre**: los correos, el portal de documentación y las
páginas del studio vuelven a decir Astra FM.

## Vuestros pasos

**1. La constante del host, de vuelta a `https://listen.astra.fm`.** Sigue sirviendo todo y no
ha dejado de hacerlo en ningún momento.

**2. Canónico, sitemap, robots y metadatos sociales**, de vuelta al dominio anterior. Son los
cuatro sitios que vosotros mismos medisteis.

**3. ⚠️ Quitad el bloque de limpieza del service worker ANTES de publicar en el dominio
anterior.** Es el punto que más ruido haría y el más fácil de olvidar. Hoy ese bloque
desregistra el service worker y borra las cachés cuando se sirve en `astra.fm` y `www.astra.fm`.
Si el sitio vuelve a servirse ahí con el bloque dentro, **cada visita se autodestruye el service
worker**: se queda sin modo sin conexión y sin caché de audio, y encima de forma intermitente,
que es lo peor de diagnosticar.

Si queréis dejarlo preparado para el futuro, invertidlo: que la lista de dominios retirados
apunte al dominio nuevo, no al viejo.

**4. Preparad el despliegue y avisadnos. No publiquéis todavía.**

## El orden, y por qué importa

El DNS del dominio anterior apunta **hoy a nuestro servidor**, que es quien está haciendo la
redirección. La secuencia es:

1. Vosotros preparáis el despliegue y nos avisáis. **← aquí estamos**
2. Nosotros sacamos el dominio del certificado de la emisora. Si esto se hace después de mover
   el DNS, la siguiente renovación falla para todos los nombres y se cae el HTTPS de la radio.
3. Nosotros retiramos la redirección y Pablo devuelve el DNS a vuestro alojamiento.
4. Vosotros publicáis.

Hacerlo en otro orden deja el sitio sin servir o el certificado roto, así que conviene ir
diciéndonos cada paso por aquí.

## Lo que no cambia

La emisión, la API, las fichas de artista y todos los endpoints responden **por los dos
dominios** y van a seguir haciéndolo. No hay prisa ni corte: el dominio nuevo no se apaga de
golpe.

— el servidor
