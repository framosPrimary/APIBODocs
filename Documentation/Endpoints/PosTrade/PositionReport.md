# Reporte de Posicion (PositionReport)

## Objetivo

El objetivo del metodo es consultar el reporte de posicion a traves del endpoint `GET /PosTrade/PositionReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `PositionReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/PositionReport?clearingBusinessDate={{clearingBusinessDate}}&viewDetails={{viewDetails}}&viewPafg={{viewPafg}}` |

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
| `clearingBusinessDate` | `string` | Fecha en formato `yyyyMMdd`. | No en firma, pero necesaria funcionalmente para obtener datos utiles. |
| `viewDetails` | `boolean` | Si vale `true`, devuelve detalle. Default: `false`. | No |
| `viewPafg` | `boolean` | Si vale `true`, cambia al flujo de contratos PAF G. Default: `false`. | No |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/PositionReport?clearingBusinessDate=20200810&viewDetails=false&viewPafg=false
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "PosReqType": 0,
    "ClearingBusinessDate": "2020-08-10",
    "SettlSessID": "EOD",
    "Account": "1234",
    "ClearingMember": "ISV",
    "ClearingMemberCode": "123",
    "Instrument": {
      "Symbol": "DLR122022",
      "CFICode": "FXXXSX",
      "SecurityType": "Futuro",
      "UnitOfMeasure": "U$S"
    },
    "Currency": "Pesos",
    "AvgPX": 22005.275,
    "SecurityExchange": "ROFX",
    "DailySettlement": 1091,
    "SettlPrice": 21975,
    "SettlPriceType": 1,
    "SettlCurrency": "Pesos",
    "PositionQty": [
      {
        "PosType": "FIN",
        "ShortQty": 40
      }
    ]
  }
]
```

## Funcion de la BD utilizada

El endpoint usa una de estas funciones segun la combinacion de flags:

- Resumen ALyC: `[Registro].[GetReportePortfolio]`
- Detalle ALyC: `[Registro].[GetReportePortfolioDetalle]`
- Resumen PAF G: `[Productos].[GetReporteContratosAFijar]`
- Detalle PAF G: `[Registro].[GetReporteRelacionOperacionesAFijar]`

### Regla de seleccion

- `viewPafg=false` y `viewDetails=false`: usa `GetReportePortfolio`.
- `viewPafg=false` y `viewDetails=true`: usa `GetReportePortfolioDetalle`.
- `viewPafg=true` y `viewDetails=false`: usa `GetReporteContratosAFijar`.
- `viewPafg=true` y `viewDetails=true`: usa `GetReporteRelacionOperacionesAFijar`.

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@fechaProceso` / `@fecha` | `date` | Proviene de `clearingBusinessDate`. |
| `@cuentaRegistroCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@miembroCompensadorCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@cuentaCompensacionCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@participanteIntermediarioCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@participanteCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |

### Observaciones tecnicas

- El controller parsea `clearingBusinessDate` con `TryParseParameter`.
- La service expone cuatro metodos distintos y el controller elige uno segun `viewDetails` y `viewPafg`.
- En el flujo ALyC, la salida se agrupa para construir `Instrument` y `PositionQty`.
- En el flujo detalle ALyC aparecen campos adicionales como `TradeNumber`, `ExecID`, `AvailableBalance`, `CounterpartAccount` y `CounterpartAccountCode`.
- En el flujo PAF G resumen se devuelven contratos a fijar; en detalle se devuelven relaciones de operaciones a fijar.
- Las funciones SQL del flujo ALyC aplican ademas filtros internos del usuario autenticado por cuenta de registro, miembro compensador, cuenta de compensacion y participantes.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Respuesta segun la combinacion de `viewDetails` y `viewPafg`. |
| `400 BadRequest` | `clearingBusinessDate` invalida | Mensaje de parseo devuelto por `TryParseParameter`. |

### Observaciones adicionales

- Aunque `clearingBusinessDate` tiene default `null` en la firma, el comportamiento real depende de que la fecha pueda parsearse y de que las funciones subyacentes encuentren datos utiles.
- Este endpoint concentra cuatro contratos funcionales relacionados bajo una sola ruta publica.
- En los flujos ALyC, las funciones SQL relevadas excluyen contratos con `TipoContratoID` `15` y `28`.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PosTrade/PositionReport`, alineado con controller, business logic, roles relevados y funciones SQL vigentes. |
