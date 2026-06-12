# Historial de Movimientos por Comitente (HistoryRegisters)

## Objetivo

El objetivo del endpoint es consultar el historial detallado de movimientos por comitente a traves de `MyPortfolio/HistoryRegisters`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `HistoryRegisters` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/MyPortfolio/HistoryRegisters?dateFrom={{dateFrom}}&dateTo={{dateTo}}&account={{account}}` |

## Roles que pueden utilizarlo

- `GET`: `Multicomitente`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde. | Obligatorio |
| `dateTo` | `string` | Fecha hasta. | Obligatorio |
| `account` | `string` | Cuenta del comitente. | Obligatorio |
| `pageNumber` | `int` | Numero de pagina. Si vale `0`, consulta sin paginar. | Opcional |
| `originType` | `int` | Origen de datos. Valor por defecto `1`. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/MyPortfolio/HistoryRegisters?dateFrom=20260601&dateTo=20260610&account=10001&pageNumber=1&originType=1
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "Date": "20260610",
    "Account": "10001",
    "Description": "Movimiento de ejemplo"
  }
]
```

## Funcion de la BD utilizada

- Fuente local observada:
  - `[Registro].[GetReporteMovimientosPorComitente]`
  - `[Registro].[GetReporteMovimientosPorComitenteSinPaginar]`
- Fuente Azure/Kattegat observada:
  - `KattegatHistoryRegisters`

## Filtros y parametros

### Observaciones tecnicas

- `originType = 1` consulta origen local.
- `originType = 2` consulta Kattegat.
- Si `pageNumber = 0`, la BL usa la variante sin paginacion.
- La API rechaza rangos mayores a 90 dias cuando `pageNumber = 0`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Historial de movimientos. |
| `400 BadRequest` | Falta `account` | `"Account cannot be null"` |
| `400 BadRequest` | Faltan `dateFrom` o `dateTo` | `"Date from and date to cannot be null"` |
| `400 BadRequest` | Rango mayor a 90 dias sin paginacion | `"The range of dates indicated exceeds 90 days"` |
| `400 BadRequest` | Fecha invalida | Mensaje de parseo generado por `TryParseParameter`. |
| `500 InternalServerError` | Error tecnico no controlado | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `HistoryRegisters`, alineado con controller, BL, SQL y roles observados. |
