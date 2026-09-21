# A la web · el cartel no va con el artista: decisión y limpieza hecha

21-sep-2026 · del servidor · responde a `CARTEL_AGENDA_MEDIDA_WEB.md`

Gracias por medirlo antes de tocar nada, y por sacar el desajuste del campo. La respuesta corta: **teníais
razón en que algo no cuadraba, pero el error estaba en nuestro lado y era más de fondo.**

## Lo que pasaba de verdad

Esos carteles que os llegaban por `imagen` **eran la foto de artista de su ficha**. En el studio, el
botón de imagen de un concierto de artista escribe en `api/artists/images/<slug>.jpg`, que es el retrato
que sale en su página, en la app y en cualquier sitio donde se le nombre. Al subir ahí el cartel de una
fecha, el cartel se convertía en la foto oficial del grupo. Medido: de los cinco conciertos vigentes,
**tres tenían un cartel de 800×355 por foto de artista** (Blood Red Shoes, Mercury Rev, Catupecu Machu).

## La decisión de Pablo

**El cartel no se relaciona con el artista.** Un cartel tiene una vida útil muy corta —caduca con su
fecha— y no tiene por qué tener relación visual con el artista que suena en la radio. Un concierto de
artista se relaciona con él **por las fechas**, no por una imagen del evento.

Por tanto, y esto cierra vuestra pregunta:

- **Opción 2 de las vuestras, pero al revés de como la planteabais:** el contrato se queda como está
  —`imagen` de un concierto **es la foto del artista y se puede recortar**— y lo que cambia es lo que
  mandamos: **por ahí no volverá a viajar un cartel**.
- **Los carteles solo existen en los festivales**, donde ya tienen su propio `imagen` y su propia regla
  («proporción original, no recortar»). Ahí está bien planteado desde el 1-sep.
- Vuestro apaño de medir la imagen ya no hará falta. Dejadlo si queréis como red de seguridad, pero no
  debería saltar nunca; si salta, avisadnos, porque significaría que se nos ha vuelto a colar uno.

## Lo que ya hemos hecho hoy

1. **Retirados los tres carteles** de las fichas de artista. Esas tres fichas llevan ahora una foto de
   artista de verdad (TheAudioDB, comprobado que es el artista correcto y no un homónimo). `conciertos.json`
   ya las sirve.
2. **Aviso en el studio**, junto al botón de imagen de la agenda: que ahí va la foto de la ficha, que sale
   en la web y en la app, y que los carteles no van ahí.
3. **Vuestra medida, aplicada donde toca:** el cartel de festival se guardaba reducido a **1000 px** y por
   eso se veía blando; el tope sube a **2400 px**, proporción original. Contrato actualizado.

## Lo que queda de vuestro lado

Nada obligatorio. Si la caja del hero ahora tiene la proporción 800:355 y por ahí van a llegar **fotos de
artista** (cuadradas o casi), revisadla: con la nueva realidad, lo que va a recibir el carrusel de
conciertos son retratos, no carteles. Los carteles seguirán llegando solo en las filas de festivales.

— el servidor
