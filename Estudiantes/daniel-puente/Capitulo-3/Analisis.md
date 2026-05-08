# Análisis

La solución propuesta se ha diseñado bajo un enfoque arquitectónico MVC (Modelo - Vista - Controlador), lo que permite separar de forma clara la lógica de negocio, la representación de los datos y la interacción con el usuario. Esta organización mejora la mantenibilidad, facilita la escalabilidad del sistema y reduce el acoplamiento entre capas. El desarrollo del MVP se ha centrado en los casos de uso más relevantes para la operativa interna del Restaurante La Unión.

## Análisis Modelo-Vista-Controlador

### Modelos

![Modelos](/Estudiantes/daniel-puente/Capitulo-3/imagenes/modelos.svg)

Los modelos representan las entidades centrales del sistema y su estructura en la base de datos. Se han implementado con Mongoose sobre MongoDB, lo que permite definir esquemas flexibles y adaptados a la operativa real del restaurante. Entre los modelos principales se encuentran `Usuario`, `Zona`, `Mesa`, `Comanda`, `LineaComanda`, `Plato`, `TarifaMenu`, `Ticket`, `Reserva` y `LogAuditoria`.


| Modelo | Descripción |
|---------|-------------|
| `Usuario` | almacena las credenciales y el rol de acceso |
| `Zona` | identifica las áreas físicas del restaurante |
| `Mesa` | registra el estado de cada mesa y su relación con una zona |
| `Comanda` | agrupa el pedido activo asociado a una mesa |
| `LineaComanda` | representa cada plato solicitado con su estado |
| `Plato` | define la carta disponible |
| `TarifaMenu` | gestiona los precios del menú del día |
| `Ticket` | resume el importe del servicio |
| `Reserva` | modela la ocupación futura |
| `LogAuditoria` | conserva la trazabilidad de las acciones relevantes del sistema |

### Vistas

![Vistas](/Estudiantes/daniel-puente/Capitulo-3/imagenes/vistas.svg)

Las vistas constituyen la capa de presentación y se adaptan al rol autenticado en cada sesión. La aplicación se ha planteado como una PWA orientada a tablet, de modo que cada usuario visualiza únicamente las funciones correspondientes a sus permisos.

Existen vistas compartidas entre Camarero y Administrador, como el plano de mesas, la consulta y edición de comandas y el envío de tickets a caja. Por su parte, el Cocinero accede a la vista KDS, donde visualiza en tiempo real las comandas pendientes y en preparación. El Administrador dispone además de vistas exclusivas para reservas, usuarios, carta, menú del día, caja y auditoría.

### Controladores

![Controladores](/Estudiantes/daniel-puente/Capitulo-3/imagenes/controladores.svg)

Los controladores definen la lógica de negocio que conecta los modelos con las vistas. Cada uno actúa como punto de entrada para los distintos recursos de la API REST, aplicando validaciones, control de permisos y actualización de estado.

Los controladores principales son `AuthController`, `MesaController`, `ComandaController`, `TicketController`, `KDSController`, `ReservaController`, `UsuarioController` y `CartaController`.

| Controlador | Función |
|-------------|---------|
| `AuthController` | gestiona autenticación, emisión de JWT y bloqueo por intentos fallidos |
| `MesaController` | controla la apertura y cierre de mesas |
| `ComandaController` | gestiona la creación y edición de comandas |
| `TicketController` | centraliza el envío y cobro de tickets |
| `KDSController` | actualiza el estado de los platos en cocina |
| `ReservaController` | administra reservas |
| `UsuarioController` | gestiona el alta y mantenimiento de usuarios |
| `CartaController` | permite administrar la carta y el menú del día |

## Casos de Uso Seleccionados

### CU-04 Abrir mesa

![CU-04 Abrir mesa](/Estudiantes/daniel-puente/Capitulo-3/imagenes/abrirMesa.svg)

Permite al Camarero o al Administrador cambiar el estado de una mesa libre a ocupada cuando llegan nuevos clientes. Es el punto de partida del servicio en sala, ya que habilita la asociación de una comanda activa a esa mesa.

| Elemento | Descripción |
|----------|-------------|
| Modelos implicados | `Mesa`, `Zona`, `LogAuditoria` |
| Controlador | `MesaController` |
| Ruta | `PATCH /api/mesas/:mesaId/abrir` |
| Roles | Camarero, Administrador |
| Vista | `MesasView` |

### CU-06 Tomar comanda

![CU-06 Tomar comanda](/Estudiantes/daniel-puente/Capitulo-3/imagenes/tomarComanda.svg)

Permite registrar digitalmente los platos solicitados por una mesa ocupada, incluyendo cantidades, observaciones y alérgenos obligatorios. Sustituye la comanda en papel y garantiza la sincronización sala-cocina incluso ante fallos de red gracias al enfoque local-first.

| Elemento | Descripción |
|----------|-------------|
| Modelos implicados | `Mesa`, `Comanda`, `LineaComanda`, `Plato`, `TarifaMenu`, `LogAuditoria` |
| Controlador | `ComandaController`, `KDSController` |
| Rutas | `GET /api/platos`, `GET /api/tarifas-menu/actual`, `POST /api/comandas` |
| Roles | Camarero, Administrador |
| Vista | `ComandaView` |

### CU-08 Editar comanda

