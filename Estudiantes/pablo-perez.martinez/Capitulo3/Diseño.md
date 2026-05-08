# Diseño

## Diagrama de clases de diseño
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de clases de diseño](./imagenes/DiagramaClasesDiseno.svg)|[Ver Código](./codigos/DiagramaClasesDiseno.puml) 


## Diagrama de secuencia por caso de uso
### CdU-01: Listar particiones
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de secuencia](./imagenes/DiagramaSecuenciaListarParticiones.svg)|[Ver Código](./codigos/DiagramaSecuenciaListarParticiones.puml)


### CdU-02: Listar partición
Como ya se mencionó anteriormente, el caso de uso de listar partición se dividirá en subtareas 
con el fin de facilitar el análisis y diseño. 

A continuación los diagramas de secuencia de las subtareas: 

#### Obtención de datos de producción
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de secuencia](./imagenes/DiagramaSecuenciaObtencionDatos.svg)|[Ver Código](./codigos/DiagramaSecuenciaObtencionDatos.puml)


#### Cálculo del OEE
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de secuencia](./imagenes/DiagramaSecuenciaCalculoOEE.svg)|[Ver Código](./codigos/DiagramaSecuenciaCalculoOEE.puml)

#### Visualización de datos
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de secuencia](./imagenes/DiagramaSecuenciaVisualizacionDatos.svg)|[Ver Código](./codigos/DiagramaSecuenciaVisualizacionDatos.puml)


## Modelo de datos 

La solución implementada reutiliza el esquema previamente definido en el sistema, 
manteniendo la compatibilidad con el entorno de producción de la empresa. Durante el 
desarrollo, las pruebas se realizaron sobre una base de datos MySQL, aunque el funcionamiento 
previsto de la solución se corresponde con el mismo modelo de datos que utiliza la plataforma 
en explotación. 


Dado que la mejora desarrollada se limita a la pestaña de rendimiento dentro de la fase 
de producción, el análisis de la base de datos se centra únicamente en las tablas que intervienen 
en la obtención de datos y en la obtención de los datos necesarios para construir la información 
mostrada al usuario. En este contexto, la entidad principal es vt_assignment_split, ya que 
representa una partición concreta de una actividad dentro de una orden de producción y 
constituye el punto de partida de toda la consulta. A partir de su identificador, 
assignmentSplitId, el backend recupera los datos necesarios para construir la respuesta, por lo 
que esta tabla actúa como eje del modelo empleado en esta funcionalidad. 


Desde vt_assignment_split se establecen relaciones directas con varias tablas vinculadas a la ejecución real del proceso productivo. Entre ellas destacan 
vt_manufacturing_output, que almacena la producción realizada; vt_manufacturing_scrap, que recoge rechazos o mermas; vt_manufacturing_stop y vt_manufacturing_microstop, que 
registran paradas y microparadas; y vt_manufacturing_execution,  donde se registran las ejecuciones de fabricación. También se utiliza vt_manufacturing_activity,  que permite 
contextualizar la actividad de producción asociada al split, y vt_indirect_procedure, que aporta información sobre procedimientos indirectos relevantes para el análisis de indisponibilidades. 
En estos casos, la relación se establece de forma directa o lógica mediante el identificador de la partición.

Además de los datos reales de producción, se necesita información teórica de referencia 
para interpretar correctamente los resultados. Esta parte del modelo se apoya en vt_route_step 
y, especialmente, en vt_route_step_workstation, tabla desde la cual se obtienen valores 
teóricos asociados al puesto de trabajo, como tiempos de ciclo u otros parámetros utilizados 
como base para el cálculo de indicadores. Además, vt_assignment_split se relaciona con esta 
parte teórica a través de los campos route_step_id y workstation_id, lo que permite enlazar la 
ejecución real con la configuración prevista del proceso.

Aunque la mayor parte de la información utilizada en esta funcionalidad se obtiene a 
partir de relaciones directas con vt_assignment_split, esta tabla también se integra de forma 
indirecta en una estructura más amplia del proceso productivo. En este sentido, 
vt_assignment_split se vincula con vt_assignment, vt_item y vt_indirect_procedure dentro de 
la jerarquía general del sistema, aportando contexto sobre la asignación, el elemento productivo 
y los procedimientos asociados. Aunque estas relaciones no siempre se materializan mediante 
FK directa dentro del flujo principal de consulta, sí permiten situar la partición analizada dentro 
del marco funcional completo de VT. 

La base de datos, por tanto, no almacena directamente los indicadores de rendimiento ni 
el valor final del OEE, sino los datos base del proceso: producción, rechazos, ejecuciones, 
microparadas, actividades, procedimientos indirectos y parámetros teóricos. A partir de esta 
información, el backend realiza los cálculos necesarios y construye la respuesta que 
posteriormente consume el frontend. Este mismo enfoque se aplica a la curva de velocidad, que 
se obtiene a partir de los registros de vt_manufacturing_output, agrupando en memoria la 
producción por intervalos horarios dentro del rango temporal seleccionado.

En conjunto, el modelo de datos utilizado en la pestaña de rendimiento combina información real de fabricación con datos teóricos del proceso, tomando vt_assignment_split como entidad central. Esta organización permite reutilizar el modelo ya implantado en Visual Tracking, mantener la compatibilidad con el entorno de producción y disponer de una base suficientemente completa para calcular y mostrar al usuario los indicadores de rendimiento de una partición concreta. La figura 24 presenta de forma simplificada las principales tablas 
implicadas y las relaciones más relevantes dentro de esta funcionalidad. 


### Tablas de la base de datos
![Tablas BD](./imagenes/tablasBD.jpg)

### Diseño de la interfaz de rendimiento
![Wireframe Pantalla principal](./imagenes/WireframePantallaPrincipal.png)


### Diseño de la ventana del selector de partición
![Wireframe Pantalla principal](./imagenes/WireframeSelector.png)

