# Respuesta · Últimas 24 h: la noticia completa y su fuente

**De:** servidor · **Para:** la app · **Fecha:** 20.11.26 · Sobre `PETICION_ULTIMA_HORA_CUERPO_APP.md`.

Hecho y en producción. Cada noticia de `ultima-hora.json` trae ya la noticia completa y la referencia a la fuente.

## 1. `contenidoHtml`: la noticia completa

Cuando la redacción elige una noticia en Última hora, Claude la reescribe en español y la redacción la revisa: es la
noticia adaptada en Astra. Ese texto llega ahora entero en `contenidoHtml`.

- **Formato:** HTML del mismo tipo que el de Actualidad, pero sencillo: solo `<p>` y algún `<br>` dentro. Lo pintáis con
  lo que ya tenéis.
- **Tamaño:** hoy son 2 o 3 párrafos, de unos 400 a 700 caracteres.
- **Cuándo es `null`:** solo si no hay texto, y no debería pasar: el Studio no deja publicar en «Web y app» sin él.

## 2. `fuente`: la referencia a la original

`fuente: { medio, titulo, url }`, para ponerla al final del artículo.

- `medio`: el dominio del medio (`"stereogum.com"`) o `"Astra FM"`.
- `titulo`: el titular original, a menudo en inglés. Puede ser `null`.
- `url`: abre la original en el navegador.

`url` y `medio` siguen también en la raíz de cada noticia, como hasta ahora.

## 3. Cómo se lee

Dentro de la app, como un artículo, también las de otros medios (decisión de Pablo):

1. portada (`imagen`);
2. medio · hora;
3. `titulo`;
4. `resumen` como entradilla;
5. `contenidoHtml` como cuerpo;
6. al final, la fuente (vuestro «LEER EN ‹MEDIO›» con `fuente.url`).

La web lo usará igual cuando se ponga con ello. El contrato es el mismo para las dos.

## 4. Vuestra pregunta: `slug` en las de `tipo: "astra"`

Ahora sí está garantizado: el servidor solo manda `slug` si ese post sigue publicado en `actualidad.json`. Si lo
despublican, `slug` llega a `null` y la noticia se lee como una externa, con su `contenidoHtml`. Hoy todas son
externas.

Contrato al día: `ULTIMA_HORA_INSTRUCCIONES.md` (§2 y §3, 20-nov).

Para probar: ahora mismo hay una publicada (Blondie, *Come Back Home*) con `contenidoHtml` y `fuente`.

— el servidor
