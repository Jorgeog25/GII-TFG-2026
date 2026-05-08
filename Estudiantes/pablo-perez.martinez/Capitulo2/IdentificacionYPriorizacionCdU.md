# Identificación y priorización de casos de uso


A continuación, se mencionan brevemente los casos de uso identificados a partir de los requisitos funcionales detallados, los cuales están agrupados según las funcionalidades del sistema:  
- Listar particiones: el sistema debe mostrar una lista con las particiones de cada actividad 
de la orden y permitir seleccionar una para la cual mostrar los datos.
- Listar partición: el sistema obtendrá los datos de producción de la primera partición de 
la primera actividad por defecto, calcula el OEE y grafica los resultados en un mismo 
panel. Esto incluye gráfico de líneas y puntos para producción por horas, gráficos de 
barras para OEE, tiempos y productos fabricados, y tabla comparativa entre valores 
reales de indicadores y valores teóricos. 

## Relación entre casos de uso y requisitos funcionales y priorización

|Caso de uso | Requisitos funcionales | Actor(es) | Prioridad |
|:---:|:---:|:---:|:---:|
|CU-01: Listar particiones | RF09 y RF10 | Responsable de producción | Alta |
|CU-02: Listar partición| RF01, RF02, RF03, RF04, RF05, RF06, RF07 y RF08 | Responsable de producción | Alta |

## Diagrama de casos de uso - Responsable de producción
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de casos de uso - Responsable de producción](./imagenes/diagramaCdUResponsableProduccion.svg)|[Ver Código](./modeloDelDominioImagenes/diagramaCdUResponsableProduccion.puml)

## Diagrama de contexto - Responsable de producción
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de contexto - Responsable de producción](./imagenes/diagramaContextoResponsableProduccion.svg)|[Ver Código](./modeloDelDominioImagenes/diagramaContextoResponsableProduccion.puml)

