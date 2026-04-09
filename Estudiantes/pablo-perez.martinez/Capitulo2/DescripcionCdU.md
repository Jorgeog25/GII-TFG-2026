# Descripción de casos de uso

## CU-01: Obtención de datos de producción

### Actor principal
Visual Tracking


### Precondición
La orden de trabajo ha sido creada en VT, tiene una o más actividades, ha pasado la fase de asignación y se encuentra en fase de producción. La fase de asignación es el momento en que se asigna a cada actividad de la orden un puesto de trabajo y una fecha y hora para el inicio de su ejecución, en función de la disponibilidad de los puestos de trabajo que incluye y de las duraciones y disponibilidad entre sí de las actividades. Además, se dispone de un splitId del que sacar la información, y parámetros opcionales como fecha de inicio y fecha de fin. 


### Flujo principal: 
1. Al cambiar a la pestaña “Rendimiento” en fase de producción, VT hace una llamada a su API.
2. Dicha llamada utiliza el splitId de la primera partición de la orden como parámetro por defecto. Las fechas de inicio y fin por defecto son tomadas desde la fecha de inicio de la partición hasta la fecha actual respectivamente.
3. El sistema consulta los servicios y las entidades correspondientes en la base de datos para obtener la información relacionada con el splitId. Esto incluye:
    - Datos de paradas de la estación de trabajo.
    - Artículos producidos.
    - Actividades realizadas durante la producción.
    - Procedimientos indirectos involucrados.
    - Salidas de fabricación.
    - Rechazos generados.
    - Ejecuciones de la producción.
    - Microparadas detectadas.
4. Los datos obtenidos se almacenan temporalmente para su posterior uso en la realización de cálculos de OEE e indicadores.


### Flujo alternativo: 
  - Error al realizar la llamada a la API. Puede ser provocado por un fallo de la propia API (problemas de red, respuesta incorrecta del servidor…) o bien por un error en los parámetros de la llamada (falta de parámetros obligatorios, formato incorrecto, splitId no existente…).
  - Error en la obtención de datos. La conexión con la base de datos se ha realizado correctamente pero ha surgido algún error ajeno a ella. Puede venir provocado por inconsistencia de datos, timeout  (tiempo excesivo en carga de datos), errores en la validación… 

### Postcondición
Se dispone de toda la información teórica y real necesaria para realizar los cálculos en la fase posterior.


## CU-02: Cálculo del OEE

### Actor principal
Visual Tracking


### Precondición
Se dispone de toda la información teórica y real necesaria obtenida en el CU-01 para la partición seleccionada. 


### Flujo principal: 
1. El sistema crea una estructura de resultados vacía asociada al splitId sobre la que almacenar los resultados.
2. Dicha estructura almacenará información de fabricación de piezas, información de fabricación de tiempo, información de fabricación de ejecuciones e información de fabricación de indicadores.
3. Se realizará una agrupación eficiente y clara de la información de fabricación de piezas, información de fabricación de tiempos e información de fabricación de ejecuciones . Además se agrupará en un parámetro la información de fabricación por horas desde la fecha de inicio pasada junto al splitId.
4. Se realizará el cálculo de la disponibilidad a partir de la información de tiempos.
5. Se realizará el cálculo del rendimiento a partir de la información de tiempos.
6. Se realizará el cálculo de la calidad a partir de la información de cantidades producidas y rechazos.
7. Se realizará el cálculo del OEE  a partir de la disponibilidad, rendimiento y calidad.
8. Se añadirá la información de indicadores a la estructura de resultados asociada al splitId.


### Flujo alternativo: 
  - Falta o error de datos:
    
    - No hay producción. Se establecerá calidad a 1 (100%) para evitar división entre 0.
    
    - Tiempo transcurrido es 0. Se establecerá disponibilidad a 1 (100%) para evitar división entre 0.
    
    - Tiempo de ciclo es 0. Se establecerá rendimiento a 1 (100%) para evitar división entre 0.

### Postcondición
Los cálculos han sido realizados correctamente y se dispone de una estructura de datos (ManufacturingIndexInfoBySplit) con toda la información necesaria para mostrar al usuario gráficamente en fases posteriores. Dicha información contiene información de fabricación de tiempos, información de fabricación de piezas, información de producción de ejecuciones, información de ejecución de indicadores y un array de la producción por hora.


## CU-03: Comunicación entre backend y frontend.
Actor principal: Visual Tracking
Precondición: Habrá 2 precondiciones, una para cada sentido de la comunicación:
De back a front: se dispone de la estructura de datos ManufacturingIndexInfoBySplit obtenida en el CU-02 con la información calculada y clasificada correctamente.
De front a back: se dispone de un splitId proporcionado por parte del responsable de producción mediante el CU-05.
Flujo principal: Habrá 2 flujos principales, uno para cada sentido de la comunicación:
	De back a front:
El back devuelve al front la estructura ManufacturingIndexInfoBySplit, que contiene la información de ejecuciones, piezas, tiempos, indicadores y curva de velocidad calculada para la partición seleccionada.
El front recibe la respuesta y avanza al siguiente caso de uso (CU-04) para la interpretación y graficación de resultados.
	De front a back:
Desde el filtro del front se selecciona un splitId diferente al actual.
El front construye una solicitud y se la hace al back para obtener los datos de la nueva partición seleccionada.
El back recibe la petición y vuelve al CU-01.

Flujo alternativo: 
Error en la comunicación de back a front: el back procesa la solicitud pero la respuesta no puede devolverse o interpretarse correctamente debido a fallos internos, estructura de datos incompleta o error en el tratamiento de la respuesta en frontend.
Error en la comunicación de front a back: la solicitud no puede enviarse correctamente por problemas de red, formato incorrecto o inexistencia del splitId solicitado.
Postcondición: 
La información calculada correspondiente al splitId seleccionado queda correctamente transferida del back al front y utilizada en el siguiente caso de uso (CU-04) para su interpretación y visualización..
Se vuelve a procesar la solicitud en el CU-01 y calcula los datos de la nueva partición seleccionada.

