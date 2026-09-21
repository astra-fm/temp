# El cron de colecciones es diario y el ciclo editorial es semanal

21-09-2026 · de la app · para el servidor

## Lo que hemos medido hoy

`https://listen.astra.fm/collections/212.json` —HCR— trae `updatedAt: 2026-09-21T03:16:07Z`. Hoy es
**lunes**. El índice `collections.json`, en cambio, sigue en `2026-09-18T03:00:05Z`, que es el
viernes. Coincide con lo que nos dijisteis el 11.09: son dos crones, el índice semanal y las
canciones diarias.

**El problema no es que se regenere: es que se regenera entera.** En el móvil de Pablo, el delta
entre lo que tenía descargado y la selección de hoy dio **+100 / −100** en tres listas a la vez
—279 canciones de golpe—. Cero solapamiento. Cuadra con la regla de generación, que elige el top
del programa *sin repetir las de generaciones anteriores*.

Hemos descartado que sean los mismos temas con `uniqueId` nuevo: las cien portadas de HCR llevan
marca de tiempo de entre diciembre de 2025 y el 17 de septiembre, ninguna de hoy. Los ficheros son
viejos y conservan su id; lo que cambia es la selección.

## Por qué nos importa

Para quien descarga, **una lista bajada caduca cada madrugada**. Actualizar significa bajar cien
canciones y borrar otras cien —un giga por lista— todos los días. Y contradice lo que la app enseña
en la ficha de cada lista, que es la copia editorial de siempre: **«una nueva lista cada viernes»**.

## Qué hemos hecho en la app, y qué os pedimos

**En la app, decisión de Pablo del 21.09.26:** el aviso de lista rehecha pasa a ofrecerse **solo a
partir del viernes a las 8:00 (Europe/Madrid)**. Un cambio solo se ofrece si es anterior a ese
corte, así que lo que alguien descargó esta semana no se marca como viejo aunque el fichero haya
cambiado tres veces. Ya está implementado y no depende de vosotros.

**Lo que os pedimos es alinear el origen:** que `collections/<id>.json` **se regenere una vez por
semana, los viernes**, como el índice, en vez de a diario. Con el cron diario, cada día generáis y
publicáis una selección que ningún front va a ofrecer hasta el viernes siguiente, y el trabajo se
tira.

**Si el cron diario tiene una razón que no conocemos** —por ejemplo que la emisión necesite rotar el
contenido a diario aunque la descarga sea semanal—, decídnoslo y lo dejamos como está: nuestra
ventana ya protege al oyente. Lo que no puede quedarse es que nadie sepa cuál de las dos cadencias
es la buena.

**Una pregunta concreta, por si acaso:** ¿el `updatedAt` de `collections/<id>.json` cambia todos los
días **aunque la selección sea la misma**? Si fuera así, el problema sería otro —un timestamp que se
mueve sin contenido nuevo— y la solución también.

— agente de la app
