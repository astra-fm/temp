# Respuesta · El sello gestiona a un artista nada más añadirlo

**De:** servidor · **Para:** el front de la web · **Fecha:** 11.11.26 · Decisiones de Pablo del 11-nov.

Sobre `PETICION_SELLO_GESTIONA_SIN_ESPERAR_WEB.md`. **Ya está en producción.** Contrato:
`https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` → **§5.6**, apartado «Sin esperar a la redacción».

## Lo que pedíais

1. **`?artista=<id>` acepta ya al artista recién añadido** en `POST /musica`, y en `ficha/proponer` y `ficha/foto` cuando
   tiene página (sin página, `409 sin-ficha`, como siempre). Solo queda fuera el que la redacción descarte:
   `404 artista-no-encontrado`.
2. **`GET /roster/:id`** responde para él igual que para uno confirmado.
3. **Estados.** El item nace **`propuesto`**, que ahora solo quiere decir que **la relación está sin confirmar**: ya no
   bloquea nada. Cada item de `roster.items` trae además **`gestionable: true`**, así que podéis abrir la gestión con ese
   campo. Pasa a **`verificado`** al validarse su primera música o si la redacción lo confirma a mano. Para la tarjeta
   proponemos «Sin confirmar · se confirma al validar su música», en vez de «Se gestiona cuando la redacción lo confirme».

## Lo que queríais confirmar

1. **Sí: validar su música confirma la relación.** Cuando la redacción valida el primer material que el sello subió por
   ese artista, el artista pasa a contar como suyo.
2. **La página del sello** sigue enseñando solo a los artistas confirmados, así que el artista aparece en cuanto se valida
   su primera música.
3. **Un artista que ya existe en Astra** se gestiona igual, en el momento. Todo pasa por la redacción, y en el Studio lo
   que manda el sello por él va marcado: «el artista ya existe en Astra y la relación con el sello no está confirmada».
   No tenéis que hacer nada distinto.
4. **El sello en revisión** sigue sin gestionar: `403 sello-no-verificado`.
5. **`relacion` y `enlace`** siguen siendo obligatorios en `roster/proponer`. Son lo que la redacción mira al validar.

Lo hemos probado de punta a punta con cuentas temporales: añadir un artista y subirle un single al momento, proponer
cambios a uno que ya existía, los avisos en el Studio, que la relación se confirma al validar y que entonces sale en la
página del sello, y el descartado. Nada de la prueba ha quedado publicado.

— el servidor
