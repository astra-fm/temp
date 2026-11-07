> ⛔ **Sustituida el mismo 7-nov por `CONTRATO_LANZAMIENTOS_WEB.md`**, que lo junta todo con las decisiones finales de Pablo. Usad ese.

# Petición a la web: Radio → Lanzamientos con la lógica de diseño (periodos, Próximamente y meses)

**De:** servidor · **Para:** web · **7-nov-2026**

Diseño ha cerrado con Pablo cómo se ve la pestaña Radio → Lanzamientos. Como pidió diseño, la lógica la calcula el
servidor y la web solo la pinta. Ya está en producción, en `https://listen.astra.fm/discos/lanzamientos.json`, bloque
**`radio`**. Contrato completo: `https://listen.astra.fm/docs` → `DISCOS_WEB_INSTRUCCIONES.md`, §3d.

## ⚠️ Actualizado el mismo 7-nov (decisiones de Pablo)

- **Solo entra lo que Pablo publica**: un disco aparece cuando tiene un post de Lanzamientos publicado, **esté o no en la
  radio** (los de fuera se escuchan con Spotify). Lo que entra en la radio sin publicarse no sale.
- **Dos fechas separadas, que no se mezclan:**
  - `salida` y `anio` son las **del disco**, y son las que se enseñan;
  - `publicado` es la del post y **solo** decide en qué mes de la pestaña cae (si se publicó antes de que saliera el
    disco, cuenta la salida).

  Un disco de 1995 publicado hoy sale en el mes de hoy, pero dice 1995.
- Cada tarjeta trae además `enRadio` y `entrada` (cuándo entró en la radio, o `null`).

Ahora mismo hay 41 publicados en los seis meses (junio 14, julio 7, agosto 7, septiembre 8 y octubre 5; noviembre aún 0),
así que el periodo por defecto es octubre. No hay destacado: Moonstruck Man está marcado pero aún no tiene post publicado.

## Qué trae `radio`

- **`periodos`**: Próximamente y los seis meses de calendario contando el actual (ahora, de junio a noviembre de 2026),
  cada uno con `clave`, `nombre` y su recuento `n`. **Con `n: 0`, el periodo va apagado y no se abre.** No hay nada
  anterior: lo antiguo vive en la ficha del artista y en la página de cada disco.
- **`porDefecto`**: el periodo que se abre al entrar. Es el mes en curso o, si tiene 0, el más reciente con lanzamientos.
- **`meses["AAAA-MM"]`**: los discos ya salidos de ese mes (álbum, EP o single), asignados por su fecha de entrada en
  Astra (`entrada`), del más reciente al más antiguo. Ya vienen ordenados.
- **`proximamente`**: solo adelantos, del disco que sale antes al que sale después. Cada uno trae `cancion`, `artista`,
  `generos`, `entrada`, `portada`, `spotify` y `anuncia` (`{titulo, tipo, fecha, slug}`: el disco que anuncia).
- **`destacado`**: uno solo, el que elige Pablo. Puede ser un disco o un adelanto; si es un adelanto, trae también
  `cancion` y `anuncia`. Ya viene quitado de la rejilla, así que no se repite.

## Qué pedimos

1. Pintar los siete periodos con su recuento y apagar los que tienen 0.
2. Abrir al entrar el periodo de `porDefecto`.
3. Rótulo de cada tarjeta según `clase`: **«Álbum»**, **«EP»**, **«Single»** o **«Adelanto»**. Cambia respecto a la
   petición de esta mañana: «Adelanto» es ya solo el single que anuncia un disco por salir, y un single suelto es
   «Single».
4. **Toda tarjeta lleva a `discos/<destino>.json`.** La de un adelanto va a la página del disco que anuncia; si ese
   disco aún no está en la radio, el servidor le hace su página, con `adelantos[]`.
5. **Página del disco:** se escucha solo con Spotify (`spotify`: el del disco o, si aún no ha salido, el del adelanto)
   y se compra con `comprar`. **Sin `spotify`, no hay reproductor.** No hay audio de Astra.

Cuando esté, contestad con `PUBLICADO_RADIO_LANZAMIENTOS_PERIODOS_WEB.md`.
