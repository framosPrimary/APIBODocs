# Saldos por Finalidades (MarginBalance)

## Objetivo

El objetivo del metodo es consultar margenes requeridos, activos integrados y saldo neto por finalidades a traves del endpoint `GET /Risk/MarginBalance`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `MarginBalance` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Risk/MarginBalance?marketID={{marketID}}&balanceType={{balanceType}}&consolidated={{consolidated}}&currencyDetails={{currencyDetails}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `Clearing`
- `CEL RM`
- `CEL Admin`
- `MC Admin`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `marketID` | `string` | Identificador del mercado. La documentacion historica muestra `1 = A3 Mercados`, `2 = UFEX`. | Obligatorio |
| `balanceType` | `integer` | Tipo de reporte. En el codigo vigente se relevo la implementacion de `1 = saldos finalidades`. | Obligatorio |
| `consolidated` | `boolean` | Parametro expuesto por el controller. No se observo uso efectivo en la BL vigente. Default: `false`. | No |
| `currencyDetails` | `boolean` | Parametro expuesto por el controller. No se observo uso efectivo en la BL vigente. Default: `false`. | No |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Risk/MarginBalance?marketID=1&balanceType=1
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "Resume": {
      "ClearingMemberCode": "123",
      "MarketCode": "ROFX",
      "Market": "MtR",
      "Reference": "Margenes",
      "Currency": "Pesos",
      "RequiredMargin": -100000,
      "IntegratedAsset": 80000,
      "IntegratedNetBalance": -20000,
      "CurrentMargin": -10000,
      "FinalMargin": -110000,
      "FinalTotalIntegratedNetBalance": -30000,
      "Detail": [
        {
          "ClearingAccountCode": "1123",
          "ClearingAccountType": "Terceros",
          "AccountCode": "12345",
          "Account": "TESTPERSONA",
          "AccountType": "Terceros",
          "Reference": "Margenes",
          "Currency": "Pesos",
          "RequiredMargin": -40000,
          "IntegratedAsset": 10000,
          "IntegratedNetBalance": -30000,
          "CurrentMargin": -5000,
          "FinalMargin": -45000,
          "FinalTotalIntegratedNetBalance": -35000
        }
      ]
    }
  }
]
```

## Funcion de la BD utilizada

Este endpoint no invoca una funcion SQL de negocio del repo. Consume un servicio externo configurado en endpoints.

### Capa tecnica utilizada

- El controller llama a `GetMarginBalanceService.GetMarginBalance`.
- La service delega en `GetMarginBalanceBL.GetMarginBalance`.
- La business logic resuelve la URL del endpoint `Winterfell.API.Rest`.
- Para `balanceType == 1`, consume el recurso externo `/api/saldofinalidad`.

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `mcCods` | `string` | Proviene de filtros del usuario autenticado por miembro compensador. |
| `celCods` | `string` | Proviene de filtros del usuario autenticado por cuenta de compensacion. |
| `endpoint` | `string` | Se arma segun `marketID` y filtros del usuario. |

### Observaciones tecnicas

- La BL solo releva implementacion funcional para `balanceType == 1`.
- Si `marketID` viene vacio, consume `/api/saldofinalidad` sin query string adicional.
- Si hay `marketID` y filtros de MC, agrega `EntidadBursatilID` y `MCCods`.
- Si hay `marketID` y filtros de CEL, agrega `EntidadBursatilID` y `CELCods`.
- La respuesta del servicio externo se deserializa a `MarginBalanceResponse`, luego se filtran finalidades `3`, `4` y `24`, y finalmente se agrupa.
- `consolidated` y `currencyDetails` figuran en el controller pero no alteran la logica observada en la BL vigente.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada por miembro, finalidad, moneda y mercado. |
| `400 BadRequest` | Error general no SQL | Mensaje de la excepcion. |
| `500 InternalServerError` | La excepcion contiene `SqlException` | `"Internal Server Error"` |

### Observaciones adicionales

- Si el servicio externo no devuelve elementos, la respuesta practica puede ser `null`.
- No se relevo una implementacion diferenciada para otros valores de `balanceType`; la logica vigente esta enfocada en `1`.
- La documentacion historica habla de informacion en tiempo real; eso sigue siendo consistente con la implementacion actual, que consulta un endpoint externo en linea.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /Risk/MarginBalance`, alineado con controller, business logic, roles relevados y consumo externo vigente. |
