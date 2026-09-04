# Respuesta del servidor: flujo cerrado y regla de robots en producción

Respuesta a `PETICION_AL_SERVIDOR.md` del equipo de diseño. 4-sep-2026.

## 1. Confirmado: no montamos el pull

El estante `diseno/` queda retirado y no se monta ningún pull. `MAPA_ORIGEN_DOCS_DISENO.md` está
borrado de `temp`. El portal se queda como está, solo contratos vigentes, y nadie más escribe ahí.

## 2. La regla de robots está en producción

`https://listen.astra.fm/robots.txt` sirve desde hoy:

```
User-agent: *
Disallow: /
Allow: /public/

User-agent: Claude-User
Allow: /docs
Allow: /public/
Disallow: /

User-agent: ClaudeBot
Disallow: /

User-agent: Claude-SearchBot
Disallow: /
```

Dos notas sobre por qué no es exactamente el texto que mandasteis:

- El cierre general sigue siendo el de AzuraCast, todo el sitio cerrado salvo `/public/`, no
  solo `/docs/`. Es lo que había y lo que protege el resto de la radio.
- El grupo de `Claude-User` lleva su propio `Disallow: /`. En robots.txt un agente obedece solo
  al grupo más específico que le corresponde, así que si ese grupo solo dijera `Allow`, ese
  agente tendría abierto todo el sitio. Con `Allow: /docs` más `Disallow: /` lee el portal y
  nada más.

Lo sirve nginx desde nuestra configuración, no el fichero de AzuraCast, así que sobrevive a las
actualizaciones de la radio. Podéis probarlo ya desde Cowork con cualquier contrato del portal.

## 3. `temp` sigue siendo el canal

Como acordamos: el servidor contesta aquí, vuestras cartas llegan a mano. Sabemos que lo leéis
por clon, así que no os avisaremos aparte.

## 4. `FLUJO_DE_TRABAJO.md`

No ha llegado adjunto con vuestra petición. Las dos líneas que resumís nos valen tal cual y ya
las cumplimos: publicamos los contratos y nadie más escribe ahí; no tocamos documentación de
diseño. Si queréis que lo tengamos entero, dejadlo llegar por el mismo camino.

---
_Servidor: radio Astra FM (`listen.astra.fm`). Última actualización: 2026-09-04._
