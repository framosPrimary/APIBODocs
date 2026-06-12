# Inactivar Cuenta (AccountStatus)

## Objetivo

El objetivo del endpoint es cambiar el estado de una cuenta de registro a traves de `POST /PreTrade/AccountStatus`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccountStatus` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PreTrade/AccountStatus` |

## Roles que pueden utilizarlo

- `MC RM`
- `MC`

## Parametros

Este metodo recibe un `body` JSON.

Observaciones relevadas:

- el body debe ser un `JObject`
- la BL agrega `UserInsert` y `CompensationMember`
- la validacion de negocio se apoya en `ValidateCambioEstadoCuenta`

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PreTrade/AccountStatus
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "AccountCode": "22300",
  "PartyId": "20123456789",
  "Status": 0
}
```

### Ejemplo de respuesta

```json
"1258"
```

## Funcion de la BD utilizada

- Stored procedure de validacion: `[Contribucion].[ValidateCambioEstadoCuenta]`
- Stored procedure de insercion: `[Contribucion].[InsertCambiarEstadoCuenta]`

## Filtros y parametros

### Observaciones tecnicas

- La BL toma el miembro compensador desde los filtros del usuario y lo agrega al JSON.
- Si la cuenta corresponde a neteo y no a cuenta registro, la BL consulta stock de billetera digital antes de permitir la inactivacion.
- Si la operacion se concreta, la cuenta se envia a la cola de clearing.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Cambio exitoso | ID del cambio de estado. |
| `400 BadRequest` | Body nulo o formato invalido | `"Json format invalid"` |
| `400 BadRequest` | La cuenta ya esta activa | `"Account is already active."` |
| `400 BadRequest` | La cuenta ya esta inactiva | `"Account is already inactive."` |
| `400 BadRequest` | No se puede inactivar cuenta propia | `"Can’t inactivate own account."` |
| `400 BadRequest` | La cuenta tiene operaciones abiertas | `"Account has open trades."` |
| `400 BadRequest` | La cuenta tiene activos integrados | `"Has Integrated Assets."` |
| `400 BadRequest` | Cambio en proceso | `"Account Status In Process."` |
| `503 ServiceUnavailable` | Error tecnico en persistencia | `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `POST /PreTrade/AccountStatus`, alineado con controller, business logic, roles y procedures observadas. |
