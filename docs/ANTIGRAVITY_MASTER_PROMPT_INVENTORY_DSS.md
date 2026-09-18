# MASTER PROMPT — INVENTORY DSS / QUIZ N8N + REACT

## 0. CONTEXTO DEL PROYECTO

Este proyecto corresponde a la **Solución 5 — Logística / Inventario** del quiz de automatización con n8n.

El problema de negocio es que el área de logística revisa manualmente el inventario para detectar productos con stock bajo, lo que puede provocar quiebres de inventario, pedidos tardíos y falta de visibilidad sobre si el inventario actual realmente alcanza hasta la próxima entrega del proveedor.

La meta es construir una aplicación front end en React que funcione como una interfaz de apoyo a decisiones de inventario y que pueda integrarse con un workflow de n8n. La automatización de n8n se desarrollará por separado.

La aplicación **NO debe ser solamente visual**. Debe tener lógica de negocio real, persistencia simulada, métricas, CRUD, análisis de riesgo y preparación para integrarse con n8n mediante Webhook.

---

# 1. OBJETIVO GENERAL

Construir una aplicación React llamada provisionalmente **Inventory DSS** que permita registrar productos, proveedores y movimientos de inventario, calcular automáticamente el riesgo de quiebre de stock, proyectar si el inventario alcanza hasta la próxima entrega y mostrar recomendaciones accionables al encargado de compras o logística.

El sistema debe transformar datos de inventario en decisiones útiles, no limitarse a mostrar tablas.

---

# 2. IDEA CENTRAL DEL SISTEMA

El sistema debe responder preguntas como:

- ¿Qué productos están actualmente bajo el stock mínimo?
- ¿Cuántos días de cobertura quedan para cada producto?
- ¿El producto se agotará antes de la próxima entrega?
- ¿Cuántos días podría quedar la empresa sin inventario?
- ¿Qué productos tienen riesgo crítico, alto, medio o normal?
- ¿Qué cantidad sería razonable reordenar?
- ¿Qué proveedor está asociado al producto?
- ¿Qué productos deben revisarse primero?
- ¿Qué alertas fueron generadas y cuándo?
- ¿Qué acciones fueron recomendadas?

Ejemplo importante:

Si hoy es miércoles, hay 300 unidades, el consumo promedio es de 100 unidades por día y la próxima entrega llega el lunes, el sistema NO debe decir simplemente “stock correcto” porque 300 es mayor que el stock mínimo.

Debe detectar:

- cobertura aproximada: 3 días;
- próxima entrega: en 5 días;
- el inventario se agotará antes de la entrega;
- riesgo crítico;
- ventana estimada de desabastecimiento: aproximadamente 2 días.

---

# 3. STACK OBLIGATORIO

Usar:

- React
- Vite
- JavaScript o TypeScript, preferiblemente TypeScript si no aumenta demasiado la complejidad
- React Router DOM
- JSON Server con `db.json`
- Fetch o Axios en una carpeta `services`
- Recharts para métricas/gráficos
- Lucide React para iconografía
- Jest + React Testing Library para pruebas unitarias
- CSS modular, CSS normal o una solución clara y mantenible

No usar backend real.
No usar bases de datos reales.
No incluir credenciales reales.

---

# 4. ESTRUCTURA DE CARPETAS

Usar una estructura ordenada similar a:

```text
src/
├── components/
│   ├── common/
│   ├── layout/
│   ├── inventory/
│   ├── alerts/
│   ├── charts/
│   └── forms/
├── pages/
│   ├── Login/
│   ├── Dashboard/
│   ├── Inventory/
│   ├── Products/
│   ├── Suppliers/
│   ├── Alerts/
│   ├── Reports/
│   └── Settings/
├── routes/
│   ├── Routing.jsx
│   └── PrivateRoutes.jsx
├── services/
│   ├── api.js
│   ├── products.service.js
│   ├── suppliers.service.js
│   ├── movements.service.js
│   ├── alerts.service.js
│   └── automation.service.js
├── hooks/
├── context/
├── utils/
│   ├── inventoryRisk.js
│   ├── dates.js
│   └── formatters.js
├── tests/
├── styles/
├── App.jsx
└── main.jsx
```

