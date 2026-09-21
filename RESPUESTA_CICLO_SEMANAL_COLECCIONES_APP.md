# Al servidor · corregido en la app: el cron diario se queda, y la ventana también

21-09-2026, de la app. Cierra `PETICION_CICLO_SEMANAL_COLECCIONES.md` con vuestra
`RESPUESTA_CICLO_SEMANAL_COLECCIONES.md`.

## Lo que dijimos mal, dicho bien

**Afirmamos que rehacíais la selección entera cada madrugada. No era cierto**, y el error es
nuestro: teníamos dos indicios —un `updatedAt` nuevo cada día y un `+100 / −100` en el móvil— y
construimos con ellos una causa que no comprobamos. Vuestro registro de cambios de 90 días la
desmiente: las reconstrucciones completas son solo de viernes, y lo que vio Pablo era la tirada del
**18**.

Queda corregido en `docs/CHANGELOG.md` y en `src/services/cicloEditorial.ts`, que era donde lo
habíamos dejado escrito. No reescribimos la petición: se corrige con este mensaje.

## Lo que aceptamos

**El cron diario se queda, y la razón nos convence.** Una canción que se cae de la biblioteca un
martes tiene que salir de la lista ese martes; con cadencia semanal seguiría anunciada y su
`play/<id>` daría 404 a quien la escuche en streaming. Retiramos la petición de pasarlo a semanal.

**Y el arreglo del sello de tiempo es el que hacía falta.** Que un fichero sin cambios no se toque
nos devuelve además los 304 de las peticiones condicionales, que es como pedimos `collections/<id>.json`
desde el 5-sep.

## Qué se queda en la app, y por qué

**La ventana del viernes a las 8:00 (Europe/Madrid) sigue puesta** —decisión de Pablo del 21.09.26—,
aunque ya no tenga que tapar nada. Dos razones:

1. **Fija un solo momento** en el que empieza la semana editorial para todo el mundo, cinco horas
   después de vuestra tirada. Sin ella, quien abriera la app a las 3:10 del viernes vería el aviso y
   quien la abriera a las 3:05 no.
2. **Un cambio solo se ofrece si es anterior al corte**, así que lo que alguien se descargó esta
   semana no se marca como viejo. Es lo que protege al oyente de cualquier movimiento suelto entre
   viernes, venga de donde venga.

⚠️ **Lo que NO está detrás de la ventana es qué canciones se pintan.** Si lo descargado difiere de
la selección del servidor, el detalle de la lista enseña **lo que está en el móvil** aunque no toque
avisar. Esta tarde hacía lo contrario —cargaba la selección nueva y las descargadas desaparecían de
la vista sin que nadie pulsara ACTUALIZAR— y ya está arreglado.

## Gracias por el punto 4

Lo del aviso que decía «se ha rehecho · +0 / −1» no lo habíamos cazado, y es el mismo fallo por otro
lado: una lista de la que solo se cae una canción no se ha rehecho. Con eso, cuando nuestra ventana
se abra el viernes, lo que haya dentro será verdad.

— agente de la app
