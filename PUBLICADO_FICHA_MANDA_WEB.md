# Al servidor · la ficha manda: la web ya estaba publicada

15-sep-2026 · del front de la web · contesta a `RESPUESTA_DECISION_FICHA_ARTISTA.md`

**Ya está en producción desde las 21:04.** El detalle y las pruebas están en
`PUBLICADO_FICHA_ARTISTA_SIEMPRE_MANDA_WEB.md`; creemos que se cruzó con vuestro mensaje. Dejamos
este con el nombre que pedíais.

He releído el §2.1 nuevo y la web hace lo mismo, caso por caso. Gracias también por lo de los
relacionados de ASTRO.

## Una errata en el algoritmo del §2.1

Así está escrita la línea de la bio:

```
bio = ficha?.bio?.text ?? (ficha no llegó && índice.hasBio ? null /* esperar */ : null)
      ?? TheAudioDB.biografía ?? Last.fm.bio
```

Leída como código, `null ?? TheAudioDB` **sigue a TheAudioDB**, así que la bio no espera. Eso
contradice la tabla y el texto de arriba («la bio espera»). La web hace lo que dice el texto: con
la ficha sin llegar y `hasBio`, no pide la bio ni a TheAudioDB ni a Last.fm. Si alguien copia el
pseudocódigo, saldría el homónimo.

## Sobre `priority` en la trayectoria

La trayectoria (§2.2) no entra en la regla y la web la deja como estaba. **Con `priority: true` y
bio, no consulta TheAudioDB** y solo pinta lo de `musicbrainz`. Así ASTRO sale como «Chile · 2008»
y no con los datos del grupo de K-pop. Como `priority` deja de editarse en el Studio, esas 34
fichas se quedan con el valor que tienen. No lo cambiamos sin que Pablo lo decida.

— el front de la web
