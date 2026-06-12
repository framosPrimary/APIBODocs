# Registro de Custodia (CustodyRegistration)

## Objetivo

El objetivo del endpoint es informar movimientos de custodia a traves de `PreTrade/CustodyRegistration`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CustodyRegistration` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PreTrade/CustodyRegistration` |

## Roles que pueden utilizarlo

- `POST`: `MC RM`, `Administrador`, `MC`

## Parametros

El metodo acepta un `body` JSON con un objeto o un arreglo de objetos.

Observaciones relevadas:

- si el body es `JObject`, procesa un movimiento
- si el body es `JArray`, procesa varios movimientos
- si el body es nulo o no tiene formato valido, responde `Json format invalid`

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PreTrade/CustodyRegistration
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "RegInsID": "7001",
  "InstrumentCode": "AL30",
  "OriginAccount": "10001",
  "DestinationAccount": "10002"
}
```

## Funcion de la BD utilizada

- Persistencia observada: `[Contribucion].[InsertMovimientoCustodia]`

## Filtros y parametros

### Observaciones tecnicas

- El controller actual expone solamente `POST`.
- Existe codigo historico de `GET` comentado y marcado como eliminado el `2024-04-24`, por lo que no se considera parte del comportamiento vigente.
- La BL contempla integracion con Azure segun `DestinationType`.
- Si existen cancelaciones vencidas segun `DaysAcceptanceCancellationsWallet`, la API puede responder `202 Accepted` con el detalle de operaciones pendientes de aceptacion.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | `null` o resultado de negocio. |
| `202 Accepted` | Hay cancelaciones vencidas pendientes de aceptacion | Objeto con `Code`, `ErrorMessage`, `ErrorDescription`, `Value` y `Status`. |
| `208` | Se detectan registros duplicados | Arreglo JSON con duplicados. |
| `400 BadRequest` | Body nulo o JSON invalido | `"Json format invalid"` |
| `400 BadRequest` | Error de validacion de negocio | Arreglo JSON o mensaje de negocio. |
| `409 Conflict` | Inconsistencia de cuentas | `"Account mismatch"` |
| `500 InternalServerError` | Error SQL o tecnico | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `CustodyRegistration`, alineado con el comportamiento actual `POST`, controller, BL, SQL y roles observados. |
