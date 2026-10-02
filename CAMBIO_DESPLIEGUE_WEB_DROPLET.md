# A la web · la web ya se sirve desde el servidor de la radio: hay que cambiar el despliegue

2-oct-2026 · del servidor

Desde hoy a las 12:27, `astra.fm` y `www.astra.fm` apuntan al servidor de la radio
(`159.89.111.18`, el mismo de `listen.astra.fm`) y la web se sirve desde ahí, con certificado
propio. El alojamiento de cdmon se da de baja en unas semanas.

**Lo urgente: vuestro despliegue sigue subiendo por FTP a cdmon.** Un `push` a `main` hoy
publica en un sitio que ya no ve nadie. Hasta que cambiéis el último paso de `deploy.yml`, no
despleguéis (o avisadnos y compilamos nosotros a mano).

## Qué hay que cambiar

Solo el paso `🚀 Deploy to Server via SFTP`. El checkout, Node 18, pnpm 10 y el build se quedan
como están. Lo sustituís por este:

```yaml
      - name: 🚀 Deploy al servidor (rsync por SSH)
        env:
          DEPLOY_SSH_KEY: ${{ secrets.DEPLOY_SSH_KEY }}
          DEPLOY_KNOWN_HOSTS: ${{ secrets.DEPLOY_KNOWN_HOSTS }}
        run: |
          install -d -m 700 ~/.ssh
          printf '%s\n' "$DEPLOY_SSH_KEY" > ~/.ssh/id_ed25519
          chmod 600 ~/.ssh/id_ed25519
          printf '%s\n' "$DEPLOY_KNOWN_HOSTS" > ~/.ssh/known_hosts
          rsync -rltz --exclude='.git*' ./dist/ astraweb@159.89.111.18:/
```

Los dos secretos **ya están creados en vuestro repositorio** (`DEPLOY_SSH_KEY` y
`DEPLOY_KNOWN_HOSTS`); no tenéis que pedir nada. Los seis `FTP_*` y `SERVER_DIR` dejan de
usarse: podéis borrarlos cuando el despliegue nuevo haya funcionado una vez.

Por qué así:

- **La ruta es `/` y no es la raíz del servidor.** La clave solo puede escribir en la carpeta de
  la web; para ella, `/` es esa carpeta. No puede abrir una terminal, ni leer nada, ni salirse de
  ahí (probado). Por eso no hay usuario con contraseña ni FTP.
- **Sin `--delete`, a propósito.** Es lo mismo que hacíais con `dangerous-clean-slate: false`:
  los trozos de builds anteriores se quedan, y quien tenga la web abierta desde antes de un
  despliegue no se queda con la navegación muerta. Lo hemos medido con el build de hoy
  (`c780e35`): 22 MB, 0 ficheros que cambiar porque ya estaba subido.
- `known_hosts` fijo en un secreto en vez de aceptar la huella en cada ejecución: así nadie puede
  hacerse pasar por el servidor.

## Lo que cambia para vosotros (y lo que no)

- **El `.htaccess` ya no lo lee nadie.** El servidor no es Apache, es nginx, y las reglas están
  traducidas una a una en su configuración: https sin `www`, 410 de `/astraless`, `sitemap.php`,
  `tv-og.php` y `butaca-og.php`, 404 para los trozos de `js/` y `css/` que ya no existen, y el
  resto a `index.html`. **Si un día cambiáis el `.htaccess`, el cambio no hará nada: pedídnoslo
  aquí** y lo trasladamos. Podéis seguir subiéndolo (no molesta: nginx no sirve ficheros ocultos).
- **PHP es la 8.1**, con `curl`. Los tres PHP pasan la comprobación y dan exactamente lo mismo que
  en cdmon: el sitemap con sus 187 direcciones, y las metas `og:`/`twitter:` de un vídeo de TV y
  de una secuencia de Butaca, comparadas una a una. Si usáis algo de PHP 8.2 o superior, avisad.
- `sys_get_temp_dir()` funciona igual: las cachés de los PHP viven en una carpeta propia, fuera de
  la web.
- `/manager` y `/api/…` (los restos del manager en cdmon) dan 404. Ninguna vista los usaba.
- Nada cambia en los contratos ni en `listen.astra.fm`.

## Después de cambiarlo

Haced un despliegue (vale un `workflow_dispatch` sin cambios) y avisadnos con un `PUBLICADO_`.
Comprobaremos que el build que llega es el de vuestro commit y os lo confirmamos aquí.
