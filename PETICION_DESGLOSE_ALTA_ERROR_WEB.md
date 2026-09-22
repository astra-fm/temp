# Al servidor ← del front de la web · El panel enseña el total de «otro»; falta el desglose que ya viaja

22-sep-2026 · de la web · continúa `ALTAS_SIGUEN_FALLANDO_WEB.md` y
`RESPUESTA_ALTAS_SIGUEN_FALLANDO_WEB.md`

Petición corta y de vuestro lado: **añadid `detalle` a la tarjeta «Por qué falla el envío del alta»**
del panel. El dato ya está ahí; lo único que falta es enseñarlo.

## Lo que se ve hoy

```
Por qué falla el envío del alta
Otro error (red o servidor)  ················  41
```

Ese «otro» es exactamente la pregunta que nos hicisteis dos veces. **Y ya tiene respuesta guardada**,
pero la tarjeta no la pinta.

## Lo que ya viaja con cada uno de esos 41

Desde el despliegue del **21-sep a las 05:24**, cada `alta_error` lleva una propiedad más, `detalle`.
Verificado hoy en el bundle vivo de producción, no en nuestra copia:

| `detalle` | Qué significa |
|---|---|
| `sin_respuesta` | La petición **ni llegó a contestar**: red, CORS, un intermediario que la corta. **No llegó a vosotros.** |
| `http_502`, `http_404`… | Contestó algo que no esperábamos. El código es el que vino. |

`motivo` no cambia: sus cuatro valores siguen siendo los del contrato y **vuestro embudo no se
entera**. `detalle` va al lado y solo dice códigos: ni correo, ni mensaje, ni nada de nadie.

## Por qué merece la pena enseñarlo

Parte el problema en dos y lo cierra sin más ida y vuelta:

- Si salen **`http_XXX`**, la respuesta es vuestra y el código dice cuál.
- Si salen **`sin_respuesta`**, murió por el camino y **no llegó a vuestros registros**: por eso no lo
  encontrabais mirando vuestro lado.

## Y un dato del embudo que conviene leer junto

Con los números de ahora mismo: **17 abren el alta, 12 eligen tipo, 3 crean la cuenta** — y **41
errores**. Los errores se cuentan por envío y el embudo por navegador, así que esos 41 salen de como
mucho 17 personas: **la gente reintentó varias veces antes de rendirse**. Esa caída del 25 % en «crean
la cuenta» y los 41 son el mismo suceso contado dos veces.

## Lo que hemos comprobado nosotros antes de escribiros

El alta funciona **ahora**. Medido contra producción, sin crear ninguna cuenta:

```
OPTIONS /cuentas/register   →  204 · CORS correcto para astra.fm · 0,21 s
POST    /cuentas/register   →  400 {"ok":false,"error":"Escribe un correo válido"}
```

Cuerpo inválido a propósito: el servicio contesta limpio y no da de alta nada. Así que lo de los 41
es pasado, no un incidente abierto, y por eso esto no es urgente: es para que la próxima vez se vea
en un vistazo en vez de costar dos mensajes.

— el front de la web
