# Disciplina de Requisitos
## Actores
| Diagrama | Código Fuente |
|----------|---------------|
|![Actores](./Actores/imagen/Actores.png)|[Ver Código de Actores](./Actores/codigo/Actores.puml)

El Técnico constituye el único actor humano del sistema, ya que es quien accede a la información procesada, consulta los formularios pendientes y realiza la actualización del estado de las solicitudes. Su interacción es directa y necesaria para la gestión operativa del sistema.

Por otro lado, se consideran actores externos los servicios que generan los eventos que desencadenan la ejecución de los flujos automatizados. En este sentido, el servicio de correo Exchange Online actúa como origen de eventos cuando se recibe un nuevo mensaje, activando el flujo principal del sistema.

De forma análoga, Microsoft Forms se identifica como actor externo al generar eventos cuando un formulario es completado, lo que da lugar a la ejecución de flujos secundarios.

## Casos De Uso Por Actor

| Diagrama  | Código |
|---------|---------|
|![CdU_ExchangeOnline](./CdU/imagen/CdU_Exchange.png)|[Ver código](./CdU/codigo/CdU_Exchange.puml)|
|![CdU_Forms](./CdU/imagen/CdU_Forms.png)|[Ver código](./CdU/codigo/CdU_Forms.puml)|
|![CdU_Técnico](./CdU/imagen/CdU_Tecnico.png)|[Ver código](./CdU/codigo/CdU_Tecnico.puml)|

## Relación Casos de Uso con Requisitos Funcionales

| Caso de Uso           | Requisitos Funcionales relacionados                                                                                                                               |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CA1 Recibir solicitud | RF1 Integrarse con el buzón de correo corporativo, RF2 Recibir solicitud, RF3 Procesar solicitud recibida, RF4 Generar respuesta automática, RF5 Enviar respuesta |
| CA2 Recibir formulario   | RF6 Incorporar sistema de ampliación de detalle, RF7 Integrarse con sistema de formularios, RF8 Recibir detalle                                                   |
| CA3 Ver solicitudes   | RF9 Registrar información recibida, RF10 Ver solicitudes, RF11 Actualizar estado                                                                                  |


## Flujo de Procesos por Entidad

### Solicitud

| Diagrama | Código |
|---------|---------|
|![Flujo](./FlujoEntidades/imagen/Flujo_Solicitud.png)|[Ver código](./FlujoEntidades/codigo/Flujo_Solicitud.puml)|

El flujo de la entidad Solicitud comienza con la recepción de un correo electrónico en el buzón corporativo, gestionado mediante Exchange Online. Este evento activa el sistema de automatización, que inicia el procesamiento de la solicitud.

En primer lugar, el sistema analiza el asunto del correo con el objetivo de clasificar la solicitud según una serie de reglas predefinidas. En función del contenido del asunto, se contemplan cuatro posibles escenarios. Si el asunto indica que un formulario ha sido resuelto (“TE:FORMRESUELTO”), el sistema procede a actualizar el estado del formulario correspondiente. Si el correo corresponde al envío de documentación de acuerdo o desacuerdo, el sistema clasifica el mensaje y lo mueve automáticamente a la carpeta correspondiente para su gestión documental.

En caso de que el asunto no coincida con ninguno de los escenarios anteriores, la solicitud pasa a una fase de análisis más detallado. En esta etapa, el sistema identifica la intención del mensaje, clasificándola en categorías como consulta, reclamación o situaciones de posible riesgo asociadas a un identificador numérico.

Si la intención identificada es válida, el sistema realiza una consulta a las bases de datos disponibles para obtener la información necesaria. A partir de los datos recuperados, se lleva a cabo el procesamiento de la solicitud y se genera una respuesta automática adaptada al contexto. Posteriormente, el sistema registra la información relevante de la solicitud, incluyendo los datos procesados y el responsable asignado, y finalmente envía la respuesta al remitente.

Por el contrario, si la intención de la solicitud no se corresponde con ninguno de los casos contemplados, el sistema responde mediante el envío de un correo basado en una plantilla predefinida, garantizando así una respuesta consistente.

De este modo, el flujo de la entidad Solicitud combina una lógica basada en reglas para el tratamiento inicial con un procesamiento más avanzado en los casos necesarios, asegurando una gestión automatizada, estructurada y completa de todas las solicitudes recibidas.

### Formulario

| Diagrama | Código |
|---------|---------|
|![Flujo](./FlujoEntidades/imagen/Flujo_Formulario.png)|[Ver código](./FlujoEntidades/codigo/Flujo_Formulario.puml)

El flujo de la entidad Formulario se inicia con la ejecución del caso de uso CA2 Recibir formulario, momento en el cual el sistema recibe la información introducida por el usuario a través del formulario.

