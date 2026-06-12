# Estado de Liquidacion OTC (SettlementStatusReport)

## Objetivo

El objetivo del endpoint es consultar operaciones para OTC o la relacion detallada futuro-OTC a traves de `PosTrade/SettlementStatusReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `SettlementStatusReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/SettlementStatusReport` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `AN`, `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `viewDetails` | `boolean` | Si es `true`, consulta la relacion detallada futuro-OTC. | Opcional |
| `clearingBusinessDate` | `string` | Fecha en formato `yyyyMMdd`. Obligatoria cuando `viewDetails = true`. | Condicional |
| `accountCode` | `string` | Cuenta local. | Opcional |
| `otcAccountCode` | `string` | Cuenta OTC. | Opcional |
| `symbol` | `string` | Simbolo del instrumento. | Opcional |
| `tradeNumber` | `int` | Numero de trade local. | Opcional |
| `otcTradeNumber` | `int` | Numero de trade OTC. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/SettlementStatusReport?viewDetails=true&clearingBusinessDate=20260610&accountCode=10001
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

- Detalle futuro-OTC: `GetReporteRelacionOperacionesFuturoOtcAsync(...)`
- Consulta OTC general: `GetReporteOperacionesParaOtcAsync(...)`
- Parametros auxiliares OTC: `GetReporteValoresOTCAgroAsync()`

## Filtros y parametros

### Observaciones tecnicas

- La BL deriva miembro compensador y cuenta de compensacion del usuario.
- En `viewDetails = false`, tambien usa parametros auxiliares de productos OTC para construir el reporte.
- La BL normaliza varias fechas de salida al formato `yyyy-MM-dd`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Reporte OTC o detalle futuro-OTC. |
| `400 BadRequest` | `clearingBusinessDate` invalida | `"Parameter 'ClearingBusinessDate' malformed. (YYYYMMDD)"` |
| `400 BadRequest` | `viewDetails = true` sin fecha | `"Parameter 'ClearingBusinessDate' is required when ViewDetails is True"` |
| `500 InternalServerError` | Error tecnico no controlado | `"Internal Server Error"` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `SettlementStatusReport`, alineado con controller, BL, SQL y roles observados. |
