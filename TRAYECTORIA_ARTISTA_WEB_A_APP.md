# A la app · cerremos la trayectoria del artista entre nosotros

16-sep-2026 · del front de la web · con copia al servidor

Pablo nos pide que coordinemos esto y lo cerremos los dos, sin subírselo. Va una propuesta cerrada:
si os vale, contestad «vale» y lo implantamos cada uno; si no, decid qué cambiaríais.

**Lo ya cerrado, que no se toca:** foto y bio, la ficha manda dato a dato (§2.1). En producción en la
web desde ayer.

## El cabo suelto

La **trayectoria** —origen, años, género, estilo, sello, miembros— quedó fuera de la decisión con un
«seguid como la tenéis». En la web esos datos salen de TheAudioDB, salvo los años, que salen de
`musicbrainz` cuando vienen. Y ahí sigue vivo el mismo fallo que arreglamos en la foto: **con un
homónimo, TheAudioDB describe a otro artista.** ASTRO es el caso: si cogemos sus datos, la banda
chilena sale con «Género: K-Pop» y «Miembros: 6».

Hoy la web lo esquiva con un apaño: si la ficha tiene `priority` y bio, no consulta TheAudioDB. Pero
el servidor ha quitado esa casilla del Studio y nos pide ignorar el campo, así que el apaño se queda
sin base. Y os preguntamos en `ALINEACION_ARTISTA_WEB_A_APP.md` si vosotros enseñáis esos campos, que
es lo único que nos falta saber.

## La propuesta

La misma regla de la foto y la bio, dato a dato, más un control:

1. **Origen y años: de la ficha** (`musicbrainz.pais` y `formado`/`fin`), con `desambiguacion` al
   lado. El servidor los tiene para 2.287 artistas, tengan o no `priority`.
2. **Género, estilo, sello y miembros: de TheAudioDB**, porque el servidor no los tiene…
3. **…solo si su país coincide con el de la ficha.** Si no coincide, es otro artista y no se coge
   nada suyo. Con ASTRO: ficha `CL`, TheAudioDB «Seul» → fuera.
4. **`priority` desaparece** de nuestro código, como pide el servidor.
5. **Sin bloque `musicbrainz`** (unos 400 artistas) no hay con qué comparar: se sigue como hoy.

Detalle del control: `pais` viene en ISO (`CL`, `GB`, `US`) y TheAudioDB manda texto libre
(«Seul», «Brighton, England», «Baltimore, Maryland, USA»). Comparamos con el nombre del país en
español y en inglés más los alias obvios (England/Scotland/Wales → GB; USA/US/America → US). Si el
país de TheAudioDB viene vacío, no descartamos nada: sin dato no hay contradicción.

## Lo que os pedimos

1. **¿Enseñáis género, sello o miembros de TheAudioDB?** Si no los enseñáis, esto es solo nuestro y
   lo implantamos igual, para que las dos digamos lo mismo el día que los enseñéis.
2. **¿Os vale el control del país**, o tenéis otra forma de saber que TheAudioDB habla del artista
   correcto?

## Y una petición conjunta al servidor, si estáis de acuerdo

Que rellenen **`genero` en la ficha**: el campo existe y está vacío en todas las que hemos mirado. El
día que lo tengan, la trayectoria sale entera de la ficha y ninguno de los dos necesita TheAudioDB
para esto. Si decís que sí, lo pedimos firmado por los dos.

— el front de la web
