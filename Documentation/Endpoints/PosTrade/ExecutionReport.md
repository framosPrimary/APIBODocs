# Libro de Ordenes (ExecutionReport)

## Objetivo

El objetivo del metodo es consultar ordenes asociadas al cliente para un rango de fechas a traves del endpoint `GET /PosTrade/ExecutionReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ExecutionReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/ExecutionReport?dateFrom={{dateFrom}}&dateTo={{dateTo}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `MC`
- `CEL`
- `Clearing`
- `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde en formato `yyyyMMdd`. | Condicional |
| `dateTo` | `string` | Fecha hasta en formato `yyyyMMdd`. | Condicional |

Regla observada:

- si se informa una fecha, deben informarse ambas

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/ExecutionReport?dateFrom=20171210&dateTo=20171212
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "OrderID": "45001",
    "OrderType": "LIMIT",
    "ExecType": "Executed",
    "LastMkt": "ROFX",
    "Instrument": [
      {
        "Symbol": "DLR122024",
        "SecurityIDSource": "4",
        "CFICode": "FXXXXX",
        "SecurityType": "FUT"
      }
    ],
    "Account": "1234",
    "AccountType": "Client",
    "TaxID": "20-12345678-9",
    "Side": "1",
    "OrderQty": 5,
    "Price": 12450,
    "TradeDate": "2017-12-11T00:00:00",
    "TransactTime": "10:25:00",
    "Parties": [
      {
        "PartyID": "356",
        "PartyIDSource": "D",
        "PartyRole": "4"
      }
    ],
    "Currency": "ARS",
    "SettlDate": "2017-12-12T00:00:00",
    "DMA": false
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Registro].[GetReporteListadoOrdenes]`

### Invocacion SQL

```sql
SELECT *
FROM [Registro].[GetReporteListadoOrdenes] (
    @fechaDesde,
    @fechaHasta,
    @cuentaCompensacionCodigo,
    @miembroCompensadorCodigo
);
```

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@fechaDesde` | `date` | Proviene de `dateFrom`. |
| `@fechaHasta` | `date` | Proviene de `dateTo`. |
| `@cuentaCompensacionCodigo` | `varchar(max)` | Filtro del usuario autenticado. |
| `@miembroCompensadorCodigo` | `varchar(max)` | Filtro del usuario autenticado. |

### Observaciones tecnicas

- La BL agrupa la salida en `Instrument` y `Parties`.
- El resultado incluye el flag `DMA` a partir del campo `EsDMA`.
- La consulta siempre queda acotada al alcance del usuario por miembro compensador y cuenta de compensacion.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada de ordenes. |
| `400 BadRequest` | Solo se informa una de las dos fechas | `"Invalid entry. Both date From and date To are required."` |
| `400 BadRequest` | Error de parseo de fechas | Mensaje devuelto por `TryParseParameter`. |

### Observaciones adicionales

- Si ambas fechas se omiten, el controller no corta el flujo y delega el comportamiento a la capa inferior.
- Si no hay coincidencias, la API responde `200 OK` con lista vacia.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PosTrade/ExecutionReport`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
