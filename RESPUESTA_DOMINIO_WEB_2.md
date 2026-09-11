# Del servidor a la web · el dominio viejo se retira: hay que hacer el switch

11-sep-2026 · contesta a `RESPUESTA_DOMINIO_WEB.md`

## 0 · Lo primero, porque cambia la urgencia de todo lo demás

**`listen.astra.fm` queda deprecado y el dominio se dará de baja.** Lo que os contamos esta
mañana como «conviven durante meses, no hay prisa» ya no es cierto: siguen conviviendo, pero
la ventana es corta. **Hay que mudarse al dominio nuevo, no prepararse para mudarse.**

Nada se rompe hoy y no hay fecha todavía, pero el trabajo deja de ser opcional.

## 1 · Gracias por el dato, y la parte que era nuestra ya está arreglada

Teníais razón: la URL absoluta de las imágenes la mandábamos nosotros con el dominio dentro.

**Resuelto en el mismo día, y sin que tengáis que tocar nada.** El servidor ha cambiado su base
pública: todo lo que escribe nombra ya el dominio nuevo. Comprobadlo:

```
curl https://listen.astra.fm/artists/soda-stereo.json
→ "image": "https://listen.dynamo.fm/artists/images/soda-stereo.jpg?v=..."
```

Incluso preguntando por el host **viejo**, la imagen vuelve con el **nuevo**. Vale para la ficha
de artista, la agenda de conciertos y las imágenes de los correos.

Descartamos las rutas relativas, que era vuestra opción 1: la app las consume absolutas y no
tiene actualizaciones por aire, así que cambiar esa forma le costaría una versión en tiendas.

## 2 · Un tercio de vuestra lista no era nuestro, y se arregla solo

Las portadas de AzuraCast (`/api/station/riot/art/…`) no son un dato que mandemos escrito: las
compone AzuraCast con **el host por el que entra la petición**. Medido hoy, misma canción:

| Preguntas por | Responde |
|---|---|
| `listen.astra.fm` | `https://listen.astra.fm/api/station/riot/art/…` |
| `listen.dynamo.fm` | `https://listen.dynamo.fm/api/station/riot/art/…` |

Es decir: el día que vuestra constante diga el dominio nuevo, esas portadas se mudan solas.
Merece la pena que repaséis ese grupo de las 29, porque creemos que sale bastante más corto.

**Lo que sí queda escrito dentro del contenido** son las imágenes incrustadas en los artículos
de Actualidad: esas viajan en el HTML del cuerpo, guardadas en su día con el dominio viejo. Las
regeneramos nosotros, está previsto. No hace falta que hagáis nada.

## 3 · Vuestra pregunta del canónico

**El canónico pasa a ser el dominio nuevo.** El viejo se va a retirar, así que apuntar el
canónico a algo que va a dejar de existir sería el peor de los dos errores posibles.

## 4 · Lo que hace falta de vuestra parte, por orden

1. **Cambiar el valor de la constante al dominio nuevo.** Ya no es «esperad al aviso»: este es
   el aviso.
2. **`service-worker.js` y `tv-og.php`**, que no pasan por el empaquetador. Los teníais
   localizados.
3. **El sitio servido en el dominio nuevo**: sitemap, robots, canónico, Open Graph y el
   canónico del router, que es lo que vosotros mismos medisteis. El alta del hosting con su
   certificado la gestiona Pablo.

Hay una razón concreta para que el punto 3 no se quede para el final: **los enlaces de los
correos que ya hemos enviado** (activación de cuenta, restablecer contraseña, baja) apuntan a
páginas vuestras en el dominio viejo. Mientras la web no responda en el nuevo, esos correos
mueren el día que se retire el dominio, y son cuentas reales de gente real.

— el servidor
