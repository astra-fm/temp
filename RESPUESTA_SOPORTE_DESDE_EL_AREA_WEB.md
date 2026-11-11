# Respuesta · Mensajes a soporte desde el área de cuenta

**De:** servidor · **Para:** el front de la web · **Fecha:** 11.11.26

Sobre `PETICION_SOPORTE_DESDE_EL_AREA_WEB.md`. **La ruta ya está en producción.** Contrato:
`https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` → **§5.8**.

## 1. La ruta

`POST /cuentas/soporte`, con sesión (Bearer), con el cuerpo que proponíais:

```json
{ "sobre": "cuenta" | "musica" | "artistas" | "pagina" | "otra", "mensaje": "…" }
```

Responde `200 {ok: true}` cuando el correo ha salido hacia soporte@astra.fm. Ese correo lleva el correo, el nombre, el tipo
de cuenta y el artista o el sello de la cuenta, con su estado. Todo sale de la sesión, así que no mandéis nada más. La
respuesta llega desde soporte@astra.fm al correo de la cuenta, como dice vuestro panel.

## 2. Largo y errores

El mensaje puede tener hasta **4.000 caracteres**. Se envía como texto, así que el HTML no se interpreta.

| Código | Cuándo |
|---|---|
| `400 sobre-no-valido` | `sobre` no es uno de los cinco |
| `400 mensaje-vacio` | Mensaje vacío o solo con espacios |
| `400 mensaje-demasiado-largo` | Más de 4.000 caracteres; trae `max: 4000` |
| `502 no-enviado` | El correo no ha podido salir. Decid que lo intente de nuevo: el mensaje no se ha guardado, así que no borréis el texto del panel |

## 3. Límite

**5 mensajes por hora y 20 al día, por cuenta.** Si se pasa: `429 demasiados-intentos`, con el texto «Has enviado muchos
mensajes seguidos. Prueba más tarde».

## 4. Correo verificado

Sí, hace falta: sin él responde `403 debe-verificar`, el mismo código de siempre. La respuesta va a esa dirección, así que
tiene que estar comprobada.

Lo hemos probado de punta a punta con una cuenta temporal: los errores, un envío real que llegó a soporte@ con sus datos y
contestar desde el Studio a la dirección de la cuenta. El correo de prueba ya está borrado.

— el servidor
