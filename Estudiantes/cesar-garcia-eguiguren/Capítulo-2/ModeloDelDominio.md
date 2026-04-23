# Modelo del Dominio

El sistema se compone de una aplicación web analítica conectada a la base de datos del ERP en modo lectura y de una base de datos documental propia para persistir las capturas históricas de los paneles calculados. Su función principal es consumir los datos operativos del ERP Odoo, estructurarlos y transformarlos en información útil para la toma de decisiones, permitiendo además guardar el estado de cualquier panel como una *snapshot* consultable posteriormente sin necesidad de recalcular sobre Odoo.

Por ello, el modelo del dominio no introduce nuevas entidades operativas, sino que se apoya en las ya existentes en Odoo, garantizando coherencia con la base de datos corporativa y evitando inconsistencias. La única familia de entidades añadidas por el propio sistema es la de los **snapshots**, que viven en MongoDB y son independientes del ciclo de vida de las entidades de Odoo.

En las siguientes secciones se presentan los diferentes diagramas que conforman el modelo del dominio: el diagrama de clases, el diagrama de objetos, el diagrama de estados y el glosario de términos, proporcionando una visión completa de la estructura conceptual del sistema.

## Diagrama de Clases

En el contexto de Netkia, el diagrama de clases está directamente alineado con la estructura de datos del ERP Odoo v16, lo que garantiza la consistencia entre la información almacenada en el sistema de gestión y los indicadores generados por el módulo analítico. Cada clase del dominio operativo representa una entidad real del entorno organizativo —clientes, proyectos, tareas, empleados o departamentos— y refleja los atributos necesarios para calcular métricas de productividad, carga de trabajo y rentabilidad.

Junto al dominio operativo, el diagrama incorpora el **dominio de snapshots**, que modela los documentos persistidos en MongoDB. Se compone de una clase abstracta `Snapshot` y tres subtipos concretos —`MetricSnapshot`, `ChartSnapshot` y `EntitySnapshot`— que corresponden respectivamente a capturas de métricas, gráficos y fichas de entidad. Una cuarta clase, `SnapshotActor`, actúa como *value object* y registra al usuario responsable de crear o actualizar cada snapshot. La relación entre `EntitySnapshot` y las entidades operativas (empleado, departamento, proyecto, tarea) se modela como una referencia blanda mediante `entity_type` y `entity_id`, al no existir una clave foránea real entre MongoDB y PostgreSQL.

![Diagrama de clases](./imagenes/diagramaDeClases.png)

Las relaciones entre las clases permiten entender la jerarquía y dependencia entre los elementos del sistema. Por ejemplo, un cliente puede tener múltiples proyectos, cada proyecto puede contener varias tareas, y cada tarea puede estar asociada a diferentes empleados y partes de horas. Esta estructura refleja el flujo natural del trabajo dentro de la empresa y facilita la obtención de indicadores agregados. En el dominio de snapshots, cada subtipo mantiene una clave compuesta única —`(metric_name, params_hash, snapshot_date)` para métricas, `(chart_name, params_hash, snapshot_date)` para gráficos y `(entity_type, entity_id, snapshot_date)` para entidades— que garantiza que exista como máximo una snapshot por combinación de tipo, parámetros y día.

## Diagrama de Objetos

El diagrama de objetos complementa al diagrama de clases mostrando una instancia concreta del modelo del dominio en un escenario realista dentro de Netkia SL. Mientras que el diagrama de clases describe la estructura general del sistema, el diagrama de objetos permite observar cómo se materializan esas clases en situaciones reales de uso.

![Diagrama de objetos](./imagenes/diagramaDeObjetos.png)

En este caso, se representa un proyecto real con sus correspondientes tareas, subtareas, empleados asignados y partes de horas registrados. Esta representación facilita la comprensión del funcionamiento del sistema, ya que muestra cómo los datos se relacionan en la práctica y cómo se organizan dentro del ERP.

El objetivo principal de este diagrama es ilustrar el flujo de información desde el cliente hasta las tareas y los registros de horas, permitiendo visualizar de forma clara la interacción entre los distintos elementos del dominio. De esta forma, se puede entender cómo el sistema es capaz de generar métricas a partir de los datos reales de trabajo, como la productividad, la carga de trabajo o la eficiencia de los proyectos. A su vez, una instancia del dominio de snapshots ilustra cómo una `MetricSnapshot` de la métrica de productividad de un empleado concreto captura en un día determinado el resultado calculado sobre los objetos operativos, preservándolo de forma inmutable en MongoDB.

## Diagrama de Estados

En este apartado se presentan dos diagramas de estados: el primero describe el comportamiento general del sistema desde el punto de vista de la sesión del usuario, y el segundo representa el ciclo de vida de una tarea dentro del sistema de gestión de proyectos.

### Diagrama de estados del sistema
![Diagrama de estados](./imagenes/diagramaDeEstados.png)

