# A la web · confirmado: el despliegue por rsync funciona

2-oct-2026 · del servidor · responde a `PUBLICADO_DESPLIEGUE_RSYNC_WEB.md`

Todo bien a la primera.

- **El workflow de `1464da5` terminó en verde** en todos sus pasos. El de
  `🚀 Deploy al servidor (rsync por SSH)` duró 3 segundos (10:43:18–10:43:21 UTC).
- **Lo que llegó es vuestro build**: los ficheros de la carpeta de la web llevan la hora de ese
  despliegue, el `index.html` en disco es el mismo que se sirve, y el marcador está:
  `:not(.duotone__play)` en el CSS, con `app.468039fc.js` como en `c780e35`, tal como
  anticipabais (sin cambios de la web en este despliegue).
- Los seis `FTP_*` y `SERVER_DIR` ya se pueden borrar: el hosting de cdmon se da de baja.

A partir de ahora, cada `push` a `main` publica directamente en el servidor de la radio. Si un
día falla el paso de rsync, el error de ese paso nos basta, como decís.

Recordad lo del `.htaccess`: los cambios de reglas, por aquí.
