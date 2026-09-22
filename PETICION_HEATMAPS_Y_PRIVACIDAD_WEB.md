# A la web · mapas de calor: falta cargar el grabador, y un texto para privacidad

22-sep-2026 · del servidor · petición de Pablo

Los mapas de calor de Stats salen vacíos («No data available»). El motivo es de los dos lados y ya
hemos hecho el nuestro: **en Umami el mapa de calor no se calcula con el script de medición, sino con un
grabador aparte** (`recorder.js`) que recoge clics y desplazamiento. Estaba desactivado; Pablo lo ha
activado hoy. Falta que la web lo cargue.

## 1 · Cargar el grabador

Junto al script de medición que ya tenéis:

```html
<script defer src="https://stats.astra.fm/recorder.js"
        data-website-id="3fc509d6-c343-4689-b954-2fc3c7a44e2d"></script>
```

- Lee su configuración sola de `https://stats.astra.fm/api/websites/<id>/recorder` (responde `200`
  público) y manda a `/api/record`. No hay nada más que configurar por vuestra parte.
- Ajustes que ya hemos puesto en el servidor, para que sepáis con qué trabaja: **15 % de las sesiones**,
  **máximo 5 minutos** por sesión, enmascarado **moderado** (lo que se escribe en los campos no se
  guarda) y mapas de calor activados.

## 2 · Dónde NO cargarlo · **esto es lo importante**

No lo carguéis en las rutas donde la persona escribe sus datos:

- `/cuenta` y todo lo que cuelgue de ella (alta, acceso, área de usuario, envíos de Radar)
- `/verificar-correo` y `/restablecer-contrasena`

El enmascarado ya evita guardar el contenido de los campos, pero preferimos no grabar ni el recorrido en
esas pantallas. Si en alguna página suelta hay un bloque que no queráis registrar, `rr-block` en su clase
lo excluye.

## 3 · Texto para el aviso de cookies y la política de privacidad

Encargo de Pablo: que esto quede dicho **sin asustar y sin mentir**. La base es que **los datos no salen
de nuestro servidor** —Umami está alojado en `stats.astra.fm`, no es Google ni Hotjar—, y eso conviene
decirlo, porque suma confianza en vez de restarla.

**En el aviso de cookies, dentro de la categoría de analítica** (no hace falta una categoría nueva):

> Nos ayuda a entender cómo se usa la web: páginas más vistas, en qué se hace clic y hasta dónde se
> desplaza la página. No recogemos lo que escribes ni datos que te identifiquen.

**En la política de privacidad**, párrafo dentro del apartado de analítica:

> Para mejorar la web medimos su uso de forma agregada con una herramienta propia alojada en nuestros
> servidores. En una parte de las visitas registramos interacciones como los clics y el desplazamiento
> por la página. El contenido de los formularios queda oculto y no se almacena, y no registramos esta
> actividad en las páginas de cuenta ni en las de acceso. No compartimos estos datos con terceros ni los
> usamos para publicidad.

Dos avisos honestos sobre ese texto:

- **Es analítica no esencial**, así que va detrás del consentimiento del banner, como el resto de la
  medición. Si vuestro banner ya tiene «analíticas» y solo carga la medición al aceptar, cargad el
  grabador en el mismo punto.
- **No somos vuestros abogados.** Pablo va a pasar el texto por su gestoría; si os lo devuelven
  corregido, mandad la versión buena y la ponemos también en el contrato.

## 4 · Qué veréis cuando esté

Umami empezará a llenar `heatmap_event` y `session_replay`. Los mapas de calor y la sección Replays del
panel dejan de estar vacíos en cuanto pase tráfico real, con el 15 % de muestreo. Avisadnos al
desplegar y lo comprobamos desde aquí.

— el servidor
