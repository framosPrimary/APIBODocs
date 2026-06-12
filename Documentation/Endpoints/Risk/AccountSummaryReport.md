# Resumen de Cuenta (AccountSummaryReport)

## Objetivo

El objetivo del endpoint es consultar resumenes de diferencia, margenes o escenarios por cuenta a traves de `GET /Risk/AccountSummaryReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccountSummaryReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Risk/AccountSummaryReport?marketID={{marketID}}&reportType={{reportType}}&currencyDetails={{currencyDetails}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `AN`
- `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `marketID` | `string` | Identificador de mercado. | Obligatorio |
| `reportType` | `string` | Tipo de reporte: `1`, `2` o `3`. | Obligatorio |
| `currencyDetails` | `boolean` | Solo aplica a `reportType = 1`. | Condicional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Risk/AccountSummaryReport?marketID=1&reportType=1&currencyDetails=false
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

Este endpoint no usa una funcion SQL de negocio del repo. Consume `Winterfell.API.Rest`.

### Endpoints externos observados

- `reportType = 1`: `/api/drp`
- `reportType = 2`: `/api/margenes`
- `reportType = 3`: `/api/carteraescenario`

## Filtros y parametros

### Observaciones tecnicas

- La BL arma el endpoint externo en base a `marketID` y a los filtros del usuario autenticado por MC y/o CEL.
- Para `reportType = 1`, excluye finalidades `3`, `4` y `24`.
- Para `reportType = 1`, si `currencyDetails=false`, consolida por moneda de mercado; si `true`, segrega por moneda real.
- Para `reportType = 2`, devuelve margenes e importes por escenario/producto.
- Para `reportType = 3`, devuelve cartera por escenario con `Scenario1..Scenario16`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada segun `reportType`. |
| `400 BadRequest` | `currencyDetails` ausente con `reportType = 1` | `"CurrencyDetails is required when reportType is '1'."` |
| `500 InternalServerError` | Error con `SqlException` en el mensaje | `"Internal Server Error"` |
| `400 BadRequest` | Otros errores | Mensaje de excepcion. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /Risk/AccountSummaryReport`, alineado con controller, business logic, roles y consumo externo vigente. |
