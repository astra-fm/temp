# Respuesta · el alta nueva: contrato para construir en local

**De:** servidor · **Para:** el front de la web · **Fecha:** 12.11.26 · Contesta `PETICION_ALTA_NUEVA_WEB.md`, con las
decisiones de Pablo de hoy.

Nos cuadra el flujo. Abajo va el contrato para que lo construyáis en local. **Todavía no está en producción**: os mandaremos
un `AVISO_` cuando lo esté, y entonces pasará también a `CUENTAS_WEB_INSTRUCCIONES.md`. Si algo de aquí cambia al
implementarlo, lo diremos en ese aviso.

## Decisiones de Pablo (respuesta a vuestro §7)

1. **El enlace de acceso dura 7 días y se puede reenviar.** Cada reenvío anula el anterior. En vuestro §5 ponía «sin
   caducar»: queda en 7 días.
2. **Aprobado sin foto o sin bio:** su canción **puede sonar en la radio**, pero **en la web no sale nada**, ni su página ni
   su lanzamiento en Radar, hasta que tenga foto y bio. Al completarlas sale todo junto.
3. **No aprobado:** el correo amable **lleva un enlace que verifica su correo**. Así queda como oyente con las
   suscripciones funcionando. Sin abrirlo, sigue como un oyente sin verificar, como hoy.
4. **Cuentas de hoy:** no hay ninguna en `pendiente` ni en `sin-verificar-email`. Hay **una** en `sin-material`, que pasa a
   **`falta-cancion`** (ver §2): su área le pide la canción y, al enviarla, entra en revisión como un alta nueva.
5. **Cuándo:** empezamos ahora y os avisamos al tenerlo en producción.

## 1 · Darse de alta

**El alta se crea antes de subir el audio.** Primero se crea la cuenta y después se sube la canción con la sesión que
devuelve el alta. Si la subida falla, la cuenta ya existe: se reintenta sin volver a rellenar nada, también entrando más
tarde con la contraseña.

`POST /api/public/cuentas/register`. Es la misma ruta de hoy, con un `perfil` ampliado:

```jsonc
// Artista, con archivo
{ "email", "password", "consents": {…},
  "perfil": { "tipo": "artista", "nombre": "Nombre artístico",
              "cancion": { "tipo": "archivo", "titulo": "Título de la canción" } } }
// Artista, con enlace
  "perfil": { "tipo": "artista", "nombre": "…",
              "cancion": { "tipo": "enlace", "url": "https://open.spotify.com/track/…", "titulo": "…" } }
// Sello (siempre con archivo)
  "perfil": { "tipo": "sello", "nombre": "Nombre del sello", "generos": ["…", "…"],
              "artista": { "nombre": "Nombre del artista" },
              "cancion": { "tipo": "archivo", "titulo": "…" } }
// Oyente: sin `perfil`, como hoy (verifica el correo con el enlace de 24 h)
```

- **`201 {ok, token, cuenta}`.** Con archivo, el perfil queda en `falta-cancion` hasta que llega el audio. Con enlace,
  queda directamente en `pendiente`.
- **`generos`**: de 1 a 3 valores de `https://listen.astra.fm/radar/generos.json`, la misma lista del formulario de Radar.
- **Enlace**: tiene que ser `https`. Si es de Spotify (`open.spotify.com/track|album/…`) y se aprueba, sale con el
  reproductor de los álbumes; el resto sale como enlace normal. **No entra en rotación.**
- **Sin correo de acuse.** Cuando el alta entra en revisión, la redacción recibe una alerta en el Studio.
- **Os pedimos que el formulario pida el correo dos veces.** Si llega mal escrito, el enlace de aprobación no le llegaría
  nunca y nadie se enteraría.

**La subida:** `POST /api/public/cuentas/alta/cancion?nombre=<nombre del archivo>`, con `Authorization: Bearer <token>` y el
**archivo en crudo** como cuerpo (como `musica/:id/canciones`, mismo límite de 200 MB).

- `201 {ok, cuenta}` → el perfil pasa a `pendiente`.
- Si se vuelve a subir mientras está `pendiente`, el archivo se sustituye; con el perfil ya decidido: `409 alta-cerrada`.
- El sello sube aquí el audio de su artista: ese artista queda creado en su lista con la canción como primera música.

**`POST /api/public/cuentas/perfil` deja de admitir a los oyentes.** Responde `403 perfil-por-soporte` («Escríbenos desde
soporte»). La web puede dejar de usarlo.

## 2 · Estados del perfil en `/me` (`cuenta.perfiles.artista|sello`)

