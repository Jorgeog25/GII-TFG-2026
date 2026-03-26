# Requisitos funcionales

Los requisitos funcionales describen las funciones que debe ofrecer la solución desarrollada para dar respuesta a las necesidades identificadas en el apartado anterior. Igual que anteriormente, los dividiremos en los requisitos funcionales correspondientes al módulo de backend, para el tratamiento y cálculo de la información, y los requisitos funcionales correspondientes al módulo de frontend, centrados en la visualización y explotación de los resultados dentro de VT.

La solución debe cubrir los siguientes requisitos funcionales:
## Módulo de backend
- Obtención de datos de planificación
- Obtención de datos reales de producción
- Cálculo de indicadores: disponibilidad, calidad y rendimiento.
- Cálculo del OEE.
- Realización de cálculos filtrando por fechas.
- Suministro de datos al frontend.
## Módulo de frontend
- Comunicación con backend para recibir datos.
- Representación gráfica de los resultados
- Comparación entre valores teóricos y reales.
- Filtro por fechas.

## Especificación de requisitos funcionales
|Código | Descripción |
|:---:|:---|
|RF01 | Obtención de los datos teóricos de planificación de la base de datos. La mayoría a partir del puesto de trabajo |
|RF02 | Obtención de los datos reales de producción de la base de datos. Información de actividades, productos completados, rechazos, microparadas, ejecuciones… |
|RF03 | Cálculo de disponibilidad a partir de información de tiempos. |
|RF04 | Cálculo de rendimiento a partir de información de tiempos. |
|RF05 | Cálculo de calidad a partir de información de cantidades producidas y rechazos. |
|RF06 | Cálculo de OEE a partir de disponibilidad, rendimiento y calidad. |
|RF07 | Realización de cálculos con filtro por fecha de inicio y/o fecha de fin. |
|RF08 | Suministro al frontend de los datos calculados y la información a representar. |
|RF09 | Comunicación con backend para recepción de los cálculos realizados. |
|RF10 | Visualización gráfica de los resultados: gráfico de líneas y puntos para mostrar producción de uds./hora y gráficas de barras para OEE e indicadores. |
|RF11 | Tabla comparativa entre valores reales y teóricos de los indicadores del OEE. |
|RF12 | Filtro por fechas para obtener resultados en un período acotado. |

