| [-> ActoresCdU](./3-ActoresCdU.md) | [<- Requisitos](./1-Requisitos.md) |

# Matriz de trazabilidad entre requisitos y casos de uso

## Casos de uso considerados

### Actor Ingeniero de QA

- CDU01. IniciarSesion
- CDU02. CerrarSesion
- CDU03. IntroducirDocumentacion
- CDU04. AsociarDocumentacionAProyecto
- CDU05. ConsultarReferenciasDocumentacionProyecto
- CDU06. ListarDocumentacion
- CDU07. ConsultarDocumentacion
- CDU08. ActualizarDocumentacion
- CDU09. EliminarDocumentacion
- CDU10. ListarCdU
- CDU11. ConsultarCdU
- CDU12. CrearCdU
- CDU13. ActualizarCdU
- CDU14. EliminarCdU
- CDU15. ListarRequisitosFuncionales
- CDU16. ConsultarRequisitoFuncional
- CDU17. CrearRequisitoFuncional
- CDU18. ActualizarRequisitoFuncional
- CDU19. EliminarRequisitoFuncional
- CDU20. ListarEscenariosGherkin
- CDU21. CrearEscenarioGherkin
- CDU22. ConsultarEscenarioGherkin
- CDU23. ActualizarEscenarioGherkin
- CDU24. EliminarEscenarioGherkin
- CDU25. CrearBorradorCasoPrueba
- CDU26. ListarBorradores
- CDU27. ConsultarBorrador
- CDU28. AnadirFeedbackBorrador
- CDU29. RechazarBorrador
- CDU30. AceptarYPublicarCasoPruebaDesdeBorrador
- CDU31. BuscarCasoPruebaKiwi
- CDU32. VerCasoPruebaKiwi
- CDU33. SeleccionarSesiones
- CDU34. CrearNuevaSesion
- CDU35. EliminarSesion
- CDU36. GuardarResultados

### Actor Kiwi TCMS

- CDU37. RegistrarCasoPruebaKiwi

## Matriz de requisitos funcionales

