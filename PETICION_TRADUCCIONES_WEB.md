# A la web · la web en cuatro idiomas: español, inglés, catalán y francés

04-11-2026 · del servidor · decisión de Pablo

La web pasa a tener cuatro idiomas: **español** (el de siempre y el de referencia), **inglés**, **catalán** y
**francés**. Esta primera fase traduce solo **lo estático**: lo que está escrito en el código de la web. Lo dinámico
sigue en español de momento.

## Qué se traduce y qué no

**Se traduce:**
- toda la interfaz: menús, botones, títulos de sección, formularios, avisos, estados vacíos, el área de cuenta y el
  alta;
- las páginas legales (privacidad, términos, cookies y contacto) y la de Sobre Astra FM;
- los títulos y descripciones para buscadores y redes (`<title>`, `description`, Open Graph).

**No se traduce de momento** (sale en español en los cuatro idiomas):
- las biografías y fichas de artista;
- Actualidad, Radar, Butaca, conciertos y lanzamientos: todo lo que llega de los JSON del servidor;
- los textos de los programas (`programas.json`) y los nombres de los géneros. Si el servidor los traduce más adelante,
  os avisará con el cambio del contrato.

**Los nombres propios no se traducen:** Astra FM y los nombres de los programas.

**Pendiente de Pablo:** si se traducen también los nombres de las secciones, como Radar, Butaca o Actualidad.
Preguntadle antes de decidirlo.

## Quién traduce

**Claude, como hasta ahora**, con el texto en español delante. Nada de traductores automáticos, como DeepL o Google
Translate, ni de servicios de pago por palabra. Si hay que cambiar un texto, se cambia en español y se traduce a los
otros tres en el mismo commit, para que ningún idioma se quede atrás.

## Lo que hemos medido

En el repositorio de la web, el 4-nov, hay unos **60.000 a 70.000 caracteres** de texto fijo:
- unos 43.000 en las plantillas de los 215 ficheros de `src/`, con las páginas legales dentro, que suman unos 20.000;
- unos 10.000 en mensajes que se enseñan desde el código (`script`).

Es una medida aproximada, sacada con un script. No cuenta los `console.log` ni los comentarios.

## El coste

Traducir esos 60.000 a 70.000 caracteres es poco: menos de medio punto del cupo semanal de Claude por idioma. Lo caro
es preparar la web para tener idiomas: sacar cada texto del código a ficheros de idioma, recorrer los 215 ficheros,
poner el selector y las rutas, y revisar.

Por eso, **hacedlo por tandas y no en una sola sesión larga.** El cupo es compartido con la radio, y las emisiones
dependen de él. Antes de cada tanda grande, preguntad a Pablo cómo va el cupo: lo ve en
Studio → Costes, y desde fuera del Studio no se puede consultar. Si pasa del 80 %, esperad al reinicio.

## Lo que pedimos antes de empezar

Una **propuesta** en este repositorio (`PROPUESTA_TRADUCCIONES_WEB.md`) con:
1. **Cómo guardaréis los textos:** la librería, si la hay, o vuestra solución, y la estructura de los ficheros de
   idioma.
2. **Cómo se elige el idioma:** rutas con prefijo, como `/en/…`, `/ca/…` o `/fr/…`, con el español sin prefijo; la
   primera visita según el idioma del navegador; el selector visible, y dónde se recuerda la elección. Sugerimos el
   prefijo en la ruta, con `hreflang` y `<html lang>`, porque es lo que necesitan los buscadores para indexar cada
   idioma. Si lo veis distinto, explicad por qué.
3. **Qué pasa con lo dinámico:** cómo se ve una página en inglés con una bio o un artículo en español, y si conviene
   marcarlo con `lang="es"` en ese bloque.
4. **Los mensajes del servidor que enseñáis al usuario:** errores del alta, del inicio de sesión y de los envíos, y
   estados del área. Haced una lista de los que pintáis tal cual: el servidor decidirá si os manda un código para que
   los traduzcáis vosotros o el texto ya traducido según `Accept-Language`. No los traduzcáis por vuestra cuenta
   hasta que respondamos.
5. **Las tandas, en orden,** con lo que entra en cada una.
6. **La analítica:** si Umami debe distinguir el idioma de la visita. Los nombres de los eventos no cambian, porque
   la radio los lee en Stats.

Con la propuesta aprobada por Pablo, adelante con la primera tanda.

## Las páginas legales

Las traducciones de privacidad, términos y cookies son **informativas**: si hay diferencias, vale la versión en
español. Que cada una lo diga en una línea arriba, en su idioma.

— el servidor
