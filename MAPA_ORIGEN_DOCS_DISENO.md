# Mapa de origen del estante de diseño

Texto del equipo de diseño para el agente del servidor de la radio.
Respuesta a `RESPUESTA_DOCS_DISENO.md` del 4-sep-2026. Actualizado el 4-sep-2026 (v2).

## Conformes con el plan

El *pull* nos vale mejor que el SFTP, y por el mismo motivo que dais: nadie tiene que repartir
credenciales de producción. Que el `index.json` se genere en cada petición leyendo el disco
resuelve además lo único que nos preocupaba de las fechas —que se escribieran a mano—. Cadencia
de 10 minutos, bien; no hace falta disparo desde GitHub Actions para empezar.

## El mapa de origen

| Publicado como | Repo | Ruta en el repo | Rama |
|---|---|---|---|
| `diseno/DESIGN_WEB.md` | `astra-fm/web` | `DESIGN.md` | `develop` |
| `diseno/DESIGN_APP.md` | `astra-fm/mobile` | `DESIGN.md` | `develop` |
| `diseno/APP_COMPORTAMIENTO.md` | `astra-fm/mobile` | `docs/astra-app-comportamiento.md` | `develop` |
| `diseno/APP_INVENTARIO.md` | `astra-fm/mobile` | `docs/astra-app-inventario.md` | `develop` |
| `diseno/INSTRUCCIONES_DISENO.md` | `astra-fm/design` | `INSTRUCCIONES_DISENO.md` | `develop` |
| `diseno/entregas/` | `astra-fm/web` | `docs/entregas/` | `develop` |
| `diseno/entregas/` | `astra-fm/mobile` | `docs/entregas/` | `develop` |

**Tres avisos sobre las ramas, para que no os cueste una tarde:**

- **Todo sale de `develop`, en los tres repos.** En la primera versión de este documento os
  dijimos `main` para web; lo corregimos. Diseño no escribe en `main`, así que publicar desde ahí
  retrasaría los cambios hasta el siguiente despliegue.
- **`astra-fm/mobile` no tiene `main`.** Su única rama es `develop` y su `HEAD` remoto apunta ahí.
- **`astra-fm/design` sí tiene `main`, y es su rama por defecto**, pero lo que hay que publicar
  está en `develop`. Un `clone` sin `--branch develop` os dará la rama equivocada.

**Sobre las entregas**: las dos carpetas `docs/entregas/` todavía no existen; las creamos al
reordenar la documentación. Podéis dejar la ruta configurada desde ya: mientras no exista, no hay
nada que publicar. Como pedís, publicad todo lo que haya dentro con su propio nombre.

## Las dos confirmaciones que pedís

**Secretos: no hay, y lo hemos comprobado, no supuesto.** Búsqueda de contraseñas, claves de API,
tokens, cabeceras `Bearer`, claves privadas y URLs con credenciales incrustadas sobre los seis
documentos: **cero coincidencias**. Lo que salta a simple vista es la palabra «token» en su
sentido de sistema de diseño —`color/bg/canvas` y compañía— y un `type="password"` que describe un
campo de formulario. La regla queda asumida: en estos documentos no entran credenciales, y si
alguno las necesitara, ese documento no se publica.

**Publicables tal cual, con una excepción que arreglamos nosotros.** Hay **un** enlace relativo a
un fichero que no estará en el estante: `](docs/REFACTOR.md)`, dentro del `DESIGN.md` de web. Lo
convertimos en texto plano antes de que empecéis. El resto de enlaces son a Figma y a rutas de
código, que no se rompen.

## Una cosa que falta en vuestro plan: los borrados

Decís que el proceso descargará los ficheros «solo si el commit cambió». Eso cubre altas y
modificaciones, pero no las bajas: **si un documento desaparece del repo o se renombra, la copia
publicada se queda huérfana** y se sigue sirviendo como si fuera buena. Con las entregas, que se
retiran al implementarse, va a pasar seguro.

Pedimos que la sincronización sea **espejo de la lista y de la carpeta**: lo que ya no está en el
origen, se retira del estante y sale del `index.json`. Si preferís no borrar automáticamente, nos
vale que lo marquéis —un campo `retirado: true`— mientras un agente pueda distinguirlo sin abrir
el fichero.

## Una salvedad nuestra, pendiente de decisión

La clave del archivo de Figma aparece en cinco de los seis documentos, y el portal es público. Por
sí sola no abre nada —quien no esté invitado se topa con el muro de permisos de Figma— pero deja
de ser un dato interno. Lo estamos decidiendo. Si resolvemos que no salga, os avisamos antes de
que publiquéis y os mandamos los documentos con esa referencia sustituida. Si no decimos nada,
publicad tal cual.

## Qué esperamos de vuelta

Las URLs finales cuando esté montado, y un aviso si al descargar alguno de los documentos os
encontráis algo que no cuadra —un fichero que no está donde decimos, o una rama que no existe—.
El mapa está verificado contra los tres repositorios hoy, pero las ramas se mueven.
