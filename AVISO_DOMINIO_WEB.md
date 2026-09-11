# Aviso a la web: el servicio responde ya en un segundo dominio

11-sep-2026 · del servidor · **no hay que cambiar nada hoy**

## Qué ha pasado

El servicio tiene desde hoy un segundo host: **`listen.dynamo.fm`**. Misma máquina, misma
IP que `listen.astra.fm`, un solo certificado para los dos.

**Los dos dominios conviven y van a seguir conviviendo durante meses.** Ni corte, ni
redirección, ni fecha límite. La web sigue funcionando igual sin tocar nada.

El CORS del microservicio ya admite los cuatro orígenes: `astra.fm`, `www.astra.fm`,
`dynamo.fm` y `www.dynamo.fm`. Es decir, el día que la web se sirva desde el dominio nuevo,
las llamadas no se van a estrellar contra el CORS.

## Lo que está verificado por el host nuevo

Comprobado uno por uno contra producción: el HLS de la emisión, la API de AzuraCast,
`/artists/index.json` y una ficha suelta, `/avisos.json`, `/actualidad.json`,
`/programas.json`, `/conciertos.json` y `/collections.json` responden 200. Las rutas de
`/cuentas/` responden también: sin sesión devuelven 401, que es lo correcto.

## Lo que pedimos, y no corre prisa

**1. Que el host esté en un solo sitio.** El bundle de producción (`/js/app.*.js`) nombra
`listen.astra.fm` diecisiete veces. Mientras el host esté repartido por el código, cambiarlo
es una revisión completa; con una constante o una variable de entorno es un valor.

Se puede hacer **ya**, sin cambiar el valor: que siga apuntando a `listen.astra.fm`.

**2. No cambiéis el valor todavía.** Cuando toque, lo avisamos.

**3. Una pregunta, para saber con qué contamos.** El sitio se sirve hoy en `astra.fm` desde
el hosting de cdmon, que es una máquina distinta de la del servicio. Si en algún momento el
sitio tuviera que responder también en otro dominio, ¿qué necesitáis por vuestra parte
además de que el hosting lo tenga dado de alta con su certificado? Interesa saber si hay
rutas, enlaces canónicos, mapas del sitio o metadatos sociales con el dominio escrito
dentro, y cuántos.

## Cómo contestar

Por aquí mismo o por donde ya venís dejando las peticiones. Lo de arriba está medido leyendo
vuestro bundle de producción desde fuera; si algo no encaja con el código fuente, mandad
vosotros el dato bueno.
