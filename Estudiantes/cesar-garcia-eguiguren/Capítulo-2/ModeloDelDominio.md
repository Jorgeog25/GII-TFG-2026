# Modelo del Dominio

El sistema se compone de una aplicación web analítica conectada a la base de datos del ERP en modo lectura, cuya función principal es consumir los datos del ERP Odoo, estructurarlos y transformarlos en información útil para la toma de decisiones. Por ello, el modelo del dominio no introduce nuevas entidades de negocio, sino que se apoya en las ya existentes en Odoo, garantizando coherencia con la base de datos corporativa y evitando inconsistencias.

En las siguientes secciones se presentan los diferentes diagramas que conforman el modelo del dominio: el diagrama de clases, el diagrama de objetos, el diagrama de estados y el glosario de términos, proporcionando una visión completa de la estructura conceptual del sistema.

## Diagrama de Clases

En el contexto de Netkia, el diagrama de clases está directamente alineado con la estructura de datos del ERP Odoo v16, lo que garantiza la consistencia entre la información almacenada en el sistema de gestión y los indicadores generados por el módulo analítico. Cada clase representa una entidad real del entorno organizativo, como clientes, proyectos, tareas, empleados o departamentos, y refleja los atributos necesarios para calcular métricas de productividad, carga de trabajo y rentabilidad.

![Diagrama de clases](./imagenes/diagramaDeClases.png)

Las relaciones entre las clases permiten entender la jerarquía y dependencia entre los elementos del sistema. Por ejemplo, un cliente puede tener múltiples proyectos, cada proyecto puede contener varias tareas, y cada tarea puede estar asociada a diferentes empleados y partes de horas. Esta estructura refleja el flujo natural del trabajo dentro de la empresa y facilita la obtención de indicadores agregados.

## Diagrama de Objetos

El diagrama de objetos complementa al diagrama de clases mostrando una instancia concreta del modelo del dominio en un escenario realista dentro de Netkia SL. Mientras que el diagrama de clases describe la estructura general del sistema, el diagrama de objetos permite observar cómo se materializan esas clases en situaciones reales de uso.

![Diagrama de objetos](./imagenes/diagramaDeObjetos.png)

En este caso, se representa un proyecto real con sus correspondientes tareas, subtareas, empleados asignados y partes de horas registrados. Esta representación facilita la comprensión del funcionamiento del sistema, ya que muestra cómo los datos se relacionan en la práctica y cómo se organizan dentro del ERP.

El objetivo principal de este diagrama es ilustrar el flujo de información desde el cliente hasta las tareas y los registros de horas, permitiendo visualizar de forma clara la interacción entre los distintos elementos del dominio. De esta forma, se puede entender cómo el sistema es capaz de generar métricas a partir de los datos reales de trabajo, como la productividad, la carga de trabajo o la eficiencia de los proyectos.

## Diagrama de Estados

En este apartado se presentan dos diagramas de estados: el primero describe el comportamiento general del sistema de métricas y dashboards, mientras que el segundo representa el ciclo de vida de una tarea dentro del sistema de gestión de proyectos.

### Diagrama de estados del sistema
![Diagrama de estados](./imagenes/diagramaDeEstados.png)

Este diagrama representa el comportamiento general del sistema desde el punto de vista de la sesión del usuario, describiendo cómo transita entre los estados de autenticación, navegación activa y cierre de sesión.

El sistema parte del estado **NoAutenticado**, que es el estado inicial siempre que no exista una sesión activa. Desde aquí se abren dos caminos: si el navegador detecta un token almacenado en `localStorage`, se pasa automáticamente al estado **ValidandoToken**, donde se comprueba si el JWT sigue siendo válido; si en cambio el usuario introduce sus credenciales y pulsa Acceder, se transita al estado **Autenticando**, donde el frontend realiza la llamada `POST /auth/token` al backend.

Si la autenticación falla —por credenciales incorrectas, usuario inexistente o rol `empleado` sin acceso— el sistema pasa al estado **ErrorAuth**, donde se muestra el mensaje de error en el formulario de login sin perder los datos introducidos, permitiendo al usuario reintentar directamente. Si la autenticación es correcta, o si el token almacenado se valida con éxito, el sistema transita a **SesionActiva**.

