# A la web · actualización: la analítica es Umami, el contrato de Clarity queda anulado

13-sep-2026 · del servidor · **sustituye a lo anterior sobre analítica**

Contrato vigente, el único: `https://listen.astra.fm/docs/ANALITICA_WEB_INSTRUCCIONES.md`

## Qué pasa con lo que leísteis

Hoy hubo dos textos sobre analítica:

1. **`ANALITICA_INSTRUCCIONES.md` (Microsoft Clarity): anulado.** Estuvo un rato en el portal y el
   contrato de cuentas llegó a enlazarlo. Se retiró porque la radio descartó Clarity: sus eventos no
   llevan datos y necesita consentimiento para medir bien en la UE. **Ya no está en el portal y no
   hay que aplicar nada de él.**
2. **`CAMBIO_ANALITICA_WEB.md` (Umami): sigue valiendo.** Este aviso lo completa con la equivalencia
   de lo anterior y con una regla nueva sobre los nombres de los eventos.

Si algo del contrato de Clarity ya está hecho en la web, la tabla de abajo dice qué queda, qué cambia
y qué sobra.

## De Clarity a Umami, punto por punto

| En el contrato de Clarity | Ahora, con Umami |
|---|---|
| Script de Clarity (`w16y41ydrd`) cargado desde el código tras comprobar exclusiones y consentimiento | **Sale de la web.** En su lugar, la etiqueta de `https://stats.astra.fm/m.js` (§1 del contrato) |
| Banner de consentimiento propio, `clarity('consentv2', …)` | **No hace falta para la analítica:** Umami no usa cookies ni datos personales. Si el banner ya está hecho, no se publica por este motivo |
| Modo de consentimiento de Google (`gtag('consent', …)`) | **Fuera de este contrato.** Las etiquetas de Google quedan como están hasta que la radio decida |
| `?no-medir=1` y `?medir=1`: no cargar Clarity ni las etiquetas de Google | **Se mantienen los dos parámetros,** pero lo que hacen cambia: ponen o quitan `localStorage umami.disabled` (§3). Ya no deciden si se cargan las etiquetas de Google |
| `cuenta.interno: true`: no inicializar Clarity | **Se mantiene la idea:** con `cuenta.interno: true` se pone `umami.disabled` y no se quita al cerrar sesión (§3) |
| `clarity("set", "tipo_cuenta", …)` | `umami.identify({ tipo_cuenta })`, sin identificador de persona (§4). **Mismos valores** |
| `data-clarity-mask` en el área de cuenta | **Sobra:** Umami no graba pantallas. En su lugar, `astraAntesDeMedir` quita `email` y `token` de las direcciones (§2) |
| Eventos sin datos, con la variante en el nombre | **Eventos con propiedades** (§5). Equivalencias abajo |

### Eventos que cambian de nombre

| Clarity (anulado) | Umami (vigente) |
|---|---|
| `perfil_solicitado_artista` | `perfil_solicitado` con `{ tipo: 'artista' }` |
| `perfil_solicitado_sello` | `perfil_solicitado` con `{ tipo: 'sello' }` |
| `descarga_app_ios` | `descarga_app` con `{ plataforma: 'ios' }` |
| `descarga_app_android` | `descarga_app` con `{ plataforma: 'android' }` |

Los demás conservan el nombre y ganan propiedades: `escucha_inicio` y `escucha_1min` con `programa`,
`alta_iniciada` y `alta_completada` con `tipo`, `radar_envio_completado` con `con_cuenta`,
`concierto_entradas` con `artista`. `correo_verificado` y `login` siguen sin propiedades.

## Regla nueva: los nombres son fijos

La radio ya lee estos eventos en su Studio, en la pestaña **Stats**:
- el embudo del alta (`alta_iniciada` → `alta_completada` → `correo_verificado`);
- escuchas por programa, altas y perfiles por tipo, descargas por plataforma y entradas por artista.

Por eso:
- **Los nombres de eventos, propiedades y valores van exactamente como en la tabla del contrato:**
  minúsculas, guion bajo y los valores en minúscula (`oyente`, `ios`, `si`). Un nombre distinto
  aparece como un evento aparte y la pestaña no lo cuenta.
- **Un evento o propiedad nueva se habla antes con la radio,** para añadirlo también a la pestaña.

## Cómo se comprueba

Igual que en el aviso anterior, en la pestaña **Red** del navegador:
- **Al navegar:** `POST https://stats.astra.fm/api/m` con respuesta 200.
- **En verificar correo:** la `url` enviada va sin `email` ni `token`.
- **Con `?no-medir=1`:** no sale ninguna petición.
- **Sin Clarity:** no queda ninguna petición a `clarity.ms`.

Cuando esté publicado, basta un `PUBLICADO_ANALITICA_WEB.md` aquí: la radio lo comprueba en su
pestaña en tiempo real.
