# Petición · Mensajes a soporte desde el área de cuenta

**De:** el front de la web · **Para:** servidor · **Fecha:** 11.11.26 · Con el visto bueno de Pablo.

Gracias por las fases 2 y 3: con ellas está todo lo de `PETICION_AREA_ARTISTA_Y_SELLO_MI_MUSICA_WEB.md`. Ha salido una
cosa nueva al cerrar el diseño del área.

## Qué ha decidido diseño

Pablo aprobó hoy un botón de **soporte** que flota en Mi cuenta: en las pestañas del artista, del artista sin página y
del sello. Abre un panel pequeño con tres cosas:

- **Sobre qué**: Mi cuenta · Mi música (en el sello, Artistas) · Mi página pública · Otra cosa. Viene elegida la pestaña
  en la que se está.
- **Mensaje**: texto libre.
- **Enviar mensaje**. Al enviarlo, el panel dice «Recibido. Te contestamos desde soporte@astra.fm.».

Hoy no hay a dónde mandarlo: la web no tiene formulario de contacto y el contrato no tiene ruta de soporte.

## Qué necesitamos

Una ruta con sesión, por ejemplo `POST /cuentas/soporte`, que reciba:

```json
{ "sobre": "cuenta" | "musica" | "artistas" | "pagina" | "otra", "mensaje": "…" }
```

Y que el mensaje llegue a **soporte@astra.fm** con lo que haga falta para contestar sin preguntar, sacado de la sesión y
no de lo que mande la web: correo de la cuenta (para responder), tipo de cuenta y nombre del artista o del sello.

## Lo que nos gustaría saber

1. La ruta y su respuesta (`{ok}` nos basta).
2. Largo máximo del mensaje y códigos de error (vacío, demasiado largo, `sobre` no válido).
3. Límite de envíos por cuenta y el código cuando se pasa, para decirlo en el panel.
4. Si hace falta el correo verificado. Quien ve el panel siempre lo tiene, pero preferimos saberlo.

No es urgente. Mientras tanto montamos el panel sin enviar y no lo publicamos hasta que esté la ruta.

— el front de la web
