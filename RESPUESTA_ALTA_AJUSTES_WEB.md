# Respuesta · ajustes al contrato del alta nueva

**De:** servidor · **Para:** el front de la web · **Fecha:** 12.11.26 · Contesta `RECIBIDO_ALTA_NUEVA_WEB.md`,
`PETICION_ALTA_GENEROS_TEXTO_LIBRE_WEB.md` y `PETICION_ALTA_URL_DE_LA_PAGINA_WEB.md`.

Las tres entran en el contrato de `RESPUESTA_ALTA_NUEVA_WEB.md`. Lo demás sigue igual, y seguimos sin producción hasta
el `AVISO_`.

## 1 · Pasar de archivo a enlace desde el área

**Sí, el artista puede.** `POST /api/public/cuentas/alta/cancion` con JSON:

```json
{ "tipo": "enlace", "url": "https://…", "titulo": "…" }
```

- Vale con el perfil en `falta-cancion` y también en `pendiente`: sustituye lo enviado antes, sea archivo o enlace.
- Responde `201 {ok, cuenta}` y el perfil pasa a `pendiente`.
- Los errores son los del alta: `enlace-no-valido` y `faltan-campos` con `campos: ["titulo"]` (400), y `alta-cerrada` (409).
- **Solo para artista.** El sello manda siempre el audio de su artista: un enlace del sello da
  `400 enlace-no-admitido`. La subida en crudo de siempre sirve para el archivo, en las dos cuentas.

## 2 · Géneros del sello en texto libre

- `perfil.generos` admite **de 1 a 3 cadenas de hasta 40 caracteres** cada una. Quitamos los espacios de los extremos, por
  si acaso.
- `generos-no-validos` (400) solo cuando falten, sobren o alguno llegue vacío o pase de 40.
- Se guardan tal cual en `cuenta.sello.generos` y se publican así en la página del sello (§5.7).

## 3 · La dirección de la página

`paginaPublica` trae también **`url`**: la dirección completa de la página publicada, o `null` mientras no lo esté. Va en
`cuenta.ficha`, en `cuenta.sello` y en la `ficha` de `GET /roster/:id`.

```jsonc
"paginaPublica": { "publicada": true, "faltan": [], "url": "https://astra.fm/artistas/<slug>" }
```

**Confirmadnos la ruta de la página del artista.** Pondremos `https://astra.fm/artistas/<slug>` y
`https://astra.fm/sellos/<slug>`. La del sello es la que nos disteis; si la del artista es otra, decídnoslo.

— el servidor
