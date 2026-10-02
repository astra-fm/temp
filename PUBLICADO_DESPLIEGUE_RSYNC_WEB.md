# Publicado: el primer despliegue de la web por rsync

02-10-2026 · del front de la web · responde a `CAMBIO_DESPLIEGUE_WEB_DROPLET.md`

**De:** el front de la web · **Fecha:** 02.10.26

Gracias por el paso hecho y por los secretos ya creados. Cambiado tal cual lo mandasteis.

## Qué hemos hecho

- **`deploy.yml`**: el paso `🚀 Deploy to Server via SFTP` está sustituido por el vuestro,
  `🚀 Deploy al servidor (rsync por SSH)`, letra por letra: `DEPLOY_SSH_KEY`,
  `DEPLOY_KNOWN_HOSTS` y `rsync -rltz --exclude='.git*' ./dist/ astraweb@159.89.111.18:/`, sin
  `--delete`. El checkout, Node 18, pnpm 10 y el build siguen igual.
- **Desplegado**: `main` en **`1464da5`**, que trae ese cambio (`14fe0b0`) y la documentación del
  despliegue nueva (`34cdbf2`). El `push` a `main` ha disparado el workflow.
- **`docs/DEPLOY.md` reescrito**: dónde vive la web, los pasos, por qué sin `--delete`, qué hacer
  si falla el rsync, y **que el `.htaccess` ya no lo lee nadie y las reglas se os piden aquí**.

## Lo que veréis llegar

**Ningún cambio de la web en este despliegue**: solo el workflow y la documentación. El build
debería ser el mismo que el de `c780e35`, que ya teníais, así que es posible que rsync no tenga
nada que copiar. Si queréis un marcador, el CSS de producción contiene `:not(.duotone__play)`,
que entró en `c780e35`.

## Lo que no podemos ver desde aquí

No tenemos acceso a la pestaña de Actions desde esta máquina. **¿Nos confirmáis que el paso de
rsync ha terminado bien y que el build que llega es el de `1464da5`?** Si ha fallado, el error
del paso nos basta para saber si es la clave, la huella o la conexión.

Cuando lo confirméis, Pablo borra los seis `FTP_*` y `SERVER_DIR` del repositorio.

— el front de la web
