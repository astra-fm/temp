# A la web · la analítica pasa a Umami, la de la propia radio

13-sep-2026 · del servidor · **contrato nuevo**

Contrato completo: `https://listen.astra.fm/docs/ANALITICA_WEB_INSTRUCCIONES.md`

## Qué cambia

La radio tiene desde hoy su propia analítica, **Umami**, en `https://stats.astra.fm`, alojada en el
servidor. Mide cómo se usa la web: páginas, procedencia y acciones clave con sus datos.

- **Microsoft Clarity deja de usarse.** Su script (proyecto `w16y41ydrd`) sale de la web.
- **Umami no usa cookies ni guarda datos personales,** así que no necesita aviso de consentimiento.
- **Las etiquetas de Google no entran en este cambio:** siguen como están.

## Por qué

- **Clarity no sirve para lo que necesita la radio:** sus eventos solo llevan un nombre, sin datos
  (qué tipo de alta, qué concierto), así que no permite contar acciones con detalle.
- **Clarity necesita consentimiento en la UE.** Sin él cuenta cada página como una visita nueva, y las
  cifras de sesiones y usuarios dejan de valer.

## El recorrido

1. **Cada página carga una sola etiqueta**, `https://stats.astra.fm/m.js`, con el ID del sitio y los
   dominios de producción. Umami cuenta las páginas vistas solo, también al navegar sin recargar.
2. **Antes de cada envío, la función `astraAntesDeMedir` limpia la dirección.** Los enlaces de
   verificar correo, restablecer contraseña y darse de baja llevan `email` y `token`, y nunca deben
   llegar a la analítica. La función va en línea en el `<head>`, delante de la etiqueta.
3. **El personal de la radio no se mide.** Un navegador queda excluido con la llave
   `localStorage umami.disabled`, que Umami respeta en cada envío. Se pone de dos formas:
   - al abrir la web con `?no-medir=1` (`?medir=1` la quita);
   - cuando la sesión carga una cuenta con `cuenta.interno: true`, que ya llega en el objeto
     `cuenta`. Al cerrar sesión la llave no se quita.
4. **Con sesión, la visita lleva el tipo de cuenta,** sacado de `cuenta.roles` y sin identificador:
   `umami.identify({ tipo_cuenta })`.
5. **Las acciones clave se envían como eventos con propiedades:**
   - escuchas (`escucha_inicio`, `escucha_1min`);
   - alta (`alta_iniciada`, `alta_completada`), `correo_verificado` y `login`;
   - `perfil_solicitado`, `radar_envio_completado`, `concierto_entradas` y `descarga_app`.

   La tabla con cuándo se envía cada uno y sus propiedades está en el contrato.

Qué pantalla o qué botón dispara cada evento es la parte de la web. El contrato solo fija el momento y
los datos.

## Cómo se comprueba

En la pestaña **Red** del navegador:
- **Al navegar:** sale `POST https://stats.astra.fm/api/m` con respuesta 200.
- **En la página de verificar correo:** la `url` enviada no lleva `email` ni `token`.
- **Con `?no-medir=1`:** no sale ninguna petición a `/api/m`.

Cuando esté publicado, un `PUBLICADO_ANALITICA_WEB.md` aquí basta: la radio lo verá en el panel en
tiempo real.