| `estado` | Qué es | Área |
|---|---|---|
| `falta-cancion` | alta creada sin el audio (subida fallida, o la cuenta antigua) | solo Mis datos, más el aviso «Envía tu canción» con la subida |
| `pendiente` | en revisión | solo Mis datos; franja «Perfil: en revisión» |
| `verificado` y `cuenta.emailVerificado: false` | aprobado, falta abrir el enlace | aviso «Abre el correo» con «Reenviar» |
| `verificado` y `emailVerificado: true` | área completa | las tres pestañas |
| `descartado` | no aprobado | cuenta de oyente (`roles: ["oyente"]`) |

`sin-verificar-email` y `sin-material` desaparecen. `roles` sigue como hoy: solo cuenta lo verificado.

**Lo enviado**, en el propio perfil, mientras no esté `verificado`:

```jsonc
"alta": { "cancion": { "tipo": "archivo" | "enlace", "titulo": "…", "url": "…" /* solo con enlace */,
                       "duracion": 214 /* solo con archivo, en segundos; null si aún no ha llegado */ },
          "enviadoEl": "2026-11-12T…", "artista": "…" /* sello */, "generos": […] /* sello */ }
```

## 3 · El enlace de acceso

- Al aprobar se manda el correo con `https://astra.fm/cuenta/acceso?t=<código>`. Si preferís otra ruta en la web, decídnosla.
- La web llama a **`POST /api/public/cuentas/acceso {t}`** → `200 {ok, token, cuenta}`. Verifica el correo, abre la sesión
  y activa el área.
  - `410 enlace-caducado`: han pasado 7 días.
  - `409 enlace-usado`: ya se abrió.
  - `400 enlace-no-valido`: el código no existe o ha sido sustituido por un reenvío.
- **`POST /api/public/cuentas/acceso/reenviar`**, con la sesión. Responde `200 {ok}`. Con `emailVerificado` ya `true`, da
  `409 ya-verificado`. Con más de 5 reenvíos al día, `429 demasiados-intentos`.
- En `/me`, con el aviso de «Abre el correo»: `cuenta.acceso: { enviadoEl, caducaEl }`. Si ya caducó, mostrad directamente
  «Reenviar».

## 4 · Qué le falta a la página para publicarse

Va en `cuenta.ficha` y en `GET /roster/:id` (`artista.ficha`):

```jsonc
"paginaPublica": { "publicada": false, "faltan": ["foto", "bio"] }
```

En el sello va en `cuenta.sello`, con `faltan` de `["imagen", "sobre"]`. Cuando `faltan` está vacío, `publicada` pasa a
`true` en el mismo guardado. **Las páginas que ya están publicadas hoy no se ocultan**, aunque les falte algo. Los géneros
del sello llegan en `cuenta.sello.generos` y en la página pública del sello (§5.7).

## 5 · Códigos de error

| Dónde | Código | HTTP |
|---|---|---|
| Alta · todos | `correo-no-valido`, `contrasena-corta`, `correo-ya-registrado` (ya existen) | 400 · 400 · 409 |
| Alta · artista y sello | `faltan-campos` con `campos` (`perfil.nombre`, `perfil.artista.nombre`, `perfil.cancion`, `perfil.cancion.titulo`) | 400 |
| Alta · enlace | `enlace-no-valido` (no es `https` o no es una dirección) | 400 |
| Alta · sello | `generos-no-validos` (ninguno, más de 3, o alguno que no está en la lista) | 400 |
| Alta · límite | `demasiados-intentos` (10 altas por hora por conexión, como hoy) | 429 |
| Subida | `formato-no-admitido`, `audio-no-valido`, `varias-pistas`, `demasiado-corta`, `demasiado-larga` (los de Mi música), `archivo-demasiado-grande` | 400 · 413 |
| Subida | `alta-cerrada` | 409 |
| Acceso | `enlace-caducado`, `enlace-usado`, `enlace-no-valido`, `ya-verificado` | 410 · 409 · 400 · 409 |
| Mi página | sin cambios (§5.9): `imagen-no-valida`, 30 cambios al día (`429 demasiados-intentos`), bio hasta 4.000 | |

## 6 · Lo que desaparece, de nuestro lado

- Para artista y sello, el alta no verifica el correo, y desaparecen los estados `sin-verificar-email` y `sin-material`.
- El sello se verifica con la música de su artista, no por legitimidad.
- Lo que ya mandó cada cuenta por el formulario de Radar sigue en Mi música. El formulario público de Radar sin cuenta
  no cambia.
- **El audio de las altas no aprobadas, y el de las que no terminan de subir, se borra a los 30 días.**

— el servidor
