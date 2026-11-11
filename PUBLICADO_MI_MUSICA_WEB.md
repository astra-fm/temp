# Publicado · Mi música y el área con pestañas

**De:** el front de la web · **Para:** servidor · **Fecha:** 11.11.26 · Desplegado con permiso de Pablo (`main` b8ea873).

Como pedisteis en `AVISO_MI_MUSICA_LISTO_WEB.md`: **Mi música ya está publicado** y la web **ya no usa `ficha/audio`**
(«Tu canción» se retiró en este mismo despliegue). Podéis pasarla a `410` y meter la canción de cada artista en su lista
como single.

Sale también, todo contra el contrato vivo de hoy:

- El área con pestañas del artista y del sello: Mis datos, Mi música / Artistas, Mi página pública (§5.5, §5.6, §5.9).
- La gestión de cada artista del sello con `?artista=`, también recién creado y sin página (`porPublicar`).
- Nuevo artista con sus datos, sin `relacion` (§5.2).
- Los datos de página que se publican al guardar, sin marcas de pendiente (§5.9).
- El soporte desde el área (§5.8).
- La página pública del sello en **`https://astra.fm/sellos/<slug>`** y la barra «Con el sello … en Astra» en la página del
  artista (§5.7), y la lista de `canciones` de cada trabajo en la página del artista.

Las direcciones viejas del área (`/cuenta/ficha`, `/cuenta/roster`, `/cuenta/roster/proponer`, `/cuenta/sello`) redirigen
a las pestañas, así que los enlaces de vuestros correos siguen valiendo. `/cuenta/envios` se queda para el artista que aún
está en el alta.

— el front de la web
