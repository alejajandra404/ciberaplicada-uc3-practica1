# Práctica 1 — Seguridad en APIs (BOLA · Mass Assignment · Validación estricta)

**Ciberseguridad Aplicada** · Ingeniería en Software · ITSON
**Unidad de Competencia 3:** Seguridad de APIs y DevSecOps

---

## 👥 Equipo

| Integrante | Matrícula | Rol en la práctica |
|---|---|---|
| Alejandra García Preciado | 00000252444 | Corrección y verificación |
| Angel Beltran | 00000244865 | Explotación y evidencias |
| Andre Arellano  | 00000229842 | Documentación |

---

## 📁 Estructura del repositorio

| Carpeta / archivo | Contenido |
|---|---|
| `codigo-original/pedidos-api-vulnerable/` | API con vulnerabilidades intencionales (código original provisto) + colección Postman de explotación |
| `codigo-modificado/pedidos-api-seguro/` | API corregida + pruebas de seguridad (`src/test/.../seguridad/`) + colección Postman de validación |
| `evidencias/01-explotacion/` | Capturas de los ataques contra la versión vulnerable (ANTES) |
| `evidencias/02-retest/` | Capturas de los mismos ataques contra la versión segura (DESPUÉS) |
| `evidencias/03-pruebas/` | Salida de `mvn test` |
| `reportes/` | Reporte de vulnerabilidades, de correcciones y de auditoría (Word y PDF) |
| `BITACORA.md` | Registro de quién hizo qué, con fecha y hora |
| `docs/README_docente.md` | README original de la práctica proporcionado por el docente |

---

## ▶️ Cómo ejecutar

**Requisitos:** JDK 17 o superior, Maven 3.9+, Postman.

### Versión vulnerable

```cmd
cd codigo-original\pedidos-api-vulnerable
mvn spring-boot:run
```

La API queda en `http://localhost:8080`. Importar en Postman `codigo-original/pedidos-api-vulnerable/postman/Coleccion_Explotacion_P1.json`.

### Versión segura

> Detener antes la versión vulnerable (`Ctrl+C`): ambas usan el puerto 8080.

```cmd
cd codigo-modificado\pedidos-api-seguro
mvn spring-boot:run
```

Importar en Postman `codigo-modificado/pedidos-api-seguro/postman/Coleccion_Validacion_P1.json`.

### Pruebas de seguridad

```cmd
cd codigo-modificado\pedidos-api-seguro
mvn test
```

### Datos sembrados

| Usuario | `X-User-Id` | Email | Pedidos |
|---|---|---|---|
| Ana | 1 | ana@itson.mx | 1 y 2 |
| Beto | 2 | beto@itson.mx | 3 |

---

## 🔎 Resumen de hallazgos

| ID | Hallazgo | Severidad | OWASP | Estado |
|---|---|---|---|---|
| P1-001 | BOLA de lectura en `GET /api/pedidos/{id}` | Crítica | API1 | ✅ Corregido |
| P1-002 | BOLA en el listado `GET /api/pedidos` | Alta | API1 | ✅ Corregido |
| P1-003 | BOLA de escritura en `POST /api/pedidos` | Crítica | API1 | ✅ Corregido |
| P1-004 | Mass Assignment: autorregistro con rol ADMIN | Crítica | API3 | ✅ Corregido |
| P1-005 | Exposición de contraseñas en respuestas | Alta | API3 | ✅ Corregido |
| P1-006 | BOLA en `GET /api/usuarios/{id}` | Alta | API1 | ✅ Corregido |
| P1-007 | Validación inadecuada de entradas | Alta | API8 | ✅ Corregido |
| P1-008 | Esquema no estricto (campos desconocidos) | Media | API8 | ✅ Corregido |
| P1-009 | Listado de usuarios sin autenticación (versión segura provista) | Alta | API1/API3 | ✅ Corrección C |
| P1-010 | Consola H2 expuesta | Media | API8 | ✅ Corrección F |
| P1-011 | Error 500 por contador IDENTITY de H2 | Baja | API8 | ✅ Corrección D |

Correcciones aplicadas por el equipo sobre la versión segura provista: **A** (import faltante, no compilaba), **B** (`@JsonIgnoreProperties` en el DTO de registro), **C** (listado de usuarios autenticado + 2 pruebas), **D** (contador IDENTITY), **E** (mensaje de validación determinista), **F** (consola H2 desactivada). Resultado: **11/11 pruebas de seguridad en verde** y colección de validación del docente 8/8.

---

## 📄 Reportes

| Reporte | PDF | Word |
|---|---|---|
| Reporte de vulnerabilidades | [PDF](reportes/Reporte_Vulnerabilidades_P1.pdf) | [docx](reportes/Reporte_Vulnerabilidades_P1.docx) |
| Reporte de correcciones | [PDF](reportes/Reporte_Correcciones_P1.pdf) | [docx](reportes/Reporte_Correcciones_P1.docx) |
| Reporte de auditoría | [PDF](reportes/Reporte_Auditoria_P1.pdf) | [docx](reportes/Reporte_Auditoria_P1.docx) |
| Evidencias de la colección de validación del docente (anexo B) | — | [docx](reportes/Evidencias_Validacion_API_P1.docx) |
