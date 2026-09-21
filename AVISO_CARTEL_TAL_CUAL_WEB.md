# A la web · El cartel de concierto se sirve tal cual: cambia la extensión y pesa más

21-09-2026, del servidor. Responde a nada vuestro; es consecuencia de una decisión de Pablo de esta
tarde. Contrato actualizado: `https://listen.astra.fm/docs/CONCIERTOS_WEB_INSTRUCCIONES.md`.

El campo, la ruta y el sitio en `conciertos.json` **no cambian**. Cambian dos cosas del fichero.

## 1 · La extensión ya no es siempre `.jpg`

El servidor **guarda el cartel exactamente como se sube**: no lo reescala, no lo recorta y no lo
recodifica. Antes lo pasaba por ffmpeg y salía siempre JPEG; ahora conserva el formato original, así
que la URL puede acabar en **`.jpg`, `.png` o `.webp`**.

De hecho, los cuatro carteles de la agenda de ahora mismo son **`.png`**:

```
https://listen.astra.fm/conciertos/images/cartel-5051071b14b0.png?v=1790016035195
```

**Usad la URL tal cual llega en el campo `cartel`.** Si en algún sitio construís la ruta o dais por
hecho `.jpg`, ahí se rompe. Ya está corregido en el contrato.

## 2 · Llegan a 2508 × 1200 y pesan entre 3 y 4,7 MB

La redacción los está exportando al doble de vuestro componente (1254 × 600), que para retina está
bien, pero en PNG eso son **3–4,7 MB por cartel** y hay cuatro en el carrusel de la home. Medido
ahora: `content-type: image/png`, 3.059.267 bytes el más ligero.

No los vamos a tocar desde el servidor —es decisión de Pablo que lleguen tal cual—, así que si eso
os estropea la métrica de carga de la home, decidnos qué preferís y se habla: exportarlos en JPEG
desde diseño es lo más probable.

Lo que sí os conviene de vuestro lado, mientras tanto: `loading="lazy"` en los que no sean el primer
hueco del carrusel y `decoding="async"`, para que el peso no bloquee el render.

— el servidor
