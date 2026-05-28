# Arquitectura del Proyecto — Booqi

## Mapa Conceptual General

```mermaid
flowchart TD
    USER(["👤 Usuario"])
    FRONT["⚛️ React Frontend\n(Vite · Puerto 80)"]
    US["🟢 User Service\n:8080"]
    ES["🟢 Event Service\n:8081"]
    BS["🟢 Booking Service\n:8082"]
    PS["🟢 Payment Service\n:8083"]
    DB[("🐬 MySQL 8.0\n:3307")]
    DOCKER["🐳 Docker Compose\nbooking-network"]

    USER -->|HTTP| FRONT
    FRONT -->|REST| US
    FRONT -->|REST| ES
    FRONT -->|REST| BS
    FRONT -->|REST| PS

    BS -->|Feign/RestTemplate| US
    BS -->|Feign/RestTemplate| ES
    PS -->|Feign/RestTemplate| BS
    PS -->|Feign/RestTemplate| ES

    US --- DB
    ES --- DB
    BS --- DB
    PS --- DB

    DOCKER -.->|orquesta| US
    DOCKER -.->|orquesta| ES
    DOCKER -.->|orquesta| BS
    DOCKER -.->|orquesta| PS
    DOCKER -.->|orquesta| FRONT
    DOCKER -.->|orquesta| DB
```

> Cada microservicio posee su propia base de datos dentro de MySQL (patrón **DB per Service**).

---

## Estructura de Capas por Microservicio

Todos los servicios backend siguen la misma arquitectura en capas:

```mermaid
flowchart LR
    C["Controller\n(REST API)"]
    S["Service\n(Lógica de negocio)"]
    R["Repository\n(Interfaz)"]
    JPA["JPA Repository\n(Spring Data)"]
    DB[("MySQL\nDB propia")]

    C --> S --> R --> JPA --> DB
```

| Capa | Clase representativa | Responsabilidad |
|---|---|---|
| **Controller** | `*Controller.java` | Recibir peticiones HTTP, validar entrada |
| **Service (interfaz)** | `*Service.java` | Contrato del dominio |
| **Service (impl)** | `*ServiceImpl.java` | Lógica de negocio |
| **DTO** | `*CreateDTO / *ResponseDTO` | Transferencia de datos entre capas |
| **Mapper** | `*Mapper.java` (MapStruct) | Conversión DTO ↔ Modelo |
| **Repository (interfaz)** | `*Repository.java` | Contrato de persistencia |
| **Repository JPA** | `*RepositoryJPA.java` | Implementación con Spring Data JPA |
| **JPA Model** | `JPA*Model.java` | Entidad JPA (`@Entity`) |
| **Config** | `CorsConfig / OpenAPIConfig` | CORS, Swagger, RestTemplate |

---

## Comunicación entre Servicios

```mermaid
flowchart TD
    BS["Booking Service"]
    PS["Payment Service"]
    US["User Service"]
    ES["Event Service"]

    BS -->|"GET /api/users/{id}"| US
    BS -->|"GET /api/events/{id}"| ES
    PS -->|"GET /api/bookings/{id}"| BS
    PS -->|"GET /api/events/{id}"| ES
```

Los clientes HTTP se implementan con `RestTemplate` en las clases `*ServiceClient.java`.

---

## Frontend — Estructura React

```
front/src/
├── api/            # Llamadas REST a cada microservicio
├── components/     # Componentes reutilizables
├── context/        # Estado global (React Context)
├── pages/          # Vistas principales
├── services/       # Lógica de servicios del lado cliente
└── utils/          # Utilidades y helpers
```

Servido con **Nginx** dentro de Docker (puerto `80`).

---

## Despliegue con Docker Compose

```mermaid
flowchart TD
    subgraph booking-network
        MYSQL[("mysql\n:3307")]
        US["user-service\n:8080"]
        ES["event-service\n:8081"]
        BS["booking-service\n:8082"]
        PS["payment-service\n:8083"]
        FE["front-service\n:80"]
    end

    MYSQL -->|healthcheck OK| US
    MYSQL -->|healthcheck OK| ES
    MYSQL -->|healthcheck OK| BS
    MYSQL -->|healthcheck OK| PS
    US & ES --> BS
    BS & ES --> PS
    US & ES & BS & PS --> FE
```

| Contenedor | Imagen / Build | Puerto |
|---|---|---|
| `mysql` | `mysql:8.0` | `3307:3306` |
| `user-service` | Build `../user` | `8080:8080` |
| `event-service` | Build `../event` | `8081:8081` |
| `booking-service` | Build `../booking` | `8082:8082` |
| `payment-service` | Build `../payment` | `8083:8083` |
| `front-service` | Build `../front` | `80:80` |

---

## Stack Tecnológico

| Capa | Tecnología |
|---|---|
| Frontend | React 18 · Vite · Nginx |
| Backend | Java 21 · Spring Boot · Spring Data JPA |
| Mapeo | MapStruct |
| Documentación API | SpringDoc OpenAPI (Swagger UI) |
| Base de datos | MySQL 8.0 |
| Contenedores | Docker · Docker Compose |
| Testing | JUnit 5 · Mockito · H2 (integración) · PIT (mutación) |

---

## Visión Completa — Frontend · Microservicios · BDD · Docker

```mermaid
flowchart LR
    USER(["👤 Usuario\nNavegador"])

    subgraph DOCKER ["🐳 Docker Compose — booking-network"]
        direction TB

        subgraph FRONT ["⚛️ Frontend"]
            FE["React + Vite\nNginx · :80"]
        end

        subgraph MICROSERVICES ["☕ Microservicios — Spring Boot"]
            direction TB
            US["User Service\n:8080"]
            ES["Event Service\n:8081"]
            BS["Booking Service\n:8082"]
            PS["Payment Service\n:8083"]
        end

        subgraph BDD ["🐬 Base de Datos — MySQL 8.0 · :3307"]
            direction TB
            DU[("DB: user")]
            DE[("DB: event")]
            DB2[("DB: booking")]
            DP[("DB: payment")]
        end
    end

    USER -->|"HTTP :80"| FE

    FE -->|"REST /api/users"| US
    FE -->|"REST /api/events"| ES
    FE -->|"REST /api/bookings"| BS
    FE -->|"REST /api/payments"| PS

    BS -->|"RestTemplate"| US
    BS -->|"RestTemplate"| ES
    PS -->|"RestTemplate"| BS
    PS -->|"RestTemplate"| ES

    US -->|"JPA"| DU
    ES -->|"JPA"| DE
    BS -->|"JPA"| DB2
    PS -->|"JPA"| DP
```

| Zona | Rol | Tecnología |
|---|---|---|
| **Usuario** | Consume la UI desde el navegador | HTTP · Puerto 80 |
| **Frontend** | Renderiza vistas y llama a los servicios | React · Vite · Nginx |
| **Microservicios** | Lógica de negocio, REST API, comunicación inter-servicio | Java 21 · Spring Boot · MapStruct |
| **BDD** | Persistencia aislada por dominio (DB per Service) | MySQL 8.0 · Spring Data JPA |
| **Docker** | Orquesta contenedores en red privada compartida | Docker Compose · bridge network |
