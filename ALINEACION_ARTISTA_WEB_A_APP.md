# A la app · cómo resuelve la web la ficha del artista, para ir alineados

15-sep-2026 · del front de la web · con copia al servidor

**Contexto:** `FALLO_IMAGEN_PRIORITY_WEB.md`, `PUBLICADO_IMAGEN_PRIORITY_WEB.md` y vuestra
`RESPUESTA_PRIORITY_ARTISTA_APP.md`. **Contrato:** `ARTISTAS_WEB_INSTRUCCIONES.md` §2.1, §2.2 y §8.

No os pide nada: es para que sepáis qué enseña la web y dónde no coincidimos.

## Lo que hace la web desde hoy (en producción)

Cada campo se resuelve por separado. El catálogo es un híbrido: hay fichas con foto y texto, y
otras solo con texto.

| Campo | `priority: true` y dato en la ficha | En cualquier otro caso |
|---|---|---|
| **Foto** | La de la ficha, crédito `imageSourceName`. Sin TheAudioDB | TheAudioDB → ficha → carátula |
| **Bio** | La de la ficha. Sin TheAudioDB ni Last.fm | TheAudioDB → Last.fm → ficha |
| **Trayectoria** (origen, años, género, sello, miembros) | No se consulta TheAudioDB: país y años de `musicbrainz`, con su desambiguación | TheAudioDB, y los años de `musicbrainz` si viene |
| **Enlaces** | Los oficiales de la ficha (§7) primero; TheAudioDB y Spotify solo rellenan | Igual |

ASTRO en la web: foto `astro.jpg`, bio de la ficha, «Chile · En activo desde 2008 · Chilean band».

## Dónde no coincidimos

1. **Sin `priority`, el orden es distinto.** En la app manda la ficha siempre. En la web la ficha
   solo rellena lo que falta, que es lo que marca el contrato. Un artista curado sin `priority` con
   datos en TheAudioDB puede tener **otra foto y otra bio en la web que en la app**. No lo cambiamos
   por nuestra cuenta: se lo pasamos a Pablo.
2. **El hueco de la ficha que no llega también está en la web.** Si falla la petición de
   `/artists/<slug>.json`, la web lo trata como «no hay ficha» y va a TheAudioDB. En ASTRO eso
   saldría con la foto de K-pop. Vosotros ya lo habéis cerrado con `index.json`. Lo tenemos
   apuntado y lo decide Pablo.
3. **Trayectoria.** No sabemos si la app enseña datos de TheAudioDB (género, sello, miembros). Si lo
   hace con una bio priorizada, ojo con el homónimo: en la web salían los de otro artista.

## En lo que ya coincidimos

- **Crédito de la foto:** ninguno de los dos lo enseña. Pablo lo ha dejado para más adelante. Si se
  hace, es `imageSourceName` con enlace a `imageSource`.
- **Relacionados de ASTRO:** `/relacionados/astro` devuelve Pentagon, VAV y Shaun, que son de
  K-pop. Se lo hemos dicho al servidor; si la app los pinta, os afecta igual.

— el front de la web
