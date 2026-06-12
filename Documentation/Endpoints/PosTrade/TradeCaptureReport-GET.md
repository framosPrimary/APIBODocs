# Operaciones (TradeCaptureReport - GET)

## Objetivo

El objetivo del metodo es consultar operaciones a traves del endpoint `PosTrade/TradeCaptureReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `TradeCaptureReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/TradeCaptureReport?dateFrom={{dateFrom}}&dateTo={{dateTo}}&marketID={{marketID}}&marketSegmentID={{marketSegmentID}}&cFICode={{cFICode}}&trdRptStatus={{trdRptStatus}}&product={{product}}&segmentID={{segmentID}}` |

## Roles que pueden utilizarlo

- `GET`: `Multi Cel`, `MC RM`, `Comitente`, `Administrador`, `AN`, `MC`, `CEL`, `xComitente`, `Clearing`, `EntidadBursatil`, `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde en formato `yyyyMMdd`. | Condicional |
| `dateTo` | `string` | Fecha hasta en formato `yyyyMMdd`. | Condicional |
| `marketID` | `string` | Mercado. | No |
| `marketSegmentID` | `string` | Segmento de mercado. | No |
| `cFICode` | `string` | Codigo CFI. | No |
| `trdRptStatus` | `integer` | Estado de la operacion. | No |
| `product` | `string` | Producto o activo. | No |
| `segmentID` | `string` | Segmento operativo. | No |

Regla observada:

- si se informa una fecha, deben informarse ambas `dateFrom` y `dateTo`

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/TradeCaptureReport?dateFrom=20210420&dateTo=20210420&marketID=ROFX
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "TradeID": 9876,
    "TradeNumber": 654321,
    "TrdRptStatus": "0",
    "ExecID": 8463463
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Registro].[GetReporteOperacionPublicacion]`

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@dateFrom` | `date` | Proviene de `dateFrom`. |
| `@dateTo` | `date` | Proviene de `dateTo`. |
| `@marketID` | `varchar` | Proviene de `marketID`. |
| `@marketSegmentID` | `varchar` | Proviene de `marketSegmentID`. |
| `@cFICode` | `varchar` | Proviene de `cFICode`. |
| `@trdRptStatus` | `int` | Proviene de `trdRptStatus`. |
| `@product` | `varchar` | Proviene de `product`. |
| `@segmentID` | `varchar` | Proviene de `segmentID`. |
| filtros de usuario | `varchar(max)` | Cuenta registro, participante, registrante, cuenta compensacion, miembro compensador y entidad bursatil. |

### Observaciones tecnicas

- La BL agrupa la respuesta en `RootParties`, `Instrument` y `TrdCapRptSideGrp`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de operaciones. |
| `400 BadRequest` | Solo se informa una de las dos fechas | `"Invalid entry. Both dateFrom and dateTo are required."` |
| `400 BadRequest` | Error de parseo de fechas | Mensaje devuelto por `TryParseParameter`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `GET` para `TradeCaptureReport`, alineado con controller, BL, roles y funciones vigentes. |
