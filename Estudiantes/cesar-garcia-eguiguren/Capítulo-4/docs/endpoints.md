# Catálogo de endpoints de la API

La API REST expone **47 endpoints activos** agrupados en 10 routers. Todos los endpoints sin uso han sido eliminados del backend y del código frontend en la refactorización documentada en esta sección.

## Auth — `/auth`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `POST` | `/auth/token` | `Login.jsx` | Envía las credenciales del usuario (login y contraseña) y recibe el JWT junto con los datos de sesión (rol, scope de empleados, departamentos y proyectos accesibles) para inicializar el contexto de autenticación de la aplicación. |
| `GET` | `/auth/me` | ⚠️ *Sin uso* | — |

> **Endpoint sin uso:** `GET /auth/me` devuelve los datos del usuario autenticado, pero toda esa información ya viaja codificada en el JWT y se almacena en el contexto React al hacer login. Se puede **eliminar** sin impacto.

---

## Employees — `/employees`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/employees/` | `Employees.jsx`, `Metrics.jsx`, `Charts.jsx` | En `Employees.jsx`: listado paginado de empleados con filtros por nombre, departamento y estado activo. En `Metrics.jsx`: pobla el selector de empleado para las métricas que requieren un empleado concreto (WIP, carga de trabajo, exactitud de estimación). En `Charts.jsx`: pobla el selector de entidad "Por empleado" del gráfico de evolución de tareas. |
| `GET` | `/employees/{employee_id}` | `EmployeeDetail.jsx` | Recupera la ficha completa del empleado: nombre, cargo, departamento, coste por hora y correo electrónico. Se usa para construir la cabecera de la página de detalle. |
| `GET` | `/employees/managers/list` | `Attendance.jsx` | Pobla el selector desplegable de responsables en el modo de vista "Por responsable" de la página de asistencia, permitiendo filtrar la comparativa de horas por el equipo a cargo de un manager concreto. |
| `GET` | `/employees/managers/profitability` | `Rentability.jsx` | Pobla el selector de responsables del panel de rentabilidad para filtrar los datos financieros por el manager que gestiona cada proyecto. |
| `GET` | `/employees/attendance/comparison` | `Attendance.jsx` | Carga la tabla comparativa global entre horas fichadas en el control de presencia y horas imputadas en timesheets, con posibilidad de filtrar por departamento o empleado individual. |
| `GET` | `/employees/attendance/{employee_id}/daily` | `Attendance.jsx` | Carga la serie temporal diaria de horas fichadas e imputadas de un empleado concreto, que se muestra como un panel de detalle al hacer clic sobre su fila en la tabla comparativa. |
| `GET` | `/employees/attendance/manager/{manager_id}` | `Attendance.jsx` | Carga la comparativa de asistencia filtrada al equipo jerárquico del manager seleccionado, incluyendo a sus subordinados directos e indirectos. |

---

## Departments — `/departments`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/departments/` | `Departments.jsx`, `Employees.jsx`, `Attendance.jsx`, `Charts.jsx`, `Manager.jsx` | En `Departments.jsx`: cuadrícula de tarjetas de departamentos con botón de guardado masivo de snapshots. En `Employees.jsx`: pobla el selector de filtro por departamento del listado de empleados. En `Attendance.jsx`: pobla el selector de departamento del modo de comparativa de asistencia. En `Charts.jsx`: pobla el selector "Por departamento" del gráfico de evolución de tareas. En `Manager.jsx`: pobla el selector de departamento para filtrar el equipo visible en el panel de manager. |
| `GET` | `/departments/{department_id}` | `DepartmentDetail.jsx` | Recupera los datos básicos del departamento (nombre completo, manager asignado y departamento padre) para construir la cabecera de la página de detalle. |
| `GET` | `/departments/{department_id}/employees` | `DepartmentDetail.jsx` | Muestra la pestaña "Empleados" con la lista completa de miembros del departamento, incluyendo cargo, correo y coste por hora. |
| `GET` | `/departments/{department_id}/workload-summary` | `DepartmentDetail.jsx` | Muestra la pestaña "Carga de trabajo" con la distribución del equipo por estado de ocupación (normal, sobrecargado, subcargado, sin tareas) y el detalle de horas pendientes por empleado. |

---