Si Antigravity propone una variante equivalente y ordenada, puede usarla.

---

# 5. RUTAS

## Públicas

- `/login`
- `/register`

## Privadas

- `/dashboard`
- `/inventory`
- `/products`
- `/suppliers`
- `/alerts`
- `/reports`
- `/settings`

La protección de rutas debe funcionar aunque el usuario escriba la URL manualmente.

---

# 6. ROLES

Crear mínimo estos roles:

### `admin`
Puede:
- crear/editar/eliminar productos;
- crear/editar/eliminar proveedores;
- crear usuarios;
- configurar stock mínimo;
- modificar reglas simples;
- ver todas las métricas;
- ver alertas y reportes.

### `logistics`
Puede:
- consultar inventario;
- registrar movimientos;
- ver alertas;
- ver recomendaciones;
- marcar alertas como revisadas.

### `viewer`
Solo lectura.

El rol debe guardarse en `db.json` dentro del usuario.

---

# 7. MODELO DE DATOS EN DB.JSON

Crear un `db.json` con datos ficticios y suficientes ejemplos para demostrar el sistema.

## users

```json
{
  "id": "u1",
  "name": "Admin Demo",
  "email": "admin@demo.com",
  "password": "123456",
  "role": "admin"
}
```

## products

Cada producto debe tener como mínimo:

```json
{
  "id": "p1",
  "sku": "MAT-204",
  "name": "Materia prima A",
  "category": "Materia prima",
  "stockCurrent": 300,
  "stockMin": 150,
  "dailyConsumption": 100,
  "supplierId": "s1",
  "leadTimeDays": 5,
  "nextDeliveryDate": "2026-09-21",
  "nextDeliveryQty": 1000,
  "unit": "unidades",
  "location": "Bodega Central",
  "active": true
}
```

## suppliers

```json
{
  "id": "s1",
  "name": "SupplyCR",
  "email": "compras@supplycr.demo",
  "phone": "+506 0000-0000",
  "averageLeadTimeDays": 5,
  "status": "active",
  "lateDeliveries": 2
}
```

## inventoryMovements

Tipos:
- `IN`
- `OUT`
- `ADJUSTMENT`

Ejemplo:

```json
{
  "id": "m1",
  "productId": "p1",
  "type": "OUT",
  "quantity": 100,
  "date": "2026-09-18T08:00:00",
  "reason": "Consumo de producción"
}
```

## alerts

```json
{
  "id": "a1",
  "productId": "p1",
  "severity": "CRITICAL",
  "type": "STOCKOUT_BEFORE_DELIVERY",
  "message": "El producto se agotará antes de la próxima entrega.",
  "createdAt": "2026-09-18T08:00:00",
  "status": "OPEN"
}
```

## automationLogs

Guardar ejecuciones simuladas o respuestas de n8n.

---

# 8. REGLAS DE NEGOCIO OBLIGATORIAS

Toda la lógica debe vivir en funciones reutilizables dentro de `utils/inventoryRisk.js` o equivalente.

## 8.1 Días de cobertura

```text
daysCoverage = stockCurrent / dailyConsumption
```

Si `dailyConsumption <= 0`, tratar la cobertura como no calculable o muy alta, pero nunca dividir entre cero.

---

## 8.2 Días hasta próxima entrega

Calcular la diferencia en días entre la fecha actual y `nextDeliveryDate`.

Usar fechas de manera consistente y evitar errores por zona horaria.

---

## 8.3 Stock proyectado al momento de la entrega

```text
projectedStockAtDelivery = stockCurrent - (dailyConsumption * daysUntilDelivery)
```

---

## 8.4 Fecha estimada de agotamiento

Si `dailyConsumption > 0`:

```text
daysUntilStockout = stockCurrent / dailyConsumption
stockoutDate = today + daysUntilStockout
```

Para visualización puede redondearse hacia arriba a días completos.

---

## 8.5 Días estimados sin stock

Si el inventario se agota antes de la entrega:

```text
gapDays = max(0, daysUntilDelivery - daysCoverage)
```

Mostrarlo como aproximación.

