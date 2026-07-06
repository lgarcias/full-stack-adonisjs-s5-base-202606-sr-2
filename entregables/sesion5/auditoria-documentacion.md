# Auditoría de documentación — full-stack-adonisjs-master

> Auditoría realizada con Claude Code sobre el estado del repositorio en la rama
> `alumno/luis-garcia`. `docs/NOTAS-FORMADOR.md` se excluyó explícitamente del
> alcance por ser material de solución del ejercicio no destinado al alumnado.

## Repository Summary

Monorepo docente (máster AI4Devs): **backend** AdonisJS 7 + TypeScript + Lucid/SQLite +
VineJS + `@adonisjs/auth` (access tokens), **frontend** React 19 + Vite + React Router.
Documentación en capas: PRD de producto (**FlowSync**, gestor de tareas + sync Google
Calendar) → OpenSpec (`authentication`, `users`) → código. El código implementado cubre
solo autenticación y directorio de usuarios; el resto del alcance del PRD (tareas,
filtrado, exportación, Google Calendar) es, correctamente, trabajo aún no iniciado — no
se reporta como hallazgo, según la jerarquía de validación.

## Documentation Inventory

| Document | Status | Location |
|---|---|---|
| README raíz | Partial (pasos de arranque no reproducibles en clon limpio) | `README.md` |
| README backend | Partial | `backend/README.md` |
| README frontend | Missing | — |
| README docs/ | Partial | `docs/README.md` |
| PRD | Complete | `docs/PRD.md` |
| Notas del formador | Not Applicable (excluido a petición del usuario) | `docs/NOTAS-FORMADOR.md` |
| CLAUDE.md | Complete | `CLAUDE.md` |
| OpenSpec config | Complete | `openspec/config.yaml` |
| Spec `authentication` | Complete | `openspec/specs/authentication/spec.md` |
| Spec `users` | Partial | `openspec/specs/users/spec.md` |
| Spec `health` | Missing (referenciado pero inexistente) | — |
| ADRs | Not Applicable (aún no planificadas, ver `docs/README.md:8`) | — |
| Architecture / C4 | Missing | — |
| OpenAPI / Postman | Missing | — |
| Entregable backlog | Partial | `entregables/sesion5/Análisis del backlog sesion 4.md` |

## Documentation Audit

**README (raíz + backend)**
- Status: Partial (revisado a la baja tras verificación en entorno real)
- Evidence: `README.md:25-32` (arranque rápido del backend), `backend/README.md:12-21`
  (scripts), `backend/package.json` en el commit `a3f40ff` (estado original del repo).
- Findings:
  - `backend/README.md:20` documenta `npm run test` como "Suite de tests (Japa)" pero
    `backend/tests/` solo contiene `bootstrap.ts` — ningún archivo de test real. Esto es
    una instrucción operativa que no refleja el estado actual del código.
  - **Verificado en ejecución real**: siguiendo exactamente los pasos de
    `README.md:25-32` en un clon limpio, `npm run dev` falla porque
    `@poppinss/ts-exec` no está declarado en `backend/package.json` (confirmado con
    `git show a3f40ff:backend/package.json`, que no lo incluye). El script `dev` usa
    `node ace serve --hmr`, que depende de `ts-exec` para el HMR de `@adonisjs/assembler`;
    sin la dependencia declarada, `npm install` no lo instala y el arranque se rompe.
    Bloquea el "camino feliz" documentado del README para cualquiera que clone el repo.
  - **Verificado en ejecución real**: `npm run migration:run` falla en un clon limpio
    porque `backend/tmp/` no existe. `config/database.ts` apunta la conexión SQLite a
    `app.tmpPath('db.sqlite3')`, y `tmp/` está en `.gitignore` (correctamente, no debe
    commitearse el `.sqlite3`), pero el README no indica crear ese directorio antes de
    migrar. Severidad baja (se soluciona con un `mkdir -p backend/tmp`), pero rompe el
    paso a paso tal como está escrito.

