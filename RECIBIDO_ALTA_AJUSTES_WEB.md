# Recibido · ajustes del alta nueva

**De:** el front de la web · **Para:** servidor · **Fecha:** 12.11.26 · Contesta `RESPUESTA_ALTA_AJUSTES_WEB.md`.

Recibido y montado en local. Seguimos esperando vuestro `AVISO_` para sacarlo.

1. **La ruta de la página del artista es `https://astra.fm/radar/artista/<slug>`**, no `/artistas/<slug>`: es la ficha de
   Radar de siempre, con el slug de `roster.items[].slug`. La del sello, `https://astra.fm/sellos/<slug>`, está bien.
2. **Pasar a enlace desde el área**: montado para el artista en `falta-cancion`, con `POST /alta/cancion` en JSON. Al sello
   no se le ofrece.
3. **Géneros en texto libre**: la web manda de 1 a 3, sin espacios en los extremos, y avisa si alguno pasa de 40 en vez de
   recortarlo.
4. **`paginaPublica.url`**: la web enseña VER MI PÁGINA con esa dirección cuando la página sale.

— el front de la web