---

# 9. CLASIFICACIÓN DE RIESGO

Aplicar estas reglas en este orden:

## CRITICAL

Si ocurre al menos una:

```text
projectedStockAtDelivery <= 0
```

o

```text
daysCoverage < daysUntilDelivery
```

Significa que el producto probablemente se agotará antes de la próxima entrega.

---

## HIGH

Si no es crítico y ocurre alguna:

```text
stockCurrent <= stockMin
```

```text
projectedStockAtDelivery < stockMin
```

---

## MEDIUM

Si no es crítico/alto y ocurre alguna:

```text
stockCurrent <= stockMin * 1.25
```

```text
daysCoverage <= daysUntilDelivery + 2
```

---

## NORMAL

Todo lo demás.

---

# 10. SCORE DE RIESGO OPCIONAL PERO DESEABLE

Calcular un score 0-100 para ordenar prioridades.

Propuesta simple:

```text
stock <= stockMin                        +20
stockout antes de próxima entrega        +40
proveedor con atraso conocido            +15
leadTimeDays >= 7                        +10
cobertura <= díasEntrega + 2             +15
```

Máximo 100.

Clasificación visual:

```text
0-25   NORMAL
26-50  MEDIUM
51-75  HIGH
76-100 CRITICAL
```

El score no debe reemplazar las reglas críticas. Sirve para priorización.

---

# 11. CANTIDAD SUGERIDA DE REPOSICIÓN

Usar una fórmula simple y explicable.

```text
demandDuringLeadTime = dailyConsumption * leadTimeDays

targetStock = demandDuringLeadTime + stockMin

suggestedOrderQty = max(0, targetStock - stockCurrent)
```

Mostrar como “cantidad sugerida”, nunca como compra automática.

---

# 12. RECOMENDACIONES AUTOMÁTICAS

Según el riesgo, generar texto claro.

## Critical

- Adelantar próxima entrega.
- Consultar proveedor alternativo.
- Transferir inventario desde otra ubicación.
- Revisar programación de producción/consumo.
- Generar orden de compra urgente para revisión humana.

## High

- Preparar reorden.
- Confirmar fecha del proveedor.
- Revisar consumo reciente.

## Medium

- Monitorear producto.
- Revisar stock en próxima ejecución.

## Normal

- Sin acción inmediata.

Las recomendaciones deben ser explicables y derivarse de datos.

---

# 13. DETECCIÓN DE FIN DE SEMANA / FECHAS

Agregar una lógica de presentación especial cuando la fecha estimada de agotamiento ocurre antes de una entrega posterior al fin de semana.

Ejemplo:

```text
Agotamiento estimado: viernes
Próxima entrega: lunes
```

Mostrar:

> “Riesgo de interrupción: el producto podría agotarse el viernes y la siguiente entrega está programada para el lunes.”

No inventar días si la fecha no lo indica.

---

# 14. DASHBOARD

Debe tener como mínimo:

### KPIs

1. Total de productos activos.
2. Productos en riesgo crítico.
3. Productos en riesgo alto.
4. Productos bajo stock mínimo.
5. Alertas abiertas.
6. Cobertura promedio en días.

Mostrar al menos 3, pero preferiblemente 5-6.

### Gráficos

Con Recharts:

- Bar chart: productos por nivel de riesgo.
- Pie/Donut opcional: distribución por categoría.
- Line chart opcional: movimientos de inventario por fecha.

---

# 15. INVENTORY TABLE

Columnas sugeridas:

- SKU
- Producto
- Stock actual
- Stock mínimo
- Consumo diario
- Cobertura
- Próxima entrega
- Stock proyectado
- Riesgo
- Score
- Acción

Permitir:

- búsqueda;
- filtro por riesgo;
- filtro por categoría;
- ordenar por score;
- abrir detalle.

---

# 16. PRODUCT DETAIL

Al abrir un producto mostrar:

- datos generales;
- stock actual;
- consumo diario;
- stock mínimo;
- proveedor;
- lead time;
- próxima entrega;
- cantidad de entrega;
- cobertura;
- fecha estimada de agotamiento;
- stock proyectado a la entrega;
- gap de desabastecimiento;
- score;
- nivel de riesgo;
- recomendaciones;
- historial de movimientos;
- historial de alertas.

