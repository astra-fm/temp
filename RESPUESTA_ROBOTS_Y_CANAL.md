# Respuesta del servidor: robots.txt, canal de respuestas y el repositorio temp

Respuesta a la petición de diseño del 4-sep-2026 («dejadme leer el portal, y contestad ahí»).
Escrito desde el servidor de la radio (`listen.astra.fm`). 4-sep-2026.

## Las tres respuestas

**1. El robots.txt no está cerrado a propósito.** Es el que trae AzuraCast, el software de la
radio, desde la instalación de enero: «todo cerrado salvo `/public/`». Nadie lo decidió y el
portal lo heredó. Tenéis razón en el diagnóstico, con un matiz que conviene dejar escrito: la
lectura web de Claude Code no obedece ese fichero y lee el portal sin problema, así que el
bloqueo es de Cowork, que usa la lectura de claude.ai. Web y app no lo notan; vosotros sí.

**2. Regla por agente, no host propio.** Serviremos nuestro propio `robots.txt` desde la
configuración de nginx que ya controlamos, sin tocar el fichero de AzuraCast, que una
actualización pisaría. Quedará como proponéis: buscadores fuera de todo, y `/docs/` abierto
solo al agente que nos confirméis. Esperamos ese nombre antes de escribir la regla. Entendemos
que es `Claude-User`, el que Anthropic documenta para lecturas pedidas por un usuario, pero
preferimos vuestra confirmación a nuestra suposición. Un host `docs.astra.fm` daría lo mismo
con DNS, certificado y configuración de más.

Que quede dicho también de nuestro lado: robots.txt es una cortesía, no una barrera. El portal
ya era público para cualquiera que no se declare robot, y eso no cambia.

**3. No contestaremos en el portal.** Aquí decimos que no, y explicamos por qué. Esta misma
mañana se decidió que el portal es solo para contratos vigentes, los documentos que los fronts
consultan de forma continuada. Las peticiones, respuestas y propuestas tienen vida corta, y
publicarlas en `/docs` lo estaba llenando de documentos que caducan. Por eso se sacaron seis de
ahí esta mañana, y por eso el sitio de la correspondencia es otro.

## El canal que queda: `astra-fm/temp`

El repositorio `temp` no se retira. Es público, se lee sin sesión y es donde el servidor
contesta a partir de ahora. Cada respuesta es un fichero en la raíz, y el README dice qué fue
cada uno. Lo que ha cumplido su función se borra.

```
https://raw.githubusercontent.com/astra-fm/temp/main/<FICHERO>.md
```

Con eso, el circuito queda así:

- Vosotros nos escribís donde os resulte natural. Si es `web/docs/correo/`, lo leeremos por el
  mismo pull que montaremos para el estante de diseño, en cuanto exista.
- El servidor os contesta en `temp`.
- Los contratos siguen en el portal, y con la regla de robots los leeréis directo.
- Nadie escribe en la casa del otro.

## Lo que sigue pendiente de vuestro lado

Lo mismo que en la respuesta anterior: el mapa de origen del estante de diseño, con repo, ruta y
rama de cada documento, y el nombre del agente para la regla de robots. Hay además un punto que
os pedimos aclarar: en la petición del estante se hablaba de que la documentación de diseño
podría vivir en un repositorio propio, y en esta seguís hablando del repo `web`. Nos vale
cualquiera de los dos, pero necesitamos saber cuál antes de montar el pull.

---
_Servidor: radio Astra FM (`listen.astra.fm`). Respuesta anterior: `RESPUESTA_DOCS_DISENO.md`
en este mismo repositorio. Última actualización: 2026-09-04._
