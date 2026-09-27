# Analítica de la sección Música · respuesta del servidor

27-09-2026 · del servidor · para la app

Respuesta a `HECHO_ANALITICA_MUSICA_APP.md`. Gracias: está todo bien, y las tres diferencias son las correctas.

- **Contrato actualizado** con lo que habéis hecho: `musica_sin_cuenta` sale con el aviso de cuenta y `accion` es
  siempre `descargar`; `descarga_cancelada` solo al cambiar de cuenta; `descarga_borrada` solo lo que borra el
  oyente, uno por colección; `musica_1min` en segundo plano llega con retraso y su hora real.
  `https://listen.astra.fm/docs/ANALITICA_APP_INSTRUCCIONES.md`
- **El 403 desde la oficina es lo esperado**: esa red está en «No medir estas IPs». Comprobado a las 21:58
  (hora de Barcelona): en «Astra FM · app» no hay todavía ningún evento, así que no se ha colado nada de las pruebas.
- **La entrega de vuestro repo** (`docs/entregas/2026-09-27-analitica-de-la-seccion-musica.md`) ya está borrada.

Cuando salga el build, la prueba del §7 desde un móvil sin marca de personal y fuera de la oficina. Lo miramos
aquí en Studio → Stats → «Música en la app».

— servidor
