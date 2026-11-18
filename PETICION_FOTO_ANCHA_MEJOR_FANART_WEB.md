# Petición · `imageWide`: la mejor foto apaisada, no la primera

**De:** el front de la web · **Para:** servidor · **Fecha:** 18.11.26 · Sobre `ARTISTAS_WEB_INSTRUCCIONES.md`, foto apaisada (28-oct-2026).

Pablo ha visto borrosa la foto de Pavement en el hero de Radio. La web pinta `imageWide` tal cual, y la de Pavement es una
foto pequeña ampliada.

## Lo que hemos medido (18.11.26)

`artists/pavement.json` trae `imageWide` del `strArtistFanart` de TheAudioDB. Pero el perfil de Pavement en TheAudioDB
(`search.php?s=pavement`, id 113953) tiene **cuatro** fanart, todas de 1280 × 720:

| Campo | Fichero | Peso | Cómo es |
|---|---|---|---|
| `strArtistFanart` | `fanart/i0iyg01696215603.jpg` | 97 KB | **Ampliada desde una foto pequeña: borrosa y con bloques.** Es la que usáis |
| `strArtistFanart2` | `fanart/btowo11696215779.jpg` | 245 KB | Nítida |
| `strArtistFanart3` | `fanart/ptx6x61696216073.jpg` | 212 KB | Nítida |
| `strArtistFanart4` | `fanart/vcehc71696216086.jpg` | 203 KB | Nítida |

Con la misma medida, el peso delata la ampliación: la primera tiene menos de la mitad de información que las otras.

## Lo que os pido

1. **Elegir `imageWide` entre todas las fanart (`strArtistFanart` a `strArtistFanart4`), no solo la primera.** Una regla
   simple que funcionaría aquí: a igual medida, la que más pesa. Si tenéis otra mejor, usadla.
2. **Que Pablo pueda elegirla a mano en Studio → Artistas**, como la foto cuadrada, para los casos en que ninguna regla
   acierte. Si la elige, manda sobre TheAudioDB, como el resto de la ficha.
3. Repasar con la nueva regla las `imageWide` que ya están guardadas, no solo las nuevas.

En la web no hay que cambiar nada: seguimos pintando `imageWide`. Si añadís algún campo, avisadnos en el contrato.

— el front de la web
