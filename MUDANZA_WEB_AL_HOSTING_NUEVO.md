# Mudanza de la web al alojamiento del dominio nuevo · lo que hemos medido desde fuera

11-sep-2026 · del servidor · sabemos que ya estáis con ello por FTP

Esto no es una petición, es lo que se ve desde fuera y desde vuestro repositorio no se ve.
Tres cosas que rompen de formas que no saltan a la vista, y dos que os ahorran trabajo.

## 1 · El `.htaccess` tiene que viajar

Medido en producción: cualquier ruta devuelve la aplicación, incluso una inventada
(`/ruta-que-no-existe-xyz` responde 200). Eso es una regla de reescritura de Apache mandando
todo a `index.html`.

**Si ese fichero no sube, en el alojamiento nuevo funcionará la portada y cualquier enlace
directo dará 404.** Es el fallo clásico de mudar una aplicación de una sola página, y no se
nota navegando desde la portada: solo aparece al entrar directo a una URL, que es justo lo que
hace quien llega de un buscador o de un correo.

Comprobación después de subir: pedir `/tv`, `/actualidad` y `/programas` **directamente** en el
dominio nuevo, no navegando.

## 2 · `tv-og.php` necesita PHP con curl y un sitio donde escribir

Su propio diagnóstico dice, en el servidor actual:

```
"servidor": {"curl": true, "allow_url_fopen": false, "json_descargado": true, ...}
```

O sea: la lectura remota por la vía alternativa está **desactivada**, así que depende de curl.
Y mantiene una caché propia, que necesita un directorio escribible.

Si en el alojamiento nuevo curl no estuviera disponible, ese fichero deja de funcionar y con él
la tarjeta de TV en Facebook. Vale la pena pedirle el diagnóstico nada más subirlo:
`https://<dominio nuevo>/tv-og.php?video=<id>&og-check=1`.

Por nuestra parte ya está hecho lo que tocaba: la llamada del servidor a ese fichero **ya no
tiene el dominio escrito a mano**, lo lee de su configuración. Cuando nos digáis que el sitio
responde en el dominio nuevo, cambiamos el valor y la llamada os sigue sola.

## 3 · El service worker se queda pegado en el dominio viejo

`/service-worker.js` está activo en producción. Quien ya haya visitado el sitio tiene la
aplicación cacheada en su navegador, y va a seguir viéndola un rato **después** de que cambiéis
nada.

En el dominio nuevo empieza de cero, así que ahí no hay problema. El problema es el viejo:
conviene publicar en él una versión que lo limpie **antes** de mover a la gente, o habrá quien
siga sirviéndose de la caché de una web que ya no es la buena.

## 4 · Lo que os ahorra un viaje

- **La constante del host**, al dominio nuevo, en este mismo despliegue. Ya os lo pedimos y lo
  repetimos aquí para que no haga falta subir dos veces.
- **Canónico, sitemap, robots y metadatos sociales**, que vosotros mismos medisteis: seis URL
  en el sitemap, la línea del sitemap en robots, el canónico y cuatro metadatos en el index, y
  el canónico del router.
- **El canónico apunta al dominio nuevo**, no al viejo. El viejo se va a retirar.

## 5 · Lo que NO hay que hacer todavía

**No mováis el dominio viejo al alojamiento nuevo.** Primero que el nuevo esté verificado. Luego
se lleva el viejo al mismo sitio y los dos dominios sirven el mismo despliegue, sin dos copias
que mantener. Ese paso lo coordina Pablo en el panel.

## 6 · Avisadnos cuando esté

Con que digáis «ya responde» nos basta: lo verificamos desde fuera (rutas directas, el
diagnóstico de TV, el reproductor y el certificado) y os devolvemos el resultado por aquí. Y si
necesitáis algo del servidor para terminar, pedidlo en este mismo repositorio.

— el servidor
