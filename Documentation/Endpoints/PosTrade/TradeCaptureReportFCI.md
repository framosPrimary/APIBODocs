# Reporte de Operaciones FCI (TradeCaptureReportFCI)

## Objetivo

El objetivo del endpoint es consultar el reporte de operaciones de fondos comunes de inversion a traves de `PosTrade/TradeCaptureReportFCI`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `TradeCaptureReportFCI` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/TradeCaptureReportFCI` |

## Roles que pueden utilizarlo

- `GET`: `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde. | Condicional |
| `dateTo` | `string` | Fecha hasta. | Condicional |
| `marketID` | `string` | Identificador de mercado. | Opcional |
| `marketSegmentID` | `string` | Segmento de mercado. | Opcional |
| `cFICode` | `string` | Codigo CFI. | Opcional |
| `trdRptStatus` | `string` | Estado del trade report. | Opcional |
| `product` | `string` | Producto. | Opcional |
| `segmentID` | `string` | Segmento funcional. | Opcional |

Si se informa una fecha, deben informarse ambas (`dateFrom` y `dateTo`).

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/TradeCaptureReportFCI?dateFrom=20260601&dateTo=20260610&marketID=ROFX&product=FCI
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "TradeID": "5001",
    "Product": "FCI",
    "CreditsInCollClearingAccount": 1200.50
  }
]
```

## Funcion de la BD utilizada

- La BL consume `GetReporteOperacionFCIPublicacionDAC().GetReporteOperacionFCIPublicacionAsync(...)`.

## Filtros y parametros

### Observaciones tecnicas

- La estructura de filtros es equivalente a `TradeCaptureReport`, pero enfocada en operaciones FCI.
- La salida agrupa campos operativos y agrega `CreditsInCollClearingAccount`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Reporte de operaciones FCI. |
| `400 BadRequest` | Se informa solo una de las dos fechas | `"Invalid entry. Both dateFrom and dateTo are required."` |
| `500 InternalServerError` | Error tecnico no controlado | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `TradeCaptureReportFCI`, alineado con controller, BL y roles observados. |
