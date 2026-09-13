# Al servidor · la analítica con Umami está publicada

13-sep-2026 · del front de la web · cierra `CAMBIO_ANALITICA_WEB.md` y `ACTUALIZACION_ANALITICA_WEB.md`

**Publicado en `astra.fm`.** Contra `ANALITICA_WEB_INSTRUCCIONES.md` leído hoy a las 16:39. Del
contrato anulado de Clarity no se había aplicado nada.

## Comprobado en producción

| | Resultado |
|---|---|
| Etiqueta de `stats.astra.fm/m.js` con ID, dominios y `astraAntesDeMedir` | ✅ servida, `m.js` responde 200 |
| Clarity | ✅ fuera del HTML, cero peticiones a `clarity.ms` |
| `?no-medir=1` | ✅ pone la llave, quita el parámetro y **cero envíos a `/api/m`** navegando después |
| Etiquetas de Google | sin tocar |

**No hemos generado visitas reales para probar el 200 de `/api/m`**: las pruebas en producción se
hicieron con el navegador excluido. La primera visita real que veáis en el panel es de verdad.

## Comprobado en local, con un Umami simulado

Sin tocar vuestra analítica ni cuentas reales:

- `astraAntesDeMedir` con `/verificar-correo?email=…&token=…&ok=1` devuelve `/verificar-correo?ok=1`,
  y el `referrer` también sale limpio.
- `tipo_cuenta`: `anonimo` sin sesión y `oyente` al iniciarla. Sale de `cuenta.roles`, nunca con
  identificador.
- `cuenta.interno: true` pone `umami.disabled`, y cerrar sesión no la quita.
- Eventos, con nombres y valores exactos de la tabla: `escucha_inicio` y `escucha_1min` con
  `programa` —el minuto medido con la radio sonando de verdad—, `alta_iniciada` y `alta_completada`
  con `tipo`, `login`, `correo_verificado` y `descarga_app` con `plataforma`.
- **Sin probar en navegador**, montados igual: `perfil_solicitado`, `radar_envio_completado` con
  `con_cuenta` y `concierto_entradas` con `artista`.

## Dónde se dispara cada uno

| Evento | Dónde |
|---|---|
| `escucha_inicio` | el `playing` del audio de la radio, no el clic: cuando suena |
| `escucha_1min` | sesenta segundos seguidos sonando; una pausa o un corte del búfer reinician la cuenta. Una vez por reproducción |
| `alta_iniciada` | al abrir el alta, y cada vez que cambia la opción del selector. Ver abajo |
| `alta_completada` | la cuenta queda creada |
| `login` | el inicio de sesión responde bien |
| `correo_verificado` | la página del enlace confirma la verificación |
| `perfil_solicitado` | la solicitud de perfil desde el área responde bien |
| `radar_envio_completado` | el envío de Radar queda hecho, con o sin carátula y audio |
| `concierto_entradas` | «Más información» de la agenda de la portada cuando lleva a las entradas |
| `descarga_app` | los dos botones de tienda de la cabecera y los enlaces «App iOS» y «App Android» del pie |

## ⚠️ Una decisión que os afecta al embudo

**`alta_iniciada` cuenta también la opción preseleccionada al abrir el alta.** El contrato dice «se
elige una opción», pero «Solo escucho» viene marcada de serie: quien se da de alta como oyente sin
tocar el selector habría llegado a `alta_completada` sin ningún `alta_iniciada`, y vuestro embudo
tendría más altas completadas que iniciadas.

Así, cada apertura del alta cuenta una vez con la opción marcada, y cada cambio de opción vuelve a
contar con la nueva. **Consecuencia**: quien cambia de opción aparece más de una vez en
`alta_iniciada`. Si preferís contar solo una por visita, o solo cambios, decidlo y lo ajustamos.

— el front de la web
