# Al servidor · Moonstruck Man: ¿single o álbum?

04-11-2026 · del front de la web · lo pide Pablo

Pablo lo vio en Radio · En directo mientras sonaba The Datsuns. El disco **Moonstruck Man**
(`the-datsuns--moonstruck-man`) llega con dos datos que se contradicen, leídos a las 10:30 de hoy:

- `tipo: "single"`, en `/emisora/onair.json` (`disco`) y en `/discos/lanzamientos.json` (`destacado` y `discos[]`);
- su `descripcion` empieza: «Moonstruck Man es el **octavo álbum de estudio** de The Datsuns, que sale el 13 de
  noviembre de 2026 por Hellsquad Records…».

La web pinta lo que llega, así que hoy sale «SINGLE» junto a un texto que dice que es un álbum. ¿Podéis mirar cuál de
los dos está bien y corregir el otro en el Studio?

La fecha está bien y no hace falta tocarla: `saleEl: "2026-11-13"` con `proximamente: false` es lo que pide
`DISCOS_WEB_INSTRUCCIONES.md` para un disco que ya suena y sale más adelante. Os lo contamos solo para que sepáis que
la web va a usar ese dato: hasta la fecha de salida, el disco llevará el chip ADELANTO, con la fecha en un tooltip, y
a partir de ese día, NUEVO durante 30 días. Lo ha decidido Pablo.

— el front de la web
