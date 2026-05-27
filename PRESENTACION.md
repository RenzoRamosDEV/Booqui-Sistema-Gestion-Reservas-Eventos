# PRESENTACION — Booqi

> Guía diapositiva a diapositiva. Sigue los consejos del PDF: poca info por slide, storytelling, "menos es más".

---

## DIAPOSITIVA 1: Portada

**Título:**
# Booqi — Tu entrada. En segundos.

**Subtítulo:**
Plataforma de gestión de eventos y reserva de entradas construida sobre arquitectura de microservicios con Java Spring Boot, React y Docker.

---

## DIAPOSITIVA 2: El Problema

**Título:** ¿Por qué es tan difícil reservar una entrada?

**3 puntos clave:**

- **Altas comisiones** — Plataformas como Eventbrite o Cvent aplican modelos de alta comisión que penalizan a pequeños organizadores y PyMEs.
- **Caídas en picos de demanda** — Arquitecturas monolíticas no escalan elásticamente; los sistemas colapsan justo cuando más se necesitan (lanzamiento de entradas).
- **Sin flexibilidad ni control** — Escasa personalización, dependencia del proveedor y ningún control sobre los datos ni las integraciones propias.

---

## DIAPOSITIVA 3: La Solución

**Título:** Booqi — Un flujo. Sin fricciones.

**Cómo lo resuelve:**
Booqi es una plataforma propia, sin intermediarios, construida sobre microservicios que eliminan las tres fricciones del mercado actual.

**3 características de negocio clave:**

1. **Sin comisiones abusivas** — Al ser una solución propia, el organizador controla su modelo de precios y no cede margen a terceros.
2. **Escalabilidad real bajo demanda** — Arquitectura de microservicios independientes: si hay un pico de reservas, solo escala el servicio de Booking, sin que el resto del sistema se vea afectado.
3. **Control total y flexibilidad** — El organizador gestiona sus eventos, sus datos y sus integraciones. Sin dependencia de proveedor.

---

## DIAPOSITIVA 4: Arquitectura y Tecnologías

**Título:** Cómo está construido Booqi

**Estructura del sistema — 4 microservicios independientes:**

| Microservicio | Puerto | Responsabilidad |
|---|---|---|
| **User Service** | 8080 | Registro, login, gestión de perfiles y roles |
| **Event Service** | 8081 | Catálogo de eventos, disponibilidad y capacidad |
| **Booking Service** | 8082 | Reservas, validación y generación de tickets PDF |
| **Payment Service** | 8083 | Procesamiento de pagos y transacciones |

**Frontend:** React + Vite → desplegado como web estática vía Nginx en Docker.

**Stack Tecnológico y por qué:**

- **Java + Spring Boot** — Ecosistema maduro, inyección de dependencias y REST API listos desde el primer momento.
- **React + Vite** — Build ultrarrápido, componentes reutilizables y SPA fluida sin recargas.
- **Docker** — Cada microservicio vive en su propio contenedor; despliegue reproducible y aislado en cualquier entorno.

---

## DIAPOSITIVA 5: Organización del Trabajo y Calidad

**Título:** Cómo garantizamos que funciona

**Metodología:**

- **Agile / Scrum** — Sprints cortos, revisión continua y entrega incremental.
- **Especificación primero (OpenSpec + IA)** — Antes de escribir código, se definió el contrato de cada servicio. La IA ayudó a refactorizar y validar las specs.
- **Control de versiones en GitLab** — Ramas por feature, pull requests revisados y commits semánticos.
- **Tests automatizados** — Clases de equivalencia, valores límite y tests de mutación (Pitest) sobre BookingService, EventService y PaymentService.
- **Base de datos H2 en tests** — Tests de integración reales sin depender de la base de datos de producción.

---

## DIAPOSITIVA 6: Demo en Vivo

**Título:** Veámoslo funcionar

**Dónde está alojado:** GitLab Pages / despliegue local con Docker Compose.

**3 cosas que el público debe observar durante la demo:**

1. **Flujo de reserva completo** — Buscar un evento → seleccionar plaza → pagar → ticket PDF generado al instante.
2. **Independencia de los servicios** — Observar en los logs cómo cada microservicio responde de forma autónoma.
3. **Panel de administración** — Un administrador crea un evento nuevo y aparece disponible para reserva en tiempo real.

---

> *"Menos es más" — usa estas notas para guiarte, no las leas literalmente. Habla despacio, mantén contacto visual y cierra con una conclusión fuerte.*
