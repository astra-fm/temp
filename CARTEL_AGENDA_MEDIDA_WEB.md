# Al servidor · la medida del cartel de la agenda, y un campo que dice otra cosa

21-sep-2026 · de la web

Contrato de referencia: `CONCIERTOS_WEB_INSTRUCCIONES.md`, el campo `imagen` de `conciertos.json`.

## Lo que ha pasado

Pablo vio que **los carteles del carrusel del hero salían cortados**. Lo medimos antes de tocar nada:

- el cartel que servís es de **800 × 355** (2,2535);
- la caja donde entraba **no tenía proporción fija** —su alto lo ponía el cuadrado de emisión, topado
  en 530, y su ancho era lo que sobrara—, así que iba de 2,078 a **3,19 a 1920**;
- con `object-fit: cover`, a 1920 se perdía el **29,5 % del alto**: justo la banda donde el cartel
  lleva el nombre del grupo.

**Arreglado en la web**, y con una decisión de Pablo detrás: la caja ha pasado a llevar **la
proporción del cartel**, no la del nodo de diseño. Medido a doce anchos, de 390 a 2560: entra justo,
sin recorte y sin banda, en todos.

## La medida, para la redacción

**Proporción 800 : 355.** En píxeles pedimos **2400 × 1065** (el triple): los 800 × 355 de hoy se
estiran hasta 932 de ancho a 1440 y se ven blandos. El mínimo razonable es 1600 × 710.

Pablo confirma que **a partir de ahora los carteles vendrán con esa medida**. Si algún día cambia,
avisad antes de subirlos: la retícula del hero se deriva de esa proporción, así que un cartel con otra
forma volvería a dejar banda.

## 🔴 Y un desajuste que conviene cerrar

El contrato dice que ese campo es **«la foto editorial del artista (cuadrada no garantizada —
recortad con `object-fit: cover`)»**. O sea: el contrato nos manda recortar, y por ahí llegan
**carteles con letra dentro**, que es justo lo que no se puede recortar.

Hoy la web lo distingue **midiendo la imagen** —más ancha que 2:1 no es una foto, es un cartel, y un
cartel entra entero—, y funciona, pero es una suposición nuestra contra lo que está escrito.

Dos maneras de cerrarlo, la que prefiráis:

1. **Un campo que lo diga** —`tipo: "cartel" | "foto"`, o un `cartel` aparte del `imagen` de siempre—.
   Es lo que ya hacéis en el bloque de festivales, donde `imagen` es «el cartel/imagen promocional
   (≤1000px, proporción original)». Ahí está claro y aquí no.
2. **Cambiar la frase del contrato**, si la decisión es que ese campo lleve carteles: que diga
   «proporción original, no recortar» y nos quitamos la adivinanza.

Mientras tanto seguimos midiendo, así que no hay prisa; pero si un día sube una foto apaisada de más
de 2:1, la trataremos como cartel y no se recortará.

— el front de la web
