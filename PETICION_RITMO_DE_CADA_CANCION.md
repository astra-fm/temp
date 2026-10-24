# El ritmo de cada canción en `emisora/onair.json`

24-10-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 24.10.26

## Por qué

Desde el 23.10.26, en Radio · En directo, **Sonorama y Crepúsculo** pueden cambiar la foto del artista
por un **visor de animaciones**. Pablo ha pedido dos cosas:

1. **Una animación por tema**, que no cambie a mitad de canción.
2. **Que la animación vaya con la música**: con una canción lenta, una animación suave; con una con
   ritmo, una más movida.

Hoy la web saca el ritmo **escuchando** el audio en el navegador (las bandas del analizador). Funciona,
pero necesita oír **de 8 a 14 segundos** de cada canción antes de saber si es lenta. Mientras tanto
sigue la animación de la canción anterior y luego cambia, así que el cambio cae siempre dentro del
tema nuevo. Es justo lo que Pablo no quiere. Además, en Safari no hay analizador y todo sale «lento».

Vosotros tenéis los archivos: el tempo se puede calcular **una vez por canción**, sin prisa, y
mandarlo con lo que suena. Así la web cambia la animación **en el mismo momento en que cambia
`ahora`**, una sola vez y del tipo correcto.

## La petición

Añadir a `ahora` (y, si es fácil, a cada elemento del historial que ya mandáis) un objeto `ritmo`:

| Campo | Qué es | Si no se sabe |
|---|---|---|
| `ritmo.bpm` | El tempo **que se percibe**, en pulsos por minuto, redondeado | `null` |
| `ritmo.pulso` | `true` si la canción tiene un pulso claro (batería, bombo); `false` en ambientes, piano solo, voz… | `null` |
| `ritmo.golpes` | Golpes claros por segundo (densidad de ataques), con un decimal | `null` |

Si no hay `ritmo` (o vale `null`), la web sigue escuchando como hoy: no se rompe nada.

## Cómo lo calculamos nosotros, por si os sirve

Lo hemos medido contra **librosa** (`librosa.beat.beat_track`) con la emisión grabada y con avances de
diez canciones. Lo que aprendimos:

- **El doble y la mitad son el problema.** Las baladas se leen al doble (Radiohead «No Surprises» sale
  a 150 en vez de 75) y el rock rápido a la mitad o al doble según el tramo (Eagulls, 89 o 177). Lo
  que necesitamos es el tempo **que se siente**: llevarlo siempre a la franja **70 a 140** lo resolvió
  en nueve de diez canciones (falló Justice «D.A.N.C.E.», que se lee a 75).
- **Mejor la canción entera**, o un tramo central, que los primeros segundos: muchas empiezan con una
  intro sin batería.
- Con eso, la web decide: por debajo de **85** o sin pulso, animación suave; desde **115** con 2,5
  golpes por segundo o más, la más movida; en medio, la intermedia. Los umbrales son nuestros; vosotros
  solo mandad los números.

## Mientras tanto

La web sigue como está: un solo cambio de animación por tema, entre 8 y 14 segundos después de que
empiece. En cuanto `ritmo` llegue en `onair.json`, lo leemos y el cambio pasa al principio de la canción.

— el front de la web
