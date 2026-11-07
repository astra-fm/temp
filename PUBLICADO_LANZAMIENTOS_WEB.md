# Al servidor · lanzamientos: la web ya pinta el contrato

7-nov-2026 · del front de la web · contesta a `CONTRATO_LANZAMIENTOS_WEB.md`

**Está en producción** (despliegue `f8b8f6a`, 7-nov-2026 por la tarde). Seguimos el contrato tal cual, sin rehacer
ninguna regla.

## La pestaña Radio → Lanzamientos (§2)

- Pinta `radio` y nada más: los siete periodos en el orden en que llegan, con su `n`. Con `n: 0` el periodo va apagado y
  no se abre. Se abre `porDefecto`; si viniera `null`, el primero con algo.
- Cada periodo enseña `meses[clave]` o `proximamente` tal cual llegan, sin reordenar. El destacado va arriba y no se
  repite.
- **Rótulo por `clase`**: «Álbum», «EP», «Single», «Adelanto». Con `null`, el de `tipo`.
- **La fecha que se enseña es `salida`**: «24 sep». Si llega `AAAA-MM`, «sep 2026». Si `anio` no es el de `hoy`, solo
  el año. `publicado` no se enseña nunca.
- **Adelantos**: el título es `cancion` y debajo «Del álbum «…» · Sale el 13 nov» (o «Del EP»), con `anuncia`. **Con
  `anuncia.fecha: null`, solo «Del álbum «…»»**, sin fecha.
- Toda tarjeta lleva a `/lanzamientos/<destino>`. Las tarjetas no enlazan a Spotify.

## El carrusel de la portada (§3)

- `nuevos[]`, once como máximo, con el rótulo por `clase` y el chip ADELANTO en los adelantos.
- Mientras `nuevos[]` llegue vacío, caemos a lo de antes: los once últimos de `discos[]` sin los «Próximamente». En
  cuanto Pablo marque alguno, el carrusel pasa a ser solo los marcados.

## La página del disco y los posts (§4)

- **Escuchar, solo con Spotify**: el reproductor oficial con la API del iframe, ya cargado al entrar. Un single va en el
  mini de 80; un EP o un álbum, en el grande con la lista. Sin `spotify`, no hay reproductor, solo la lista sin play.
- **Cookies**: con «Aceptar todas» se carga solo; con «Solo necesarias», una caja con «Cargar el reproductor».
- **Una sola fuente de audio**: si Spotify suena, la radio se calla; si suena la radio, Spotify se pausa.
- **Comprar** con `comprar`.
- **La página de un adelanto** dice «Del álbum «…» · Sale el …» con `anuncia`. Si es la de un disco por salir con
  `adelantos[]`, se escucha el Spotify del adelanto.
- **Posts de Lanzamientos**: el antetítulo dice «Lanzamientos · Álbum» con `clase`, y el reproductor va al final del
  cuerpo con `spotify` (mini en single y adelanto, grande en EP y álbum). Sin `clase` ni `spotify`, «Lanzamientos» a
  secas y sin reproductor.

## Cómo lo hemos probado

Con los datos de hoy, la pestaña abre en octubre (5) y apaga noviembre y Próximamente. Lo que hoy no llega se ha probado
con respuestas simuladas: una `salida` sin día, un adelanto con `anuncia.fecha: null`, la página de un adelanto con
`anuncia` y un post con `clase` y `spotify`.

## Lo que vemos hoy en los datos

- Ningún post de `actualidad.json` trae todavía `clase` ni `spotify`.
- `radio.destacado` llega `null` y `proximamente` vacío. `nuevos[]` también vacío.

— el front de la web
