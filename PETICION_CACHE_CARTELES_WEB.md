# Al servidor ← del front de la web · `/conciertos/images/` manda dos `Cache-Control` que se contradicen

22-sep-2026 · de la web · relacionado con `AVISO_CARTEL_TAL_CUAL_WEB.md`

Pequeño y con arreglo de una línea, pero hoy cuesta megas de verdad.

## Lo que responde esa ruta

```
$ curl -sI https://listen.astra.fm/conciertos/images/cartel-5051071b14b0.png?v=1790018035195

content-length: 3150249
etag: "6ab1879d-3011a9"
cache-control: no-store, no-cache, must-revalidate, proxy-revalidate, max-age=0
cache-control: public, max-age=86400
```

**Dos cabeceras `Cache-Control`, y se contradicen.** El navegador las junta y manda `no-store`, así
que **no guarda el fichero**. Comprobado en el navegador, no deducido: el mismo cartel se descarga
entero cada vez que el carrusel pasa por él.

## Lo que cuesta

Los carteles pesan ahora entre **2,9 y 4,5 MB** —son los PNG de 2508 × 1200 de los que nos
avisasteis—. La agenda tiene **siete pasos** y gira sola cada ocho segundos. Con `no-store`, alguien
que deje la portada abierta un minuto se baja **siete carteles, y otra vez los mismos siete** a la
vuelta siguiente. En una portada, eso es mucho.

Y son ficheros **inmutables**: el nombre es un hash y la URL trae `?v=`. Son el caso de libro de lo
que sí se cachea.

## Lo que pedimos

Una sola cabecera en `/conciertos/images/`:

```
Cache-Control: public, max-age=31536000, immutable
```

Con el `?v=` que ya ponéis, subir un cartel nuevo sigue invalidando el anterior al instante: cambia
la URL.

⚠️ Sospechamos que el `no-store` viene de una regla general de la API aplicada de más —tiene pinta de
ser la de los JSON de cuentas, donde está muy bien puesta— y que se está sumando a la de estáticos.
Si es así, basta con que la de imágenes no herede la otra.

## Lo que tenemos parado esperando esto

Pablo nos pidió **precargar el cartel siguiente**, para que al saltar de paso no haya espera. Está
escrito y **apagado**, porque con `no-store` no adelanta nada y **duplica la descarga**: se baja el
cartel para nada y se vuelve a bajar al llegar su paso. En cuanto la cabecera esté, lo encendemos.

Mientras tanto, de nuestro lado ya va un indicador de carga en el hueco del cartel, para que esos
segundos no parezcan un módulo roto.

— el front de la web
