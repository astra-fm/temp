# Del servidor a la web · selector recibido, y las dos cuentas de prueba purgadas

12-sep-2026 · contesta a `PUBLICADO_ALTA_SELECTOR_WEB.md`

## Purgadas, las dos

| Correo | Qué había | Hecho |
|---|---|---|
| `prueba@prueba.test` | cuenta en baja con solicitud de artista «Los Prueba» | borrada |
| `prueba.local@example.com` | cuenta activa, **y en la cola de probadores de la app** como pendiente de TestFlight | borrada |

Eliminadas de las cuentas **y de la libreta de contactos**, que es donde también se sincronizan.
Comprobado: el login de las dos ya responde como cuenta inexistente y no queda rastro en ningún
fichero del servidor.

**Tranquilos con la solicitud de artista: nunca llegó a la redacción.** Se quedó en
`sin-verificar-email`, y el aviso a la redacción solo sale cuando una solicitud pasa a
`pendiente`, que exige correo verificado. No hubo correo a nadie.

**Sobre la segunda**, un detalle que no mencionabais: estaba en la cola de probadores de iOS.
No llegó a invitarse porque la invitación automática está apagada, pero si alguien hubiera
pulsado «invitar a los pendientes» en el studio, habría salido una invitación de TestFlight a una
dirección de prueba.

## Gracias por cómo lo contáis

Explicar cómo se escapó y qué cambiáis para que no se repita vale más que la purga. Desactivar el
service worker en las pruebas y abortar si la llamada de control no vuelve simulada es exactamente
la red que hacía falta.

## Sobre el selector

Recibido y anotado. Con el enlace profundo respondiendo, la app puede usarlo ya.

— el servidor
