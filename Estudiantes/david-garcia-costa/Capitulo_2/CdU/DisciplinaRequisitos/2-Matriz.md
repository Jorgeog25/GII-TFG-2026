| [-> ActoresCdU](./3-ActoresCdU.md) | [<- Requisitos](./1-Requisitos.md) |

# Matriz de trazabilidad entre requisitos y casos de uso

## Casos de uso considerados

### Actor Ingeniero de QA

- CDU01. IniciarSesion
- CDU02. CerrarSesion
- CDU03. IntroducirDocumentacion
- CDU04. AsociarDocumentacionAProyecto
- CDU05. ConsultarReferenciasDocumentacionProyecto
- CDU06. ExtraerCdU
- CDU07. ListarCdU
- CDU08. ConsultarCdU
- CDU09. CrearCdU
- CDU10. ActualizarCdU
- CDU11. EliminarCdU
- CDU12. ExtraerRequisitosFuncionales
- CDU13. ListarRequisitosFuncionales
- CDU14. ConsultarRequisitoFuncional
- CDU15. CrearRequisitoFuncional
- CDU16. ActualizarRequisitoFuncional
- CDU17. EliminarRequisitoFuncional
- CDU18. GenerarEscenariosGherkin
- CDU19. ListarEscenariosGherkin
- CDU20. CrearBorradorCasoPrueba
- CDU21. ListarBorradores
- CDU22. ConsultarBorrador
- CDU23. AnadirFeedbackBorrador
- CDU24. RechazarBorrador
- CDU25. AceptarYPublicarCasoPruebaDesdeBorrador
- CDU26. BuscarCasoPruebaKiwi
- CDU27. VerCasoPruebaKiwi
- CDU28. SeleccionarSesiones
- CDU29. CrearNuevaSesion
- CDU30. GuardarResultados

### Actor Kiwi TCMS

- CDU31. RegistrarCasoPruebaKiwi

## Matriz de requisitos funcionales

