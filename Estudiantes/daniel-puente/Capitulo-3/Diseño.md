# Diseño

## Tecnologías Utilizadas

| Categoría | Tecnología | Propósito | Justificación |
|----------|------------|-----------|---------------|
| Frontend | React + Vite | Biblioteca e interfaz | Desarrollo de una SPA modular con tiempos de carga optimizados. |
| Frontend | TypeScript | Tipado estático | Garantía de robustez, mantenibilidad y reducción de errores en tiempo de compilación. |
| Frontend | Material UI (MUI) | Diseño de interfaz | Implementación de un sistema de componentes profesional y consistente. |
| Frontend | React Router | Gestión de rutas | Control de navegación y protección de vistas basada en roles de usuario. |
| Frontend | PWA (Service Workers + IndexedDB) | Soporte offline | Estrategia local-first para asegurar la operatividad ante fallos de red. |
| Backend | Node.js & Express | Entorno y framework | Ejecución de lógica de servidor bajo un modelo asíncrono y escalable. |
| Backend | Socket.io | Tiempo real | Comunicación bidireccional inmediata entre sala, cocina y caja. |
| Backend | JWT & bcrypt | Seguridad | Gestión de sesiones y cifrado de credenciales de acceso. |
| Persistencia | MongoDB Atlas | Base de datos | Almacenamiento documental flexible (NoSQL) con alta disponibilidad en la nube. |
| Persistencia | Mongoose | ODM | Modelado de datos con validaciones estrictas y gestión de referencias. |
| Herramientas | Axios | Cliente HTTP | Gestión centralizada de peticiones, interceptores de seguridad y manejo de errores. |
| Herramientas | Git & GitHub | Control de versiones | Gestión del código fuente, trazabilidad de cambios y respaldo en repositorio. |
| Herramientas | PlantUML | Documentación | Generación de diagramas. |
| Herramientas | Postman | Pruebas de API | Validación técnica de los endpoints, cabeceras y respuestas del servidor. |

## Diagrama General del Sistema

El sistema se divide en cuatro capas principales: frontend, backend, comunicación en tiempo real y soporte offline. La aplicación cliente se desarrolla como una PWA utilizando React con TypeScript; en cambio, el servidor se implementa con Express y Mongoose sobre MongoDB. La sincronización en tiempo real es gestionada mediante Socket.io y la persistencia offline se garantiza con Service Workers e IndexedDB.

![diagramaGeneral](/Estudiantes/daniel-puente/Capitulo-3/imagenes/diagramaGeneral.svg)

## Diagrama de Despliegue

![diagramaDespligue](/Estudiantes/daniel-puente/Capitulo-3/imagenes/diagramaDespligue.png)

## Diagrama Entidad-Relación

El diagrama entidad-relación representa la estructura lógica de los datos del sistema y la relación entre las principales entidades del restaurante. Permite visualizar cómo se conectan los documentos de MongoDB para dar soporte a los distintos flujos funcionales de la aplicación.

![diagramaEntidadRelacion](/Estudiantes/daniel-puente/Capitulo-3/imagenes/diagramaEntidadRelacion.png)

##  Modelos MongoDB

Para la gestión de los datos se ha utilizado Mongoose, que permite definir esquemas estructurados sobre MongoDB. Esta combinación ha sido elegida porque ofrece la flexibilidad de una base de datos documental, pero manteniendo la capacidad de establecer validaciones y relaciones entre documentos, garantizando así la integridad de la información.

| Modelo | Campos principales | Relaciones |
|--------|-------------------|------------|
| `Usuario` | `nombre`, `email`, `password`, `rol`, `activo`, `intentosFallidos`, `bloqueadoHasta` | Referenciado por `Comanda`, `Ticket` y `LogAuditoria` |
| `Zona` | `nombre`, `descripcion` | Relacionada con `Mesa` y `Reserva` |
| `Mesa` | `numero`, `estado`, `zona_id`, `comanda_activa_id` | Referencia a `Zona` y asociada activamente con `Comanda` durante el servicio |
| `Comanda` | `mesa_id`, `camarero_id`, `estado`, `creadaEn` | Compuesta por `LineaComanda` y referenciada a `Mesa` y `Usuario` |
| `LineaComanda` | `comanda_id`, `plato_id`, `cantidad`, `observaciones`, `alergenos`, `esMenu`, `tarifa_menu_id`, `estado` | Asociación condicionada a `TarifaMenu` por el campo `esMenu` y referencia a `Plato` y `Comanda` |
| `Plato` | `nombre`, `precio`, `categoria`, `disponible`, `disponibleEnMenu` | Referenciado por `LineaComanda` |
| `TarifaMenu` | `tipo`, `modalidad`, `precio`, `vigente` | Asociado a `LineaComanda` cuando pertenece al menú |
| `Ticket` | `comanda_id`, `total`, `estado`, `creadoEn`, `cobradoEn`, `cobradoPor` | Referencia a `Comanda` y `Usuario` |
| `Reserva` | `nombreCliente`, `telefono`, `comensales`, `fecha`, `mesa_id`, `zona_id`, `estado`, `creadaPor` | Referencia a `Mesa`, `Zona` y `Usuario` |
| `LogAuditoria` | `accion`, `usuario_id`, `entidadAfectada`, `entidadID`, `timestamp`, `detalles` | Referencia a `Usuario` |

### Ejemplo de esquema de Mongoose

```js
const mongoose = require('mongoose');

const usuarioSchema = new mongoose.Schema({
  nombre: String,
  email: String,
  password: String,
  rol: String,
  activo: Boolean,
  intentosFallidos: Number,
  bloqueadoHasta: Date
});

module.exports = mongoose.model('Usuario', usuarioSchema);
```

## Estructura del Proyecto

### Backend

```text
backend/
├── src/
│   ├── config/
│   ├── controllers/
│   ├── services/
│   ├── models/
│   ├── routes/
│   ├── middleware/
│   ├── sockets/
│   │   ├── handlers/
│   │   └── connection.ts
│   ├── utils/
│   ├── types/
│   ├── app.ts
│   └── index.ts
├── .env
└── package.json
```

### Frontend

```text
frontend/
├── public/
│   ├── icons/
│   ├── manifest.json
│   └── sw.js
├── src/
│   ├── assets/
│   │   └── styles/
│   ├── components/
│   │   ├── ui/
│   │   └── layout/
│   ├── views/
│   ├── router/
│   ├── context/
│   ├── hooks/
│   ├── services/
│   │   ├── api.ts
│   │   └── socket.ts
│   ├── types/
│   ├── utils/
│   ├── App.tsx
│   └── main.tsx
├── .env
└── package.json
```

## Wireframes
### Login
![login](/Estudiantes/daniel-puente/Capitulo-3/imagenes/login.png)
### Panel Administrador
![PanelAdministrador](/Estudiantes/daniel-puente/Capitulo-3/imagenes/panelAdmin.png)
### Gestion Reservas
![Gestion Reservas](/Estudiantes/daniel-puente/Capitulo-3/imagenes/gestionReservas.png)
### Vista Mesas
![Vista Mesas](/Estudiantes/daniel-puente/Capitulo-3/imagenes/vistaMesas.png)
### Pantalla KDS Cocina
![PantallaKDSCocina](/Estudiantes/daniel-puente/Capitulo-3/imagenes/pantallaKDSCocina.png)
### Vista Ticket Caja
![VistaTicketCaja](/Estudiantes/daniel-puente/Capitulo-3/imagenes/vistaTicketCaja.png)
### Tomar Comanda
![TomarComanda](/Estudiantes/daniel-puente/Capitulo-3/imagenes/tomarComanda.png)