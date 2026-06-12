# Listado de Cuentas Depositarias (DepositaryAccountList)

## Objetivo

El objetivo del endpoint es consultar las cuentas depositarias publicadas para el usuario a traves de `PosTrade/DepositaryAccountList`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DepositaryAccountList` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/DepositaryAccountList?MarketAccount={{MarketAccount}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `MarketAccount` | `boolean` | Indica si se consultan cuentas de mercado. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/DepositaryAccountList?MarketAccount=true
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "DepositaryAccountCode": "01999",
    "Description": "Cuenta depositaria principal"
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada por data layer: `[Personas].[GetReporteCuentaDepositariaPublicacion]`

## Filtros y parametros

### Observaciones tecnicas

- La BL complementa la consulta con el miembro compensador derivado del usuario autenticado.
- La llamada observada es `[Personas].[GetReporteCuentaDepositariaPublicacion](miembroCompensadorCodigo, MarketAccount)`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de cuentas depositarias. |
| `500 InternalServerError` | Error tecnico no controlado | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `DepositaryAccountList`, alineado con controller, BL, SQL y roles observados. |
