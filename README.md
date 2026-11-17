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

> [PENDIENTE: se completa al terminar la explotación y el retest]

---

## 📄 Reportes

> [PENDIENTE: enlaces a los PDF en `reportes/`]
