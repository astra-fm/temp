# A la web · el alta empieza por un selector: Oyente, Artista, Sello

12-sep-2026 · del servidor · **cambio de contrato**

## Qué cambia

Decisión de Pablo: **que el alta y el área del oyente queden limpios**, sin nada de artistas ni
sellos. Para eso el alta empieza por un selector de tres opciones.

**El servidor no cambia.** Mismos endpoints y mismos cuerpos. Es un cambio de pantallas y de
reglas. Contrato actualizado en el portal:

`https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` → §1.1 (nueva), §3 y §4.

## Lo que hay que construir

**1 · Selector en `/cuenta`**, con Oyente preseleccionado.

**2 · Un formulario por opción:**

| Opción | Campos | `POST /cuentas/register` |
|---|---|---|
| Oyente | correo · contraseña · 3 consentimientos · nombre opcional | sin `perfil` |
| Artista | lo mismo + nombre artístico | `perfil: {tipo: "artista", nombre}` |
| Sello | lo mismo + nombre del sello | `perfil: {tipo: "sello", nombre}` |

- **Oyente no lleva nada de artistas ni sellos.** Desaparece el antiguo paso opcional.
- **Artista y Sello llevan una frase visible** de que es una solicitud que revisa la redacción.
- **Sello, solo el nombre.** La web y el cargo se piden en su área, no en el alta.

**3 · El área, según `cuenta.perfiles`:**

- **Vacío**: área de oyente limpia. **Se quita el bloque de «solicitar perfil».**
- **Con perfil**: el estado de la solicitud, como hasta ahora.
- **Un enlace discreto** en ajustes, «¿Haces música o llevas un sello?», para el oyente que
  empiece a hacer música más adelante. Enlace, no bloque.

**4 · El enlace profundo que va a usar la app.** Esto es nuevo y lo necesita la app:

```
https://astra.fm/cuenta?alta=artista
https://astra.fm/cuenta?alta=sello
```

- Sin sesión: abre el alta con esa opción ya elegida.
- Con sesión: abre el formulario de solicitar ese perfil en el área.
- Valor raro o ausente: selector normal.

## Un aviso aparte, y es una corrección

La cabecera del contrato decía que las cuentas migradas se validan contra WordPress en su primer
login. **Ya no**: esa dependencia se cortó el 11-sep. Ahora esas cuentas reciben en el login un
`401` con `debeRestablecer: true`. Pintad el `error` tal cual y ofreced directamente restablecer
la contraseña. El campo es aditivo, no rompe nada si no lo leéis.

## Lo que no cambia

El modelo sigue siendo el mismo: todo el mundo nace oyente, y artista o sello son solicitudes.
**El selector enruta, no concede.**

— el servidor