Este diagrama representa el comportamiento general del sistema desde el punto de vista de la sesión del usuario, describiendo cómo transita entre los estados de autenticación, navegación activa y cierre de sesión. El mismo ciclo aplica tanto al frontend principal como al visor de snapshots: ambos reutilizan el contexto de autenticación y comparten el esquema JWT.

El sistema parte del estado **NoAutenticado**, que es el estado inicial siempre que no exista una sesión activa. Desde aquí se abren dos caminos: si el navegador detecta un token almacenado en `localStorage`, se pasa automáticamente al estado **ValidandoToken**, donde se comprueba si el JWT sigue siendo válido; si en cambio el usuario introduce sus credenciales y pulsa Acceder, se transita al estado **Autenticando**, donde el frontend realiza la llamada `POST /auth/token` al backend.

Si la autenticación falla —por credenciales incorrectas, usuario inexistente o rol `empleado` sin acceso— el sistema pasa al estado **ErrorAuth**, donde se muestra el mensaje de error en el formulario de login sin perder los datos introducidos, permitiendo al usuario reintentar directamente. Si la autenticación es correcta, o si el token almacenado se valida con éxito, el sistema transita a **SesionActiva**.

Dentro de **SesionActiva** se modelan los estados propios de la navegación en la aplicación. Cuando el usuario navega a cualquier ruta, el sistema pasa a **CargandoPagina**, estado en el que el frontend realiza las peticiones a los endpoints del backend. Si alguna petición devuelve un error 4xx o 5xx, se transita a **ErrorCarga**, desde donde el usuario puede reintentar la carga. Si las peticiones se resuelven correctamente, el sistema pasa a **VistaActiva**, estado en el que el usuario puede interactuar con los datos. Desde cualquier vista activa, navegar a otra sección reinicia el ciclo de carga.

Los snapshots, al ser documentos inmutables por día, no modifican el diagrama de estados del sistema: la operación de guardar una snapshot (CU-17) se resuelve dentro de **VistaActiva** como una petición puntual al backend y no genera un estado propio de navegación. Desde el visor (frontend2), la navegación entre la home, los listados de colecciones y la ficha de detalle sigue el mismo ciclo `CargandoPagina → VistaActiva`.

La sesión activa finaliza por cierre de sesión voluntario (botón "Cerrar sesión").


### Diagrama de estados de una tarea
![Diagrama de estados de tareas](./imagenes/diagramaDeEstadosDeTareas.png)
Este diagrama refleja las transiciones más comunes dentro de su ciclo de vida, como la creación de la tarea, su asignación a un empleado, el inicio del trabajo, su finalización o su cancelación. Además, contempla la posibilidad de reapertura de tareas cerradas, lo que permite modelar situaciones de retrabajo o correcciones dentro del sistema.

Aunque el módulo de métricas no gestiona directamente la creación o modificación de tareas, las métricas y dashboards dependen del estado en el que se encuentran, por lo que es necesario comprender su evolución.
Este diagrama permite interpretar correctamente los datos obtenidos de la base de datos y entender cómo influyen en los indicadores mostrados en el sistema.

## Requisitos del Sistema

### Requisitos Funcionales

| RF | Descripción | CU asociado |
|---|---|---|
| RF-01 | Autenticar al usuario mediante usuario y contraseña devolviendo JWT con scope precomputado | CU-01 |
| RF-02 | Listar empleados paginados con búsqueda por nombre | CU-02 |
| RF-03 | Obtener el resumen detallado de un empleado (KPIs, tareas, tiempos) | CU-03 |
| RF-04 | Listar departamentos del ámbito del actor | CU-04 |
| RF-05 | Obtener el resumen de un departamento (plantilla, carga agregada) | CU-05 |
| RF-06 | Listar proyectos del ámbito con filtros básicos | CU-06 |
| RF-07 | Obtener el resumen de un proyecto (eficiencia, riesgo, equipo) | CU-07 |
| RF-08 | Listar tareas con filtros por proyecto, responsable, estado, prioridad | CU-08 |
| RF-09 | Consultar el detalle de una tarea (histórico, imputaciones, subtareas) | CU-09 |
| RF-10 | Consultar cualquiera de las 15 métricas operativas con parámetros dinámicos | CU-10 |
| RF-11 | Calcular Workload en modo agregado de equipo sin `employee_id` | CU-10 (modo equipo) |
| RF-12 | Visualizar gráficos de evolución y distribución de tareas | CU-11 |
| RF-13 | Visualizar gráfico de horas por cliente (solo Director) | CU-11 |
| RF-14 | Comparar asistencia fichada vs horas imputadas por empleado y período | CU-12 |
| RF-15 | Analizar rentabilidad financiera por período con descomposición por proyecto, cliente y responsable (solo Director) | CU-13 |
| RF-16 | Consultar líneas analíticas de drill-down parametrizado por `scope ∈ {proyecto, cliente}` | CU-14 |
| RF-17 | Realizar búsqueda global en tareas, proyectos y empleados con filtrado de ámbito | CU-15 |
| RF-18 | Cerrar sesión e invalidar credenciales locales | CU-16 |
| RF-19 | Guardar snapshot (métrica, gráfico o entidad) con semántica upsert diario | CU-17 |
| RF-20 | Listar snapshots con paginación server-side y filtros por tipo y rango de fechas | CU-18 |
| RF-21 | Consultar el detalle de una snapshot reconstruyendo la vista a partir del JSON guardado | CU-19 |
| RF-22 | Eliminar una snapshot de forma permanente (hard delete) | CU-20 |


