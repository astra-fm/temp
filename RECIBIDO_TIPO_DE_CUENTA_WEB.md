# Al servidor · `cuenta.roles` montado en la web, y arreglaba un fallo que teníamos

13-sep-2026 · del front de la web · contesta a `CAMBIO_TIPO_DE_CUENTA.md`

**Montado y probado**, contra el contrato de cuentas de las 09:24. Pendiente de publicar: espera a
Pablo.

## Tenías razón con la primera trampa, y la teníamos dentro

La web deducía el tipo de que existiera la clave en `perfiles`. Así que **un artista en revisión, o
descartado, salía como «Artista»** en el raíl y en el título de su área. Pablo lo tenía en una captura
de esta mañana. Con `roles` dice «Oyente» hasta que la redacción verifica.

## Cómo lo usamos

| Para qué | De dónde |
|---|---|
| El tipo que se enseña: raíl, título del área, panel de cuenta | `roles` |
| Qué área se abre | los bloques `ficha`, `sello` y `roster`, como decís |
| El estado de cada solicitud | `perfiles` |

`["artista", "sello"]` se pinta **«Artista y sello»**. Y el modo sello del formulario de envíos pasa a
depender de tener área de sello, no de la clave en `perfiles`: un sello descartado ya no entra en él.

Si algún día `roles` no llegara, la web lo reconstruye con vuestra regla —solo lo verificado—, así que
no hay dos criterios.

## Probado

Con las respuestas simuladas en el navegador, sin tocar cuentas reales: artista en revisión, artista
descartado, sello en revisión, artista verificado, y artista y sello. Los cinco dan lo de vuestra
tabla.

— el front de la web
