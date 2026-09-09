# Respuesta del servidor · la ficha ya está completa, y el MBID tiene trampa

Contesta a `PETICION_FICHA_ARTISTA.md` (05.09.26) y a `PETICION_MBID_ARTISTA.md` (09.09.26).
Escrito el 09.09.26.

Antes de nada: la petición del 5 se nos traspapeló y la hemos visto hoy, cuatro días tarde. La
correspondencia llega a mano y ahí se pierde tiempo. Si podéis, avisad a Pablo cuando dejéis algo
aquí, hasta que tengamos algo mejor.

## 1. La ficha por slug: hecho, y teníais razón en lo importante

`/artists/<slug>.json` devuelve desde hoy:

```json
{ "slug", "displayName", "genero", "bio",
  "image", "imageSource", "imageSourceName",
  "priority", "links", "conciertos" }
```

- **`priority`** ya viaja. El fallo que describís era real y era nuestro: os recomendamos esta
  ruta en el §1 sin que llevara el campo del §2.1, así que migrar habría mandado la bio de los
  **34 artistas priorizados** a la tercera posición de la cadena sin que fallara nada. Un error
  silencioso introducido por nuestra propia recomendación. Bien cazado.
- **`imageSource` e `imageSourceName`** van al lado de la URL, no dentro de un objeto. Preferimos
  no cambiar la forma de `image`, que la app ya consume como URL absoluta versionada; así el
  contrato sigue siendo aditivo y no rompemos a nadie.

Verificado con los tres que mirasteis: `propaganda`, `cranes` y `kadavar` responden ahora con
`priority: true` y su crédito.

**Y lo de los 1,9 MB**: corregido en el contrato. Decía «~1 KB» porque se escribió cuando la capa
editorial estaba vacía, y ese número es justo el que sostiene la recomendación de usar la ficha
por slug. Gracias por medirlo.

## 2. El MBID: podemos dároslo mañana, y no os serviría

Aquí la respuesta corta es «sí, pero no así», y merece la pena explicarlo porque el problema que
describís es más profundo de lo que parece.

**Ya llamamos a MusicBrainz.** `riot/lib/artistInfo.js` consulta la API por cada artista al
construir nuestra base, y guarda país, año de formación, año de fin, tipo y **disambiguation**.
Es decir: los mismos datos duros que estáis sacando de TheAudioDB, ya los tenemos para 2.287
artistas. El `id` viene en esa respuesta y **lo estamos tirando**. Añadirlo es una línea.

**El problema es de dónde sale ese id.** Nuestra consulta es exactamente la que criticáis:

```
/ws/2/artist/?query=artist:"<nombre>"&fmt=json&limit=1
```

Búsqueda por nombre, y nos quedamos con el primero. Consultado hoy, «Nirvana» devuelve **cinco**
candidatos:

| score | país | años | quién es |
|---|---|---|---|
| 100 | US | 1987–1994 | banda de grunge de los 80–90 |
| 75 | GB | 1967– | la banda británica de los 60 |
| 74 | US | 2009– | (sin desambiguar) |
| 61 | FR | — | banda francesa de los 70 de Martigues |
| 61 | FI | — | banda punk finlandesa de principios de los 80 |

Nos quedamos siempre con el de arriba. Si os diéramos ese MBID, os estaríamos entregando **la
misma apuesta que hacéis vosotros, pero congelada en un UUID y con aspecto de dato fiable**. Eso
es peor que lo de ahora: hoy sabéis que estáis adivinando; con un identificador dejaríais de
saberlo.

Dicho de otro modo: el MBID no desambigua, **desambiguar es una decisión**. Y esa decisión, en
este proyecto, la toma la redacción.

## 3. Lo que proponemos

**Ahora, sin coste:** os exponemos en la ficha lo que ya tenemos de MusicBrainz —país, formación,
fin, tipo y la línea de `disambiguation`— marcado explícitamente como **coincidencia por nombre
sin verificar**. Con eso podéis, como mínimo, enseñar la desambiguación al lado de los años
(«1980s–1990s US grunge band») y que se vea de qué artista se está hablando. Y podéis decidir no
pintar la barra de años cuando la coincidencia sea dudosa, que es el caso que le chirrió a Pablo.

**Después, que es lo que de verdad lo arregla:** un paso de confirmación en el studio. En la
sección de Artistas, junto a «Verificar en servicios», la redacción ve los candidatos de
MusicBrainz con su desambiguación y elige. A partir de ahí ese artista tiene un MBID **verificado
por una persona**, y ese sí os lo damos como llave dura, con una marca que diga que está
confirmado. Para los artistas curados —que son los que os importan, como bien decís— eso cubre
los casos reales.

**Lo del now playing**: ahí no podemos. El objeto `song` lo compone AzuraCast a partir de las
etiquetas del fichero; meter un campo nuestro significaría escribir un `custom_field` en 15.000
ficheros de la biblioteca. La ficha editorial es el sitio, y coincidimos con vuestro apunte:
son 2.633 artistas y el dato no cambia nunca.

Sobre el `isrc` vacío: viene de las etiquetas de los ficheros, así que está vacío porque nuestra
biblioteca no lo trae. No es algo que podamos rellenar sin reetiquetar el catálogo.

## Lo que necesitamos de vuelta

Solo una cosa: si os vale el plan. Si con la desambiguación sin verificar ya podéis dejar la
ficha honesta, lo hacemos esta semana y el paso de confirmación va después, sin prisa.

---
_Servidor: radio Astra FM (`listen.astra.fm`). Contrato actualizado hoy:
`ARTISTAS_WEB_INSTRUCCIONES.md` en el portal._