---

### Requisitos No Funcionales (Suplementarios)

| ID | Categoría | Descripción |
|---|---|---|
| RNF-01 | **Seguridad — Solo lectura sobre Odoo** | El módulo accede a la base de datos de Odoo únicamente en modo lectura. Ninguna operación de escritura, actualización o eliminación está permitida sobre PostgreSQL. SQLAlchemy ORM mapea directamente las tablas existentes de Odoo. |
| RNF-02 | **Seguridad — Autenticación JWT** | Todos los endpoints están protegidos mediante JWT HS256. El token incluye `user_id`, `employee_id`, `role`, `employee_ids`, `department_ids` y `project_ids`. Expiración de 8 horas; el frontend detecta HTTP 401 y redirige al login. |
| RNF-03 | **Seguridad — Control de acceso por roles** | Los endpoints del Director devuelven HTTP 403 para cualquier otro rol. Los del Responsable aplican filtros de scope del JWT automáticamente. |
| RNF-04 | **Rendimiento** | Consultas individuales de métricas < 2 s (hasta 20 usuarios concurrentes). Gráficos complejos admiten hasta 5 s. |
| RNF-05 | **Disponibilidad** | Disponible en horario laboral (08:00–20:00 h, L–V). Mantenimiento fuera de horario. |
| RNF-06 | **Mantenibilidad — Arquitectura en capas** | Backend con cuatro capas: `routes → services → data_access → models/schemas`. Las rutas validan y delegan; los servicios contienen lógica de negocio; la capa de datos contiene consultas SQL. |
| RNF-07 | **Extensibilidad** | Añadir nueva métrica = nuevo servicio en `app/services/metrics/` + nueva ruta en `app/routes/metrics.py`, sin modificar código existente. El mismo criterio aplica al subsistema de snapshots: añadir un nuevo subtipo de snapshot implica una nueva colección MongoDB con su índice único y un nuevo renderer en el visor. |
| RNF-08 | **Compatibilidad con Odoo** | Compatible con Odoo v16 Enterprise y PostgreSQL 14+. Sin módulos adicionales ni modificación de esquema. CTEs recursivas de PostgreSQL. |
| RNF-09 | **Compatibilidad con navegadores** | Chrome, Firefox y Edge en sus dos últimas versiones. |
| RNF-10 | **Internacionalización** | Nombres JSONB (`{es_ES, en_US}`) se muestran en español con `extract_translated_name()`, fallback a inglés. |
| RNF-11 | **Trazabilidad de datos** | Retrabajo y tiempos por estado se basan en `mail_tracking_value`, garantizando inmutabilidad y trazabilidad. |
| RNF-12 | **Configuración por entorno** | Conexión DB, clave JWT y parámetros del servidor mediante variables de entorno (`.env`). Sin credenciales en código. |
| RNF-13 | **Usabilidad — Tiempo de respuesta percibido** | Skeletons/spinners durante peticiones, estados de error con reintento. Paginación en listas largas. |
| RNF-14 | **Usabilidad — Adaptación al rol** | Navegación y opciones se adaptan automáticamente según rol. |
| RNF-15 | **Escalabilidad** | Pool de conexiones SQLAlchemy (`pool_size=5`, `max_overflow=10`). Paginación server-side para workload masivo. |
| RNF-16 | **Persistencia MongoDB** | El subsistema de snapshots utiliza MongoDB 6+ con `pymongo`. Cada colección (`metric_snapshots`, `chart_snapshots`, `entity_snapshots`) mantiene un índice único sobre su clave compuesta para garantizar la restricción "una snapshot por tipo, parámetros y día" a nivel de base de datos. El acceso a MongoDB es de lectura/escritura, a diferencia del acceso a Odoo (solo lectura). |
| RNF-17 | **Dos frontends, un mismo esquema de autenticación** | El sistema expone dos aplicaciones React/Vite independientes —frontend principal en el puerto 3000 y visor de snapshots en el puerto 3001— que consumen el mismo backend FastAPI y comparten el esquema de autenticación JWT y el `AuthContext`. La separación es intencional: el principal resuelve el caso de uso operativo (decisiones hoy sobre datos vivos); el visor resuelve el caso de uso histórico (consultar el estado de un panel en un día pasado). |