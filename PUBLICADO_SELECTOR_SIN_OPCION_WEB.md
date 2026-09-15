# Al servidor · el selector del alta ya entra sin ninguna opción marcada

15-sep-2026 · del front de la web · responde a `RESPUESTA_ANALITICA_WEB.md`, del 13-sep

Publicado en producción hoy, con dos días de retraso: vuestra decisión del 13-sep se leyó y no se
llegó a implementar. La encontramos al bajar el contrato para el encargo del embudo.

## Lo que cambia

- **Al entrar en el alta no hay nada seleccionado.** Antes nacía con Oyente puesto, que elegía por
  quien llega. Un `?alta=` con un valor desconocido también deja el selector vacío.
- **Sin elegir no se crea la cuenta.** El botón está apagado aunque el correo y la contraseña estén
  puestos.
- **`alta_iniciada` se manda una sola vez por apertura, a la primera elección.** Cambiar después de
  opción ya no vuelve a contar. Con `?alta=artista|sello` se manda al abrir, con ese tipo.
- **Abrir el alta ya no manda `alta_iniciada`**: abrirla no es elegir. Lo que se manda al abrir es
  `alta_abierta`, el evento nuevo.

## Comprobado en el navegador

Con Umami simulado y `/cuentas/*` interceptado, sin tocar cuentas reales:

- El selector abre con **tres tarjetas y ninguna marcada**.
- Con correo y contraseña puestos y sin elegir, el botón sigue **apagado**; al elegir, se enciende.
- Elegir Oyente y luego Sello deja **una sola** `alta_iniciada`, con `tipo: oyente`.
- `?alta=artista` manda `alta_abierta` `{desde: enlace}` y después `alta_iniciada`
  `{tipo: artista}`.

## Lo que falta

Vuestra decisión también decía que **el formulario del paso 2 no aparece hasta elegir**. Eso no
está: el selector entra vacío, pero sigue donde estaba, debajo de los datos de acceso, no delante.
Partir el alta en dos pasos mueve la pantalla, así que lo tiene que ver diseño antes.

Lo decimos claro para que no lo deis por hecho: el embudo ya cuadra —una apertura, una iniciada—,
pero la pantalla todavía no es la de dos pasos que describe `CUENTAS_WEB_INSTRUCCIONES.md` §1.1.

— el front de la web
