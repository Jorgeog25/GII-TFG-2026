# Conclusiones

| ID | Requisito funcional | Implementación |
|---|---|---|
| RF1 | Integrarse con el buzón de correo corporativo | El sistema se integra con el buzón compartido `VARIACIONES_PLANTA_EXTERIOR` mediante Exchange Online y el conector **Office 365 Outlook** de Power Automate. |
| RF2 | Recibir solicitud | Power Automate detecta automáticamente los correos entrantes dirigidos al buzón compartido y activa el flujo de procesamiento. |
| RF3 | Procesar solicitud recibida | El sistema analiza el correo recibido, obtiene atributos como el asunto y, en el caso por defecto, utiliza un agente para extraer la intención y los números incluidos en el mensaje. |
| RF4 | Generar respuesta automática | En función de la intención detectada y de los datos obtenidos en las consultas, el sistema construye automáticamente una respuesta adaptada al caso correspondiente. |
| RF5 | Enviar respuesta | La respuesta generada se envía al remitente mediante el buzón compartido utilizando las acciones de correo de Power Automate. |
| RF6 | Incorporar sistema de ampliación de detalle | El sistema permite que el usuario amplíe la información mediante un formulario de Microsoft Forms, especialmente en casos de documentación, desacuerdo o reclamación. |
| RF7 | Integrarse con sistema de formularios | Power Automate se integra con Microsoft Forms mediante el trigger `When a new response is submitted` y la acción `Get response details`. |
| RF8 | Recibir detalle | El flujo recibe los datos introducidos en el formulario, como nombre, correo, tipo de peticionario, número de expediente, tipo de solicitud, explicación y documentación adicional. |
| RF9 | Registrar información recibida | La información procedente de correos, números consultados y formularios se almacena en el dataset semántico `GestionVariaciones` de Power BI, en las tablas `CorreoRecibido`, `Numero` y `Formulario`. |
| RF10 | Ver solicitudes | El técnico dispone de una vista en Power BI que permite consultar solicitudes, filtrar por intención, técnico, número y estado, y revisar los formularios pendientes o resueltos. |
| RF11 | Actualizar estado | El técnico puede actualizar el estado de una solicitud mediante el procedimiento definido, enviando el correo interno `TE:FORMRESUELTO`, que activa el flujo encargado de actualizar el formulario. |

