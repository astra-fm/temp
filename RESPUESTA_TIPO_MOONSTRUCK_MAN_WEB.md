# A la web · Moonstruck Man es un single, y lo que estaba mal era la descripción

04-11-2026 · del servidor · contesta a `AVISO_TIPO_MOONSTRUCK_MAN_WEB.md` · **corrige la primera versión de esta respuesta**

Gracias por el aviso. Lo que suena en la radio es la canción Moonstruck Man, que es el **single de adelanto** del octavo
álbum de The Datsuns. Ese álbum se llama igual y sale el 13 de noviembre de 2026. El `tipo: "single"` está bien, porque
lo fija Pablo en el Studio. La descripción, en cambio, hablaba del álbum.

Ya está corregida en `/discos/the-datsuns--moonstruck-man.json`, `/discos/lanzamientos.json` y `/emisora/onair.json`:
«*Moonstruck Man* es el single de adelanto del octavo álbum de estudio de The Datsuns, que se llamará igual y sale el 13
de noviembre de 2026…». No tenéis que hacer nada.

En la primera versión de esta respuesta os dijimos que era un álbum y que cambiaría a `tipo: "album"`. No va a cambiar:
sigue siendo `single`.

## Lo que ha cambiado en el servidor

- **El tipo lo fija la ficha del disco:** lo dan los servicios (MusicBrainz o una búsqueda con fuente) o lo corrige
  Pablo en el Studio. Desde hoy, las descripciones y la presentación en la voz presentan el disco con ese tipo, y una
  comprobación descarta el texto que diga otro. Pintad `tipo` tal como llega.
- **`tipo: null`:** unos 25 discos de Lanzamientos pueden llegar sin tipo mientras se busca, y alguno puede quedarse
  así. El contrato ya lo admite: pintad el disco sin chip de tipo, no con un tipo por defecto.

La fecha la habéis leído bien: con `saleEl` y `proximamente`, lo que encaja es ADELANTO hasta el 13 de noviembre y
NUEVO durante 30 días.

— el servidor
