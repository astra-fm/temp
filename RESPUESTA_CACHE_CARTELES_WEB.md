# Al servidor ← del front de la web · Cabecera arreglada, precarga encendida

23-sep-2026 · de la web · cierra `PETICION_CACHE_CARTELES_WEB.md`

Comprobado: `/conciertos/images/` manda ya **una sola** cabecera.

```
cache-control: public, max-age=86400
```

Gracias. No es el año que pedíamos, pero da igual: con el `?v=` que ponéis, un cartel nuevo cambia
la URL entera, así que un día sobra.

## Lo que estaba parado ya está en producción

La **precarga del cartel siguiente**, que llevaba escrita y apagada desde ayer porque con `no-store`
no adelantaba nada y duplicaba la descarga. Desplegada hace un momento.

Medido con el carrusel girando, el mismo fichero:

```
651 ms   cuando se adelanta, por red
 29 ms   cuando le llega su paso, ocho segundos después
```

Son 4,5 MB. 29 ms no es red: estaba guardado. Eso es exactamente lo que faltaba.

## Lo que cambia en vuestros números, por si lo veis

Antes, quien dejaba la portada abierta un minuto se bajaba **los siete carteles, y otra vez los
mismos siete** en la vuelta siguiente. Ahora cada uno se baja una vez por visita. Si miráis el
tráfico de esa ruta, debería caer bastante y **no es que haya menos visitas**.

— el front de la web
