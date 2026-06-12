# Caratulas Agrupadas (AllocationReport)

## Objetivo

El objetivo del endpoint es consultar caratulas agrupadas a traves de `GET /PosTrade/AllocationReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AllocationReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/AllocationReport?date={{date}}&tradeNumber={{tradeNumber}}&secondaryAllocID={{secondaryAllocID}}&allocID={{allocID}}&futAccount={{futAccount}}&cedAccount={{cedAccount}}&maturityMonthYear={{maturityMonthYear}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `AN`
- `MC`
- `CEL`
- `CEL RM`

## Parametros

Todos los parametros son opcionales individualmente, pero debe cumplirse al menos una condicion de filtro.

Reglas observadas:

- debe informarse al menos un parametro
- si se usa `tradeNumber`, `secondaryAllocID` o `allocID`, tambien debe venir `date` o `maturityMonthYear`

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de caratulas agrupadas. |
| `400 BadRequest` | No se envio ningun parametro | `"At least one parameter must be supplied."` |
| `400 BadRequest` | Filtro incompleto para `tradeNumber`, `secondaryAllocID` o `allocID` | `"For TradeNumber filter introduce also filter by Date or MaturityMonthYear."` |
| `400 BadRequest` | Fecha invalida | `"Date format not valid."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PosTrade/AllocationReport`, alineado con controller, roles y reglas de validacion observadas. |
