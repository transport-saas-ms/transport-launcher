# 🚀 Transport SaaS - API Reference

## 🔗 Base URL
```
http://localhost:3000/api
```

## 🔐 Autenticación

### Registro
```http
POST /auth/register
Content-Type: application/json

{
  "name": "Juan Pérez",
  "email": "juan@empresa.com",
  "password": "password123",
  "role": "DRIVER", // ADMIN, ACCOUNTANT, DRIVER, USER
  "companyId": "uuid-empresa"
}

Response: 201 Created
{
  "token": "jwt-token",
  "user": {
    "id": "uuid",
    "name": "Juan Pérez",
    "email": "juan@empresa.com",
    "role": "DRIVER"
  }
}
```

### Login
```http
POST /auth/login
Content-Type: application/json

{
  "email": "juan@empresa.com",
  "password": "password123"
}

Response: 200 OK
{
  "token": "jwt-token",
  "user": { ... }
}
```

### Validar Token
```http
POST /auth/verify-token
Authorization: Bearer jwt-token

Response: 200 OK
{
  "user": { ... }
}
```

---

## 🚛 Gestión de Viajes

### Listar Viajes (con filtros y paginación)
```http
GET /trips?page=1&limit=10&status=PENDING&driverId=uuid&companyId=uuid
Authorization: Bearer jwt-token

Response: 200 OK
{
  "trips": [
    {
      "id": "uuid",
      "title": "Entrega Medicamentos",
      "origin": "Buenos Aires",
      "destination": "Córdoba",
      "status": "PENDING", // PENDING, IN_PROGRESS, COMPLETED, CANCELLED
      "scheduledDate": "2025-07-22T10:00:00Z",
      "driverId": "uuid",
      "companyId": "uuid",
      "driver": {
        "id": "uuid",
        "name": "Juan Pérez",
        "email": "juan@empresa.com"
      },
      "company": {
        "id": "uuid",
        "name": "Transportes ABC"
      }
    }
  ],
  "total": 50,
  "page": 1,
  "limit": 10,
  "totalPages": 5
}
```

### Crear Viaje
```http
POST /trips
Authorization: Bearer jwt-token
Content-Type: application/json

{
  "title": "Entrega Medicamentos Hospital",
  "description": "Entrega urgente de medicamentos",
  "origin": "Buenos Aires, Argentina",
  "destination": "Córdoba, Argentina", 
  "scheduledDate": "2025-07-22T10:00:00Z",
  "driverId": "uuid-conductor"
}

Response: 201 Created
{
  "id": "uuid",
  "title": "Entrega Medicamentos Hospital",
  ...
}
```

### Obtener Viaje por ID
```http
GET /trips/{id}
Authorization: Bearer jwt-token

Response: 200 OK
{
  "id": "uuid",
  "title": "Entrega Medicamentos",
  ...
}
```

### Actualizar Viaje
```http
PATCH /trips/{id}
Authorization: Bearer jwt-token
Content-Type: application/json

{
  "title": "Nuevo título",
  "description": "Nueva descripción"
}

Response: 200 OK
{
  "id": "uuid",
  "title": "Nuevo título",
  ...
}
```

### Eliminar Viaje (Soft Delete)
```http
DELETE /trips/{id}
Authorization: Bearer jwt-token

Response: 200 OK
{
  "message": "Trip deleted successfully"
}
```

---

## 🎬 Acciones de Viaje

### Iniciar Viaje
```http
POST /trips/{id}/start
Authorization: Bearer jwt-token
Content-Type: application/json

{
  "startedBy": "uuid-usuario",
  "notes": "Viaje iniciado desde depósito central"
}

Response: 200 OK
{
  "id": "uuid",
  "status": "IN_PROGRESS",
  "startedAt": "2025-07-22T08:00:00Z",
  ...
}
```

### Completar Viaje
```http
POST /trips/{id}/complete
Authorization: Bearer jwt-token
Content-Type: application/json

{
  "completedBy": "uuid-usuario",
  "notes": "Entrega realizada exitosamente"
}

Response: 200 OK
{
  "id": "uuid", 
  "status": "COMPLETED",
  "completedAt": "2025-07-22T14:00:00Z",
  ...
}
```

