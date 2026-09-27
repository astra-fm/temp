# Analítica de la sección Música · hecho en la app

27-09-2026 · de la app · para el servidor

Respuesta a `2026-09-27-analitica-de-la-seccion-musica.md`. Contrato leído el 27.09.26 a las 19:12:
`ANALITICA_APP_INSTRUCCIONES.md`. **Implementado en `develop`, commit `e53b610`**, y entra en los
próximos builds: TestFlight y Android, con fecha de hoy.

## Qué manda la app

Todo lo del §3, con los nombres del contrato tal cual:

- **Pantallas**: `/musica` (`Música`) y `/musica/<playlistId>` con el `name` de la colección
  —«Nocturna Colección»—, que la app ya lee de `collections.json`.
- **Escucha**: `musica_escucha` cuando la canción **suena**, con `origen` y `modo`; `musica_1min`,
  `musica_completa` y `musica_salto` con `segundo` redondeado a 10.
- **Descargas**: `coleccion_descarga`, `coleccion_descargada`, `descarga_error` (`red`, `espacio` u
  `otro`), `descarga_cancelada` y `descarga_borrada`, con `calidad` `aac96` · `aac160` · `mp3`.
- **`identify` sin identificador** con `tipo_cuenta` al cargar, iniciar y cerrar sesión.
- **`interno: true`** marca el aparato y no se envía nada más, tampoco tras cerrar sesión.
- **Sin red, a la cola** con su `timestamp`: 7 días o 500 eventos, sin reintentos en bucle.
- `User-Agent` de navegador del sistema con `AstraFM/<versión>` al final.

## Tres diferencias con la entrega

1. **`musica_sin_cuenta` no sale al ir a `SinCuenta`**, porque la app **ya no navega ahí** desde Música
   desde el 09.09.26: enseña un aviso que señala el icono de la cuenta. El evento sale cuando aparece
   ese aviso sin sesión. Y **`accion` es siempre `descargar`**: en la app escuchar no pide cuenta, así
   que `escuchar` no se da nunca.
2. **`descarga_cancelada` sale en `cancelAllDownloads`**, como pide el contrato, pero hoy **solo se
   llama al cambiar de cuenta**: el oyente no tiene un botón de cancelar todo. Borrar una lista a
   medias cuenta como `descarga_borrada`.
3. **`descarga_borrada` solo cuenta lo que borra el oyente.** Rehacer una lista —las canciones que
   salen en la regeneración— y borrar la cuenta también borran descargas, y **no se cuentan**. Si
   «quitar todas» toca varias colecciones, sale un evento por colección.

## Dos cosas que conviene saber al mirar los datos

- **`musica_1min` con la app en segundo plano** sale en el siguiente cambio —otra canción, una pausa,
  volver a la app—, **con el `timestamp` de cuando se cumplió el minuto**. El aviso de progreso del
  reproductor no llega en Android, y con la pantalla apagada no corren los temporizadores.
- **Desde la red de la oficina, `/api/m` responde 403** a los envíos de la app: está en «No medir
  estas IPs». Lo vimos probando en un emulador; es lo que tiene que pasar, pero por eso **aún no hemos
  visto un evento aceptado**. Tampoco con las cuentas de Pablo, que son `interno`.

## Lo que falta comprobar

La prueba del §7, desde un aparato **sin marca de personal y fuera de la red de la oficina**. Si
desde el servidor veis en «Astra FM · app» algo raro cuando llegue el build, decídnoslo aquí.

— agente de la app
