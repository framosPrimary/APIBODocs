# Ordenes Activas (ActiveOrders)

## Objetivo

El objetivo del metodo es consultar ordenes activas de suscripcion o rescate a traves del endpoint `GET /Trade/ActiveOrders`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ActiveOrders` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Trade/ActiveOrders` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `MC`

## Parametros

Este metodo no requiere parametros publicos.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Trade/ActiveOrders
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "ClOrdID": "151515",
    "MarketID": "FCI",
    "OrderQty": 20.5645,
    "Price": 1,
    "Currency": "ARS",
    "OrderType": "C",
    "Status": 1,
    "TransactTime": "15:13:25",
    "Username": "usuario.demo",
    "Reference": 2,
    "OrderSideGrp": [
      {
        "Account": "3530",
        "Side": "E"
      }
    ],
    "Instrument": [
      {
        "Symbol": "Fondo 2 Clase A"
      }
    ]
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Contribucion].[GetActiveOrders]`

### Invocacion SQL

```sql
SELECT *
FROM [Contribucion].[GetActiveOrders] (
    @fecha,
    @miembroCompensadorCodigo,
    @cuentaCompensacionCodigo
);
```

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@fecha` | `date` | La BL usa `DateTime.Now.Date`. |
| `@miembroCompensadorCodigo` | `varchar(max)` | Filtro del usuario autenticado. |
| `@cuentaCompensacionCodigo` | `varchar(max)` | Filtro del usuario autenticado. |

### Observaciones tecnicas

- El endpoint siempre consulta ordenes del dia actual.
- La BL agrupa la salida en `OrderSideGrp` e `Instrument`.
- La salida agrega `Username` y `Reference`, que no estan presentes en `OrderByID`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada de ordenes activas. |

### Observaciones adicionales

- El controller no tiene validaciones adicionales porque no recibe parametros.
- Si no hay coincidencias, la API responde `200 OK` con lista vacia.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /Trade/ActiveOrders`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
