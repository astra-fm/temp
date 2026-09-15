# Al servidor · urgente: una credencial de la emisora viaja en el código público de la web

15-sep-2026 · del front de la web

⚠️ **Este repositorio es público.** Aquí no va la credencial ni el detalle de dónde está: lo justo
para que actuéis. Si necesitáis más, pedídselo a Pablo por un canal privado.

## Qué pasa

**La clave de API del panel de la emisora está dentro del JavaScript que sirve `astra.fm`.**
Comprobado hoy sobre el paquete publicado. Cualquiera que abra la web puede sacarla desde las
herramientas del navegador.

No es un descuido de un día: la web consulta desde el navegador cuatro endpoints privados de la
estación —cola, listas de reproducción, ficheros de una lista e historial— y para eso la clave va
en el cliente. Es así desde diciembre de 2025.

## Por qué es urgente

Lo que se pueda hacer con ella depende de los permisos de esa clave y de la cuenta a la que
pertenece. **Vosotros sabéis cuáles son; nosotros no.** Si la cuenta tiene permisos de
administración, alguien podría manejar la emisora.

## Lo que os pedimos

1. **Rotarla ya** y dar la actual por comprometida. Revisad en el panel si ha habido uso raro.
2. **Decirnos cómo sacamos la clave del navegador**, porque si solo se rota, la nueva vuelve a
   quedar publicada en el siguiente despliegue. Dos salidas, la que prefiráis:
   - **Endpoints públicos vuestros**, sin clave, que den eso mismo o lo que la web necesite de ello;
   - **o un paso por servidor** que ponga la clave del lado vuestro.

   Nos basta con saber qué consultamos en lugar de esos cuatro. **Hasta tenerlo, no desplegamos una
   clave nueva en la web.**

## Lo que hacemos nosotros

- Retirar del repositorio los ficheros de configuración que llevan credenciales. Es privado, pero no
  deberían estar.
- Avisar a Pablo de que otra clave de un servicio externo, que tampoco debería ir en el cliente,
  también hay que rotarla.

Cuando esté rotada, decidlo aquí sin nombrar la clave.

— el front de la web
