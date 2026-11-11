# Petición · El sello gestiona a un artista nada más añadirlo

**De:** el front de la web · **Para:** servidor · **Fecha:** 11.11.26 · Decisión de Pablo del 11.11.26.

Gracias por el soporte: ya está montado y envía de verdad.

## Qué cambia

Hoy (§5.6) un artista que añade un sello nace `propuesto` y **no se gestiona hasta que la redacción confirma la
relación**: no se le puede subir música ni proponer nada (`404 artista-no-encontrado`). Pablo, al probar el área, lo ha
visto como un bloqueo para el sello:

> Debería poder añadir el artista y entrar como cualquier otro artista para validar en Radar, en Studio.

Es decir: **el sello añade al artista y en ese mismo momento puede subir su música**, como si fuera el propio artista.
La redacción valida en el Studio lo que llegue, como cualquier material de Mi música; no hay un paso previo de confirmar
la relación.

## Lo que necesitamos

1. Que `?artista=<id>` acepte también un artista recién añadido por un sello verificado, sin esperar a la redacción, en
   `POST /musica`, y en `ficha/proponer` y `ficha/foto` cuando tenga página.
2. Que `GET /roster/:id` responda para ese artista igual que para uno verificado.
3. Saber en qué estado nace el item del roster y cuándo cambia. Si deja de existir `propuesto`, o si sigue existiendo
   pero ya no bloquea, decídnoslo para pintarlo: hoy la tarjeta dice «PROPUESTO · EN REVISIÓN» y «SE GESTIONA CUANDO LA
   REDACCIÓN LO CONFIRME».

## Lo que queremos confirmar

1. **Validar el material ¿confirma también la relación?** Por ejemplo: al validar el primer material que subió el sello
   para ese artista, el artista pasa a contar como suyo.
2. **La página pública del sello** (§5.7) hoy solo enseña artistas confirmados. ¿Sigue así, o aparece al validarse su
   primer material?
3. **Un artista que ya existe en Astra** (con su propia cuenta o ya publicado en Radar): si un sello lo añade, ¿puede
   subirle música en el momento o ahí sí hace falta que la redacción confirme? Es el caso en que un sello podría escribir
   en la página de alguien que no lleva.
4. **El sello en revisión** sigue sin gestionar (`403 sello-no-verificado`), entendemos. Decidnos si no.
5. `roster/proponer` sigue pidiendo `relacion` y `enlace`, que la redacción usa para comprobar la relación. ¿Se mantienen
   obligatorios?

En cuanto contestéis, la web abre la gestión de los artistas recién añadidos. Hasta entonces no tocamos nada.

— el front de la web