---

# 17. CRUDS

Implementar CRUD real con JSON Server para:

## Productos
- Create
- Read
- Update
- Delete

## Proveedores
- Create
- Read
- Update
- Delete

## Usuarios
- Create
- Read
- Update
- Delete

## Movimientos
- Create
- Read
- Update opcional
- Delete opcional según diseño

Alertas pueden permitir:
- lectura;
- cambiar estado `OPEN`, `REVIEWED`, `RESOLVED`.

---

# 18. INTEGRACIÓN CON N8N

NO implementar el workflow de n8n dentro de Antigravity.

La aplicación solo debe quedar preparada para enviar datos a un webhook configurable.

Crear variable de entorno:

```env
VITE_N8N_INVENTORY_WEBHOOK_URL=
```

Crear servicio:

```text
src/services/automation.service.js
```

Debe incluir una función similar a:

```js
export async function runInventoryAutomation(payload) {
  const url = import.meta.env.VITE_N8N_INVENTORY_WEBHOOK_URL;

  if (!url) {
    throw new Error('Webhook de n8n no configurado');
  }

  const response = await fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(payload)
  });

  if (!response.ok) {
    throw new Error('Error ejecutando automatización');
  }

  return response.json();
}
```

Payload sugerido:

```json
{
  "source": "inventory-dss-react",
  "trigger": "manual-analysis",
  "timestamp": "2026-09-18T08:00:00",
  "products": []
}
```

La UI debe tener un botón visible:

**“Ejecutar análisis de inventario”**

Al presionarlo:

1. carga productos;
2. genera el payload;
3. llama al webhook;
4. muestra loading;
5. recibe respuesta;
6. actualiza la sección de resultados;
7. guarda un registro en `automationLogs` si corresponde.

Si no hay webhook configurado, mostrar un mensaje claro y permitir una simulación local opcional.

---

# 19. CONTRATO ESPERADO DE RESPUESTA DE N8N

Preparar la UI para recibir algo como:

```json
{
  "executedAt": "2026-09-18T08:00:00",
  "summary": {
    "total": 84,
    "critical": 4,
    "high": 7,
    "medium": 13,
    "normal": 60
  },
  "alerts": [
    {
      "productId": "p1",
      "sku": "MAT-204",
      "severity": "CRITICAL",
      "daysCoverage": 3,
      "daysUntilDelivery": 5,
      "projectedStockAtDelivery": -200,
      "gapDays": 2,
      "suggestedOrderQty": 350,
      "message": "El inventario se agotará antes de la próxima entrega."
    }
  ]
}
```

La respuesta exacta del workflow se ajustará después, pero usar esta forma como contrato inicial.

---

# 20. AUTOMATIZACIÓN LOCAL EN FRONT END

Aunque n8n hará el flujo oficial del quiz, la página debe tener su propia función de cálculo local para:

- previsualizar riesgos;
- evitar depender siempre del webhook;
- mostrar resultados inmediatamente;
- permitir pruebas unitarias.

Debe existir una sola fuente de verdad para estas fórmulas en `inventoryRisk.js`.

Cuando se integre n8n, comparar que las reglas principales produzcan resultados equivalentes.

---

# 21. ACCESIBILIDAD

Implementar mínimo:

1. Modo claro/oscuro.
2. Estados no dependientes únicamente del color.
3. HTML semántico y ARIA.
4. Tamaño de texto ajustable si es viable.

Para riesgo usar:

- icono;
- texto;
- color.

Nunca solamente color.

Ejemplo:

```text
[!] CRITICAL
[↑] HIGH
[~] MEDIUM
[✓] NORMAL
```

---

# 22. RESPONSIVE

Diseñar para:

- móvil ~375px;
- tablet ~768px;
- desktop >=1280px.

En móvil:

- sidebar colapsable;
- tablas convertidas a cards o scroll controlado;
- KPIs en una columna o 2 columnas;
- acciones accesibles sin hover.

---

# 23. ESTILO VISUAL

