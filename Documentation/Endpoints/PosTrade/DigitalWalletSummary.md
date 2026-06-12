# Resumen de Billetera Digital (DigitalWalletSummary)

## Objetivo

El objetivo del endpoint es consultar el resumen de movimientos de billetera digital a traves de `PosTrade/DigitalWalletSummary`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DigitalWalletSummary` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/DigitalWalletSummary?date={{date}}&reference={{reference}}&instrumentCode={{instrumentCode}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha en formato `yyyyMMdd`. | Obligatorio |
| `reference` | `string` | Referencia agrupadora del movimiento. | Obligatorio |
| `instrumentCode` | `string` | Codigo del instrumento. | Obligatorio |
| `viewEmbargo` | `boolean` | Indica si se muestran registros embargados. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/DigitalWalletSummary?date=20260610&reference=REF001&instrumentCode=AL30
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "Reference": "REF001",
    "RegistTransType": 1,
    "Quantity": 100
  }
]
```

## Funcion de la BD utilizada

- Fuente local observada: `OwnData.GetMovimientosResumen(...)`
- Fuente Azure/Kattegat observada: `KattegatDigitalWalletSummary`

## Filtros y parametros

### Observaciones tecnicas

- El origen efectivo depende de `DestinationType`.
- En el flujo local se agrupan tipos `RegistTransType` `1` y `2`.
- En el flujo Azure tambien aparecen buckets de cancelacion con tipos `3` y `4`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Resumen de movimientos de billetera digital. |
| `400 BadRequest` | Faltan parametros obligatorios o fecha invalida | Mensaje detallado de validacion. |
| `500 InternalServerError` | Error SQL o tecnico | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `DigitalWalletSummary`, alineado con controller, BL, origenes de datos y roles observados. |
