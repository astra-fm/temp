# Respuesta · Área de artista y sello: Mi música

**De:** servidor · **Para:** el front de la web · **Fecha:** 11.11.26 · Decisiones de Pablo del 11-nov.

Sobre `PETICION_AREA_ARTISTA_Y_SELLO_MI_MUSICA_WEB.md`. Gracias por el repaso de dependencias: nos ha ahorrado trabajo.

## Cómo lo vamos a hacer

Tres fases, en este orden:

1. **Mi música del artista** (puntos 1 a 7). Es lo que os bloquea y es lo que empezamos ya. Os avisaremos con
   `AVISO_MI_MUSICA_LISTO_WEB.md` cuando esté en producción. **Hasta ese aviso, nada de lo de abajo existe.**
2. **El sello gestionando a sus artistas** (S1, S2, S3, S5, S6, S7).
3. **La página pública del sello** (S4). Hoy no existe en ningún sitio: es trabajo nuevo para los dos.

Mientras tanto, Mis datos se puede hacer con lo que hay, como decís.

## La idea: Mi música es el buzón de Radar desde el área

Ya no hay dos caminos para la música (Mis envíos y Tu canción). Un **material** (single, EP o álbum) sube con todas sus
canciones y entra en **la misma cola que un envío de Radar**. En el Studio, la redacción escucha las canciones que quiera
y **valida o no publica el material entero**. Al validarlo:

- **Todas sus canciones salen en la página del artista** y en Radar, sea single, EP o álbum.
- **Por separado**, la redacción puede marcar alguna canción para la radio. Esa canción sale «en parrilla».

Un material validado es una entrada de Radar, como las de hoy, pero con su lista de canciones. Por eso la página pública
del artista sigue leyendo `radar/artistas.json` (punto 7).

## 1. Material

Todas las rutas van bajo `/api/public/cuentas`, con la sesión como siempre (Bearer).

| Llamada | Qué hace |
|---|---|
| `POST /musica` `{tipo, titulo, derechos: true}` | Crea el material, en estado `subiendo`. Responde `201 {material}` |
| `POST /musica/:id/canciones?n=<orden>&nombre=<nombre del archivo>` | Cuerpo: el archivo tal cual, como `ficha/audio`. Responde `201 {cancion}` |
| `POST /musica/:id/cerrar` | Lo da por completo y entra en la cola de la redacción como **una** pieza. Pasa a `pendiente` |
| `DELETE /musica/:id` | Borra el material entero, en cualquier estado |
| `DELETE /musica/:id/canciones/:cid` | Borra una canción de un EP o un álbum |

- **`tipo`**: `single`, `ep` o `album`. **`derechos: true`** es obligatorio, como en `ficha/audio`.
- **Opcionales al crear**, para que la redacción no pierda lo que hoy le llega de un envío: `anio`, `genero` (texto),
  `sello` (texto) e `historia` (hasta 2.000 caracteres). Si el diseño no los pide, no los mandéis.
- **Canciones**: mismas reglas y mismos códigos que `ficha/audio` (MP3 o WAV, de 30 s a 12 min, una sola pista, hasta
  200 MB). **Un archivo que falla no anula el material**: responde su error y el resto se sigue subiendo.
- **Mínimos y máximos por tipo**, que se comprueban **al cerrar** (`400 numero-de-canciones`, con `min` y `max`):

  | Tipo | Canciones |
  |---|---|
  | single | de 1 a 3 |
  | ep | de 2 a 7 |
  | album | de 8 a 30 |

  Validadlos también en el navegador. Mientras está `subiendo`, se puede pasar del máximo y quitar canciones.
- **Un material que se queda en `subiendo`** más de 24 horas sin cerrarse se borra solo.
- **Límite de subidas**: cuenta **por cuenta**, no por IP, y las canciones van aparte: **60 canciones por hora** y
  **10 materiales al día**. Responde `429 demasiados-intentos`, como ahora.
- **Artista sin página** (`ficha.estado: "sin-ficha"`): **sí puede subir material**. Es justo como consigue su página:
  al validar el primer material, la página nace. `ficha/proponer` y `ficha/foto` siguen respondiendo `409 sin-ficha`.

## 2. La lista, en `cuenta.musica.items[]`

La más reciente primero:

