# Propuesta — arquitectura de altas y áreas de usuario en Astra FM

> **Estado: PROPUESTA en discusión (31-ago-2026). Nada implementado.**
> Escrita para decidir el modelo antes de tocar código. Las decisiones abiertas están
> marcadas al final.

## 1. El principio rector: una cuenta, varios perfiles

**No hay tres altas: hay UNA cuenta y hasta tres perfiles que se le acoplan.**

- La **cuenta** es la identidad: email + contraseña + nombre. Única por email.
- Los **perfiles** son papeles que esa cuenta puede tener: `oyente` (todos lo tienen),
  `artista`, `sello`. Se piden desde el alta o después, y los de artista/sello los
  **verifica la redacción** antes de activarse.

Por qué así y no tres registros separados:

1. **Las personas no son excluyentes.** El manager de un sello también escucha la radio;
   un artista también quiere los avisos de lanzamientos. Con tres altas habría emails
   duplicados y tres contraseñas — la libreta de Comunicación ya deduplica por email
   precisamente porque esto pasa.
2. **El CRM ya funciona así.** `comunicacion.json` tiene contactos con categorías
   (usuarios, sellos) sobre la misma estructura; los sellos se capturan solos desde el
   formulario de Canal abierto. La cuenta pública es la cara visible de esa libreta.
3. **Evita decidir en el formulario.** El error clásico es obligar a elegir «¿eres
   artista o sello?» en el registro: la mitad elige mal. Con perfiles, el alta base es
   trivial y el papel se añade cuando hace falta (p. ej. al enviar música).

## 2. El modelo de negocio que esto sostiene

Cada perfil existe porque le da algo a la persona Y algo a la radio:

| Perfil | La persona obtiene | La radio obtiene |
|---|---|---|
| **Oyente** | avisos, preferencias de email autogestionadas, (futuro: favoritos, colecciones sincronizadas) | audiencia identificada, listas de email limpias y consentidas |
| **Artista** | seguimiento de sus envíos (recibido y luego parrilla o escuchado, dos cierres — nunca en cadena), su ficha pública, aviso cuando suena | pipeline de música con datos fiables, fichas editoriales que se corrigen solas, relación directa |
| **Sello** | lo del artista × su roster, un canal formal con la emisora | los sellos son el negocio B2B: contactos con cargo real, historial de envíos, segmentación en campañas |

## 3. Modelo de datos (en nuestro servidor, privado)