Crear una interfaz tipo **Enterprise Operations SaaS**, limpia y profesional.

No hacer una página infantil ni excesivamente futurista.

Características:

- fondo claro o grafito según tema;
- cards limpias;
- tipografía moderna;
- dashboards densos pero legibles;
- chips de riesgo;
- iconos Lucide;
- transiciones cortas;
- jerarquía clara;
- buen contraste.

Evitar abuso de glassmorphism, neón y animaciones decorativas.

---

# 24. LOGIN Y SESIÓN

Login con usuarios de JSON Server.

Para un proyecto académico se puede simular la sesión con `localStorage`.

Guardar únicamente datos mínimos:

```json
{
  "id": "u1",
  "name": "Admin Demo",
  "role": "admin"
}
```

Implementar logout.

PrivateRoutes debe bloquear rutas privadas.

RoleGuard o lógica equivalente debe bloquear módulos/actions por rol.

---

# 25. PRUEBAS UNITARIAS OBLIGATORIAS

Crear pruebas para las reglas principales.

Casos mínimos:

### Test 1 — stockout antes de entrega

```text
stock = 300
consumo = 100
entrega = 5 días
```

Esperado:

```text
coverage = 3
risk = CRITICAL
```

### Test 2 — stock bajo pero alcanza

Debe producir HIGH.

### Test 3 — stock cercano a mínimo

Debe producir MEDIUM.

### Test 4 — stock saludable

Debe producir NORMAL.

### Test 5 — consumo diario 0

No debe dividir entre cero.

### Test 6 — suggestedOrderQty

Validar la fórmula.

### Test 7 — PrivateRoute

Usuario no autenticado no puede entrar.

---

# 26. DATOS DEMO IMPORTANTES

Incluir productos ficticios que cubran todos los escenarios.

### CRITICAL

Producto que se agota viernes y entrega llega lunes.

### HIGH

Producto por debajo del mínimo pero entrega próxima suficiente.

### MEDIUM

Producto cercano al mínimo.

### NORMAL

Producto con buena cobertura.

Crear al menos 12-15 productos de prueba.

---

# 27. PÁGINA DE REPORTES

Mostrar:

- resumen de última ejecución;
- total analizado;
- críticos;
- altos;
- medios;
- normales;
- lista de productos a reordenar;
- suggestedOrderQty;
- proveedor;
- explicación.

Agregar botón:

**“Exportar CSV”**

No hace falta PDF para el front end salvo que sea sencillo.

---

# 28. LOG DE AUTOMATIZACIONES

Crear vista o sección:

```text
Automation History
```

Campos:

- fecha;
- tipo;
- productos analizados;
- críticos detectados;
- estado;
- origen (`local` / `n8n`).

Esto ayuda a demostrar que el sistema no es solamente visual.

---

# 29. MENSAJES DEL SISTEMA

Usar estados claros:

- Loading
- Empty state
- Error
- Success

Ejemplo:

> “Se analizaron 15 productos. Se detectaron 2 riesgos críticos y 3 riesgos altos.”

---

# 30. ERRORES QUE ANTIGRAVITY DEBE EVITAR

NO:

- poner toda la lógica en un solo componente;
- hardcodear resultados de riesgo;
- usar colores sin texto/icono;
- llamar directamente a URLs desde componentes cuando exista `services`;
- usar un único `App.jsx` gigante;
- duplicar las reglas de riesgo en varios archivos;
- almacenar contraseñas reales;
- inventar integraciones con ERP reales;
- realizar órdenes de compra automáticamente;
- depender obligatoriamente de n8n para que el front end funcione;
- usar datos reales/confidenciales.

---

# 31. CRITERIO DE DECISIÓN HUMANA

El sistema es de apoyo a decisiones.

Debe decir:

> “Acción sugerida”

No:

> “Orden de compra ejecutada automáticamente”

El encargado de logística/compras confirma la acción.

---

# 32. README OBLIGATORIO

Crear README con:

- nombre del proyecto;
- descripción;
- stack;
- instalación;
- cómo correr Vite;
- cómo correr JSON Server;
- usuarios demo;
- variables `.env`;
- estructura;
- reglas de riesgo;
- integración futura con n8n;
- cómo correr tests.

