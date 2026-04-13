# Disciplina de Requisitos

## Índice

1. [Encontrar Actores y Casos de Uso](#1-encontrar-actores-y-casos-de-uso)
   - 1.1 [Identificación de Actores](#11-identificación-de-actores)
   - 1.2 [Criterio de diseño del modelo](#12-criterio-de-diseño-del-modelo)
   - 1.3 [Lista de Casos de Uso](#13-lista-de-casos-de-uso)
   - 1.4 [Diagramas de Casos de Uso](#14-diagramas-de-casos-de-uso-por-actor)
2. [Priorizar Casos de Uso](#2-priorizar-casos-de-uso)
3. [Detallar Casos de Uso](#3-detallar-casos-de-uso)
4. [Prototipar Casos de Uso](#4-prototipar-casos-de-uso)
5. [Estructurar el Modelo de Casos de Uso](#5-estructurar-el-modelo-de-casos-de-uso)

---

## 1. Encontrar Actores y Casos de Uso

### 1.1 Identificación de Actores

| Actor | Descripción | Identificación en Odoo | Alcance de datos |
|---|---|---|---|
| **Director** | Máximo responsable de la organización. Acceso irrestricto a todo el sistema, incluido el módulo de rentabilidad financiera. | Nodo raíz del árbol jerárquico de empleados | Global — sin filtro |
| **Responsable** | Jefe de departamento o responsable de proyecto. Acceso filtrado a su ámbito organizativo calculado en el momento de la autenticación. | Gestiona al menos un departamento o proyecto | Filtrado por empleados, departamentos y proyectos bajo su responsabilidad |

> Los empleados rasos **no tienen acceso** al sistema y son redirigidos al login.
> El rol y el ámbito de cada usuario se determinan en el momento de la autenticación.

| Actor externo | Descripción | Conexión |
|---|---|---|
| **Sistema ERP (Odoo v16)** | Actor externo. Fuente única de datos. El módulo de analítica solo lee de su base de datos. | Solo lectura — el sistema consulta al ERP, no al contrario |

#### Diagrama de relaciones entre actores y sistema
![Diagrama de contexto con actores y sistema externo](./imagenes/diagramaActores.png)

---

### 1.2 Criterio de diseño del modelo

El modelo se organiza alrededor de un principio de **simetría por entidad**: cada entidad principal del dominio expone exactamente dos casos de uso:

| Entidad | CU de listado/búsqueda | CU de resumen analítico |
|---|---|---|
| Empleado | CU-02 Listar Empleados | CU-03 Resumen de Empleado |
| Departamento | CU-04 Listar Departamentos | CU-05 Resumen de Departamento |
| Proyecto | CU-06 Listar Proyectos | CU-07 Resumen de Proyecto |
| Tarea | CU-08 Listar Tareas | CU-09 Detalle de Tarea |

Los CU de resumen son el panel analítico de cada entidad: agregan indicadores, métricas y accesos a recursos relacionados. Las métricas operativas (Paquete 6) se consumen tanto desde los resúmenes de entidad como desde la página de métricas.

---

### 1.3 Identificación de Casos de Uso

A continuación se presenta la lista completa de los 27 casos de uso identificados, organizados en 9 paquetes.

#### Paquete 1 – Autenticación

| ID | Nombre | Actor(es) |
|---|---|---|
| CU-01 | Autenticarse en el Sistema | Director, Responsable |
| CU-27 | Cerrar Sesión | Director, Responsable |

#### Paquete 2 – Entidad: Empleado

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-02 | Listar y Buscar Empleados | Director, Responsable | Listado paginado con búsqueda y filtros. El Responsable solo ve los empleados de su ámbito. |
| CU-03 | Consultar Resumen de Empleado | Director, Responsable | Panel individual con indicadores de carga, WIP, productividad y tareas agrupadas por estado. |

#### Paquete 3 – Entidad: Departamento

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-04 | Listar Departamentos | Director, Responsable | Cuadrícula de departamentos activos con nombre y responsable. |
| CU-05 | Consultar Resumen de Departamento | Director, Responsable | Panel de departamento con indicadores de carga del equipo, tabla de workload por empleado y listado de miembros. |

#### Paquete 4 – Entidad: Proyecto

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-06 | Listar Proyectos | Director, Responsable | Cuadrícula de proyectos activos con cliente y código. |
| CU-07 | Consultar Resumen de Proyecto | Director, Responsable | Panel de proyecto con indicadores de eficiencia, riesgo y rentabilidad; comparativa de horas; listado de tareas y equipo. |

#### Paquete 5 – Entidad: Tarea

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-08 | Listar Tareas | Director, Responsable | Acceso único a tareas con filtros combinables. Invocable desde el contexto global, desde un proyecto o desde un empleado. |
| CU-09 | Consultar Detalle de Tarea | Director, Responsable | Ficha de tarea con información general, personas, horas y subtareas. |

#### Paquete 6 – Métricas Operativas

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-10 | Consultar Productividad | Director, Responsable | Ratio de horas estimadas frente a reales en tareas cerradas, con ranking y promedio. |
| CU-11 | Consultar Cumplimiento de Plazos | Director, Responsable | Porcentaje de tareas cerradas dentro de la fecha límite establecida. |
| CU-12 | Consultar WIP de Empleado | Director, Responsable | Número de tareas en paralelo del empleado, con umbral de referencia y recomendación. Requiere empleado. |
| CU-13 | Consultar Carga de Trabajo de Empleado | Director, Responsable | Horas de trabajo pendientes respecto a la jornada de referencia, con estado de carga. Requiere empleado. |
| CU-14 | Consultar Riesgo de Proyecto | Director, Responsable | Porcentaje de tareas abiertas vencidas o con el plazo muy consumido. Requiere proyecto. |
| CU-15 | Consultar Tasa de Retrabajo | Director, Responsable | Porcentaje de tareas que fueron cerradas y posteriormente reabiertas. |
| CU-16 | Consultar Exactitud de Estimación | Director, Responsable | Comparación entre horas estimadas y reales del responsable, con sesgo de estimación. Requiere empleado. |
| CU-17 | Consultar Lead Time | Director, Responsable | Días medios entre la asignación y el cierre de las tareas completadas. |
| CU-18 | Consultar Tiempo por Estado | Director, Responsable | Tiempo medio de permanencia de las tareas en cada etapa del flujo de trabajo. |
| CU-19 | Consultar Tareas Canceladas | Director, Responsable | Porcentaje de tareas cuya etapa corresponde a una cancelación. |
| CU-20 | Consultar Tiempo por Prioridad | Director, Responsable | Horas medias invertidas por nivel de prioridad (Normal / Urgente). |

#### Paquete 7 – Análisis Visual

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-21 | Visualizar Gráficos Analíticos | Director, Responsable | Gráficos interactivos de evolución de tareas, distribución por estado y horas por cliente (solo Director). |
| CU-22 | Consultar Asistencia vs Imputaciones | Director, Responsable | Comparativa de horas fichadas frente a horas imputadas en partes, con cobertura individual y serie diaria. |
| CU-28 | Consultar Carga del Equipo      | Director, Responsable | Panel global de carga del equipo con distribución por estado (sobrecargado, normal, subcargado, sin tareas), ranking de los empleados más cargados y listado filtrable por estado y departamento. |

#### Paquete 8 – Rentabilidad Financiera _(exclusivo para el Director)_

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-23 | Consultar Rentabilidad Financiera | Director | Análisis financiero basado en partes analíticos: resumen global, desglose por proyecto, por cliente y por responsable. |
| CU-24 | Consultar Líneas Analíticas de Proyecto | Director | Detalle de ingresos y gastos individuales de un proyecto. Accesible desde CU-23. |
| CU-25 | Consultar Líneas Analíticas de Cliente | Director | Detalle de ingresos y gastos de todos los proyectos de un cliente. Accesible desde CU-23. |

#### Paquete 9 – Utilidades

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-26 | Realizar Búsqueda Global | Director, Responsable | Búsqueda en tiempo real de tareas, proyectos y empleados por nombre o código, con navegación directa al detalle. |

---

### 1.4 Diagramas de Casos de Uso por Actor

|Director|Responsable|
|--------|-----------|
|![Diagrama de casos de uso - Director](./imagenes/actor_director.png)|![Diagrama de casos de uso - Responsable](./imagenes/actor_responsable.png)|
|[Ver código](./diagramas/actorDirector.puml)|[Ver código](./diagramas/actorResponsable.puml)|

Ambos actores comparten la mayoría de los casos de uso. El Director tiene acceso exclusivo al módulo de rentabilidad financiera (CU-23, CU-24 y CU-25). El Responsable opera siempre con un filtro automático sobre su ámbito organizativo.

**Resumen rápido:**
- **Director:** Acceso a los 28 CU sin restricciones de ámbito.
- **Responsable:** Acceso a CU-01 hasta CU-22 y CU-26/CU-27/CU-28, con datos filtrados automáticamente a su ámbito; acceso denegado a CU-23, CU-24 y CU-25.

---

## 2. Priorizar Casos de Uso

### 2.1 Criterios

| Criterio | Descripción | Escala |
|---|---|---|
| **Criticidad** | ¿Bloquea la ejecución de otros CU? | 1–3 |
| **Valor de negocio** | ¿Apoya decisiones estratégicas o tácticas? | 1–3 |
| **Frecuencia de uso** | ¿Se ejecuta en cada sesión o esporádicamente? | 1–3 |
| **Riesgo técnico** | ¿Implica lógica compleja o integración crítica con el ERP? | 1–3 |

### 2.2 Tabla de Priorización

#### Prioridad Alta

| ID | Nombre | Crit. | V.Neg. | Frec. | R.Téc. | **Prioridad** |
|---|---|:---:|:---:|:---:|:---:|:---:|
| CU-02 | Listar Empleados | 2 | 3 | 3 | 1 | **Alta** |
| CU-03 | Consultar Resumen Empleado | 2 | 3 | 3 | 2 | **Alta** |
| CU-06 | Listar Proyectos | 2 | 3 | 3 | 1 | **Alta** |
| CU-07 | Consultar Resumen Proyecto | 2 | 3 | 2 | 2 | **Alta** |
| CU-08 | Listar Tareas | 3 | 3 | 3 | 2 | **Alta** |
| CU-23 | Consultar Rentabilidad Financiera | 3 | 3 | 2 | 3 | **Alta** |
| CU-24 | Consultar Líneas Analíticas Proyecto | 1 | 3 | 1 | 2 | **Alta** |
| CU-25 | Consultar Líneas Analíticas Cliente | 1 | 3 | 1 | 2 | **Alta** |

#### Prioridad Media

| ID | Nombre | Crit. | V.Neg. | Frec. | R.Téc. | **Prioridad** |
|---|---|:---:|:---:|:---:|:---:|:---:|
| CU-01 | Autenticarse | 1 | 2 | 3 | 2 | **Media** |
| CU-04 | Listar Departamentos | 2 | 2 | 2 | 1 | **Media** |
| CU-05 | Consultar Resumen Departamento | 2 | 3 | 2 | 1 | **Media** |
| CU-09 | Consultar Detalle de Tarea | 2 | 2 | 3 | 1 | **Media** |
| CU-10 | Consultar Productividad | 2 | 3 | 2 | 2 | **Media** |
| CU-11 | Consultar Cumplimiento | 2 | 3 | 2 | 1 | **Media** |
| CU-12 | Consultar WIP (Empleado) | 2 | 3 | 2 | 2 | **Media** |
| CU-13 | Consultar Workload (Empleado) | 2 | 3 | 2 | 2 | **Media** |
| CU-14 | Consultar Riesgo Proyecto | 2 | 3 | 2 | 2 | **Media** |
| CU-15 | Consultar Retrabajo | 1 | 2 | 1 | 3 | **Media** |
| CU-16 | Consultar Exactitud Estimación | 1 | 2 | 1 | 2 | **Media** |
| CU-17 | Consultar Lead Time | 2 | 3 | 2 | 2 | **Media** |
| CU-18 | Consultar Tiempo por Estado | 2 | 3 | 2 | 2 | **Media** |
| CU-21 | Consultar Gráficos Analíticos | 2 | 3 | 2 | 2 | **Media** |
| CU-22 | Consultar Asistencia vs Imputaciones | 2 | 3 | 2 | 2 | **Media** |
| CU-26 | Búsqueda Global | 1 | 2 | 3 | 1 | **Media** |
| CU-27 | Cerrar Sesión | 1 | 1 | 3 | 1 | **Media** |
| CU-28 | Consultar Carga del Equipo      |   2   |   3    |   2   |   1    | **Media**     |

#### Prioridad Baja

| ID | Nombre | Crit. | V.Neg. | Frec. | R.Téc. | **Prioridad** |
|---|---|:---:|:---:|:---:|:---:|:---:|
| CU-19 | Tareas Canceladas | 1 | 2 | 1 | 2 | **Baja** |
| CU-20 | Tiempo por Prioridad | 1 | 2 | 1 | 2 | **Baja** |

> **Nota estratégica:** CU-23 tiene alta prioridad aunque sea de uso menos frecuente, por su relevancia para la toma de decisiones del Director. CU-24 y CU-25 se integran como extensiones directas de CU-23.
>
> **Riesgo técnico:** CU-15 a CU-18 dependen del historial de cambios de estado del ERP, lo que requiere integración cuidadosa con los registros de seguimiento de Odoo.

---

## 3. Detallar Casos de Uso

Todos los casos de uso están documentados en detalle en: [Disciplina de Requisitos – Casos de Uso Detallados](./docs/CasosDeUsoDetallados.md)

### CU-01 – Autenticarse en el Sistema

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | El usuario dispone de credenciales válidas en el sistema ERP con un empleado activo vinculado. |
| **Postcondición** | El usuario tiene una sesión activa y accede al sistema con el rol y ámbito que le corresponden. |

![Diagrama de flujo de autenticación](./imagenes/CdU/flujoCU01.png)

**Flujo principal:**
1. El actor accede a la pantalla de inicio de sesión e introduce sus credenciales.
2. El sistema valida las credenciales contra el ERP.
3. El sistema verifica que existe un empleado activo asociado al usuario.
4. El sistema determina el rol del usuario: Director (acceso global) o Responsable (acceso filtrado).
5. Para el Responsable, el sistema calcula el ámbito organizativo que le corresponde.
6. Se crea la sesión del usuario y se redirige a la pantalla principal.

**Flujos alternativos:**
- `FA-01`: Credenciales incorrectas → mensaje de error, permanece en el login.
- `FA-02`: Sin empleado activo vinculado → acceso denegado.
- `FA-03`: Usuario sin permisos de acceso al sistema → acceso denegado con mensaje informativo.
- `FA-04`: Sesión expirada → el sistema cierra la sesión y redirige al login.

**Relaciones:** CU-01 es precondición de todos los demás CU.

---

### CU-02 – Listar y Buscar Empleados

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el empleado buscado y puede navegar a CU-03. |

![Diagrama de flujo](./imagenes/CdU/flujoCU02.png)

**Flujo principal:**
1. El actor accede al listado de empleados.
2. El sistema muestra los empleados disponibles según su rol y ámbito.
3. El actor puede filtrar por nombre, departamento o estado (activo/inactivo).
4. El sistema actualiza el listado paginado con los filtros aplicados.
5. El actor selecciona un empleado para consultar su resumen.

**Flujos alternativos:**
- `FA-01`: Sin resultados con los filtros aplicados → estado vacío con mensaje.

**Relaciones:** Navega a CU-03.

---

### CU-03 – Consultar Resumen de Empleado

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El empleado pertenece al ámbito del actor. |
| **Postcondición** | El actor conoce el estado completo del empleado: carga, WIP, productividad y tareas. |

![Diagrama de flujo](./imagenes/CdU/flujoCU03.png)

**Flujo principal:**
1. El actor selecciona un empleado desde CU-02 o CU-05.
2. El sistema verifica que el empleado pertenece al ámbito del actor.
3. El sistema muestra el perfil del empleado con sus indicadores de rendimiento.
4. El actor navega por las pestañas de tareas (pendientes, completadas, asignadas, como responsable).
5. El actor puede filtrar las tareas por rango de fechas.
6. El actor puede seleccionar una tarea para ver su detalle.

**Flujos alternativos:**
- `FA-01`: Empleado fuera del ámbito → acceso denegado.
- `FA-02`: Empleado sin usuario vinculado → indicadores a 0, tareas vacías.

**Relaciones:** `<<extend>>` por CU-08 (listados de tareas contextuales). Navega a CU-09.

---

### CU-04 – Listar Departamentos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el departamento y puede navegar a CU-05. |

![Diagrama de flujo](./imagenes/CdU/flujoCU04.png)

**Flujo principal:**
1. El actor accede al listado de departamentos.
2. El sistema muestra los departamentos activos disponibles según su ámbito.
3. El actor selecciona un departamento para consultar su resumen.

**Flujos alternativos:**
- `FA-01`: Sin departamentos en el ámbito → estado vacío.

**Relaciones:** Navega a CU-05.

---

### CU-05 – Consultar Resumen de Departamento

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El departamento pertenece al ámbito del actor. |
| **Postcondición** | El actor conoce el estado de carga del departamento y puede navegar a sus empleados. |

![Diagrama de flujo](./imagenes/CdU/flujoCU05.png)

**Flujo principal:**
1. El actor selecciona un departamento desde CU-04.
2. El sistema verifica que el departamento pertenece al ámbito del actor.
3. El sistema muestra el panel con nombre, responsable e indicadores de distribución de carga.
4. El sistema alerta visualmente si hay empleados sobrecargados.
5. El actor puede consultar la carga de trabajo o el listado de empleados mediante pestañas.
6. El actor puede seleccionar un empleado para acceder a su resumen.

**Flujos alternativos:**
- `FA-01`: Departamento fuera del ámbito → acceso denegado.
- `FA-02`: Departamento sin empleados → pestañas vacías.

**Relaciones:** Navega a CU-03.

---

### CU-06 – Listar Proyectos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el proyecto y puede navegar a CU-07. |

![Diagrama de flujo](./imagenes/CdU/flujoCU06.png)

**Flujo principal:**
1. El actor accede al listado de proyectos.
2. El sistema muestra los proyectos activos disponibles según su ámbito.
3. El actor selecciona un proyecto para consultar su resumen.

**Flujos alternativos:**
- `FA-01`: Sin proyectos en el ámbito → estado vacío.

**Relaciones:** Navega a CU-07.

---

### CU-07 – Consultar Resumen de Proyecto

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El proyecto pertenece al ámbito del actor. |
| **Postcondición** | El actor conoce el estado completo del proyecto: eficiencia, riesgo, rentabilidad, tareas y equipo. |

![Diagrama de flujo](./imagenes/CdU/flujoCU07.png)

**Flujo principal:**
1. El actor selecciona un proyecto desde CU-06 o desde los resultados de CU-26.
2. El sistema verifica que el proyecto pertenece al ámbito del actor.
3. El sistema muestra el panel del proyecto con indicadores de eficiencia, riesgo y rentabilidad.
4. El actor puede consultar las tareas del proyecto o los miembros del equipo mediante pestañas.
5. El actor puede filtrar las tareas por estado o etapa.
6. El actor puede seleccionar una tarea o un empleado para ver su detalle.

**Flujos alternativos:**
- `FA-01`: Sin horas registradas → rentabilidad y eficiencia a 0.
- `FA-02`: Proyecto fuera del ámbito → acceso denegado.

**Relaciones:** Navega a CU-03 y CU-09.

---

### CU-08 – Listar Tareas

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza las tareas buscadas o accede a su detalle (CU-09). |

![Diagrama de flujo](./imagenes/CdU/flujoCU08.png)

**Flujo principal:**
1. El actor accede al listado de tareas, bien desde la sección global, bien desde CU-07 o CU-03.
2. El sistema muestra las tareas disponibles según el ámbito del actor, con los filtros contextuales preseleccionados si los hubiera.
3. El actor puede combinar filtros: estado, etapa, proyecto, empleado, rango de fechas y tareas principales.
4. El sistema actualiza el listado paginado con los filtros aplicados.
5. El actor selecciona una tarea para ver su detalle.

**Flujos alternativos:**
- `FA-01`: Sin tareas con los filtros aplicados → estado vacío con mensaje.
- `FA-02`: Filtro por proyecto o empleado fuera del ámbito → acceso denegado.

**Relaciones:** Navega a CU-09.

---

### CU-09 – Consultar Detalle de Tarea

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. La tarea pertenece a un proyecto en el ámbito del actor. |
| **Postcondición** | El actor conoce todos los datos de la tarea. |

![Diagrama de flujo](./imagenes/CdU/flujoCU09.png)

**Flujo principal:**
1. El actor selecciona una tarea desde CU-08, CU-03 o CU-07.
2. El sistema verifica que la tarea pertenece al ámbito del actor.
3. El sistema muestra la ficha completa: información general, personas responsables y asignadas, horas y subtareas.
4. El actor puede navegar al proyecto de la tarea, a los perfiles de los empleados implicados, a las subtareas o a la tarea padre si existe.

**Flujos alternativos:**
- `FA-01`: Tarea no encontrada → mensaje de error.
- `FA-02`: Tarea fuera del ámbito → acceso denegado.

**Relaciones:** Navega a CU-03, CU-07, CU-09 (recursivo en subtareas).

---

### CU-10 – Consultar Productividad

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor conoce la eficiencia de ejecución frente a la estimación. |

![Diagrama de flujo](./imagenes/CdU/flujoCU10.png)

**Flujo principal:**
1. El actor accede a la página de métricas y selecciona la métrica de Productividad.
2. El actor puede filtrar por empleado, proyecto y rango de fechas.
3. El sistema calcula el ratio de horas estimadas frente a reales para las tareas cerradas.
4. El sistema muestra el promedio de productividad y el ranking de tareas.

**Relaciones:** Accesible desde la página de métricas.

---

### CU-11 a CU-20 – Métricas restantes

> Los CU-11 (Cumplimiento), CU-12 (WIP), CU-13 (Workload), CU-14 (Riesgo), CU-15 (Retrabajo), CU-16 (Exactitud Estimación), CU-17 (Lead Time), CU-18 (Tiempo por Estado), CU-19 (Tareas Canceladas) y CU-20 (Tiempo por Prioridad) se documentan con el mismo nivel de detalle en [Casos de Uso Detallados](./docs/CasosDeUsoDetallados.md). Todos comparten el patrón de acceso a través de la página de métricas, presentándose como tarjetas interactivas con panel de detalle.

---

### CU-21 – Visualizar Gráficos Analíticos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor analiza tendencias y distribuciones de forma visual. |

![Diagrama de flujo](./imagenes/CdU/flujoCU21.png)

**Flujo principal:**
1. El actor accede a la página de gráficos.
2. El actor configura el rango de fechas, la agrupación temporal y la entidad de análisis.
3. El sistema genera los gráficos disponibles para el rol del actor: evolución temporal de tareas, distribución por estado y — solo para el Director — distribución de horas por cliente.
4. Al cambiar los filtros, el sistema recalcula y actualiza los gráficos.

**Flujos alternativos:**
- `FA-01`: Sin datos en el período → mensaje de sin datos por gráfico.
- `FA-02`: El Responsable no visualiza la distribución por cliente ni datos fuera de su ámbito.

**Relaciones:** `<<include>>` CU-02, CU-04 y CU-06 para los selectores de filtro.

---

### CU-22 – Consultar Asistencia vs Imputaciones

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor detecta discrepancias entre la presencia registrada y las horas imputadas. |

![Diagrama de flujo](./imagenes/CdU/flujoCU22.png)

**Flujo principal:**
1. El actor accede a la página de asistencia.
2. El actor configura el rango de fechas y, si es Director, el modo de visualización (equipo global o por responsable).
3. El sistema compara las horas fichadas con las horas imputadas por empleado.
4. El sistema muestra indicadores globales, gráfico comparativo y tabla con el estado de cobertura individual.
5. El actor puede expandir el detalle de un empleado para ver su evolución diaria.

**Flujos alternativos:**
- `FA-01`: Sin datos en el período → indicadores a 0 y tabla vacía.
- `FA-02`: El Responsable no dispone del modo de visualización por responsable.

**Relaciones:** `<<include>>` CU-02 y CU-04 para los selectores de filtro.

---

### CU-23 – Consultar Rentabilidad Financiera

| Campo | Valor |
|---|---|
| **Actores** | **Director** (exclusivo) |
| **Precondición** | CU-01 con rol de Director. |
| **Postcondición** | El Director conoce la rentabilidad real por proyecto, cliente y responsable. |

![Diagrama de flujo](./imagenes/CdU/flujoCU23.png)

**Flujo principal:**
1. El actor accede a la página de rentabilidad.
2. El sistema verifica que el actor tiene rol de Director.
3. El actor selecciona el rango de fechas y el modo de análisis (global, por proyecto o por responsable).
4. El sistema muestra el resumen financiero con ingresos, gastos, resultado neto y rentabilidad.
5. El actor puede desglosar por proyecto o por cliente mediante pestañas.
6. Desde cualquier fila de proyecto o cliente, el actor puede solicitar el detalle de líneas analíticas.

**Flujos alternativos:**
- `FA-01`: Sin partes analíticos registrados → todos los valores a 0.
- `FA-02`: Actor sin rol Director → pantalla de acceso restringido.

**Relaciones:** `<<extend>>` por CU-24 (líneas por proyecto) y CU-25 (líneas por cliente).

---

### CU-24 – Consultar Líneas Analíticas de Proyecto

| Campo | Valor |
|---|---|
| **Actores** | **Director** (exclusivo) |
| **Precondición** | CU-01 con rol de Director. Proyecto seleccionado en CU-23. |
| **Postcondición** | El Director conoce el desglose de ingresos y gastos individuales del proyecto. |

![Diagrama de flujo](./imagenes/CdU/flujoCU24.png)

**Flujo principal:**
1. Desde CU-23, el actor solicita el detalle de un proyecto.
2. El sistema recupera y muestra las líneas de partes analíticos separadas en ingresos y gastos, con fecha, descripción, importe y horas de cada línea.

**Flujos alternativos:**
- `FA-01`: Sin líneas en el período → tablas vacías.

**Relaciones:** `<<extend>>` desde CU-23.

---

### CU-25 – Consultar Líneas Analíticas de Cliente

| Campo | Valor |
|---|---|
| **Actores** | **Director** (exclusivo) |
| **Precondición** | CU-01 con rol de Director. Cliente seleccionado en CU-23. |
| **Postcondición** | El Director conoce el desglose de ingresos y gastos de todos los proyectos del cliente. |

![Diagrama de flujo](./imagenes/CdU/flujoCU25.png)

**Flujo principal:**
1. Desde CU-23, el actor solicita el detalle de un cliente.
2. El sistema recupera y muestra las líneas de todos los proyectos del cliente, separadas en ingresos y gastos, con la referencia al proyecto de cada línea.

**Flujos alternativos:**
- `FA-01`: Sin líneas → tablas vacías.

**Relaciones:** `<<extend>>` desde CU-23.

---

### CU-26 – Realizar Búsqueda Global

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. Mínimo 2 caracteres introducidos. |
| **Postcondición** | El actor localiza el recurso y navega a su detalle. |

![Diagrama de flujo](./imagenes/CdU/flujoCU26.png)

**Flujo principal:**
1. El actor accede a la búsqueda global e introduce un texto de búsqueda.
2. El sistema busca coincidencias en tareas, proyectos y empleados dentro del ámbito del actor.
3. El sistema muestra los resultados agrupados por tipo de entidad.
4. El actor puede filtrar por tipo de entidad y seleccionar un resultado para navegar a su detalle.

**Flujos alternativos:**
- `FA-01`: Sin resultados → estado vacío con mensaje.

**Relaciones:** Navega a CU-03, CU-07, CU-09.

---

### CU-27 – Cerrar Sesión

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. Usuario autenticado. |
| **Postcondición** | Sesión eliminada. El usuario es redirigido a la pantalla de inicio de sesión. |

![Diagrama de flujo](./imagenes/CdU/flujoCU27.png)

**Flujo principal:**
1. El actor selecciona la opción de cerrar sesión.
2. El sistema invalida la sesión activa y elimina las credenciales almacenadas en el navegador.
3. El sistema redirige al actor a la pantalla de inicio de sesión.

**Relaciones:** Ninguna.

---

## 4. Prototipar Casos de Uso

Los prototipos de baja fidelidad presentados a continuación representan la disposición visual de cada pantalla del sistema. Cada prototipo ilustra la estructura de la interfaz, la organización de los datos y los puntos de interacción disponibles para el usuario, sirviendo como referencia para la implementación del frontend.

> Además de los prototipos asociados a casos de uso, el sistema incluye pantallas de navegación y agregación que actúan como punto de entrada y supervisión global. No constituyen casos de uso en sí mismas, sino vistas compuestas que consolidan información de múltiples casos de uso ya documentados.

---

### Pantalla de inicio — `/`

Panel de bienvenida que actúa como punto de entrada al sistema tras completar CU-01. Muestra un resumen ejecutivo con alertas activas, indicadores globales y accesos rápidos a las secciones principales.

![Pantalla de inicio](./imagenes/prototipado/Vista-Overview.png)

---

### Prototipo CU-01 – Autenticarse
Formulario de inicio de sesión con campos de usuario y contraseña, indicación de acceso restringido y mensajes de error contextuales.

![Prototipo de autenticación](./imagenes/prototipado/CU-01.png)

---
### Prototipo CU-02 – Listar Empleados
Tabla paginada con barra de búsqueda por nombre, selector de departamento y opción de mostrar solo activos. Cada fila es navegable al resumen del empleado.

![Prototipo de listar empleados](./imagenes/prototipado/CU-04.png)

---

### Prototipo CU-03 – Resumen de Empleado
Panel individual con cabecera de perfil, indicadores principales y pestañas de tareas con filtro de fechas.

![Prototipo de resumen de empleado](./imagenes/prototipado/CU-05.png)

---
### Prototipo CU-04 – Listar Departamentos
Cuadrícula de tarjetas con el nombre del departamento y el responsable asignado. Cada tarjeta navega al resumen del departamento.

![Prototipo de listar departamentos](./imagenes/prototipado/CU-06.png)

---
### Prototipo CU-05 – Resumen de Departamento
Panel de departamento con indicadores de distribución de carga, alerta para empleados sobrecargados y dos pestañas de visualización.

![Prototipo de resumen de departamento](./imagenes/prototipado/CU-07.png)

---
### Prototipo CU-06 – Listar Proyectos
Cuadrícula de tarjetas con nombre del proyecto, cliente asociado y código. Cada tarjeta navega al resumen del proyecto.

---
### Prototipo CU-07 – Resumen de Proyecto
Panel de proyecto con indicadores de eficiencia, riesgo y rentabilidad, gráfico comparativo de horas y pestañas de tareas y equipo.

![Prototipo de resumen de proyecto](./imagenes/prototipado/CU-09.png)

---
### Prototipo CU-08 – Listar Tareas
Tabla paginada con barra de filtros combinables: estado, etapa, proyecto, rango de fechas y opción de mostrar solo tareas principales.

![Prototipo de listar tareas](./imagenes/prototipado/CU-10.png)

---
### Prototipo CU-09 – Detalle de Tarea
Ficha de tarea con secciones de información general, personas, horas con barra de progreso y lista de subtareas.

![Prototipo de detalle de tarea](./imagenes/prototipado/CU-11.png)

---
### Prototipo P6 – Métricas Operativas (CU-10 a CU-20)
Página de métricas con cuadrícula de tarjetas a la izquierda y panel de detalle de la métrica seleccionada a la derecha. Panel de filtros en la parte superior.

![Prototipo de métricas](./imagenes/prototipado/CU-P7.png)

---
### Prototipo CU-21 – Gráficos Analíticos
Página de gráficos con barra de filtros y cuadrícula de visualizaciones: evolución temporal, distribución por estado y horas por cliente.

![Prototipo de gráficos analíticos](./imagenes/prototipado/CU-22.png)

---
### Prototipo CU-22 – Asistencia vs Imputaciones
Página de asistencia con selector de modo de vista, filtros de fecha y departamento, indicadores globales, gráfico comparativo y tabla de empleados con semáforo de cobertura.

![Prototipo de asistencia](./imagenes/prototipado/CU-23.png)

---
### Prototipo CU-23 – Rentabilidad Financiera
Página de rentabilidad con filtros de fecha y modo de análisis, indicadores financieros, gráfico comparativo y pestañas por proyecto y por cliente con opción de desglose detallado.

![Prototipo de rentabilidad](./imagenes/prototipado/CU-24.png)

---
### Prototipo CU-24/CU-25 – Líneas Analíticas por Proyecto/Cliente
Panel de desglose accesible desde CU-23 con dos tablas paralelas de ingresos y gastos individuales.

![Prototipo de líneas analíticas](./imagenes/prototipado/CU-26-27.png)

---
### Prototipo CU-26 – Búsqueda Global
Página de búsqueda con campo prominente, botones de filtro por tipo de entidad y resultados en forma de tarjetas navegables.

![Prototipo de búsqueda global](./imagenes/prototipado/CU-25.png)

---
### Prototipo CU-28 – Consultar Carga del Equipo
Panel de supervisión con cinco tarjetas numéricas clicables (total, sobrecargado, normal,
subcargado, sin tareas), gráfico de barras de distribución por estado, panel de empleados
más cargados y — al hacer click en una tarjeta — listado paginado de empleados filtrados
por ese estado con su porcentaje de carga y horas pendientes.
![Prototipo de supervisión de carga](./imagenes/prototipado/CU-28.png)

---

## 5. Estructurar el Modelo de Casos de Uso

### 5.1 Diagrama de Contexto – Director

![Diagrama de Contexto - Director](./imagenes/contexto_director.png)

El Director tiene acceso a todos los casos de uso sin restricciones de ámbito. Es el único actor con acceso al módulo de rentabilidad financiera (CU-23, CU-24 y CU-25).

---

### 5.2 Diagrama de Contexto – Responsable

![Diagrama de Contexto - Responsable](./imagenes/contexto_responsable.png)

El Responsable tiene acceso a la mayoría de los casos de uso, pero con datos filtrados automáticamente a su ámbito organizativo (empleados, departamentos y proyectos bajo su responsabilidad).

---

### 5.3 Relaciones Include / Extend del Modelo

Para la correcta interpretación del modelo se definen dos tipos de relaciones:

#### 1. Relaciones de Inclusión `<<include>>`
Representan comportamientos **obligatorios y reutilizados** que se insertan en el flujo de un caso de uso base.

- **Nota sobre autenticación:** CU-01 se trata como precondición transversal de todos los casos de uso y no se modela como `<<include>>` explícito para no sobrecargar el diagrama.
- **Ejemplos reales:**
  - CU-21 y CU-22 incluyen CU-02, CU-04 y CU-06 para poblar los selectores de filtro con empleados, departamentos y proyectos disponibles.

#### 2. Relaciones de Extensión `<<extend>>`
Representan funcionalidades **opcionales o condicionales** activadas desde un caso de uso base bajo una condición específica.

| Caso de Uso Base | Caso de Uso Extendido (`<<extend>>`) | Condición de Extensión |
| :--- | :--- | :--- |
| **CU-03** (Resumen Empleado) | **CU-08** (Listar Tareas) | Cuando el actor accede a una pestaña o tarjeta de tareas del empleado. |
| **CU-23** (Rentabilidad) | **CU-24** (Líneas por Proyecto) | Cuando el actor solicita el desglose de líneas analíticas de un proyecto. |
| **CU-23** (Rentabilidad) | **CU-25** (Líneas por Cliente) | Cuando el actor solicita el desglose de líneas analíticas de un cliente. |