Dentro de **SesionActiva** se modelan los estados propios de la navegación en la aplicación. Cuando el usuario navega a cualquier ruta, el sistema pasa a **CargandoPagina**, estado en el que el frontend realiza las peticiones a los endpoints del backend. Si alguna petición devuelve un error 4xx o 5xx, se transita a **ErrorCarga**, desde donde el usuario puede reintentar la carga. Si las peticiones se resuelven correctamente, el sistema pasa a **VistaActiva**, estado en el que el usuario puede interactuar con los datos. Desde cualquier vista activa, navegar a otra sección reinicia el ciclo de carga.

La sesión activa finaliza por cierre de sesión voluntario (botón "Cerrar sesión").


### Diagrama de estados de una tarea
![Diagrama de estados de tareas](./imagenes/diagramaDeEstadosDeTareas.png)
Este diagrama refleja las transiciones más comunes dentro de su ciclo de vida, como la creación de la tarea, su asignación a un empleado, el inicio del trabajo, su finalización o su cancelación. Además, contempla la posibilidad de reapertura de tareas cerradas, lo que permite modelar situaciones de retrabajo o correcciones dentro del sistema.

Aunque el módulo de métricas no gestiona directamente la creación o modificación de tareas, las métricas y dashboards dependen del estado en el que se encuentran, por lo que es necesario comprender su evolución.
Este diagrama permite interpretar correctamente los datos obtenidos de la base de datos y entender cómo influyen en los indicadores mostrados en el sistema.

## Requisitos del Sistema

### Requisitos Funcionales