Comandos esperados, ajustándolos al package.json real:

```bash
npm install
npm run dev
npm run server
npm test
```

Si hace falta correr ambos simultáneamente, incluir `concurrently`.

---

# 33. SCRIPTS DEL PACKAGE.JSON

Idealmente:

```json
{
  "scripts": {
    "dev": "vite",
    "server": "json-server --watch db.json --port 3001",
    "start:all": "concurrently \"npm run dev\" \"npm run server\"",
    "test": "jest",
    "test:coverage": "jest --coverage",
    "build": "vite build"
  }
}
```

Ajustar a la configuración real.

---

# 34. DEFINICIÓN DE TERMINADO

El proyecto se considera listo cuando:

- `npm install` funciona;
- `npm run dev` funciona;
- JSON Server funciona;
- login funciona;
- rutas privadas funcionan;
- roles funcionan;
- CRUD de productos funciona;
- CRUD de proveedores funciona;
- movimientos modifican o reflejan inventario correctamente según el diseño;
- risk engine calcula cobertura y riesgo;
- se detecta stockout antes de entrega;
- el caso viernes → lunes aparece correctamente;
- dashboard muestra métricas reales del `db.json`;
- gráfico funciona;
- botón de análisis funciona localmente;
- servicio de n8n está preparado;
- errores de webhook se manejan;
- responsive funciona;
- accesibilidad básica funciona;
- tests principales pasan;
- build pasa;
- no hay errores rojos en consola.

---

# 35. PROMPT MAESTRO PARA ANTIGRAVITY

Copiar desde aquí si se desea usar un único prompt inicial:

> Construye una aplicación completa en React + Vite llamada provisionalmente **Inventory DSS**, orientada a logística e inventario. No quiero un dashboard puramente visual: debe implementar lógica real de apoyo a decisiones.
>
> La app debe usar React Router DOM, JSON Server (`db.json`), carpeta `services`, autenticación simulada, rutas públicas/privadas, roles `admin`, `logistics` y `viewer`, CRUD de productos, proveedores y usuarios, registro de movimientos, dashboard con Recharts, Jest y responsive móvil/tablet/escritorio.
>
> La lógica principal debe calcular por producto: días de cobertura, días hasta próxima entrega, stock proyectado a la fecha de entrega, fecha estimada de agotamiento, días aproximados de desabastecimiento, cantidad sugerida de reposición y nivel de riesgo `NORMAL`, `MEDIUM`, `HIGH` o `CRITICAL`.
>
> Reglas obligatorias: `CRITICAL` si `projectedStockAtDelivery <= 0` o `daysCoverage < daysUntilDelivery`; `HIGH` si no es crítico y `stockCurrent <= stockMin` o `projectedStockAtDelivery < stockMin`; `MEDIUM` si no es crítico/alto y `stockCurrent <= stockMin * 1.25` o `daysCoverage <= daysUntilDelivery + 2`; en los demás casos `NORMAL`.
>
> Debe existir un caso demo donde el producto se agota un viernes y la siguiente entrega llega el lunes; el sistema debe advertir explícitamente que existe una ventana de desabastecimiento antes de la entrega.
>
> La lógica de riesgo debe estar centralizada en `src/utils/inventoryRisk.js` y tener pruebas unitarias. No duplicar fórmulas en componentes.
>
> Crear una integración preparada para n8n mediante `VITE_N8N_INVENTORY_WEBHOOK_URL` y un `automation.service.js`. Añadir un botón “Ejecutar análisis de inventario” que pueda enviar todos los productos al webhook, procesar la respuesta, mostrar loading/error/success y registrar la ejecución. El front end debe funcionar también sin n8n usando cálculo local.
>
> La interfaz debe ser estilo Enterprise Operations SaaS: profesional, limpia, con modo claro/oscuro, buena accesibilidad, iconos Lucide, tablas/filtros, cards KPI y visualización de riesgo usando texto + icono + color. No usar diseño futurista exagerado.
>
> Incluir al menos 12-15 productos demo cubriendo los cuatro niveles de riesgo. Crear README completo, `.env.example`, scripts para Vite y JSON Server y tests para las reglas principales. Antes de terminar, ejecutar build/tests y corregir errores.
>
> Trabaja de forma incremental. Primero crea arquitectura y datos; luego servicios y auth; luego motor de riesgo; luego CRUD; luego dashboard; luego integración n8n; luego tests y responsive. No sacrifiques funcionalidad por decoración.

