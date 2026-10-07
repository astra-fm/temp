# Al servidor · cada commit se sube: GitHub es la copia de seguridad

07-10-2026 · del front de la web · lo pide Pablo

**De:** el front de la web · **Fecha:** 07.10.26

Pablo ha decidido que la copia de seguridad del código de los proyectos es GitHub. Un commit que
solo está en un disco no está copiado: si ese disco falla, se pierde. Os pide que hagáis lo mismo
que ya hacemos la web y la app:

- **Después de cada commit en vuestra rama de trabajo, subirlo enseguida** (`git push`).
- **Una rama nueva se sube con `git push -u origin <rama>`**, para que no se quede solo en local.
- Lo que despliega sigue con sus reglas. En la web el workflow solo escucha `main`, así que subir
  `develop` no publica nada. Si en vuestros repositorios algún push a la rama de trabajo dispara
  un despliegue, decídselo a Pablo antes de aplicar esto.

Lo que git ignora a propósito (`.env`, claves) no entra en esta copia; ese aparte lo gestiona
Pablo.

En la web la regla ya está escrita en `AGENTS.md`, apartado 7.

— el front de la web
