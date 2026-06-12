# Creditos por Accion Corporativa (CorporateActionCredits)

## Objetivo

El objetivo del endpoint es consultar los creditos por acciones corporativas a traves de `PosTrade/CorporateActionCredits`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CorporateActionCredits` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/CorporateActionCredits?dateFrom={{dateFrom}}&dateTo={{dateTo}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `AN`, `MC`, `CEL`, `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde en formato `yyyyMMdd`. | Obligatorio |
| `dateTo` | `string` | Fecha hasta en formato `yyyyMMdd`. | Obligatorio |
| `corporateActionType` | `string` | Tipo de accion corporativa. | Opcional |
| `instrumentCode` | `string` | Codigo del instrumento. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/CorporateActionCredits?dateFrom=20260601&dateTo=20260610&corporateActionType=DVCA&instrumentCode=AL30
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "InstrumentCode": "AL30",
    "CorporateActionType": "DVCA",
    "Amount": 1500.25
  }
]
```

## Funcion de la BD utilizada

- Stored procedure / funcion observada: `[Registro].[GetReporteCreditosAccionCorporativa]`

## Filtros y parametros

### Observaciones tecnicas

- La BL deriva filtros de miembro compensador y cuenta de compensacion desde el usuario autenticado.
- El acceso a datos invoca `[Registro].[GetReporteCreditosAccionCorporativa] (@fechaDesde, @fechaHasta, @tipoEventoCorporativo, @activoCodigo, @miembroCompensadorCodigo, @cuentaCompensacionCodigo)`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de creditos por accion corporativa. |
| `400 BadRequest` | Formato de fecha invalido | `"Parameter malformed. Date format: (YYYYMMDD)"` |
| `503 ServiceUnavailable` | Error SQL controlado | `"The operation could not be performed."` |
| `500 InternalServerError` | Error tecnico no controlado | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `CorporateActionCredits`, alineado con controller, BL, SQL y roles observados. |
