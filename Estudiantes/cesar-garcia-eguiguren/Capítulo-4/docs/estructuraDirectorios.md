# Estructura de directorios

El proyecto se organiza en tres capas independientes: el backend FastAPI, el frontend principal (React, puerto 5173) y el visor de snapshots (React, puerto 3001).

```
ModuloAnalisisERP/
│
├── app/                              # Backend — FastAPI
│   ├── main.py                       # Entrada: monta los 10 routers, configura CORS y lifespan
│   ├── core/                         # Configuración (settings, conexiones DB)
│   ├── models/                       # Modelos SQLAlchemy (solo lectura sobre Odoo/PostgreSQL)
│   ├── schemas/                      # Modelos Pydantic (request/response)
│   ├── routes/                       # Capa HTTP — un fichero por router
│   │   ├── auth.py                   # POST /auth/token · GET /auth/me
│   │   ├── employee.py               # GET /employees/** (listado, ficha, asistencia, managers)
│   │   ├── department.py             # GET /departments/** (listado, detalle, empleados, carga)
│   │   ├── project.py                # GET /projects/** (listado, detalle, equipo, tareas)
│   │   ├── task.py                   # GET /tasks/filter · /tasks/stages · /tasks/{id}
│   │   ├── search.py                 # GET /search/
│   │   ├── dashboards.py             # GET /dashboards/overview · /summary/{employee,manager,project}
│   │   ├── charts.py                 # GET /charts/{task-distribution,productivity-trend,task-evolution}
│   │   ├── metrics.py                # GET /metrics/** (14 métricas + 6 de rentabilidad)
│   │   └── snapshots.py              # CRUD /snapshots/{metrics,charts,entities}
│   ├── services/                     # Lógica de negocio — un fichero por dominio
│   │   ├── auth_service.py
│   │   ├── employee_service.py
│   │   ├── department_service.py
│   │   ├── project_service.py
│   │   ├── task_service.py
│   │   ├── dashboard_service.py
│   │   ├── chart_service.py
│   │   ├── search_service.py
│   │   ├── snapshot.py
│   │   └── metrics/                  # Sub-paquete: una clase por cada métrica
│   │       ├── attendance.py         # Horas fichadas vs imputadas (comparativa + serie diaria)
│   │       ├── productivity.py
│   │       ├── compliance.py
│   │       ├── workload.py
│   │       ├── risk_index.py
│   │       ├── project_efficiency.py
│   │       ├── profitability.py
│   │       ├── rentability_service.py  # Rentabilidad financiera (analytic lines)
│   │       ├── lead_time.py
│   │       ├── wip.py
│   │       ├── rework_rate.py
│   │       ├── estimation_accuracy.py
│   │       ├── client_distribution.py
│   │       ├── cancelled_tasks.py
│   │       ├── priority_time.py
│   │       └── state_time.py
│   ├── repositories/                 # Acceso a datos — SQL (PostgreSQL/Odoo) y MongoDB
│   │   ├── employee.py · department.py · project.py · task.py
│   │   ├── dashboard.py · chart.py · search.py
│   │   ├── snapshot.py               # CRUD sobre MongoDB (colección snapshots)
│   │   ├── tracking.py               # Horas de fichaje (hr.attendance)
│   │   ├── timesheet.py              # Horas imputadas (account.analytic.line)
│   │   └── metrics/                  # Repositorios específicos por métrica
│   └── utils/                        # Helpers (paginación, formateo de fechas, scope JWT)
│
├── frontend/                         # Frontend principal — React 18 + Vite (puerto 5173)
│   └── src/
│       ├── api/                      # Clientes Axios agrupados por dominio
│       │   ├── analytics.js          # → /employees/attendance/**, /dashboards/**, /charts/**
│       │   ├── employees.js          # → /employees/, /employees/{id}, /managers/profitability
│       │   ├── departments.js        # → /departments/**
│       │   ├── projects.js           # → /projects/**
│       │   ├── tasks.js              # → /tasks/**
│       │   ├── metrics.js            # → /metrics/**
│       │   ├── snapshots.js          # → /snapshots/**
│       │   └── search.js             # → /search/
│       ├── pages/                    # Vistas (18 ficheros — ver galería más abajo)
│       ├── components/ui/            # Componentes reutilizables (Table, Badge, Card, KpiCard…)
│       ├── context/                  # AuthContext (JWT, rol, scope)
│       ├── hooks/                    # useApi (fetch + loading/error), useDebounce
│       └── utils/formatters.js       # fmtHours, fmtPct, fmtNum, workloadConfig…
│
└── frontend2/                        # Visor de snapshots — React 18 + Vite (puerto 3001)
    └── src/
        ├── api/snapshots.js          # → /snapshots/**, /snapshots/stats
        └── pages/
            ├── Home.jsx              # Panel de contadores de MongoDB
            ├── MetricSnapshots.jsx   # Listado + borrado de snapshots de métricas
            ├── ChartSnapshots.jsx    # Listado + borrado de snapshots de gráficos
            ├── EntitySnapshots.jsx   # Listado + borrado de snapshots de entidades
            └── SnapshotDetail.jsx    # Visualizador JSON del snapshot seleccionado
```

> **Bases de datos:** PostgreSQL (instancia Odoo, acceso solo lectura mediante SQLAlchemy) para toda la información operativa, y MongoDB para la persistencia de snapshots. La capa de repositorios encapsula ambas conexiones de forma transparente para los servicios.