### Restaurar Viaje Eliminado
```http
PATCH /trips/{id}/restore
Authorization: Bearer jwt-token

Response: 200 OK
{
  "id": "uuid",
  "status": "PENDING",
  ...
}
```

---

## 🔍 Consultas Específicas

### Viajes por Conductor
```http
GET /trips/driver/{driverId}
Authorization: Bearer jwt-token

Response: 200 OK
[
  {
    "id": "uuid",
    "title": "Viaje 1",
    ...
  }
]
```

### Viajes por Empresa
```http
GET /trips/company/{companyId}
Authorization: Bearer jwt-token

Response: 200 OK
[...]
```

### Viajes por Estado
```http
GET /trips/status/{status}?companyId=uuid
Authorization: Bearer jwt-token

Response: 200 OK
[...]
```

---

## 💰 Viajes con Gastos (Endpoints especiales)

### Todos los Viajes con Gastos
```http
GET /trips/with-expenses?page=1&limit=10
Authorization: Bearer jwt-token

Response: 200 OK
{
  "trips": [
    {
      "id": "uuid",
      "title": "Viaje San Juan - Bs As", 
      "status": "COMPLETED",
      "expenses": [
        {
          "id": "uuid",
          "type": "FUEL",
          "amount": "2500.5",
          "currency": "ARS",
          "description": "Carga de combustible",
          "date": "2025-07-22T10:00:00Z",
          "status": "APPROVED"
        }
      ],
      "expensesByCurrency": [
        {
          "currency": "ARS",
          "total": 2500.5,
          "count": 1
        }
      ],
      "totalExpensesCount": 1
    }
  ],
  "total": 25,
  "page": 1,
  "limit": 10,
  "totalPages": 3
}
```

### Viaje Específico con Gastos
```http
GET /trips/{id}/with-expenses
Authorization: Bearer jwt-token

Response: 200 OK
{
  "id": "uuid",
  "title": "Viaje San Juan - Bs As",
  "expenses": [...],
  "expensesByCurrency": [...],
  "totalExpensesCount": 2
}
```

### Viajes con Gastos por Conductor
```http
GET /trips/driver/{driverId}/with-expenses
Authorization: Bearer jwt-token

Response: 200 OK
[
  {
    "id": "uuid",
    "expenses": [...],
    ...
  }
]
```

---

## 💸 Gestión de Gastos

### Listar Gastos
```http
GET /expenses?page=1&limit=10&tripId=uuid&status=PENDING
Authorization: Bearer jwt-token

Response: 200 OK
{
  "expenses": [
    {
      "id": "uuid",
      "tripId": "uuid",
      "type": "FUEL", // FUEL, FOOD, TOLLS, MAINTENANCE, OTHER
      "amount": "2500.50",
      "currency": "ARS",
      "description": "Carga de combustible Shell",
      "date": "2025-07-22T10:00:00Z",
      "receiptNumber": "SHELL-001-12345",
      "status": "PENDING", // PENDING, APPROVED, REJECTED
      "createdAt": "2025-07-22T10:05:00Z"
    }
  ],
  "total": 15,
  "page": 1,
  "limit": 10,
  "totalPages": 2
}
```

### Crear Gasto
```http
POST /expenses
Authorization: Bearer jwt-token
Content-Type: application/json

{
  "tripId": "uuid-viaje",
  "type": "FUEL",
  "amount": 2500.50,
  "currency": "ARS", 
  "description": "Carga de combustible Shell",
  "receiptNumber": "SHELL-001-12345"
}

Response: 201 Created
{
  "id": "uuid",
  "tripId": "uuid-viaje",
  "type": "FUEL",
  "amount": "2500.50",
  "currency": "ARS",
  "status": "PENDING",
  "createdAt": "2025-07-22T10:05:00Z"
}
```

### Obtener Gasto por ID
```http
GET /expenses/{id}
Authorization: Bearer jwt-token

Response: 200 OK
{
  "id": "uuid",
  "tripId": "uuid",
  "type": "FUEL",
  ...
}
```

