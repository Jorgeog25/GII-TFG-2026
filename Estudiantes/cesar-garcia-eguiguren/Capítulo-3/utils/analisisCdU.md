# Disciplina de Análisis — Análisis de Casos de Uso

## Netkia Analytics · Módulo externo sobre Odoo v16 Enterprise

---

## Índice

1. [Marco metodológico](#1-marco-metodológico)
2. [CU-02 — Listar empleados](#2-cu-02--listar-empleados)
3. [CU-03 — Ver resumen de empleado](#3-cu-03--ver-resumen-de-empleado)
4. [CU-08 — Listar tareas](#4-cu-08--listar-tareas)
5. [CU-13 — Consultar rentabilidad financiera](#5-cu-13--consultar-rentabilidad-financiera)
6. [CU-17 — Guardar snapshot](#6-cu-17--guardar-snapshot)
7. [CU-19 — Consultar detalle de snapshot](#7-cu-19--consultar-detalle-de-snapshot)
8. [Patrón de colaboración establecido](#8-patrón-de-colaboración-establecido)
9. [Tabla resumen de trazabilidad](#9-tabla-resumen-de-trazabilidad)

---

## 1. Marco metodológico

### Propósito de esta fase

En RUP, el análisis de casos de uso no describe *cómo* se implementa cada flujo —eso corresponde al diseño— sino *qué responsabilidades* asume cada clase y *qué colaboraciones* son necesarias para que el sistema satisfaga el contrato acordado con el actor. El resultado es una **realización de caso de uso en análisis**: un conjunto de clases con estereotipos bien definidos que colectivamente cumplen el comportamiento descrito.

### Los tres estereotipos y su correspondencia con el código

En Netkia Analytics los tres estereotipos del análisis se proyectan directamente sobre las capas del backend FastAPI y los componentes React del frontend.

| Estereotipo RUP | Capa real en Netkia Analytics | Ejemplos concretos |
|---|---|---|
| **Vista (Boundary)** | Ruta FastAPI + componente/página React | `employee.router`, `Employees.jsx`, `SaveSnapshotButton.jsx` |
| **Control** | Clase de servicio | `EmployeeService`, `DashboardService`, `SnapshotService` |
| **Entidad (Entity)** | Modelo SQLAlchemy / colección MongoDB | `Employee`, `Task`, `Timesheet`, `metric_snapshots` |

Esta correspondencia no es casual. La **restricción de solo lectura** sobre la base de datos de Odoo fuerza a que toda la lógica de negocio resida en los servicios (Control) y a que ningún modelo (Entity) lleve comportamiento propio. Los modelos SQLAlchemy únicamente declaran columnas y relaciones; nunca ejecutan cálculos ni toman decisiones.

### Restricción fundamental que condiciona el análisis

El acceso exclusivo de lectura sobre PostgreSQL (base de datos de Odoo) determina las siguientes decisiones de análisis aplicadas de forma transversal a todos los casos de uso:

- No existen patrones de escritura ni transacciones de modificación sobre la base relacional.
- Todos los modelos mapean tablas existentes sin añadir columnas.
- La lógica operativa se concentra en los servicios (Control): optimización de consultas y cálculo de métricas.
- La **única excepción** es el subsistema de capturas (CU-17 a CU-20), que persiste en MongoDB, base documental completamente independiente de Odoo.

---

## 2. CU-02 — Listar empleados

### Clases de análisis identificadas

#### Clases de Vista (Boundary)

**`Employees.jsx` + `employee.router GET /employees/`**

Estereotipo: Vista (Boundary)

Responsabilidades:

- Recibir la solicitud de apertura del listado de empleados desde el actor (Responsable o Director).
- Capturar y validar los criterios de filtrado introducidos por el usuario: nombre, departamento, estado activo, criterio de ordenación y página.
- Comunicarse con el Control para obtener la lista paginada de empleados.
- Presentar al actor la respuesta `PaginatedResponse[EmployeeDetail]` renderizada en una tabla con columnas nombre, departamento, cargo, email, coste/hora y estado.
- Gestionar la navegación hacia el detalle de un empleado concreto (`EmployeeDetail.jsx`).

Colaboraciones:

- **Entrada:** recibe la solicitud del actor (Responsable o Director) autenticado.
- **Control:** se comunica con `EmployeeService` a través del router FastAPI.
- **Salida:** retorna la lista paginada renderizada al actor.

---

#### Clases de Control

**`EmployeeService.list_employees()`**

Estereotipo: Control

Responsabilidades:

- Aplicar la restricción de ámbito según el rol del usuario autenticado (`CurrentUser`): si el usuario es `responsable`, la consulta se limita a los IDs de empleado presentes en `cu.employee_ids`; si es `director`, no se aplica restricción alguna.
- Verificar la existencia y el ámbito del departamento si se ha filtrado por él (`verify_department_exists`, `verify_department_scope`).
- Delegar en el repositorio la construcción y ejecución de la query SQLAlchemy filtrada y ordenada.
- Aplicar paginación sobre el resultado mediante `paginate()`.
- Serializar cada fila del resultado al esquema de salida `EmployeeDetail`.

Colaboraciones:

- **Vista:** responde a solicitudes de `employee.router`.
- **Repositorio:** delega la construcción de la query a `get_filtered_employees_query()`.

---

#### Clases de Entidad (Entity)

**`Employee`** (mapea `hr_employee`)

Estereotipo: Entidad

Responsabilidades:

- Representar la información de un empleado individual tal como existe en Odoo.
- Encapsular atributos: `id`, `name`, `job_title`, `work_email`, `work_phone`, `hourly_cost`, `active`, `department_id`, `user_id`.
- Mantener la relación de agregación con `Department` a través de `department_id`.

Colaboraciones:

- **Repositorio:** es gestionado por `get_filtered_employees_query()` mediante `outerjoin` hacia `hr_department`.

**`Department`** (mapea `hr_department`)

Estereotipo: Entidad

Responsabilidades:

- Proporcionar el nombre del departamento al que pertenece cada empleado sin necesidad de una segunda consulta, gracias al `outerjoin`.

---

### Flujo de colaboración

#### Secuencia de operaciones

1. **Inicio:** el actor abre la vista de empleados → `Employees.jsx` inicializa la petición.
2. **Petición HTTP:** `Employees.jsx → employee.router GET /employees/` con parámetros de filtro.
3. **Guard de autenticación:** `require_manager_or_above` verifica el token JWT y extrae `CurrentUser`.
4. **Aplicación de ámbito:** `EmployeeService.list_employees()` determina si aplica filtro por `employee_ids`.
5. **Construcción de query:** `EmployeeService → get_filtered_employees_query(db, employee_ids, department_id, search, active, sort_by, sort_order)`.
6. **Paginación:** `paginate(query, page, page_size)` ejecuta `COUNT` y `SELECT … OFFSET … LIMIT`.
7. **Serialización y respuesta:** el router devuelve `PaginatedResponse[EmployeeDetail]` al Boundary.
8. **Presentación:** `Employees.jsx` renderiza la tabla paginada con controles de filtrado y ordenación.

### Correspondencia con requisitos

| Requisito del caso de uso | Clase responsable | Método / Colaboración |
|---|---|---|
| Presentar lista paginada de empleados | `Employees.jsx` | Coordina con `EmployeeService.list_employees()` |
| Filtrar por nombre, departamento, estado | `Employees.jsx` | Invoca `employee.router` con `search`, `department_id`, `active` |
| Ordenación servidor por columna | `EmployeeService` | Delega `sort_by` y `sort_order` al repositorio |
| Restricción de ámbito por rol | `EmployeeService` | Aplica `cu.employee_ids` si `cu.is_responsable` |
| Encapsular datos del empleado | `Employee` | Atributos de `hr_employee` |
| Obtener nombre de departamento | `Department` | `outerjoin` sobre `hr_department` |

---

## 3. CU-03 — Ver resumen de empleado

### Clases de análisis identificadas

#### Clases de Vista (Boundary)

**`EmployeeDetail.jsx` + `employee.router GET /employees/{id}` + `dashboards.router GET /dashboards/summary/employee/{id}`**

Estereotipo: Vista (Boundary)

Responsabilidades:

- Recibir la solicitud de apertura del detalle de un empleado concreto.
- Lanzar en paralelo dos peticiones al backend: la ficha básica del empleado y el resumen compuesto de métricas.
- Presentar al actor la cabecera del empleado (nombre, cargo, departamento, coste/hora) y cuatro KPIs: tareas pendientes, tareas vencidas, WIP actual y productividad media de los últimos 30 días.
- Gestionar las pestañas de tareas (`pending`, `completed`, `assigned`, `responsible`) con filtros de rango de fechas por pestaña.
- Navegar hacia el detalle de una tarea concreta.

Colaboraciones:

- **Entrada:** recibe la solicitud del actor tras navegar desde `Employees.jsx` o `DepartmentDetail.jsx`.
- **Control:** se comunica con `EmployeeService` (ficha) y `DashboardService` (resumen).
- **Salida:** presenta el dashboard compuesto del empleado.

---

#### Clases de Control

**`DashboardService.get_employee_summary()` — Orquestador principal**

Estereotipo: Control

Responsabilidades:

- Verificar que el empleado solicitado existe y está dentro del ámbito del usuario autenticado.
- Instanciar y coordinar los tres subservicios de métricas: `WorkloadService`, `WIPService` y `ProductivityService`.
- Componer los resultados en un único objeto `EmployeeSummaryResponse` con los campos `workload`, `wip`, `productivity_last_30_days` y `quick_stats`.
- Calcular `quick_stats` a partir de los tres resultados parciales para proporcionar un acceso rápido a los indicadores más relevantes.

Colaboraciones:

- **Vista:** responde a `dashboards.router GET /dashboards/summary/employee/{id}`.
- **Subservicios:** delega el cálculo parcial en `WorkloadService.calculate()`, `WIPService.calculate()` y `ProductivityService.calculate()`.

**`WorkloadService.calculate()`**

Estereotipo: Control

Responsabilidades:

- Obtener el `user_id` del empleado a partir de su `employee_id`.
- Recuperar todas las tareas abiertas asignadas al usuario con sus horas planificadas y trabajadas.
- Recuperar las tareas cerradas en los últimos 30 días.
- Calcular el porcentaje de carga como `(horas_pendientes / 40) × 100`, con referencia a una jornada semanal de 40 horas.
- Determinar el estado (`sobrecargado` si > 120 %, `subcargado` si < 70 %, `normal` en otro caso).
- Si se solicita modo detallado, formatear las listas de tareas pendientes y completadas.

Colaboraciones:

- **Orquestador:** responde a `DashboardService`.
- **Repositorio:** delega en `get_assigned_open_tasks()`, `get_assigned_closed_tasks()` y `worked_hours_subq()`.

**`WIPService.calculate()`**

Estereotipo: Control

Responsabilidades:

- Contar las tareas abiertas asignadas simultáneamente al usuario.
- Determinar el estado del WIP: `optimo` (≤ 3), `aceptable` (≤ 5) o `sobrecargado` (> 5).
- Generar una recomendación textual según el estado.

**`ProductivityService.calculate()`**

Estereotipo: Control

Responsabilidades:

- Recuperar las tareas cerradas con horas planificadas y reales en el período indicado.
- Calcular la productividad por tarea como `(planned_hours / actual_hours) × 100`.
- Calcular la productividad media del conjunto y ordenar las tareas de mayor a menor productividad.

---

#### Clases de Entidad (Entity)

| Entidad | Tabla Odoo | Rol en el caso de uso |
|---|---|---|
| `Employee` | `hr_employee` | Ficha básica del empleado |
| `Task` | `project_task` | Tareas asignadas abiertas y cerradas |
| `TaskStage` | `project_task_type` | Determina si una tarea está abierta o cerrada |
| `Timesheet` | `account_analytic_line` | Horas trabajadas por tarea (`worked_hours_subq`) |
| `TaskAssignment` | `project_task_user_rel` | Relación M:N entre tareas y usuarios |
| `ResUsers` | `res_users` | Obtener `user_id` desde `employee_id` |

---

### Flujo de colaboración

#### Secuencia de operaciones

1. **Inicio:** el actor navega al detalle de un empleado → `EmployeeDetail.jsx` monta el componente.
2. **Peticiones paralelas:** `getEmployee(id)` y `getEmployeeSummary(id)` se lanzan en paralelo con `useApi`.
3. **Guard y verificación de ámbito:** `require_manager_or_above` + `verify_employee_scope(cu, employee_id)`.
4. **Ficha básica:** `EmployeeService.get_employee(cu, id) → get_employee_by_id(db, id)`.
5. **Orquestación del resumen:** `DashboardService.get_employee_summary(cu, id)` instancia los tres subservicios.
6. **Cálculo de carga:** `WorkloadService.calculate(employee_id, detailed=True)` → `get_user_id_for_employee` → `get_assigned_open_tasks` + `get_assigned_closed_tasks`.
7. **Cálculo de WIP:** `WIPService.calculate(employee_id)` → `count_open_assigned_tasks`.
8. **Cálculo de productividad:** `ProductivityService.calculate(employee_id, date_from, date_to)` → `get_completed_tasks_with_hours`.
9. **Composición:** `DashboardService` combina los tres resultados en `EmployeeSummaryResponse`.
10. **Presentación:** `EmployeeDetail.jsx` renderiza KPIs, cabecera y tabla de tareas con pestañas.

### Correspondencia con requisitos

| Requisito del caso de uso | Clase responsable | Método / Colaboración |
|---|---|---|
| Mostrar ficha básica del empleado | `EmployeeService` | `get_employee(cu, id)` |
| Calcular % de carga de trabajo | `WorkloadService` | `(pending_hours / 40) × 100` |
| Indicar estado de carga | `WorkloadService` | Umbrales 120 % / 70 % |
| Contar tareas en paralelo (WIP) | `WIPService` | `count_open_assigned_tasks` |
| Calcular productividad media 30d | `ProductivityService` | `(planned / actual) × 100` |
| Componer resumen unificado | `DashboardService` | Orquesta los tres subservicios |
| Verificar ámbito del actor | `EmployeeService` | `verify_employee_scope(cu, id)` |

### Decisión de análisis relevante

La separación de `WorkloadService`, `WIPService` y `ProductivityService` en clases independientes, cada una con su propio repositorio, responde directamente al **Principio de Responsabilidad Única**. Si la fórmula de productividad cambia —por ejemplo, se introduce un factor de corrección por complejidad de tarea—, solo se modifica `ProductivityService`; el orquestador `DashboardService` y los demás subservicios permanecen inalterados.

---

## 4. CU-08 — Listar tareas

### Clases de análisis identificadas

#### Clases de Vista (Boundary)

**`Tasks.jsx` + `task.router GET /tasks/filter`**

Estereotipo: Vista (Boundary)

Responsabilidades:

- Recibir la solicitud de listado de tareas con hasta diez parámetros de filtrado combinables: `status`, `stage_id`, `project_id`, `department_id`, `employee_id`, `date_from`, `date_to`, `root_only`, `responsable` y `search`.
- Gestionar la prioridad entre `stage_id` (etapa exacta) y `status` (abierto/cerrado/vencido): si el actor selecciona una etapa concreta, se desactiva el selector de estado genérico.
- Sincronizar los parámetros de la URL con el estado del componente para permitir navegación directa (ej.: desde el panel `Overview` con `?status=overdue`).
- Presentar la tabla paginada con ordenación servidor por las columnas nombre, etapa, horas estimadas, deadline, fecha de cierre y estado.

Colaboraciones:

- **Entrada:** recibe solicitud del actor o navegación directa desde otro componente.
- **Control:** se comunica con `TaskService` a través del router FastAPI.
- **Salida:** presenta la lista paginada con los datos de cada tarea.

---

#### Clases de Control

**`TaskService.filter_tasks()`**

Estereotipo: Control

Responsabilidades:

- Resolver el ámbito del usuario autenticado antes de construir la query: si el usuario es `responsable` y solicita un proyecto fuera de su `cu.project_ids`, lanzar 403 sin llegar a la base de datos.
- Calcular los parámetros efectivos de proyecto: si el usuario es `responsable` sin filtro de empleado, usar `effective_project_ids = cu.project_ids`; si es `director`, usar el `project_id` indicado directamente.
- Verificar existencia de empleado y departamento si se han indicado.
- Delegar en `build_filtered_query()` la construcción de la query compuesta.
- Recuperar las horas trabajadas de todas las tareas de la página en **una sola consulta** mediante `get_worked_hours_batch(db, task_ids)`.
- Seleccionar el serializador correcto según la combinación de parámetros: `_to_pending` si `status=pending` y hay `employee_id`; `_to_completed` si `status=completed` y hay `employee_id`; `_to_assigned` si hay `employee_id` sin estado; `TaskResponse` genérico en caso contrario.

Colaboraciones:

- **Vista:** responde a `task.router GET /tasks/filter`.
- **Repositorio de tareas:** delega en `build_filtered_query()`.
- **Repositorio de horas:** delega en `get_worked_hours_batch()`.

---

#### Clases de Entidad (Entity)

| Entidad | Tabla Odoo | Rol en el caso de uso |
|---|---|---|
| `Task` | `project_task` | Entidad principal; filtros sobre sus columnas |
| `TaskStage` | `project_task_type` | Determina estado abierto/cerrado; nombre de etapa |
| `Project` | `project_project` | JOIN para nombre del proyecto y filtro por proyecto |
| `Employee` | `hr_employee` | JOIN para filtrar tareas por empleado asignado |
| `TaskAssignment` | `project_task_user_rel` | Relación M:N tarea-usuario; necesaria para filtro por empleado |
| `Timesheet` | `account_analytic_line` | Horas trabajadas por tarea (batch query) |

Las subqueries reutilizables `open_stage_ids_subq()` y `closed_stage_ids_subq()` son compartidas por otros repositorios de métricas (`compliance.py`, `productivity.py`, `wip.py`), lo que representa una abstracción de Entity reconocida en la fase de análisis.

---

### Flujo de colaboración

#### Secuencia de operaciones

1. **Inicio:** el actor aplica filtros en `Tasks.jsx` → la URL se actualiza y `useApi` lanza nueva petición.
2. **Petición HTTP:** `GET /tasks/filter` con parámetros.
3. **Guard:** `require_manager_or_above` extrae `CurrentUser`.
4. **Resolución de ámbito:** `TaskService` determina `effective_project_ids` y verifica restricciones de acceso.
5. **Construcción de query:** `build_filtered_query(db, ...)` compone condiciones SQLAlchemy de forma incremental.
6. **Paginación:** `paginate(query, page, page_size)` ejecuta `COUNT` y `SELECT`.
7. **Carga de horas en batch:** `get_worked_hours_batch(db, [t.id for t in items])` — una sola consulta para toda la página.
8. **Serialización selectiva:** `_build_items()` selecciona el serializador correcto para cada modo.
9. **Respuesta:** `PaginatedResponse[TaskResponse | PendingTaskItem | CompletedTaskItem | AssignedTaskItem]`.
10. **Presentación:** `Tasks.jsx` renderiza la tabla con las columnas adaptadas al modo activo.

### Correspondencia con requisitos

| Requisito del caso de uso | Clase responsable | Método / Colaboración |
|---|---|---|
| Filtrar por estado abierto/cerrado/vencido | `TaskService` | Subqueries `open_stage_ids_subq` / `closed_stage_ids_subq` |
| Filtrar por etapa exacta | `build_filtered_query` | `Task.stage_id == stage_id` |
| Filtrar por empleado asignado | `build_filtered_query` | JOIN con `TaskAssignment` y `Employee` |
| Filtrar solo tareas raíz | `build_filtered_query` | `Task.parent_id.is_(None)` |
| Restricción de ámbito por rol | `TaskService` | `effective_project_ids` según `cu.is_responsable` |
| Horas trabajadas sin N+1 queries | `Timesheet` (via batch) | `get_worked_hours_batch(db, task_ids)` |
| Serialización diferenciada por modo | `TaskService` | `_to_pending`, `_to_completed`, `_to_assigned` |

### Decisión de análisis relevante

La función `get_worked_hours_batch()` resuelve el clásico problema N+1: en lugar de consultar las horas de cada tarea individualmente (N consultas adicionales), se recuperan todas las horas de las tareas de la página en una sola consulta `GROUP BY task_id`, y el resultado se mapea en un diccionario `{task_id: hours}`. Esta decisión pertenece al análisis, no al diseño, porque afecta directamente a la **corrección funcional** del sistema bajo carga real: sin ella, una página de 50 tareas generaría 51 consultas a la base de datos de Odoo.

---

## 5. CU-13 — Consultar rentabilidad financiera

### Clases de análisis identificadas

#### Clases de Vista (Boundary)

**`Rentability.jsx` + `metrics.router GET /metrics/profitability/*`**

Estereotipo: Vista (Boundary)

Responsabilidades:

- Presentar el panel de rentabilidad exclusivamente al Director; cualquier otro rol recibe un error 403 antes de que se ejecute ninguna lógica de negocio.
- Gestionar seis modos de visualización: resumen global, por proyecto, por cliente, por responsable, líneas analíticas de proyecto y líneas analíticas de cliente.
- Capturar los parámetros de filtrado: rango de fechas, proyecto específico y responsable.
- Presentar los datos mediante gráficos de barras (ingresos vs. gastos), gráfico de pastel (distribución por estado de proyecto) y tablas detalladas de líneas analíticas.

Colaboraciones:

- **Entrada:** recibe la solicitud del Director autenticado.
- **Control:** se comunica con `RentabilityService` a través del router.
- **Salida:** presenta el panel financiero completo.

---

#### Clases de Control

**`RentabilityService`**

Estereotipo: Control

Responsabilidades:

- Encapsular los seis modos de consulta financiera: `get_summary()`, `get_per_project()`, `get_per_client()`, `get_by_manager()`, `get_project_lines()` y `get_client_lines()`.
- Calcular el porcentaje de rentabilidad como `(net / income) × 100`, gestionando el caso especial en que `income == 0`.
- Determinar el estado de cada proyecto: `ganancia` si `pct > 10 %`, `neutro` si `pct ≥ 0 %`, `perdida` si `pct < 0 %`.
- Agregar los resultados por proyecto para obtener el resumen global o el resumen por cliente/responsable.
- Delegar el acceso a datos en las funciones del repositorio `rentability.py`.

Colaboraciones:

- **Vista:** responde a los seis endpoints del router.
- **Repositorio:** delega en `get_profitability_by_project()`, `get_global_totals()`, `get_project_meta()`, `get_account_analytic_lines()`, `get_client_analytic_lines()`.

---

#### Clases de Entidad (Entity)

| Entidad | Tabla Odoo | Rol en el caso de uso |
|---|---|---|
| `Timesheet` | `account_analytic_line` | **Entidad principal:** `amount` (€, positivo=ingreso / negativo=gasto), `unit_amount` (horas) |
| `Project` | `project_project` | Nombre y cliente del proyecto (campo JSONB multilingüe) |
| `Employee` | `hr_employee` | Nombre del responsable del proyecto |
| `Partner` | `res_partner` | Nombre del cliente |

La entidad `Timesheet` actúa con **dos semánticas distintas** según el campo consultado: en los casos de uso de métricas operativas (CU-10 a CU-12) se usa `unit_amount` para contar horas; en CU-13 se usa `amount` para operar con importes monetarios. El análisis reconoce esta dualidad y la asigna a servicios distintos (`RentabilityService` vs. servicios de métricas operativas), garantizando que un cambio en la lógica financiera no afecte a los cálculos de horas.

---

### Flujo de colaboración

#### Secuencia de operaciones

1. **Inicio:** el Director abre la vista de rentabilidad → `Rentability.jsx` verifica el rol en el contexto de autenticación local.
2. **Petición HTTP:** `GET /metrics/profitability/summary` con parámetros de fechas.
3. **Guard exclusivo:** `require_director` — si el rol no es `director`, se devuelve 403 sin invocar ningún servicio.
4. **Obtención de totales globales:** `RentabilityService.get_summary() → get_global_totals(db, date_from, date_to)`.
5. **Distribución por proyecto:** `get_profitability_by_project(db)` → agrega en `_build_rows()`.
6. **Enriquecimiento:** `get_project_meta(db, project_ids)` obtiene nombres y clientes en un solo query adicional.
7. **Cálculo de estado:** `_pct()` y `_status()` determinan el estado de cada proyecto.
8. **Respuesta:** JSON con resumen global y distribución de estados.
9. **Presentación:** `Rentability.jsx` renderiza gráficos y tabla. El actor puede desglosar en líneas analíticas por proyecto o cliente.

### Correspondencia con requisitos

| Requisito del caso de uso | Clase responsable | Método / Colaboración |
|---|---|---|
| Acceso exclusivo al Director | `metrics.router` | Guard `require_director` |
| Calcular rentabilidad global | `RentabilityService` | `get_summary()` → `get_global_totals()` |
| Desglosar por proyecto | `RentabilityService` | `get_per_project()` → `get_profitability_by_project()` |
| Desglosar por cliente | `RentabilityService` | `get_per_client()` → agrega proyectos por `partner_id` |
| Desglosar por responsable | `RentabilityService` | `get_by_manager()` → filtra por `manager_id` |
| Ver líneas analíticas de proyecto | `RentabilityService` | `get_project_lines()` → `get_account_analytic_lines()` |
| Ver líneas analíticas de cliente | `RentabilityService` | `get_client_lines()` → `get_client_analytic_lines()` |
| Encapsular importe vs. horas | `Timesheet` | `amount` (€) vs. `unit_amount` (h) |

---

## 6. CU-17 — Guardar snapshot

### Clases de análisis identificadas

#### Clases de Vista (Boundary)

**`SaveSnapshotButton.jsx` + `snapshots.router POST /snapshots/{colección}`**

Estereotipo: Vista (Boundary)

Responsabilidades:

- Presentar al actor un botón de guardado integrado en cualquier vista calculada del frontend principal (métricas, gráficos, fichas de entidad).
- Gestionar el ciclo de estados visible del botón: `idle → saving → saved / updated / error`.
- Distinguir visualmente entre una snapshot nueva (`created: true`, color verde) y una actualización de una ya existente ese mismo día (`created: false`, color ámbar).
- Enviar al backend el payload de la snapshot junto con su tipo (`metric`, `chart` o `entity`) y sus parámetros de contexto.
- Gestionar el error de red o de validación y mostrarlo al actor durante 3,5 segundos.

Colaboraciones:

- **Entrada:** recibe la acción del actor (clic en el botón).
- **Control:** se comunica con `SnapshotService` a través del router.
- **Salida:** muestra el resultado del guardado al actor.

---

#### Clases de Control

**`SnapshotService.save_metric() / save_chart() / save_entity()`**

Estereotipo: Control

Responsabilidades:

- Construir el objeto `actor` a partir del `CurrentUser` del token JWT (`user_id`, `employee_id`, `employee_name`, `role`).
- Determinar la fecha actual del snapshot (`_today()` en formato ISO).
- Calcular el hash de unicidad de los parámetros como `SHA-256[:16](json.dumps(params, sort_keys=True))` — solo para métricas y gráficos.
- Delegar en el repositorio la operación `upsert`: si ya existe un documento con la misma clave de unicidad y la misma fecha, actualizar; si no existe, insertar.
- Devolver el resultado `{id, created, snapshot_date}` al Boundary.

Colaboraciones:

- **Vista:** responde a `snapshots.router POST /snapshots/{métrica|gráfico|entidad}`.
- **Repositorio:** delega en `upsert_metric_snapshot()`, `upsert_chart_snapshot()` o `upsert_entity_snapshot()`.

---

#### Clases de Entidad (Entity) — Colecciones MongoDB

| Colección MongoDB | Rol en el caso de uso |
|---|---|
| `metric_snapshots` | Almacena capturas de métricas con índice único sobre `(metric_name, params_hash, snapshot_date)` |
| `chart_snapshots` | Almacena capturas de gráficos con índice único sobre `(chart_name, params_hash, snapshot_date)` |
| `entity_snapshots` | Almacena capturas de entidades con índice único sobre `(entity_type, entity_id, snapshot_date)` |

Los índices únicos de MongoDB garantizan la semántica de **una snapshot por día y combinación de parámetros** a nivel de base de datos, independientemente de la lógica de aplicación.

---

### Flujo de colaboración

#### Secuencia de operaciones

1. **Inicio:** el actor hace clic en `SaveSnapshotButton.jsx`. El botón pasa a estado `saving`.
2. **Construcción del payload:** el componente compone `{metric_name, params, data}` con los datos ya cargados en el estado de la vista.
3. **Petición HTTP:** `POST /snapshots/metrics` (o charts / entities).
4. **Guard:** `require_manager_or_above` extrae `CurrentUser`.
5. **Construcción del actor:** `SnapshotService._actor(cu)` construye el objeto de autoría.
6. **Hash de parámetros:** `_hash_params(params)` calcula el identificador de unicidad.
7. **Upsert en MongoDB:** `upsert_metric_snapshot()` ejecuta `update_one(match, {$set, $setOnInsert}, upsert=True)`.
8. **Resultado:** el repositorio devuelve `(oid, created, snap_date)`.
9. **Respuesta HTTP:** `{id, created, snapshot_date}` al Boundary.
10. **Actualización UI:** el botón pasa a estado `saved` (verde) o `updated` (ámbar) durante 2,2 segundos.

### Correspondencia con requisitos

| Requisito del caso de uso | Clase responsable | Método / Colaboración |
|---|---|---|
| Guardar snapshot de métrica | `SnapshotService` | `save_metric(cu, metric_name, params, data)` |
| Guardar snapshot de gráfico | `SnapshotService` | `save_chart(cu, chart_name, params, data)` |
| Guardar snapshot de entidad | `SnapshotService` | `save_entity(cu, entity_type, entity_id, data)` |
| Unicidad diaria por parámetros | Colección MongoDB | Índice único + `upsert_*_snapshot()` |
| Identificar al autor del snapshot | `SnapshotService` | `_actor(cu)` construye objeto de autoría |
| Feedback visual al actor | `SaveSnapshotButton.jsx` | Ciclo de estados `idle → saving → saved/updated/error` |
| Acceso solo para responsable o director | `snapshots.router` | Guard `require_manager_or_above` |

### Decisión de análisis relevante

La semántica de **upsert diario** —insertar si no existe, actualizar si ya existe para ese día y esos parámetros— se implementa a dos niveles complementarios: en el Control (que calcula el hash y construye el `match`), y en las Entidades documentales (que tienen índice único que lo garantiza a nivel de almacenamiento). Esta doble garantía asegura que, incluso ante condiciones de carrera, la invariante de unicidad diaria se preserva.

---

## 7. CU-19 — Consultar detalle de snapshot

### Clases de análisis identificadas

#### Clases de Vista (Boundary)

**`SnapshotDetail.jsx` (frontend2, puerto 3001) + `snapshots.router GET /snapshots/{colección}/{id}`**

Estereotipo: Vista (Boundary)

Responsabilidades:

- Recibir la solicitud de apertura del detalle de una snapshot concreta, identificada por su `ObjectId` de MongoDB.
- Determinar el tipo de snapshot (`metric`, `chart` o `entity`) a partir de la ruta de navegación.
- Presentar la cabecera con metadatos (tipo, nombre, fecha del snapshot, actor, hash de parámetros).
- Despachar el contenido del campo `data` al renderizador visual apropiado: `MetricVisualizer`, `ChartVisualizer` o `EntityVisualizer`.
- Ofrecer al actor un toggle para ver el JSON completo del documento MongoDB.
- Exponer el botón de eliminación permanente de la snapshot.

Colaboraciones:

- **Entrada:** recibe la solicitud del actor desde `MetricSnapshots.jsx`, `ChartSnapshots.jsx` o `EntitySnapshots.jsx`.
- **Control:** se comunica con `SnapshotService` a través del router.
- **Salida:** presenta la snapshot reconstruida visualmente.

---

#### Clases de Control

**`SnapshotService.get_metric() / get_chart() / get_entity()`**

Estereotipo: Control

Responsabilidades:

- Validar que el `id` recibido es un `ObjectId` de MongoDB válido.
- Delegar en el repositorio la búsqueda del documento por `_id`.
- Si el documento no existe, lanzar `HTTPException(404)` con el mensaje adecuado.
- Serializar el documento MongoDB (convirtiendo `_id` → `id` como string) y devolverlo al Boundary.

Colaboraciones:

- **Vista:** responde a `snapshots.router GET /snapshots/{colección}/{id}`.
- **Repositorio:** delega en `get_metric_snapshot(id_str)`, `get_chart_snapshot(id_str)` o `get_entity_snapshot(id_str)`.

---

#### Clases de Entidad (Entity) — Colecciones MongoDB

| Colección MongoDB | Campos consultados |
|---|---|
| `metric_snapshots` | `_id`, `metric_name`, `params`, `params_hash`, `snapshot_date`, `data`, `created_at`, `created_by`, `updated_at`, `updated_by` |
| `chart_snapshots` | `_id`, `chart_name`, `params`, `params_hash`, `snapshot_date`, `data`, `created_at/by`, `updated_at/by` |
| `entity_snapshots` | `_id`, `entity_type`, `entity_id`, `snapshot_date`, `data`, `created_at/by`, `updated_at/by` |

---

### Flujo de colaboración

#### Secuencia de operaciones

1. **Inicio:** el actor hace clic en una fila del listado de snapshots → `SnapshotDetail.jsx` recibe el `id` por la URL.
2. **Petición HTTP:** `GET /snapshots/metricas/{id}` (o graficos / entidades).
3. **Guard:** `require_manager_or_above` extrae `CurrentUser`.
4. **Validación del ID:** `_oid(id_str)` convierte el string a `ObjectId`; si falla, lanza `ValueError → 422`.
5. **Búsqueda en MongoDB:** `coll.find_one({"_id": oid})`.
6. **Serialización:** `_serialize(doc)` convierte `_id` → `id` (string).
7. **Verificación de existencia:** si `doc is None`, `SnapshotService` lanza `HTTPException(404)`.
8. **Respuesta:** JSON completo del documento al Boundary.
9. **Despacho visual:** `SnapshotDetail.jsx` selecciona `MetricVisualizer`, `ChartVisualizer` o `EntityVisualizer` según el tipo.
10. **Renderizado:** el renderizador interpreta `snap.data` y presenta gráficos, fichas o indicadores adaptados al subtipo de snapshot.

### Correspondencia con requisitos

| Requisito del caso de uso | Clase responsable | Método / Colaboración |
|---|---|---|
| Recuperar snapshot por ID | `SnapshotService` | `get_metric/chart/entity(id_str)` |
| Validar ObjectId de MongoDB | Repositorio | `_oid(id_str)` → `bson.ObjectId` |
| Manejar snapshot inexistente | `SnapshotService` | `HTTPException(404)` si `doc is None` |
| Renderizar métrica visualmente | `MetricVisualizer` | Interpreta `snap.data` según `metric_name` |
| Renderizar gráfico visualmente | `ChartVisualizer` | Interpreta `snap.data` según `chart_name` |
| Renderizar entidad visualmente | `EntityVisualizer` | Interpreta `snap.data` según `entity_type` |
| Ver JSON completo del documento | `SnapshotDetail.jsx` | Toggle `showJson` → `JsonViewer` |
| Eliminar snapshot permanentemente | `SnapshotService` | `delete_metric/chart/entity(id_str)` → CU-20 |

### Decisión de análisis relevante

El visor de capturas (`frontend2`, puerto 3001) opera **exclusivamente sobre datos guardados en MongoDB**, sin realizar ninguna llamada a los endpoints de cálculo sobre el ERP. Este desacoplamiento es una decisión de análisis: garantiza que una snapshot mantenga su representación visual original aunque los algoritmos del frontend principal evolucionen en el futuro. Los renderizadores (`MetricVisualizer`, `ChartVisualizer`, `EntityVisualizer`) son Boundaries especializados que conocen la estructura del campo `data` pero son totalmente independientes de la lógica de cálculo.

---

## 8. Patrón de colaboración establecido

Todos los casos de uso del sistema siguen el mismo patrón metodológico base, lo que garantiza consistencia arquitectónica y facilita el mantenimiento:

### Patrón estándar de colaboración

```
Actor autenticado
    │
    ▼
[Vista (Boundary)] ← FastAPI Router / Componente React
    │  Valida parámetros HTTP / captura entrada de usuario
    │
    ▼
[Guard de autenticación y rol]
    │  require_manager_or_above / require_director
    │
    ▼
[Control (Servicio)]
    │  Aplica ámbito, orquesta, aplica reglas de negocio
    │
    ▼
[Repositorio]
    │  Construye y ejecuta queries SQLAlchemy o MongoDB
    │
    ▼
[Entidad]
    PostgreSQL (hr_employee, project_task, …)  →  Solo lectura
    MongoDB (metric_snapshots, …)              →  Lectura/escritura
```

### Características del análisis aplicadas en todos los casos de uso

**Separación estricta de responsabilidades:**

- La Vista solo conoce HTTP y presentación; nunca toma decisiones de negocio.
- El Control conoce las reglas de negocio y el ámbito del usuario; nunca construye queries directamente.
- El Repositorio conoce el esquema de base de datos; nunca aplica reglas de negocio.
- La Entidad solo declara estructura; nunca ejecuta lógica.

**Agnóstico tecnológicamente en el análisis:**

- Las clases de análisis no asumen que la base de datos es PostgreSQL ni que el ORM es SQLAlchemy.
- No asumen que el frontend es React ni que el protocolo es HTTP/JSON.
- Estas decisiones pertenecen al diseño.

**Trazabilidad completa:**

- Cada clase de análisis tiene origen en un caso de uso detallado.
- Cada clase tiene destino en una clase de diseño y en un artefacto de código real.

**Patrón Repository aplicado uniformemente:**

- Todos los repositorios abstraen el acceso a datos, permitiendo cambiar la implementación sin afectar al Control.
- Los repositorios de métricas (`repositories/metrics/`) extienden este patrón con un submódulo por métrica: cada función de repositorio tiene una única razón de cambio.

---

## 9. Tabla resumen de trazabilidad

| CU | Vista React (Boundary) | Router FastAPI (Boundary) | Control (Servicio) | Entidades principales | Colección MongoDB | Guard |
|---|---|---|---|---|---|---|
| **CU-02** | `Employees.jsx` | `GET /employees/` | `EmployeeService.list_employees` | `Employee`, `Department` | — | `require_manager_or_above` |
| **CU-03** | `EmployeeDetail.jsx` | `GET /employees/{id}` + `GET /dashboards/summary/employee/{id}` | `DashboardService` (orquesta `WorkloadService`, `WIPService`, `ProductivityService`) | `Employee`, `Task`, `Timesheet`, `TaskAssignment`, `TaskStage` | — | `require_manager_or_above` |
| **CU-08** | `Tasks.jsx` | `GET /tasks/filter` | `TaskService.filter_tasks` | `Task`, `TaskStage`, `Project`, `Employee`, `TaskAssignment`, `Timesheet` | — | `require_manager_or_above` |
| **CU-13** | `Rentability.jsx` | `GET /metrics/profitability/*` | `RentabilityService` | `Timesheet`, `Project`, `Employee`, `Partner` | — | **`require_director`** |
| **CU-17** | `SaveSnapshotButton.jsx` | `POST /snapshots/{métrica\|gráfico\|entidad}` | `SnapshotService.save_metric/chart/entity` | — | `metric_snapshots`, `chart_snapshots`, `entity_snapshots` | `require_manager_or_above` |
| **CU-19** | `SnapshotDetail.jsx` (frontend2) | `GET /snapshots/{métrica\|gráfico\|entidad}/{id}` | `SnapshotService.get_metric/chart/entity` | — | (colección según tipo) | `require_manager_or_above` |

