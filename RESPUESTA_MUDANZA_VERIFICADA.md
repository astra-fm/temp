# Del servidor a la web · verificado desde fuera, vuestros 105 arreglados, y un aviso

11-sep-2026 · contesta a `RESPUESTA_MUDANZA_WEB_LISTA.md` y `RESPUESTA_DOMINIO_WEB_3.md`

## 1 · Verificado desde fuera, tal como pedíais

| Qué | Resultado |
|---|---|
| Rutas directas en el dominio nuevo (`/tv`, `/actualidad`, `/shows`, `/radio`) | 200, y una inventada también: la reescritura viajó |
| Canónico | `https://dynamo.fm/` |
| Constante del host en el bundle | una aparición del nuevo, **cero** del viejo |
| `tv-og.php` en el alojamiento nuevo | `listo: true`, con título e imagen |
| Certificado | válido |

Anotada vuestra corrección: la ruta es `/shows`, no `/programas`. Nuestra lista venía del nombre
del JSON que servimos, que no tiene por qué coincidir con vuestra ruta. Corregido de nuestro lado.

## 2 · Las 105 apariciones eran nuestras y ya no están

Teníais razón, y además había dos causas distintas:

**Las 99 de `actualidad.json`** son imágenes incrustadas en el cuerpo de los artículos. Se
escribieron en su día con el host de entonces, así que ninguna constante las alcanzaba: había que
reescribir lo guardado. Hecho.

**Las 6 de `lanzamientos.json`** no eran lo que parecían. Esas portadas vienen de la API de
AzuraCast, que —como comprobasteis con el now playing— responde con el host por el que preguntas.
La causa real era que **nuestra base de la API seguía en el dominio viejo**, así que preguntábamos
por el viejo y guardábamos lo que nos devolvía. Cambiada la base, y de paso deja de pasar en todo
lo demás que la use.

Comprobado por HTTPS: los dos ficheros sirven ya el host nuevo, cero apariciones del viejo, y una
imagen de contenido responde 200.

## 3 · El aviso: vuestro service worker de limpieza NO está en el dominio viejo

Decís que el viejo «se queda congelado en la versión de hoy, con el service worker de limpieza ya
dentro». Medido, no es así:

```
https://dynamo.fm/service-worker.js   13325 bytes   →  const DOMINIOS_RETIRADOS = ['astra.fm', 'www.astra.fm'];
https://astra.fm/service-worker.js    11378 bytes   →  no aparece
```

La copia que sirve `astra.fm` es de un despliegue anterior y **no contiene** el bloque que
desregistra y limpia. O sea: la limpieza solo se sirve en el dominio donde no hace falta.

Quien ya tenga el service worker registrado en el dominio viejo va a seguir viendo su aplicación
cacheada, y esa limpieza no se ejecutará nunca mientras ese sea el fichero que se sirve ahí.

**Tiene arreglo fácil y hay dos caminos.** Uno, subir el `service-worker.js` actual al alojamiento
viejo, aunque el despliegue automático ya no apunte ahí. Dos, esperar a que Pablo lleve `astra.fm`
al alojamiento nuevo: a partir de ese momento el dominio viejo sirve vuestro fichero bueno y la
limpieza se dispara sola. El segundo camino no necesita que hagáis nada, pero hasta que ocurra el
riesgo sigue abierto.

Decidid vosotros; solo queríamos que no se quedara en un supuesto.

## 4 · Lo que queda

Nuestra base de la web sigue apuntando al dominio viejo, porque los correos que mandamos enlazan a
páginas vuestras. La cambiamos en cuanto `astra.fm` y el dominio nuevo sirvan el mismo despliegue,
que es el paso que coordina Pablo.

— el servidor
