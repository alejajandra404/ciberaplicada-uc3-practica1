# Práctica 1 — Seguridad en APIs

Materia: **Ciberseguridad Aplicada** · Ingeniería en Software · ITSON

> Este archivo es el README original proporcionado por el docente junto con el código de la práctica. Se conserva sin cambios como referencia. El README del equipo está en la raíz del repositorio.

Esta práctica se trabaja sobre una API REST de gestión de pedidos construida con **Java 17** y **Spring Boot 3.3.x**. Se entrega en dos versiones: una **vulnerable** con fallas intencionales de seguridad y una **segura** con las correcciones correspondientes, más una suite de pruebas de seguridad automatizadas.

---

## 1. Objetivo de aprendizaje

Al finalizar la práctica, el estudiante será capaz de:

1. **Identificar** vulnerabilidades Broken Object Level Authorization (BOLA), Mass Assignment y validación inadecuada de esquemas en una API REST real.
2. **Explotar** dichas vulnerabilidades de forma controlada mediante peticiones HTTP con Postman, registrando evidencias.
3. **Corregir** las fallas aplicando controles: verificación de propiedad de recursos, uso de DTOs con Bean Validation, esquemas estrictos de deserialización y salida de datos sin información sensible.
4. **Automatizar** la verificación mediante pruebas de seguridad con `@SpringBootTest` + `MockMvc`.
5. Relacionar cada falla con su categoría en la **OWASP API Security Top 10 2023**.

> **Nota:** la autenticación se simula con el encabezado `X-User-Id`. 

---

## 2. Vulnerabilidades trabajadas

| # | Vulnerabilidad | Categoría OWASP API Security Top 10 (2023) | Dónde está en la versión vulnerable | Corrección en la versión segura |
|---|----------------|---------------------------------------------|--------------------------------------|----------------------------------|
| 1 | **BOLA** (acceso a objetos de otros usuarios) | **API1:2023 – Broken Object Level Authorization** | `GET /api/pedidos/{id}` no verifica que el pedido pertenezca al usuario autenticado | Verificación `pedido.usuario.id == usuarioAutenticado`; de lo contrario, **403** |
| 2 | **BOLA de escritura** | API1:2023 | `POST /api/pedidos` toma el dueño del campo `usuario` del cuerpo JSON | El dueño **siempre** se resuelve del encabezado `X-User-Id`; el DTO no incluye `usuario` |
| 3 | **Mass Assignment** (BOPLA) | **API3:2023 – Broken Object Property Level Authorization** | `POST /api/usuarios` enlaza directamente la entidad `Usuario`: se puede enviar `"rol":"ADMIN"` o `"id":99` | DTO `UsuarioRegistroDTO` sin `rol` ni `id`; rol fijado por el servidor; esquema estricto de Jackson |
| 4 | **Validación inadecuada de esquemas** | **API8:2023 – Security Misconfiguration** (falta de `spring-boot-starter-validation`) | Entradas sin validar: montos negativos, emails inválidos, contraseñas cortas | `@Valid` + restricciones (`@NotBlank`, `@Email`, `@Size`, `@Positive`, `@DecimalMax`) y manejo centralizado de errores |
| 5 | **Exposición de datos sensibles** | Relacionada con **API3:2023** y buenas prácticas de minimización | Las respuestas incluyen la contraseña de los usuarios (sin `@JsonIgnore`, entidades serializadas completas) | `@JsonIgnore` en `password` y DTOs de salida (`UsuarioRespuestaDTO`, `PedidoRespuestaDTO`) |

