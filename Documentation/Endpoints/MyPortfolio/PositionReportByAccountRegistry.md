# Reporte de Posicion por Cuenta de Registro (PositionReportByAccountRegistry)

## Objetivo

El objetivo del endpoint es consultar la posicion por cuenta de registro, en modo resumen o detalle, a traves de `MyPortfolio/PositionReportByAccountRegistry`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `PositionReportByAccountRegistry` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/MyPortfolio/PositionReportByAccountRegistry?clearingBusinessDate={{clearingBusinessDate}}` |

## Roles que pueden utilizarlo

- `GET`: `Administrador`, `Multicomitente`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `clearingBusinessDate` | `string` | Fecha del proceso. | Opcional |
| `viewDetails` | `boolean` | Si es `true`, devuelve detalle. | Opcional |
| `InstrumentCode` | `string` | Instrumento puntual para detalle. Solo valido cuando `viewDetails = true`. | Condicional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/MyPortfolio/PositionReportByAccountRegistry?clearingBusinessDate=20260610&viewDetails=false
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "Account": "10001",
    "Instrument": "DLR0726",
    "LongQty": 5,
    "ShortQty": 1
  }
]
```

## Funcion de la BD utilizada

- Actual resumen: `[Registro].[GetReportePortfolioPorComitente]`
- Actual detalle: `[Registro].[GetReportePortfolioDetallePorComitente]`
- Historico resumen: `[Registro].[GetReportePortfolioResumenHistoricoPorComitente]`
- Historico detalle: `[Registro].[GetReportePortfolioDetalleHistoricoPorComitente]`

## Filtros y parametros

### Observaciones tecnicas

- La BL decide actual vs historico segun `HistoricalThreshold` de la aplicacion.
- Siempre filtra por las cuentas de registro del usuario autenticado.
- En modo resumen, la respuesta agrega cantidades long y short por instrumento.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Posicion resumen o detalle. |
| `400 BadRequest` | Se informa `InstrumentCode` sin `viewDetails = true` | `"InstrumentCode parameter is only valid when viewDetails is true."` |
| `400 BadRequest` | Fecha invalida | Mensaje de parseo generado por `TryParseParameter`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `PositionReportByAccountRegistry`, alineado con controller, BL, SQL y roles observados. |
