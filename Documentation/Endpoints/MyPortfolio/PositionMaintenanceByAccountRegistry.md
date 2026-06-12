# Mantenimiento de Posicion por Cuenta de Registro (PositionMaintenanceByAccountRegistry)

## Objetivo

El objetivo del endpoint es consultar cancelaciones o mantenimiento de posicion por cuenta de registro a traves de `MyPortfolio/PositionMaintenanceByAccountRegistry`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `PositionMaintenanceByAccountRegistry` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/MyPortfolio/PositionMaintenanceByAccountRegistry?dateFrom={{dateFrom}}&dateTo={{dateTo}}` |

## Roles que pueden utilizarlo

- `GET`: `Multicomitente`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde. | Obligatorio |
| `dateTo` | `string` | Fecha hasta. | Obligatorio |
| `AccountCode` | `string` | Cuenta puntual a filtrar. | Opcional |
| `pageNumber` | `int` | Numero de pagina. Si es menor a `1`, se normaliza a `1`. | Opcional |
| `pageSize` | `int` | Tamanio de pagina. Si no se informa, la BL usa `int.MaxValue`. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/MyPortfolio/PositionMaintenanceByAccountRegistry?dateFrom=20260601&dateTo=20260610&pageNumber=1&pageSize=100
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "PosMaintRptID": 1,
    "AccountCode": "10001",
    "MarketID": "ROFX"
  }
]
```

## Funcion de la BD utilizada

- Funcion observada: `[Registro].[GetReporteOperacionCarteraCanceladaPorComitente]`

## Filtros y parametros

### Observaciones tecnicas

- La BL agrega `AccountRegistry` desde los filtros del usuario.
- La salida agrupa por posicion, instrumento y trades relacionados.
- El resultado incluye `RelatedTradeGrp` con las operaciones original y de cierre.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de mantenimientos / cancelaciones. |
| `400 BadRequest` | Fecha con formato invalido | Mensaje de `FormatException`. |
| `503 ServiceUnavailable` | Error tecnico | `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `PositionMaintenanceByAccountRegistry`, alineado con controller, BL, SQL y roles observados. |
