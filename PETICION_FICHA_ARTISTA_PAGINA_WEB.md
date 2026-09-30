# La ficha del artista pasa a ser una página: lo que le falta a `/artists/<slug>.json`

30-09-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 30.09.26

Contrato leído el 30.09.26: `ARTISTAS_WEB_INSTRUCCIONES.md`, versión del 30/09/2026 17:59, con §2.2b y
§2.2c (`confirmado` y `discos`), y `EMISORA_WEB_INSTRUCCIONES.md`, del 25/09/2026. Datos mirados en la
ficha de `placebo` y en `onair.json` hoy.

## Por qué

**Decisión de Pablo del 30.09.26:** el artista de rotación —el que suena en la radio y no está en
Radar— deja de abrirse en un diálogo y tiene **página propia**. Todo nombre de artista que se pulsa
en la web abre su ficha. La página vale para **cualquier artista de la biblioteca**, no solo para el
que suena, así que tiene que salir entera de `/artists/<slug>.json`. `onair.json` solo describe lo que
suena ahora y lo que viene.

La página enseña, además de foto, enlaces, nombre y bio, esta rejilla de datos (una celda sin dato
no se pinta):

| Celda | Qué dice |
|---|---|
| DE | el país, con su nombre en español |
| EN ACTIVO | «Desde 1987» o «1987–2010». Nunca el nacimiento |
| TIPO | Banda · Solista · Orquesta · Coro |
| MIEMBROS | el número. En solistas se cambia por NACIÓ, y MURIÓ si hay fecha: «30 sep 1986» |
| SELLO | el nombre del sello |
| EN ASTRA | «6 discos · 14 canciones» |
| SUENA EN | el programa; si son varios, todos: «Alta Fidelidad · Canal Nocturno» |

Y debajo, los discos con portada, tipo, año y «2 canciones en Astra», con una etiqueta en la portada
cuando toca: **«NUEVO EN ASTRA»** o **«HOY HACE 30 AÑOS»**.

## Lo que ya tenemos, gracias

Foto y crédito (`image`, `imageSource`, `imageSourceName`), `links`, `genero`, `displayName`, `bio`
con su crédito y **`discos`**, que cubre el bloque entero. EN ASTRA lo calculamos nosotros con
`discos` (número de discos y suma de `canciones`).

## Lo que pedimos

Todo aditivo, en `/artists/<slug>.json`. `null` o ausente cuando no se sepa: la celda no se pinta.

### 1 · Estilo, sello y miembros

Hoy **solo viajan en `onair.json`** (de TheAudioDB) y únicamente para lo que suena. Los necesitamos
en la ficha, con la misma regla de la ficha primero:

```json
"estilo": "Britpop", "sello": "Hut Records", "miembros": 3
```

`miembros` como número. Si lo sacáis de TheAudioDB, con los mismos filtros de homónimo que ya aplicáis
en `onair.json`.

### 2 · `trayectoria` resuelta, como en `onair.json`

En la ficha, `trayectoria` es solo lo curado y **casi siempre llega `null`** (Placebo: `null`, aunque
`musicbrainz` está verificado con país GB y formación 1994). En `onair.json` ya la resolvéis con la
cadena ficha → MusicBrainz → TheAudioDB, con el país en español y `de`. Pedimos **esa misma forma en
la ficha**, para no repetir la cadena en el navegador:

```json
"trayectoria": { "pais": { "nombre": "Reino Unido", "codigo": "GB", "de": "musicbrainz" },
                 "desde": 1994, "hasta": null, "de": "musicbrainz", "nota": null }
```

Si preferís no cambiar la forma de `trayectoria` en la ficha (hoy es `{pais: "AR", desde, hasta}`),
nos vale una clave nueva, por ejemplo `trayectoriaResuelta`. Decidnos cuál.

### 3 · Tipo, con sus valores

Hoy llega `musicbrainz.tipo: "banda"`, pero el contrato no dice qué otros valores puede tomar. Pedimos
un `tipo` en la raíz de la ficha, **lista cerrada en minúsculas**: `banda` · `solista` · `orquesta` ·
`coro`, o `null`. Un valor que no conozcamos lo tratamos como `null`.

### 4 · Nacimiento y muerte, en solistas

Solo existe dentro de `musicbrainz.confirmado` (`inicio` y `fin`), y solo con confianza alta. Pedimos
dos campos propios, solo en solistas:

```json
"nacimiento": "1986-09-30", "fallecimiento": null
```

Con la precisión que tengáis (`AAAA`, `AAAA-MM` o `AAAA-MM-DD`). Pintamos la fecha completa cuando
venga completa y el año cuando solo venga el año.

### 5 · En qué programas suena

No está en ningún sitio. Pedimos la lista de programas en cuya parrilla suena el artista, con el
nombre como lo escribís en `programas.json` y su slug:

```json
"suenaEn": [ { "slug": "alta-fidelidad", "nombre": "Alta Fidelidad" } ]
```

`[]` si no suena en ninguno. Es un dato, no una señal de «está sonando ahora»: no hace falta que se
refresque al minuto.

### 6 · Dos marcas por disco: nuevo y efeméride

En cada elemento de `discos`:

```json
{ "titulo": "Signify", "…": "…", "nuevo": true, "efemeride": null }
```

- **`nuevo`**: `true` si el disco es una incorporación reciente a Astra. Decidnos vosotros el criterio
  (días desde que entró, o una marca de la redacción) y escribidlo en el contrato.
- **`efemeride`**: solo el día de una efeméride **aprobada** de ese disco, con los años que cumple:
  `{ "anios": 30 }`. `null` el resto de días. La fecha de primera edición ya viene en `fecha`, pero no
  sabemos cuáles están aprobadas, y la portada solo lleva la etiqueta las aprobadas.

## Qué hace la web mientras tanto

Monta la página con lo que ya trae la ficha. Las celdas sin dato no se pintan, que es lo que pide el
diseño, y cada una aparecerá en cuanto llegue su campo. Cuando esté, os pedimos que lo anotéis en
`ARTISTAS_WEB_INSTRUCCIONES.md`.

— el front de la web
