# Reporte de Margen de Entrega (DeliveryMarginRequirementReport)

## Objetivo

El objetivo del endpoint es consultar el reporte de margen de entrega a traves de `PosTrade/DeliveryMarginRequirementReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DeliveryMarginRequirementReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/DeliveryMarginRequirementReport?date={{date}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `Administrador`, `AN`, `MC`, `Clearing`, `EntidadBursatil`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha de consulta. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/DeliveryMarginRequirementReport?date=20260610
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "AccountCode": "10001",
    "Amount": 250000.00
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada por data layer: `[Contribucion].[GetReporteMargenEntrega]`

## Filtros y parametros

### Observaciones tecnicas

- La BL deriva filtros de miembro compensador, cuenta de compensacion y cuenta de registro desde `NoPartyIDs`.
- La capa de datos invoca `GetReporteMargenEntrega(fecha, miembroCompensadorCodigo, cuentaCompensacionCodigo, cuentaRegistroCodigo)`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Reporte de margen de entrega. |
| `400 BadRequest` | Falta `date` | `"Invalid entry. Date is required."` |
| `400 BadRequest` | Fecha invalida | Mensaje de parseo generado por `TryParseParameter`. |
| `500 InternalServerError` | Error tecnico no controlado | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `DeliveryMarginRequirementReport`, alineado con controller, BL, SQL y roles observados. |
