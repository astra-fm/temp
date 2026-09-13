# A la web y a la app · el tipo de cuenta lo dice el servidor: `cuenta.roles`

13-sep-2026 · del servidor · **cambio de contrato, aditivo**

## Qué hay de nuevo

Decisión de producto: **las cuentas se discriminan entre oyente, artista y sello**, porque a nivel de
negocio esa clasificación puede importar. Y como es el servidor el que decide, **ahora lo dice él**.
Todas las respuestas que traen `cuenta` incluyen:

```json
"roles": ["oyente"] | ["artista"] | ["sello"] | ["artista", "sello"]
```

Es un campo nuevo: no quita ni cambia nada de lo que ya usáis.

## Por qué

Hasta hoy el tipo había que deducirlo de `perfiles`, y esa deducción tenía tres trampas:

- **Un perfil descartado sigue llegando en `perfiles`**, con estado `descartado`. Mirar solo si existe
  la clave enseñaba como artista a alguien a quien la redacción rechazó.
- **Las solicitudes en curso todavía no son un artista.** `sin-verificar-email`, `sin-material` y
  `pendiente` son peticiones, no un tipo.
- **Cada front lo deducía por su lado**, así que web y app podían acabar contando lo mismo de forma
  distinta.

## Cómo se calcula

- **Son exclusivos.** `["oyente"]` es la cuenta sin ningún perfil verificado. Un artista verificado es
  `["artista"]`, no además oyente. Así, contar oyentes cuenta solo oyentes.
- **Solo cuenta lo verificado.** Mientras una solicitud esté en curso o se descarte, la cuenta sigue
  siendo `["oyente"]`. El estado de esa solicitud sigue llegando en `perfiles`, como hasta ahora.
- **El único caso con dos valores** es la cuenta que es artista y sello a la vez. Por eso es una lista.

| La cuenta tiene… | `roles` |
|---|---|
| ningún perfil | `["oyente"]` |
| artista `verificado` | `["artista"]` |
| sello `verificado` | `["sello"]` |
| artista y sello `verificado` | `["artista", "sello"]` |
| artista `pendiente`, `sin-material` o `descartado` | `["oyente"]` |
| sello `pendiente` | `["oyente"]` |
| artista `verificado` y sello `descartado` | `["artista"]` |

## Lo que `roles` no es

**Es clasificación, no permiso.** Qué área se abre lo siguen marcando los bloques de siempre: `ficha`
para el artista verificado, y `sello` y `roster` para el sello en `pendiente` o `verificado`.

Por eso **un sello en revisión es `["oyente"]` y a la vez trae `sello` y `roster`**: todavía no es un
sello verificado, pero ya puede usar su área mientras la redacción lo revisa. Las dos cosas son
verdad a la vez y no se contradicen.

## Dónde está

Contratos actualizados en el portal: `CUENTAS_WEB_INSTRUCCIONES.md` §2, `CUENTAS_APP_INSTRUCCIONES.md`
§4 y `CUENTAS_DISENO_FUNCIONAL.md`. En producción desde hoy. Comprobado contra la función real con las
siete combinaciones de la tabla y dos más.

Cómo y dónde se muestra el tipo en pantalla es cosa de diseño.

— el servidor
