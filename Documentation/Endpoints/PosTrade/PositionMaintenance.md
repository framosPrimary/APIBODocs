# Operaciones Canceladas (PositionMaintenance)

## Objetivo

El objetivo del metodo es consultar operaciones canceladas a traves del endpoint `GET /PosTrade/PositionMaintenance`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `PositionMaintenance` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/PositionMaintenance?clearingBusinessDate={{clearingBusinessDate}}&cfiCode={{cfiCode}}&symbol={{symbol}}&account={{account}}` |

## Roles que pueden utilizarlo

- `Multi Cel`
- `MC RM`
- `Comitente`
- `Administrador`
- `AN`
- `MC`
- `CEL`
- `xComitente`
- `Clearing`
- `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `clearingBusinessDate` | `string` | Fecha del proceso en formato `yyyyMMdd`. | Obligatorio |
| `cfiCode` | `string` | Codigo CFI del instrumento. | No |
| `symbol` | `string` | Simbolo del instrumento. | No |
| `account` | `string` | Cuenta a filtrar. | No |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/PositionMaintenance?clearingBusinessDate=20240223&cfiCode=FXXXXX&symbol=DLR042024&account=1234
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "PosMaintRptID": 145,
    "PosTransType": 2,
    "ClearingBusinessDate": "2024-02-23",
    "SettlSessID": "EOD",
    "Account": "1234",
    "ClearingMemberCode": "356",
    "Instrument": [
      {
        "Symbol": "DLR042024",
        "CFICode": "FXXXXX",
        "SecurityType": "FUT",
        "StrikePrice": 0,
        "UnitOfMeasure": "USD"
      }
    ],
    "Currency": "ARS",
    "PositionQty": [
      {
        "PosType": "FIN",
        "LongQty": 0,
        "ShortQty": 10
      }
    ],
    "PositionAmountData": 125000,
    "TradeVariationAmount": 4300,
    "MarketID": "ROFX",
    "RelatedTradeGrp": [
      {
        "RelatedTradeID": 4587,
        "RelatedTradeIDSource": 1,
        "RelatedTradeDate": "2024-02-23T00:00:00",
        "RelatedTradeQuantity": 10,
        "RelatedTradePrice": 12500,
        "RelatedTradeSide": 1
      }
    ]
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Registro].[GetReporteOperacionCarteraCancelada]`

### Invocacion SQL

```sql
SELECT *
FROM [Registro].[GetReporteOperacionCarteraCancelada] (
    @clearingBusinessDate,
    @cFICode,
    @symbol,
    @account,
    @cuentaRegistroCodigo,
    @participanteCodigo,
    @participanteIntermediarioCodigo,
    @cuentaCompensacionCodigo,
    @miembroCompensadorCodigo
);
```

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@clearingBusinessDate` | `date` | Proviene de `clearingBusinessDate`. |
| `@cFICode` | `varchar` | Proviene de `cfiCode`. |
| `@symbol` | `varchar` | Proviene de `symbol`. |
| `@account` | `varchar` | Proviene de `account`. |
| `@cuentaRegistroCodigo` | `varchar(max)` | Filtro del usuario autenticado. |
| `@participanteCodigo` | `varchar(max)` | Filtro del usuario autenticado. |
| `@participanteIntermediarioCodigo` | `varchar(max)` | Filtro del usuario autenticado. |
| `@cuentaCompensacionCodigo` | `varchar(max)` | Filtro del usuario autenticado. |
| `@miembroCompensadorCodigo` | `varchar(max)` | Filtro del usuario autenticado. |

### Observaciones tecnicas

- La BL agrupa la respuesta en `Instrument`, `PositionQty` y `RelatedTradeGrp`.
- La salida incluye dos operaciones relacionadas posibles: la original y la de cierre/cancelacion.
- El endpoint aplica filtros de alcance del usuario autenticado antes de exponer la informacion.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada de operaciones canceladas. |
| `400 BadRequest` | `clearingBusinessDate` invalida | `"Parameters 'ClearingBusinessDate' malformed. (YYYYMMDD)"` |

### Observaciones adicionales

- `clearingBusinessDate` es funcionalmente obligatorio porque el controller intenta parsearlo siempre.
- Si no hay coincidencias, la API responde `200 OK` con lista vacia.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PosTrade/PositionMaintenance`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
