# A la app · los 502 eran una caída nuestra, no la carga: arreglado, sin HTTP/1.1

26-09-2026 · del servidor · contesta a `PETICION_DESCARGAS_HTTP1.md`

Gracias por el registro con horas exactas: ha sido lo que ha permitido encontrarlo.

## Qué pasaba

**No era un límite de carga: se nos caía el servicio entero.** Cuando AzuraCast cortaba una canción a
mitad de envío, `/play` no recogía ese error y **el proceso que sirve `/play` se reiniciaba**. Durante
esos 10 segundos todo lo que pasa por él daba 502: `/play` y también `onair.json`, `queue.json` y
las cuentas.

Hoy ha pasado cinco veces mientras Pablo probaba: 05:19, 05:37, 05:59, 07:56 y **08:16:12 UTC**. Esta
última coincide con vuestra ventana de 08:16:15 a 08:16:22: el servicio volvió a las 08:16:22. Por eso
las mismas URLs daban 200 minutos después.

## Qué hemos cambiado

- Un corte a mitad de canción **solo afecta a esa descarga**. El servicio sigue en pie y el móvil la
  reanuda con `Range` + `If-Range`, que ya teníais.
- Si el móvil cancela una descarga, cortamos también la petición a AzuraCast.

## Comprobado después del arreglo

| Prueba | Resultado |
|---|---|
| 90 descargas de `/play` a la vez **por una sola conexión HTTP/2**, como vuestro iPhone | 90 × 200 en 18 s, sin cortes y sin reinicios |
| 90 descargas a la vez por 90 conexiones | 90 × 200 en 16 s |

## Vuestras preguntas

1. **¿HTTP/1.1 en un nombre aparte?** **No hace falta**, y preferimos no hacerlo: la causa no era
   HTTP/2. Un nombre nuevo supone DNS, certificado y otra configuración más que mantener, para limitar
   algo que ya aguanta. **No cambia ninguna URL ni el contrato.**
2. **Plazo:** está en producción desde las 09:08 UTC de hoy (11:08 en Barcelona).

## Lo que os toca decidir

- **El límite de seis descargas a la vez en iOS podéis quitarlo** si os estorba con el móvil
  bloqueado. Por nuestra parte no hace falta. Los reintentos escalonados sí os los recomendamos:
  un corte puntual sigue pudiendo pasar, y sin coste.
- **Descargas en AAC:** van por ficheros estáticos (`/descargas/*.m4a`), que no pasan por el proceso
  que se caía. La conversión de las 896 canciones termina hoy hacia las 11:35 (hora de Barcelona);
  desde entonces las listas en AAC no tocan `/play`.

Si en la próxima prueba vuelve a salir un 502, mandadnos la hora exacta como hoy.

— servidor
