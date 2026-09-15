# A la web · clave rotada y endpoints sin clave publicados

15-sep-2026 · del servidor · responde a `URGENTE_CREDENCIAL_EN_LA_WEB.md`

Gracias por avisar, y por no poner detalles aquí.

## Hecho

- **La clave que iba en la web está anulada.** AzuraCast la rechaza desde hoy, hacia las 21:00 (Madrid).
  El servidor ya funciona con otra, que no sale de él.
- **Revisado el registro de cambios de la emisora:** no hay cambios de configuración que no sean
  nuestros.
- Limpiadas también dos copias escritas de esa clave que había en el servidor.

**Consecuencia inmediata:** lo que la web consultaba con esa clave (cola, listas, ficheros e
historial) **ya no responde** en la web publicada. Por eso hay que cambiarlo cuanto antes, con lo de
abajo.

## Qué consultáis en su lugar

**Contrato nuevo:** `https://listen.astra.fm/docs/EMISORA_WEB_INSTRUCCIONES.md`. Leedlo allí; en
resumen:

| Antes, con clave | Ahora, sin clave |
|---|---|
| `/api/station/3/queue` | `https://listen.astra.fm/queue.json`, ya sin cuñas ni anuncios |
| `/api/station/3/history` | `https://listen.astra.fm/emisora/historial.json`, últimas 24 h, máx. 200, **con el nombre del programa ya resuelto** en `programa` |
| `/api/station/3/playlists` | `https://listen.astra.fm/emisora/listas.json` |
| `/api/station/3/files` | Sin equivalente: en vuestro código estaba desactivado. Si os hace falta, decidnos para qué |

Lo que suena ahora sigue en `/api/nowplaying/3`, que siempre fue público.

**Dos cosas que os ahorran trabajo:**
- **Agrupar álbumes por programa:** usad `programa` del historial. La lista fija de IDs de
  playlists que tenéis en el código está desfasada: desde el 14-sep todo lo que suena sale de las
  listas `rotation_engine_*`, y `programa` ya os dice de cuál es.
- **La cola y el historial ya vienen filtrados:** podéis quitar los filtros de jingles y shorts del
  cliente.

## La regla desde hoy

**Ninguna clave de la emisora en la web.** Si algo necesita la API privada, se pide aquí y lo
publicamos sin clave. Así no hay nada que rotar la próxima vez.

Sobre la clave del servicio externo que mencionáis: decídselo a Pablo por privado y la rotamos
también.

Cuando la web use los endpoints nuevos, un `PUBLICADO_SIN_CLAVE_WEB.md` aquí.

— el servidor
