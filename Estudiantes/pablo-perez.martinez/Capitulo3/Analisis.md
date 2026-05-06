# Análisis 

La solución a desarrollar se integra en la arquitectura existente de EMI Suite, 
concretamente en el módulo Visual Tracking, sobre el que se ha implementado una mejora 
funcional en la fase de producción. Por tanto, no se parte del diseño de un sistema 
completamente nuevo, sino de la extensión de una plataforma empresarial consolidada, 
respetando su estructura interna y las tecnologías previamente empleadas. 

En la funcionalidad a implementar intervienen distintos elementos que pueden 
agruparse siguiendo el patrón Modelo-Vista-Controlador, con el fin de diferenciar entre la lógica 
de negocio, la representación de los datos y la interacción del usuario. 

## Modelo-Vista-Controlador
| Diagrama | Código Fuente |
|----------|---------------|
|![Modelo-Vista-Controlador](./imagenes/MVC.svg)|[Ver Código](./codigos/MVC.puml) 

En el modelo se incluyen: 
- AssignmentSplit: entidad a partir de la que se obtiene toda la información teórica y de 
producción.
- ManufacturingIndexInfoBySplitDto: objeto de transferencia de datos de respuesta, 
almacena toda la información calculada.
- ManufacturingInfoBySplitToCalculateDto: objeto de transferencia de datos de entrada 
con la información de la partición.
- ManufacturingInfoServiceV2 de API: contiene toda la lógica para la realización de los 
cálculos a partir de ManufacturingInfoBySplitToCalculateDto.
- ManufacturingInfoService de vt-master-api: actúa como intermediario entre los dos 
controladores, ManufacturingInfoController de API y ManufacturingInfoController de 
vt-master-api.


En las vistas intervienen los siguientes componentes:
- Performance: pestaña principal que muestra los distintos elementos:
  - PerformanceSpeedCurve: curva de velocidad que muestra la producción por 
hora.
  - PerformanceInfoGraphs: gráficos de barras que muestran información del OEE, 
de tiempos (transcurrido, paradas, indirectos y restante) y de fabricación 
(cantidad producida, rechazada y restante).
  - PerformanceTable: tabla que compara entre el valor real y el objetivo de 
indicadores: calidad, rendimiento, disponibilidad y OEE.
  - PerformanceSplitState: componente que permite seleccionar entre los distintos 
assignmentSplits en los que están particionadas una o varias actividades de una 
orden de trabajo.

En cuanto a los controladores, intervienen los puntos de entrada encargados de recibir 
las peticiones y coordinar la comunicación entre la vista y el modelo: 
- ManufacturingInfoController de vt-master-api: recibe solicitud para el cálculo de 
indicadores y delega la operación en el servicio ManufacturingInfoService de 
vt-master-api.
- ManufacturingInfoController de API: recibe la petición procedente de vt-master-api y la 
delega en ManufacturingInfoServiceV2, y una vez se han realizado los cálculos 
devuelve la respuesta estructurada para que pueda utilizarse por las vistas.


## Diagramas de colaboración

### CdU-01: Listar particiones
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de colaboración](./imagenes/ListarParticiones.svg)|[Ver Código](./codigos/ListarParticiones.puml)

### CdU-02: Listar particion

Para facilitar la comprensión del comportamiento del sistema, se ha dividido el caso de uso 
“Listar partición” CdU-02 en tres subtareas para representar de forma clara, ordenada y precisa 
las interacciones entre los objetos implicados. 

Las subtareas en las que se ha fragmentado el análisis y diseño del CdU-02 son: 
- Obtención de datos de producción.
- Cálculo del OEE.
- Visualización de datos.


#### Obtención de datos de producción
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de colaboración](./imagenes/ObtencionDatosProduccion.svg)|[Ver Código](./codigos/ObtencionDatosProduccion.puml)

  
#### Cálculo del OEE
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de colaboración](./imagenes/CalculoOEE.svg)|[Ver Código](./codigos/CalculoOEE.puml)


#### Visualización de datos
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de colaboración](./imagenes/VisualizacionDatos.svg)|[Ver Código](./codigos/VisualizacionDatos.puml)

  
## Selección de tecnologías

A continuación se describen las tecnologías seleccionadas, ya utilizadas previamente 
sobre el ecosistema implantado en EMI Suite, con el objetivo de mantener la coherencia con la 
arquitectura existente y facilitar la integración de la mejora dentro del producto. 

### Backend