| Requisito | Titulo | Caso de uso |
|---|---|---|
| RF01 | Autenticacion | CDU01. IniciarSesion |
| RF01 | Autenticacion | CDU02. CerrarSesion |
| RF02 | Introduccion de documentacion funcional | CDU03. IntroducirDocumentacion |
| RF03 | Asociacion de documentacion a proyecto | CDU04. AsociarDocumentacionAProyecto |
| RF04 | Gestion de documentacion registrada | CDU05. ConsultarReferenciasDocumentacionProyecto |
| RF04 | Gestion de documentacion registrada | CDU06. ListarDocumentacion |
| RF04 | Gestion de documentacion registrada | CDU07. ConsultarDocumentacion |
| RF04 | Gestion de documentacion registrada | CDU08. ActualizarDocumentacion |
| RF04 | Gestion de documentacion registrada | CDU09. EliminarDocumentacion |
| RF05 | Gestion de casos de uso locales | CDU10. ListarCdU |
| RF05 | Gestion de casos de uso locales | CDU11. ConsultarCdU |
| RF05 | Gestion de casos de uso locales | CDU12. CrearCdU |
| RF05 | Gestion de casos de uso locales | CDU13. ActualizarCdU |
| RF05 | Gestion de casos de uso locales | CDU14. EliminarCdU |
| RF06 | Gestion de requisitos funcionales locales | CDU15. ListarRequisitosFuncionales |
| RF06 | Gestion de requisitos funcionales locales | CDU16. ConsultarRequisitoFuncional |
| RF06 | Gestion de requisitos funcionales locales | CDU17. CrearRequisitoFuncional |
| RF06 | Gestion de requisitos funcionales locales | CDU18. ActualizarRequisitoFuncional |
| RF06 | Gestion de requisitos funcionales locales | CDU19. EliminarRequisitoFuncional |
| RF07 | Gestion de escenarios Gherkin | CDU20. ListarEscenariosGherkin |
| RF07 | Gestion de escenarios Gherkin | CDU21. CrearEscenarioGherkin |
| RF07 | Gestion de escenarios Gherkin | CDU22. ConsultarEscenarioGherkin |
| RF07 | Gestion de escenarios Gherkin | CDU23. ActualizarEscenarioGherkin |
| RF07 | Gestion de escenarios Gherkin | CDU24. EliminarEscenarioGherkin |
| RF08 | Gestion de borradores | CDU25. CrearBorradorCasoPrueba |
| RF08 | Gestion de borradores | CDU26. ListarBorradores |
| RF08 | Gestion de borradores | CDU27. ConsultarBorrador |
| RF08 | Gestion de borradores | CDU28. AnadirFeedbackBorrador |
| RF08 | Gestion de borradores | CDU29. RechazarBorrador |
| RF09 | Aceptacion y publicacion de casos de prueba | CDU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RF09 | Aceptacion y publicacion de casos de prueba | CDU37. RegistrarCasoPruebaKiwi |
| RF10 | Consulta en Kiwi TCMS | CDU31. BuscarCasoPruebaKiwi |
| RF10 | Consulta en Kiwi TCMS | CDU32. VerCasoPruebaKiwi |
| RF11 | Gestion de sesiones de trabajo | CDU33. SeleccionarSesiones |
| RF11 | Gestion de sesiones de trabajo | CDU34. CrearNuevaSesion |
| RF11 | Gestion de sesiones de trabajo | CDU35. EliminarSesion |
| RF12 | Almacenamiento de resultados | CDU36. GuardarResultados |
| RF13 | Trazabilidad funcional | CDU03. IntroducirDocumentacion |
| RF13 | Trazabilidad funcional | CDU04. AsociarDocumentacionAProyecto |
| RF13 | Trazabilidad funcional | CDU05. ConsultarReferenciasDocumentacionProyecto |
| RF13 | Trazabilidad funcional | CDU07. ConsultarDocumentacion |
| RF13 | Trazabilidad funcional | CDU11. ConsultarCdU |
| RF13 | Trazabilidad funcional | CDU16. ConsultarRequisitoFuncional |
| RF13 | Trazabilidad funcional | CDU20. ListarEscenariosGherkin |
| RF13 | Trazabilidad funcional | CDU22. ConsultarEscenarioGherkin |
| RF13 | Trazabilidad funcional | CDU25. CrearBorradorCasoPrueba |
| RF13 | Trazabilidad funcional | CDU27. ConsultarBorrador |
| RF13 | Trazabilidad funcional | CDU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RF13 | Trazabilidad funcional | CDU31. BuscarCasoPruebaKiwi |
| RF13 | Trazabilidad funcional | CDU32. VerCasoPruebaKiwi |
| RF13 | Trazabilidad funcional | CDU33. SeleccionarSesiones |
| RF13 | Trazabilidad funcional | CDU34. CrearNuevaSesion |
| RF13 | Trazabilidad funcional | CDU36. GuardarResultados |

## Matriz de requisitos no funcionales

