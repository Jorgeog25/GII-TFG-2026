| [<- Matriz](./2-Matriz.md) | [-> PriorizarCdU](./4-PriorizarCdU.md) |

# Actores del sistema y casos de uso

## 1. Identificacion de actores

En el sistema se identifican dos actores principales:

![Actores](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/ActoresIndividuales/Actores.svg)

- **Ingeniero de QA (Quality Assurance)**: actor principal del sistema, responsable de iniciar y controlar el flujo de trabajo. A partir de la documentacion del proyecto, utiliza el sistema para introducir documentacion funcional, asociarla a proyectos, consultar y mantener la documentacion registrada, seleccionar, crear y eliminar sesiones de trabajo, crear casos de uso y requisitos funcionales, gestionar escenarios Gherkin, crear y revisar borradores, aceptar y publicar casos de prueba en Kiwi TCMS, y guardar los resultados obtenidos.
- **Kiwi TCMS**: sistema externo con el que se integra la solucion, encargado de recibir y registrar los casos de prueba publicados. Tambien proporciona funcionalidades de consulta para buscar y visualizar casos de prueba existentes.

---

## 2. Actor y casos de uso

### 2.1 Ingeniero de QA

#### Casos de uso del actor Ingeniero de QA

![Casos de uso QA](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/CdUPorActor/QA/QA.svg)

---

### 2.2 Kiwi TCMS

#### Casos de uso del actor Kiwi TCMS

![Casos de uso Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/CdUPorActor/KiwiTCMS/KiwiTCMS.svg)

---

## 3. Relacion entre actores y sistema

El flujo principal del sistema se articula a traves del actor Ingeniero de QA, que interactua con la solucion para gestionar sesiones de trabajo y transformar documentacion funcional en artefactos de prueba.

El sistema, a su vez, se integra con **Kiwi TCMS** para publicar, registrar y consultar los casos de prueba finales.

De esta forma:

- **Ingeniero de QA** actua como actor primario e iniciador del flujo.
- **Kiwi TCMS** actua como sistema externo de integracion, persistencia y consulta.
