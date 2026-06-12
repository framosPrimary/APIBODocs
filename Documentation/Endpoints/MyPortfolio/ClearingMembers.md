# Miembros Compensadores por Comitente (ClearingMembers)

## Objetivo

El objetivo del endpoint es consultar los miembros compensadores asociados al comitente autenticado a traves de `GET /MyPortfolio/ClearingMembers`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ClearingMembers` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/MyPortfolio/ClearingMembers` |

## Roles que pueden utilizarlo

- `Administrador`
- `Multicomitente`

## Funcion de la BD utilizada

La implementacion vigente prioriza un servicio externo Kattegat.

### Endpoint externo observado

- `KattegatClearingMembers`

## Filtros y parametros

### Observaciones tecnicas

- La BL toma las cuentas de registro desde los filtros del usuario autenticado.
- Para consumo externo obtiene primero un JWT desde `KattegatJWT`.
- La respuesta se agrupa por `ClearingMember` y `ClearingMemberCode`, con una lista interna de `Accounts`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada de miembros compensadores y cuentas. |
| `400 BadRequest` | Error general | Mensaje de excepcion. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /MyPortfolio/ClearingMembers`, alineado con controller, business logic, roles y consumo externo vigente. |
