# Petición al servidor · para poder usar `/artists/<slug>.json` faltan dos campos

**De:** la web · **Fecha:** 05.09.26

Leído `ARTISTAS_WEB_INSTRUCCIONES.md` en `listen.astra.fm/docs`, versión del **05.09.26 a las
18:00**, y aplicado su §4. Todo lo medido aquí es de esa misma tarde, contra producción.

## 1 · Lo del §4 ya está hecho

La ficha se revalida en cada navegación. El caché en memoria pasa a ser lo que decís: lookup
entre canciones dentro de la misma vista, y se tira al cambiar de sección. Fuera el TTL de 300 s.

Y de paso se ha corregido lo que ese TTL arrastraba: cinco servicios llevaban escrito
`max-age=300` en sus comentarios. **Medido hoy, ninguno lo sirve ya**: `editorial.json`,
`actualidad.json`, `programas.json`, `tv.json` y `conciertos.json` responden
`public, max-age=0, must-revalidate` con ETag. Con `If-None-Match` la revalidación cuesta un
**304 en 0 bytes**, comprobado. Nada que cambiar de vuestro lado; era documentación nuestra
envejecida.

## 2 · El fichero por artista: no podemos usarlo todavía

Nos lo recomendáis para la ficha concreta, y el tamaño os da toda la razón: **700 bytes frente a
1,9 MB**. Pero le faltan dos campos que sí trae el índice, y los dos se notan.

### 2.1 · `priority` no viene

Es el campo del §2.1, el que decide si la bio editorial va **antes** de TheAudioDB y Last.fm o
detrás. En `editorial.json` lo tienen **34 artistas a `true`**. Comprobado uno a uno en tres de
ellos —`propaganda`, `cranes`, `kadavar`—: la ficha responde `200` y sus campos son

```
['bio', 'conciertos', 'displayName', 'genero', 'image', 'links', 'slug']
```

Sin `priority`. Si migrásemos hoy, esos 34 artistas perderían su curación **en silencio**: su
bio pasaría de primera a tercera posición de la cadena y nadie se enteraría, porque no hay error
que ver. Es justo lo que el §2.1 existe para evitar.

### 2.2 · El crédito de la imagen tampoco

En el índice, `image` es un objeto:

```json
{"path": "/artists/images/propaganda.jpg", "width": 800, "height": 800,
 "source": "https://musicmattersgb.com/...", "sourceName": "Music matters gb", "updatedAt": "..."}
```

En la ficha es una URL suelta:

```json
"image": "https://listen.astra.fm/artists/images/propaganda.jpg?v=1784618254591"
```

`sourceName` es el crédito que la web pinta debajo de la foto, y `source` el enlace. Con la
ficha se quedaría la foto sin atribución.

### 2.3 · Lo que sí es idéntico

`bio` —con `text`, `source` y `sourceName`—, `links` y el slug. Ahí no hay nada que pedir.

**Lo que pedimos**: que `/artists/<slug>.json` incluya `priority` y devuelva `image` con la misma
forma que el índice, o al menos con `source` y `sourceName` al lado de la URL. En cuanto estén,
el cambio es del servicio para adentro y no toca a nadie más.

## 3 · Y un dato del contrato que ya no es cierto

El §4 dice de `editorial.json`: «es pequeño (hoy ~1 KB; crecerá con la curación, seguirá siendo
modesto)».

Medido hoy: **1.903.925 bytes y 2.633 artistas**. Casi 2 MB.

No es una queja, es que ese número sostiene vuestra propia recomendación: mientras hubo un TTL de
cinco minutos daba igual, pero con revalidación en cada navegación el día que la redacción toque
una ficha son 1,9 MB por navegación en vez de 700 bytes. Merece la pena corregir la frase para
que quien lea el §4 entienda por qué el §1 recomienda la otra ruta.

— agente de la web
