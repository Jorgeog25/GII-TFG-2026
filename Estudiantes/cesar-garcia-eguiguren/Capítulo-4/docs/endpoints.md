# Catálogo de endpoints de la API

La API REST expone **47 endpoints activos** agrupados en 10 routers. Todos los endpoints sin uso han sido eliminados del backend y del código frontend en la refactorización documentada en esta sección.

## Auth — `/auth`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `POST` | `/auth/token` | `Login.jsx` | Envía credenciales, recibe JWT y datos del usuario (rol, scope) para inicializar el contexto de autenticación. |
| `GET` | `/auth/me` | ⚠️ *Sin uso* | — |

> **Endpoint sin uso:** `GET /auth/me` devuelve los datos del usuario autenticado, pero toda esa información ya viaja codificada en el JWT y se almacena en el contexto React al hacer login. Se puede **eliminar** sin impacto.

---

## Employees — `/employees`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/employees/` | `Employees.jsx`, `Metrics.jsx`, `Attendance.jsx`, `Charts.jsx` | En `Employees.jsx`: listado paginado con filtros + `BulkSaveButton` (`page_size=200`). En `Metrics.jsx`: pobla el selector de empleado de las tarjetas que requieren `employee_id` (L757, `page_size=200`). En `Attendance.jsx`: pobla el selector de empleado del modo comparativa (L260, `page_size=200`). En `Charts.jsx`: pobla el selector "Por empleado" del gráfico de evolución de tareas (L120). |
| `GET` | `/employees/{employee_id}` | `EmployeeDetail.jsx` | Recupera la ficha completa del empleado (coste/hora, cargo, KPIs asociados). |
| `GET` | `/employees/managers/list` | `Attendance.jsx` | Pobla el selector desplegable de responsables en el modo "Por responsable" de la vista de asistencia (`getManagers`). |
| `GET` | `/employees/managers/profitability` | `Rentability.jsx` | Pobla el selector de responsables del panel de rentabilidad para filtrar por manager (`getProfitabilityManagers`). |
| `GET` | `/employees/attendance/comparison` | `Attendance.jsx` | Carga la tabla comparativa global entre horas fichadas y horas imputadas en timesheets, con filtro por departamento (`getHoursComparison`). |
| `GET` | `/employees/attendance/{employee_id}/daily` | `Attendance.jsx` | Carga la serie diaria de horas fichadas/imputadas de un empleado concreto al hacer clic en su fila (`getEmployeeDaily`). |
| `GET` | `/employees/attendance/manager/{manager_id}` | `Attendance.jsx` | Carga la comparativa de asistencia filtrada por el equipo a cargo del manager seleccionado en el modo "Por responsable" (`getManagerView`). |

---

## Departments — `/departments`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/departments/` | `Departments.jsx`, `Employees.jsx`, `Attendance.jsx`, `Charts.jsx`, `Manager.jsx` | En `Departments.jsx`: cuadrícula de tarjetas + `BulkSaveButton`. En `Employees.jsx`: selector de filtro "Por departamento". En `Attendance.jsx`: selector de departamento del modo comparativa (L259). En `Charts.jsx`: selector "Por departamento" del gráfico de evolución (L121). En `Manager.jsx`: selector de departamento para filtrar el equipo del mánager (L115). |
| `GET` | `/departments/{department_id}` | `DepartmentDetail.jsx` | Recupera los datos del departamento (nombre, responsable, número de empleados). |
| `GET` | `/departments/{department_id}/employees` | `DepartmentDetail.jsx` | Muestra la pestaña "Equipo" con todos los miembros del departamento y sus métricas básicas. |
| `GET` | `/departments/{department_id}/workload-summary` | `DepartmentDetail.jsx` | Muestra la distribución de carga de trabajo (carga normal / sobrecargado / subcargado) del equipo. |

---

## Projects — `/projects`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/projects/` | `Projects.jsx`, `Tasks.jsx`, `Metrics.jsx`, `Charts.jsx`, `Rentability.jsx` | En `Projects.jsx`: cuadrícula por scope + `BulkSaveButton`. En `Tasks.jsx`: selector "Todos los proyectos" de la barra de filtros. En `Metrics.jsx`: selector de proyecto de las tarjetas que requieren `project_id` (L756). En `Charts.jsx`: selector "Por proyecto" del gráfico de evolución (L122). En `Rentability.jsx`: selector de proyecto del panel de rentabilidad (L271). |
| `GET` | `/projects/{project_id}` | `ProjectDetail.jsx` | Recupera los datos del proyecto (cliente, fechas, presupuesto de horas). |
| `GET` | `/projects/{project_id}/employees` | `ProjectDetail.jsx` | Muestra la pestaña "Equipo" con los empleados asignados al proyecto. |
| `GET` | `/projects/{project_id}/tasks` | `ProjectDetail.jsx` | Muestra la pestaña "Tareas" con el listado paginado de tareas del proyecto. |

---

