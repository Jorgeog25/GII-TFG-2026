| [<- PriorizarCdU](./4-PriorizarCdU.md) | [-> Requisitos](./1-Requisitos.md) |

# Detalle de casos de uso

En esta seccion se detallan los casos de uso del sistema manteniendo una descripcion homogenea de actor, precondiciones, flujo principal, errores, postcondiciones y diagrama asociado.

---

## 1. Iniciar sesion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite autenticar al usuario para acceder al sistema.
- **Precondiciones:** El usuario dispone de credenciales validas.
- **Flujo principal:** 1. Se muestra el formulario. 2. El actor introduce credenciales. 3. El sistema valida la informacion. 4. Se inicia la sesion.
- **Casos de Error:** Credenciales invalidas o usuario sin permisos.
- **Postcondiciones:** El usuario queda autenticado.
- **Diagrama:** ![Iniciar sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/IniciarSesion/IniciarSesion.svg)

---

## 2. Cerrar sesion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite finalizar la sesion activa.
- **Precondiciones:** Existe una sesion iniciada.
- **Flujo principal:** 1. El actor solicita cerrar sesion. 2. El sistema invalida la sesion. 3. Se redirige a la pantalla de acceso.
- **Casos de Error:** No existe sesion activa o se produce un fallo al cerrarla.
- **Postcondiciones:** La sesion queda cerrada.
- **Diagrama:** ![Cerrar sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CerrarSesion/CerrarSesion.svg)

---

## 3. Introducir documentacion funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite introducir documentacion funcional en formato texto libre, DRF, DDS o combinacion de ellos.
- **Precondiciones:** El actor ha iniciado sesion y se permite registrar documentacion.
- **Flujo principal:** 1. El actor aporta el contenido o archivo. 2. El sistema valida el formato recibido. 3. Se registra la documentacion en el contexto de trabajo.
- **Casos de Error:** No se proporciona documentacion, el formato no es soportado o falla el almacenamiento.
- **Postcondiciones:** La documentacion queda disponible para su consulta y procesamiento.
- **Diagrama:** ![Introducir documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/IntroducirDocumentacion/IntroducirDocumentacion.svg)

---

## 4. Asociar documentacion a proyecto
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite vincular documentacion registrada a un proyecto concreto.
- **Precondiciones:** Existe documentacion registrada y un proyecto de referencia.
- **Flujo principal:** 1. El actor selecciona la documentacion. 2. Indica el proyecto asociado. 3. El sistema valida y registra la asociacion.
- **Casos de Error:** Proyecto no encontrado o documentacion ya asociada.
- **Postcondiciones:** La documentacion queda vinculada a un proyecto.
- **Diagrama:** ![Asociar documentacion a proyecto](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/AsociarDocumentacionAProyecto/AsociarDocumentacionAProyecto.svg)

---

## 5. Consultar referencias de documentacion de proyecto
- **Actor:** Ingeniero de QA

- **Descripcion:** Permite consultar las referencias documentales asociadas a un proyecto.
- **Precondiciones:** Existe documentacion asociada a proyectos.
- **Flujo principal:** 1. El actor solicita la consulta. 2. El sistema recupera las referencias asociadas. 3. Se muestra la informacion disponible.
- **Casos de Error:** Proyecto no encontrado, sin documentacion asociada o fallo de recuperacion.
- **Postcondiciones:** El actor visualiza las referencias documentales del proyecto.
- **Diagrama:** ![Consultar referencias de documentacion de proyecto](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarReferenciasDocumentacionProyecto/ConsultarReferenciasDocumentacionProyecto.svg)

---

## 6. Listar documentacion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar la documentacion funcional registrada.
- **Precondiciones:** El actor ha iniciado sesion.
- **Flujo principal:** 1. El actor solicita el listado. 2. El sistema recupera los documentos disponibles. 3. Se muestra el listado.
- **Casos de Error:** No existe documentacion registrada o falla la consulta.
- **Postcondiciones:** El actor dispone de una vista general de la documentacion.
- **Diagrama:** ![Listar documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarDocumentacion/ListarDocumentacion.svg)

---

## 7. Consultar documentacion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un documento funcional concreto.
- **Precondiciones:** Existe documentacion registrada.
- **Flujo principal:** 1. El actor selecciona un documento. 2. El sistema recupera su contenido y metadatos. 3. Se muestra la informacion detallada.
- **Casos de Error:** Documento no encontrado o error de recuperacion.
- **Postcondiciones:** El actor visualiza el contenido del documento seleccionado.
- **Diagrama:** ![Consultar documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarDocumentacion/ConsultarDocumentacion.svg)

