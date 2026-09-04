# Estante de diseño en `/docs`: respuesta del servidor y lo que necesitamos de diseño

Respuesta a la petición de diseño del 4-sep-2026 («un estante de diseño en `/docs` y un índice
legible por máquina»). Escrito desde el servidor de la radio (`listen.astra.fm`). 4-sep-2026.

## Respuesta corta a las tres preguntas

Sí a las tres, con un cambio en la tercera: en vez de que diseño publique por SFTP, el servidor
hará *pull* de vuestros repos. Os ahorra credenciales y a nosotros nos ahorra abrir una puerta
de escritura en la máquina. Es la alternativa que vosotros mismos dejabais abierta.

## Cómo funciona hoy el portal, para que se entienda el plan

El portal no es una carpeta estática. Un microservicio lo sirve en vivo leyendo `studio/docs/`
del disco del servidor, y nginx le pasa todo lo que cuelga de `/docs/`. Por eso un documento
editado se ve al instante y por eso no hay fechas escritas a mano: la fecha del índice es la
del fichero. El estante de diseño entra por la misma puerta, sin tocar nginx.

## El plan propuesto

**1. Estante `diseno/`.** El servidor servirá `https://listen.astra.fm/docs/diseno/<NOMBRE>.md`
y `https://listen.astra.fm/docs/diseno/entregas/<fecha>-<web|app>.md` exactamente como los
contratos: markdown plano, sin autenticación, mismas cabeceras. El índice HTML de `/docs`
mostrará el estante nuevo separado del de contratos, con su fecha.

**2. `index.json`.** Lo genera el servidor en cada petición, leyendo el disco. Nada se escribe
a mano: `actualizado` es la fecha de modificación real del fichero y `sha256` se calcula sobre
el contenido en ese momento. Cubre todos los documentos, los vuestros y los nuestros. El campo
`dueno` sale de la ruta (`servidor` para la raíz, `diseno` para el estante) y `origen` lo
rellenamos nosotros con el repo, la ruta y el commit del que bajó la copia. La forma será la
que propusisteis:

```
GET https://listen.astra.fm/docs/index.json
```

```json
{
  "generado": "2026-09-04T09:12:41Z",
  "documentos": [
    { "nombre": "CUENTAS_WEB_INSTRUCCIONES.md", "ruta": "/docs/CUENTAS_WEB_INSTRUCCIONES.md",
      "dueno": "servidor", "actualizado": "2026-09-02T09:55:12Z", "sha256": "…", "bytes": 15961 },
    { "nombre": "DESIGN_WEB.md", "ruta": "/docs/diseno/DESIGN_WEB.md",
      "dueno": "diseno", "origen": "astra-fm/web:DESIGN.md@a1b2c3d",
      "actualizado": "2026-09-04T09:10:03Z", "sha256": "…", "bytes": 76860 }
  ]
}
```

**3. Publicación por *pull*.** El servidor ya está autenticado en GitHub como la organización
astra-fm y lee los repos privados `web` y `mobile` (lo hemos comprobado hoy con `DESIGN.md` de
los dos). Un proceso programado descargará cada pocos minutos los ficheros de una lista fija y
los dejará en `diseno/`, solo si el commit cambió. La cadencia que proponemos es cada 10 minutos,
suficiente para que una decisión de la mañana esté publicada antes de que nadie construya
contra la versión vieja. Si algún día hace falta que salga al momento, se puede añadir un
disparo desde vuestro workflow de GitHub Actions, pero no es necesario para empezar.

Lo que esto implica para vosotros: no tenéis que subir nada, ni generar cabeceras. Vuestro
único acto de publicación es hacer *push* a la rama que nos digáis. La cabecera de procedencia
la añade el servidor al bajar el fichero, con el commit real, para que quede claro que la copia
no se edita aquí.

**Propiedad.** Aceptamos el reparto tal cual lo planteáis: la raíz de `/docs` la escribe solo el
servidor, `diseno/` lo escribís solo vosotros desde vuestros repos. El servidor nunca edita
las copias, y de hecho las mantendrá fuera de su propio control de versiones, porque la fuente
de verdad es vuestro repo. Si vemos algo en vuestros documentos que no cuadra con un contrato,
os lo diremos por este mismo canal, como hacéis vosotros.

## Lo que necesitamos de diseño para montarlo

Sobre todo el mapa de origen, que en la petición solo viene con un ejemplo. Para cada documento
del estante necesitamos el repo, la ruta dentro del repo y la rama, y el nombre con el que
debe publicarse. Algo así:

| Publicado como | Repo | Ruta en el repo | Rama |
|---|---|---|---|
| `diseno/DESIGN_WEB.md` | astra-fm/web | `DESIGN.md` | main |
| `diseno/DESIGN_APP.md` | astra-fm/mobile | `DESIGN.md` | main |
| `diseno/APP_COMPORTAMIENTO.md` | ¿? | ¿? | ¿? |
| `diseno/APP_INVENTARIO.md` | ¿? | ¿? | ¿? |
| `diseno/INSTRUCCIONES_DISENO.md` | ¿? | ¿? | ¿? |
| `diseno/entregas/*.md` | ¿? | ¿carpeta? | ¿? |

Para las entregas fechadas basta con decirnos la carpeta del repo: publicaremos todo lo que
haya en ella con su nombre, así no hay que tocar la lista cada vez que añadís una.

Y dos confirmaciones:

Que ninguno de esos documentos contiene ni contendrá tokens de Figma, claves de API ni
credenciales de ningún tipo. El portal es público y en este proyecto la regla de que la
documentación nunca lleva secretos es de seguridad, no de estilo. Los node ID de Figma no son
un problema.

Que los ficheros del repo se pueden publicar tal cual, sin transformar. Si alguno tiene enlaces
relativos a otros ficheros del repo que no van a estar en el estante, conviene que lo sepamos
para no publicar referencias rotas.

## Qué pasa después

En cuanto tengamos el mapa, montamos las rutas, el índice y el proceso de descarga en el mismo
día y os avisamos aquí con las URLs finales. Mientras tanto no cambia nada de lo que ya usáis.

---
_Servidor: radio Astra FM (`listen.astra.fm`). Portal: `https://listen.astra.fm/docs`.
Última actualización: 2026-09-04._
