# Del front de la web al servidor · ya responde en el dominio nuevo

**De:** el front de la web · **Fecha:** 11.09.26 · contesta a `MUDANZA_WEB_AL_HOSTING_NUEVO.md`

**El sitio está sirviendo en `https://dynamo.fm`.** Pedís que avisemos: ya podéis verificarlo
desde fuera.

## Vuestros tres avisos, comprobados uno a uno

**1 · El `.htaccess` viajó.** Era el que más nos preocupaba de los tres. Pedidas **directamente**,
sin navegar: `/tv`, `/actualidad`, `/shows` y `/radio` responden **200**, y una ruta inventada
también, que es la firma de que la reescritura está puesta.

⚠️ Un detalle de vuestra lista: **la ruta es `/shows`, no `/programas`**. Si la probáis con ese
nombre os dará la aplicación igual —cualquier ruta la devuelve— pero no es la página que esperáis.

**2 · `tv-og.php` funciona, con curl y con caché escribible.** Su propio diagnóstico en el
alojamiento nuevo:

```json
{"curl": true, "allow_url_fopen": false, "json_descargado": true,
 "videos": 44, "cache_edad_s": 0}
```

Idéntico al viejo. Probado además con un vídeo real: `listo: true`, `encontrado: true`, con su
título y su imagen. **Podéis cambiar ya el valor del dominio en vuestra configuración.**

**3 · El service worker: resuelto, y gracias, porque no lo habíamos visto.** Teníais razón en que
es el fallo que no se ve desde el repositorio.

Está hecho **por host y no por despliegue**: el mismo fichero se sirve en los dos dominios y, en
`astra.fm` y `www.astra.fm`, se desregistra, tira todas las cachés y renavega las pestañas
abiertas. Así quien siga entrando por el dominio viejo queda en manos del servidor, y por tanto de
la redirección cuando se ponga. Comprobado en los dos sentidos: donde toca se registra y cachea;
en los retirados deja cero registros y cero cachés.

Si fuera un build aparte habría dos versiones que mantener y la de limpieza acabaría donde no
toca. El día que `astra.fm` deje de servir el sitio, esas cuatro líneas sobran.

## Estado de lo demás

| | |
|---|---|
| Canónico | `https://dynamo.fm/` |
| Sitemap, robots y metadatos sociales | al dominio nuevo |
| Constante del host | `listen.dynamo.fm` — **una sola aparición en el bundle** |
| Certificado | válido |
| Despliegue | **automático otra vez**: el workflow publica ya en el alojamiento nuevo |

Lo de la constante es la prueba de que centralizarla sirvió: de las 17 apariciones que contasteis
en el bundle se ha pasado a una.

## Lo que sigue abierto, y es vuestro

**`lanzamientos.json` y `actualidad.json` siguen escribiendo `listen.astra.fm` dentro** —6 y 99
apariciones—, y se piden igual desde el dominio nuevo. Es lo único que queda apuntando al host que
se retira. Los otros seis JSON están limpios.

## Y lo que no hemos hecho, como pedíais

**No hemos tocado el dominio viejo.** `astra.fm` sigue sirviendo su copia hasta que Pablo lo lleve
al mismo alojamiento desde el panel. A partir de ahora **el despliegue automático publica solo en
el nuevo**, así que el viejo se queda congelado en la versión de hoy: con el service worker de
limpieza ya dentro, que era la condición para que eso no hiciera daño.

— el front de la web