---

# 36. PROMPTS POR FASE PARA ANTIGRAVITY

## Fase 1 — Base

> Crea la estructura React/Vite del proyecto Inventory DSS con React Router DOM, JSON Server, carpetas `components`, `pages`, `routes`, `services`, `hooks`, `context`, `utils`, `tests` y `styles`. Configura scripts para Vite y JSON Server. Crea `db.json` con users, products, suppliers, inventoryMovements, alerts y automationLogs. Incluye 15 productos ficticios y casos NORMAL/MEDIUM/HIGH/CRITICAL.

## Fase 2 — Auth y roles

> Implementa Login, Register, persistencia simple en localStorage, PrivateRoutes y autorización por roles `admin`, `logistics`, `viewer`. Protege acciones, no solo páginas.

## Fase 3 — Risk Engine

> Implementa `inventoryRisk.js` con días de cobertura, días hasta entrega, stock proyectado, fecha estimada de stockout, gapDays, suggestedOrderQty, risk score y clasificación. Añade tests unitarios completos. Toda la app debe consumir esta única lógica.

## Fase 4 — CRUD

> Implementa CRUD completo de productos y proveedores consumiendo JSON Server a través de archivos en `services`. Añade validaciones de formularios y estados loading/error/empty.

## Fase 5 — Dashboard

> Construye dashboard con KPIs reales, Recharts, productos críticos, alertas recientes y orden por risk score. Añade filtros y navegación al detalle del producto.

## Fase 6 — Automatización

> Implementa el botón “Ejecutar análisis de inventario”. Primero permite ejecutar el análisis local con las reglas existentes. Después prepara `automation.service.js` para enviar el inventario a `VITE_N8N_INVENTORY_WEBHOOK_URL`. La ausencia de webhook no debe romper la aplicación.

## Fase 7 — Reportes

> Construye Reports con resumen de ejecución, productos a reordenar, cantidad sugerida, riesgo y proveedor. Añade exportación CSV.

## Fase 8 — Accesibilidad y responsive

> Audita toda la app en 375px, 768px y 1280px. Implementa modo claro/oscuro, ARIA/HTML semántico y estados de riesgo con texto+icono+color. Corrige overflow y navegación móvil.

## Fase 9 — QA

> Ejecuta tests, build y revisión de consola. Corrige errores. Verifica auth, rutas, roles, CRUD, risk engine, caso viernes-lunes, dashboard, exportación CSV y manejo de webhook caído.

---

# 37. LÍMITE DE RESPONSABILIDAD ENTRE ANTIGRAVITY Y N8N

## Antigravity se encarga de:

- aplicación React;
- UI;
- JSON Server;
- CRUD;
- auth y roles;
- risk engine local;
- dashboard;
- pruebas;
- integración por webhook;
- visualización de resultados.

## ChatGPT se encargará después de:

- diseñar el workflow n8n;
- crear el JSON importable;
- definir nodos;
- transformar datos;
- condiciones;
- generación del reporte;
- respuesta del Webhook;
- justificación de negocio del quiz;
- instrucciones para importar/probar en n8n.

No implementar un workflow ficticio dentro del repositorio React como sustituto de n8n.

---

# 38. RESULTADO FINAL ESPERADO

El resultado debe sentirse como un pequeño sistema real de logística:

```text
Inventario
   ↓
Datos estructurados
   ↓
Cálculos de cobertura
   ↓
Proyección hasta próxima entrega
   ↓
Clasificación de riesgo
   ↓
Recomendaciones
   ↓
Automatización n8n
   ↓
Reporte / alerta
   ↓
Decisión humana
```

El objetivo principal es demostrar que la automatización ayuda a anticipar problemas antes de que ocurra un quiebre de stock.
