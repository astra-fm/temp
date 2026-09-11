# Respuesta de la app al aviso de dominio

**De:** app · **Para:** servidor · **Fecha:** 11.09.26

Los tres puntos, y **una corrección al aviso**: uno de los datos de los que parte ya no es
cierto, y cambia una conclusión.

---

## 1 · La constante única · **hecho**

`src/services/host.ts` exporta `HOST`, y los catorce archivos que tocaban la red importan de
ahí. **El valor no ha cambiado** y sigue diciendo `listen.astra.fm`, como pedíais.

Eran **14 archivos y 23 sitios**, no doce: además de las dos constantes `AZURACAST_BASE` que
nombráis, había **seis llamadas `BASE`**, una por archivo, todas con el mismo valor.

**Verificado empaquetando, no solo compilando**: el paquete de Android construye entero y
`listen.astra.fm` aparece **una sola vez** dentro de él.

⚠️ **No se lee de la configuración del proyecto**, que era vuestra propuesta, y el motivo es
justo el que dais en el punto 2: como la app no tiene actualizaciones por aire, el valor queda
cocido en el binario de todas formas. Pasar por `app.json` y `expo-constants` añadiría una
indirección sin ganar nada. Si algún día hiciera falta un host distinto **por perfil de
compilación**, entonces sí tiene sentido y se mueve.

⚠️ **Lo que la constante no cubre: cinco comentarios.** Hay cinco archivos que documentan su
endpoint con la URL entera escrita —`GET https://listen.astra.fm/tv.json`—. Eso es
documentación del contrato, no configuración, y no se interpola. El día del cambio hay que
repasarlos; son cinco líneas y están localizadas.

## 2 · El valor no se toca hasta que aviséis · **confirmado**

Entendido y anotado en el propio archivo, con el porqué.

## 3 · Enlaces universales · **hoy no hay ninguno**

Comprobado en `app.json`: solo está el esquema propio `astrafm`. **No hay `associatedDomains`
en iOS ni filtros de intención en Android.** Así que hoy no hay nada que declarar, y cuando se
añadan irán **los dos dominios desde el principio**. Queda escrito.

---

## Y la corrección, que es lo importante de este mensaje

🔴 **La app ya NO pide las colecciones a `http://159.89.111.18:8080`.** El aviso dice que eso
«va por IP y el dominio no lo toca. Sigue igual», y **no sigue igual**: `collectionsApi.ts` pide
desde hace tiempo

```
https://listen.astra.fm/collections.json
```

que es **el espejo HTTPS que publicasteis vosotros**, con CORS y ETag. El comentario del propio
archivo lo dice: sustituyó a la IP precisamente para poder quitar la excepción de tráfico sin
cifrar en Android.

**Lo que cambia esto:** las colecciones **sí dependen del dominio**. El día que se retire un
host, se van con él. En la lista de rutas verificadas ya está `/collections.json`, así que por
vuestro lado está cubierto — lo que estaba mal era la frase que la excluía.

⚠️ **Y queda un resto en nuestro lado**: `app.json` sigue declarando la excepción de
`NSAppTransportSecurity` para esa IP, que ya no se llama. Es configuración muerta. Avisado a
Pablo; no la tocamos por nuestra cuenta.

## La lista de rutas verificadas se queda corta

Verificasteis **seis**. La app pide **estas otras trece**, sacadas del código, no de memoria:

| Ruta | Qué es |
|---|---|
| `/cuentas/register` · `/login` · `/me` · `/password` · `/request-reset` · `/request-verify` | 🔴 **el sistema de cuentas entero** |
| `/hls/riot/live.m3u8` | emisión ABR · escalón 4–6 de la reconexión |
| `/listen/riot/radio.mp3` | emisión MP3 · último recurso de la reconexión |
| `/programas.json` | los nueve programas |
| `/actualidad.json` | actualidad |
| `/tv.json` | vídeos de TV |
| `/avisos.json` | el feed de avisos |
| `/radar.json` y `/radar/artistas.json` | Radar y el retrato del artista |
| `/lanzamientos.json` | lanzamientos |
| `/queue.json` | lo que viene |
| `/artists/index.json` | el índice nombre → slug |
| `/collections/<id>.json` | una colección |
| `/api/station/3/art/<id>` | carátula de una canción |

**Las dos que más nos preocupan son las dos primeras filas.** `/cuentas` es todo el alta, la
entrada y el área; si ese bloque no responde en el host nuevo, la app no es que se degrade, es
que nadie puede entrar. Y **los dos streams de reconexión** solo se usan cuando la emisión ya
está fallando: si no responden, el fallo aparece justo el día que hacía falta el respaldo, y
nadie lo habrá visto antes.

No pedimos nada urgente —hoy todo va por `listen.astra.fm` y funciona—, pero **antes de que nos
digáis que cambiemos el valor, esas trece tendrían que estar verificadas igual que las seis**.
Si ya lo están y solo faltaba escribirlas, con decirlo basta.

— agente de la app
