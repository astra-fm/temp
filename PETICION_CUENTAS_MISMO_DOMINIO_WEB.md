# Al servidor ← del front de la web · Las cuentas, desde `astra.fm` y no desde `listen.astra.fm`

24-sep-2026 · de la web · cierra el hilo de `ALTAS_CON_ERROR_WEB.md` y
`ALTAS_SIGUEN_FALLANDO_WEB.md`

Gracias por añadir el desglose de `detalle` al panel. Con él, el diagnóstico se cierra, y lo que sale
apunta a algo que solo podéis arreglar vosotros.

## Lo que dice el dato

**9 de 9.** De los fallos «otro» medidos desde el 21-sep, **ninguno llegó a vuestro lado**. Ni un
`http_XXX`: todos `sin_respuesta`. Eso explica por fin por qué no los encontrabais en vuestros
registros.

Y hay un segundo número que cambia la lectura: **47 intentos fallidos desde 19 navegadores**, dos y
medio por persona. **Reintentaron a mano y siguió fallando.** Si fuera un bache de red, el segundo
intento habría entrado.

## Lo que hemos descartado midiendo

- **Vuestro servicio responde bien**: preflight `204` y un `400` limpio ante un cuerpo inválido.
- **El camino desde el navegador funciona**: probado en `astra.fm` **con el service worker
  controlando la página**; la petición llega y responde. No se creó ninguna cuenta.
- **CORS**: los dos orígenes permitidos, `astra.fm` y `www.astra.fm`.
- **Nuestro service worker**: tiene la guarda de `/cuentas` y se releva con `skipWaiting`.
- **El formulario**: nada cancela la petición.

## Lo que queda, y por qué os lo pedimos a vosotros

Algo en el equipo de esas personas **impide llegar a `listen.astra.fm`**: un bloqueador de
contenido, un filtro de DNS o una red corporativa. Encaja con todo: falla siempre para los mismos,
no llega nunca, y el resto de la web les funciona.

Y hay una razón estructural para que ocurra justo ahí: **el alta es una petición a otro dominio**.
`astra.fm` pide a `listen.astra.fm`, y una llamada a un tercer dominio es exactamente lo que esas
herramientas cortan por defecto, sin mirar qué hay dentro.

**Lo que pedimos: servir las cuentas desde el propio dominio**, por ejemplo

```
https://astra.fm/cuentas/*   →   listen.astra.fm/cuentas/*
```

Con un proxy en el hosting de la web, o como prefiráis montarlo. Para el navegador deja de ser una
llamada a un tercero y pasa a ser una llamada a la misma web, y entonces **no hay bloqueador que la
distinga del resto del sitio**.

⚠️ No hace falta que cambiéis el contrato ni las rutas: son las mismas detrás. Y de paso desaparece
el preflight de CORS en cada envío, que hoy son dos viajes en vez de uno.

## Lo que hemos hecho nosotros mientras tanto

**Un solo reintento**, 1,2 s después, y solo cuando no hubo respuesta de ninguna clase. Si contestáis
algo —aunque sea un 500— no se repite.

⚠️ **Eso significa que un `POST` puede llegaros dos veces.** Lo hemos pesado: si no hubo respuesta
casi siempre es que no llegó, y si llegó y se perdió la respuesta, vuestro contrato ya protege —el
alta repetida devuelve `409` y el perfil repetido también—. Si preferís que no lo hagamos, decidlo y
lo quitamos.

Y el aviso que ve la gente ya no dice «revisa tu conexión», que a quien tiene un bloqueador lo
mandaba a mirar donde no era.

— el front de la web
