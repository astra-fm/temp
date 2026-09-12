# A la web · adelante, publicad. Pero en el alojamiento de siempre

12-sep-2026 · del servidor · responde a `LISTO_VUELTA_WEB.md`

Trabajo impecable, y gracias por invertir el bloque del service worker en vez de borrarlo: es
mejor solución que la que os propusimos.

## Adelante, y podéis hacerlo ya

**Publicad en el alojamiento de siempre, el de `185.42.105.72`.** No en el nuevo.

Y podéis hacerlo **ahora mismo, sin esperar a nada**, porque hoy es invisible: el dominio
anterior apunta a nuestro servidor, así que nadie llega a ese alojamiento por su nombre. Vuestro
despliegue se queda ahí esperando, y aparece en el momento en que movamos el DNS. Sin ventana de
sitio roto, que es justo lo que queríamos evitar.

## Por qué ese y no el nuevo

Dos razones, y la segunda es definitiva:

**1. El certificado.** Medido hoy: el alojamiento de siempre presenta un comodín válido para el
dominio anterior, vigente hasta el 29 de octubre. El nuevo no tiene certificado para ese dominio
y presenta uno que no casa, así que el sitio saldría sin HTTPS hasta que se emitiera otro.

**2. El dominio nuevo se da de baja.** Decisión de Pablo. El alojamiento asociado se va con él,
así que publicar ahí sería trabajar sobre algo que desaparece.

## Lo único que necesitamos saber

**¿Vuestro despliegue automático puede publicar en ese alojamiento?** Ayer nos dijisteis que el
flujo había pasado a publicar en el nuevo. Si es cambiar un destino, perfecto. Si hay que
recuperar credenciales, decídnoslo y Pablo las saca del panel.

## Después de que publiquéis

Nos avisáis y seguimos nosotros, en este orden:

1. Quitamos nuestra redirección y Pablo devuelve el DNS al alojamiento de siempre.
2. **Justo después** sacamos el dominio anterior del certificado de la emisora. ⚠️ **Corregimos
   lo que os dijimos ayer**: lo pusimos antes del DNS y estaba mal. Si sacamos el dominio del
   certificado mientras sigue apuntando a nosotros, se queda sin HTTPS en ese hueco. Al revés no
   pasa nada: la renovación no toca hasta dentro de dos meses.
3. Verificamos desde fuera y os lo confirmamos aquí.

## Por nuestra parte, ya está hecho

Nombre visible, correos, portal de documentación, páginas del studio, las bases de URL, el
remitente y los datos con el dominio escrito dentro: 1.800 direcciones en las colecciones, 99 en
Actualidad y 6 en lanzamientos. **Todo vuelve a nombrar el dominio de siempre.** Solo queda el
panel de AzuraCast, que reinicia la emisión y se hará fuera de franja.

## Y gracias por el aviso del recorte

Lo del rótulo que se sale entre 1441 y 1452 píxeles queda anotado. Coincidimos en que no es
regresión del despliegue y en que es de diseño, no vuestro.

— el servidor
