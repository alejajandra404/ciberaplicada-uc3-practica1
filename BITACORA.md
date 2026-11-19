# Bitácora de trabajo — Práctica 1

Registro de **quién hizo qué, cuándo**. Agregar una fila cada vez que terminen una actividad.

> ⚠️ **Para evitar conflictos de Git:** cada integrante escribe **solo en su propia sección**. Hagan `git pull` antes de editar este archivo.
> Formato de fecha y hora: `dd/mm/aaaa hh:mm`.

---

## 🟦 Corrección y verificación  — Alejandra García 

| Fecha y hora | Actividad | Evidencia / commit |
|---|---|---|
| 16/11/2026 15:20 | Creación del repositorio, estructura de carpetas y commit inicial con el código provisto | Commit inicial |
| 17/11/2026 16:23 | Explotación de reconocimiento y BOLA de lectura contra la API vulnerable (E-01 a E-05). Enumeración: `GET /api/pedidos/99` da 404, lo que permite distinguir IDs existentes | E-01 a E-05, E-04b (rama `explotacion`) |
| 17/11/2026 16:23 | `mvn test` sobre la versión segura original: no compila (falta import de `Pedido` en `PedidoController`) | T-01 |
| 17/11/2026 16:23 | Corrección A: agregar import faltante en `PedidoController`. Compila falla 1 de 9 pruebas (`rolAdminEnJson_seIgnoraYSeAlmacenaUSER`) | T-02 |
| 17/11/2026 16:28 | Corrección B: `@JsonIgnoreProperties({"id","rol"})` en `UsuarioRegistroDTO`. Aparecen 2 fallos ocultos: registro de usuario responde 500 (contador IDENTITY de H2) y mensaje de validación de `nombre` no determinista | T-03 |
| 17/11/2026 16:33 | Corrección D: reinicio del contador IDENTITY en `data.sql`. Corrección E: prioridad del mensaje `@NotBlank` en `GlobalExceptionHandler`. 9 de 9 pruebas pasan | T-04 |
| 17/11/2026 16:58 | Corrección C: `GET /api/usuarios` exige `X-User-Id` y devuelve solo al usuario autenticado (+2 pruebas nuevas en `PruebaBolaTest`). Corrección F: consola H2 desactivada. 11 de 11 pruebas pasan | T-05 |

<!-- Fin de la sección de corrección. Agregar filas ARRIBA de esta línea. -->

---

## 🟩 Explotación y evidencias — Angel Beltran

| Fecha y hora | Actividad | Evidencia / commit |
|---|---|---|

<!-- Fin de la sección de explotación. Agregar filas ARRIBA de esta línea. -->

---

## 🟨 Documentación — Andre Arellano

| Fecha y hora | Actividad | Evidencia / commit |
|---|---|---|

<!-- Fin de la sección de documentación. Agregar filas ARRIBA de esta línea. -->

---

## 📝 Revisión final del equipo

| Fecha y hora | Integrante | Documento revisado | Resultado |
|---|---|---|---|

<!-- Fin de la sección de revisión. -->
