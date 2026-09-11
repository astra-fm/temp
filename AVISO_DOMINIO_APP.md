# Aviso a la app: el servicio responde ya en un segundo dominio

11-sep-2026 · del servidor · **no hay que cambiar nada hoy**

## Qué ha pasado

El servicio tiene desde hoy un segundo host: **`listen.dynamo.fm`**. Apunta a la misma
máquina y la misma IP que `listen.astra.fm`, con un único certificado que cubre los dos.

**Los dos dominios funcionan a la vez y van a seguir así durante meses.** No hay día del
cambio, ni corte, ni redirección. La app instalada hoy sigue funcionando exactamente igual.

## Lo que está verificado por el host nuevo

Comprobado uno por uno contra producción, todo responde 200:

| Ruta | Qué es |
|---|---|
| `/collections.json` | índice de colecciones |
| `/collections/<id>.json` | una colección |
| `/hls/riot/aac_low_320.m3u8` | emisión HLS, perfil de datos |
| `/hls/riot/aac_hifi.m3u8` | emisión HLS, perfil de wifi |
| `/api/nowplaying` y `/api/station/3/nowplaying` | lo que suena |
| `/artists/<slug>.json` | ficha de artista |

El CORS del microservicio ya admite los dos dominios, con y sin `www`.

**Lo que no cambia:** la app pide las colecciones a `http://159.89.111.18:8080` con su
excepción declarada en `app.json`. Eso va por IP y el dominio no lo toca. Sigue igual.

## Lo que pedimos, y no corre prisa

**1. Que la base de URL sea una sola constante.** Hoy está escrita a mano en doce archivos
del repositorio, y hay varias copias de `AZURACAST_BASE` conviviendo (`src/services/`,
`src/hooks/`, y dos pantallas). Mientras siga así, cualquier cambio de host es una cacería
por el código y una versión nueva en las tiendas. Con una constante, leída de la
configuración del proyecto, es una línea.

Esto se puede hacer **ya**, sin cambiar el valor: que siga diciendo `listen.astra.fm`.

**2. No cambiéis el valor todavía.** Cuando toque, lo avisamos. La app no tiene
actualizaciones por aire (ni `expo-updates` ni CodePush), así que cambiar el host obliga a
compilar y pasar revisión en las dos tiendas. Conviene que viaje con una versión que ya
estuviera prevista, no con una publicación hecha solo para esto.

**3. Enlaces universales, cuando los haya.** Hoy la app no declara dominios asociados, solo
el esquema propio `astrafm`. Si en algún momento se añaden, que se declaren **los dos**
dominios desde el principio: declarar solo uno obliga a otra revisión de tienda después.

## Cómo contestar

Por aquí mismo, en este repositorio, o por donde ya venís dejando las peticiones. Si algo de
lo anterior no encaja con cómo está montada la app, decidlo: lo de arriba está escrito
leyendo vuestro repositorio desde fuera, no ejecutándolo.
