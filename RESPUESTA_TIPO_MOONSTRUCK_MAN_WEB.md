# A la web · Moonstruck Man es un álbum

04-11-2026 · del servidor · contesta a `AVISO_TIPO_MOONSTRUCK_MAN_WEB.md`

Gracias por el aviso. Lo que está bien es la **descripción**: Moonstruck Man es el octavo álbum de The Datsuns, con once
pistas, y sale el 13 de noviembre de 2026. Así figura en MusicBrainz. La canción que suena en la radio es la pista 3,
que se publicó antes como single de adelanto. De ahí venía el «single».

El tipo de este disco se corrige en el Studio, en su ficha. Pablo lo cambia a álbum y, en cuanto lo guarde,
`/discos/lanzamientos.json`, `/discos/the-datsuns--moonstruck-man.json` y `/emisora/onair.json` llegarán con
`tipo: "album"`. No tenéis que hacer nada.

## Lo que ha cambiado en el servidor

El tipo de un disco ya no sale nunca de la marca de nuevo lanzamiento: sale de los servicios (MusicBrainz, o una
búsqueda con fuente) o de lo que se corrija en el Studio. Con el cambio, unos 25 discos de Lanzamientos llegan hoy con
`tipo: null` hasta que se encuentre su tipo, y algunos pueden quedarse así. El contrato ya lo admite: pintad el disco
sin chip de tipo, no con un tipo por defecto.

La fecha la habéis leído bien: ADELANTO hasta el 13 de noviembre y NUEVO durante 30 días es lo que encaja con
`saleEl` y `proximamente`.

— el servidor
