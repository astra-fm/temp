# A la web · Butaca: endpoint nuevo de secuencias, ya en producción

21-09-2026, del servidor. No responde a nada vuestro: es un encargo de Pablo. Contrato de
referencia, en vivo y sin copias: `https://listen.astra.fm/docs/SECUENCIAS_WEB_INSTRUCCIONES.md`.

> **Corregido el mismo día, antes de que empezarais:** el endpoint ya **no trae `published_at` ni
> `destacado`**. La destacada es simplemente **la primera del array** (`secuencias[0]`), que es la
> última que ha publicado la radio, y el orden lo da el alta. Si algún día hace falta destacar otra
> cosa, se habla y se añade. El contrato del portal ya está actualizado (rev. 2).

## Lo que pasa

La sección de vídeo pasa a llamarse **Butaca** y tiene dos tipos de contenido. Los **videoclips**
son los de siempre y **no cambian nada**: `tv.json` y su contrato siguen igual, palabra por palabra.
Lo nuevo son las **secuencias**: fragmentos de películas, series o documentales donde la música es
la protagonista, con su ficha de la película y el recorte del trozo.

Van en un **endpoint aparte**, a propósito, para no tocaros lo que ya tenéis integrado:

```
https://listen.astra.fm/secuencias.json
```

## Lo que ya hemos comprobado (medido hoy, no supuesto)

- `GET https://listen.astra.fm/secuencias.json` → **200**, `content-type: application/json`,
  `access-control-allow-origin: *`, `cache-control: public, max-age=0, must-revalidate` y `ETag`
  (revalida a 304). Mismas cabeceras que `/tv.json`.
- Dentro hay **5 secuencias publicadas** ahora mismo, cargadas por la radio: The Cure en *The Crow*,
  The Velvet Underground en *Perfect Days*, Pixies en *Fight Club*, Tears for Fears en *Donnie
  Darko* y George Harrison en *Weapons*.
- **`tv.json` no ha cambiado**: comparados los 44 vídeos campo a campo antes y después del cambio,
  son idénticos. Los borradores no salen del servidor, así que no tenéis que filtrar nada.
- `avisos.json` ya está sirviendo el tipo nuevo: 5 avisos con `tipo: "secuencia"` y
  `destino: {pantalla: "secuencia", params: {secuenciaId}}`.
- El JSON de hoy trae 27 campos por secuencia; **`published_at` y `destacado` no están** (ver la
  corrección de arriba).
- Os damos el vídeo listo para embeber: cada secuencia trae `youtube_id` y **`embed_url` ya montada
  con el recorte** (`start`/`end`), además de `start_seconds`/`end_seconds` sueltos por si preferís
  construirla vosotros. No hace falta parsear la URL de YouTube.

## Lo que os pedimos

1. **Vuestra ruta de la sección y el nombre del parámetro** del deep-link, por ejemplo
   `https://astra.fm/butaca?secuencia=<id>`. Es lo único que bloquea que la radio pueda publicar
   secuencias en Facebook y Bluesky: mientras no la tengamos, no publicamos, porque el post
   llevaría a ninguna parte.
2. **`og:` por secuencia en esa ruta**, resueltos en servidor o edge (el crawler de Facebook no
   ejecuta JavaScript). Facebook **fija la tarjeta al publicar** y luego no hay arreglo: sin esto,
   el post sale con el logo genérico para siempre. Los campos, en el §4.2 del contrato.
3. **El `og-check` equivalente al de TV.** Para los vídeos servís
   `astra.fm/tv-og.php?video=<id>&og-check=1` y nuestro dashboard lo consulta **antes** de publicar,
   frenando el post si la web aún no ve el contenido. Para secuencias hace falta el suyo, con la
   misma respuesta (`{listo, encontrado, titulo, imagen, tarjeta}`). Sin él publicaríamos a ciegas.
4. **Confirmadnos que ignoráis un `destino.pantalla` desconocido** en el feed de avisos. El contrato
   siempre lo ha dicho, pero hasta hoy nunca había aparecido uno: ahora sí, y queremos saber que no
   os rompe nada mientras no tengáis la pantalla.

Y una pregunta sin prisa: si algún día preferís **un solo fichero** con los dos tipos en vez de
`tv.json` + `secuencias.json`, decidlo y lo preparamos. Hoy son dos por no tocaros nada.

— el servidor
