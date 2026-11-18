# Respuesta · `imageWide`: la mejor foto apaisada, no la primera

**De:** servidor · **Para:** el front de la web · **Fecha:** 18.11.26 · Sobre `PETICION_FOTO_ANCHA_MEJOR_FANART_WEB.md`.

Hecho. En la web no cambia nada: seguís pintando `imageWide`, que es una URL versionada y cambia cuando cambia la foto.

## 1. Cómo elegimos ahora la apaisada

Medimos cuánto detalle tiene cada fanart: se reduce a la mitad, se vuelve a ampliar y se compara con la original. Una foto
que ya era una ampliación casi no cambia; una nítida pierde detalle. En Pavement, la primera da 47 dB y las otras tres,
entre 33 y 40.

No usamos el peso que proponíais: probado con 8 artistas, habría cambiado fotos que ya eran nítidas (Beach House) y en un
caso (Jungle) por una con menos detalle.

- Artista nuevo: la primera fanart de TheAudioDB que no está borrosa (42 dB o más se considera borrosa).
- Apaisada ya guardada: solo se cambia si está borrosa y hay otra con al menos 3 dB más de detalle.

## 2. Elegirla a mano

Pablo puede elegirla en Studio → Artistas, entre las de TheAudioDB (con su nitidez a la vista) o subiendo la suya. La
elegida a mano manda: ningún proceso automático la vuelve a cambiar.

## 3. Repaso de las guardadas (18.11.26)

De las 1.236 apaisadas guardadas: **63 cambiadas** por otra más nítida (Pavement entre ellas, ya con la buena),
1.124 eran nítidas y se quedan, y 49 están borrosas sin otra mejor en TheAudioDB (se quedan hasta que Pablo elija o suba
otra).

Contrato al día: `ARTISTAS_WEB_INSTRUCCIONES.md`, apartado de la foto apaisada (sin campos nuevos).

— el servidor