## Projects — `/projects`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/projects/` | `Projects.jsx`, `Tasks.jsx`, `Metrics.jsx`, `Charts.jsx`, `Rentability.jsx` | En `Projects.jsx`: cuadrícula de tarjetas de proyectos activos con botón de guardado masivo de snapshots. En `Tasks.jsx`: pobla el selector de proyecto de la barra de filtros del listado de tareas. En `Metrics.jsx`: pobla el selector de proyecto para las métricas que requieren un proyecto concreto (eficiencia, riesgo, rentabilidad). En `Charts.jsx`: pobla el selector "Por proyecto" del gráfico de evolución de tareas. En `Rentability.jsx`: pobla el selector de proyecto del panel de rentabilidad financiera. |
| `GET` | `/projects/{project_id}` | `ProjectDetail.jsx` | Recupera los datos básicos del proyecto (nombre, cliente, código y estado activo) para construir la cabecera de la página de detalle. |
| `GET` | `/projects/{project_id}/employees` | `ProjectDetail.jsx` | Muestra la pestaña "Equipo" con los empleados que han registrado horas en el proyecto, sus horas totales imputadas y número de tareas asignadas. Los mismos datos se usan también para componer el snapshot del proyecto. |
| `GET` | `/projects/{project_id}/tasks` | `ProjectDetail.jsx` | Muestra la pestaña "Tareas" con el listado completo de tareas del proyecto, con filtros por estado y etapa. Los mismos datos se usan también para componer el snapshot del proyecto. |

---

## Tasks — `/tasks`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/tasks/filter` | `Tasks.jsx`, `EmployeeDetail.jsx` | En `Tasks.jsx`: listado paginado de tareas con filtros combinables por estado, etapa, proyecto, rango de fechas y empleado. En `EmployeeDetail.jsx`: carga bajo demanda el contenido de cada pestaña (Pendientes, Completadas, Asignadas, Responsable) al activarla, pasando el `employee_id` y el `status` correspondiente para obtener solo las tareas relevantes. |
| `GET` | `/tasks/stages` | `Tasks.jsx`, `ProjectDetail.jsx` | En `Tasks.jsx`: pobla el selector de etapa exacta de la barra de filtros del listado de tareas. En `ProjectDetail.jsx`: pobla el selector de etapa del filtro de la pestaña "Tareas" del detalle de proyecto. |
| `GET` | `/tasks/{task_id}` | `TaskDetail.jsx` | Recupera el detalle completo de una tarea: horas estimadas, horas trabajadas, horas pendientes, etapa, fechas, responsable, empleados asignados y subtareas anidadas. |

---

## Search — `/search`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/search/` | `Search.jsx` | Ejecuta la búsqueda full-text transversal sobre empleados, proyectos y tareas y devuelve resultados agrupados por tipo de entidad, con posibilidad de acotar la búsqueda a un solo tipo. |

---

## Dashboards — `/dashboards`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/dashboards/overview` | `Overview.jsx` | Carga los contadores globales del sistema (proyectos activos, empleados, tareas abiertas y tareas vencidas) para las tarjetas KPI de la pantalla de inicio. |
| `GET` | `/dashboards/summary/employee/{employee_id}` | `EmployeeDetail.jsx` | Obtiene los KPIs rápidos del empleado (WIP actual, tareas pendientes y horas asociadas, productividad de los últimos 30 días) para las tarjetas de cabecera y el bloque de alertas diarias. |
| `GET` | `/dashboards/summary/manager` | `Manager.jsx`, `Overview.jsx` | En `Manager.jsx`: se realizan dos llamadas con parámetros distintos — una para obtener el resumen de estados del equipo completo (KPIs y gráfico de distribución) y otra paginada con filtro de estado concreto para cargar el listado de empleados al hacer clic en una categoría. En `Overview.jsx`: se llama sin filtros para poblar el widget de distribución del equipo y las alertas de empleados sobrecargados. |
| `GET` | `/dashboards/summary/project/{project_id}` | `ProjectDetail.jsx` | Obtiene los indicadores de salud del proyecto (índice de eficiencia, índice de riesgo, rentabilidad y health score global) para las cuatro tarjetas KPI de la cabecera y las barras de progreso de horas. |

---

## Charts — `/charts`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/charts/task-distribution` | `Charts.jsx`, `Overview.jsx` | En `Charts.jsx`: alimenta el gráfico de tarta de distribución de tareas por etapa Kanban, con filtro de rango de fechas. En `Overview.jsx`: alimenta el widget "Distribución por estado" del panel de inicio con la distribución actual de todas las tareas. |
| `GET` | `/charts/productivity-trend` | `Overview.jsx` | Alimenta el gráfico de área "Actividad — últimas 2 semanas" con la serie de horas imputadas diarias de los últimos 14 días. |
| `GET` | `/charts/task-evolution` | `Charts.jsx` | Alimenta el gráfico de líneas "Evolución de Tareas" con series de tareas completadas, abiertas y vencidas agrupadas por mes o semana, con filtro por empleado, departamento o proyecto. |