| Requisito | Titulo | Caso de uso |
|---|---|---|
| RF01 | Autenticacion | CDU01. IniciarSesion |
| RF01 | Autenticacion | CDU02. CerrarSesion |
| RF02 | Introduccion de documentacion funcional | CDU03. IntroducirDocumentacion |
| RF03 | Asociacion de documentacion a proyecto | CDU04. AsociarDocumentacionAProyecto |
| RF04 | Consulta de documentacion registrada | CDU05. ConsultarReferenciasDocumentacionProyecto |
| RF05 | Gestion de casos de uso locales | CDU06. ExtraerCdU |
| RF05 | Gestion de casos de uso locales | CDU07. ListarCdU |
| RF05 | Gestion de casos de uso locales | CDU08. ConsultarCdU |
| RF05 | Gestion de casos de uso locales | CDU09. CrearCdU |
| RF05 | Gestion de casos de uso locales | CDU10. ActualizarCdU |
| RF05 | Gestion de casos de uso locales | CDU11. EliminarCdU |
| RF06 | Gestion de requisitos funcionales locales | CDU12. ExtraerRequisitosFuncionales |
| RF06 | Gestion de requisitos funcionales locales | CDU13. ListarRequisitosFuncionales |
| RF06 | Gestion de requisitos funcionales locales | CDU14. ConsultarRequisitoFuncional |
| RF06 | Gestion de requisitos funcionales locales | CDU15. CrearRequisitoFuncional |
| RF06 | Gestion de requisitos funcionales locales | CDU16. ActualizarRequisitoFuncional |
| RF06 | Gestion de requisitos funcionales locales | CDU17. EliminarRequisitoFuncional |
| RF07 | Generacion de escenarios Gherkin | CDU18. GenerarEscenariosGherkin |
| RF07 | Generacion de escenarios Gherkin | CDU19. ListarEscenariosGherkin |
| RF08 | Gestion de borradores | CDU20. CrearBorradorCasoPrueba |
| RF08 | Gestion de borradores | CDU21. ListarBorradores |
| RF08 | Gestion de borradores | CDU22. ConsultarBorrador |
| RF08 | Gestion de borradores | CDU23. AnadirFeedbackBorrador |
| RF08 | Gestion de borradores | CDU24. RechazarBorrador |
| RF09 | Aceptacion y publicacion de casos de prueba | CDU25. AceptarYPublicarCasoPruebaDesdeBorrador |
| RF09 | Aceptacion y publicacion de casos de prueba | CDU31. RegistrarCasoPruebaKiwi |
| RF10 | Consulta en Kiwi TCMS | CDU26. BuscarCasoPruebaKiwi |
| RF10 | Consulta en Kiwi TCMS | CDU27. VerCasoPruebaKiwi |
| RF11 | Gestion de sesiones de trabajo | CDU28. SeleccionarSesiones |
| RF11 | Gestion de sesiones de trabajo | CDU29. CrearNuevaSesion |
| RF12 | Almacenamiento de resultados | CDU30. GuardarResultados |
| RF13 | Trazabilidad funcional | CDU04. AsociarDocumentacionAProyecto |
| RF13 | Trazabilidad funcional | CDU05. ConsultarReferenciasDocumentacionProyecto |
| RF13 | Trazabilidad funcional | CDU06. ExtraerCdU |
| RF13 | Trazabilidad funcional | CDU08. ConsultarCdU |
| RF13 | Trazabilidad funcional | CDU12. ExtraerRequisitosFuncionales |
| RF13 | Trazabilidad funcional | CDU14. ConsultarRequisitoFuncional |
| RF13 | Trazabilidad funcional | CDU18. GenerarEscenariosGherkin |
| RF13 | Trazabilidad funcional | CDU20. CrearBorradorCasoPrueba |
| RF13 | Trazabilidad funcional | CDU22. ConsultarBorrador |
| RF13 | Trazabilidad funcional | CDU25. AceptarYPublicarCasoPruebaDesdeBorrador |
| RF13 | Trazabilidad funcional | CDU26. BuscarCasoPruebaKiwi |
| RF13 | Trazabilidad funcional | CDU27. VerCasoPruebaKiwi |
| RF13 | Trazabilidad funcional | CDU28. SeleccionarSesiones |
| RF13 | Trazabilidad funcional | CDU29. CrearNuevaSesion |
| RF13 | Trazabilidad funcional | CDU30. GuardarResultados |

## Matriz de requisitos no funcionales

