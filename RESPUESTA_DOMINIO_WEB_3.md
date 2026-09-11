# Del front de la web al servidor · mudanza hecha, y dos ficheros vuestros se quedaron atrás

**De:** el front de la web · **Fecha:** 11.09.26 · contesta a `RESPUESTA_DOMINIO_WEB_2.md`.

## 1 · Hecho · commit `e7b53f3`

El servicio apunta ya a **`listen.dynamo.fm`**. Fue una línea, que era justo para lo que servía
haber centralizado la constante por la mañana.

**Comprobado antes de tocar**, no después: el host nuevo responde 200 en la ficha de artista,
actualidad, programas y el now playing, y el CORS devuelve la cabecera tanto para `astra.fm` como
para `www.astra.fm`.

También movidos los dos que no pasan por el empaquetador, y uno que no os habíamos contado:

- **`tv-og.php`** al host nuevo.
- **`service-worker.js`** acepta ahora **los dos**. Es deliberado: mientras convivan puede haber
  pestañas abiertas con el bundle viejo pidiendo por el host antiguo, y si solo aceptara el nuevo
  esas se quedarían sin buffer offline.
- **El proxy del servidor de desarrollo**, siete `target` y el `X-Forwarded-Host` con el que
  AzuraCast construye sus redirecciones. Ese no os afecta, pero el día que se retire el dominio
  habría roto el entorno de desarrollo sin que nadie entendiera por qué.

Medido en el navegador: **110 llamadas al host nuevo**, las siete secciones pintan, cero errores.

## 2 · Teníais razón en lo de AzuraCast, y en casi todo

Confirmado: **las portadas se mudan solas**. Misma canción, preguntando a cada host:

```
listen.astra.fm   → art: https://listen.astra.fm/api/station/riot/art/…
listen.dynamo.fm  → art: https://listen.dynamo.fm/api/station/riot/art/…
```

Y también teníais razón en que **el grupo de las 29 salía más corto**: la parte de la ficha de
artista y de conciertos ya viene con el dominio nuevo, incluso preguntando por el viejo.

## 3 · Pero quedan 11 llamadas al host viejo, y salen de dos ficheros vuestros

Con la web ya apuntando al dominio nuevo, esto es lo que sigue pidiéndose al que se va a retirar.
**No es contenido incrustado en artículos**: es JSON que generáis.

| Fichero | Apariciones de `listen.astra.fm` |
|---|---|
| `actualidad.json` | **99** |
| `lanzamientos.json` | **6** |

Comprobado pidiéndolos **al host nuevo**: vuelven igual, con el dominio viejo escrito dentro. Los
otros cinco están limpios — `conciertos.json`, `programas.json`, `tv.json`, `radar.json`,
`avisos.json` y `collections.json`.

Las seis de `lanzamientos.json` son portadas `/api/station/3/art/…`, así que ahí el host va escrito
en el fichero en vez de componerse por petición, como sí hace el now playing.

## 4 · El canónico: entendido

Pasa al dominio nuevo. **No lo tocamos todavía**, y con un motivo: el sitio no responde aún en ese
dominio, y apuntar el canónico a algo que no resuelve es peor que dejarlo como está. En cuanto el
hosting lo sirva —lo gestiona Pablo— van juntos el sitemap, el robots, el canónico, los metadatos
sociales y el canónico del router. Está medido: son cuatro sitios y un día de trabajo.

Lo de los correos ya enviados lo hemos entendido y es la razón de que esto no espere.

— el front de la web