**README frontend**
- Status: Missing
- Findings: nada documenta `frontend/src` (estructura de `services/`, `pages/`,
  `components/ui/`), ni la estrategia de sesión (`localStorage`, `authService.ts:5-6`).
  Concretamente, no hay ningún documento que liste las rutas de `App.tsx:9-23`
  (`/login`, `/signup`, `/dashboard`, redirección de `/` y de `ProtectedRoute`), las tres
  páginas existentes (`LoginPage`, `SignupPage`, `DashboardPage`), ni que explique que el
  token y el usuario se guardan sin cifrar en `localStorage` bajo las claves `auth_token`
  y `auth_user` (`authService.ts:5-6`).

**Architecture documentation**
- Status: Missing
- Evidence: `docs/README.md:6` afirma que el PRD incluye "diseño de alto nivel, diagrama
  C4"; `docs/PRD.md` no contiene ninguna sección de arquitectura ni diagrama (secciones
  1-8 cubren visión, usuario, requisitos, stack, riesgos, glosario — nada de C4).
- Findings: contradicción de contenido — `docs/README.md` describe un contenido de
  `docs/PRD.md` que el archivo no tiene. Concretamente, faltan **modelo de datos**,
  **diseño de alto nivel** y **diagrama C4** (este último ni siquiera está esbozado en
  ningún formato); "casos de uso" está solo parcialmente cubierto de forma informal por
  el §3 "Requisitos funcionales".

**API Documentation (OpenAPI)**
- Status: Missing
- Findings: no existe contrato de API formal; los endpoints solo están descritos en
  prosa (README) y en OpenSpec. Concretamente, no hay ni `openapi.yaml`/`.json` ni
  colección Postman en el repo, y ninguno de los 8 endpoints existentes
  (`GET /`, `GET /api/v1/health`, `POST /api/v1/account/register`,
  `POST /api/v1/account/login`, `POST /api/v1/account/logout`,
  `GET /api/v1/account/profile`, `GET /api/v1/users`, `GET /api/v1/users/:id`) tiene
  esquema de request/response, códigos de error o ejemplos machine-readable.

**Code documentation (TSDoc/JSDoc)**
- Status: Complete para su alcance
- Evidence: cada método de controller lleva un docblock con la ruta HTTP y una línea de
  propósito (`users_controller.ts:6-9,15-18`, `access_tokens_controller.ts:8-11,28-31`).
- Findings: consistente y sin comentarios triviales.

**ADRs**
- Status: Not Applicable
- Findings: `docs/README.md:8` declara explícitamente que se añadirán en sesiones
  posteriores; no es una carencia en este snapshot.

**Coding conventions**
- Status: Complete pero triplicada
- Evidence: `CLAUDE.md:41-49`, `backend/README.md:37-41` y `openspec/config.yaml:7-9`
  documentan, en tres archivos distintos, las mismas cuatro convenciones (lógica en
  controllers, VineJS, transformers, subpath imports). Además, "rutas bajo `/api/v1`,
  protegidas con `middleware.auth()`" se repite en `CLAUDE.md:48` y
  `openspec/config.yaml:9` (no en `backend/README.md`). Y la descripción del stack
  técnico (AdonisJS 7 + Lucid + SQLite + VineJS + access tokens / React 19 + Vite +
  React Router + Tailwind v4 + shadcn/ui) se repite en cuatro sitios: `README.md:7-10`,
  `CLAUDE.md:17-26`, `backend/README.md:5-10` y `openspec/config.yaml:4-6`.
- Findings: consistentes entre sí hoy, pero triple/cuádruple punto de mantenimiento.
  Nota aparte: `openspec/config.yaml:10` menciona "Usamos conventional commits", una
  convención que no aparece duplicada en ningún otro documento (ni `CLAUDE.md` ni
  `CONTRIBUTING.md`, que no existe) — es el caso inverso, una convención real que solo
  vive en un sitio y podría pasar desapercibida.

**OpenSpec**
- Status: Partial
- Evidence: `openspec/specs/authentication/spec.md` (3 requirements) vs
  `new_accounts_controller.ts`, `access_tokens_controller.ts`, `profiles_controller.ts`,
  `validators/auth.ts` — sin divergencias. `openspec/specs/users/spec.md:51-77`
  (requirement "Listado de usuarios activos") vs `users_controller.ts:10-22` — sin
  implementación.
- Findings: ver Traceability Findings.

## Traceability Findings

### PRD contradictions