---

## Metrics — `/metrics`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/metrics/productivity` | `Metrics.jsx` | Tarjeta "Productividad" — calcula la productividad media de las tareas completadas (ratio horas planificadas / horas reales) y muestra un ranking de las tareas con mayor y menor productividad, con filtro por empleado, proyecto y período. |
| `GET` | `/metrics/compliance` | `Metrics.jsx`, `Overview.jsx` | En `Metrics.jsx`: tarjeta "Cumplimiento" — muestra la tasa de tareas cerradas dentro del plazo acordado con un gráfico de tarta. En `Overview.jsx`: alimenta el indicador de cumplimiento del widget "Estado operativo". |
| `GET` | `/metrics/workload` | `Metrics.jsx` | Tarjeta "Carga de trabajo" — muestra el porcentaje de ocupación de un empleado respecto a la jornada de referencia, las horas pendientes totales y el estado resultante (normal, sobrecargado o subcargado). |
| `GET` | `/metrics/project-efficiency` | `Metrics.jsx` | Tarjeta "Eficiencia proyecto" — compara las horas planificadas con las horas reales registradas para calcular el índice de eficiencia y la desviación absoluta y porcentual del proyecto seleccionado. |
| `GET` | `/metrics/profitability` | `Metrics.jsx` | Tarjeta "Rentabilidad por horas estimadas/reales" — calcula el margen de rentabilidad comparando el coste estimado (horas planificadas × coste/hora) con el coste real (horas imputadas × coste/hora) del proyecto seleccionado. |
| `GET` | `/metrics/lead-time` | `Metrics.jsx`, `Overview.jsx` | En `Metrics.jsx`: tarjeta "Lead Time" — muestra el número de días promedio transcurridos desde la asignación de una tarea hasta su cierre, con filtro por empleado o proyecto. En `Overview.jsx`: alimenta el indicador de lead time del widget de salud operativo. |
| `GET` | `/metrics/risk-index` | `Metrics.jsx` | Tarjeta "Índice de riesgo" — calcula el porcentaje de tareas abiertas que han superado su fecha límite o que han consumido más del 80% del tiempo disponible sin estar cerradas, sobre el total de tareas abiertas del proyecto seleccionado. |
| `GET` | `/metrics/estimation-accuracy` | `Metrics.jsx` | Tarjeta "Exactitud estimación" — mide el ratio medio entre horas planificadas y horas reales de las tareas cerradas del empleado seleccionado e informa del sesgo sistemático (subestima, sobreestima o preciso). |
| `GET` | `/metrics/client-distribution` | `Metrics.jsx`, `Charts.jsx` | En `Metrics.jsx`: tarjeta "Dist. clientes" (solo Director) — muestra la distribución porcentual de horas imputadas por cliente en el período seleccionado. En `Charts.jsx`: alimenta el gráfico de barras horizontales "Distribución por Cliente". |
| `GET` | `/metrics/wip` | `Metrics.jsx` | Tarjeta "WIP" — cuenta las tareas que el empleado seleccionado tiene actualmente abiertas y asignadas, evalúa si el nivel de paralelismo es óptimo, aceptable o excesivo y emite una recomendación de acción. |
| `GET` | `/metrics/rework-rate` | `Metrics.jsx`, `Overview.jsx` | En `Metrics.jsx`: tarjeta "Tasa retrabajo" — calcula el porcentaje de tareas que fueron reabiertas tras cerrarse sobre el total de tareas cerradas, con filtro por proyecto o empleado. En `Overview.jsx`: alimenta el indicador de retrabajo del widget de salud operativo. |
| `GET` | `/metrics/profitability/summary` | `Rentability.jsx` | Carga el resumen financiero global del período seleccionado: ingresos totales, gastos totales, neto y margen de rentabilidad, más el recuento de proyectos en ganancia, neutros y en pérdida. |
| `GET` | `/metrics/profitability/per-project` | `Rentability.jsx` | Desglosa el margen de rentabilidad proyecto a proyecto en la tabla principal del panel financiero, con posibilidad de filtrar por proyecto concreto o por manager. |
| `GET` | `/metrics/profitability/per-client` | `Rentability.jsx` | Desglosa el margen de rentabilidad por cliente, agrupando los proyectos de cada cliente y calculando los totales financieros consolidados. |
| `GET` | `/metrics/profitability/manager/{manager_id}` | `Rentability.jsx` | Carga el detalle financiero completo del manager seleccionado: sus proyectos, los totales de ingresos y gastos y la distribución de estados de rentabilidad de su cartera. |
| `GET` | `/metrics/profitability/per-project/{project_id}/lines` | `Rentability.jsx` | Carga las líneas analíticas contables individuales (ingresos y gastos) de un proyecto concreto al expandir su detalle en el panel de rentabilidad. |
| `GET` | `/metrics/profitability/per-client/{client_id}/lines` | `Rentability.jsx` | Carga las líneas analíticas contables individuales (ingresos y gastos) de un cliente concreto al expandir su detalle en el panel de rentabilidad. |

