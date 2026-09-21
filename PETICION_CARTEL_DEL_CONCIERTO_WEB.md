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


---

## 🔴 Añadido el mismo día · esto no era un error de la redacción

**Corregimos lo que os dijimos.** Habíamos aceptado vuestra lectura —«alguien subía el cartel donde va
la foto»— y Pablo nos ha parado:

> «Los carteles han sido creados en la sección **conciertos** de la administración para que alimenten
> el carrusel de la home. No tienen nada que ver con los artistas que mostramos en la radio y su
> información.»

O sea: **no era una equivocación de quien subía, era contenido hecho a propósito para ese módulo**. La
equivocación está en que el sistema los guarda en la ruta del retrato del artista, que es donde
vosotros los encontrasteis y, con razón desde vuestro punto de vista, los retirasteis.

**Los ficheros siguen vivos**, comprobado hace un minuto: las tres URL versionadas que servía
`conciertos.json` ayer siguen devolviendo los carteles a 800×355.

```
/artists/images/blood-red-shoes.jpg?v=1789877421976   800×355  ✓
/artists/images/mercury-rev.jpg?v=1789289425812       800×355  ✓
/artists/images/catupecu-machu.jpg?v=1788253459714    800×355  ✓
```

⚠️ **Por favor, no los borréis**: son el trabajo de la redacción para el carrusel, no restos de un
error.

## Lo que pedimos, ya con el motivo completo

Lo mismo de arriba, pero ahora sabemos por qué corre prisa: **la sección de conciertos de la
administración necesita guardar su cartel en un sitio propio**, no en la ruta del artista. Con eso:

- el retrato del artista se queda limpio, que es lo que queríais;
- el cartel vuelve al carrusel, que es para lo que la redacción lo hace;
- y nadie tiene que elegir entre las dos cosas.

Si la ruta `/conciertos/images/<id>.jpg` que ya cita vuestro contrato para festivales sirve también
para esto, mejor: es un campo más en cada fila de `conciertos`, y la web lo pinta en cuanto llegue.

— el front de la web
