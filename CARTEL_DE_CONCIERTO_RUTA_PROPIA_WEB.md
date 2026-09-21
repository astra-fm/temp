# Al servidor · el cartel del concierto tiene que venir por una ruta propia

21-sep-2026 · de la web · **encargo directo de Pablo**

Gracias por devolver los carteles: ya se ven en el carrusel de la portada. Pero han vuelto **por la
misma ruta que la foto del artista**, y eso deja el problema a medias. Pablo pide que se cierre bien:

> Que el cartel que pertenece a conciertos se devuelva **por una ruta diferente**.

## Cómo llega hoy

```
Holy Wave        /artists/images/holy-wave.jpg?v=…        600×600   ← retrato del artista
Blood Red Shoes  /artists/images/blood-red-shoes.jpg?v=…  800×355   ← cartel del concierto
Mercury Rev      /artists/images/mercury-rev.jpg?v=…      800×355   ← cartel del concierto
Catupecu Machu   /artists/images/catupecu-machu.jpg?v=…   800×355   ← cartel del concierto
```

Cuatro filas, misma ruta, mismo campo, **dos cosas distintas dentro**. Y con esto siguen en pie los
dos problemas, el vuestro y el nuestro:

- **El vuestro**: el cartel sigue siendo la foto oficial del grupo. Lo que os llevó a limpiarlo esta
  mañana no está resuelto: si alguien abre la ficha de Mercury Rev, su retrato es un cartel.
- **El nuestro**: la web **no puede distinguirlos**. Lo único que tenemos es medir la imagen y suponer
  que más ancha que 2:1 es un cartel. Es una adivinanza, y ya nos ha fallado dos veces en dos días.

## Lo que pedimos

**Ruta propia y campo propio.** Lo que ya hacéis con los festivales:

```json
{
  "artista": "Mercury Rev",
  "imagen":  "https://listen.astra.fm/artists/images/mercury-rev.jpg?v=…",   // el retrato. No se toca.
  "cartel":  "https://listen.astra.fm/conciertos/images/<id>.jpg?v=…"        // el cartel de ESA fecha, o null
}
```

- **La ruta**: `/conciertos/images/…`, la que vuestro contrato ya cita para festivales. Que el fichero
  del cartel **no se guarde nunca** bajo `/artists/images/`.
- **El campo**: uno nuevo, `null` cuando ese concierto no tenga cartel. Si `cartel` os estorba porque
  en festivales ya es texto, llamadlo `imagenEvento` o `poster`: lo que importa es que sean dos.
- **La medida**: la vuestra de hoy, ≤2400 px y proporción original. Lo que llega ahora —800×355— lo
  pintamos bien, y con más resolución se verá mejor.

## Qué desbloquea

Con el campo separado, y sin adivinar nada:

1. **La ficha del artista queda limpia**, que es lo que os importaba.
2. **El carrusel pinta el cartel cuando lo hay.**
3. Y Pablo puede decidir qué hacer con un concierto **sin** cartel —hoy, Holy Wave—: enseñarlo con el
   retrato, o no sacarlo en el carrusel. Hoy no podemos ni ofrecerle la opción, porque no sabemos cuál
   es cuál.

Mientras tanto no tocamos nada más: los carteles se ven y se recortan un 7 % del ancho, que es
inofensivo. Decidnos si podéis y cuándo.

— el front de la web
