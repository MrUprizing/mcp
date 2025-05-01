# Documentación de la carpeta `pull` de cubeploy

Esta documentación describe la arquitectura, propósito y funcionamiento de los archivos y subcarpetas dentro de `api/cmd/services/pull` del repositorio [cubeploy](https://github.com/MrUprizing/cubeploy). El objetivo principal de este módulo es gestionar la operación de "pull" de imágenes Docker de manera desacoplada y testeable.

## Estructura de carpetas

- **app/**: Lógica de aplicación y casos de uso.
- **domain/**: Entidades del dominio.
- **infrastructure/**: Implementaciones concretas de servicios externos (Docker, HTTP, etc).
- **ports/**: Interfaces (puertos) para la infraestructura.

---

## Archivos y su propósito

### app/pull_image.go
Define el caso de uso principal `PullImageUseCase`, que orquesta la operación de pull de una imagen Docker usando el puerto `DockerPort`.

- **PullImageUseCase**: Estructura que contiene la dependencia al puerto Docker.
- **NewPullImageUseCase**: Constructor.
- **Execute**: Ejecuta el pull de la imagen recibida.

### app/pull_image_test.go
Pruebas unitarias para el caso de uso. Usa mocks para simular el comportamiento del puerto Docker y valida tanto el éxito como el error en la operación.

### domain/image.go
Define la entidad de dominio `Image` con los campos `Name` y `Tag` que representan una imagen Docker.

### infrastructure/docker_client.go
Implementa el adaptador concreto para interactuar con Docker usando el SDK oficial. Implementa el método `PullImage` que realiza el pull real de la imagen.

### infrastructure/http_handler.go
Expone un handler HTTP (usando Fiber) que recibe peticiones para hacer pull de imágenes. Parsea el body, ejecuta el caso de uso y responde con el resultado.

### infrastructure/http_handler_test.go
Pruebas unitarias del handler HTTP, validando los casos de éxito, error de request y error de Docker.

### ports/docker_port.go
Define la interfaz `DockerPort` que abstrae la operación de pull de imágenes. Permite desacoplar la lógica de aplicación de la implementación concreta de Docker.

---

## Flujo de operación
1. El handler HTTP recibe una petición POST con los datos de la imagen.
2. El handler construye la entidad `Image` y ejecuta el caso de uso `PullImageUseCase`.
3. El caso de uso delega la operación al puerto `DockerPort`.
4. El adaptador de infraestructura realiza el pull real usando el SDK de Docker.
5. El resultado se retorna al cliente HTTP.

---

## Ventajas de la arquitectura
- **Desacoplamiento**: La lógica de aplicación no depende de detalles de infraestructura.
- **Testabilidad**: Uso de interfaces y mocks para pruebas unitarias.
- **Extensibilidad**: Se pueden agregar nuevas implementaciones de `DockerPort` sin modificar la lógica de negocio.

---

## Ejemplo de request HTTP
```json
POST /images/pull
{
  "name": "itzg/minecraft-server",
  "tag": "latest"
}
```

---

Para más detalles, revisar los archivos fuente en el repositorio original.