# Aviso · el alta nueva está en producción

**De:** servidor · **Para:** el front de la web · **Fecha:** 12.11.26 · Sigue a `RECIBIDO_ALTA_AJUSTES_WEB.md`.

**Nuestra parte del alta nueva está en producción.** Podéis fusionar y pedir a Pablo permiso para desplegar. El contrato
completo está ya en `https://listen.astra.fm/docs/CUENTAS_WEB_INSTRUCCIONES.md` → **§5.10**. Lo que decía de artista y
sello en §1, §2 y «Estados» está marcado como sustituido.

Sale tal como lo acordamos en `RESPUESTA_ALTA_NUEVA_WEB.md` y `RESPUESTA_ALTA_AJUSTES_WEB.md`, con la dirección de la
página que confirmasteis: **`paginaPublica.url` = `https://astra.fm/radar/artista/<slug>`** (y `/sellos/<slug>`).

## Lo que añadimos o concretamos al implementarlo

1. **Hasta abrir el enlace de acceso, el área entera responde `403 debe-verificar`, también los cambios de la página.**
   Solo funcionan Mis datos (`PUT /me`, `POST /password`), `GET /me`, `DELETE /me` y la subida del alta.
2. **`POST /request-verify` en el alta:** `409 en-revision` mientras no está aprobado. Aprobado, reenvía el enlace de acceso,
   igual que `acceso/reenviar`.
3. **`sello.paginaPublica.faltan`** puede traer también **`"artista"`**: el sello no sale sin al menos un artista con su
   página a la vista. Llega cuando el sello está verificado.
4. **`ficha.estado: "publicada"`** quiere decir que la página existe y se edita al momento (§5.9), aunque aún no se vea.
   Lo que se ve lo dice `paginaPublica.publicada`.
5. **El audio de un alta no aprobada se borra en el momento**, no a los 30 días. Lo mismo con el que se sustituye.
6. **`POST /register-form`** (el formulario sin `fetch`) no puede subir la canción, así que no sirve para artista ni sello:
   responde `alta=error&motivo=datos`.
7. **Un código más:** `403 sin-alta` en `alta/cancion` si la cuenta no está en un alta.
8. **La cuenta antigua que esperaba su música** ya está en `falta-cancion`, con `alta.cancion: null` y el correo ya
   verificado. Pintadla como cualquier `falta-cancion`. Al aprobarla, su correo no lleva enlace de acceso: lleva
   «Entrar en mi área», porque ya verificó su correo.
9. **Tras aprobarse**, la canción del alta aparece en Mi música como un single validado más.

## Lo que hemos probado

De punta a punta, con cuentas temporales, todo correcto:

- las validaciones y los códigos del alta;
- el alta por enlace y por archivo, el cambio de uno a otro y la sustitución;
- el alta del sello con sus géneros, y que no admite enlace;
- Mis datos en el alta, y el resto del área cerrado;
- aprobar y no aprobar, también a mano desde el Studio;
- el enlace de acceso: abierto, usado, caducado, inventado y reenviado;
- la página que espera foto y bio y sale al completarla, con su `url`.

Nada de la prueba ha quedado en producción.

— el servidor
