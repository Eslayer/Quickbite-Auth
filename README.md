# Quick Bite - Servicio de Autenticación

Microservicio de Autenticación y Autorización para Quick Bite, diseñado según la arquitectura de microservicios moderna para gestionar la identidad y acceso de usuarios en el sistema de gestión de restaurantes.

## 🏗️ Arquitectura

Este microservicio implementa el patrón **IAM (Identity and Access Management)** como parte del Dominio de Usuarios y Experiencia en la arquitectura de Quick Bite.

### Características Principales

- ✅ **Autenticación JWT**: Tokens stateless con expiración configurable
- ✅ **Autorización RBAC**: Control de acceso basado en roles (CLIENTE, COCINA, ADMIN, REPARTIDOR)
- ✅ **Circuit Breaker**: Resiliencia con Resilience4j
- ✅ **Rate Limiting**: Protección contra ataques de fuerza bruta
- ✅ **Validación de Entrada**: Validaciones robustas con Jakarta Validation
- ✅ **Documentación de API**: OpenAPI 3.0 con Swagger UI
- ✅ **Docker Ready**: Contenerización completa con Docker Compose

## 🚀 Tecnologías

- **Java 21** - Última versión LTS
- **Spring Boot 4.0.5** - Framework principal
- **Spring Security** - Seguridad y autenticación
- **JWT (JJWT)** - Tokens de acceso
- **MySQL 8.0** - Base de datos relacional
- **Spring Data JPA** - Persistencia de datos
- **Resilience4j** - Patrones de resiliencia
- **Docker** - Contenerización
- **OpenAPI/Swagger** - Documentación de API

## 📋 Roles del Sistema

| Rol | Descripción | Permisos |
|-----|-------------|----------|
| **CLIENTE** | Clientes finales del restaurante | Realizar pedidos, ver historial |
| **COCINA** | Personal de cocina | Ver comandas, actualizar estados |
| **ADMIN** | Administradores de local | Gestionar inventario, menú, usuarios |
| **REPARTIDOR** | Repartidores | Ver entregas, actualizar estados |

## 🛠️ Configuración y Ejecución

### Prerrequisitos

- Java 21 o superior
- Maven 3.8+
- MySQL 8.0+
- Docker y Docker Compose (opcional)

### Ejecución Local

1. **Clonar el repositorio**
   ```bash
   git clone <repository-url>
   cd Autenticacion
   ```

2. **Configurar base de datos**
   ```sql
   CREATE DATABASE quickbite_auth;
   ```

3. **Ejecutar la aplicación**
   ```bash
   ./mvnw spring-boot:run
   ```

La aplicación estará disponible en `http://localhost:8081`

### Ejecución con Docker

1. **Levantar servicios**
   ```bash
   docker-compose up -d
   ```

2. **Verificar estado**
   ```bash
   docker-compose ps
   ```

3. **Ver logs**
   ```bash
   docker-compose logs -f authentication-service
   ```

## 📚 Endpoints de la API

### Autenticación

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | `/api/v1/auth/register` | Registrar nuevo usuario |
| POST | `/api/v1/auth/authenticate` | Autenticar usuario |
| POST | `/api/v1/auth/refresh` | Refrescar token JWT |
| POST | `/api/v1/auth/validate` | Validar token |

### Ejemplos de Uso

#### Registrar Usuario
```bash
curl -X POST http://localhost:8081/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "juanperez",
    "email": "juan@quickbite.com",
    "password": "password123",
    "firstName": "Juan",
    "lastName": "Perez",
    "role": "CLIENT"
  }'
```

#### Autenticar Usuario
```bash
curl -X POST http://localhost:8081/api/v1/auth/authenticate \
  -H "Content-Type: application/json" \
  -d '{
    "username": "juanperez",
    "password": "password123"
  }'
```

#### Usar Token JWT
```bash
curl -X GET http://localhost:8081/api/v1/auth/validate \
  -H "Authorization: Bearer <tu-jwt-token>"
```

## 🔧 Configuración

### Variables de Entorno

| Variable | Valor por Defecto | Descripción |
|----------|-------------------|-------------|
| `SPRING_DATASOURCE_URL` | `jdbc:mysql://localhost:3306/quickbite_auth` | URL de base de datos |
| `JWT_SECRET_KEY` | `mySecretKeyForQuickBiteAuthenticationService2024` | Clave secreta para JWT |
| `JWT_EXPIRATION` | `86400000` | Expiración de token (ms) |
| `SERVER_PORT` | `8081` | Puerto del servicio |

### Configuración de Circuit Breaker

```yaml
resilience4j:
  circuitbreaker:
    instances:
      authenticationService:
        failure-rate-threshold: 50
        wait-duration-in-open-state: 30s
        sliding-window-size: 10
  ratelimiter:
    instances:
      authenticationService:
        limit-for-period: 10
        limit-refresh-period: 1s
```

## 📖 Documentación

- **Swagger UI**: `http://localhost:8081/swagger-ui.html`
- **OpenAPI JSON**: `http://localhost:8081/v3/api-docs`
- **Health Check**: `http://localhost:8081/actuator/health`

## 🔐 Seguridad

### Características de Seguridad

- **Contraseñas encriptadas** con BCrypt
- **Tokens JWT** con firma HMAC-SHA256
- **CORS** configurado para desarrollo
- **Rate Limiting** para prevenir ataques
- **Validación de entrada** robusta

### Mejores Prácticas

- Cambiar la clave secreta JWT en producción
- Usar HTTPS en todos los endpoints
- Configurar timeouts adecuados
- Monitorear logs de seguridad
- Implementar políticas de bloqueo

## 🧪 Testing

### Ejecutar Tests
```bash
./mvnw test
```

### Tests de Integración
```bash
./mvnw test -Dspring.profiles.active=test
```

## 📊 Monitoreo

### Endpoints de Actuator

- `/actuator/health` - Estado del servicio
- `/actuator/info` - Información del servicio
- `/actuator/metrics` - Métricas de rendimiento

## 🚀 Despliegue

### Consideraciones de Producción

1. **Base de Datos**: Configurar MySQL con alta disponibilidad
2. **Secretos**: Usar secret manager para claves JWT
3. **Escalabilidad**: Configurar horizontal pod autoscaling
4. **Logs**: Centralizar logs con ELK stack
5. **Monitoreo**: Implementar Prometheus + Grafana

### Despliegue en Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: authentication-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: authentication-service
  template:
    metadata:
      labels:
        app: authentication-service
    spec:
      containers:
      - name: authentication-service
        image: quickbite/authentication-service:latest
        ports:
        - containerPort: 8081
        env:
        - name: SPRING_DATASOURCE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
```

## 🤝 Contribución

1. Fork del repositorio
2. Crear feature branch
3. Commit con cambios
4. Push al branch
5. Crear Pull Request

## 📝 Licencia

Este proyecto está licenciado bajo la Licencia Apache 2.0 - ver el archivo [LICENSE](LICENSE) para detalles.

## 🆘 Soporte

Para soporte técnico o preguntas:

- **Email**: dev@quickbite.com
- **Documentación**: [Wiki del Proyecto](https://github.com/quickbite/authentication-service/wiki)
- **Issues**: [GitHub Issues](https://github.com/quickbite/authentication-service/issues)

---

**Servicio de Autenticación Quick Bite** - Parte de la arquitectura de microservicios para la gestión moderna de restaurantes.
