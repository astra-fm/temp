# Al servidor · ¿qué puede usar un sello antes de que la redacción lo verifique?

13-sep-2026 · del front de la web · contrato de cuentas leído hoy a las 07:08

Diseño ha dibujado el raíl del área de un sello **durante el alta**, y lo abre antes de la
verificación. Antes de montarlo necesitamos saber si el servidor lo sostiene, porque el contrato
dice otra cosa.

## Lo que pide diseño

Para un sello con el correo verificado y el perfil todavía en `pendiente`:

| Acceso | Diseño |
|---|---|
| Datos del sello | **abierto** |
| Nuestro roster | **abierto**, «Ningún artista todavía» o «3 artistas» |
| Nuestros envíos | apagado hasta tener roster, y después hasta tener material |

## Lo que dice el contrato hoy

- `cuenta.sello` y `cuenta.roster` llegan **solo si el perfil de sello está `verificado`**.
- `PUT /cuentas/sello` y `POST /cuentas/sello/proponer` responden **`403` sin perfil de sello
  verificado**.
- `POST /cuentas/roster/proponer` **no documenta** qué pasa sin verificar.

Así que tal como está, un sello en `pendiente` abriría «Datos del sello» y no podría guardar, y
«Nuestro roster» no tendría datos que pintar.

## Las preguntas

1. **¿Puede un sello en `pendiente` proponer artistas al roster?** Si sí, ¿llega `cuenta.roster` en
   `GET /me` antes de verificar, para poder contarlos y enseñarlos?
2. **¿Puede editar sus datos** —`PUT /sello` y `sello/proponer`— antes de verificar, o sigue siendo
   `403`?
3. Si la respuesta es no a las dos, lo montamos con esos accesos **apagados hasta la verificación**
   y se lo decimos a diseño.

El raíl del **artista** no depende de esto y ya está montado.

— el front de la web
