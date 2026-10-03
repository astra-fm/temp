# A la web y a la app · Actualidad: enlaces de fuente arreglados y tablas usadas como columnas

03-10-2026, del servidor. No responde a nada vuestro: es un encargo de Pablo. El contrato no cambia
(`https://listen.astra.fm/docs/ACTUALIDAD_WEB_INSTRUCCIONES.md` y el §1 de
`https://listen.astra.fm/docs/APP_MOVIL_INSTRUCCIONES.md` siguen valiendo palabra por palabra). Lo que
cambia es el contenido de `actualidad.json` y cómo lo está usando la redacción. Este aviso se borra
cuando lo hayáis comprobado.

## 1. Enlaces de fuente: arreglados en los datos, no tenéis que hacer nada

En muchas imágenes del cuerpo el campo de fuente se había rellenado como `Fuente: https://…`. El
resultado era `<a class="image-source" href="Fuente: https://…">`: el enlace Fuente llevaba a una
dirección que no existe.

- **33 imágenes en 26 artículos** tenían ese prefijo en `data-source` y en el `href`. Corregido.
- **16 imágenes** tenían la URL de la fuente en el pie (`data-caption="Fuente: https://…"`). Ahora la
  URL está en `data-source` y en el `href`, y el pie queda vacío. **15 de ellas eran un `<img>` suelto
  con `data-caption`, sin `figure`**: ese pie no se veía en ningún sitio. Ahora tienen la forma del
  contrato: `figure.image-figure > img + figcaption > a.image-source`.
- Desde hoy el editor del Studio quita el `Fuente:` solo, así que no debería volver a pasar.

Medido hoy en `https://listen.astra.fm/actualidad.json`: **0** `href` que empiecen por `Fuente:`,
**0** `data-source` con `Fuente:` y **0** pies con `Fuente:`.

## 2. Tablas usadas para maquetar a columnas: comprobad cómo las pintáis

La redacción usa `table.editor-table` para poner **dos columnas**: texto a un lado e imagen al otro,
o dos imágenes juntas. Las celdas llevan `figure.image-figure` dentro, con su pie y su enlace Fuente.
Ya está publicado en estos 4 artículos (22 tablas en total, 10 de ellas con imágenes en las celdas):

- `seattle-antes-del-nombre-una-historia-cronolgica-del-grunge`
- `a-50-aos-del-golpe-el-arte-como-forma-de-resistencia-en-argentina`
- `voces-que-incomodan-mujeres-en-la-msica-alternativa-actual`
- `da-mundial-de-la-radio-del-experimento-elctrico-al-algoritmo`

Hay un borrador en camino, `cordoba-las-raices-de-una-ciudad-alternativa`, con 8 tablas así.

Esto es lo que trae el HTML:

```html
<table class="editor-table" style="min-width: 50px;">
  <colgroup><col style="min-width: 25px;"><col style="min-width: 25px;"></colgroup>
  <tbody><tr>
    <td colspan="1" rowspan="1"><p>Texto…</p><p>Más texto…</p></td>
    <td colspan="1" rowspan="1">
      <figure class="image-figure">
        <img src="https://listen.astra.fm/actualidad/images/content/<id>.jpg" data-media-id="<id>" data-caption="Pie" data-source="https://…">
        <figcaption><span class="image-caption-text">Pie</span><a class="image-source" href="https://…" target="_blank" rel="noopener noreferrer">Fuente</a></figcaption>
      </figure>
    </td>
  </tr></tbody>
</table>
```

Las columnas **no traen ancho** (el `colgroup` solo pone un mínimo de 25 px). Si las dejáis con el
reparto automático del navegador, salen de anchos desiguales y la imagen se encoge o se desborda.
Así lo enseña ahora el Studio en su vista previa:

- `table-layout: fixed; width: 100%`, para que las columnas salgan iguales;
- celdas con `vertical-align: top` y algo de separación entre ellas;
- `img` de las celdas a `width: 100%; height: auto`;
- pie con su enlace Fuente, como en las imágenes fuera de tablas.

Lo que hagáis en móvil (dejar las dos columnas o ponerlas una debajo de otra) lo decidís vosotros.
Lo único que os pedimos es que abráis esos 4 artículos y comprobéis que se leen bien.

## 3. Nada más cambia

El editor del Studio permite ahora insertar una imagen pegando su URL: el servidor la descarga y la
guarda en `https://listen.astra.fm/actualidad/images/content/`, igual que las subidas. El HTML que os
llega es exactamente el mismo.

Cuando lo hayáis mirado, decídselo a Pablo. Si algo de estas tablas os obliga a tocar el contrato,
escribidlo aquí.
