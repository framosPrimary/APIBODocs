# Ordenes Ejecutadas (FilledOrders)

## Objetivo

El objetivo del metodo es consultar ordenes ejecutadas de suscripcion o rescate a traves del endpoint `GET /Trade/FilledOrders`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `FilledOrders` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Trade/FilledOrders` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `MC`

## Parametros

Este metodo no requiere parametros publicos.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Trade/FilledOrders
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "ClOrdID": "151516",
    "MarketID": "FCI",
    "OrderQty": 20.5645,
    "Price": 1,
    "Currency": "ARS",
    "OrderType": "M",
    "Status": 2,
    "TransactTime": "16:40:11",
    "Username": "usuario.demo",
    "Reference": 7,
    "OrderSideGrp": [
      {
        "Account": "999",
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

- Funcion utilizada: `[Contribucion].[GetFilledOrders]`

### Invocacion SQL

```sql
SELECT *
FROM [Contribucion].[GetFilledOrders] (
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
- La estructura de salida es la misma que `ActiveOrders`, cambiando la fuente SQL y el estado de las ordenes.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada de ordenes ejecutadas. |

### Observaciones adicionales

- El controller no tiene validaciones adicionales porque no recibe parametros.
- Si no hay coincidencias, la API responde `200 OK` con lista vacia.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /Trade/FilledOrders`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
