# Registro de Operaciones OTC (TradeMatchReport)

## Objetivo

El objetivo del endpoint es registrar operaciones OTC a traves de `Trade/TradeMatchReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `TradeMatchReport` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/Trade/TradeMatchReport` |

## Roles que pueden utilizarlo

- `POST`: `MC RM`, `AN`, `MC`

## Parametros

El metodo acepta un `body` JSON con un objeto.

Campos validados explicitamente por el controller:

- `TradeNumber` obligatorio, entero
- `CounterpartAccountCode` obligatorio
- `Quantity` opcional, entero
- `Price` opcional, decimal con hasta `12` digitos enteros y `6` decimales
- `SettlDate` opcional, formato `yyyyMMdd`

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/Trade/TradeMatchReport
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "TradeNumber": "12345",
  "CounterpartAccountCode": "20001",
  "Quantity": "10",
  "Price": "1500.250000",
  "SettlDate": "20260610"
}
```

## Funcion de la BD utilizada

- Validaciones de negocio: `[Contribucion].[ValidacionesReporteOTCAgro]`
- Persistencia cabecera OTC: `[Contribucion].[InsertReporteOTCAgro]`
- Persistencia operacion asociada: `[Contribucion].[InsertReporteOperacionOTCAgro]`
- Cola de integracion observada: `ClearingInterface.ContribucionReporteOperacion`

## Filtros y parametros

### Observaciones tecnicas

- La BL valida primero que el mercado este abierto usando parametros `TradeMatchReportMarket` y `TradeMatchReportMarketDays`.
- Luego agrega al JSON `CompesationMemberAccount` y `CompensationAccount` desde los filtros del usuario.
- Si la operacion se inserta correctamente, tambien genera y envia el mensaje de reporte de operacion a Clearing.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | ID de detalle OTC generado. |
| `400 BadRequest` | JSON invalido o regla de validacion incumplida | Mensaje detallado de validacion. |
| `400 BadRequest` | Error de negocio controlado | Mensaje de `TradeMatchReportException`, por ejemplo `Closed Market` o error SQL validado. |
| `503 ServiceUnavailable` | Error tecnico en persistencia o configuracion | Mensaje de `InvalidOperationException`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `TradeMatchReport`, alineado con controller, BL, persistencia y roles observados. |
