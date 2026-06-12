# Consulta de Orden por Identificador (OrderByID)

## Objetivo

El objetivo del metodo es consultar una orden de suscripcion o rescate mediante su identificador a traves del endpoint `GET /Trade/OrderById`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `OrderByID` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Trade/OrderById?ClOrdID={{ClOrdID}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `ClOrdID` | `string` | Identificador de la orden a consultar. Debe poder parsearse como entero de 64 bits. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Trade/OrderById?ClOrdID=12373
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "ClOrdID": "12373",
    "MarketID": "ROFX",
    "OrderQty": 20,
    "Price": 374.4,
    "Currency": "USD",
    "OrderType": "M",
    "Status": 1,
    "TransactTime": "15:13:25",
    "OrderSideGrp": [
      {
        "Account": "3530",
        "Side": "D"
      }
    ],
    "Instrument": [
      {
        "Symbol": "Fondo 1 Clase B"
      }
    ]
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Contribucion].[GetOrder]`

### Invocacion SQL

```sql
SELECT ClOrdID, MarketID, Status, OrderQty, Price, TransactTime, Symbol, OrderType, Currency, Account, Side, ExecId
FROM [Contribucion].[GetOrder] (
    @clOrdId,
    @miembroCompensadorCodigo
);
```

### Tabla de salida relevante

| Columna fn / DTO | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `ClOrdID` | `ClOrdID` | `varchar` | Identificador de la orden. |
| `MarketID` | `MarketID` | `varchar` | Mercado. |
| `Status` | `Status` | `byte` | Estado de contribucion/orden. |
| `OrderQty` | `OrderQty` | `decimal` | Cantidad. |
| `Price` | `Price` | `decimal` | Precio. |
| `TransactTime` | `TransactTime` | `varchar` | Hora de transaccion. |
| `Symbol` | `Instrument[].Symbol` | `varchar` | La BL lo agrupa dentro de `Instrument`. |
| `Account` | `OrderSideGrp[].Account` | `varchar` | La BL lo agrupa dentro de `OrderSideGrp`. |
| `Side` | `OrderSideGrp[].Side` | `varchar` | La BL lo agrupa dentro de `OrderSideGrp`. |
| `ExecId` | no expuesto en agrupacion actual | `bigint` | El DTO lo recibe, pero no queda en la salida agrupada vigente. |

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@clOrdId` | `varchar(max)` | Proviene de `ClOrdID` en el request. |
| `@miembroCompensadorCodigo` | `varchar(max)` | Proviene de los filtros del usuario autenticado. |

### Observaciones tecnicas

- El controller valida que `ClOrdID` pueda parsearse como `Int64` antes de consultar.
- La business logic agrega el filtro del miembro compensador del usuario autenticado.
- La funcion SQL filtra por `FechaOrden = GETDATE()`, por lo que la consulta esta acotada a ordenes del dia actual.
- La BL agrupa la salida plana en una estructura con `OrderSideGrp` e `Instrument`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada de ordenes. |
| `400 BadRequest` | `ClOrdID` no es numerico valido | Mensaje de parseo generado por `TryParseParameter`. |

### Observaciones adicionales

- Si no hay coincidencias, la API responde `200 OK` con lista vacia.
- La consulta solo releva ordenes del dia actual porque la funcion SQL filtra `FechaOrden = CONVERT(DATE, GETDATE())`.
- El contrato historico muestra `Status` como texto; en la implementacion vigente el agrupador devuelve el valor numerico recibido por DTO.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /Trade/OrderById`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
