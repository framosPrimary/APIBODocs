# Stock de Billetera Digital (DigitalWalletStock)

## Objetivo

El objetivo del endpoint es consultar el stock o detalle agrupado de billetera digital a traves de `PosTrade/DigitalWalletStock`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DigitalWalletStock` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/DigitalWalletStock?date={{date}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `MC`, `Clearing`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha en formato `yyyyMMdd`. | Opcional |
| `viewDetails` | `boolean` | Si es `true`, devuelve detalle; si es `false`, resumen por referencia. | Opcional |
| `referenceCode` | `string` | Referencia requerida cuando `viewDetails = false`. | Condicional |
| `accountCode` | `string` | Cuenta a consultar. | Opcional |
| `pageNumber` | `int` | Pagina solicitada. | Opcional |
| `pageSize` | `int` | Cantidad de registros por pagina. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/DigitalWalletStock?date=20260610&viewDetails=true&pageNumber=1&pageSize=50
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "ReferenceCode": "REF001",
    "InstrumentCode": "AL30",
    "AvailableQty": 1000
  }
]
```

## Funcion de la BD utilizada

- Implementacion actual relevada con integracion Kattegat / Azure para stock de billetera digital.

## Filtros y parametros

### Observaciones tecnicas

- Si `date` no se informa, el endpoint consulta con la fecha actual.
- Si `viewDetails = false`, el controller exige `referenceCode`.
- La implementacion actual tiene trazas de rollback historico desde AP5, pero el flujo activo responde a la integracion vigente.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Stock o detalle de billetera digital. |
| `400 BadRequest` | `viewDetails = false` sin `referenceCode` | `"Reference Code is required"` |
| `400 BadRequest` | Fecha invalida | `"Parameter 'Date' malformed. (YYYYMMDD)"` |
| `500 InternalServerError` | Error SQL o tecnico | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `DigitalWalletStock`, alineado con controller, BL e integracion vigente observada. |