- Java 21: Lenguaje de programación principal utilizado en la parte servidora. Se ha elegido este 
lenguaje porque es una versión moderna y estable de Java, con soporte a largo plazo, lo que 
permite mantener compatibilidad con el ecosistema empresarial y asegurar la evolución futura 
del proyecto.
- Spring Boot 3.5: Framework empleado para estructurar y desarrollar la lógica del backend. Su 
uso resulta adecuado porque simplifica la creación de servicios, la configuración del proyecto y 
la integración con otras tecnologías de Spring ya presentes en la plataforma.
- Spring MVC: Tecnología utilizada para exponer los endpoints y gestionar las peticiones 
procedentes del frontend. Se recomienda porque permite organizar de forma clara los 
controladores REST, facilitando la separación entre la entrada de peticiones y la lógica de 
negocio.
- Spring Data JPA: Capa de acceso a datos empleada para interactuar con la base de datos 
relacional. Su uso facilita la consulta y manipulación de entidades sin necesidad de escribir 
manualmente gran parte del código SQL, reduciendo la complejidad del acceso a datos.
- Hibernate: Herramienta de mapeo objeto-relacional utilizada en la gestión de entidades y 
persistencia. Se recomienda porque permite trabajar con objetos Java vinculados a tablas de 
base de datos, manteniendo una estructura coherente entre el modelo de dominio y la 
persistencia.
- Maven: Sistema de construcción y gestión de dependencias del proyecto. Su uso resulta 
adecuado porque permite centralizar las dependencias, automatizar la compilación y mantener 
una estructura homogénea con el resto de módulos backend del ecosistema.
- JSON Web Token (JWT): Mecanismo de autenticación utilizado de forma general en la 
plataforma. Se recomienda porque permite transmitir la identidad del usuario de forma segura 
entre frontend y backend, evitando tener que gestionar sesiones tradicionales en servidor.
- OpenAPI /Swagger: Herramienta empleada para la documentación y visualización de servicios 
API. Su uso facilita la consulta de los endpoints disponibles, la validación de las peticiones y la 
coordinación entre frontend y backend durante el desarrollo.


### Frontend

- React 18: Librería principal utilizada para la construcción de la interfaz de usuario. Se 
recomienda porque permite desarrollar interfaces mediante componentes reutilizables, lo que 
encaja con la estructura modular de la pestaña de rendimiento.
- JavaScript: Lenguaje principal empleado en el desarrollo del frontend. Su uso es necesario 
porque constituye la base del ecosistema React utilizado en el proyecto y permite mantener 
compatibilidad con el código existente.
- React Scripts: Herramienta utilizada para la ejecución y construcción del proyecto frontend. Se 
recomienda porque simplifica tareas como el arranque del entorno de desarrollo, la generación 
de la versión compilada y la configuración interna del proyecto.
- Material-UI: Librería principal de componentes y estilos para la interfaz. Su uso resulta 
adecuado porque permite mantener una apariencia visual coherente con el resto de la aplicación, 
reutilizando componentes ya integrados en el producto.
- React Query: Librería utilizada para la gestión de llamadas al backend, caché y estado servidor. 
Se recomienda porque facilita la obtención y actualización de datos procedentes de la API, reduciendo la necesidad de gestionar manualmente estados de carga, error y refresco de 
información.
- Redux / React Redux / Redux Thunk: Conjunto de herramientas utilizadas para la gestión del 
estado global de la aplicación. Su uso resulta conveniente cuando ciertos datos deben 
compartirse entre distintos componentes, manteniendo un flujo de información centralizado y 
predecible.
- React Router DOM: Librería empleada para la gestión de rutas y navegación dentro del 
frontend. Se recomienda porque permite organizar las distintas pantallas o secciones de la 
aplicación, facilitando la integración de la pestaña de rendimiento dentro de la estructura 
existente.
- Highcharts y Highcharts React Official: Librerías utilizadas para la representación de 
indicadores y métricas en la pestaña de rendimiento. Su uso resulta adecuado porque permiten 
crear gráficos interactivos y personalizables, necesarios para visualizar la curva de velocidad, 
los indicadores y la evolución de la producción.
- Moment, Moment Range y Moment Timezone: Librerías utilizadas para la gestión de fechas, 
rangos temporales y zonas horarias. Se recomiendan porque la funcionalidad de rendimiento 
trabaja con periodos de producción, fechas de inicio y fin, y cálculos temporales que requieren 
un tratamiento consistente.
- Sass: Preprocesador CSS empleado en la definición de estilos. Su uso facilita la organización de 
hojas de estilo más mantenibles, permitiendo reutilizar variables, anidar reglas y estructurar 
mejor los estilos de la interfaz.
- Paquetes corporativos “@snc/*”: Conjunto de librerías internas utilizadas para componentes, 
configuración, utilidades y elementos comunes del ecosistema de la empresa. Se recomienda su 
uso porque garantiza la coherencia con el resto de EMI Suite y evita duplicar funcionalidades ya 
existentes dentro de la plataforma. 