## Tasks — `/tasks`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/tasks/filter` | `Tasks.jsx`, `EmployeeDetail.jsx`, `ProjectDetail.jsx` | En `Tasks.jsx`: listado paginado con filtros combinables (estado, etapa, proyecto, fechas, empleado). En `EmployeeDetail.jsx`: carga lazy de cada pestaña (Pendientes / Completadas / Asignadas / Responsable) con `employee_id` y `status`. En `ProjectDetail.jsx`: carga lazy de la pestaña "Tareas" con `project_id`. |
| `GET` | `/tasks/stages` | `Tasks.jsx`, `ProjectDetail.jsx` | En `Tasks.jsx`: pobla el selector "Todas las etapas" de la barra de filtros. En `ProjectDetail.jsx`: pobla el selector de etapa de la pestaña "Tareas" del detalle de proyecto (L48, `getStages`). |
| `GET` | `/tasks/{task_id}` | `TaskDetail.jsx` | Recupera el detalle de la tarea: horas gastadas, subtareas, asignados y fecha límite. |

---

## Search — `/search`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/search/` | `Search.jsx` | Ejecuta la búsqueda full-text transversal sobre empleados, proyectos y tareas y devuelve resultados agrupados. |

---

## Dashboards — `/dashboards`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/dashboards/overview` | `Overview.jsx` | Carga los contadores globales del sistema (proyectos activos, empleados, tareas abiertas y vencidas) para las 4 tarjetas KPI de la pantalla de inicio (`getSystemOverview`). |
| `GET` | `/dashboards/summary/employee/{employee_id}` | `EmployeeDetail.jsx` | Obtiene los KPIs rápidos del empleado (WIP, tareas pendientes, productividad 30d) para las tarjetas superiores y la sección de hoy (`getEmployeeSummary`). |
| `GET` | `/dashboards/summary/manager` | `Manager.jsx`, `Overview.jsx` | En `Manager.jsx`: dos llamadas paralelas — una para el resumen de estados (KPIs y gráfico) y otra paginada con filtro de estado para el listado de empleados. En `Overview.jsx`: llamada sin filtros para poblar el widget de distribución del equipo y las alertas de sobrecarga (`getManagerSummary`). |
| `GET` | `/dashboards/summary/project/{project_id}` | `ProjectDetail.jsx` | Obtiene los KPIs del proyecto (eficiencia, índice de riesgo, rentabilidad, health score) para las 4 tarjetas de cabecera y las barras de progreso (`getProjectSummary`). |

---

## Charts — `/charts`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/charts/task-distribution` | `Charts.jsx`, `Overview.jsx` | En `Charts.jsx`: alimenta el gráfico de tarta de distribución por etapa Kanban con filtro de fecha. En `Overview.jsx`: alimenta el widget "Distribución por estado" del panel de inicio (`getTaskDistribution`). |
| `GET` | `/charts/productivity-trend` | `Charts.jsx`, `Overview.jsx` | En `Charts.jsx`: no se llama directamente (solo se importa). En `Overview.jsx`: alimenta el gráfico de área "Actividad — últimas 2 semanas" con los últimos 14 días de horas imputadas (`getProductivityTrend`). |
| `GET` | `/charts/task-evolution` | `Charts.jsx` | Alimenta el gráfico de líneas "Evolución de Tareas" con series de tareas completadas, abiertas y vencidas agrupadas por mes o semana, con filtro por empleado/departamento/proyecto (`getTaskEvolution`). |

---

