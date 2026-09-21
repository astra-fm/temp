# Al servidor · urgente: la agenda se ha quedado sin carteles

21-sep-2026 · de la web · sigue a `RESPUESTA_CARTEL_AGENDA_WEB.md`

Vuestra limpieza era correcta y no la discutimos: **un cartel no puede guardarse como la foto de la
ficha de un artista**. Pero ha tenido un efecto que no vimos ninguno de los dos, y Pablo lo ha visto
en cuanto ha mirado la portada:

> «Se están mostrando carteles de los artistas y no de los conciertos.»

## Lo que pasa

El carrusel del hero es **la agenda de conciertos**. Su imagen era el cartel de la fecha. Al retirar
los carteles, lo único que le queda por pintar es `imagen`, que ahora es **el retrato del artista** —el
mismo que sale en su ficha, en la app, y a veces el mismo que está en el hero de al lado—.

Un módulo que anuncia conciertos enseñando el retrato del grupo no anuncia el concierto. **El dato que
falta no es la foto: es el cartel.**

## Lo que os pedimos

**Un campo propio para el cartel del concierto**, como el que ya tienen los festivales:

```
"imagen":  "…/artists/images/<slug>.jpg"     ← la foto del artista, como ahora. No se toca.
"cartel":  "…/conciertos/images/<id>.jpg"    ← el cartel de ESA fecha, o null
```

⚠️ Ojo con el nombre: en el bloque de festivales `cartel` ya existe y es **texto** —«artistas
destacados»—. Si eso os estorba, llamadlo `imagenEvento`, `poster` o lo que prefiráis; lo que
necesitamos es **que sean dos campos distintos**, no cómo se llamen.

Con eso:

- el cartel deja de contaminar la ficha del artista, que es lo que queríais arreglar;
- la agenda vuelve a enseñar el cartel cuando lo hay;
- y cuando no lo haya, pintamos el retrato, que es lo que hay hoy.

**La medida**: la que ya habéis puesto para festivales, **≤2400 px, proporción original, sin
recortar**. Nosotros la respetamos: la web mide la imagen y si es más ancha que 2:1 la deja entera.

## Mientras tanto

Hemos devuelto la caja del hero a la proporción del nodo para que un retrato cuadrado no salga
partido por la mitad —estaba perdiendo el 56 % del alto—. En cuanto haya campo de cartel, la caja se
adapta otra vez a lo que toque, que es un cambio de una línea de nuestro lado.

Decidnos si podéis y cuándo.

— el front de la web
