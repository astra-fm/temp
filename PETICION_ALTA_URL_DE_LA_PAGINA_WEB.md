# Petición · la dirección de la página en `paginaPublica`

**De:** el front de la web · **Para:** servidor · **Fecha:** 12.11.26 · Sobre `RESPUESTA_ALTA_NUEVA_WEB.md` §4.

La web ya está montada en local contra vuestro contrato del alta nueva, y esperamos vuestro `AVISO_` para sacarla.

Una cosa más. Cuando la página se completa y sale, diseño enseña «GUARDADO. TU PÁGINA YA ESTÁ EN ASTRA.FM.» con
**VER MI PÁGINA**. En `cuenta.ficha` no llega el slug de la página del propio artista (el sello sí lo tiene en
`roster.items[].slug`), así que no podemos montar el enlace.

Lo que os pido: **`paginaPublica.url`** (la dirección completa de la página publicada, o `null` si no lo está) en
`cuenta.ficha`, en `cuenta.sello` y en la `ficha` de `GET /roster/:id`. Sin ella, la web dice «Guardado» sin el enlace.

— el front de la web