Una vez recibidos los datos, el sistema realiza una evaluación específica para determinar si se ha seleccionado alguna opción relacionada con el envío de documentación. En caso afirmativo, se desencadena una acción adicional consistente en el envío automático de un correo electrónico con las instrucciones necesarias para remitir dicha documentación.

Es importante destacar que esta acción no altera el flujo principal del proceso. Independientemente de si se ha solicitado el envío de documentación o no, el sistema continúa con el procesamiento de los datos del formulario. En esta fase, se tratan las respuestas proporcionadas y se prepara la información para su almacenamiento.

Finalmente, el sistema registra todos los datos del formulario en la base de datos, garantizando su disponibilidad para consultas posteriores y su integración en la gestión global de solicitudes.

De este modo, el flujo de la entidad Formulario combina un procesamiento uniforme de la información con la ejecución de acciones adicionales condicionadas, asegurando una gestión eficiente y estructurada de los datos recibidos.

## Diagramas de Contexto  

| Diagrama | Código |
|---------|---------|
|![Diagrama de Contexto](./DdC/imagen/DdC_Tecnico.png)|[Ver código](./DdC/codigo/DdC_Tecnico.puml)|

El presente diagrama muestra la interacción entre el Técnico y la Vista Power BI, que actúa como interfaz de consulta de la información procesada por el sistema.

El técnico no interactúa directamente con el sistema de automatización, sino que accede a los datos a través de una vista desarrollada en Power BI, donde se presentan las solicitudes registradas y su estado.

Esta vista permite al técnico consultar de forma estructurada la información almacenada, facilitando el seguimiento y análisis de las solicitudes gestionadas. De este modo, Power BI actúa como una capa intermedia de visualización, desacoplando la gestión de datos del acceso por parte del usuario.

En consecuencia, la interacción del técnico se limita a la consulta de información, sin intervenir directamente en los procesos automatizados del sistema.

| Diagrama | Código |
|---------|---------|
|![Diagrama de Contexto](./DdC/imagen/DdC_Exchange.png)|[Ver código](./DdC/codigo/DdC_Exchange.puml)|

Este diagrama representa la interacción entre Exchange Online y el sistema de automatización. Exchange Online actúa como servicio externo de correo corporativo y es el origen del evento que inicia el flujo principal. Cuando se recibe un nuevo correo en el buzón configurado, el sistema detecta la solicitud y comienza su procesamiento automático.

| Diagrama | Código |
|---------|---------|
|![Diagrama de Contexto](./DdC/imagen/DdC_Forms.png)|[Ver código](./DdC/codigo/DdC_Forms.puml)|

Este diagrama muestra la relación entre Microsoft Forms y el sistema. Microsoft Forms actúa como servicio externo encargado de recoger información adicional mediante formularios. Cuando un formulario es completado, el sistema recibe los datos introducidos y continúa con su procesamiento y registro en la base de datos.

## Priorizar Casos de Uso 

| ID  | Caso de uso                | Prioridad | Justificación                                                                              |
| --- | -------------------------- | --------- | ------------------------------------------------------------------------------------------ |
| CA1 | Enviar solicitud           | Alta      | Es el punto de entrada del sistema y condición necesaria para el resto de funcionalidades. |
| CA2 | Recibir respuesta          | Alta      | Constituye la finalidad principal del sistema: proporcionar respuesta al cliente.          |
| CA3 | Ver solicitudes pendientes | Media     | Permite la gestión por parte del técnico, pero depende de solicitudes previas.             |
| CA4 | Actualizar estado          | Media     | Necesario para la gestión interna, ligado al seguimiento de solicitudes.                   |
| CA5 | Completar formulario       | Baja      | Funcionalidad complementaria para aportar información adicional en casos específicos.      |


## Detallar Casos de Uso

### Caso de Uso - Enviar Solicitud

| Diagrama | Código |
|---------|---------|
|![CdU](./Detallar_CdU/imagen/EnviarSolicitud.png)|[Ver código](./Detallar_CdU/codigo/EnviarSolicitud.puml)|

Este caso de uso describe el proceso mediante el cual el cliente genera y envía una solicitud al sistema. El flujo comienza cuando el cliente identifica una necesidad, ya sea un problema o una consulta, lo que le lleva a redactar la solicitud.

Antes de enviarla, el cliente revisa su contenido para comprobar que la información es correcta. En caso de no estar conforme, puede modificarla tantas veces como sea necesario. Una vez validada, la solicitud es enviada al sistema.

Este proceso garantiza que las solicitudes recibidas tengan un mínimo nivel de calidad y coherencia, facilitando su posterior procesamiento.

