# Disciplina de Análisis

## Índice

1. [Análisis de la Arquitectura](#1-análisis-de-la-arquitectura)
   - 1.1 [Visión general del módulo](#11-visión-general-del-módulo)
   - 1.2 [Diagrama de arquitectura del sistema](#12-diagrama-de-arquitectura-del-sistema)
   - 1.3 [Restricción fundamental: solo lectura sobre Odoo](#13-restricción-fundamental-solo-lectura-sobre-odoo)
2. [Análisis de Casos de Uso](#2-análisis-de-casos-de-uso)
   - 2.1 [Actores](#21-actores)
   - 2.2 [Casos de uso del sistema](#22-casos-de-uso-del-sistema)
3. [Análisis de Clases](#3-análisis-de-clases)
   - 3.1 [Capas de la solución](#31-capas-de-la-solución)
   - 3.2 [Identificación de clases de análisis](#32-identificación-de-clases-de-análisis)
   - 3.3 [Relaciones entre clases del dominio](#33-relaciones-entre-clases-del-dominio)
4. [Análisis de Paquetes](#4-análisis-de-paquetes)
   - 4.1 [Estructura de paquetes del backend](#41-estructura-de-paquetes-del-backend)
   - 4.2 [Justificación de la estructura](#42-justificación-de-la-estructura)
   - 4.3 [Paquetes del frontend principal y del visor](#43-paquetes-del-frontend-principal-y-del-visor)


---

## 1. Análisis de la Arquitectura

### 1.1 Visión general del módulo

Netkia Analytics es un módulo externo de analítica construido sobre Odoo v16 Enterprise. Consta de **tres capas perfectamente desacopladas**:

- **Backend único** en FastAPI + Python 3.11, que accede a Odoo a través de su base PostgreSQL en **solo lectura** y persiste snapshots en **MongoDB** en lectura/escritura.
- **Frontend principal** en React 18 + Vite (puerto 3000), que calcula todos los paneles en tiempo real sobre Odoo y permite *generar* snapshots desde cualquier vista calculada (CU-17).
- **Visor de snapshots** en React 18 + Vite (puerto 3001), aplicación independiente de solo lectura que lista, visualiza y elimina snapshots (CU-18, CU-19, CU-20). Reutiliza el mismo esquema de autenticación JWT que el frontend principal.

La separación entre los dos frontends es intencional: el principal resuelve el caso de uso *operativo* (decisiones hoy sobre datos vivos); el visor resuelve el caso de uso *histórico* (consultar el estado de un panel en un día pasado).


### 1.2 Diagrama de arquitectura del sistema

El diagrama de componentes muestra la arquitectura física desplegada: el frontend principal y el visor se comunican con el mismo backend FastAPI a través de HTTP; el backend accede a la base de datos PostgreSQL de Odoo mediante SQLAlchemy en modo solo lectura y, adicionalmente, a MongoDB mediante `pymongo` para las colecciones de snapshots en modo lectura/escritura. El backend se organiza en capas (Routes, Services, Repositories, Models) que siguen principios de separación de responsabilidades y una única razón para cambiar.

![Diagrama de Componentes](./imagenes/paquetesSistema.png)

### 1.3 Restricción fundamental: solo lectura sobre Odoo

El acceso exclusivo de lectura a la base de datos de Odoo determina varias decisiones de arquitectura: no se utilizan patrones de escritura como Unit of Work ni transacciones de modificación sobre PostgreSQL; todos los modelos ORM mapean tablas existentes sin añadir columnas; y la lógica del módulo operativo se concentra en optimizar consultas y calcular métricas, no en persistir estado propio.

La única excepción a esta restricción es el subsistema de snapshots, que persiste sus propios documentos en MongoDB. MongoDB queda por completo fuera del espacio de Odoo: no existe integridad referencial real entre ambas bases, y la correspondencia entre una `EntitySnapshot` y la entidad operativa que retrata se mantiene de forma lógica mediante los campos `entity_type` y `entity_id`. De este modo, la restricción de solo lectura sobre Odoo se preserva íntegramente y el almacenamiento de snapshots queda encapsulado en un subsistema independiente.

---

## 2. Análisis de Casos de Uso

Esta sección describe qué sucede en cada caso de uso desde el punto de vista del actor, sin entrar en detalles de implementación. Para cada CU se indica el actor que lo inicia, los datos de entrada que proporciona y la información que el sistema devuelve.

Tras la aplicación de los principios de consolidación RUP documentados en el Capítulo 2, el sistema cuenta con **20 casos de uso** organizados en 10 paquetes funcionales.

### 2.1 Actores

| Actor | CUs disponibles | Descripción |
|---|---|---|
| **Director** | 20 | Acceso total. Ve datos de toda la organización: todos los empleados, proyectos, departamentos y métricas financieras. |
| **Responsable** | 17 | Acceso restringido a su ámbito (empleados de su equipo, proyectos que gestiona, departamentos a su cargo). No puede acceder a los casos de uso exclusivos de Rentabilidad Financiera (CU-13 y CU-14). |

### 2.2 Casos de uso del sistema

**CU-01 — Autenticarse**
- Actor: Director, Responsable
- Entrada: credenciales de usuario (login + contraseña Odoo)
- Salida: token JWT con rol y ámbito organizativo embebidos; perfil del usuario

**CU-02 — Listar empleados**
- Actor: Director, Responsable
- Entrada: filtros opcionales (departamento, búsqueda por nombre, estado activo/inactivo, ordenación, paginación)
- Salida: lista paginada de empleados con nombre, cargo, departamento, email y coste por hora

**CU-03 — Ver resumen de empleado**
- Actor: Director, Responsable
- Entrada: identificador del empleado
- Salida: ficha del empleado + indicadores de carga de trabajo (% ocupación, horas pendientes), WIP (tareas en paralelo), productividad de los últimos 30 días y contadores de tareas por estado

**CU-04 — Listar departamentos**
- Actor: Director, Responsable
- Entrada: ninguna (filtros opcionales de búsqueda)
- Salida: lista de departamentos activos con nombre, manager y jerarquía

**CU-05 — Ver detalle de departamento**
- Actor: Director, Responsable
- Entrada: identificador del departamento
- Salida: ficha del departamento + carga de trabajo del equipo (% por empleado, sobrecargados, subcargados, sin tareas) + listado de empleados

**CU-06 — Listar proyectos**
- Actor: Director, Responsable
- Entrada: ninguna
- Salida: lista de proyectos activos con nombre, cliente y código

**CU-07 — Ver detalle de proyecto**
- Actor: Director, Responsable
- Entrada: identificador del proyecto
- Salida: ficha del proyecto + métricas de eficiencia, riesgo y rentabilidad por horas + listado de tareas y equipo asignado

**CU-08 — Listar tareas**
- Actor: Director, Responsable
- Entrada: filtros combinables (estado, etapa, proyecto, departamento, fechas, empleado, responsable, solo-raíz, paginación, ordenación)
- Salida: lista paginada de tareas con nombre, etapa, horas estimadas, deadline y estado; las tareas filtradas por empleado incluyen horas trabajadas, pendientes y productividad según el estado seleccionado

**CU-09 — Ver detalle de tarea**
- Actor: Director, Responsable
- Entrada: identificador de la tarea
- Salida: toda la información de la tarea (fechas, horas, etapa, responsable, usuarios asignados, subtareas, progreso de horas, productividad)

**CU-10 — Consultar métrica operativa**
- Actor: Director, Responsable
- Entrada: `metric_name` (productividad, cumplimiento, WIP, workload, riesgo, retrabajo, estimación, lead time, tiempo por estado, tareas canceladas, tiempo por prioridad, eficiencia de proyecto, distribución por cliente…) y los parámetros contextuales que esa métrica requiera (empleado, proyecto, departamento, rango de fechas, `root_only`)
- Salida: KPI calculado con su gauge, gráficos de apoyo y descomposiciones específicas de la métrica seleccionada. El modo Workload admite parámetros individuales (`employee_id`) o agregados de equipo (`department_id` o sin parámetro), lo que sustituye al antiguo panel manager como otra variante del mismo caso de uso

**CU-11 — Consultar gráficos analíticos**
- Actor: Director, Responsable
- Entrada: rango de fechas, agrupación (semana/mes), filtros de entidad (empleado/departamento/proyecto)
- Salida: gráficos de evolución temporal (tareas abiertas/cerradas/vencidas), distribución de tareas por estado, distribución de horas por cliente (solo para director)

**CU-12 — Comparar asistencia vs partes**
- Actor: Director, Responsable (solo modo equipo global)
- Entrada: rango de fechas, filtros opcionales (departamento, empleado o responsable)
- Salida: horas fichadas vs horas imputadas por empleado, diferencia, porcentaje de cobertura y estado (ok/revisar/alerta); serie diaria cuando se filtra por empleado

**CU-13 — Consultar rentabilidad financiera ★**
- Actor: Director (exclusivo)
- Entrada: rango de fechas, modo de análisis (global / por proyecto / por responsable)
- Salida: ingresos totales, gastos totales, neto, rentabilidad (%), estado por proyecto y descomposición por cliente/responsable cuando aplica

**CU-14 — Consultar líneas analíticas ★**
- Actor: Director (exclusivo)
- Entrada: `scope ∈ { proyecto, cliente }`, identificador del objetivo (proyecto o cliente) y rango de fechas
- Salida: desglose de líneas analíticas en ingresos y gastos. Caso de uso único que unifica el drill-down por proyecto (líneas de un proyecto concreto) y por cliente (líneas agregadas de todos los proyectos del cliente), invocable exclusivamente vía `<<extend>>` desde CU-13

**CU-15 — Buscar globalmente**
- Actor: Director, Responsable
- Entrada: término de búsqueda (mínimo 2 caracteres), tipo de entidad (all/tasks/projects/employees), límite
- Salida: coincidencias de tareas, proyectos y empleados con nombre, código y metadatos

**CU-16 — Cerrar sesión**
- Actor: Director, Responsable
- Entrada: ninguna
- Salida: token JWT invalidado (el frontend borra el token localmente y redirige al login)

**CU-17 — Guardar snapshot**
- Actor: Director, Responsable
- Entrada: tipo de snapshot (métrica, gráfico o entidad), parámetros actuales de la vista calculada y datos ya calculados en pantalla
- Salida: identificador de la snapshot persistida en MongoDB e indicador de si se ha creado o actualizado. Si ya existe una snapshot para el mismo tipo, parámetros y día, se actualiza en lugar de crearse una nueva (semántica upsert diario)

**CU-18 — Listar snapshots**
- Actor: Director, Responsable
- Entrada: colección (métricas, gráficos, entidades), filtros opcionales por tipo y rango de fechas, parámetros de paginación
- Salida: tabla paginada server-side con tipo, fecha, parámetros resumidos, última actualización y actor responsable

**CU-19 — Consultar detalle de snapshot**
- Actor: Director, Responsable
- Entrada: identificador de la snapshot y tipo de colección
- Salida: ficha completa con metadatos (fecha, hash, creado/actualizado por), parámetros usados y la vista reconstruida por el renderer correspondiente al subtipo de la snapshot (métrica, gráfico o entidad)

**CU-20 — Eliminar snapshot**
- Actor: Director, Responsable
- Entrada: identificador de la snapshot y tipo de colección
- Salida: confirmación de eliminación permanente (hard delete) del documento en MongoDB

> **Nota sobre consolidaciones.** CU-10 absorbe las antiguas métricas operativas individuales y el panel de supervisión de equipo; CU-14 unifica las antiguas líneas de proyecto y líneas de cliente parametrizando el ámbito; y CU-17 absorbe la semántica de actualización mediante upsert diario. Véase §1.3 y §5.3 de `DisciplinaDeRequisitos.md` para la justificación RUP detallada.

---

## 3. Análisis de Clases

### 3.1 Capas de la solución

La solución sigue una **arquitectura por capas** con cuatro niveles en el backend y dos SPA independientes (frontend principal y visor) sobre el mismo backend. Las dependencias fluyen exclusivamente hacia abajo (nunca ascienden).

| Capa | Responsabilidad | Cambios por |
|------|-----------------|-------------|
| **Routes** | Enrutamiento HTTP FastAPI | Nuevos endpoints, nueva interfaz HTTP |
| **Services** | Lógica de negocio, cálculos, orquestación | Reglas de negocio, fórmulas, algoritmos |
| **Repositories** | Acceso a datos, queries SQL o MongoDB | Cambios en estructura de tablas Odoo o colecciones Mongo |
| **Models** | Mapeo de tablas Odoo con SQLAlchemy | Presencia/ausencia de columnas en Odoo |
| **Schemas** | Contratos JSON Pydantic (incluidos los de snapshot) | Nuevos campos en respuestas API |
| **Core** | Configuración, autenticación, conexiones a PostgreSQL y MongoDB | Cambios globales de configuración |
| **Utils** | Constantes y utilidades compartidas | Nuevas constantes, nuevos helpers |

Cada capa tiene **una única razón para cambiar** (Single Responsibility Principle).

Aunque el subsistema de snapshots persiste en una base de datos distinta (MongoDB), respeta la misma arquitectura en cuatro niveles: tiene su propia clase de ruta (`snapshots.py`), su servicio (`SnapshotService`), su repositorio (`snapshot.py`) y su esquema Pydantic (`schemas/snapshot.py`). No introduce un modelo ORM porque MongoDB no requiere mapeo relacional; la serialización/deserialización de los documentos se realiza directamente a través de los schemas de Pydantic.

### 3.2 Identificación de clases de análisis

Siguiendo la metodología RUP, las clases se clasifican en tres estereotipos:

**Clases Modelo (Entidad) — tablas Odoo mapeadas con SQLAlchemy:**

| Clase | Tabla Odoo | Datos principales |
|---|---|---|
| `Task` | `project_task` | id, name, planned_hours, is_closed, stage_id, project_id, responsable_id, date_deadline |
| `TaskStage` | `project_task_type` | id, name, closed |
| `Employee` | `hr_employee` | id, name, hourly_cost, department_id, user_id |
| `Department` | `hr_department` | id, name, manager_id, parent_id |
| `Project` | `project_project` | id, name, partner_id, date_start |
| `Timesheet` | `account_analytic_line` | id, unit_amount, amount, task_id, employee_id, date |
| `Attendance` | `hr_attendance` | id, employee_id, check_in, check_out, worked_hours |
| `TaskAssignment` | `project_task_user_rel` | task_id, user_id |
| `ResUsers` | `res_users` | id, login |
| `Partner` | `res_partner` | id, name |
| `MailMessage` | `mail_message` | id, model, res_id, date |
| `MailTrackingValue` | `mail_tracking_value` | mail_message_id, old_value_integer, new_value_integer |

**Clases Modelo (Entidad) — colecciones MongoDB mediante schemas Pydantic:**

| Clase | Colección Mongo | Datos principales |
|---|---|---|
| `MetricSnapshot` | `metric_snapshots` | `_id`, `metric_name`, `params`, `params_hash`, `snapshot_date`, `data`, `created_at`, `updated_at`, `created_by`, `updated_by` |
| `ChartSnapshot` | `chart_snapshots` | `_id`, `chart_name`, `params`, `params_hash`, `snapshot_date`, `data`, `created_at`, `updated_at`, `created_by`, `updated_by` |
| `EntitySnapshot` | `entity_snapshots` | `_id`, `entity_type`, `entity_id`, `snapshot_date`, `data`, `created_at`, `updated_at`, `created_by`, `updated_by` |
| `SnapshotActor` | *value object embebido* | `user_id`, `employee_id`, `employee_name`, `role` |

**Clases Vista (Interfaz) — páginas React:**

| Aplicación | Clase | Actor | CU asociados |
|---|---|---|---|
| Principal | `Login` | Ambos | CU-01 |
| Principal | `Overview` | Ambos | Panel de inicio |
| Principal | `Employees` / `EmployeeDetail` | Ambos | CU-02, CU-03 |
| Principal | `Departments` / `DepartmentDetail` | Ambos | CU-04, CU-05 |
| Principal | `Projects` / `ProjectDetail` | Ambos | CU-06, CU-07 |
| Principal | `Tasks` / `TaskDetail` | Ambos | CU-08, CU-09 |
| Principal | `Metrics` / `MetricDetail` | Ambos | CU-10 (modo individual de las 14 métricas operativas) |
| Principal | `Manager` | Ambos | CU-10 (modo agregado de equipo — variante de Workload) |
| Principal | `Attendance` | Ambos | CU-12 |
| Principal | `Rentability` | Director | CU-13, CU-14 |
| Principal | `Charts` | Ambos | CU-11 |
| Principal | `Search` | Ambos | CU-15 |
| Visor | `Login` | Ambos | CU-01 (reutiliza el mismo esquema JWT) |
| Visor | `Home` | Ambos | Resumen global y atajo a las últimas snapshots guardadas |
| Visor | `MetricSnapshots` | Ambos | CU-18 (colección de métricas) |
| Visor | `ChartSnapshots` | Ambos | CU-18 (colección de gráficos) |
| Visor | `EntitySnapshots` | Ambos | CU-18 (colección de entidades) |
| Visor | `SnapshotDetail` | Ambos | CU-19 + CU-20 |

**Clases Controlador — routers FastAPI:**

| Clase | CU coordinados | Servicio al que delega |
|---|---|---|
| `auth.router` | CU-01, CU-16 | `auth_service`, `scope_service` |
| `employee.router` | CU-02, CU-03 | `EmployeeService` |
| `department.router` | CU-04, CU-05 | `DepartmentService` |
| `project.router` | CU-06, CU-07 | `ProjectService` |
| `task.router` | CU-08, CU-09 | `TaskService` |
| `metrics.router` | CU-10 (15 métricas), CU-12 | 15 servicios de métricas + `AttendanceService` |
| `dashboards.router` | CU-03, CU-05, CU-07, CU-10 (modo equipo), CU-13, CU-14 | `DashboardService`, `RentabilityService` |
| `charts.router` | CU-11 | `ChartService` |
| `search.router` | CU-15 | `SearchService` |
| `snapshots.router` | CU-17, CU-18, CU-19, CU-20 | `SnapshotService` |


### 3.3 Relaciones entre clases del dominio

| Relación | Tipo | Cardinalidad |
|---|---|---|
| `Project` → `Task` | Composición | 1 a 0..* |
| `Task` → `TaskStage` | Asociación | * a 1 |
| `Task` → `Task` | Auto-referencia (subtareas) | * a 0..1 |
| `Employee` → `Department` | Agregación | * a 0..1 |
| `Task` → `Employee` (responsable) | Asociación | * a 0..1 |
| `Task` ↔ `ResUsers` | Asociación M:N | * a * (via `TaskAssignment`) |
| `Timesheet` → `Task` | Asociación | * a 0..1 |
| `Timesheet` → `Employee` | Asociación | * a 1 |
| `Project` → `Partner` | Asociación | * a 0..1 |
| `MailTrackingValue` → `MailMessage` | Asociación | * a 1 |
| `MetricSnapshot` / `ChartSnapshot` / `EntitySnapshot` → `Snapshot` | Generalización | * a 1 |
| `Snapshot` → `SnapshotActor` | Agregación (value object) | 1 a 0..1 (created_by + updated_by) |
| `EntitySnapshot` → `Employee` / `Department` / `Project` / `Task` | Referencia blanda mediante `entity_type` + `entity_id` | * a 1 |

La referencia blanda entre `EntitySnapshot` y las entidades operativas de Odoo es intencional: MongoDB y PostgreSQL son bases de datos independientes, por lo que no existe una clave foránea real. La unicidad por día se garantiza mediante los índices únicos sobre la clave compuesta de cada colección.

---

## 4. Análisis de Paquetes

### 4.1 Estructura de paquetes del backend

El backend se distribuye en carpetas siguiendo el criterio de **cohesión funcional**: cada paquete agrupa módulos con la misma naturaleza de responsabilidad. La estructura relevante es:

```
app/
├── core/
│   ├── config.py        → Configuración por entorno
│   ├── database.py      → Conexión SQLAlchemy a PostgreSQL (Odoo RO)
│   ├── mongo.py         → Cliente pymongo a MongoDB (snapshots RW)
│   └── security.py      → Generación y validación de JWT + guards RBAC
├── models/              → Clases ORM de Odoo (una por entidad)
├── schemas/             → Modelos Pydantic de entrada/salida
│   └── snapshot.py      → Schemas de MetricSnapshot / ChartSnapshot / EntitySnapshot / SnapshotActor
├── repositories/
│   ├── employee.py      → Acceso a hr_employee
│   ├── project.py       → Acceso a project_project
│   ├── department.py    → Acceso a hr_department
│   ├── task.py          → Acceso a project_task
│   ├── analytic_line.py → Acceso a account_analytic_line
│   ├── attendance.py    → Acceso a hr_attendance
│   ├── snapshot.py      → Acceso a las 3 colecciones MongoDB
│   └── metrics/         → Sub-paquete: una función por métrica operativa
├── services/
│   ├── metric/          → 15 implementaciones de MetricService
│   ├── dashboard.py     → Composición para resúmenes de entidad
│   ├── chart.py         → Datos para gráficos analíticos
│   ├── search.py        → Búsqueda global
│   ├── auth.py          → Autenticación y construcción del scope
│   └── snapshot.py      → Orquestación del subsistema de snapshots
├── routes/
│   ├── auth.py
│   ├── resources/       → Endpoints de entidades operativas
│   ├── metrics/         → Endpoints de métricas operativas
│   ├── charts/          → Endpoints de gráficos analíticos
│   ├── dashboards/      → Endpoints de resúmenes y rentabilidad
│   └── snapshots.py     → Endpoints POST/GET/DELETE /snapshots/{metrics|charts|entities}
└── utils/               → Paginación, traducción JSONB, validaciones de scope
```

### 4.2 Justificación de la estructura

**`core/`** agrupa todo lo que es infraestructura transversal: configuración de entorno, generación y validación de tokens JWT, pool de conexiones PostgreSQL y cliente MongoDB, y guards RBAC. Cambia solo cuando cambia la infraestructura, no el negocio. La adición de `mongo.py` respeta este criterio: aísla el cliente de MongoDB y su configuración en un único punto, independiente de `database.py`.

**`models/`** contiene exclusivamente el mapeo ORM sobre Odoo. Ningún modelo ejecuta lógica; solo declaran columnas y relaciones. No se añaden modelos ORM para el subsistema de snapshots porque MongoDB no requiere tal mapeo: la serialización se delega a Pydantic a través de `schemas/snapshot.py`.

**`repositories/`** encapsula cada consulta como una función pura que recibe una sesión (o un cliente Mongo) y devuelve datos crudos. El sub-paquete `repositories/metrics/` extiende esta idea con un submódulo por métrica, evitando que un único fichero de 200 líneas acumule consultas heterogéneas. `repositories/snapshot.py` es el único repositorio que no opera sobre SQLAlchemy: ejecuta directamente operaciones `insert_one`, `find_one`, `update_one`, `find_many` (con paginación y filtros) y `delete_one` sobre las tres colecciones MongoDB a través del cliente de `core/mongo.py`.

**`services/`** aplica las reglas de negocio sobre los datos recuperados por los repositorios. El sub-paquete `services/metrics/` sigue la misma granularidad que `repositories/metrics/`: una clase, una métrica, una razón de cambio. `SnapshotService` añade responsabilidades propias del subsistema: normalizar parámetros, calcular `params_hash` con SHA-256, construir el `SnapshotActor` a partir de los *claims* del JWT, fijar la `snapshot_date` y decidir entre insertar o actualizar (semántica upsert) según la existencia previa.

**`routes/`** actúa como capa de entrada HTTP. Los routers no contienen lógica de negocio: validan parámetros, comprueban autenticación y delegan en el servicio correspondiente. `snapshots.py` sigue esta norma y expone tres familias de endpoints homogéneas (una por colección) sobre la misma estructura CRUD.

**`utils/`** recoge funciones reutilizables que no pertenecen a ningún dominio: paginación, extracción de nombres de campos JSONB, ordenación de diccionarios y validaciones de rango de fechas.

### 4.3 Paquetes del frontend principal y del visor

Aunque el diseño de calidad recae principalmente sobre el backend, los frontends también siguen una estructura orientada a la separación de responsabilidades. El frontend principal y el visor son **dos aplicaciones Vite independientes** que comparten contexto de autenticación pero no código de presentación.

**Frontend principal (`frontend/`):**

```
src/
├── api/            → Un módulo por dominio (employees.js, tasks.js, metrics.js,
│                     charts.js, rentability.js, snapshots.js, search.js, auth.js)
├── components/     → Componentes reutilizables (Card, Table, KpiCard, Sidebar,
│                     SaveSnapshotButton...)
├── context/        → Estado global de autenticación (AuthContext)
├── hooks/          → Lógica reutilizable (useApi, useDebounce)
├── pages/          → Una página por recurso o funcionalidad
├── styles/         → Variables CSS globales y tokens de diseño
└── utils/          → Formateadores y helpers (formatters.js)
```

Todas las páginas calculadas (`MetricDetail`, `Charts`, `Rentability` y las fichas de entidad `EmployeeDetail`, `DepartmentDetail`, `ProjectDetail`, `TaskDetail`) integran el componente `SaveSnapshotButton`, que llama a `api/snapshots.js` para disparar CU-17 desde el contexto actual de la vista.

**Visor de snapshots:**

```
src/
├── api/
│   ├── auth.js             → Mismo contrato que el frontend principal
│   └── snapshots.js        → Lectura y borrado de las 3 colecciones
├── components/             → Tabla paginada, filtros, panel JSON expandible
├── context/                → AuthContext reutilizado del mismo esquema JWT
├── pages/
│   ├── Login.jsx
│   ├── Home.jsx            → Resumen global + últimas snapshots guardadas
│   ├── MetricSnapshots.jsx → Listado de snapshots de métricas (CU-18)
│   ├── ChartSnapshots.jsx  → Listado de snapshots de gráficos (CU-18)
│   ├── EntitySnapshots.jsx → Listado de snapshots de entidades (CU-18)
│   └── SnapshotDetail.jsx  → Detalle reconstruido + eliminación (CU-19, CU-20)
├── renderers/
│   ├── MetricView.jsx      → Gauge + gráfico + KPIs según metric_name
│   ├── ChartView.jsx       → Chart interactivo según chart_name
│   └── EntityView.jsx      → Ficha con avatar, campos y barra de progreso
└── vite.config.js          → Puerto 3001, proxy → :8000/api
```

El visor no duplica los componentes de cálculo del frontend principal: sus *renderers* operan exclusivamente sobre el JSON guardado en MongoDB, sin llamar a ningún endpoint de cálculo sobre Odoo. Este desacoplamiento garantiza que una snapshot mantenga su representación original aunque los cálculos del frontend principal evolucionen en el futuro.

---