```json
{
  "id": "…", "tipo": "ep", "titulo": "…",
  "estado": "subiendo" | "pendiente" | "validado" | "no-publicado",
  "subidoEl": "2026-11-11T…Z", "validadoEl": "…" | null,
  "duracion": 1234, "portada": "/canal-abierto/images/….jpg" | null,
  "origen": "area" | "envio",
  "canciones": [{ "id": "…", "n": 1, "archivo": "…", "duracion": 222, "url": "…", "enParrilla": false }]
}
```

- **`url` para escuchar también lo pendiente**: dirección firmada que caduca a las **6 horas**. Funciona en un `<audio>`
  sin token y admite saltos (`Range`). Si caduca, basta con volver a leer `/me`. En lo validado, `url` es el mp3 público.
- **Portada**: la de un envío de Radar si la tiene. En lo subido desde el área, `null` de momento: pintad las iniciales.
- **`no-publicado`**: la redacción puede no publicarlo. Pintadlo «sin drama», como las propuestas rechazadas. El
  artista puede borrarlo.
- **`enParrilla`**: la canción que la redacción ha pasado a la radio.
- **`origen: "envio"`**: un envío antiguo de Radar (punto 4).

## 3. Borrar

- **Material entero**: en cualquier estado. Si estaba validado, sale de la página y de Radar al momento. Si alguna
  canción estaba en parrilla, sigue sonando en la radio hasta que la redacción la retire.
- **Una canción de un EP o un álbum validado**: sale de la página **sin volver a la redacción**. No se puede borrar la
  última: para eso se borra el material. Un single no se borra por canción.

## 4. Lo que ya hay

- **`ficha/audio` y `cuenta.ficha.audio` conviven hasta que publiquéis Mi música.** Después, `ficha/audio` responderá
  `410` y la canción de cada artista pasará a su lista como single (`origen: "area"`). Avisadnos al publicar y lo hacemos
  en el momento.
- **Los envíos de Radar pasan a la lista de Mi música** de su artista, con `origen: "envio"`. Sus estados se traducen
  así: `recibido` y `escuchado` → `pendiente`; publicado en Radar → `validado`; descartado → `no-publicado`. Si estaba en
  parrilla, su canción lleva `enParrilla: true`. `cuenta.envios` seguirá llegando mientras lo uséis.
- **El formulario de Radar** (`/cuenta/envios/nuevo`) sigue funcionando tal cual hasta que diseño decida. Lo que entra
  por él acaba en la misma cola y en la misma lista.

## 5. Nombre artístico

Lo añadimos: `nombre` en `ficha/proponer`, como un campo propuesto más (hasta 120 caracteres). Al aplicarse cambia el
nombre de la página.

## 6. Enlaces por separado

Confirmado: al aplicarse, cada clave de `enlaces` que llega se escribe y **las que no llegan no se tocan**. Mi música
puede mandar las cinco redes y Mi página pública solo `web`.

## 7. Aviso de la pestaña y página pública

- La marca, como decís, sale de la lista: algún item en `pendiente`.
- **La página pública del artista hoy pinta una sola canción por artista.** En `radar/artistas.json` cada entrada
  traerá además `canciones: [{n, titulo, duracion, url, enParrilla}]`, con todas las del material validado.
  `featuredTrackUrl` sigue llegando, con la primera canción, para no romper nada. Os toca pintar la lista en la página
  del artista y en Radar.

## El sello (fase 2)

Lo decidido hasta ahora:

- **S5:** si el artista y su sello proponen a la vez, **las dos propuestas llegan a la redacción**, cada una con quién
  la mandó. No se sustituyen entre ellas.
- **Dependencia 6:** toda propuesta y todo material llevarán **quién lo mandó** (el artista o el sello).
- **Dependencia 8:** el límite cuenta por cuenta. Un sello con cuatro artistas tendrá un límite mayor.
- **S1 a S4, S6 y S7**: los contestaremos al empezar la fase 2, con la forma de las rutas, permisos y errores.

## Dependencias

1. **Direcciones**: nuestros correos solo enlazan a `astra.fm/cuenta`, sin subpáginas. No nos afecta.
2. **Formulario de Radar**: punto 4.
3. **`cuenta.envios`**: punto 4.
4. **`ficha/audio`**: punto 4.
5. **La app**: la avisamos nosotros cuando cambie algo de lo que lee.
6. y 8. En el apartado del sello.
7. Punto 7.

Si algo de esto no encaja con el diseño, decidlo aquí antes del aviso.

— el servidor
