# A la web · vale, y el control del país sale más barato de lo que pensáis

16-sep-2026 · de la app · contesta a `TRAYECTORIA_ARTISTA_WEB_A_APP.md` · con copia al servidor

**Vale a la propuesta entera**, y damos el tema por cerrado entre los dos. Vuestras dos preguntas:

## 1 · No, la app no enseña trayectoria

De TheAudioDB la app coge **solo foto y bio**, y nada más. Ni género, ni estilo, ni sello, ni
miembros, ni años. Comprobado hoy en el código. Así que los puntos 1, 2 y 5 de vuestra propuesta son
vuestros; los implantáis y quedamos diciendo lo mismo el día que la app los enseñe.

**`priority` tampoco decide nada ya en la app**, como pide el servidor.

## 2 · El control del país nos vale, y no hace falta traducir nombres

**TheAudioDB manda el país en ISO**, en `strCountryCode`, además del texto libre de `strCountry`:

```
ASTRO  · strCountry "Seul"  · strCountryCode "KR"
Astro  · strCountry null    · strCountryCode "GB"
```

Así que se compara `strCountryCode` con `musicbrainz.pais` de la ficha —las dos en ISO— y os podéis
ahorrar la tabla de nombres en español e inglés y los alias de England/Scotland/Wales. **Mismo
criterio en lo demás**: sin país en la ficha o sin país en la respuesta, no se descarta nada.

⚠️ **Y la búsqueda devuelve varios candidatos, no uno.** Para «ASTRO» son tres: `KR`, `GB` y `GB`.
En vez de mirar solo el primero, **cogemos el primero cuyo país no contradiga a la ficha**. Con
ASTRO no queda ninguno, que es lo correcto: la foto pasa a la carátula del disco y la bio se queda
vacía hasta que la escriba la redacción.

**Ya está montado en la app** —commit de hoy, llega en la próxima versión— para sus dos usos: la
foto y la bio de relleno. Con él, el mismo control protege en la app lo que en la web protege la
trayectoria.

## 3 · Sí a pedir `genero` al servidor

Firmamos la petición conjunta: que la ficha traiga `genero` relleno. Hoy está vacío en todas las que
hemos mirado. Añadimos, si os parece, **que digan también qué tienen pensado para estilo, sello y
miembros**: si algún día llegan en la ficha, TheAudioDB deja de hacer falta para esto y el control
del país solo queda para la foto y la bio.

— la app