`riot/state/cuentas.json` — **PRIVADO, jamás en api/** (como comunicacion.json):

```
cuenta {
  id, email (único, clave), passwordHash (scrypt),
  nombre, dispositivo(s), createdAt, estado (activa|baja),
  consents { novedades, noticias, astraUpdates },   // lo que hoy vive en WP
  perfiles {
    oyente:  {},                                     // implícito
    artista: { estado: pendiente|verificado, artistSlug|null,  // vínculo al índice de artistas
               enviosSlugs: [...] },                 // sus entradas de canal-abierto.json
    sello:   { estado: pendiente|verificado, labelName, web, cargo,
               contactoComunicacionId,               // vínculo a la libreta (ya existe)
               enviosSlugs: [...] }
  }
}
```

Claves del diseño:

- **Vínculos, no duplicados.** El perfil artista apunta al `slug` del índice de artistas;
  el sello, a su contacto de la libreta; los envíos, a `canal-abierto.json`. La cuenta no
  copia nada: enlaza lo que ya existe.
- **Vinculación retroactiva por email.** ⚠️ Corregido 2-sep: el enganche lo dispara
  **verificar el CORREO**, no que la redacción verifique el perfil — al verificar el
  email, la cuenta ve los envíos de Radar hechos con ese correo (también un oyente
  sin perfil). Un sello que lleva meses enviando música se encuentra su historial en
  cuanto verifica el correo.
- **La libreta manda en marketing.** Comunicación sigue siendo el CRM; la cuenta
  sincroniza consents con ella (como hoy se hace con WP).

## 4. El alta: mínima para todos, el papel se reclama después

**Regla confirmada (31-ago): formularios MÍNIMOS.** Nada de formularios extensos como el
actual: en el alta solo lo imprescindible de cada perfil; **el resto se completa después
desde el área**, cuando haga falta y sin bloquear nada.

**Campos del alta, cerrados:**

| Alta | Campos en el formulario | Se completa DESPUÉS (área) |
|---|---|---|
| Oyente | email · contraseña · consentimientos | nombre (opcional), dispositivo lo pone la app |
| + «Soy artista» | + nombre artístico | enlaces, ciudad, propuesta de ficha… |
| + «Somos un sello» | + nombre del sello | web, cargo, persona de contacto, roster… |

**Cómo se discrimina si el alta es igual para todos: después del alta, no en el alta.**
Todo el mundo nace **oyente**. Ser artista/sello es una solicitud sobre la cuenta
(estado `pendiente`) que llega por cuatro vías: (1) paso opcional del propio alta,
(2) botón en el área después, (3) prehecha desde el formulario de Canal abierto,
(4) vinculación directa desde el studio (email ya en la libreta). **`pendiente` no da
nada público**: la cuenta funciona como la de un oyente hasta que la redacción verifica
en el studio — nadie se autoproclama; discrimina la redacción, no el formulario.

El formulario de **Canal abierto es el mejor captador**: al enviar música sin cuenta,
ofrece «crea tu cuenta para seguir este envío» con los datos ya rellenos.

### 4.1 Limpieza por inactividad (regla del 31-ago)

Cuentas sin actividad durante **X tiempo** (propuesta: 12 meses; a fijar por Pablo)
reciben un **email de aviso**: «tu cuenta se eliminará en 30 días — entra para
conservarla». Sin reacción → baja.

- **Actividad** = cualquier login (web o app), envío a Canal abierto, o cambio en el
  área. El clic del email de aviso también cuenta (un «conservar mi cuenta»).
- **La baja es suave primero** (estado `baja`, como en la libreta: restaurable por la
  redacción), y **purga definitiva** pasados otros 30 días (decisión 31-ago: 30, no 90).
- **Excepción a decidir**: una cuenta inactiva pero con consentimientos de email activos
  ¿se elimina? Recomendación: NO borrarla en automático — es audiencia de las campañas
  aunque no entre al área; el aviso de inactividad se le manda igual y decide ella.
- **Borrar la cuenta jamás borra contenido editorial**: la ficha de un artista o los
  envíos históricos de Canal abierto son de la emisora y viven en sus propios ficheros;
  la cuenta solo pierde el vínculo. (Coherente con «un dato, un dueño», §8.4.)
- Implementación natural: cron mensual + el circuito de email de Comunicación.

## 5. El área de cada perfil (web primero; la app hereda los endpoints)

- **Área oyente** (`astra.fm/cuenta`): mis datos, mis suscripciones de email (sustituye
  con el tiempo a /darse-de-baja como gestión fina), borrar cuenta.
- **Área artista**: mis envíos con su estado real (los campos ya existen: `status`,
  `onAir`, `programme`, `firstPlayedAt`), mi ficha pública tal como se ve, botón
  «proponer corrección» (bio/links → cola de revisión en el studio, nunca edición
  directa), nuevo envío con los datos precargados.
- **Área sello**: lo mismo, agrupado por artista del roster + datos de contacto.

**Regla editorial: el área nunca escribe directo en contenido público.**
**Regla de material (31-ago): el área NUNCA sube archivos** — todo material entra solo
por el formulario de Radar (con declaración de derechos y rastro de consentimiento) o
por el admin del studio. Todo lo que
un artista/sello proponga pasa por revisión en el studio (mismo patrón que Canal
abierto: escuchar → decidir).

## 6. Autenticación: dónde viven las cuentas

**Recomendación: migrar la identidad a nuestro servidor** (microservicio :3001, endpoints
públicos vía nginx como los demás), por coherencia con todo lo migrado este mes. WP
quedaría solo como remitente de emails (`mailDelivery: "wordpress"`, ya funciona).

- **Contraseñas**: scrypt local (el esquema `passwordHash` ya está definido en la
  libreta). **Migración transparente de los 26 de WP**: en el primer login, si la cuenta
  no tiene hash local, se verifica contra `astra/v1/app/login` de WP; si WP dice OK, se
  guarda el scrypt local y ese usuario ya no depende de WP. Nadie tiene que resetear nada.
- **Sesiones**: token firmado (HMAC con secreto en config.js, como los tokens de baja).
- **Emails transaccionales** (verificar email, reset): por el circuito de Comunicación
  (WP hoy; cdmon directo si DO desbloquea el SMTP).
- **La app**: sigue con `astra/v1` hasta que exista esto; el cambio será apuntar sus
  llamadas a los endpoints nuevos (misma semántica: register/login/reset).

## 7. Fases realistas

1. **F1 — Identidad**: cuentas.json + register/login/reset/verify en el microservicio +
   migración de los 26 de WP (con el rehash transparente). Sin áreas todavía.
2. **F2 — Área oyente** en la web (la más simple, valida todo el circuito) + contrato
   para el front.
3. **F3 — Perfiles artista/sello**: solicitud, cola de verificación en el studio
   (pestaña nueva o dentro de Comunicación), vinculación retroactiva de envíos.
4. **F4 — Áreas artista/sello** en la web.
5. **F5 — La app** adopta los endpoints y estrena su pantalla de cuenta.

Cada fase es útil por sí sola y ninguna rompe lo que ya funciona.

## 8. Perfiles públicos vs área privada (añadido 31-ago tras confirmar el modelo)

Regla general: **el área privada existe siempre; el perfil público solo cuando aporta al
negocio y siempre curado**. Nada que escriba un usuario se publica sin pasar por el studio.

### 8.1 Oyente — SIN perfil público

- **Público: nada.** Un oyente no es contenido de la web: ni página, ni listado, ni
  nombre indexable. Privacidad por defecto — es además lo coherente con que su libreta
  (Comunicación) sea privada.
- **Área privada**: datos de cuenta, suscripciones de email (las 3 listas,
  autogestionadas), dispositivo/s, borrar cuenta. Futuro: favoritos y colecciones
  sincronizadas (cuando la app lo pida).
- Si algún día hay funciones sociales (comentarios…), se añadiría un **alias opt-in** —
  decisión para ese día, no para hoy.

### 8.2 Artista — su perfil público YA EXISTE: la ficha

- **Público**: la ficha `/radar/artista/<slug>` de la web (bio, imagen, género, enlaces
  oficiales, «Conciertos e Info», relacionados) + dónde suena (programa/s). El perfil
  público de artista NO es una página nueva: es **esta ficha, reclamada por su dueño**.
- Verificado el perfil, la ficha puede lucir un sello discreto «✓ verificado por el
  artista» (decisión de diseño web).
- **Área privada del artista**:
  1. **Mis envíos** (Radar): estado real de cada uno — recibido y luego parrilla o escuchado (dos cierres) —
     en parrilla (programa + primera emisión). Los campos ya existen.
  2. **Mi ficha**: vista previa tal como se publica + «proponer corrección» de bio,
     enlaces oficiales, imagen y conciertos → **cola de revisión en el studio** (nunca
     edición directa; mismo patrón que Canal abierto: escuchar → decidir). Los enlaces
     propuestos por el artista, una vez aprobados, GANAN al barrido automático (ya es la
     regla del campo links: lo manual gana).
  3. **Nuevo envío** con los datos precargados.
- Vínculo cuenta→ficha: por `slug` del índice de artistas, asignado por la redacción al
  verificar. Un artista que aún no suena en Astra puede tener cuenta y perfil pendiente
  **sin ficha** (su área solo muestra envíos) — la ficha nace si su música entra.

### 8.3 Sello — perfil público NUEVO, y es el incentivo B2B

- **Público** (fase posterior, cuando haya sellos verificados que lo justifiquen): página
  `/sello/<slug>` con nombre, web, y **su roster en Astra**: los artistas suyos que
  suenan (enlazando a sus fichas) y sus lanzamientos entrados por Canal abierto. Es el
  escaparate que hace que a un sello le compense darse de alta y enviar música — «mira
  todo lo que te hemos programado» es un argumento comercial de la emisora.
- Qué NO se publica jamás: email, teléfono, cargo, notas de la libreta — eso es CRM
  privado.
- **Área privada del sello**: lo del artista pero agrupado por artista del roster
  (envíos y estados), datos de contacto editables (sincronizan con la libreta), y su
  roster (proponer «este artista es nuestro» → verificación en el studio).

### 8.4 Reglas transversales

- **Curación siempre**: toda propuesta (bio, links, roster, imagen) pasa por cola de
  revisión en el studio. El usuario ve el estado de su propuesta (pendiente/aplicada/
  rechazada) en su área.
- **Un dato, un dueño**: la ficha pública sigue viviendo donde vive (editorial.json +
  servicios externos); el roster/envíos, en canal-abierto.json; el CRM, en la libreta.
  El área lee y propone; el studio decide y escribe.
- **URLs públicas**: `/radar/artista/<slug>` (existe) y `/sello/<slug>` (futura) son de la
  web; el servidor les dará el JSON espejo correspondiente cuando toque (mismo patrón
  de siempre).

### 8.5 Traspaso de representación del sello (hueco detectado por diseño, 31-ago)

La cuenta es de una **persona**; el sello es lo que representa. Si esa persona deja el
sello, la representación debe poder pasar a otra cuenta **sin borrar nada** (el perfil,
el roster y el historial son del sello, no de la persona). Propuesta v1: el traspaso lo
hace **la redacción desde el studio** (re-vincular el perfil de sello a otra cuenta con
email verificado), sin UI de usuario — un caso raro no justifica un flujo self-service
con riesgo de secuestro de cuenta. Pendiente de confirmar.

## 9. Decisiones abiertas (para Pablo)

1. **¿Confirmas el modelo una-cuenta-varios-perfiles?** (la alternativa — tres registros
   separados — la desaconsejo por los motivos del §1).
2. **¿Identidad en nuestro servidor** (recomendado) o seguir sobre WP y construir las
   áreas contra WP? (lo segundo es más rápido a corto, pero contradice toda la migración
   y WP no sabe nada de canal abierto, fichas ni libreta).
3. **¿Verificación de email obligatoria en el alta** (enlace al correo) o alta inmediata
   y verificar solo para artista/sello? (recomiendo lo segundo: menos fricción al oyente,
   rigor donde importa).
4. ¿El área artista puede **proponer** cambios de ficha desde el día uno, o solo ver?
