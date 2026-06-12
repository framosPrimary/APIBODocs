# Detalle de Billetera Digital (DigitalWalletDetails)

## Objetivo

El objetivo del endpoint es consultar el detalle de movimientos de billetera digital a traves de `PosTrade/DigitalWalletDetails`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DigitalWalletDetails` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/DigitalWalletDetails?date={{date}}&reference={{reference}}&instrumentCode={{instrumentCode}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha en formato `yyyyMMdd`. | Obligatorio |
| `reference` | `string` | Referencia agrupadora del movimiento. | Obligatorio |
| `instrumentCode` | `string` | Codigo del instrumento. | Obligatorio |
| `viewEmbargo` | `boolean` | Indica si se incluyen registros embargados. | Opcional |
| `viewErrors` | `boolean` | Indica si se consultan errores / no procesados. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/DigitalWalletDetails?date=20260610&reference=REF001&instrumentCode=AL30&viewEmbargo=false&viewErrors=false
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "Reference": "REF001",
    "InstrumentCode": "AL30",
    "Quantity": 100
  }
]
```

## Funcion de la BD utilizada

- Fuente local observada: `[Registro].[GetMovimientosDetalle]`
- Fuente Azure/Kattegat observada:
  - `KattegatDigitalWalletDetails`
  - `KattegatDigitalWalletDetailsErrors`

## Filtros y parametros

### Observaciones tecnicas

- El comportamiento depende de `DestinationType`.
- Si `viewErrors = true` y `DestinationType = 0`, la BL usa `GetMovimientosDetalleNoProcesadosGroup`.
- Si `DestinationType = 1`, la consulta se resuelve contra Kattegat Azure.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Detalle de movimientos de billetera digital. |
| `400 BadRequest` | Faltan parametros obligatorios o fecha invalida | Mensaje detallado de validacion. |
| `500 InternalServerError` | Error SQL o tecnico | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `DigitalWalletDetails`, alineado con controller, BL, origenes de datos y roles observados. |
