# Al servidor · idiomas, la última tanda: nginx para Butaca con prefijo, y dos cambios a lo acordado

04-11-2026 · del front de la web · sigue a `PROPUESTA_TRADUCCIONES_WEB.md` y `RESPUESTA_ERRORES_TRADUCIDOS_WEB.md` §4 y §5

Las tandas 0 a 3 están en producción desde hoy (`a9f4c60`): la web entera, menos legales, Sobre Astra FM, Donar y
Contacto (tanda 4, en marcha), se ve en `/en`, `/ca` y `/fr`, todavía con `noindex` y sin selector. La tanda 5, la
que lo publica, está preparada en develop pero **apagada** hasta que diseño dibuje el selector.

## 1. Lo que os pedimos: nginx para `butaca-og.php` con prefijo

Como quedamos (vuestro §5). Hoy nginx manda a `butaca-og.php` las direcciones `/butaca/videoclip/<slug>` y
`/butaca/secuencia/<slug>`. Necesitamos lo mismo con prefijo de idioma:

```
/en/butaca/videoclip/<slug>    /ca/…    /fr/…
/en/butaca/secuencia/<slug>    /ca/…    /fr/…
```

El PHP ya lo entiende (en develop; sale con el próximo despliegue): saca el idioma del prefijo y devuelve
`og:url` con prefijo, `og:locale`, `<html lang>` y el texto de respaldo en ese idioma. Los datos de la pieza
siguen en español. Mientras no esté la regla, esas direcciones caen en `index.html` y se comparten con las metas
genéricas del sitio: no se rompe nada, solo se ve peor.

**`tv-og.php` no hace falta:** `/tv` ya solo redirige a Butaca y nadie va a compartir `/en/tv/…`.

Una pregunta: ¿el sitemap (`/sitemap.xml` → `sitemap.php`) necesita algo vuestro? Cuando se encienda, cada
página saldrá cuatro veces con sus `hreflang` (812 direcciones hoy en vez de 203). Es el mismo PHP y la misma
dirección; solo crece.

## 2. Cambia lo de la primera visita: no hay aviso, se detecta

Pablo lo ha decidido distinto de lo que propusimos: **la web detecta el idioma del navegador y redirige**, sin
aviso. Para no perder la versión española en Google:

- solo al entrar por una dirección sin prefijo (un enlace a `/en/…` se respeta);
- solo la primera vez: lo detectado se guarda en el navegador y desde ahí manda eso, o lo que se elija en el
  selector;
- **nunca a un buscador** (Googlebot renderiza en `en-US`; por agente de usuario y `navigator.webdriver`).

## 3. Cambia el evento `idioma_elegido`

Por lo anterior, `desde` ya no tendrá `"aviso"`. Quedan **`"selector"`** y **`"deteccion"`** (la redirección de
la primera visita). Lo demás, como acordamos: el prefijo sale de la dirección en `limpiar` y el idioma va en
`umami.identify` con `tipo_cuenta`. Estas dos ya salen con el próximo despliegue.

— el front de la web
