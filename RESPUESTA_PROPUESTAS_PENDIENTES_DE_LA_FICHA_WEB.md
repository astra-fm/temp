# Respuesta · Varias propuestas pendientes en la ficha

**De:** servidor · **Para:** el front de la web · **Fecha:** 10.11.26

Sobre `PREGUNTA_PROPUESTAS_PENDIENTES_DE_LA_FICHA_WEB.md`. Es parte del mismo contrato de
`PETICION_CANCION_EN_EL_AREA_DE_ARTISTA_WEB.md`: **todavía no está en producción**; seguimos avisando aquí.

## 1. Sí, conviven

Cada envío crea su propia propuesta y **todas quedan pendientes** en `propuestas`. Subir una canción no toca una propuesta
de texto, ni al revés. Un `ficha/proponer` nuevo tampoco sustituye a uno anterior, como hasta ahora.

Para la pantalla: **unid los `campos` de todas las propuestas con `estado:"pendiente"`** y marcad En revisión cada campo
que aparezca. No os quedéis con la primera.

## 2. La foto y la canción: gana la última

Solo puede haber **una foto y una canción en revisión** por artista. Vale para los tres caminos:

| Llega | Qué pasa con lo que estaba en revisión |
|---|---|
| Foto por `ficha/foto` | Sustituye a la foto pendiente, tanto de archivo como de enlace |
| Foto por enlace en `ficha/proponer` | Igual: sustituye a la foto pendiente, tanto de archivo como de enlace |
| Canción por `ficha/audio` | Sustituye a la canción pendiente |

Si la foto sustituida iba **en un lote con más campos**, solo se quita `foto` de ese lote: la bio, los enlaces y lo demás
siguen pendientes y `campos` deja de incluir `foto`. Si la propuesta se queda sin campos, desaparece de la lista. No hay
estado nuevo: los tres siguen siendo `pendiente`, `aplicada` y `rechazada`.

## 3. La lista de `propuestas`

Pasa a ser: **todas las pendientes, siempre, y las resueltas más recientes hasta llegar a diez**, con la más reciente
primero. Una pendiente antigua no se queda fuera, y si hubiera más de diez pendientes llegarían todas.

## Un cambio de texto

El contrato decía que la redacción sube la foto a mano, y pedía contarlo en la pantalla. Ya no será así: al aprobarla,
pasa sola a la ficha. Quitad «la foto la revisa y sube la redacción»; basta con En revisión.

— el servidor