---

## 8. Actualizar documentacion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite modificar la informacion de un documento registrado.
- **Precondiciones:** Existe documentacion registrada y editable.
- **Flujo principal:** 1. El actor selecciona el documento. 2. Se muestra su informacion actual. 3. El actor modifica el contenido. 4. El sistema valida y guarda los cambios.
- **Casos de Error:** Documento no encontrado o cambios invalidos.
- **Postcondiciones:** La documentacion queda actualizada.
- **Diagrama:** ![Actualizar documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarDocumentacion/ActualizarDocumentacion.svg)

---

## 9. Eliminar documentacion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite retirar documentacion registrada que ya no debe permanecer activa.
- **Precondiciones:** Existe documentacion registrada.
- **Flujo principal:** 1. El actor selecciona el documento. 2. El sistema solicita confirmacion. 3. El actor confirma la eliminacion. 4. El sistema elimina el documento.
- **Casos de Error:** Documento inexistente o eliminacion no permitida.
- **Postcondiciones:** La documentacion deja de estar disponible.
- **Diagrama:** ![Eliminar documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarDocumentacion/EliminarDocumentacion.svg)

---

## 10. Listar casos de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar el conjunto de casos de uso disponibles.
- **Precondiciones:** Existen casos de uso registrados o consultables.
- **Flujo principal:** 1. El actor solicita el listado. 2. El sistema recupera los casos de uso. 3. Se muestra la informacion resultante.
- **Casos de Error:** No existen casos de uso o la consulta falla.
- **Postcondiciones:** El actor dispone de una vista general de los casos de uso.
- **Diagrama:** ![Listar casos de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarCdU/ListarCdU.svg)

---

## 11. Consultar caso de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un caso de uso concreto.
- **Precondiciones:** Existe al menos un caso de uso registrado.
- **Flujo principal:** 1. El actor selecciona un caso de uso. 2. El sistema recupera su detalle. 3. Se muestra la informacion.
- **Casos de Error:** Caso de uso inexistente o error de acceso.
- **Postcondiciones:** El actor visualiza el caso de uso seleccionado.
- **Diagrama:** ![Consultar caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarCdU/ConsultarCdU.svg)

---

## 12. Crear caso de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear manualmente un caso de uso local.
- **Precondiciones:** El actor dispone de permisos de edicion.
- **Flujo principal:** 1. Se muestra el formulario. 2. El actor introduce la informacion. 3. El sistema valida los datos. 4. Se registra el nuevo caso de uso.
- **Casos de Error:** Datos incompletos o error de validacion.
- **Postcondiciones:** Se crea un nuevo caso de uso.
- **Diagrama:** ![Crear caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearCdU/CrearCdU.svg)

---

## 13. Actualizar caso de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite modificar un caso de uso existente.
- **Precondiciones:** Existe un caso de uso registrado.
- **Flujo principal:** 1. El actor selecciona el caso de uso. 2. Se muestra su informacion actual. 3. El actor modifica el contenido. 4. El sistema valida y guarda los cambios.
- **Casos de Error:** Caso de uso no encontrado o cambios invalidos.
- **Postcondiciones:** El caso de uso queda actualizado.
- **Diagrama:** ![Actualizar caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarCdU/ActualizarCdU.svg)

---

## 14. Eliminar caso de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite eliminar un caso de uso local.
- **Precondiciones:** Existe un caso de uso registrado.
- **Flujo principal:** 1. El actor selecciona el caso de uso. 2. El sistema solicita confirmacion. 3. El actor confirma la operacion. 4. Se elimina el caso de uso.
- **Casos de Error:** Caso inexistente o eliminacion restringida.
- **Postcondiciones:** El caso de uso deja de estar disponible.
- **Diagrama:** ![Eliminar caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarCdU/EliminarCdU.svg)

---

## 15. Listar requisitos funcionales
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar el conjunto de requisitos funcionales disponibles.
- **Precondiciones:** Existen requisitos funcionales registrados o consultables.
- **Flujo principal:** 1. El actor solicita el listado. 2. El sistema recupera los requisitos. 3. Se muestran los resultados.
- **Casos de Error:** No existen requisitos o la consulta falla.
- **Postcondiciones:** El actor dispone de una vista general de los requisitos funcionales.
- **Diagrama:** ![Listar requisitos funcionales](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarRequisitosFuncionales/ListarRequisitosFuncionales.svg)

---

