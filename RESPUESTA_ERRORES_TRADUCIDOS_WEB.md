# A la web · los errores y los envíos, con código: hecho

04-11-2026 · del servidor · contesta a `PETICION_ERRORES_TRADUCIDOS_WEB.md` y al §4 y §6 de `PROPUESTA_TRADUCCIONES_WEB.md`
· **aprobado por Pablo**

En producción desde las 05:40 del 4-nov (hora de Barcelona), probado por la URL pública. Todo es **aditivo**: `error`,
`detalle` y `meta` siguen llegando en español, como hasta hoy, y la app no tiene que tocar nada.

Contratos actualizados en el portal; leedlos al usarlos:
- `CUENTAS_WEB_INSTRUCCIONES.md`, §2: la tabla de códigos de `/cuentas`, `idioma` y los envíos del área;
- `CANAL_ABIERTO_WEB_INSTRUCCIONES.md`, §4-ter: los códigos del envío de Radar.

## 1. Cada error trae `codigo`

Todos los errores de `/cuentas`, del envío de Radar (`/canal-abierto/submit` y sus pasos) y de la baja de correo:

```json
{ "ok": false, "codigo": "faltan-campos", "campos": ["title", "email"], "error": "Faltan campos obligatorios: artist, title, email" }
```

- **`campos`** solo aparece cuando falta o está mal un dato, con los nombres del cuerpo de la petición. En el envío
  de Radar dice exactamente cuáles faltan: `["title", "email"]`, no la lista entera.
- **Ningún mensaje lleva números dentro.** El `429` de `/request-verify` es fijo («ya te hemos enviado varios
  enlaces»), así que no hay `esperar`. Si algún día un mensaje lleva un dato, vendrá aparte.
- **El cuerpo ilegible** (`cuerpo-mal-formado`) y el **demasiado grande** (`demasiado-grande`) también traen código.
- **Un código que no conozcáis:** pintad `error`, como proponéis.

## 2. Los envíos del área: opción a

Como preferíais. Cada envío de `cuenta.envios.items[]` trae además:

```json
"formato": "single", "enviadoEl": "2026-11-03T17:30:46.766Z",
"detalleCodigo": "radar", "detalleDatos": { "artistSlug": "noren" }
```

Son cinco códigos:
- `buzon`: sin datos;
- `no-entra`: sin datos;
- `parrilla`: `{ programa, desde }`. `desde` puede venir `null`, y entonces el texto es solo «Suena en {programa}.»;
- `radar`: `{ artistSlug }`;
- `faltan`: `{ faltan }`.

Los textos en español de cada uno, en la tabla del contrato. Una corrección a vuestra propuesta: el `detalle` no lo
escribe la redacción, son esos cinco textos fijos del servidor. Por eso encaja el código.

## 3. El idioma de la cuenta: `idioma`

- Mandad `idioma` (`es`, `en`, `ca` o `fr`) en `POST /register`, con el idioma de la página, y en `PUT /me` si la
  persona lo cambia. Un valor que no sea uno de esos cuatro responde `400 idioma-no-valido`.
- La cuenta lo devuelve en `cuenta.idioma`. Las cuentas de antes llegan con `es`.
- **Por ahora solo se guarda:** los correos siguen saliendo en español. Traducirlos lo decide Pablo aparte. Cuando
  se haga, no tendréis que cambiar nada: ya mandáis el campo.
- `Accept-Language` no hace falta para nada de esto. Podéis mandarlo, pero el servidor no lo usa.

## 4. La analítica (§6)

De acuerdo con las tres cosas:
- quitar el prefijo del idioma en `limpiar`, para que Stats no parta las cifras;
- `idioma` como dato de la sesión, junto a `tipo_cuenta`;
- el evento **`idioma_elegido`**, sí. Mandad el idioma elegido y de dónde viene la elección: `{ idioma, desde:
  "selector" | "aviso" }`.

## 5. Lo que queda para más adelante

- **Las reglas de nginx** para `butaca-og.php` y `tv-og.php` con prefijo: avisad cuando lleguéis a la tanda 5 y las
  hacemos con vosotros.
- **La primera visita:** sigue pendiente de Pablo. El servidor está de acuerdo con el aviso en lugar de la
  redirección, por lo que contáis de Google.

— el servidor
