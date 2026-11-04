# Al servidor · los errores y los envíos, en el idioma de la página

04-nov-2026 · del front de la web · sigue a `PROPUESTA_TRADUCCIONES_WEB.md` §4 · **decisión de Pablo**

Pablo ha decidido que **los mensajes de error se traducen**: son validación, información fija, y mandan
material personas de muchos países. Va en la tanda 2, junto al alta y al área del oyente, del artista y del sello,
que se traducen enteras.

Contratos leídos el 04-nov: `CUENTAS_WEB_INSTRUCCIONES.md` (01-nov, 11:42) y `CANAL_ABIERTO_WEB_INSTRUCCIONES.md`
(01-nov, 11:42).

## Lo que hacemos nosotros, sin esperar

Los errores que vuestro contrato documenta por su código HTTP los traducimos en la web. Reconocemos el caso por la
llamada, el estado y vuestras marcas, no por el texto:

- `POST /register` `409` (correo ya registrado) · `429`
- `POST /login` `401` (credenciales), y `401` con `debeRestablecer`
- `POST /reset-password` `403` · `POST /verify-email` `403` (enlace inválido o caducado)
- `403` con `debeVerificar` en `PUT /me`, `POST /password`, `POST /perfil` y `/sello`
- `POST /perfil` `409` (ya solicitado) · `POST /roster/proponer` `409` (ya está en el roster)
- `POST /ficha/proponer` `400` (sin cambios), `403` y `409`
- `POST /request-verify` `429`
- Radar: `429`, `404` (no existe o ya revisado), `500` («No se pudo procesar la imagen»), «Formato no admitido» y
  «No se pudo procesar el audio»

Si llega un caso que no conocemos, pintamos vuestro `error` en español, como hoy. Así nada se queda sin mensaje.

## Lo que necesitamos de vosotros

**1. Los `400` que dicen qué falta.** Hoy el texto varía (`/register`, `/sello/proponer`, `/roster/proponer`, y el
envío de Radar: «dice exactamente qué falta»). No lo podemos reconocer. Pedimos que, además del `error`, venga:

```json
{ "ok": false, "error": "…", "codigo": "faltan-campos", "campos": ["relacion", "enlace"] }
```

Con un `codigo` estable por caso, y `campos` con los nombres del cuerpo de la petición cuando sea lo que falta.

**2. Los envíos del área** (`cuenta.envios.items[]`). El contrato dice que el front nunca redacta los mensajes de
envíos, así que no lo hacemos por nuestra cuenta. Son dos textos vuestros:

- `detalle`: los cuatro textos de siempre, más el de `faltan`;
- `meta`: «EP · enviado el 5 de agosto de 2026».

Valen dos salidas. Decidid vosotros:

- **a. Un código y sus datos**, y el texto lo pone la web en los cuatro idiomas:
  `"detalleCodigo": "parrilla", "detalleDatos": { "programa": "Haciendo Cosas Raras", "fecha": "2026-08-12" }`, y
  `formato` y `enviadoEl` (ISO) en lugar de `meta`. Os ahorra traducir.
- **b. El texto en el idioma de la página**, en `/cuentas/me`. Desde la tanda 2 mandamos en cada llamada a
  `/cuentas` la cabecera `Accept-Language` con el idioma de la página (`es`, `en`, `ca`, `fr`), no el del
  navegador.

Nos inclinamos por la **a**: los textos estarían con el resto de la interfaz, con el mismo glosario.

**3. Los mensajes con un dato dentro** que pintáis listos, como el `429` de `/request-verify` «con mensaje listo
para pintar»: si lleva un número (minutos, intentos), que venga también aparte (`"esperar": 12`).

## Qué no cambia

El `error` en español sigue llegando como hasta ahora: es el respaldo y lo que lee la app. Todo lo nuevo son campos
añadidos.

— el front de la web
