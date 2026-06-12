# Alta Masiva de Reportes de Operacion (TradeCaptureReportList)

## Objetivo

El objetivo del endpoint es registrar una lista de reportes de operacion a traves de `PosTrade/TradeCaptureReportList`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `TradeCaptureReportList` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PosTrade/TradeCaptureReportList` |

## Roles que pueden utilizarlo

- `POST`: `MC RM`, `MC`, `Clearing`

## Parametros

El metodo acepta un `body` JSON con un arreglo de objetos.

Observaciones relevadas:

- el body debe ser un `JArray`
- el arreglo no puede venir vacio
- cada elemento se valida individualmente y puede devolver error propio

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PosTrade/TradeCaptureReportList
Authorization: Bearer {{token}}
Content-Type: application/json

[
  {
    "ExecID": 1001,
    "SecondaryExecID": "A1",
    "MarketID": "ROFX"
  }
]
```

## Funcion de la BD utilizada

- Persistencia observada: `[Contribucion].[InsertReporteOperacion]`
- Cola de integracion observada: `ClearingInterface.ContribucionReporteOperacion`

## Filtros y parametros

### Observaciones tecnicas

- La BL agrega `User` al JSON de cada operacion.
- Tambien valida compatibilidad de `MarketID` con el filtro de entidad bursatil del usuario.
- Si un item falla, se agrega a una lista de errores con `ExecID` y `SecondaryExecID`.
- Si no hay errores de usuario, la API devuelve `"Created"`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta total sin errores | `"Created"` |
| `200 OK` | Alta parcial con errores por item | Lista de `InsertTradeCaptureReportList_OutDTO`. |
| `400 BadRequest` | Body nulo o formato invalido | `"Json format invalid"` |
| `400 BadRequest` | Arreglo vacio | `"Json object must contain at least one element"` |
| `400 BadRequest` | Error funcional en algun item | Lista de errores o mensaje. |
| `503 ServiceUnavailable` | Error tecnico | `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `TradeCaptureReportList`, alineado con controller, BL, persistencia y roles observados. |
