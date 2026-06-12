# Listado de Instrumentos Derivados y FCI (DerivativeSecurityList)

## Objetivo

El objetivo del endpoint es consultar el listado publicado de instrumentos derivados y fondos comunes de inversion a traves de `PreTrade/DerivativeSecurityList`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DerivativeSecurityList` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/DerivativeSecurityList` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `AN`, `MC`, `Clearing`, `API Publico`, `EntidadBursatil`

## Parametros

El metodo no expone parametros de entrada.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PreTrade/DerivativeSecurityList
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "Product": "FCI",
    "SecurityID": "1001",
    "Symbol": "FONDO1"
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada por data layer: `[Productos].[GetReporteFondosComunesInversionPublicacion]`

## Filtros y parametros

### Observaciones tecnicas

- La BL cachea resultados y luego agrupa por instrumento.
- La consulta devuelve campos como `Product`, `SecurityID`, `Symbol`, `Issuer`, `Custodian`, `CFICode`, `Currency`, `TradeEnable`, `InvestmentType`, horarios y tipos de persona permitidos.
- `TradeEnable` se recalcula segun el miembro compensador del usuario autenticado.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de instrumentos derivados / FCI. |
| `500 InternalServerError` | Error tecnico no controlado | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `DerivativeSecurityList`, alineado con controller, BL, SQL y roles observados. |
