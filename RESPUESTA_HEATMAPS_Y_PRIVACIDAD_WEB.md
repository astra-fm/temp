# Al servidor ← del front de la web · El grabador está desplegado. Ya podéis mirar

22-sep-2026 · de la web · responde a `PETICION_HEATMAPS_Y_PRIVACIDAD_WEB.md`

Desplegado hace un momento. Podéis comprobarlo desde ahí: con el 15 % de muestreo tardará un rato en
aparecer tráfico.

## 1 · El grabador

Cargado tal como lo distéis, con `data-website-id`. Dos cosas nuestras encima:

**Va detrás del consentimiento**, como decíais. No arranca solo: solo con la analítica aceptada en el
aviso de cookies.

**Y respeta `umami.disabled`**, la marca que pone `?no-medir=1` para excluir los navegadores del
equipo. ⚠️ **El grabador no la mira por su cuenta** —lo comprobamos leyéndolo entero—, así que la
miramos nosotros antes de cargarlo. Sin eso, vuestras pruebas y las nuestras entrarían en los mapas de
calor y os los ensuciarían justo al principio, que es cuando hay pocos datos.

## 2 · Las rutas excluidas, y por qué hicieron falta dos cosas

Fuera de `/cuenta` y todo lo que cuelga, `/verificar-correo` y `/restablecer-contrasena`.

**No basta con no arrancarlo ahí.** `recorder.js` **no se puede parar**: leído entero, no deja ningún
global propio para detenerlo y no mira `doNotTrack`. Una vez cargado graba hasta agotar su sesión de
cinco minutos. Y esto es una aplicación de una sola página: quien entra por la portada y después va a
su cuenta **seguiría grabándose**, sin recargar en medio.

Así que hacemos las dos: no se arranca en esas rutas, **y además** el contenido principal lleva
`rr-block` mientras se está en ellas. Va en un solo sitio, el `main` de la aplicación, y no vista por
vista.

Comprobado en el navegador, seis casos: sin decidir nada no carga · con «solo las necesarias» no carga
· con «aceptar todas» sí · con `?no-medir` no, aunque haya consentimiento · entrando directamente por
`/cuenta` no arranca · y navegando a `/cuenta` el contenido queda con `rr-block`.

## 3 · Los textos

Puestos **literales**, los dos: el del aviso de cookies dentro de la categoría de analítica, y el
párrafo de la política. Como decís, Pablo los pasa por su gestoría; si vuelven corregidos, mandad la
versión buena y la cambiamos.

## 4 · Y algo que salió mirando esto, por si os afecta

Repasando de dónde colgaba el consentimiento encontramos que **el interruptor de «Cookies Analíticas»
no gobernaba nada**: las etiquetas de Google —Analytics y conversiones de Ads— se cargaban en toda
visita desde el `index.html`, antes de que nadie aceptara, mientras ese interruptor encendía un Google
Analytics con el código de ejemplo `G-XXXXXXXXXX`. Quien pulsaba «Solo las necesarias» creía haber
dicho que no.

Arreglado en el mismo despliegue, por decisión de Pablo: las dos etiquetas se cargan solo al aceptar,
con los códigos de verdad. Contado en el navegador: sin decidir, cero peticiones a Google; con «solo
las necesarias», cero.

**Umami no cambia**: se sigue cargando siempre, sin cookies y sin datos personales, declarado por
interés legítimo en la política. Os lo decimos por si al mirar los números veis una caída en Google y
ninguna en los vuestros: es esto, y es lo esperado.

— el front de la web
