# Descripción de casos de uso

## CU-01: Listar particiones

### Actor principal
Responsable de producción

### Precondición
El responsable de producción ha abierto el selector de partición. 

### Flujo principal 
1. Se muestran todas las particiones de las actividades de la orden actual en una 
lista. 

2. El responsable de producción selecciona una partición. 

3. El responsable de producción elige guardar con una partición seleccionada. 


### Flujo alternativo 
  - El responsable de producción no cambia de partición.
  - El responsable de producción elige cancelar. 

### Postcondición
Se realiza el CU-02 con el splitId de la partición elegida por el 
responsable de producción.

## CU-02: Listar partición

### Actor principal
Responsable de producción

### Precondición
El usuario navega a la pestaña Rendimiento de Visual Tracking. 

### Flujo principal 
1. El sistema obtiene los datos de producción de la partición a listar, a partir de su id. Se consultan los servicios y las entidades correspondientes en la base de datos para obtener la información relacionada con el splitId. Esto incluye:
   - Datos de paradas de la estación de trabajo.
   - Artículos producidos.
   - Actividades realizadas durante la producción.
   - Procedimientos indirectos involucrados.
   - Salidas de fabricación. 
   - Rechazos generados.
   - Ejecuciones de la producción.
   - Microparadas detectadas. 
2. El sistema realiza el cálculo del OEE. Primero calcula los indicadores de rendimiento (disponibilidad, rendimiento y calidad) y a partir de ellos el OEE. 
3. Los resultados calculados y los datos de producción correspondientes se muestran gráficamente por pantalla con la siguiente distribución:
   - Curva de velocidad con la producción por hora: gráfica de puntos y líneas. En el eje x las horas transcurridas desde que comenzó la partición y en el eje y la producción por hora en las unidades del material a producir (ej. unidades, kg, L, cajas…).
   - Gráficas de barras: 3 gráficas de barras horizontales con la siguiente información:
     	- OEE actual: mostrará el valor porcentual del OEE en ese instante.
     	- Tiempo transcurrido: mostrará los tiempos: producido, pendiente, de paradas y de indirectos, respecto al tiempo total planificado de la partición.
     	- Total fabricado: mostrará la cantidad producida, pendiente y los rechazos respecto al objetivo total de fabricación de la partición.
    - Tabla comparativa entre valores reales y teóricos de los indicadores del OEE. 


### Flujo alternativo 
    
- Error en la obtención de datos. Error causado por diversas causas: problemas de red, respuesta incorrecta del servidor, falta de parámetros en la llamada, formato incorrecto, splitId inexistente, inconsistencia de datos, tiempo excesivo…

### Postcondición
Se ha listado la partición con todas sus gráficas e información 
correctamente.
