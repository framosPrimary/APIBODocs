# Autorizacion de Garantias MAE (TradeAllocationRequest)

## Objetivo

El objetivo del endpoint es registrar una autorizacion o rechazo de mensaje de garantias MAE a traves de `Trade/TradeAllocationRequest`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `TradeAllocationRequest` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/Trade/TradeAllocationRequest` |

## Roles que pueden utilizarlo

- `POST`: `EntidadBursatil`

## Parametros

El metodo acepta un `body` JSON con un objeto.

Campos validados:

- `Status` obligatorio, boolean
- `MessageCode` obligatorio, string no vacio, maximo `20` caracteres
- `Observations` opcional, string

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/Trade/TradeAllocationRequest
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "Status": true,
  "MessageCode": "MSG001",
  "Observations": "Aceptado por mesa"
}
```

## Funcion de la BD utilizada

- Persistencia observada: `[Contribucion].[InsertMensajeGarantiaMAE]`
- Cola de integracion observada: `IE.AutorizacionGarantiasMAE`

## Filtros y parametros

### Observaciones tecnicas

- La BL completa `AuditoriaFechaHoraAprobacion` y `AuditoriaUsuarioAprobacion`.
- Si la persistencia devuelve un ID positivo, se envia un mensaje de integracion con estado aceptado o rechazado.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | ID del mensaje registrado. |
| `400 BadRequest` | JSON invalido o regla de validacion incumplida | Mensaje detallado de validacion. |
| `400 BadRequest` | Error de negocio devuelto por persistencia | Mensaje de excepcion, por ejemplo mensaje inexistente o duplicado previo. |
| `503 ServiceUnavailable` | Error tecnico en persistencia | `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `TradeAllocationRequest`, alineado con controller, BL, persistencia y roles observados. |