**Criterios de Aceptación**
+ El cliente puede redactar una solicitud con la información necesaria.
+ El cliente puede modificar la solicitud antes de enviarla.
+ El sistema permite el envío únicamente cuando la solicitud contiene información válida.
+ La solicitud queda registrada en el sistema tras su envío.
+ El sistema confirma la recepción de la solicitud al cliente.

### Caso de Uso - Recibir Respuesta

| Diagrama | Código |
|---------|---------|
|![CdU](./Detallar_CdU/imagen/RecibirRespuesta.png)|[Ver código](./Detallar_CdU/codigo/RecibirRespuesta.puml)|

Este caso de uso describe el proceso mediante el cual el cliente recibe una respuesta tras haber enviado una solicitud al sistema.

El flujo comienza con el cliente en espera de una respuesta. A continuación, el sistema procesa la solicitud previamente enviada y genera una respuesta, que es posteriormente enviada al cliente.

Finalmente, el cliente recibe y revisa la respuesta. En caso de necesitar aportar información adicional, podrá iniciar un nuevo caso de uso independiente mediante el formulario.

**Criterios de aceptación**

+ El sistema procesa la solicitud previamente enviada por el cliente.
+ El sistema genera una respuesta adecuada en función de la solicitud.
+ La respuesta es enviada correctamente al cliente.
+ El cliente puede recibir y visualizar la respuesta.
+ El cliente puede iniciar un nuevo proceso en caso de requerir aportar información adicional.

### Caso de Uso - Ver Solicitudes Pendientes

| Diagrama | Código |
|---------|---------|
|![CdU](./Detallar_CdU/imagen/VerSolicitudesPendientes.png)|[Ver código](./Detallar_CdU/codigo/VerSolicitudesPendientes.puml)|

Este caso de uso describe el proceso mediante el cual el técnico accede y consulta las solicitudes pendientes en el sistema.

El flujo comienza cuando el técnico accede a la vista correspondiente. El sistema recupera la información disponible y la presenta al técnico, quien puede revisar el estado y contenido de las solicitudes.

En caso de producirse un error en la carga de la información, el sistema no podrá mostrar las solicitudes.

**Criterios de aceptación**

+ El técnico puede acceder a la vista de solicitudes pendientes.
+ El sistema muestra correctamente las solicitudes no resueltas.
+ La información presentada incluye los datos necesarios para su revisión.
+ El sistema gestiona errores en la carga de datos mostrando un mensaje adecuado.
+ Las solicitudes se muestran actualizadas en el momento de la consulta.

### Caso de Uso - Actualizar Estado

| Diagrama | Código |
|---------|---------|
|![CdU](./Detallar_CdU/imagen/ActualizarEstado.png)|[Ver código](./Detallar_CdU/codigo/ActualizarEstado.puml)|

Este caso de uso describe el proceso mediante el cual el técnico actualiza el estado de una solicitud en el sistema.

El flujo comienza cuando el técnico selecciona una solicitud pendiente y la marca como resuelta. A continuación, el sistema verifica si existe un formulario asociado a dicha solicitud. En caso de existir, el sistema registra el nuevo estado. Si no existe formulario, el proceso finaliza sin realizar ninguna modificación.

Este comportamiento garantiza la coherencia de los datos y evita cambios innecesarios en el sistema.

**Criterios de aceptación**

+ El técnico puede marcar la solicitud como resuelta.
+ El sistema verifica la existencia de un formulario asociado.
+ Si el formulario existe, el estado de la solicitud se actualiza correctamente.
+ Si no existe formulario, el sistema no realiza cambios en el estado.
+ El sistema refleja el estado actualizado de forma correcta tras la operación.

### Caso de Uso - Completar Formulario

| Diagrama | Código |
|---------|---------|
|![CdU](./Detallar_CdU/imagen/CompletarFormulario.png)|[Ver código](./Detallar_CdU/codigo/CompletarFormulario.puml)|

Este caso de uso describe el proceso mediante el cual el cliente aporta información adicional a través de un formulario.

El flujo comienza cuando el cliente accede al formulario y completa los campos requeridos. A continuación, revisa la información introducida antes de enviarla. Si está conforme, el formulario es enviado al sistema. En caso contrario, puede modificar los datos antes de realizar el envío.

Este proceso permite complementar la información de una solicitud previa de forma estructurada y controlada.

**Criterios de aceptación**

+ El cliente puede crear un formulario para detallar su solicitud.
+ El cliente puede completar los campos requeridos del formulario.
+ El cliente puede revisar y modificar la información antes de enviarla.
+ El sistema valida que los campos obligatorios estén cumplimentados.
+ El formulario es enviado correctamente al sistema.
+ La información adicional queda registrada y asociada a la solicitud correspondiente.

