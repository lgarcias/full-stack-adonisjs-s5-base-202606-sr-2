# Parte A - Revisión de tu backlog de historias de usuario
## ¿Siguen teniendo sentido las historias tal como las generaste? ¿El alcance sigue ceñido al MVP del PRD, o se coló alguna que la IA "inventó" fuera de scope?

En general si, creo que la IA hizo un buen trabajo al crear las historias. Eso si, se colaron muchos criterios de aceptación que realmente no estaban en el PRD. Supongo que el prompt, al dar la posibilidad de crear criterios y marcarlos como (asumido) provoca este efecto.

## ¿Hay historias cuyos criterios de aceptación ahora ves incompletos o poco verificables?

Varios:

- US-10 (Ordenación)

El criterio "las tareas más relevantes para hoy" no es verificable.

El propio PRD dice que el criterio exacto se decidirá durante el refinamiento. Eso significa que realmente esta historia no está lista para desarrollo.


- US-15 y US-16 (Sincronización)

Son demasiado grandes:

creación de eventos
actualización
borrado
completado
reintentos
manejo de errores

Cada uno tiene criterios de aceptación propios y dependencias diferentes, las dividiría en varias historias más pequeñas.

- US-13 (Conectar Google)

Faltan criterios relacionados con:

usuario ya conectado
expiración de autorización
cancelación del flujo OAuth

Aunque parte de ello pueda decidirse posteriormente. Aqui el poke-hole encontró muchos posibles añadidos para la User Story.

- US-05

Incluye el requisito de rendimiento (<1 segundo). Es un requisito no funcional que afecta al sistema completo.

## ¿Hay historias que han cambiado de naturaleza desde entonces? (porque descubriste una dependencia, porque la spec evolucionó, porque entiendes mejor el dominio).

Sí. La principal es toda la sincronización con Google Calendar.

Leyendo el PRD rápidamente parece una única funcionalidad: "sincronizar con Google Calendar"

Además, el propio PRD introduce una nota muy importante: la sincronización debe validarse mediante un spike técnico.
Eso indica que parte del comportamiento todavía es una hipótesis y no un requisito comprometido del MVP.

También ha cambiado la percepción sobre la historia de ordenación: ahora es evidente que depende de una decisión funcional que todavía no existe.

## ¿Hay historias nuevas que no aparecieron cuando lo generaste y que ahora sí deberían estar?

Sí, aunque no necesariamente como User Stories de producto.
Con la experiencia adquirida ahora introduciría elementos que antes probablemente habría pasado por alto:

- un Spike técnico para validar la sincronización con Google Calendar, ya que el propio PRD lo recomienda explícitamente.

Supongo no todo lo que aparece en un PRD debe convertirse automáticamente en una User Story.

## Ajustes al backlog

- Ajuste: Reducir lo que se asume en los criterios de aceptación y eliminar aquellas que introducen decisiones de diseño o UX no descritas en el PRD.

- Motivo: mantener el backlog alineado con el alcance del PRD. Las decisiones de interfaz o implementación deben surgir durante el refinamiento o documentarse en las especificaciones, no asumirse al generar las User Stories.
---
- Ajuste: Dividir las historias demasiado grandes en historias más pequeñas e independientes (conexión OAuth, creación de eventos, actualización, eliminación, gestión de errores, etc.).

- Motivo: es mejor crear historias verticales y con un alcance menor, lo que facilita su desarrollo y tests
---
- Ajuste: Incorporar Spikes para investigación e incertidumbre técnica, incorporando un Spike para la sincronización con Google Calendar antes de descomponer completamente esa funcionalidad.

- Motivo: no toda la incertidumbre debe resolverse mediante User Stories. Cuando el propio PRD identifica un riesgo técnico importante, resulta más apropiado planificar primero un trabajo de investigación.
---