![CU-08 Editar comanda](/Estudiantes/daniel-puente/Capitulo-3/imagenes/editarComanda.svg)

Permite modificar una comanda activa mientras existan líneas que todavía no hayan entrado en preparación. El objetivo es adaptar el pedido a cambios de última hora sin alterar platos ya procesados en cocina.

| Elemento | Descripción |
|----------|-------------|
| Modelos implicados | `Comanda`, `LineaComanda`, `Mesa`, `Plato`, `LogAuditoria` |
| Controlador | `ComandaController` |
| Rutas | `GET /api/comandas/mesa/:mesaId/activa`, `PATCH /api/comandas/:comandaId` |
| Vista | `ComandaView` |

### CU-12 Marcar plato como listo

![CU-12 Marcar plato como listo](/Estudiantes/daniel-puente/Capitulo-3/imagenes/platoListo.svg)

Permite al Cocinero indicar que un plato ya está finalizado y listo para servirse. Este cambio de estado activa la notificación en tiempo real al camarero responsable de la mesa.

| Elemento | Descripción |
|----------|-------------|
| Modelos implicados | `Comanda`, `LineaComanda`, `Mesa`, `LogAuditoria` |
| Controlador | `KDSController` |
| Rutas | `GET /api/kds/comandas`, `PATCH /api/lineas-comanda/:lineaId/listo` |
| Rol | Cocinero |
| Vista | `KDSView` |

### CU-14 Enviar ticket a caja

![CU-14 Enviar ticket a caja](/Estudiantes/daniel-puente/Capitulo-3/imagenes/ticketCaja.svg)

Permite generar el ticket de una mesa a partir de su comanda activa y dejarlo preparado para su cobro posterior. Este flujo marca la transición entre servicio en sala y facturación.

| Elemento | Descripción |
|----------|-------------|
| Modelos implicados | `Mesa`, `Comanda`, `LineaComanda`, `Ticket`, `Zona`, `LogAuditoria` |
| Controlador | `TicketController` |
| Rutas | `GET /api/comandas/mesa/:mesaId/activa`, `POST /api/tickets` |
| Vista | `ComandaView` o vista resumida de mesa |

### CU-17 Cobrar ticket en caja

![CU-17 Cobrar ticket en caja](/Estudiantes/daniel-puente/Capitulo-3/imagenes/cobrarTicket.svg)

Permite al Administrador confirmar el cobro de un ticket previamente enviado a caja. Con esta acción se cierra la operación económica y se deja lista la mesa para su posterior liberación.

| Elemento | Descripción |
|----------|-------------|
| Modelos implicados | `Ticket`, `Mesa`, `Usuario`, `LogAuditoria` |
| Controlador | `TicketController`, `MesaController` |
| Rutas | `GET /api/caja/tickets-pendientes`, `PATCH /api/tickets/:ticketId/cobrar` |
| Rol | Administrador |
| Vista | `CajaView` |

### CU-05 Cerrar mesa

![CU-05 Cerrar mesa](/Estudiantes/daniel-puente/Capitulo-3/imagenes/cerrarMesa.svg)

Permite liberar una mesa ocupada una vez que el ticket asociado ha sido cobrado correctamente. Es el último paso del flujo operativo de atención a una mesa.

| Elemento | Descripción |
|----------|-------------|
| Modelos implicados | `Mesa`, `Ticket`, `Zona`, `LogAuditoria` |
| Controlador | `MesaController` |
| Ruta | `PATCH /api/mesas/:mesaId/cerrar` |
| Rol | Administrador |
| Vista | `CajaView`, `MesasView` |

## Analisis de Clases



## Analisis de Paquetes

![Diagrama de Paquetes](/Estudiantes/daniel-puente/Capitulo-3/imagenes/diagramaPaquetes.png)

El sistema se ha diseñado bajo un enfoque modular que separa claramente el cliente (Frontend) del servidor (Backend). Esta organización permite un bajo acoplamiento, asegurando que los cambios en la interfaz no afecten a la lógica de negocio ni a la base de datos, y viceversa.

### Estructura del Backend

El servidor organiza sus paquetes siguiendo el flujo unidireccional de una petición HTTP.

| Paquete | Función |
|---------|---------|
| `Routes` | Recibe la petición entrante y la deriva al controlador correspondiente. |
| `Middleware` | Capa transversal que verifica el token JWT y el rol del usuario. |
| `Controllers` | Gestionan la respuesta HTTP y delegan la lógica de negocio. |
| `Services` | Implementan reglas funcionales, coordinan modelos y emiten eventos. |
| `Models` | Encapsulan acceso a datos mediante esquemas Mongoose. |
| `Sockets` | Gestionan la comunicación en tiempo real. |
| `Utils / Types` | Proveen funciones auxiliares y tipos compartidos. |

### Estructura del Frontend

La aplicación cliente organiza sus paquetes para optimizar el ciclo de vida de React y la experiencia PWA.

| Paquete | Función |
|---------|---------|
| `Views` | Capa de presentación que consume datos de servicios y componentes. |
| `Services` | Centralizan comunicación: Axios (REST) y Socket.io (tiempo real). |
| `Context / Hooks` | Gestionan estado global de autenticación y lógica reutilizable. |
| `Utils / Types` | Proveen formateadores y tipos TypeScript compartidos. |
| `Components` | Elementos de interfaz reutilizables. |