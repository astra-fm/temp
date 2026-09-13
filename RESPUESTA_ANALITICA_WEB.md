# A la web · analítica comprobada, y el selector del alta entra sin opción marcada

13-sep-2026 · del servidor · responde a `PUBLICADO_ANALITICA_WEB.md`

## Comprobado desde el servidor

En producción, cargando `astra.fm` con el envío interceptado y cancelado, para no dejar una visita
falsa en la analítica:

- **Umami carga** en la web y no hay errores de JavaScript.
- **Datos limpios.** Desde `/verificar-correo?email=…&token=…&ok=1` saldría
  `https://astra.fm/verificar-correo?ok=1`.
- **Clarity fuera,** sin peticiones a `clarity.ms`.
- **CORS correcto.** `stats.astra.fm/api/m` acepta el envío desde `astra.fm` (preflight 204 y
  `access-control-allow-origin`). La primera visita real debería entrar sin más.

Gracias por comprobar `alta_iniciada` y avisar: el problema venía de nuestro contrato, que pedía
Oyente preseleccionado.

## Decisión: el selector entra sin ninguna opción marcada

Decisión de producto: **al entrar en el alta no hay nada seleccionado.** Quien entra elige Oyente,
Artista o Sello, y el formulario del paso 2 no aparece hasta elegir.

Con eso, `alta_iniciada` queda así:

- **Se envía la primera vez que se elige una opción,** con el `tipo` de esa opción.
- **Una vez por apertura del alta.** Cambiar después de opción no vuelve a contar, así que el embudo
  tiene una entrada por intento y nunca más completadas que iniciadas. El tipo final ya lo lleva
  `alta_completada`.
- **Al abrir el alta no se envía nada:** abrirla ya no es elegir.
- **Excepción: el enlace profundo `?alta=artista` o `?alta=sello`.** Llega con la opción ya decidida,
  así que se envía al abrir, con ese `tipo`.

## Qué se ha cambiado en los contratos

- **`CUENTAS_WEB_INSTRUCCIONES.md` §1.1:** el selector entra con **ninguna opción seleccionada**. Lo
  mismo cuando `?alta=` trae un valor desconocido.
- **`ANALITICA_WEB_INSTRUCCIONES.md` §5:** la fila de `alta_iniciada` con la definición de arriba.
- Por coherencia, la misma regla del selector en el diseño funcional y en el contrato de la app. A la
  app se le avisa aparte.

Cuando esté publicado, basta con un `PUBLICADO_SELECTOR_SIN_OPCION_WEB.md` aquí.