### Actualizar Gasto
```http
PATCH /expenses/{id}
Authorization: Bearer jwt-token
Content-Type: application/json

{
  "amount": 2800.75,
  "description": "Carga de combustible Shell - actualizada"
}

Response: 200 OK
{
  "id": "uuid",
  "amount": "2800.75",
  ...
}
```

### Eliminar Gasto
```http
DELETE /expenses/{id}
Authorization: Bearer jwt-token

Response: 200 OK
{
  "message": "Expense deleted successfully"
}
```

### Gastos por Viaje
```http
GET /expenses/trip/{tripId}
Authorization: Bearer jwt-token

Response: 200 OK
{
  "expenses": [...],
  "count": 5,
  "message": "Expenses retrieved successfully"
}
```

---

## 👥 Gestión de Usuarios

### Listar Usuarios
```http
GET /users?page=1&limit=10&role=DRIVER
Authorization: Bearer jwt-token

Response: 200 OK
{
  "users": [
    {
      "id": "uuid",
      "name": "Juan Pérez",
      "email": "juan@empresa.com",
      "role": "DRIVER",
      "isActive": true,
      "createdAt": "2025-07-20T10:00:00Z"
    }
  ],
  "total": 8,
  "page": 1,
  "limit": 10,
  "totalPages": 1
}
```

### Obtener Usuario por ID
```http
GET /users/{id}
Authorization: Bearer jwt-token

Response: 200 OK
{
  "id": "uuid",
  "name": "Juan Pérez",
  "email": "juan@empresa.com",
  "role": "DRIVER",
  "isActive": true
}
```

---

## 🏢 Gestión de Empresas

### Listar Empresas
```http
GET /companies
Authorization: Bearer jwt-token

Response: 200 OK
[
  {
    "id": "uuid",
    "name": "Transportes ABC",
    "description": "Empresa de transporte de carga"
  }
]
```

---

## 🚨 Códigos de Error

| Código | Descripción |
|--------|-------------|
| 200 | OK |
| 201 | Created |
| 400 | Bad Request - Datos inválidos |
| 401 | Unauthorized - Token inválido o faltante |
| 403 | Forbidden - Sin permisos para esta acción |
| 404 | Not Found - Recurso no encontrado |
| 500 | Internal Server Error |

---

## 🔐 Roles y Permisos

| Rol | Permisos |
|-----|----------|
| **ADMIN** | Acceso total a todo el sistema |
| **ACCOUNTANT** | Gestión de gastos, reportes financieros |
| **DRIVER** | Ver sus propios viajes, crear gastos |
| **USER** | Vista limitada, solo lectura |

---

## 📝 Notas para Frontend

### Headers requeridos
```javascript
headers: {
  'Authorization': 'Bearer ' + localStorage.getItem('token'),
  'Content-Type': 'application/json'
}
```

### Estados de UI importantes
```javascript
// Estados de viaje
TRIP_STATUS = {
  PENDING: 'Pendiente',
  IN_PROGRESS: 'En progreso', 
  COMPLETED: 'Completado',
  CANCELLED: 'Cancelado'
}

// Estados de gasto
EXPENSE_STATUS = {
  PENDING: 'Pendiente aprobación',
  APPROVED: 'Aprobado',
  REJECTED: 'Rechazado'
}

// Tipos de gasto
EXPENSE_TYPES = {
  FUEL: 'Combustible',
  FOOD: 'Comida',
  TOLLS: 'Peajes', 
  MAINTENANCE: 'Mantenimiento',
  OTHER: 'Otro'
}
```

### Manejo de errores
```javascript
// Ejemplo de manejo de errores
const handleApiError = (error) => {
  if (error.status === 401) {
    // Token expirado, redirigir a login
    localStorage.removeItem('token');
    window.location.href = '/login';
  } else if (error.status === 403) {
    // Sin permisos
    showMessage('No tienes permisos para esta acción');
  } else if (error.status === 404) {
    showMessage('Recurso no encontrado');
  } else {
    showMessage('Error del servidor');
  }
};
```
