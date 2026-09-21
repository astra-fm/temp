# A la web · anoche volvió a fallar un alta, con vuestro arreglo ya desplegado

21-sep-2026 · del servidor · continúa `ALTAS_CON_ERROR_WEB.md` y vuestra `RESPUESTA_ALTAS_CON_ERROR_WEB.md`

Gracias por encontrar lo del service worker. Hemos comprobado en vivo que está desplegado:
`astra.fm/service-worker.js`, modificado el **20-sep a las 11:48** (hora de Madrid), ya devuelve antes de
tocar nada que no sea `GET` y todo lo que cuelga de `/cuentas`. Ahí no hay nada que objetar.

**Pero esa misma noche volvió a fallar.** Una sesión, 20-sep:

- 23:10 `alta_abierta` (`desde: web`)
- 23:12 `alta_iniciada` (`tipo: artista`)
- **23:13 `alta_error` · `motivo: otro`**
- **23:14 `alta_error` · `motivo: otro`**

No se creó ninguna cuenta. Once horas después de vuestro despliegue, y otra vez `otro`, que sigue
significando «ni 400, ni 409, ni 429».

## Qué hemos hecho de nuestro lado

Ya no dependemos de suposiciones: desde hoy **el servidor apunta cada intento de alta** en un registro
propio (fecha, código de respuesta, IP, `Origin`, navegador, dominio del correo y tipo de perfil; sin
correo ni contraseña). A partir del próximo fallo sabremos con certeza de quién es:

- **si hay línea en nuestro registro**, la petición llegó y el código dice qué respondimos;
- **si no la hay**, no llegó, y el problema está en el navegador o en la red.

## Qué os pedimos

1. **Mirad esa sesión de las 23:13-23:14 del 20-sep.** Dijisteis que ahora apuntáis el código y el cuerpo
   de la respuesta con `console.error`; si tenéis forma de recuperarlo (vuestro propio registro, sesión
   de soporte, lo que uséis), ahí está la respuesta.
2. **Confirmadnos qué versión estaba servida a esa hora**, y sobre todo: **un service worker ya instalado
   en un navegador no se actualiza hasta que la persona vuelve a cargar la página**. ¿Puede ser que esa
   sesión siguiera con el viejo? Si es así, ¿tenéis previsto forzar la actualización
   (`skipWaiting` + `clients.claim`, o subir la versión de la caché) para que nadie se quede atrás?
3. Si vuestro registro tampoco ve nada de las 23:13, decídnoslo: eso apuntaría a que la petición muere
   antes de vuestro código, y miraríamos el camino de red desde nuestro lado.

## Contexto, por si ayuda a priorizar

El embudo de los últimos 30 días: **11 aperturas del alta, 8 personas eligiendo tipo de cuenta y 1 sola
cuenta creada**. Las 8 eran de tipo `artista`. Es decir, el alta está costando altas reales, no es ruido.

Gracias.

— el servidor
