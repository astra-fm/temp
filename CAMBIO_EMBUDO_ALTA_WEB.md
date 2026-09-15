# A la web · dos eventos nuevos en el alta

15-sep-2026 · del servidor · amplía `ANALITICA_WEB_INSTRUCCIONES.md`

La radio ha rehecho el embudo del alta en su pestaña Stats. Para que empiece por el principio y
explique los abandonos, hacen falta **dos eventos más desde la web**. Nada de lo que ya enviáis
cambia.

**Contrato:** `https://listen.astra.fm/docs/ANALITICA_WEB_INSTRUCCIONES.md`, tabla de §5 y el
apartado nuevo «El alta, paso a paso». Leedlo allí antes de implementar; esto es solo el aviso.

## Qué hay que añadir

| Evento | Cuándo | Propiedades |
|---|---|---|
| `alta_abierta` | Se muestra el alta a alguien sin sesión. Una vez por apertura | `desde`: `enlace` si llega con `?alta=artista\|sello`; `web` en cualquier otro caso |
| `alta_error` | `POST /cuentas/register` responde con error o falla la red. Una vez por envío fallido | `tipo`: la opción elegida · `motivo`: `correo_registrado` (409), `limite` (429), `datos` (400), `otro` (resto) |

**Detalles que importan para que el embudo cuadre:**

- **Una apertura** es llegar a la pantalla del alta. Cambiar de opción, corregir un campo o
  reintentar tras un error no vuelve a enviar `alta_abierta`.
- **Con `?alta=`** salen los dos en la misma apertura y en este orden: `alta_abierta` con
  `desde: enlace` y después `alta_iniciada`, como hasta ahora.
- **`alta_error` no cuenta la validación del propio formulario** (campo vacío, contraseña corta),
  solo lo que responde el servidor.
- **`alta_error` nunca lleva el mensaje de error, el correo ni nada escrito por la persona.** Solo
  `tipo` y `motivo`, con esos valores exactos.
- Igual que el resto: `window.umami && umami.track(...)`, para que la web no falle si Umami no carga.

## Qué no hay que tocar

- `alta_iniciada`, `alta_completada` y `correo_verificado` se quedan como están. La cuenta creada y
  el correo verificado los cuenta ya **el servidor** en Stats, con las cuentas reales, pero seguid
  enviándolos para el panel de Umami.
- **Lo que marcan en el alta** (consentimientos y beta) lo cuenta el servidor. No lo mandéis como
  propiedad de ningún evento.

## Cuando esté

Un `PUBLICADO_EMBUDO_ALTA_WEB.md` aquí. Como en la analítica anterior, se agradece saber qué
comprobasteis: sobre todo que `?alta=artista` envía los dos eventos en orden y que un correo ya
registrado deja un `alta_error` con `motivo: correo_registrado` y sin datos personales.

— el servidor
