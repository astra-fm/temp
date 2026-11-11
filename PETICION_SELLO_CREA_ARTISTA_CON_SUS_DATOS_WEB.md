# Petición · El sello crea al artista con sus datos, sin «de qué os conocemos»

**De:** el front de la web · **Para:** servidor · **Fecha:** 11.11.26 · Decisión de Pablo del 11.11.26.

Gracias por «sin esperar a la redacción»: ya está montado. Pablo lo ha probado y pide dos cambios más.

## 1 · Fuera `relacion`

«De qué os conocemos» no hace falta. Que `POST /cuentas/roster/proponer` no la exija (si llega, guardadla como hasta
ahora).

## 2 · Crear al artista con sus datos, como el perfil de un artista

Añadir un artista deja de ser un formulario de propuesta: el sello rellena la **misma ficha que tiene un artista en su
página** y lo crea. Hoy no se puede, porque un artista sin página no admite datos (`ficha/proponer` → `409 sin-ficha`).

Lo que necesitamos:

1. Que `roster/proponer` acepte, además de `nombre*`, los datos de la página: `ciudad`, `pais`, `bio`, `foto` (enlace),
   `enlaces {spotify, bandcamp, youtube, soundcloud, instagram, web}` y `conciertos`, con las mismas reglas que
   `ficha/proponer`. Y que devuelva el `id` del item nuevo, para abrir su gestión sin buscarlo por nombre.
2. Que `ficha/proponer?artista=` y `ficha/foto?artista=` **acepten a un artista sin página**: lo propuesto queda guardado
   y pasa a la página cuando esta nazca (al validarse su primera música). Hoy es `409 sin-ficha`.
3. Y lo mismo para el **propio artista sin página** en `ficha/proponer` y `ficha/foto`: el diseño le deja rellenar «Tu
   música en la red» y hoy le responde `409 sin-ficha`.
4. `enlace` (el identificativo): ¿puede salir de los datos? Proponemos que deje de ser un campo aparte y que valga
   **cualquiera de los enlaces** (web o una red). Decidnos si tiene que seguir siendo obligatorio y cuál usar.
5. En `GET /roster/:id`, que la `ficha` de un artista sin página traiga lo que se haya propuesto, para que el sello vea
   lo que rellenó (aunque no esté publicado). Con la misma forma que `publicada` y la marca de pendiente nos basta.

Lo demás no cambia: el artista nace `propuesto`, `gestionable`, y se confirma al validar su primera música.

En cuanto contestéis, la web lo conecta. Mientras, montamos la pantalla.

— el front de la web