Ninguna encontrada. La única funcionalidad implementada que no aparece en el PRD es el
directorio de usuarios (`GET /api/v1/users`, `GET /api/v1/users/:id`) — no contradice el
PRD, pero merece nota: **el PRD §4 exige que "los datos de un usuario ... nunca son
accesibles por otros usuarios"**, y este endpoint devuelve `email`, `fullName` y
`lastSeenAt` de **todos** los usuarios a cualquier usuario autenticado
(`users_controller.ts:10-13`, `user_transformer.ts:9-17`). El texto del PRD limita la
privacidad explícitamente a "tareas, tokens de Google", por lo que no es una
contradicción textual estricta, pero es una tensión real con la NFR de privacidad si se
traslada literalmente a datos de perfil. No se reporta como incumplimiento del PRD, pero
sí como riesgo a vigilar antes de producción.

### Specification divergence

- `openspec/specs/users/spec.md:51-77` documenta `GET /api/v1/users/active` como
  comportamiento "ya implementado" (`spec.md:9`), pero no existe método `active` en
  `users_controller.ts` ni ruta en `backend/start/routes.ts:34-35`. Divergencia
  spec→código confirmada.
- `health` — el propio código dice haberse creado "mediante el flujo OpenSpec"
  (`health_controller.ts:6-7`, README raíz línea 21) pero no existe
  `openspec/specs/health/` ni ningún spec para ese endpoint. La especificación que
  debería respaldarlo no está en el repo.

### Undocumented implementation

- `GET /` (`backend/start/routes.ts:10-12`) — no está en la tabla de endpoints del
  README ni en ninguna spec.
- Expiración de tokens: 30 días, `tokenSecretLength: 40` (`user.ts:35-41`) — no
  documentado en ningún sitio (solo el prefijo `oat_` aparece en el spec de auth).
- CORS (`backend/config/cors.ts`, dependencia en `package.json:60`) — no mencionado en
  el stack documentado.
- Persistencia de sesión en frontend vía `localStorage` (`authService.ts:5-6`) — sin
  documentar.

### Documentation without implementation

- Únicamente el requirement "Listado de usuarios activos" del spec de `users` (ya
  cubierto arriba como divergencia).

## Cross-Document Findings

### Contradictions

- `openspec/specs/users/spec.md` afirma que `/users/active` está implementado;
  `README.md:57` afirma correctamente que es un pendiente de la Sesión 3. Dos documentos
  del mismo repo se contradicen sobre el mismo endpoint.
- `docs/README.md:6` describe contenido (modelo de datos, diseño de alto nivel, diagrama
  C4) que `docs/PRD.md` no contiene — ver detalle en "Architecture documentation" más
  arriba.

### Duplicate documentation

- Convenciones de arquitectura repetidas en **tres** archivos, no solo dos:
  `CLAUDE.md:41-49`, `backend/README.md:37-41` y `openspec/config.yaml:7-9` —
  consistentes hoy, riesgo de divergencia futura si se edita solo uno. Las cuatro
  convenciones duplicadas casi palabra por palabra en los tres son: lógica de negocio en
  controllers (no en `start/routes.ts`), validación de entrada siempre con VineJS,
  serialización de salida siempre vía transformer, e imports con subpath
  (`#controllers/*`, `#models/*`) en vez de rutas relativas.
- La convención "rutas bajo `/api/v1`, protegidas con `middleware.auth()`" se repite en
  `CLAUDE.md:48` y `openspec/config.yaml:9`, pero está ausente de
  `backend/README.md` — duplicación parcial e inconsistente en su cobertura.
- La descripción del stack técnico completo se repite en **cuatro** documentos:
  `README.md:7-10`, `CLAUDE.md:17-26`, `backend/README.md:5-10` y
  `openspec/config.yaml:4-6` — mismo riesgo de mantenimiento a mayor escala.

### Missing traceability

- El entregable `entregables/sesion5/Análisis del backlog sesion 4.md` referencia
  historias de usuario (US-05, US-10, US-13, US-15, US-16) que no existen como artefacto
  versionado en `docs/` ni `openspec/` — rompe el eslabón PRD → historias de usuario →
  specs.
- No hay ADRs que justifiquen decisiones ya visibles en código (SQLite vs Postgres,
  access tokens vs JWT) — el PRD §5 las prescribe pero ningún documento explica el
  porqué.

