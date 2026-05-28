# Seguridad en Aplicaciones Web — Post-Contenido 1

Sistema de autenticación completo implementado con **Spring Security 6**, registro de usuarios con BCrypt, login basado en formulario con UserDetailsService que consulta MySQL, y autorización diferenciada por roles ADMIN y USER.

---

## Tecnologías utilizadas

- Java 21
- Spring Boot 3.5.14
- Spring Security 6
- Spring Data JPA / Hibernate
- Thymeleaf + thymeleaf-extras-springsecurity6
- BCryptPasswordEncoder
- MySQL
- Maven

---

## Estructura del proyecto

```
src/
└── main/
    ├── java/com/universidad/seguridad/
    │   ├── SeguridadApplication.java
    │   ├── config/
    │   │   └── SecurityConfig.java
    │   ├── controller/
    │   │   └── AuthController.java
    │   ├── model/
    │   │   └── Usuario.java
    │   ├── repository/
    │   │   └── UsuarioRepository.java
    │   └── service/
    │       ├── UsuarioService.java
    │       └── UsuarioDetailsService.java
    └── resources/
        ├── application.properties
        └── templates/
            ├── dashboard.html
            ├── auth/
            │   ├── login.html
            │   └── registro.html
            └── admin/
                └── panel.html
```

---

## Modelo de datos

### Usuario

| Campo       | Tipo    | Restricciones                    |
|-------------|---------|----------------------------------|
| id          | Long    | PK, autoincremental              |
| nombre      | String  | Obligatorio, máx. 100 caracteres |
| email       | String  | Obligatorio, único, email válido |
| contrasenia | String  | BCrypt hash, nunca texto claro   |
| rol         | String  | ROLE_ADMIN o ROLE_USER           |
| activo      | boolean | true por defecto                 |

---

## Rutas disponibles

| Método | Ruta       | Acceso      | Descripción               |
|--------|------------|-------------|---------------------------|
| GET    | /login     | Público     | Formulario de login       |
| POST   | /login     | Público     | Procesar login            |
| GET    | /registro  | Público     | Formulario de registro    |
| POST   | /registro  | Público     | Registrar nuevo usuario   |
| GET    | /dashboard | Autenticado | Panel principal           |
| GET    | /admin     | ROLE_ADMIN  | Panel de administración   |
| POST   | /logout    | Autenticado | Cerrar sesión             |

---

## Configuración

Edita `src/main/resources/application.properties`:

```properties
spring.application.name=seguridad

spring.datasource.url=jdbc:mysql://localhost:3306/estudiantes_db
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

---

## Cómo ejecutar

```bash
git clone https://github.com/WilliamBalaguera/balaguera-post1-u9.git
cd balaguera-post1-u9
./mvnw spring-boot:run
```

Abre en el navegador: `http://localhost:8080/login`

---

## Usuarios de prueba

| Rol        | Email                 | Contraseña |
|------------|-----------------------|------------|
| ROLE_ADMIN | admin@universidad.edu | password   |
| ROLE_USER  | registrar en /registro| la que definas |

Para crear el usuario ADMIN ejecutar en MySQL:

```sql
USE estudiantes_db;
INSERT INTO usuarios (nombre, email, contrasenia, rol, activo)
VALUES ('Administrador', 'admin@universidad.edu',
'$2a$12$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2uheWG/igi.', 'ROLE_ADMIN', 1);
```

---

## Checkpoints verificados

**Checkpoint 1** — La app arranca sin errores. Al navegar a `/dashboard` Spring Security redirige a `/login`. Se muestra el formulario personalizado.

**Checkpoint 2** — Registro de usuario desde `/registro`. Contraseña guardada como hash BCrypt en MySQL (comienza con `$2a$12$`). Login exitoso muestra el dashboard. Acceder a `/admin` con rol USER devuelve 403 Forbidden.

**Checkpoint 3** — Login como admin muestra el panel con lista de usuarios. Logout invalida la sesión y redirige a `/login?logout`.

---

## Notas de seguridad

- Contraseñas hasheadas con `BCryptPasswordEncoder(12)`.
- Token CSRF incluido automáticamente por Thymeleaf + Spring Security.
- Rutas `/admin/**` protegidas exclusivamente para `ROLE_ADMIN`.
- Logout invalida la sesión HTTP y elimina la cookie `JSESSIONID`.

---

## Autor

**William Balaguera**
