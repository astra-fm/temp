# Aviso a la web · el bloque `musicbrainz` ya está en la ficha

**Del servidor** · 09.09.26 · va detrás de `RESPUESTA_FICHA_Y_MBID.md`.

Pablo dio el visto bueno al primer paso y ya está en producción. No hay que esperar a nada.

## Qué ha cambiado

`GET https://listen.astra.fm/artists/<slug>.json` incluye, **cuando lo tenemos**, un bloque más:

```json
"musicbrainz": {
  "verificado": false,
  "pais": "US",
  "formado": "1987",
  "fin": "1994",
  "tipo": "banda",
  "desambiguacion": "1980s–1990s US grunge band"
}
```

Cubre **2.287 artistas**. Si no hay datos, el bloque no viene: nunca llega vacío.

Podéis probarlo ahora mismo con vuestro propio ejemplo:

```
curl -s https://listen.astra.fm/artists/nirvana.json
```

## Lo que os pedimos que hagáis con él

**`desambiguacion` es lo que resuelve vuestro problema**, más que ningún identificador. Enseñada
junto a los años, el lector ve de qué artista se habla: la diferencia entre «En activo 1987–1994»
y «En activo 1987–1994 · banda de grunge de los 80–90».

Y si la desambiguación no cuadra con lo que está sonando, **no pintéis la barra de años**. Un
hueco es mejor que un dato falso, que es justo lo que le chirrió a Pablo.

## Por qué sigue sin venir el `mbid`

Lo explicamos en la respuesta anterior y lo repetimos aquí porque es la parte importante: esos
datos salieron de una búsqueda por nombre quedándonos con el primer resultado, igual que la
vuestra. «Nirvana» devuelve cinco artistas en MusicBrainz y siempre gana el de Cobain. Daros un
identificador ahí sería la misma apuesta con aspecto de dato fiable.

De ahí el `verificado: false`. Cuando exista el paso de confirmación en el studio —la redacción
elige el candidato correcto— ese campo pasará a `true` y **entonces sí vendrá el `mbid`**. El
contrato es aditivo: si ya leéis `verificado`, ese día no tenéis que tocar nada.

De paso, el servidor ha dejado de descartar el identificador cuando consulta MusicBrainz, así que
estará disponible en cuanto se reconstruya la base.

## Lo otro que pedisteis, también hecho

`/artists/<slug>.json` lleva ya `priority`, `imageSource` e `imageSourceName`. Verificado con
`propaganda`, `cranes` y `kadavar`.

Todo está en `ARTISTAS_WEB_INSTRUCCIONES.md`, en el portal: el bloque nuevo es el §2.2, con la
tabla de los cinco Nirvana y las reglas de uso.

---
_Servidor: radio Astra FM (`listen.astra.fm`)._
