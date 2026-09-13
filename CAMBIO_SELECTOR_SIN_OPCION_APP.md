# A la app · el selector del alta entra sin ninguna opción marcada

13-sep-2026 · del servidor · **cambio de contrato, pequeño**

Contrato actualizado: `https://listen.astra.fm/docs/CUENTAS_APP_INSTRUCCIONES.md`, en «Alta con
selector».

## Qué cambia

Decisión de producto: **al entrar en el alta no hay ninguna opción seleccionada.** Hasta hoy el
contrato pedía **Oyente preseleccionado**. Ahora quien entra elige Oyente, Artista o Sello, y no pasa
nada hasta que elige.

**El servidor no cambia,** y lo demás del alta sigue igual.

## Por qué

- **Con Oyente marcado de serie,** darse de alta como oyente no era una elección.
- **Para contar bien.** La radio mide ya en la web cuánta gente empieza un alta y de qué tipo, y un
  valor marcado de serie cuenta como elegido aunque nadie lo haya tocado.
- **Misma regla en la web y en la app,** para que se comporten igual.

## El recorrido

1. **El alta abre con las tres opciones y ninguna marcada.**
2. **La persona elige:**
   - **Oyente:** aparece el alta dentro de la app, con correo, contraseña y 3 consentimientos. El
     `dispositivo` lo añade la app, como hasta ahora.
   - **Artista:** abre en el navegador `https://astra.fm/cuenta?alta=artista`.
   - **Sello:** abre en el navegador `https://astra.fm/cuenta?alta=sello`.
3. **La web ya tiene publicado el enlace profundo:** abre su alta con esa opción elegida, así que
   nadie elige dos veces.

Cómo se ve en pantalla un selector sin nada marcado es cosa de diseño.

Las reglas de siempre no cambian: la app **nunca crea perfiles** (ni `perfil` en `register` ni
`POST /cuentas/perfil`), y el área de oyente sigue limpia.

Cuando esté en una versión, basta con un `PUBLICADO_SELECTOR_SIN_OPCION_APP.md` aquí.

— el servidor
