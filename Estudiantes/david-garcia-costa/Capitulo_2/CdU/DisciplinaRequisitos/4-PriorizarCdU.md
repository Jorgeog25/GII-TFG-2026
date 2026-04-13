| [<- ActoresCdU](./3-ActoresCdU.md) | [-> DetallarCdU](./5-DetallarCdU.md) |

# Priorizacion de casos de uso por actor

## Tabla de prioridad

| ID | Caso de uso | Actor(es) | Prioridad |
|---|---|---|---|
| CU03 | Introducir documentacion funcional | Ingeniero de QA | Alta |
| CU06 | Extraer casos de uso | Ingeniero de QA | Alta |
| CU12 | Extraer requisitos funcionales | Ingeniero de QA | Alta |
| CU18 | Generar escenarios Gherkin | Ingeniero de QA | Alta |
| CU20 | Crear borrador de caso de prueba | Ingeniero de QA | Alta |
| CU23 | Anadir feedback a borrador | Ingeniero de QA | Alta |
| CU25 | Aceptar y publicar caso de prueba a partir de borrador | Ingeniero de QA | Alta |
| CU04 | Asociar documentacion a proyecto | Ingeniero de QA | Alta |
| CU09 | Crear caso de uso | Ingeniero de QA | Alta |
| CU10 | Actualizar caso de uso | Ingeniero de QA | Alta |
| CU15 | Crear requisito funcional | Ingeniero de QA | Alta |
| CU16 | Actualizar requisito funcional | Ingeniero de QA | Alta |
| CU01 | Iniciar sesion | Ingeniero de QA | Alta |
| CU31 | Registrar caso de prueba en Kiwi TCMS | Kiwi TCMS | Alta |
| CU28 | Seleccionar sesiones | Ingeniero de QA | Media |
| CU29 | Crear nueva sesion | Ingeniero de QA | Media |
| CU30 | Guardar resultados | Ingeniero de QA | Media |
| CU02 | Cerrar sesion | Ingeniero de QA | Media |
| CU05 | Consultar referencias de documentacion de proyecto | Ingeniero de QA | Media |
| CU07 | Listar casos de uso | Ingeniero de QA | Media |
| CU08 | Consultar caso de uso | Ingeniero de QA | Media |
| CU11 | Eliminar caso de uso | Ingeniero de QA | Media |
| CU13 | Listar requisitos funcionales | Ingeniero de QA | Media |
| CU14 | Consultar requisito funcional | Ingeniero de QA | Media |
| CU17 | Eliminar requisito funcional | Ingeniero de QA | Media |
| CU19 | Listar escenarios Gherkin | Ingeniero de QA | Media |
| CU21 | Listar borradores | Ingeniero de QA | Media |
| CU22 | Consultar borrador | Ingeniero de QA | Media |
| CU24 | Rechazar borrador | Ingeniero de QA | Media |
| CU26 | Buscar casos de prueba en Kiwi TCMS | Ingeniero de QA | Media |
| CU27 | Ver caso de prueba en Kiwi TCMS | Ingeniero de QA | Media |

---

## Justificacion por actor

### Ingeniero de QA

El actor `Ingeniero de QA` concentra el nucleo funcional del sistema. Se consideran especialmente representativos `CU03`, `CU06`, `CU12`, `CU18`, `CU20` y `CU25`, ya que reflejan el flujo principal: entrada de documentacion, extraccion de artefactos funcionales, generacion de escenarios verificables, elaboracion de borradores y aceptacion con publicacion final en la herramienta externa.

Dentro de los casos de uso de prioridad `Alta`, esos casos se sitan en primer lugar por ser los mas representativos del flujo principal. A continuacion aparecen los casos de uso de alta prioridad necesarios para completar, mantener o consolidar dicho flujo, incluyendo la gestion de documentacion y la edicion de artefactos funcionales.

Se consideran de prioridad `Media` los casos de uso orientados a consulta, listado, rechazo o soporte operativo, ya que aportan continuidad al proceso pero no desbloquean por si mismos la generacion principal de artefactos y resultados de prueba.

### Kiwi TCMS

El sistema `Kiwi TCMS` actua como sistema externo participante. Su caso de uso `Registrar caso de prueba en Kiwi TCMS` tiene prioridad `Alta` porque materializa la integracion y completa la publicacion final de los casos de prueba generados.
