# Identificación y priorización de casos de uso


A continuación, se mencionan brevemente los casos de uso identificados a partir de los requisitos funcionales detallados, los cuales están agrupados según las funcionalidades del sistema:  
- Obtención de datos de producción: el sistema debe obtener los datos teóricos de planificación y los datos reales de producción. Dicha información será la base para la correcta realización de los cálculos.
- Cálculo del OEE: el sistema calculará los indicadores de disponibilidad, rendimiento y calidad a partir de los datos obtenidos. Luego los combinará para calcular el OEE.
- Visualización de datos: el sistema permitirá la visualización gráfica en un mismo panel de todos los datos requeridos. Esto incluye gráfico de líneas y puntos para producción por horas, gráficos de barras para OEE, tiempos y productos fabricados, tooltips informativos sobre tipos de paradas y motivos de rechazos, y tabla comparativa entre valores reales de indicadores y valores teóricos.
- Selección de partición: el sistema debe mostrar una lista con las particiones de cada actividad de la orden y permitir seleccionar una para la cual mostrar los datos.

## Relación entre casos de uso y requisitos funcionales y priorización

|Caso de uso | Requisitos funcionales | Actor(es) | Prioridad |
|:---:|:---:|:---:|:---:|
|CU-01: Obtención de datos de producción | RF01 y RF02 | Responsable de producción | Alta |
|CU-02: Cálculo del OEE | RF03, RF04, RF05 y RF06 | Responsable de producción | Alta |
|CU-03: Visualización de datos | RF07 y RF08 | Responsable de producción | Alta |
|CU-04: Selección de partición | RF09 y RF10 | Responsable de producción | Alta |

## Diagrama de casos de uso - Responsable de producción
![Diagrama de casos de uso - Responsable de producción](./imagenes/diagramaCdUResponsableProduccion.svg)


## Diagrama de contexto - Responsable de producción
![Diagrama de contexto - Responsable de producción](./imagenes/diagramaDeContextoResponsableProduccion.svg)