## 16. Consultar requisito funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un requisito funcional concreto.
- **Precondiciones:** Existe al menos un requisito funcional registrado.
- **Flujo principal:** 1. El actor selecciona el requisito. 2. El sistema recupera su detalle. 3. Se muestra la informacion.
- **Casos de Error:** Requisito no encontrado o error de acceso.
- **Postcondiciones:** El actor visualiza el requisito seleccionado.
- **Diagrama:** ![Consultar requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarRequisitosFuncionales/ConsultarRequisitosFuncionales.svg)

---

## 17. Crear requisito funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear manualmente un requisito funcional local.
- **Precondiciones:** El actor dispone de permisos de edicion.
- **Flujo principal:** 1. Se muestra el formulario. 2. El actor introduce el contenido. 3. El sistema valida la informacion. 4. Se registra el nuevo requisito.
- **Casos de Error:** Datos obligatorios ausentes o error de validacion.
- **Postcondiciones:** Se crea un nuevo requisito funcional.
- **Diagrama:** ![Crear requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearRequisitosFuncionales/CrearRequisitosFuncionales.svg)

---

## 18. Actualizar requisito funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite modificar un requisito funcional existente.
- **Precondiciones:** Existe un requisito funcional registrado.
- **Flujo principal:** 1. El actor selecciona el requisito. 2. Se muestra su informacion actual. 3. El actor modifica el contenido. 4. El sistema valida y guarda los cambios.
- **Casos de Error:** Requisito no encontrado o actualizacion invalida.
- **Postcondiciones:** El requisito funcional queda actualizado.
- **Diagrama:** ![Actualizar requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarRequisitoFuncional/ActualizarRequisitoFuncional.svg)

---

## 19. Eliminar requisito funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite eliminar un requisito funcional local.
- **Precondiciones:** Existe un requisito funcional registrado.
- **Flujo principal:** 1. El actor selecciona el requisito. 2. El sistema solicita confirmacion. 3. El actor confirma la operacion. 4. Se elimina el requisito.
- **Casos de Error:** Requisito inexistente o eliminacion restringida.
- **Postcondiciones:** El requisito funcional deja de estar disponible.
- **Diagrama:** ![Eliminar requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarRequisitoFuncional/EliminarRequisitoFuncional.svg)

---

## 20. Listar escenarios Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar los escenarios Gherkin registrados para el proyecto.
- **Precondiciones:** Existen escenarios registrados o consultables.
- **Flujo principal:** 1. El actor solicita el listado. 2. El sistema recupera los escenarios. 3. Se muestran los resultados.
- **Casos de Error:** No existen escenarios o la consulta falla.
- **Postcondiciones:** El actor dispone de una vista general de escenarios Gherkin.
- **Diagrama:** ![Listar escenarios Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarEscenariosGherkin/ListarEscenariosGherkin.svg)

---

## 21. Crear escenario Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear manualmente un escenario Gherkin asociado al proyecto.
- **Precondiciones:** Existen casos de uso o requisitos funcionales de referencia.
- **Flujo principal:** 1. Se muestra el formulario. 2. El actor introduce feature, titulo y pasos. 3. El sistema valida la informacion. 4. Se registra el escenario.
- **Casos de Error:** Datos incompletos o error de validacion.
- **Postcondiciones:** Se crea un nuevo escenario Gherkin.
- **Diagrama:** ![Crear escenario Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearEscenarioGherkin/CrearEscenarioGherkin.svg)

---

## 22. Consultar escenario Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un escenario Gherkin concreto.
- **Precondiciones:** Existe al menos un escenario Gherkin registrado.
- **Flujo principal:** 1. El actor selecciona un escenario. 2. El sistema recupera su detalle. 3. Se muestra la informacion.
- **Casos de Error:** Escenario no encontrado o error de acceso.
- **Postcondiciones:** El actor visualiza el escenario seleccionado.
- **Diagrama:** ![Consultar escenario Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarEscenarioGherkin/ConsultarEscenarioGherkin.svg)

---

## 23. Actualizar escenario Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite modificar un escenario Gherkin existente.
- **Precondiciones:** Existe un escenario Gherkin registrado.
- **Flujo principal:** 1. El actor selecciona el escenario. 2. Se muestra su informacion actual. 3. El actor modifica el contenido. 4. El sistema valida y guarda los cambios.
- **Casos de Error:** Escenario no encontrado o cambios invalidos.
- **Postcondiciones:** El escenario queda actualizado.
- **Diagrama:** ![Actualizar escenario Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarEscenarioGherkin/ActualizarEscenarioGherkin.svg)