| ID | Descripción | CU | Actor | Prioridad |
|---|---|---|---|---|
| RF-01 | El sistema debe autenticar a los usuarios mediante **login y contraseña** (`res_users.login` y `res_users.password`), verificando el hash PBKDF2-SHA512 almacenado por Odoo, determinar su rol (director/responsable/empleado) y emitir un JWT HS256 de 8 horas con el scope calculado dinámicamente (`employee_ids`, `department_ids`, `project_ids`). | CU-01 | Director, Responsable | Alta |
| RF-02 | El sistema debe listar empleados con paginación server-side (50/pág.), búsqueda por nombre con debounce de 300 ms, filtro por departamento y ordenación global por cualquier columna. | CU-02 | Director, Responsable | Alta |
| RF-03 | El sistema debe mostrar un resumen individual de empleado con KPIs de WIP, carga de trabajo, productividad (últimos 30 días) y tareas vencidas sin cerrar, junto con cuatro pestañas de tareas (pendientes, completadas, asignadas y como responsable) que invocan `GET /tasks/filter` con los parámetros apropiados. | CU-03 | Director, Responsable | Alta |
| RF-04 | El sistema debe listar departamentos activos en cuadrícula de tarjetas y mostrar un resumen de departamento con distribución de carga, tabla de empleados con workload y acceso directo a los perfiles individuales. | CU-04, CU-05 | Director, Responsable | Media |
| RF-05 | El sistema debe listar proyectos activos y mostrar un resumen de proyecto con índice de eficiencia, índice de riesgo, rentabilidad por horas estimadas vs. reales, gráfico comparativo y listado de tareas del proyecto con su equipo asignado. | CU-06, CU-07 | Director, Responsable | Alta |
| RF-06 | El sistema debe listar tareas con filtros combinables: estado (pendiente/completada/vencida), etapa exacta (con prioridad sobre estado), proyecto, empleado, rango de fechas de deadline, fecha de asignación y solo tareas padre (`root_only`). Paginación y ordenación server-side. Todo a través de `GET /tasks/filter`. | CU-08 | Director, Responsable | Alta |
| RF-07 | El sistema debe mostrar el detalle completo de una tarea incluyendo información general, personas (responsable y asignados con enlace a su perfil), sección de horas con barra de progreso y productividad, y lista de subtareas navegables. | CU-09 | Director, Responsable | Alta |
| RF-08 | El sistema debe calcular y mostrar la **Productividad** (`(planificadas/reales)×100`) para tareas cerradas, con gauge visual, gráfico de barras top 8 y promedio. Filtrable por empleado, proyecto y rango de fechas. | CU-10 | Director, Responsable | Media |
| RF-09 | El sistema debe calcular y mostrar el **Cumplimiento de Plazos** (`date_end ≤ date_deadline`) con doughnut chart y semáforo (≥80% verde, ≥60% naranja, <60% rojo). | CU-11 | Director, Responsable | Media |
| RF-10 | El sistema debe calcular y mostrar el **WIP** de un empleado contando tareas abiertas asignadas, con umbral óptimo ≤3, aceptable ≤5 y sobrecargado >5, y recomendación textual. | CU-12 | Director, Responsable | Media |
| RF-11 | El sistema debe calcular y mostrar la **Carga de Trabajo (Workload)** como `(horas_pendientes/40h)×100`, con gauge, barra de progreso y estado sobrecargado/normal/subcargado. | CU-13 | Director, Responsable | Alta |
| RF-12 | El sistema debe calcular y mostrar el **Índice de Riesgo** de un proyecto como porcentaje de tareas abiertas vencidas o con ≥80% del plazo consumido. | CU-14 | Director, Responsable | Media |
| RF-13 | El sistema debe calcular y mostrar la **Eficiencia de Proyecto** como ratio de horas planificadas vs. reales, con índice porcentual, desviación en horas y porcentaje de desviación. | CU-07 | Director, Responsable | Media |
| RF-14 | El sistema debe calcular y mostrar la **Rentabilidad por Horas** de un proyecto como diferencia entre coste estimado y coste real (`hours × hourly_cost`). | CU-07 | Director, Responsable | Media |
| RF-15 | El sistema debe calcular y mostrar la **Tasa de Retrabajo** como porcentaje de tareas cerradas reabiertas, analizando `mail_tracking_value`. | CU-15 | Director, Responsable | Media |
| RF-16 | El sistema debe calcular y mostrar la **Exactitud de Estimación** con ratio medio real/planificado y sesgo (subestima/sobreestima/preciso). | CU-16 | Director, Responsable | Media |
| RF-17 | El sistema debe calcular y mostrar el **Lead Time** medio en días desde asignación hasta cierre. | CU-17 | Director, Responsable | Media |
| RF-18 | El sistema debe calcular y mostrar el **Tiempo por Estado**, con horas medias de permanencia en cada etapa Kanban. | CU-18 | Director, Responsable | Media |
| RF-19 | El sistema debe calcular y mostrar el porcentaje de **Tareas Canceladas** (etapa "Cancelado"). | CU-19 | Director, Responsable | Baja |
| RF-20 | El sistema debe calcular y mostrar el **Tiempo por Prioridad**, con media de horas por nivel Normal/Urgente. | CU-20 | Director, Responsable | Baja |
| RF-21 | El sistema debe mostrar una página de **Gráficos Analíticos** con tres visualizaciones: evolución temporal (LineChart), distribución por etapa (PieChart) y horas por cliente (BarChart, solo Director). | CU-21 | Director, Responsable | Media |
| RF-22 | El sistema debe mostrar una página de **Asistencia vs. Imputaciones** comparando `hr_attendance` con `account_analytic_line` por empleado, con cobertura porcentual, semáforo y serie diaria expandible. | CU-22 | Director, Responsable | Media |
| RF-23 | El sistema debe proporcionar un módulo de **Rentabilidad Financiera** (exclusivo Director) basado en `account_analytic_line.amount`, con desglose global, por proyecto, por cliente y por responsable. | CU-23 | Director | Alta |
| RF-24 | El sistema debe permitir el drill-down de **líneas analíticas por proyecto** desde el módulo de rentabilidad, mostrando ingresos y gastos individuales de `account_analytic_line` con fecha, nombre, importe y horas. | CU-24 | Director | Alta |
| RF-25 | El sistema debe permitir el drill-down de **líneas analíticas por cliente**, agregando las líneas de todos sus proyectos asociados, con las mismas columnas que RF-24 más `project_id`. | CU-25 | Director | Alta |
| RF-26 | El sistema debe proporcionar una **búsqueda global** en tiempo real (debounce 350 ms, mínimo 2 caracteres) de tareas, proyectos y empleados por nombre o código, con filtro por tipo y navegación directa al detalle. | CU-26 | Director, Responsable | Media |
| RF-27 | El sistema debe aplicar **control de acceso basado en roles** en todos los endpoints: Director sin restricción; Responsable con scope del JWT; HTTP 403 en caso contrario. | Todos | Director, Responsable | Alta |
| RF-28 | El sistema debe soportar **paginación y ordenación server-side** en todas las listas con `page`, `page_size`, `sort_by` y `sort_order`. | CU-02, CU-08 | Director, Responsable | Alta |
| RF-29 | El sistema debe presentar las métricas en un **formato visual estandarizado**: tarjeta interactiva con gauge/mini-visualización de preview; al seleccionar, panel de detalle lateral con gráficos y KPIs. Las métricas se agrupan por categoría (proyecto, empleado, generales). | CU-10 a CU-20 | Director, Responsable | Media |
| RF-30 | El sistema debe permitir **limitar los datos por rango de fechas** mediante `date_from` y `date_to` en los endpoints que lo soporten. Debe validar `date_from ≤ date_to` (HTTP 400 si no). El frontend ofrece atajos rápidos (30d, 3m, 6m, 1a) y selector con calendario. | Varios | Director, Responsable | Alta |
| RF-31 | El sistema debe proporcionar un **panel de supervisión global del equipo** que muestre la distribución de los empleados por estado de carga (sobrecargado, normal, subcargado, sin tareas), con el ranking de los empleados más cargados, un gráfico de distribución y la posibilidad de listar los empleados de cada estado con paginación y ordenación. Filtrable por departamento. | CU-28 | Director, Responsable | Media |

