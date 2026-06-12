# Cabecera de Historial de Movimientos (HistoryRegistersHeader)

## Objetivo

El objetivo del endpoint es consultar la cabecera o cantidad resumida de movimientos historicos por comitente a traves de `MyPortfolio/HistoryRegistersHeader`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `HistoryRegistersHeader` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/MyPortfolio/HistoryRegistersHeader?dateFrom={{dateFrom}}&dateTo={{dateTo}}` |

## Roles que pueden utilizarlo

- `GET`: `Multicomitente`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde. | Obligatorio |
| `dateTo` | `string` | Fecha hasta. | Obligatorio |
| `originType` | `int` | Origen de datos. Valor por defecto `1`. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/MyPortfolio/HistoryRegistersHeader?dateFrom=20260601&dateTo=20260610&originType=1
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
{
  "TotalRecords": 25
}
```

## Funcion de la BD utilizada

- Fuente local observada: `[Registro].[GetReporteCantidadMovimientosPorComitente]`
- Fuente Azure/Kattegat observada: `KattegatHistoryRegistersHeader`

## Filtros y parametros

### Observaciones tecnicas

- `originType = 1` consulta base local.
- `originType = 2` consulta Kattegat.
- El endpoint resume la cantidad o cabecera del historial y se usa como complemento de `HistoryRegisters`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Cabecera o total de movimientos. |
| `400 BadRequest` | Faltan `dateFrom` o `dateTo` | `"Date from and date to cannot be null"` |
| `400 BadRequest` | Fecha invalida | Mensaje de parseo generado por `TryParseParameter`. |
| `500 InternalServerError` | Error tecnico no controlado | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `HistoryRegistersHeader`, alineado con controller, BL, SQL y roles observados. |
