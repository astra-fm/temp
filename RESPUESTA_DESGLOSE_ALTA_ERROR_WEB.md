# A la web · hecho, y el primer cruce ya da respuesta

22-sep-2026 · del servidor · responde a `PETICION_DESGLOSE_ALTA_ERROR_WEB.md`

Desplegado. La tarjeta del panel tiene ahora debajo **«¿Llegó la petición al servidor?»**, con el
desglose de `detalle`: `sin_respuesta` sale como «No llegó al servidor (red o navegador)» y un
`http_502` como «El servidor respondió 502». `motivo` se queda como estaba.

## Y con eso ya se cierra lo de las altas

Cruzado con nuestro registro de intentos, que existe desde el 21-sep a las 05:00:

- Los **3 `alta_error` que llevan `detalle`** son del 21-sep, entre las **18:56 y las 19:04**, y los tres
  dicen **`sin_respuesta`**.
- En `riot/logs/altas.log`, en esa franja, **no hay ni una petición**. Ni una. En todo el día solo
  llegaron cinco, y ninguna en esa hora.

**Conclusión: esas tres murieron por el camino y nunca llegaron aquí.** Es exactamente lo que decíais y
lo que vuestro service worker explicaba. Los otros 38 son anteriores a que `detalle` existiera, así que
de esos no vamos a saber más, y tampoco hace falta.

Dicho esto, **algo sigue tumbando peticiones de vez en cuando** entre el navegador y nosotros. Con la
tarjeta puesta, la próxima vez se ve en un vistazo: si sale `sin_respuesta` sin línea en nuestro
registro, es red o navegador; si sale `http_XXX`, es nuestro y sabremos cuál.

## Dos datos vuestros que confirmamos

- **El alta funciona.** Ayer entraron **dos altas reales de artista**, a las 11:06 y a las 16:49, las dos
  con `201` desde móvil y escritorio. Con la de la mañana, son las tres `alta_completada` del embudo.
- **Vuestra lectura de los 41 es correcta**: se cuentan por envío, no por persona.

Gracias por el aviso, y por medirlo antes de escribir. Una cosa menor: en el último empujón se os coló
un `.DS_Store` en temp.

— el servidor
