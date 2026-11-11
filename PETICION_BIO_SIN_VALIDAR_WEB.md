# Petición · La bio se publica al momento, sin pasar por la redacción

**De:** el front de la web · **Para:** servidor · **Fecha:** 11.11.26 · Decisión de Pablo del 11.11.26.

Pablo, probando el área: «Las bios no se tendrían que validar, son muchas trabas para el sello o el artista».

## Qué cambia

Hoy (§5.2) todo lo público de la página entra como propuesta `pendiente` y la redacción lo aplica. A partir de ahora,
**la bio no**: se publica en el momento.

Vale para el artista en su página (`POST /ficha/proponer`) y para el sello por su artista (`?artista=`). Y, si cuadra
con lo pedido en `PETICION_SELLO_CREA_ARTISTA_CON_SUS_DATOS_WEB.md`, también para la bio que el sello escribe al crear al
artista, que quedaría guardada para cuando nazca su página.

## Lo que necesitamos

1. Que `bio` se aplique directamente a `publicada.bio` y la respuesta ya traiga la `cuenta` (o el artista, en
   `GET /roster/:id`) con la bio nueva.
2. Que no cree propuesta, o, si llega en un lote con otros campos, que se publique la bio y el resto siga como propuesta.
3. Que no deje marca de pendiente: hoy pintamos «PROPUESTA PENDIENTE» o «PROPUESTA DE …» desde `propuestas[].campos`.
4. **Si el artista y su sello la cambian**, gana la última; decidnos si queréis otra cosa.
5. El límite que veáis (largo máximo, cuántos cambios al día) y su código de error, para decirlo en el campo.

## Lo que queremos confirmar

- ¿Solo la bio, o también otros datos de texto (ciudad, país, web, la bio del sello «Sobre el sello»)? Pablo ha hablado
  de las bios; si queréis aplicar el mismo criterio a algo más, decídnoslo y lo pintamos igual.
- La redacción ¿necesita enterarse de alguna forma (un registro de cambios en el Studio) o le basta con verlo publicado?

Cuando esté, la web lo dice en el campo («Se publica al momento») y quita la marca de pendiente de la bio.

— el front de la web
