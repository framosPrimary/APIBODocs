# Operaciones (TradeCaptureReport - POST)

## Objetivo

El objetivo del metodo es registrar operaciones a traves del endpoint `PosTrade/TradeCaptureReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `TradeCaptureReport` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PosTrade/TradeCaptureReport` |

## Roles que pueden utilizarlo

- `POST`: `MC RM`, `Administrador`, `MC`, `ACVN`, `Clearing`, `EntidadBursatil`

## Parametros

Este metodo recibe un `body` JSON.

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `ExecID` | `integer` | Identificador de ejecucion. | Obligatorio |
| `LastQty` | `number` | Cantidad operada. | Obligatorio |
| `TransactTime` | `string` | Fecha y hora de transaccion. | Obligatorio |
| `MarketID` | `string` | Mercado. | Obligatorio |
| `LastPx` | `number` | Precio de la operacion. | Obligatorio |
| `TradeReportTransType` | `integer` | Tipo de transaccion reportada. | Obligatorio |
| `Instrument` | `array` | Debe contener exactamente un instrumento. | Obligatorio |
| `RootParties` | `array` | Debe contener uno o dos participantes raiz. | Obligatorio |
| `TrdCapRptSideGrp` | `array` | Debe contener al menos un lado de la operacion. | Obligatorio |
| `PaymentMethodGrp` | `array` | Medios de pago y porcentajes. | No |

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PosTrade/TradeCaptureReport
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "ExecID": 8463463,
  "LastQty": 2,
  "TransactTime": "2026-06-10T14:15:00",
  "MarketID": "ROFX",
  "LastPx": 12500,
  "TradeReportTransType": 0,
  "Instrument": [
    {
      "CFICode": "FXXXXX",
      "Symbol": "DLR042024"
    }
  ],
  "RootParties": [
    {
      "RootPartyID": "356",
      "RootPartyIDSource": "D",
      "RootPartyRole": "4"
    }
  ],
  "TrdCapRptSideGrp": [
    {
      "Account": "1234",
      "Side": "1"
    }
  ]
}
```

### Ejemplo de respuesta

```json
9876
```

## Funcion de la BD utilizada

- Stored procedure utilizada: `[Contribucion].[InsertReporteOperacion]`

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@ReporteOperacion` | `varchar(max)` | JSON enriquecido con el usuario autenticado. |
| `User` | `string` | Lo agrega la BL con el usuario autenticado. |

### Observaciones tecnicas

- Si el body trae `EndAccruedInterestAmt`, la BL lo normaliza internamente a `Rate`.
- Si el usuario tiene filtro de entidad bursatil, `MarketID` debe coincidir con ese alcance.
- Se validan feriados sobre `SettlDate` cuando ese dato viene informado.
- Luego de insertar, la operacion se envia a la cola `ClearingInterface.ContribucionReporteOperacion`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | Identificador interno. |
| `400 BadRequest` | JSON invalido | `"Json format invalid"` |
| `400 BadRequest` | Faltan campos obligatorios o grupos invalidos | Mensajes como `"Field ExecID cannot be null."`, `"You should entry just only one Instrument."` |
| `400 BadRequest` | `SettlDate` cae en feriado | `"Settlement date cannot be a holiday"` |
| `400 BadRequest` | `MarketID` fuera del alcance del usuario | `"Invalid MarketID"` |
| `503 ServiceUnavailable` | Error tecnico durante la insercion | `"The operation could not be performed."` |

### Observaciones adicionales

- `PaymentMethodCode` solo admite `1`, `2`, `3` o `4`.
- Si existe `PaymentMethodGrp`, la suma de `PaymentPercentage` debe ser `100`.
- Si la persistencia detecta un `ExecID` duplicado, la BL expone el mensaje `"ExecID in univocal."`.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `POST` para `TradeCaptureReport`, alineado con controller, BL, roles y funciones vigentes. |
