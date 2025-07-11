# Transport SaaS - Plataforma de Microservicios

## Descripción

Transport SaaS es una plataforma completa basada en microservicios para la gestión de empresas de transporte. El sistema permite administrar usuarios con diferentes roles, empresas, vehículos, rutas y más, todo mediante una arquitectura moderna y escalable.

## Arquitectura

La plataforma sigue una arquitectura de microservicios con un API Gateway como punto de entrada único. Los componentes principales son:

- **Client Gateway**: API Gateway que recibe todas las solicitudes del cliente y las enruta a los microservicios correspondientes.
- **Auth Microservice**: Gestiona la autenticación, autorización y sesiones de usuario.
- **Users Microservice**: Administra los usuarios, roles y asociaciones con empresas.
- **NATS**: Sistema de mensajería que facilita la comunicación entre microservicios.
- **Bases de datos PostgreSQL**: Cada microservicio tiene su propia base de datos independiente.

### Diagrama de la arquitectura

```
Cliente <-> Client Gateway <-> [NATS] <-> Microservicios (Auth, Users, etc.)
                                           |
                                           v
                                        Bases de datos
```

## Tecnologías utilizadas

- **Backend**: NestJS (Node.js + TypeScript)
- **Bases de datos**: PostgreSQL
- **ORM**: Prisma
- **Mensajería**: NATS
- **Contenedores**: Docker y Docker Compose
- **Autenticación**: JWT (JSON Web Tokens)

## Requisitos previos

- Docker y Docker Compose
- Node.js v18 o superior (para desarrollo local)
- npm o yarn

## Configuración inicial

1. Clonar el repositorio:
```bash
git clone https://github.com/tu-usuario/transport-saas-launcher.git
cd transport-saas-launcher
```

2. Crea un archivo `.env` en la raíz del proyecto con los mismos valores que tiene el archivo '.env.template'

## Ejecución

Para iniciar toda la plataforma por primera vez:

```bash
docker-compose up --build
```

Para iniciar toda la plataforma:

```bash
docker-compose up
```

Para iniciar servicios específicos:

```bash
docker-compose up client-gateway users-ms auth-ms
```

Para iniciar en modo detached (background):

```bash
docker-compose up -d
```

## Acceso a servicios

Una vez iniciados los servicios, puedes acceder a ellos en:

- **Client Gateway**: http://localhost:3000
- **Documentación API (Swagger)**: http://localhost:3000/api
- **Users Microservice**: http://localhost:3001
- **Auth Microservice**: http://localhost:3002

## Desarrollo

### Estructura del proyecto

```
transport-saas-launcher/
├── client-gateway/        # API Gateway
├── users-ms/              # Microservicio de usuarios
├── auth-ms/               # Microservicio de autenticación
├── docker-compose.yml     # Configuración de Docker Compose
└── .env                   # Variables de entorno
```

### Flujo de trabajo recomendado

1. Realizar cambios en los microservicios
2. Los cambios se reflejan automáticamente gracias a los volúmenes de Docker y nodemon
3. Probar los cambios a través del Client Gateway

## Manejo de errores

La plataforma implementa un sistema consistente de manejo de errores:

1. Los microservicios generan errores estructurados
2. Los errores se propagan a través de NATS
3. El Client Gateway transforma estos errores en respuestas HTTP apropiadas

## Autenticación y autorización

El sistema utiliza:

- JWT para autenticación
- Roles para autorización (ADMIN, ACCOUNTANT, DRIVER, USER)
- Guards en el Client Gateway para proteger endpoints

## Contribución

1. Crear una rama para tu funcionalidad: `git checkout -b feature/nueva-funcionalidad`
2. Hacer commit de tus cambios: `git commit -am 'Añadir nueva funcionalidad'`
3. Subir los cambios: `git push origin feature/nueva-funcionalidad`
4. Enviar un Pull Request

## Licencia

Este proyecto está licenciado bajo [MIT License](LICENSE).