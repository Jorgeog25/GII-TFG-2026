# Modelo de Dominio

## Glosario

| Término            | Descripción                                                                                                                          |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Cliente            | Usuario externo que envía solicitudes al sistema mediante correo electrónico y recibe respuestas.                                    |
| Técnico            | Usuario interno encargado de analizar las solicitudes, interpretarlas y gestionarlas dentro del sistema.                             |
| Correo             | Medio de comunicación principal utilizado para enviar solicitudes y recibir respuestas. Contiene la información inicial del cliente. |
| Respuesta          | Comunicación generada como resultado del procesamiento de una solicitud, enviada al cliente.                                         |
| Intención          | Resultado del análisis del contenido del correo que identifica el propósito de la solicitud (consulta, incidencia, etc.).            |
| Petición           | Representación estructurada de la solicitud del cliente tras su interpretación, que puede ser gestionada por el sistema.             |
| Aplicación Interna | Sistema o herramienta corporativa donde el técnico consulta o gestiona la información relacionada con las peticiones.                |

## Diagrama de Clases 
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de Clases](./DdC/imagen/DdC.png)|[Ver Código del Diagrama de Clases](./DdC/codigo/DdC.puml)

El diagrama de clases representa el funcionamiento actual del sistema de gestión del buzón. En él, un cliente envía un correo electrónico, que contiene una intención y, en muchos casos, una petición.

El técnico es el encargado de analizar el correo recibido, identificar su contenido e interpretar la petición. A partir de esta, consulta diferentes aplicaciones internas para obtener la información necesaria.

Finalmente, el técnico genera una respuesta, que es enviada al cliente como contestación a su solicitud.

## Diagrama de Objetos
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de Objetos](./DdO/imagen/DdO.png)|[Ver Código del Diagrama de Objetos](./DdO/codigo/DdO.puml)

El diagrama de objetos representa un caso concreto del funcionamiento del sistema. En él, un cliente envía un correo electrónico con una consulta asociada a una petición identificada por un número. Este correo contiene una intención, en este caso de tipo consulta.

El técnico recibe y analiza el correo, interpreta la petición y accede a una aplicación interna (Petter) para obtener la información correspondiente al número indicado. A partir de estos datos, genera una respuesta informando del estado de la petición, que finalmente es enviada al cliente.

## Diagrama de Estados
Se han elaborado diagramas de estados para las clases más importantes del sistema, concretamente Cliente y Técnico, con el objetivo de representar el cliclo de vida dentro del proceso de gestión del buzón.

### Ciclo de Vida del Cliente
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de Estado](./DdE/imagen/Cliente.png)|[Ver Código del Diagrama de Estado del Cliente](./DdE/codigo/Cliente.puml)

El diagrama de estados del Cliente describe un flujo sencillo en el que este pasa de un estado inactivo a enviar un correo y permanecer en espera hasta recibir una respuesta, tras lo cual vuelve a su estado inicial. Este comportamiento refleja la interacción básica del cliente con el sistema.

### Ciclo de Vida del Técnico
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de Estado](./DdE/imagen/Tecnico.png)|[Ver Código del Diagrama de Estado del Tecnico](./DdE/codigo/Tecnico.puml)

Por otro lado, el diagrama de estados del Técnico representa un proceso más complejo. El técnico comienza en espera de nuevos correos, y cuando recibe uno, lo analiza e interpreta su intención. En función de esta interpretación, decide si es necesario consultar los sistemas internos. En caso afirmativo, realiza consultas a diferentes aplicaciones para obtener la información necesaria. Finalmente, genera y envía una respuesta, regresando al estado inicial a la espera de nuevos correos.

## Requisitos Funcionales
| ID   | Categoría                   | Requisito                                     | Descripción                                                                                                                                                               |
| ---- | --------------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RF1  | Integración con correo      | Integrarse con el buzón de correo corporativo | El sistema debe integrarse con el buzón de correo corporativo existente, basado en Exchange Online, para detectar automáticamente la recepción de nuevas solicitudes. |
| RF2  | Gestión de solicitudes      | Recibir solicitud                             | El sistema debe recibir solicitudes a partir de los correos entrantes detectados en el buzón configurado.                                                                 |
| RF3  | Procesamiento               | Procesar solicitud recibida                   | El sistema debe analizar la solicitud recibida para extraer la información relevante necesaria para su gestión.                                                           |
| RF4  | Comunicación                | Generar respuesta automática                  | El sistema debe generar una respuesta automática en función de la información obtenida durante el procesamiento.                                                          |
| RF5  | Comunicación                | Enviar respuesta                              | El sistema debe enviar la respuesta generada al remitente de la solicitud mediante el servicio de correo.                                                                 |
| RF6  | Gestión de solicitudes      | Incorporar sistema de ampliación de detalle   | El sistema debe permitir incorporar un mecanismo que posibilite al usuario aportar información adicional para ampliar el detalle de una solicitud inicial.                |
| RF7  | Integración con formularios | Integrarse con sistema de formularios         | El sistema debe integrarse con un sistema de formularios para gestionar la información adicional proporcionada.                                |
| RF8  | Gestión de formularios      | Recibir detalle                               | El sistema debe recibir los datos introducidos en el formulario asociado a una solicitud.                                                                                 |
| RF9  | Registro de información     | Registrar información recibida                | El sistema debe almacenar la información necesaria procedente de correos y formularios para su seguimiento.                                                               |
| RF10 | Gestión interna             | Ver solicitudes                               | El técnico debe poder consultar las solicitudes registradas y pendientes de revisión.                                                                                     |
| RF11 | Gestión interna             | Actualizar estado                             | El técnico debe poder actualizar el estado de una solicitud una vez gestionada.                                                                                           |



## Requisitos Suplementarios

| ID   | Categoría      | Requisito                  | Descripción                                                                                        |
| ---- | -------------- | -------------------------- | -------------------------------------------------------------------------------------------------- |
| RNF1 | Rendimiento    | Tiempo de respuesta        | El sistema debe procesar y responder a todas las solicitudes en un tiempo inferior a 10 minutos.   |
| RNF2 | Disponibilidad | Disponibilidad del sistema | El sistema debe estar disponible durante el horario laboral o de forma continua según necesidades. |
| RNF3 | Usabilidad     | Facilidad de uso           | La interacción debe ser sencilla para el cliente, sin necesidad de conocimientos técnicos.         |
| RNF4 | Mantenibilidad | Facilidad de modificación  | El sistema debe permitir cambios en reglas, flujos o procesamiento sin grandes modificaciones.     |
| RNF5 | Extensibilidad | Posibilidad de ampliación  | El sistema debe permitir añadir nuevas funcionalidades o tipos de solicitudes en el futuro.        |
| RNF6 | Fiabilidad     | Procesamiento correcto     | El sistema debe minimizar errores en la interpretación de solicitudes.                             |
| RNF7 | Compatibilidad | Integración con correo     | El sistema debe integrarse correctamente con el servicio de correo electrónico utilizado.          |
| RNF8 | Trazabilidad   | Seguimiento de solicitudes | Debe ser posible registrar y consultar el estado de cada solicitud.                                |

