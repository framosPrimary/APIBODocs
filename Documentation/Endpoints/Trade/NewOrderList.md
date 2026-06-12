# Alta de Ordenes por Lote (NewOrderList)

## Objetivo

El objetivo del metodo es registrar una o varias ordenes de suscripcion o rescate a traves del endpoint `POST /Trade/NewOrderList`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `NewOrderList` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/Trade/NewOrderList?allowPartialProcessing={{allowPartialProcessing}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `MC`

## Parametros

Este metodo recibe un `body` JSON con un arreglo de ordenes.

### Query string

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `allowPartialProcessing` | `boolean` | Si vale `true`, intenta insertar las ordenes validas y devolver el detalle de errores de las invalidas. Default: `false`. | No |

### Body por orden

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `MarketID` | `string` | Identificador del mercado. La documentacion historica usa `FCI`. | Obligatorio |
| `ClOrdID` | `string` | Identificador externo de la orden. | No |
| `Instrument[].Symbol` | `string` | Ticker o descripcion del instrumento o FCI. | Obligatorio |
| `OrderQty` | `number` | Cantidad de cuotapartes. Se usa para rescates por cantidad. | Condicional |
| `CashOrderQty` | `number` | Monto. Se usa para suscripciones y rescates por monto. | Condicional |
| `Currency` | `string` | Moneda de la orden. | Obligatorio |
| `OrderType` | `string` | Tipo de orden. La logica vigente distingue especialmente `M` y `C`. | Obligatorio |
| `OrderSideGrp[].Side` | `string` | Tipo de solicitud. La documentacion historica usa `D` para suscripcion y `E` para rescate. | Obligatorio |
| `OrderSideGrp[].Account` | `string` | Cuenta sobre la cual se cursa la orden. | Obligatorio |
| `CreditsInCollClearingAccount` | `integer` | Cuenta de acreditacion destino para rescates. Solo aplica para `Side = E`. | No |
| `Reference` | `string` | Finalidad de la operacion. | No |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Enviar en un mismo lote una orden de rescate y una de suscripcion.

```http
POST {{url}}/Trade/NewOrderList?allowPartialProcessing=false
Authorization: Bearer {{token}}
Content-Type: application/json

[
  {
    "MarketID": "FCI",
    "Instrument": [
      {
        "Symbol": "Fondo 2 Clase A"
      }
    ],
    "OrderQty": 20.564568768,
    "Currency": "ARS",
    "CreditsInCollClearingAccount": 2,
    "ClOrdID": "151515",
    "OrderType": "C",
    "OrderSideGrp": [
      {
        "Side": "E",
        "Account": "3530"
      }
    ]
  },
  {
    "MarketID": "FCI",
    "Instrument": [
      {
        "Symbol": "Fondo 1 Clase B"
      }
    ],
    "CashOrderQty": 20.564568768,
    "ClOrdID": "151516",
    "Currency": "ARS",
    "OrderType": "M",
    "OrderSideGrp": [
      {
        "Side": "D",
        "Account": "999"
      }
    ]
  }
]
```

### Ejemplo de respuesta

Con `allowPartialProcessing=false`:

```json
"OK"
```

Con `allowPartialProcessing=true` y sin errores:

```json
{
  "HasErrors": false,
  "TotalOrders": 2,
  "OrdersInserted": 2,
  "OrdersWithErrors": 0,
  "InsertedOrderIdentifiers": [
    "151515",
    "151516"
  ]
}
```

Con `allowPartialProcessing=true` y con errores parciales:

```json
{
  "HasErrors": true,
  "TotalOrders": 2,
  "OrdersInserted": 1,
  "OrdersWithErrors": 1,
  "InsertedOrderIdentifiers": [
    "151516"
  ],
  "ErrorList": [
    {
      "OrderIdentifier": "151515",
      "ErrorCode": 460,
      "Message": "One or more orders are duplicated"
    }
  ]
}
```

## Funcion de la BD utilizada

- Stored procedure utilizada en la implementacion vigente: `[Contribucion].[InsertNewOrderList]`

### Invocacion SQL

```sql
EXEC [Contribucion].[InsertNewOrderList]
    @NewOrders = @OrdersJson;
```

### Tabla de salida relevante

| Columna SP / DTO | Campo tecnico | Tipo dato | Observaciones |
|---|---|---|---|
| `OrdenID` | `InsertNewOrderListOutDTO.OrdenID` | `int` | Identificador interno de la orden insertada. |
| `IdentificadorOrden` | `InsertNewOrderListOutDTO.IdentificadorOrden` | `varchar` | Identificador externo. |
| `MensajeError` | `InsertNewOrderListOutDTO.MensajeError` | `varchar` | Se usa para construir errores por orden en procesamiento parcial o total. |

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@NewOrders` | `varchar(max)` | JSON compuesto por la API con `Ordenes`, `Partys`, `User` y `AllowPartialProcessing`. |
| `Partys` | `json array` | Lo completa la business logic con filtros del usuario autenticado. |
| `User` | `string` | Lo completa la business logic con el usuario autenticado. |
| `AllowPartialProcessing` | `bit` | Proviene del query string. |

### Observaciones tecnicas

- La business logic valida dia habil y que todas las ordenes del lote pertenezcan al mismo mercado.
- Para `Side = D`, fuerza `CreditsInCollClearingAccount = null`.
- Cuando `allowPartialProcessing = false`, cualquier error del lote provoca rechazo integral.
- Cuando `allowPartialProcessing = true`, se eliminan del lote las ordenes con error y se insertan solo las validas.
- La procedure valida reglas de cuentas activas, finalidad, horario de mercado, stock disponible, tipo de orden y combinacion correcta entre `OrderQty` y `CashOrderQty`.
- En ambos casos las ordenes exitosas se envian luego a la cola `ClearingInterface.ContribucionOrdenes`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Insercion exitosa sin procesamiento parcial | `"OK"` |
| `200 OK` | Insercion exitosa con `allowPartialProcessing=true` y sin errores | Resumen del lote insertado. |
| `206 PartialContent` | `allowPartialProcessing=true` y algunas ordenes fallan | Resumen con `HasErrors=true` y `ErrorList`. |
| `400 BadRequest` | Body nulo o JSON invalido | `"Json format invalid"` |
| `460` a `476` | Error de negocio mapeado por orden | Mismo mapeo funcional que `NewOrderSingle`. |
| `477` | Ninguna orden fue insertada | `"No orders inserted."` |
| `500` | Error interno general | `"Internal Server Error"` |

### Observaciones adicionales

- El controller mapea varios mensajes de negocio a codigos HTTP no estandarizados `460` a `477`.
- Entre los errores relevados en el codigo/SQL aparecen: orden duplicada, referencia invalida, falta de stock, cantidad invalida, rango invalido, fuera de horario, FCI no habilitado, lado `D` por importe, falta de CBU y combinacion invalida de `OrderQty`/`CashOrderQty`.
- Si `allowPartialProcessing=true` y ninguna orden se inserta, la respuesta usa el tratamiento especial del error `477`.
- El contrato historico se concentra en el body de ordenes; el parametro `allowPartialProcessing` es una capacidad observada en el codigo vigente.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `POST /Trade/NewOrderList`, alineado con controller, business logic, roles relevados y stored procedure vigente. |