| Requisito | Titulo | Caso de uso |
|---|---|---|
| RNF01 | Consistencia | CDU06. ExtraerCdU |
| RNF01 | Consistencia | CDU09. CrearCdU |
| RNF01 | Consistencia | CDU10. ActualizarCdU |
| RNF01 | Consistencia | CDU12. ExtraerRequisitosFuncionales |
| RNF01 | Consistencia | CDU15. CrearRequisitoFuncional |
| RNF01 | Consistencia | CDU16. ActualizarRequisitoFuncional |
| RNF01 | Consistencia | CDU18. GenerarEscenariosGherkin |
| RNF01 | Consistencia | CDU20. CrearBorradorCasoPrueba |
| RNF01 | Consistencia | CDU23. AnadirFeedbackBorrador |
| RNF01 | Consistencia | CDU25. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF02 | Trazabilidad | CDU04. AsociarDocumentacionAProyecto |
| RNF02 | Trazabilidad | CDU05. ConsultarReferenciasDocumentacionProyecto |
| RNF02 | Trazabilidad | CDU06. ExtraerCdU |
| RNF02 | Trazabilidad | CDU08. ConsultarCdU |
| RNF02 | Trazabilidad | CDU12. ExtraerRequisitosFuncionales |
| RNF02 | Trazabilidad | CDU14. ConsultarRequisitoFuncional |
| RNF02 | Trazabilidad | CDU18. GenerarEscenariosGherkin |
| RNF02 | Trazabilidad | CDU20. CrearBorradorCasoPrueba |
| RNF02 | Trazabilidad | CDU22. ConsultarBorrador |
| RNF02 | Trazabilidad | CDU25. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF02 | Trazabilidad | CDU26. BuscarCasoPruebaKiwi |
| RNF02 | Trazabilidad | CDU27. VerCasoPruebaKiwi |
| RNF02 | Trazabilidad | CDU28. SeleccionarSesiones |
| RNF02 | Trazabilidad | CDU29. CrearNuevaSesion |
| RNF02 | Trazabilidad | CDU30. GuardarResultados |
| RNF03 | Usabilidad | CDU01. IniciarSesion |
| RNF03 | Usabilidad | CDU02. CerrarSesion |
| RNF03 | Usabilidad | CDU03. IntroducirDocumentacion |
| RNF03 | Usabilidad | CDU04. AsociarDocumentacionAProyecto |
| RNF03 | Usabilidad | CDU05. ConsultarReferenciasDocumentacionProyecto |
| RNF03 | Usabilidad | CDU07. ListarCdU |
| RNF03 | Usabilidad | CDU08. ConsultarCdU |
| RNF03 | Usabilidad | CDU13. ListarRequisitosFuncionales |
| RNF03 | Usabilidad | CDU14. ConsultarRequisitoFuncional |
| RNF03 | Usabilidad | CDU19. ListarEscenariosGherkin |
| RNF03 | Usabilidad | CDU21. ListarBorradores |
| RNF03 | Usabilidad | CDU22. ConsultarBorrador |
| RNF03 | Usabilidad | CDU26. BuscarCasoPruebaKiwi |
| RNF03 | Usabilidad | CDU27. VerCasoPruebaKiwi |
| RNF03 | Usabilidad | CDU28. SeleccionarSesiones |
| RNF03 | Usabilidad | CDU29. CrearNuevaSesion |
| RNF03 | Usabilidad | CDU30. GuardarResultados |
| RNF04 | Integracion | CDU25. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF04 | Integracion | CDU26. BuscarCasoPruebaKiwi |
| RNF04 | Integracion | CDU27. VerCasoPruebaKiwi |
| RNF04 | Integracion | CDU31. RegistrarCasoPruebaKiwi |
| RNF05 | Mantenibilidad | CDU03. IntroducirDocumentacion |
| RNF05 | Mantenibilidad | CDU04. AsociarDocumentacionAProyecto |
| RNF05 | Mantenibilidad | CDU06. ExtraerCdU |
| RNF05 | Mantenibilidad | CDU09. CrearCdU |
| RNF05 | Mantenibilidad | CDU12. ExtraerRequisitosFuncionales |
| RNF05 | Mantenibilidad | CDU15. CrearRequisitoFuncional |
| RNF05 | Mantenibilidad | CDU18. GenerarEscenariosGherkin |
| RNF05 | Mantenibilidad | CDU20. CrearBorradorCasoPrueba |
| RNF05 | Mantenibilidad | CDU25. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF05 | Mantenibilidad | CDU30. GuardarResultados |
| RNF06 | Extensibilidad | CDU03. IntroducirDocumentacion |
| RNF06 | Extensibilidad | CDU04. AsociarDocumentacionAProyecto |
| RNF06 | Extensibilidad | CDU06. ExtraerCdU |
| RNF06 | Extensibilidad | CDU12. ExtraerRequisitosFuncionales |
| RNF06 | Extensibilidad | CDU18. GenerarEscenariosGherkin |
| RNF06 | Extensibilidad | CDU25. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF07 | Identificacion de contexto | CDU04. AsociarDocumentacionAProyecto |
| RNF07 | Identificacion de contexto | CDU05. ConsultarReferenciasDocumentacionProyecto |
| RNF07 | Identificacion de contexto | CDU06. ExtraerCdU |
| RNF07 | Identificacion de contexto | CDU12. ExtraerRequisitosFuncionales |
| RNF07 | Identificacion de contexto | CDU18. GenerarEscenariosGherkin |
| RNF07 | Identificacion de contexto | CDU20. CrearBorradorCasoPrueba |
| RNF07 | Identificacion de contexto | CDU25. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF07 | Identificacion de contexto | CDU28. SeleccionarSesiones |
| RNF07 | Identificacion de contexto | CDU29. CrearNuevaSesion |
| RNF07 | Identificacion de contexto | CDU30. GuardarResultados |
