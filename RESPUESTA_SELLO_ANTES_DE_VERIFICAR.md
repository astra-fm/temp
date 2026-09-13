# Del servidor a la web · sí, el sello usa su área mientras la redacción lo revisa

13-sep-2026 · contesta a `PREGUNTA_SELLO_ANTES_DE_VERIFICAR.md`

Buena pregunta, y la leísteis bien: hasta hoy el servidor cerraba el área del sello hasta
verificarlo, y roster/proponer también respondía `403` aunque el contrato no lo dijera.

**Decisión de Pablo: se abre**, como la dibujó diseño. Ya está en producción.

## Por qué

Al sello no se le verifica por su música, sino por legitimidad. Y sus datos y su roster son justo lo
que la redacción necesita para juzgar eso. Con el área cerrada hasta verificar, la redacción tenía
que verificar un sello sabiendo solo su nombre.

Abrirla no publica nada: todo lo público sigue entrando como propuesta que decide la redacción, y el
contacto y el cargo son privados.

## Vuestras preguntas

**1 · ¿Puede un sello en `pendiente` proponer artistas al roster?** Sí. Y `cuenta.roster` llega en
`GET /me` desde que la solicitud está en `pendiente`, con sus artistas y su estado.

**2 · ¿Puede editar sus datos?** Sí: `PUT /sello` para contacto y cargo, que se guardan al momento, y
`sello/proponer` para nombre, web y lugar, que van a revisión. `cuenta.sello` también llega en
`pendiente`.

**3** ya no aplica.

## Cómo queda el recorrido del sello

1. **Alta como sello** con el nombre del sello. Sale solo el correo de verificación.
2. **Mientras no verifica el correo**, la cuenta no está activa: su área solo muestra el aviso, y
   estos endpoints responden `403` con `debeVerificar`.
3. **Verifica el correo**: la solicitud pasa a `pendiente` y se avisa a la redacción.
4. **En `pendiente`**: puede completar sus datos y proponer artistas a su roster. Todo eso llega en
   `GET /me`, y la redacción lo ve para decidir.
5. **La redacción lo verifica**: el área sigue igual, ya con la solicitud verificada.
6. **O lo descarta**: sus propuestas pendientes pasan a `rechazada`, los artistas propuestos del
   roster a `descartado`, y en el siguiente `GET /me` ya no llegan `sello` ni `roster`. Nada se borra.

«Nuestros envíos» no cambia: enviar música por Radar no depende ni de la cuenta ni de su verificación.

## Lo que no cambia

**El área del artista sigue cerrada hasta verificar.** Al artista se le juzga por su música, y se
verifica solo al entrar en programación. Como decíais, vuestro raíl de artista no depende de esto.

## Para contrastar

| Sello en… | `GET /me` trae `sello` y `roster` | `PUT /sello`, `sello/proponer`, `roster/proponer` |
|---|---|---|
| `sin-verificar-email` | no | `403` con `debeVerificar` |
| `pendiente` | **sí** | **sí** |
| `verificado` | sí | sí |
| `descartado` | no | `403` |

Contrato actualizado en el portal: `CUENTAS_WEB_INSTRUCCIONES.md` §5. Probado de extremo a extremo
contra producción con un sello desechable, ya borrado.

— el servidor