---

## Snapshots — `/snapshots`

| Método | Ruta | Vista | Uso en la vista |
|--------|------|-------|-----------------|
| `GET` | `/snapshots/stats` | `Home.jsx` (frontend2) | Carga los contadores de MongoDB (total de métricas, gráficos y entidades guardados, y cuántos se han guardado hoy) para el panel inicial del visor de snapshots. |
| `POST` | `/snapshots/metrics` | `Metrics.jsx` | Guarda mediante upsert diario la fotografía de una métrica calculada al pulsar el botón "Guardar snapshot", asociándola al usuario que la generó. |
| `GET` | `/snapshots/metrics` | `MetricSnapshots.jsx` (frontend2) | Lista el histórico paginado de snapshots de métricas guardados en MongoDB, con filtros por nombre de métrica y por fecha de snapshot. |
| `GET` | `/snapshots/metrics/{snapshot_id}` | `SnapshotDetail.jsx` (frontend2) | Recupera el contenido completo de un snapshot de métrica concreto y lo renderiza con visualizaciones gráficas interpretadas según el tipo de métrica. |
| `DELETE` | `/snapshots/metrics/{snapshot_id}` | `MetricSnapshots.jsx` (frontend2) | Elimina de forma permanente un snapshot de métrica tras confirmación del usuario. |
| `POST` | `/snapshots/charts` | `Charts.jsx` | Guarda mediante upsert diario la fotografía de un gráfico al pulsar "Guardar snapshot", incluyendo los parámetros de filtrado con los que fue generado. |
| `GET` | `/snapshots/charts` | `ChartSnapshots.jsx` (frontend2) | Lista el histórico paginado de snapshots de gráficos guardados en MongoDB, con filtros por nombre de gráfico y por fecha de snapshot. |
| `GET` | `/snapshots/charts/{snapshot_id}` | `SnapshotDetail.jsx` (frontend2) | Recupera el contenido completo de un snapshot de gráfico concreto y lo renderiza con la visualización Recharts correspondiente al tipo de gráfico guardado. |
| `DELETE` | `/snapshots/charts/{snapshot_id}` | `ChartSnapshots.jsx` (frontend2) | Elimina de forma permanente un snapshot de gráfico tras confirmación del usuario. |
| `POST` | `/snapshots/entities` | `EmployeeDetail.jsx`, `ProjectDetail.jsx`, `DepartmentDetail.jsx`, `Employees.jsx`, `Departments.jsx`, `Projects.jsx`, `Tasks.jsx` | En las páginas de detalle: el botón "Guardar snapshot" guarda el estado actual de la entidad individual (empleado, proyecto, departamento o tarea) con todos sus indicadores calculados. En los listados: el botón "Guardar todos" llama a este endpoint en bucle para persistir en MongoDB un snapshot de cada registro visible. |
| `GET` | `/snapshots/entities` | `EntitySnapshots.jsx` (frontend2) | Lista el histórico paginado de snapshots de entidades guardados en MongoDB, con filtros por tipo de entidad (proyecto, empleado, departamento o tarea) y por fecha de snapshot. |
| `GET` | `/snapshots/entities/{snapshot_id}` | `SnapshotDetail.jsx` (frontend2) | Recupera el contenido completo de un snapshot de entidad concreto y lo renderiza con una vista estructurada adaptada al tipo de entidad (ficha de empleado, métricas de proyecto, carga del departamento o detalle de tarea). |
| `DELETE` | `/snapshots/entities/{snapshot_id}` | `EntitySnapshots.jsx` (frontend2) | Elimina de forma permanente un snapshot de entidad tras confirmación del usuario. |