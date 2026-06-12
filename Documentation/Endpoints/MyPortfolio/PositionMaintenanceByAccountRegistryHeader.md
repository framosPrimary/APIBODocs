# Cabecera de Mantenimiento de Posicion (PositionMaintenanceByAccountRegistryHeader)

## Objetivo

El objetivo del endpoint es consultar el resumen cabecera de cancelaciones por cuenta de registro a traves de `MyPortfolio/PositionMaintenanceByAccountRegistryHeader`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `PositionMaintenanceByAccountRegistryHeader` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/MyPortfolio/PositionMaintenanceByAccountRegistryHeader?dateFrom={{dateFrom}}&dateTo={{dateTo}}` |

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
GET {{url}}/MyPortfolio/PositionMaintenanceByAccountRegistryHeader?dateFrom=20260601&dateTo=20260610
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "AccountCode": "10001",
    "CancellationsNumber": 3
  }
]
```

## Funcion de la BD utilizada

- Funcion observada: `[Registro].[GetReporteOperacionCarteraCanceladaPorComitenteResumen]`

## Filtros y parametros

### Observaciones tecnicas

- La BL agrega `AccountRegistry` desde los filtros del usuario.
- Este endpoint resume por cuenta la cantidad de cancelaciones dentro del rango consultado.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Cabecera resumen de cancelaciones. |
| `400 BadRequest` | Fecha con formato invalido | Mensaje de `FormatException`. |
| `503 ServiceUnavailable` | Error tecnico | `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `PositionMaintenanceByAccountRegistryHeader`, alineado con controller, BL, SQL y roles observados. |