---

### Requisitos No Funcionales (Suplementarios)

| ID | Categoría | Descripción |
|---|---|---|
| RNF-01 | **Seguridad — Solo lectura** | El módulo accede a la base de datos de Odoo únicamente en modo lectura. Ninguna operación de escritura, actualización o eliminación está permitida. SQLAlchemy ORM mapea directamente las tablas existentes de Odoo. |
| RNF-02 | **Seguridad — Autenticación JWT** | Todos los endpoints están protegidos mediante JWT HS256. El token incluye `user_id`, `employee_id`, `role`, `employee_ids`, `department_ids` y `project_ids`. Expiración de 8 horas; el frontend detecta HTTP 401 y redirige al login. |
| RNF-03 | **Seguridad — Control de acceso por roles** | Los endpoints del Director devuelven HTTP 403 para cualquier otro rol. Los del Responsable aplican filtros de scope del JWT automáticamente. |
| RNF-04 | **Rendimiento** | Consultas individuales de métricas < 2 s (hasta 20 usuarios concurrentes). Gráficos complejos admiten hasta 5 s. |
| RNF-05 | **Disponibilidad** | Disponible en horario laboral (08:00–20:00 h, L–V). Mantenimiento fuera de horario. |
| RNF-06 | **Mantenibilidad — Arquitectura en capas** | Backend con cuatro capas: `routes → services → data_access → models/schemas`. Las rutas validan y delegan; los servicios contienen lógica de negocio; la capa de datos contiene consultas SQL. |
| RNF-07 | **Extensibilidad** | Añadir nueva métrica = nuevo servicio en `app/services/metrics/` + nueva ruta en `app/routes/metrics.py`, sin modificar código existente. |
| RNF-08 | **Compatibilidad con Odoo** | Compatible con Odoo v16 Enterprise y PostgreSQL 14+. Sin módulos adicionales ni modificación de esquema. CTEs recursivas de PostgreSQL. |
| RNF-09 | **Compatibilidad con navegadores** | Chrome, Firefox y Edge en sus dos últimas versiones. |
| RNF-10 | **Internacionalización** | Nombres JSONB (`{es_ES, en_US}`) se muestran en español con `extract_translated_name()`, fallback a inglés. |
| RNF-11 | **Trazabilidad de datos** | Retrabajo y tiempos por estado se basan en `mail_tracking_value`, garantizando inmutabilidad y trazabilidad. |
| RNF-12 | **Configuración por entorno** | Conexión DB, clave JWT y parámetros del servidor mediante variables de entorno (`.env`). Sin credenciales en código. |
| RNF-13 | **Usabilidad — Tiempo de respuesta percibido** | Skeletons/spinners durante peticiones, estados de error con reintento. Paginación en listas largas. |
| RNF-14 | **Usabilidad — Adaptación al rol** | Navegación y opciones se adaptan automáticamente según rol. |
| RNF-15 | **Escalabilidad** | Pool de conexiones SQLAlchemy (`pool_size=5`, `max_overflow=10`). Paginación server-side para workload masivo. |