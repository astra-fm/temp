# Al servidor · qué avisos lleva la app de iOS, build 40

13-sep-2026 · de la app · contesta a `CAMBIO_TIPO_DE_CUENTA.md`, `CAMBIO_VERIFICACION_OBLIGATORIA.md`
y `CAMBIO_SELECTOR_SIN_OPCION_APP.md`

**Subida a TestFlight el 13.09.26, build 40 (versión 1.0.0).** Montada contra
`CUENTAS_APP_INSTRUCCIONES.md` en su versión de hoy, bajada a las 09:28 y releída por la tarde para
el selector.

## 1 · `cuenta.roles`

| Punto | En la app |
|---|---|
| El tipo se lee en `roles`, no en `perfiles` | ✅ «Tus envíos al buzón» sale solo con `roles` artista o sello, o si la cuenta ya trae envíos. Un oyente no la ve |
| Cuenta guardada sin `roles` | ✅ Se trata como `["oyente"]` hasta el siguiente `GET /me` |
| El área la marcan `ficha`, `sello` y `roster` | ✅ «Tu área de artista / sello» aparece con cualquiera de esos bloques. **Un sello en `pendiente` ya ve su área** |
| El estado de cada solicitud sigue en `perfiles` | ✅ La fila de perfil enseña su estado, también `sin-material` y `descartado` |

## 2 · Verificación de correo obligatoria

| Paso | En la app |
|---|---|
| 1 · El alta envía el correo sola | ✅ La app ya no dice que no haga falta verificar. «Alta · dentro» cuenta que el enlace está enviado |
| 2 · Sin verificar, la cuenta no está activa | ✅ El área solo enseña «Te hemos enviado un enlace a <correo>», reenviar (`POST /request-verify`, su `error` tal cual), borrar la cuenta y cerrar sesión. Se decide por `emailVerificado` |
| 2 · Descargar, tras verificar | ✅ Sin verificar no arranca ninguna descarga: la app avisa «Verifica tu correo para escuchar música offline» |
| 2 · `403` con `debeVerificar` | ⚠️ La app lee el campo, pero sin verificar no llega a las pantallas que editan: suscripciones, ajustes y datos no se pueden abrir. Si llegara un `403`, se enseña su `error` tal cual; **no recarga la cuenta sola** |
| 3 · Verificar activa la cuenta | ✅ Al volver a la app se refresca `GET /me` y el área aparece entera |
| 4–6 · Radar, artista y sello | ✅ Sin cambios: la app no envía material ni pide perfil |
| 7 · Restablecer también verifica | ✅ Nada que hacer en la app: el enlace abre la web |

⚠️ **Una diferencia a propósito.** Lo que **ya estaba descargado** en el móvil de una cuenta sin
verificar **sigue sonando**. Solo se bloquean las descargas nuevas: una cuenta creada antes del
13.09.26 puede tener listas bajadas, y quitárselas sin aviso nos pareció peor. Si queréis que se
silencien, se cambia.

## 3 · El selector del alta sin opción marcada

| Punto | En la app |
|---|---|
| Entra con las tres opciones sin marcar | ✅ «¿Cómo vas a usar Astra?» va primero, sin nada marcado, y debajo solo «Ya tengo cuenta» |
| Oyente → alta dentro | ✅ Al elegirla aparecen correo, contraseña, los 3 consentimientos y «Crear cuenta». `register` sale sin `perfil` y con `dispositivo` |
| Artista / Sello → web | ✅ «Continuar en astra.fm» abre `https://astra.fm/cuenta?alta=artista` o `?alta=sello` |

## Android va por detrás

**Android, versionCode 37**, lleva los puntos 1 y 2, pero **no el 3**: allí el alta entra todavía con
Oyente marcado. Cuando salga la siguiente versión de Android lo diremos aquí.

— la app
