# Reporte de Posicion de Riesgo (RiskPositionReport)

## Objetivo

El objetivo del endpoint es consultar el reporte de posicion de riesgo a traves de `Risk/RiskPositionReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `RiskPositionReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Risk/RiskPositionReport?reportType={{reportType}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `reportType` | `string` | Tipo de reporte. La BL observada implementa explicitamente el flujo para `1`. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Risk/RiskPositionReport?reportType=1
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

- El endpoint no usa SQL local directo para el reporte principal.
- La BL consume `Winterfell.API.Rest`.
- Recurso observado para `reportType = 1`: `/api/cartera`

## Filtros y parametros

### Observaciones tecnicas

- La BL deriva `MCCods` y `CELCods` desde los filtros del usuario.
- El resultado se deserializa a `RiskPositionReportResponse`.
- Luego se excluyen registros con `InstrumentTypeID = 10`.
- En el codigo relevado, el armado explicito del endpoint esta contemplado para `reportType = 1`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de posicion de riesgo. |
| `400 BadRequest` | Error de negocio o parametro invalido | Mensaje de error. |
| `500 InternalServerError` | Error SQL o tecnico detectado como `SqlException` | `"Internal Server Error"` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `RiskPositionReport`, alineado con controller, BL y roles observados. |
