## Prioridad sobre Ponytail

Las reglas de simplificación y reducción de código de Ponytail
nunca pueden eliminar requisitos académicos obligatorios.

Si existe conflicto entre Ponytail y la rúbrica de FWD,
prevalece la rúbrica de FWD y este archivo.

Ponytail puede simplificar la implementación,
pero no puede eliminar:

- React
- React Router DOM
- JSON Server
- CRUD
- autenticación
- roles
- Services
- Jest
- integración de IA
- N8N
- dashboard
- métricas
- responsive
- accesibilidad
- lógica DSS# Inventory DSS — Project Rules

Este proyecto corresponde al Quiz de Automatización n8n de FWD Academy.

## Fuente principal

Antes de implementar o modificar funcionalidades, leer:

docs/ANTIGRAVITY_MASTER_PROMPT_INVENTORY_DSS.md

## Objetivo

Construir una aplicación React para logística e inventario que permita
administrar productos y evaluar automáticamente el riesgo de quiebre
de stock.

No es únicamente un dashboard visual.

El sistema debe implementar lógica real de negocio.

## Reglas obligatorias

1. No eliminar requisitos académicos.
2. No sobreingenierizar.
3. No agregar dependencias sin necesidad.
4. Reutilizar componentes existentes antes de crear otros.
5. Separar UI, lógica de negocio y acceso a datos.
6. La lógica de inventario debe poder probarse independientemente de React.
7. Mantener compatibilidad con n8n.
8. No implementar dentro de React lo que corresponde al workflow n8n.
9. El frontend debe poder enviar y recibir JSON del webhook n8n.
10. Ejecutar build y pruebas después de cambios importantes.

## Motor DSS

Debe evaluar como mínimo:

- stock actual;
- stock mínimo;
- consumo promedio diario;
- días de cobertura;
- próxima entrega;
- días hasta próxima entrega;
- stock proyectado a fecha de entrega;
- fecha estimada de agotamiento;
- riesgo de stockout;
- retrasos de proveedor;
- cantidad sugerida de reorden.

## Ejemplo obligatorio

El sistema debe poder detectar un escenario como:

- producto disponible actualmente;
- inventario suficiente según stock mínimo;
- agotamiento previsto el viernes;
- siguiente entrega el lunes.

El sistema NO debe clasificarlo como normal.

Debe detectar que existe una ventana sin inventario y elevar el riesgo.

## Clasificación

NORMAL
MEDIO
ALTO
CRITICO

No depender exclusivamente del color para representar estos estados.

## n8n

ChatGPT desarrollará el workflow n8n.

Antigravity debe preparar únicamente:

- services para consumir el webhook;
- payload JSON;
- manejo de respuesta;
- interfaz;
- estados de carga/error;
- fallback local cuando sea necesario.

No inventar URLs reales de webhooks.

## Git

Trabajar por fases pequeñas.

Después de cada fase importante:

npm run build
npm test

No hacer refactors masivos sin necesidad.