## Metrics — `/metrics`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/metrics/productivity` | `Metrics.jsx`, `Overview.jsx` | En `Metrics.jsx`: tarjeta "Productividad" — calcula productividad media y top tareas por empleado/período (`getProductivity`). En `Overview.jsx`: no consumido directamente. |
| `GET` | `/metrics/compliance` | `Metrics.jsx`, `Overview.jsx` | En `Metrics.jsx`: tarjeta "Cumplimiento" — muestra tasa de tareas cerradas en plazo con gráfico de tarta. En `Overview.jsx`: alimenta el widget "Estado operativo" con el indicador de cumplimiento (`getCompliance`). |
| `GET` | `/metrics/workload` | `Metrics.jsx` | Tarjeta "Carga de trabajo" — muestra % de ocupación, horas pendientes y estado (normal/sobrecargado/subcargado) de un empleado seleccionado (`getWorkload`). |
| `GET` | `/metrics/project-efficiency` | `Metrics.jsx` | Tarjeta "Eficiencia proyecto" — compara horas planificadas vs reales con gráfico de barras y calcula índice de eficiencia para el proyecto seleccionado (`getProjectEfficiency`). |
| `GET` | `/metrics/profitability` | `Metrics.jsx` | Tarjeta "Rentabilidad por horas estimadas/reales" — calcula el margen de rentabilidad estimado comparando coste estimado vs coste real para el proyecto seleccionado (`getProfitability`). |
| `GET` | `/metrics/lead-time` | `Metrics.jsx`, `Overview.jsx` | En `Metrics.jsx`: tarjeta "Lead Time" — muestra días promedio desde asignación hasta cierre. En `Overview.jsx`: alimenta el indicador "Lead Time" del widget de salud (`getLeadTime`). |
| `GET` | `/metrics/risk-index` | `Metrics.jsx` | Tarjeta "Índice de riesgo" — calcula el porcentaje de tareas abiertas en riesgo de retraso para el proyecto seleccionado (`getRiskIndex`). |
| `GET` | `/metrics/estimation-accuracy` | `Metrics.jsx` | Tarjeta "Exactitud estimación" — mide el ratio entre horas estimadas y horas reales e informa del sesgo (subestima/sobreestima/preciso) para el empleado seleccionado (`getEstimationAccuracy`). |
| `GET` | `/metrics/client-distribution` | `Metrics.jsx`, `Charts.jsx` | En `Metrics.jsx`: tarjeta "Dist. clientes" (solo Director) — muestra distribución de horas por cliente. En `Charts.jsx`: alimenta el gráfico de barras horizontales "Distribución por Cliente" (`getClientDistribution`). |
| `GET` | `/metrics/wip` | `Metrics.jsx` | Tarjeta "WIP" — cuenta las tareas actualmente en curso del empleado seleccionado y emite una recomendación sobre paralelismo (`getWIP`). |
| `GET` | `/metrics/rework-rate` | `Metrics.jsx`, `Overview.jsx` | En `Metrics.jsx`: tarjeta "Tasa retrabajo" — mide el % de tareas reabiertos sobre el total cerrado. En `Overview.jsx`: alimenta el indicador "Retrabajo" del widget de salud operativo (`getReworkRate`). |
| `GET` | `/metrics/profitability/summary` | `Rentability.jsx` | Carga el resumen financiero global con margen total, ingresos y costes del período. |
| `GET` | `/metrics/profitability/per-project` | `Rentability.jsx` | Desglosa el margen por proyecto en la tabla principal del panel de rentabilidad. |
| `GET` | `/metrics/profitability/per-client` | `Rentability.jsx` | Desglosa el margen por cliente en la vista de rentabilidad por cliente. |
| `GET` | `/metrics/profitability/manager/{manager_id}` | `Rentability.jsx` | Filtra todos los datos del panel de rentabilidad por el manager seleccionado. |
| `GET` | `/metrics/profitability/per-project/{project_id}/lines` | `Rentability.jsx` | Carga las líneas analíticas contables al hacer clic en un proyecto para ver su detalle financiero. |
| `GET` | `/metrics/profitability/per-client/{client_id}/lines` | `Rentability.jsx` | Carga las líneas analíticas contables al hacer clic en un cliente para ver su detalle financiero. |

---

## Snapshots — `/snapshots`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/snapshots/stats` | `Home.jsx` (frontend2) | Carga los contadores de MongoDB (total métricas, gráficos y entidades guardados) para el panel inicial. |
| `POST` | `/snapshots/metrics` | `Metrics.jsx` | Guarda (upsert diario) la fotografía de una métrica al pulsar el botón "Guardar snapshot". |
| `GET` | `/snapshots/metrics` | `MetricSnapshots.jsx` (frontend2) | Lista el histórico paginado de snapshots de métricas con filtros por nombre y fecha. |
| `GET` | `/snapshots/metrics/{snapshot_id}` | `SnapshotDetail.jsx` (frontend2) | Recupera y renderiza el JSON completo del snapshot seleccionado tal como fue guardado. |
| `DELETE` | `/snapshots/metrics/{snapshot_id}` | `MetricSnapshots.jsx` (frontend2) | Elimina el snapshot tras confirmación en el modal de borrado. |
| `POST` | `/snapshots/charts` | `Charts.jsx` | Guarda (upsert diario) la fotografía de un gráfico al pulsar "Guardar snapshot". |
| `GET` | `/snapshots/charts` | `ChartSnapshots.jsx` (frontend2) | Lista el histórico paginado de snapshots de gráficos con filtros por nombre y fecha. |
| `GET` | `/snapshots/charts/{snapshot_id}` | `SnapshotDetail.jsx` (frontend2) | Recupera y renderiza el JSON completo del snapshot de gráfico seleccionado. |
| `DELETE` | `/snapshots/charts/{snapshot_id}` | `ChartSnapshots.jsx` (frontend2) | Elimina el snapshot de gráfico tras confirmación en el modal de borrado. |
| `POST` | `/snapshots/entities` | `EmployeeDetail.jsx`, `ProjectDetail.jsx`, `DepartmentDetail.jsx`, `Employees.jsx`, `Departments.jsx`, `Projects.jsx`, `Tasks.jsx` | En las fichas de detalle: `SaveSnapshotButton` guarda un snapshot individual. En los listados: `BulkSaveButton` llama a `saveEntitySnapshot` en bucle para guardar todos los registros del tipo correspondiente. |
| `GET` | `/snapshots/entities` | `EntitySnapshots.jsx` (frontend2) | Lista el histórico paginado de snapshots de entidades con filtros por tipo e id. |
| `GET` | `/snapshots/entities/{snapshot_id}` | `SnapshotDetail.jsx` (frontend2) | Recupera y renderiza el JSON completo del snapshot de entidad seleccionado. |
| `DELETE` | `/snapshots/entities/{snapshot_id}` | `EntitySnapshots.jsx` (frontend2) | Elimina el snapshot de entidad tras confirmación en el modal de borrado. |

