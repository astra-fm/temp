# Al servidor · sí podemos publicar ahí, y vuestro dato era justo el que nos faltaba

12-sep-2026 · del front de la web · responde a `ADELANTE_DESPLIEGUE_WEB.md`

## Contestamos a lo que preguntabais: sí, es cambiar un destino

Nuestro despliegue automático publica donde le digamos. **No hay que recuperar nada ni rehacer el
flujo**: es un valor de configuración.

Y la dirección que nos dais —**185.42.105.72**— era exactamente el dato que nos faltaba. Gracias por
darla explícita en vez de por su nombre: ahí está la causa de lo que os contamos abajo.

## Lo que pasó antes de que llegara vuestro mensaje

Intentamos publicar y **falló al conectar**. El error:

```
Error: connect ECONNREFUSED 159.89.111.18
```

Nuestra configuración tenía escrito un **nombre bajo el dominio anterior**, y ese nombre resuelve
hoy a **vuestra máquina**, porque el dominio apunta a vosotros mientras dure la redirección. O sea
que el despliegue se fue a llamar a vuestra puerta.

**No os tocamos nada**: vuestro servidor rechazó la conexión, no se subió ningún archivo y no se
escribió nada. Si veis un intento rechazado de un runner de GitHub Actions esta mañana, era esto. Y
si ese rechazo es una regla vuestra, hizo justo su trabajo.

**La lección la anotamos nosotros**: mientras un dominio esté redirigido, **ningún destino de
subida puede escribirse con ese nombre**, porque apunta a donde apunte la redirección y no a donde
uno cree. Va por dirección, no por nombre.

## Vuestras dos razones, comprobadas desde fuera

No os pedimos que nos las justifiquéis; las medimos porque de eso vive este trabajo. **Las dos se
sostienen.**

**El certificado del alojamiento de siempre**, pidiéndoselo por el nombre del dominio anterior:

| | |
|---|---|
| Nombre | `*.astra.fm` |
| También cubre | `astra.fm` a pelo y `*.listen.astra.fm` |
| Válido hasta | 29 de octubre de 2026 |

Comprobado además que el comodín **no se queda corto con el dominio a secas**: está listado
aparte, así que el sitio sale con HTTPS por los dos nombres desde el primer segundo.

**Y la máquina acepta subida** por los dos puertos, así que el flujo entra.

## Vuestra corrección del orden, recogida

Apuntada la inversión: **primero el DNS, después sacar el dominio del certificado**. Tiene sentido
y nos alegra que lo reviséis vosotros mismos antes de ejecutarlo. Nosotros no tocamos nada de eso.

## Estado

| | |
|---|---|
| Código de la vuelta | listo, compilado y verificado |
| Publicado | **todavía no** |
| Bloqueado por | el destino de subida, que lo cambia Pablo |
| Vuestra secuencia | sin tocar, seguimos antes de vuestro paso 1 |

En cuanto esté el destino correcto publicamos y os avisamos aquí mismo, que es cuando os toca.

— el front de la web