---

## 24. Eliminar escenario Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite eliminar un escenario Gherkin local.
- **Precondiciones:** Existe un escenario registrado.
- **Flujo principal:** 1. El actor selecciona el escenario. 2. El sistema solicita confirmacion. 3. El actor confirma la operacion. 4. Se elimina el escenario.
- **Casos de Error:** Escenario inexistente o eliminacion restringida.
- **Postcondiciones:** El escenario deja de estar disponible.
- **Diagrama:** ![Eliminar escenario Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarEscenarioGherkin/EliminarEscenarioGherkin.svg)

---

## 25. Crear borrador de caso de prueba
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear un borrador de caso de prueba a partir de los artefactos disponibles.
- **Precondiciones:** Existen escenarios Gherkin o artefactos funcionales relacionados.
- **Flujo principal:** 1. El actor inicia la creacion del borrador. 2. El sistema recupera los artefactos relacionados. 3. Se construye el borrador inicial. 4. Se registra el borrador.
- **Casos de Error:** No existen artefactos base, error de generacion o error de almacenamiento.
- **Postcondiciones:** Se crea un borrador de caso de prueba.
- **Diagrama:** ![Crear borrador de caso de prueba](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearBorradorCasoPrueba/CrearBorradorCasoPrueba.svg)

---

## 26. Listar borradores
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar los borradores de caso de prueba disponibles.
- **Precondiciones:** Existen borradores registrados o consultables.
- **Flujo principal:** 1. El actor solicita el listado. 2. El sistema recupera los borradores. 3. Se muestra la informacion resultante.
- **Casos de Error:** No existen borradores o la consulta falla.
- **Postcondiciones:** El actor dispone de una vista general de borradores.
- **Diagrama:** ![Listar borradores](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarBorrador/ListarBorrador.svg)

---

## 27. Consultar borrador
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un borrador concreto.
- **Precondiciones:** Existe al menos un borrador registrado.
- **Flujo principal:** 1. El actor selecciona el borrador. 2. El sistema recupera su contenido. 3. Se muestra la informacion detallada.
- **Casos de Error:** Borrador inexistente o error de acceso.
- **Postcondiciones:** El actor visualiza el borrador seleccionado.
- **Diagrama:** ![Consultar borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarBorrador/ConsultarBorrador.svg)

---

## 28. Anadir feedback a borrador
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite incorporar observaciones o correcciones a un borrador.
- **Precondiciones:** Existe un borrador disponible para revision.
- **Flujo principal:** 1. El actor selecciona el borrador. 2. Introduce el feedback correspondiente. 3. El sistema registra las observaciones. 4. Se actualiza la version del borrador.
- **Casos de Error:** Borrador no encontrado, feedback vacio o error de regeneracion.
- **Postcondiciones:** El borrador queda enriquecido con feedback.
- **Diagrama:** ![Anadir feedback a borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/AñadirFeedbackBorrador/AñadirFeedbackBorrador.svg)

---

## 29. Rechazar borrador
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite rechazar un borrador cuando su contenido no resulta adecuado.
- **Precondiciones:** Existe un borrador disponible para revision.
- **Flujo principal:** 1. El actor selecciona el borrador. 2. Solicita su rechazo. 3. El sistema actualiza su estado.
- **Casos de Error:** Borrador no encontrado o estado no valido.
- **Postcondiciones:** El borrador queda marcado como rechazado.
- **Diagrama:** ![Rechazar borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/RechazarBorrador/RechazarBorrador.svg)

---

## 30. Aceptar y publicar caso de prueba a partir de borrador
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite aceptar un borrador valido y publicarlo como caso de prueba final en Kiwi TCMS.
- **Precondiciones:** Existe un borrador valido y la integracion con Kiwi TCMS esta disponible.
- **Flujo principal:** 1. El actor revisa el borrador. 2. Confirma su aceptacion. 3. El sistema transforma el borrador en caso de prueba final. 4. Se envia la informacion a Kiwi TCMS. 5. Se confirma la publicacion.
- **Casos de Error:** Borrador no encontrado, estado invalido, error de transformacion o fallo de integracion.
- **Postcondiciones:** El caso de prueba queda publicado en Kiwi TCMS.
- **Diagrama:** ![Aceptar y publicar caso de prueba a partir de borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/AceptarYPublicarCasoPruebaBorrador/AceptarYPublicarCasoPruebaBorrador.svg)

---

