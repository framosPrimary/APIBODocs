# Comprobantes (Invoices)

## Objetivo

El objetivo del endpoint es consultar comprobantes, facturas, liquidaciones, retenciones y notas de credito a traves de `PosTrade/Invoices`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `Invoices` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/Invoices?documentTypeId={{documentTypeId}}&dateFrom={{dateFrom}}&dateTo={{dateTo}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `Administrador`, `AN`, `MC`, `Clearing`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `documentTypeId` | `byte` | Tipo de comprobante a consultar. | Obligatorio |
| `dateFrom` | `string` | Fecha desde. | Obligatorio |
| `dateTo` | `string` | Fecha hasta. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/Invoices?documentTypeId=1&dateFrom=20260601&dateTo=20260610
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "Header": {
      "DocumentType": "Factura",
      "CompleteNumber": "0001-00001234",
      "TotalAmt": 1500.25
    },
    "Details": [
      {
        "AccountCode": "10001",
        "Description": "Concepto de ejemplo"
      }
    ]
  }
]
```

## Funcion de la BD utilizada

- Para comprobantes generales, la BL usa `GetReporteComprobantePublicacionAsyncV2(...)`.
- Para `documentTypeId = 7`, la BL usa `GetReporteRetencionPublicacionAsyncV2(...)`.

## Filtros y parametros

### Observaciones tecnicas

- La BL deriva miembro compensador y cuenta de compensacion desde los filtros del usuario.
- Si `documentTypeId = 7`, el flujo cambia a retenciones.
- Para comprobantes generales, la respuesta se agrupa en `Header` y `Details`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de comprobantes o retenciones. |
| `400 BadRequest` | Faltan `dateFrom` o `dateTo` | `"Invalid entry. Both dateFrom and dateTo are required."` |
| `400 BadRequest` | Parametros con formato invalido | Mensaje de parseo generado por `TryParseParameter`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `Invoices`, alineado con controller, BL, comportamiento actual y roles observados. |