Referencia: [OWASP API Security Top 10 (2023)](https://owasp.org/API-Security/) — proyectos [API Security Top 10 2023](https://owasp.org/API-Security/editions/2023/en/0x11-t10/) y [OWASP Top 10](https://owasp.org/www-project-top-ten/).

---

## 3. Requisitos

- **JDK 17** (verificar con `java -version`).
- **Apache NetBeans** (opcional, para ejecutar y depurar) **o** **GitHub Codespaces** (no se requiere configurar nada: la base de datos es H2 en memoria).
- **Postman** (escritorio o web) para ejecutar las colecciones incluidas.
- **Maven** (incluido con `mvnw`/NetBeans; en Codespaces ya está disponible).
- ❌ **No se requiere Docker** ni MySQL para la ejecución básica.

---

## 4. Paso a paso: ejecutar y explotar la versión vulnerable

### 4.1 Ejecutar la API

**Opción A — Terminal (NetBeans, Codespaces o cmd):**

```bash
cd codigo-original/pedidos-api-vulnerable
mvn spring-boot:run
```

**Opción B — Apache NetBeans:** abra el proyecto `codigo-original/pedidos-api-vulnerable` (se reconoce como proyecto Maven), clic derecho sobre el proyecto → **Run**.

La API queda en `http://localhost:8080` con datos sembrados:

| Usuario | id | email | pedidos |
|---------|----|-------|---------|
| Ana | 1 | ana@itson.mx | 1 y 2 |
| Beto | 2 | beto@itson.mx | 3 |

### 4.2 Explotar con Postman

1. Importe la colección `codigo-original/pedidos-api-vulnerable/postman/Coleccion_Explotacion_P1.json`.
2. La variable `{{baseUrl}}` ya apunta a `http://localhost:8080`.
3. Ejecute las peticiones en orden y observe:
   - **02 - BOLA:** con `X-User-Id: 1`, `GET /api/pedidos/3` responde **200** con el monitor de Beto (¡y su contraseña!).
   - **04 - BOLA de escritura:** Ana crea un pedido de $99,999 a nombre de Beto (**201**).
   - **05 - Mass Assignment:** el registro con `"rol":"ADMIN"` se acepta y **se guarda como ADMIN**.

### 4.3 Registrar evidencias

Por cada ataque, capture (para el portafolio):

1. La **petición** completa en Postman (URL, encabezados y cuerpo).
2. La **respuesta** con código HTTP y JSON.
3. Una **breve explicación** de por qué el sistema permitió el acceso o el cambio no autorizado.

Guarde las capturas en la carpeta de evidencias de su portafolio y registre cada hallazgo en el **reporte de vulnerabilidades** (plantilla en `..\..\plantillas\`).

---

## 5. Paso a paso: ejecutar la versión segura y correr las pruebas

### 5.1 Ejecutar la API segura

```bash
cd codigo-modificado/pedidos-api-seguro
mvn spring-boot:run
```

(En NetBeans: abra el proyecto y ejecute **Run**, igual que en la versión vulnerable.)

### 5.2 Correr las pruebas de seguridad

```bash
mvn test
```

En **NetBeans**: clic derecho sobre el proyecto → **Test Project** (o clic derecho sobre la carpeta `Test Packages` → **Test**).

Las pruebas están en `src/test/java/com/itson/ciberseguridad/seguridad/` y son el **código de pruebas de seguridad** del portafolio:

| Clase | Qué verifica |
|-------|--------------|
| `PruebaBolaTest` | Su propio pedido → 200; el pedido ajeno → **403**; sin `X-User-Id` → **401** |
| `PruebaMassAssignmentTest` | `"rol":"ADMIN"` en el JSON se ignora (rol almacenado = `USER`); campo desconocido → **400** |
| `PruebaValidacionTest` | Email inválido, contraseña corta y nombre vacío → **400** con mensajes por campo |

### 5.3 Validar con Postman

Importe `codigo-modificado/pedidos-api-seguro/postman/Coleccion_Validacion_P1.json` y ejecute los mismos ataques: ahora obtendrá **403 / 401 / 400** y el rol `"ADMIN"` será ignorado. Capture las respuestas como evidencias de corrección.

---

## 6. Resumen: vulnerable vs. seguro

| Aspecto | Versión vulnerable | Versión segura | Por qué |
|---------|--------------------|----------------|---------|
| Acceso a pedidos ajenos | 200 con datos del pedido de otro usuario | **403** (`AccesoDenegadoException`) | Anti-BOLA: se verifica la propiedad del recurso |
| Dueño de un pedido nuevo | El campo `usuario` del JSON decide | Siempre el usuario del encabezado `X-User-Id` | Anti-BOLA de escritura |
| `POST /api/usuarios` con `"rol":"ADMIN"` | Se guarda como ADMIN | DTO sin `rol`; rol fijo `USER` | Anti-Mass Assignment |
| Campo desconocido en el JSON | Se ignora silenciosamente | **400** (`fail-on-unknown-properties=true`) | Esquema estricto |
| Entradas inválidas (email, password, monto…) | Se aceptan sin reclamar | **400** con mensajes por campo | Bean Validation + `@Valid` |
| Contraseña en las respuestas JSON | Visible | Nunca (DTOs + `@JsonIgnore`) | Minimización de datos |
| Dependencia `spring-boot-starter-validation` | Ausente (deliberado) | Presente | Validación de esquemas |
| Errores HTTP | Genéricos o sin control | 400/401/403/404 centralizados, sin stacktrace | `GlobalExceptionHandler` (`@RestControllerAdvice`) |

---

## 7. Entregables del portafolio

La práctica alimenta los **6 entregables** del portafolio. Verifique su checklist antes de entregar:

| # | Entregable | Material de esta práctica |
|---|------------|---------------------------|
| 1 | Código original | Proyecto `codigo-original/pedidos-api-vulnerable` (suba la carpeta a su repositorio) |
| 2 | Código modificado | Proyecto `codigo-modificado/pedidos-api-seguro` |
| 3 | Código de pruebas de seguridad | `codigo-modificado/pedidos-api-seguro/src/test/java/com/itson/ciberseguridad/seguridad/` |
| 4 | Reporte de vulnerabilidades | Sus hallazgos de la sección 4.3 (capturas + descripción) |
| 5 | Reporte de correcciones | Cambios documentados en la tabla de la sección 6, aplicados por usted |
| 6 | Reporte de auditoría | Resultado de ejecutar `mvn test` + colección de validación en Postman |

> 📄 Las **plantillas** para elaborar los reportes 4, 5 y 6 se encuentran en `..\..\plantillas\`.

---

## 8. Uso de MySQL en lugar de H2

Ambos proyectos vienen configurados con **H2 en memoria** para que funcionen sin configuración (ideal para Codespaces). Para usar el **MySQL** que ya tiene instalado:

1. Cree la base de datos: `CREATE DATABASE ciberpedidos;`
2. En `src/main/resources/application.properties`, comente las líneas de H2 y descomente el bloque MySQL:

```properties
# H2 (comentar si se usa MySQL)
# spring.datasource.url=jdbc:h2:mem:pedidosdb
# spring.datasource.driverClassName=org.h2.Driver
# spring.datasource.username=sa
# spring.datasource.password=

# MySQL
spring.datasource.url=jdbc:mysql://localhost:3306/ciberpedidos?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=TU_CONTRASENA
spring.sql.init.mode=always
```

3. Alternativa con **perfil de Spring**: copie el bloque anterior en un archivo `application-mysql.properties` y ejecute con `mvn spring-boot:run -Dspring-boot.run.profiles=mysql`.

Con MySQL, cambie `spring.jpa.hibernate.ddl-auto=update` por `create-drop` solo si desea reiniciar los datos en cada ejecución.

---

## 9. Solución de problemas

| Problema | Causa probable | Solución |
|----------|----------------|----------|
| `Port 8080 was already in use` | Otra aplicación (o la API vulnerable) sigue ejecutándose | Cierre el otro proceso, o cambie el puerto con `server.port=8081` en `application.properties` |
| No puedo entrar a la consola H2 | URL o credenciales incorrectas | Use `http://localhost:8080/h2-console`; JDBC URL: `jdbc:h2:mem:pedidosdb`, usuario `sa`, contraseña vacía |
| `data.sql` no carga los datos | Hibernate no terminó de crear el esquema | Verifique que existan `spring.jpa.defer-datasource-initialization=true` y `spring.sql.init.mode=embedded` |
| 401 en la versión segura | Falta el encabezado `X-User-Id` | Agregue el encabezado: `X-User-Id: 1` (Ana) o `X-User-Id: 2` (Beto) |
| 400 con "cuerpo de la solicitud no es válido" | Campo desconocido o tipo incorrecto en el JSON | Es el comportamiento esperado del esquema estricto; quite el campo extra |
| 403 en la versión segura | Está accediendo a un recurso de otro usuario | Es el comportamiento esperado (anti-BOLA); use el `id` de sus propios recursos |
| `mvn` no reconocido | Maven no está en el PATH | Use NetBeans (Run/Test) o GitHub Codespaces, donde Maven está preinstalado |
| 404 en cualquier ruta | La API no está corriendo o la ruta está mal escrita | Verifique que la consola muestre "Started PedidosApiApplication" y use `/api/pedidos` o `/api/usuarios` |