## 31. Buscar casos de prueba en Kiwi TCMS
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite buscar casos de prueba registrados en Kiwi TCMS.
- **Precondiciones:** La integracion con Kiwi TCMS esta disponible.
- **Flujo principal:** 1. El actor introduce criterios de busqueda. 2. Se envia la consulta a Kiwi TCMS. 3. Se recuperan los resultados. 4. Se muestran los casos encontrados.
- **Casos de Error:** Sin resultados o error de conexion.
- **Postcondiciones:** El actor obtiene resultados de busqueda en Kiwi TCMS.
- **Diagrama:** ![Buscar casos de prueba en Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/BuscarCasoPruebaKiwi/BuscarCasoPruebaKiwi.svg)

---

## 32. Ver caso de prueba en Kiwi TCMS
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un caso de prueba almacenado en Kiwi TCMS.
- **Precondiciones:** Existe al menos un caso de prueba accesible en Kiwi TCMS.
- **Flujo principal:** 1. El actor selecciona un caso de prueba. 2. Kiwi TCMS recupera su detalle. 3. Se muestra la informacion obtenida.
- **Casos de Error:** Caso no encontrado o error de consulta externa.
- **Postcondiciones:** El actor visualiza el detalle del caso de prueba.
- **Diagrama:** ![Ver caso de prueba en Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/VerCasoPruebaKiwi/VerCasoPruebaKiwi.svg)

---

## 33. Seleccionar sesiones
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite seleccionar una sesion de trabajo existente.
- **Precondiciones:** Existen sesiones registradas y accesibles para el actor.
- **Flujo principal:** 1. Se muestran las sesiones disponibles. 2. El actor selecciona una sesion. 3. El sistema recupera el contexto asociado. 4. Se activa la sesion.
- **Casos de Error:** Sesion no encontrada o error de recuperacion.
- **Postcondiciones:** La sesion seleccionada queda activa.
- **Diagrama:** ![Seleccionar sesiones](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/SeleccionarSesiones/SeleccionarSesiones.svg)

---

## 34. Crear nueva sesion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear una nueva sesion de trabajo.
- **Precondiciones:** El actor ha iniciado sesion.
- **Flujo principal:** 1. El actor solicita crear una sesion. 2. Introduce los datos basicos. 3. El sistema registra la nueva sesion. 4. La nueva sesion queda activa.
- **Casos de Error:** Datos incompletos o error de creacion.
- **Postcondiciones:** Se crea y activa una nueva sesion.
- **Diagrama:** ![Crear nueva sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearNuevaSesion/CrearNuevaSesion.svg)

---

## 35. Eliminar sesion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite eliminar una sesion de trabajo y sus artefactos asociados cuando ya no resulta necesaria.
- **Precondiciones:** Existe una sesion registrada y accesible.
- **Flujo principal:** 1. El actor selecciona la sesion. 2. El sistema solicita confirmacion. 3. El actor confirma la eliminacion. 4. El sistema elimina la sesion y sus artefactos asociados.
- **Casos de Error:** Sesion no encontrada o eliminacion no permitida.
- **Postcondiciones:** La sesion deja de estar disponible.
- **Diagrama:** ![Eliminar sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarSesion/EliminarSesion.svg)

---

## 36. Guardar resultados
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite guardar los resultados generados o editados durante la sesion actual.
- **Precondiciones:** Existe una sesion activa y artefactos pendientes de persistencia.
- **Flujo principal:** 1. El sistema identifica los artefactos modificados. 2. Se valida su consistencia. 3. Se persisten los resultados.
- **Casos de Error:** No existen cambios que guardar o falla la persistencia.
- **Postcondiciones:** Los resultados quedan guardados y asociados a la sesion.
- **Diagrama:** ![Guardar resultados](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/GuardarResultados/GuardarResultados.svg)

---

## 37. Registrar caso de prueba en Kiwi TCMS
- **Actor:** Kiwi TCMS
- **Descripcion:** Permite al sistema externo recibir y registrar un caso de prueba publicado desde la solucion.
- **Precondiciones:** Se ha enviado correctamente la informacion de un caso de prueba y Kiwi TCMS se encuentra operativo.
- **Flujo principal:** 1. Kiwi TCMS recibe la solicitud. 2. Valida la informacion. 3. Almacena el caso de prueba. 4. Devuelve la confirmacion del registro.
- **Casos de Error:** Datos invalidos o error de almacenamiento externo.
- **Postcondiciones:** El caso de prueba queda registrado en Kiwi TCMS.
- **Diagrama:** ![Registrar caso de prueba en Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/KiwiTCMS/RegistrarCasoPrueba/RegistrarCasoPrueba.svg)
