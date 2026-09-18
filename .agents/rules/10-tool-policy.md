---
trigger: always_on
---

# DecisionOps Inventory DSS — Agent Tool Policy

## Objetivo

Usar las herramientas disponibles para reducir contexto, evitar búsquedas
innecesarias y realizar cambios pequeños y verificables.

## Serena

Usar Serena para entender y navegar el código cuando el proyecto ya tenga
estructura suficiente.

Preferir Serena para:

- localizar símbolos;
- encontrar componentes;
- encontrar funciones;
- buscar referencias;
- localizar callers;
- analizar relaciones entre archivos;
- identificar dónde implementar un cambio.

No leer archivos completos si Serena puede localizar directamente el símbolo
necesario.

No usar Serena para operaciones triviales donde ya se conoce el archivo.

## RTK

Usar RTK para comandos de terminal siempre que tenga soporte.

Ejemplos:

rtk git status
rtk git diff
rtk npm run build
rtk npm test
rtk ls src

Evitar repetir comandos solo para volver a mostrar información ya disponible.

Usar `rtk proxy` únicamente cuando la salida compactada sea insuficiente.

## Ponytail

Aplicar Ponytail después de entender el problema.

Prioridades:

1. reutilizar código existente;
2. usar funcionalidad nativa;
3. usar dependencias ya instaladas;
4. escribir la mínima cantidad de código necesaria.

No crear abstracciones preventivas.

No agregar dependencias sin necesidad.

No duplicar lógica existente.

## Prioridad de requisitos

Ponytail nunca puede eliminar un requisito obligatorio de FWD.

Si existe conflicto:

1. requisitos académicos;
2. reglas del proyecto;
3. arquitectura existente;
4. Ponytail.

## Estrategia de contexto

Antes de leer muchos archivos:

1. revisar CURRENT_TASK.md;
2. revisar PROJECT_STATUS.md;
3. usar Serena para localizar código relevante;
4. leer solo los archivos necesarios;
5. ejecutar cambios pequeños;
6. validar con RTK;
7. actualizar PROJECT_STATUS.md si cambia la arquitectura.

No volver a leer el Master Prompt completo en cada tarea.

Consultar el Master Prompt solamente cuando:

- comienza una nueva fase;
- existe una duda sobre requisitos;
- una tarea toca una funcionalidad no implementada previamente.

## Cambios

Preferir cambios pequeños y localizados.

No hacer refactors masivos mientras se implementa una funcionalidad diferente.

No modificar archivos fuera del alcance de CURRENT_TASK.md salvo que sea
estrictamente necesario.

Antigravity must NOT perform project validation unless explicitly requested.

Do not automatically run:

- builds;
- tests;
- coverage;
- lint;
- server validation;
- browser QA;
- responsive checks;
- accessibility checks;
- console validation.

Implementation and validation are separate phases.

The user and ChatGPT will perform validation manually after implementation.

Antigravity should implement the requested task, update project status and stop.

Corregir errores antes de continuar.