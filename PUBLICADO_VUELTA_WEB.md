# Al servidor · publicado en el alojamiento de siempre. Os toca

12-sep-2026 · del front de la web · cierra `ADELANTE_DESPLIEGUE_WEB.md`

**La web está subida a `185.42.105.72` y verificada.** Podéis empezar cuando queráis.

## Verificado sobre el alojamiento, no sobre el fuente

Pedido por su dirección, forzando el nombre del dominio anterior para que el servidor responda como
responderá el día que mováis el DNS:

| qué | cómo ha salido |
|---|---|
| Canónico del inicio | el dominio de siempre |
| `og:url` y las sociales | el de siempre, con `www` |
| Host del servicio dentro del paquete | **una sola aparición**, y es el de siempre |
| `robots.txt` y `sitemap.xml` | el de siempre, cero menciones al otro |
| 🔴 Bloque del service worker | lista de retirados = **el dominio nuevo**, invertida como quedamos |

**Y la comprobación que más nos importaba: el alojamiento nuevo no se ha tocado.** Su paquete sigue
siendo el de anoche, byte por byte. El despliegue no se ha ido donde no debía.

## Lo que pasó con el destino, ya resuelto

Era lo que os contábamos: nuestra configuración apuntaba a un **nombre bajo el dominio anterior**, y
ese nombre lleva hoy a vuestra máquina. Con la dirección que nos disteis, a la primera.

**Vuestro dato era el que faltaba**, y darlo por dirección y no por nombre es lo que lo resolvió.

## Vuestro turno, en vuestro orden corregido

1. Quitáis la redirección y Pablo devuelve el DNS al alojamiento de siempre.
2. **Después** sacáis el dominio anterior del certificado de la emisora.
3. Verificáis desde fuera y nos lo confirmáis.

**El certificado del alojamiento ya cubre los dos nombres** —el dominio a secas y con `www`, hasta
el 29 de octubre—, así que en cuanto el DNS apunte ahí el sitio sale con HTTPS sin hueco.

## Un aviso para el momento del cambio

Quien haya visitado el dominio nuevo tiene el service worker de ayer instalado. **Se limpia solo**:
en cuanto ese dominio deje de servir el sitio, el bloque invertido se encarga. Pero si durante unas
horas los dos dominios sirven a la vez, alguien puede ver la versión de ayer hasta que su pestaña se
recargue. No es un fallo y no hace falta que hagáis nada; lo decimos para que no os extrañe.

## Estado

| | |
|---|---|
| Publicado | **sí**, en el alojamiento de siempre |
| Verificado | sí, contra el alojamiento |
| Alojamiento nuevo | intacto |
| Esperando | vuestros tres pasos |

— el front de la web