## Prototipar Casos de Uso 

### Caso de Uso - Enviar Solicitud

![Prototipo](./Prototipar_CdU/EnviarSolicitud.png)

### Caso de Uso - Recibir Respuesta

![Prototipo](./Prototipar_CdU/RecibirRespuesta.png)

### Caso de Uso - Ver Solicitudes Pendientes

![Prototipo](./Prototipar_CdU/VerSolicitudesPendientes.png)

### Caso de Uso - Actualizar Estado

![Prototipo](./Prototipar_CdU/ActualizarEstado.png)

### Caso de Uso - Completar Formulario

![Prototipo](./Prototipar_CdU/CompletarFormulario.png)

## Estructurar la Descripción de los Casos de Uso

### CA1 – Enviar solicitud

- **Actor:** Cliente  

- **Descripción:**  
El cliente genera y envía una solicitud al sistema para comunicar una necesidad o incidencia.

- **Precondiciones:**  
- El cliente dispone de la información necesaria para redactar la solicitud.  

- **Postcondiciones:**  
- La solicitud queda registrada en el sistema.  

- **Flujo principal:**  
1. El cliente redacta la solicitud.  
2. El cliente revisa la información introducida.  
3. El cliente envía la solicitud.  
4. El sistema registra la solicitud.  

- **Flujos alternativos:**  
- 2a. El cliente detecta errores → modifica la solicitud antes de enviarla.  

- **Criterios de aceptación:**  
- La solicitud contiene información mínima obligatoria.  
- El sistema confirma la recepción de la solicitud.  

### CA2 – Recibir respuesta

- **Actor:** Cliente  

- **Descripción:**  
El cliente recibe la respuesta generada por el sistema tras el procesamiento de su solicitud.

- **Precondiciones:**  
- Existe una solicitud previamente enviada.  

- **Postcondiciones:**  
- El cliente dispone de una respuesta asociada a su solicitud.  

- **Flujo principal:**  
1. El sistema procesa la solicitud.  
2. El sistema genera una respuesta.  
3. El sistema envía la respuesta al cliente.  
4. El cliente recibe y visualiza la respuesta.  

- **Flujos alternativos:**  
- 1a. La información es insuficiente → el sistema solicita información adicional.  

- **Criterios de aceptación:**  
- La respuesta es generada correctamente.  
- El cliente puede visualizar la respuesta.  

### CA3 – Ver solicitudes pendientes

- **Actor:** Técnico  

- **Descripción:**  
El técnico consulta las solicitudes o formularios pendientes para su gestión.

- **Precondiciones:**  
- Existen solicitudes o formularios pendientes en el sistema.  

- **Postcondiciones:**  
- El técnico visualiza la información necesaria para su gestión.  

- **Flujo principal:**  
1. El técnico accede a la vista de datos.  
2. El sistema recupera las solicitudes pendientes.  
3. El sistema muestra la información al técnico.  

- **Flujos alternativos:**  
- 2a. Error en la carga de datos → el sistema muestra un mensaje de error.  

- **Criterios de aceptación:**  
- Solo se muestran solicitudes no resueltas.  
- La información es accesible y está actualizada.  

### CA4 – Actualizar estado

- **Actor:** Técnico  

- **Descripción:**  
El técnico actualiza el estado de un formulario o solicitud en el sistema.

- **Precondiciones:**  
- Existe una solicitud o formulario pendiente.  

- **Postcondiciones:**  
- El estado queda actualizado correctamente o no se modifica si no procede.  

- **Flujo principal:**  
1. El técnico selecciona una solicitud o formulario.  
2. El técnico marca como resuelto.  
3. El sistema verifica si existe formulario asociado.  
4. El sistema actualiza el estado.  

- **Flujos alternativos:**  
- 3a. No existe formulario → el sistema no realiza cambios.  

- **Criterios de aceptación:**  
- El estado se actualiza correctamente si se cumplen las condiciones.  
- El sistema valida la existencia del formulario.  


### CA5 – Completar formulario

- **Actor:** Cliente  

- **Descripción:**  
El cliente completa un formulario para aportar información adicional a una solicitud.

- **Precondiciones:**  
- El sistema ha solicitado información adicional.  

- **Postcondiciones:**  
- La información adicional queda registrada en el sistema.  

- **Flujo principal:**  
1. El cliente accede al formulario.  
2. El cliente completa los campos requeridos.  
3. El cliente revisa la información.  
4. El cliente envía el formulario.  
5. El sistema registra la información.  

- **Flujos alternativos:**  
- 3a. El cliente detecta errores → modifica la información antes de enviarla.  

- **Criterios de aceptación:**  
- Los campos obligatorios están cumplimentados.  
- El formulario se registra correctamente.  