# Documentación técnica: Servicios de Start (cubeploy)

Este documento describe la estructura y propósito de los archivos y subcarpetas dentro de `api/cmd/services/start` del repositorio [MrUprizing/cubeploy](https://github.com/MrUprizing/cubeploy). Está orientado a desarrolladores que deseen entender o extender la funcionalidad de este servicio.

## Estructura general

- **app/**: Lógica de aplicación y orquestación de casos de uso.
- **domain/**: Definición de entidades y contratos del dominio.
- **infrastructure/**: Implementaciones concretas de servicios externos (HTTP, Docker).
- **ports/**: Definición de interfaces para la infraestructura.

---

## Archivos y carpetas

### app/start_container.go
- Define el caso de uso principal para iniciar un contenedor.
- Usa la interfaz `DockerPort` para abstraer la operación de inicio.
- Expone la función `NewStartContainerUseCase` para crear el caso de uso y el método `Execute` para ejecutarlo.

### app/start_container_test.go
- Pruebas unitarias para el caso de uso de inicio de contenedor.
- Usa mocks para simular la infraestructura Docker.
- Valida tanto el flujo exitoso como el manejo de errores.

### domain/container.go
- Define la estructura `StartContainerRequest` que representa la petición de inicio de un contenedor (por ahora, solo el nombre).

### ports/docker_port.go
- Define la interfaz `DockerPort` que abstrae las operaciones de Docker necesarias para el caso de uso.
- Permite desacoplar la lógica de aplicación de la implementación concreta de Docker.

### infrastructure/docker_client.go
- Implementa la interfaz `DockerPort` usando el cliente oficial de Docker.
- Expone la función `NewDockerClientAdapter` para crear la instancia y el método `StartContainer` para iniciar un contenedor real.

### infrastructure/http_handler.go
- Expone un handler HTTP para el endpoint de inicio de contenedores.
- Parsea la petición, ejecuta el caso de uso y responde con el resultado o error correspondiente.

### infrastructure/http_handler_test.go
- Pruebas unitarias y de integración para el handler HTTP.
- Valida el flujo exitoso, errores de validación y errores de infraestructura.

---

## Flujo general
1. El handler HTTP recibe una petición para iniciar un contenedor.
2. Parsea la petición y construye un `StartContainerRequest`.
3. Llama al caso de uso (`StartContainerUseCase`), que a su vez usa la interfaz `DockerPort`.
4. La implementación concreta de `DockerPort` (por ejemplo, `DockerClientAdapter`) interactúa con Docker para iniciar el contenedor.
5. El resultado se retorna al cliente HTTP.

---

## Notas
- El diseño sigue principios de Clean Architecture: separación de dominio, aplicación, infraestructura y puertos.
- Es sencillo extender el sistema agregando nuevas implementaciones de `DockerPort` o nuevos casos de uso.

---

Para detalles de implementación, revisar los archivos fuente en el repositorio original.