## Recommendations

**Alto impacto**
1. Declarar `@poppinss/ts-exec` en `backend/package.json` (devDependencies) — sin él,
   `npm run dev` falla en un clon limpio siguiendo exactamente el README. Es el bloqueador 
   más severo encontrado: rompe el camino feliz documentado para cualquier persona nueva 
   en el proyecto.
2. Añadir al README un paso explícito para crear `backend/tmp/` (o hacer que el propio
   arranque/migración lo cree si no existe) antes de `npm run migration:run`, ya que
   `config/database.ts` apunta ahí y el directorio está gitignored (verificado:
   `migration:run` falla en un clon limpio sin ese directorio).
3. Corregir `openspec/specs/users/spec.md:9` y el requirement de `/users/active`: o se
   implementa, o se elimina la afirmación de que está "ya implementado" para ese
   requirement.
4. Añadir el spec `openspec/specs/health/spec.md` que el propio código y README dicen
   que debería existir.
5. Documentar explícitamente el propósito y las restricciones de `GET /api/v1/users`
   (¿es solo para debug/demo del starter kit? ¿debe eliminarse antes de tratar datos
   reales?), dada la tensión con la NFR de privacidad del PRD §4.

**Impacto medio**
6. Crear `frontend/README.md` con estructura, variables de entorno y estrategia de
   sesión.
7. Corregir `backend/README.md:20` para no afirmar una suite de tests inexistente, o
   añadir tests mínimos de los flujos de auth.
8. Alinear `docs/README.md:6` con el contenido real de `docs/PRD.md` (quitar la
   referencia a diagrama C4/diseño de alto nivel, o añadir esa sección al PRD).
9. Versionar las historias de usuario referenciadas en el entregable de backlog como
   artefacto trazable.

**Impacto bajo**
10. Consolidar las convenciones y el stack técnico, duplicados entre `CLAUDE.md`,
    `backend/README.md` y `openspec/config.yaml` (los tres últimos podrían enlazar a
    `CLAUDE.md` como fuente única en vez de repetir contenido).
11. Añadir `GET /`, CORS y expiración de tokens a la documentación del backend.
12. Abrir la carpeta de ADRs en cuanto se tomen las primeras decisiones registrables
    (SQLite, access tokens), tal como anticipa `docs/README.md`.

## Top 3 Documentation Strengths

1. **Spec de `authentication` totalmente verificable** — lenguaje SHALL + escenarios que
   mapean 1:1 con `new_accounts_controller.ts`, `access_tokens_controller.ts`,
   `profiles_controller.ts`, sin ninguna divergencia encontrada.
2. **Transparencia sobre el estado del proyecto** — el README raíz (línea 57 y sección
   "Para el formador") deja claro qué sesión entregó qué, evitando que se asuma que todo
   el PRD está construido.
3. **Consistencia convención↔código** — "lógica en controllers, no en routes" se cumple
   sin excepción en los 5 controllers auditados; subpath imports (`#controllers/*`,
   `#models/*`) usados de forma uniforme.

## Top 3 Documentation Weaknesses

1. **Los pasos de "Arranque rápido" del README no son reproducibles en un clon
   limpio** — verificado en ejecución real: `npm run dev` falla porque
   `@poppinss/ts-exec` no está declarado en `backend/package.json` (commit `a3f40ff`), y
   `npm run migration:run` falla porque `backend/tmp/` no existe. Es el hallazgo de
   mayor impacto porque afecta directamente al criterio de "¿puede un desarrollador
   nuevo construir el proyecto siguiendo la documentación?".
2. **Spec de `users` se autodescribe como "ya implementada" y no lo está** — el tipo de
   divergencia más peligroso, porque el propio texto reclama estar verificado contra el
   código.
3. **Sin historias de usuario ni ADRs versionados** — se referencian (backlog, PRD §5)
   pero no existen en el repo, rompiendo trazabilidad.

## Exploración de formatos
- C4 Model: permite entender rápidamente la arquitectura y la relación entre los componentes.
- ADR: documenta el motivo de las decisiones técnicas, no solo el resultado.
- OpenAPI: ofrece un contrato preciso y consumible de la API sin necesidad de inspeccionar el código.