| Requisito | Titulo | Caso de uso |
|---|---|---|
| RNF01 | Consistencia | CDU08. ActualizarDocumentacion |
| RNF01 | Consistencia | CDU12. CrearCdU |
| RNF01 | Consistencia | CDU13. ActualizarCdU |
| RNF01 | Consistencia | CDU17. CrearRequisitoFuncional |
| RNF01 | Consistencia | CDU18. ActualizarRequisitoFuncional |
| RNF01 | Consistencia | CDU21. CrearEscenarioGherkin |
| RNF01 | Consistencia | CDU23. ActualizarEscenarioGherkin |
| RNF01 | Consistencia | CDU25. CrearBorradorCasoPrueba |
| RNF01 | Consistencia | CDU28. AnadirFeedbackBorrador |
| RNF01 | Consistencia | CDU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF02 | Trazabilidad | CDU03. IntroducirDocumentacion |
| RNF02 | Trazabilidad | CDU04. AsociarDocumentacionAProyecto |
| RNF02 | Trazabilidad | CDU05. ConsultarReferenciasDocumentacionProyecto |
| RNF02 | Trazabilidad | CDU07. ConsultarDocumentacion |
| RNF02 | Trazabilidad | CDU11. ConsultarCdU |
| RNF02 | Trazabilidad | CDU16. ConsultarRequisitoFuncional |
| RNF02 | Trazabilidad | CDU20. ListarEscenariosGherkin |
| RNF02 | Trazabilidad | CDU22. ConsultarEscenarioGherkin |
| RNF02 | Trazabilidad | CDU25. CrearBorradorCasoPrueba |
| RNF02 | Trazabilidad | CDU27. ConsultarBorrador |
| RNF02 | Trazabilidad | CDU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF02 | Trazabilidad | CDU31. BuscarCasoPruebaKiwi |
| RNF02 | Trazabilidad | CDU32. VerCasoPruebaKiwi |
| RNF02 | Trazabilidad | CDU33. SeleccionarSesiones |
| RNF02 | Trazabilidad | CDU34. CrearNuevaSesion |
| RNF02 | Trazabilidad | CDU36. GuardarResultados |
| RNF03 | Usabilidad | CDU01. IniciarSesion |
| RNF03 | Usabilidad | CDU02. CerrarSesion |
| RNF03 | Usabilidad | CDU03. IntroducirDocumentacion |
| RNF03 | Usabilidad | CDU06. ListarDocumentacion |
| RNF03 | Usabilidad | CDU07. ConsultarDocumentacion |
| RNF03 | Usabilidad | CDU10. ListarCdU |
| RNF03 | Usabilidad | CDU11. ConsultarCdU |
| RNF03 | Usabilidad | CDU15. ListarRequisitosFuncionales |
| RNF03 | Usabilidad | CDU16. ConsultarRequisitoFuncional |
| RNF03 | Usabilidad | CDU20. ListarEscenariosGherkin |
| RNF03 | Usabilidad | CDU22. ConsultarEscenarioGherkin |
| RNF03 | Usabilidad | CDU26. ListarBorradores |
| RNF03 | Usabilidad | CDU27. ConsultarBorrador |
| RNF03 | Usabilidad | CDU31. BuscarCasoPruebaKiwi |
| RNF03 | Usabilidad | CDU32. VerCasoPruebaKiwi |
| RNF03 | Usabilidad | CDU33. SeleccionarSesiones |
| RNF03 | Usabilidad | CDU34. CrearNuevaSesion |
| RNF03 | Usabilidad | CDU36. GuardarResultados |
| RNF04 | Integracion | CDU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF04 | Integracion | CDU31. BuscarCasoPruebaKiwi |
| RNF04 | Integracion | CDU32. VerCasoPruebaKiwi |
| RNF04 | Integracion | CDU37. RegistrarCasoPruebaKiwi |
| RNF05 | Mantenibilidad | CDU03. IntroducirDocumentacion |
| RNF05 | Mantenibilidad | CDU04. AsociarDocumentacionAProyecto |
| RNF05 | Mantenibilidad | CDU08. ActualizarDocumentacion |
| RNF05 | Mantenibilidad | CDU12. CrearCdU |
| RNF05 | Mantenibilidad | CDU17. CrearRequisitoFuncional |
| RNF05 | Mantenibilidad | CDU21. CrearEscenarioGherkin |
| RNF05 | Mantenibilidad | CDU25. CrearBorradorCasoPrueba |
| RNF05 | Mantenibilidad | CDU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF05 | Mantenibilidad | CDU36. GuardarResultados |
| RNF06 | Extensibilidad | CDU03. IntroducirDocumentacion |
| RNF06 | Extensibilidad | CDU04. AsociarDocumentacionAProyecto |
| RNF06 | Extensibilidad | CDU21. CrearEscenarioGherkin |
| RNF06 | Extensibilidad | CDU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF07 | Identificacion de contexto | CDU03. IntroducirDocumentacion |
| RNF07 | Identificacion de contexto | CDU04. AsociarDocumentacionAProyecto |
| RNF07 | Identificacion de contexto | CDU05. ConsultarReferenciasDocumentacionProyecto |
| RNF07 | Identificacion de contexto | CDU20. ListarEscenariosGherkin |
| RNF07 | Identificacion de contexto | CDU25. CrearBorradorCasoPrueba |
| RNF07 | Identificacion de contexto | CDU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF07 | Identificacion de contexto | CDU33. SeleccionarSesiones |
| RNF07 | Identificacion de contexto | CDU34. CrearNuevaSesion |
| RNF07 | Identificacion de contexto | CDU36. GuardarResultados |
