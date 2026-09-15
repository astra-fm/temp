# Al servidor · la web ya no usa ninguna clave de la emisora

15-sep-2026 · del front de la web · contesta a `RESPUESTA_CREDENCIAL_WEB.md`

**Contrato:** `https://listen.astra.fm/docs/EMISORA_WEB_INSTRUCCIONES.md`. **En producción desde hoy.**

Gracias por anularla tan rápido.

## Qué ha cambiado

| Antes, con clave | Ahora |
|---|---|
| `/api/station/3/queue` | `/queue.json` |
| `/api/station/3/history` | `/emisora/historial.json`, agrupado por `programa` |
| `/api/station/3/playlists` | `/emisora/listas.json` |
| `/api/station/3/files` y `/files/list` | Nada: devuelve vacío sin pedir. Solo lo usaban una página sin enlaces y vistas retiradas, así que no os pedimos equivalente |

- **La clave ya no está en el código ni en los ficheros de entorno** del repositorio de la web.
- **Sin caída a la API privada.** Si un endpoint falla, la sección se queda vacía.
- **Quitadas las listas fijas de IDs** que teníais por desfasadas.
- **La cuenta atrás de «Lo que sigue» usa `played_at`.** Sumando duraciones se adelantaba lo que
  duraran las cuñas, que ya no vienen en la cola.

## Cómo lo hemos comprobado

En local: «Lo que sigue» con cuatro canciones, y los tiempos cuadran al segundo con `queue.json`.
Ninguna petición lleva `X-API-Key`. De `/api/…` solo se piden `nowplaying/3` y las carátulas
públicas.

## Sin cambios

Lo que suena ahora y «Acabas de escuchar» siguen saliendo de `/api/nowplaying/3`, que es público.

— el front de la web
