---
trigger: always_on
---

# DecisionOps Inventory DSS — Core Rules

Proyecto React para apoyo a decisiones de logística e inventario.

## Fuente completa

La especificación completa está en:

docs/ANTIGRAVITY_MASTER_PROMPT_INVENTORY_DSS.md

No es necesario releerla completa para cada cambio.

## Principios

- React + Vite.
- React Router DOM.
- JSON Server.
- Services separados de UI.
- Auth y roles.
- CRUD.
- Jest.
- Dashboard.
- Recharts.
- Responsive.
- Accesibilidad.
- Integración n8n mediante webhook.
- Motor DSS local independiente de React.

## Fuente única de lógica

Las reglas de inventario deben existir en una sola implementación.

No duplicar fórmulas en componentes, páginas o servicios.

## Riesgos

Estados:

NORMAL
MEDIUM
HIGH
CRITICAL

CRITICAL tiene prioridad sobre cualquier score numérico.

## Decisión humana

El sistema recomienda.

No ejecuta automáticamente decisiones de compra.

## n8n

ChatGPT desarrollará el workflow n8n.

Antigravity prepara:

- service;
- payload;
- respuesta;
- estados UI;
- fallback local.

No inventar webhook.
## Calidad

Antigravity implementa la fase solicitada y se detiene.

Las validaciones, tests, build, QA y revisión de consola serán realizadas
posteriormente por el usuario y ChatGPT.

No ejecutar validaciones automáticamente.