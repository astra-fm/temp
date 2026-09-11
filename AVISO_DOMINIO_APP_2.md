# Aviso a la app · esto ya no es preparar: el dominio viejo se retira

11-sep-2026 · del servidor · **corrige el `AVISO_DOMINIO_APP.md` de esta mañana**

## Lo que cambia

Esta mañana os dijimos «los dos dominios conviven meses, no cambiéis el valor todavía, que
viaje con una versión ya prevista». **Eso ha cambiado el mismo día.**

**`listen.astra.fm` queda deprecado y el dominio se dará de baja.** Siguen conviviendo, y hoy
no se rompe nada, pero hay que mudarse.

## Por qué la app es el punto más delicado de todo el cambio

Sin actualizaciones por aire, **la app instalada hoy no se entera de nada**. Pide todo al
dominio viejo, y el día que ese dominio no responda no degrada: deja de funcionar, y quien la
tenga solo ve una app rota.

Esa es la razón de que la app mande sobre el calendario de todo lo demás. La retirada del
dominio **no puede ir por delante** de:

1. una versión con el host nuevo publicada en las dos tiendas,
2. su revisión, que no depende de nosotros,
3. y un tiempo de adopción razonable.

Es lo más lento de la lista, así que es lo que hay que empezar antes.

## Lo que pedimos

**1. Cambiad el valor al dominio nuevo**, `https://listen.dynamo.fm`. Si ya centralizasteis la
base en una constante, es una línea. Todo está verificado por el host nuevo: colecciones,
ficha de artista, los dos perfiles de HLS y el now playing.

**2. Publicad en cuanto podáis**, sin esperar a juntarlo con otra cosa. Antes decíamos lo
contrario; con la baja del dominio encima, el coste de esperar es mayor que el de publicar.

**3. Decidnos cuándo esté en las tiendas y cuánta adopción lleváis.** De eso depende cuándo se
puede retirar el dominio viejo, y sin ese dato no se puede fijar la fecha.

**4. Enlaces universales**: si se añaden, que declaren el dominio nuevo. El viejo ya no merece
una revisión de tienda.

**Lo que no cambia:** las colecciones por `http://159.89.111.18:8080` siguen igual, van por IP
y su excepción en `app.json` no se toca.

## Una pregunta

¿Tiene sentido para vosotros añadir un aviso de versión mínima, de esos que piden actualizar
antes de dejar entrar? Es la única red que existe para quien no actualiza nunca. Si lo veis
caro, decidlo y buscamos otra cosa.

— el servidor
