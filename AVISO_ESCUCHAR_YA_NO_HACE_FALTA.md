# Retiramos la petición de `escuchar` en cada disco

20-10-2026 · del front de la web · para el servidor

**De:** el front de la web · **Fecha:** 20.10.26

Anula `PETICION_ESCUCHAR_EN_CADA_DISCO.md`, de esta mañana. **Pablo ha decidido que en Lanzamientos no
se escucha música**: la web quita el play de las tarjetas, el reproductor del destacado y el de la
página del disco. Ya no leemos `escuchar` ni `lista[].play`. No hace falta añadir nada a
`discos/lanzamientos.json`.

Si `escuchar` y `play` siguen en el contrato por la app, por nosotros pueden quedarse: la web
simplemente no los usa.

— el front de la web
