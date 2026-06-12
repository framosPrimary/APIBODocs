# Alta de Orden Individual (NewOrderSingle)

## Objetivo

El objetivo del metodo es registrar una orden individual de suscripcion o rescate a traves del endpoint `POST /Trade/NewOrderSingle`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `NewOrderSingle` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/Trade/NewOrderSingle` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `MC`

## Parametros

Este metodo recibe un `body` JSON.

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `MarketID` | `string` | Identificador del mercado. La documentacion historica usa `FCI`. | Obligatorio |
| `ClOrdID` | `integer` | Identificador externo de la orden. | No |
| `Instrument` | `array` | Lista de instrumentos. En la practica se utiliza el primer elemento con `Symbol`. | Obligatorio |
| `Instrument[].Symbol` | `string` | Ticker o descripcion del instrumento o FCI. | Obligatorio |
| `OrderQty` | `number` | Cantidad de cuotapartes. Se usa para rescates por cantidad. | Condicional |
| `CashOrderQty` | `number` | Monto. Se usa para suscripciones y rescates por monto. | Condicional |
| `Currency` | `string` | Moneda de la orden. | Obligatorio |
| `OrderType` | `string` | Tipo de orden. La logica vigente distingue especialmente `M` y `C`. | Obligatorio |
| `OrderSideGrp` | `array` | Lista de lados. En la practica se utiliza el primer elemento. | Obligatorio |
| `OrderSideGrp[].Side` | `string` | Tipo de solicitud. La documentacion historica usa `D` para suscripcion y `E` para rescate. | Obligatorio |
| `OrderSideGrp[].Account` | `string` | Cuenta sobre la cual se cursa la orden. | Obligatorio |
| `CreditsInCollClearingAccount` | `integer` | Cuenta de acreditacion destino para rescates. Solo aplica para `Side = E`. | No |
| `Reference` | `string` | Finalidad de la operacion. Ejemplo historico: `Tenencia`. | No |

### Campos internos completados por la API

Los siguientes datos no necesitan enviarse en el body porque la API los completa internamente:

- `User` con el usuario autenticado.
- `RootParties` a partir de los filtros del usuario autenticado.
- `OrderStatus = "0"` para alta de orden.

## Ejemplo

### Ejemplo de request

**Objetivo**  
Enviar una orden individual de suscripcion por monto.

```http
POST {{url}}/Trade/NewOrderSingle
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "MarketID": "FCI",
  "ClOrdID": 1007,
  "Instrument": [
    {
      "Symbol": "F1 - Clase A"
    }
  ],
  "CashOrderQty": 1000,
  "Currency": "ARS",
  "Reference": "Tenencia",
  "OrderType": "M",
  "OrderSideGrp": [
    {
      "Side": "D",
      "Account": "83471"
    }
  ]
}
```

### Ejemplo de respuesta

```json
"OK"
```

## Funcion de la BD utilizada

- Stored procedure utilizado en la implementacion vigente: `[Contribucion].[InsertNewOrderList]`

### Regla de uso

- Aunque el endpoint se llama `NewOrderSingle`, el controller no usa la ruta individual historica.
- El controller envuelve el JSON recibido dentro de un `JArray` de un solo elemento.
- Luego llama a `InsertOrderService.InsertNewOrderList(array, allowPartialProcessing: false)`.
- Si la insercion resulta exitosa, la API mantiene el contrato externo y responde simplemente `"OK"`.

### Invocacion SQL

```sql
EXEC [Contribucion].[InsertNewOrderList]
    @NewOrders = @OrdersJson;
```

### Tabla de salida relevante de la consulta

| Columna SP / DTO | Campo tecnico | Tipo dato | Observaciones |
|---|---|---|---|
| `OrdenID` | `InsertNewOrderListOutDTO.OrdenID` | `int` | La procedure devuelve el detalle, pero el controller lo descarta y responde `"OK"`. |
| `IdentificadorOrden` | `InsertNewOrderListOutDTO.IdentificadorOrden` | `varchar` | Identificador externo de la orden. |
| `MensajeError` | `InsertNewOrderListOutDTO.MensajeError` | `varchar` | Si hay errores y `allowPartialProcessing = false`, la business logic arma la falla a partir de esta columna. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@NewOrders` | `varchar(max)` | JSON compuesto por la API con un arreglo de una sola orden. |
| `AllowPartialProcessing` | `bit` | Lo fija el controller en `false`. |
| `Partys` | `json array` | Lo completa la business logic con filtros del usuario autenticado. |
| `User` | `string` | Lo completa la business logic con el usuario autenticado. |

### Observaciones tecnicas

- La implementacion vigente de `NewOrderSingle` no llama a `InsertNewOrderBL`; reutiliza `InsertNewOrderList`.
- La business logic valida dia habil y que todas las ordenes del lote pertenezcan al mismo mercado, aun cuando en este endpoint el lote tenga un solo elemento.
- Para `Side = D`, la business logic fuerza `CreditsInCollClearingAccount = null`.
- La procedure aplica defaults tecnicos no visibles desde el controller, por ejemplo:
  - si `TipoCuentaAcreditacionDestino` llega `NULL` en rescates, inserta `1`,
  - si `Finalidad` llega `NULL`, inserta `FinalidadID = 2`.
- En caso exitoso, la API no devuelve el `OrdenID` ni el detalle insertado; solo conserva el contrato `"OK"`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Orden insertada correctamente | `"OK"` |
| `400 BadRequest` | Body nulo o JSON invalido | `"Json format invalid"` |
| `460` | Orden duplicada | `"One or more orders are duplicated"` |
| `461` | Precio o decimales del FCI no configurados | `"Price or decimal quantity are not configured"` |
| `462` | Finalidad invalida | `"Invalid Reference"` |
| `463` | Stock insuficiente | `"Stock not available"` |
| `464` | Error interno asociado a precio | `"Internal system error (price)"` |
| `465` | Decimales de cantidad invalidos | `"OrderQty decimals exceeds the maximum"` |
| `466` | Monto fuera de rango permitido | `"Invalid range CashOrderQty"` |
| `467` | Fuera de horario o sesion no habilitada | `"Trading session is closed for this symbol."` o `"The symbol does not allow extended trading session"` |
| `468` | Instrumento no habilitado | `"Symbol not allowed"` |
| `469` | Suscripcion enviada con tipo no permitido | `"Side D only allows subscriptions by amount"` |
| `470` | Fondo sin nuevas suscripciones | `"The selected asset does not receive new subscriptions"` |
| `471` | Rescate por monto sin stock suficiente | `"The selected instrument does not have sufficient stock to complete the requested redemption by amount"` |
| `472` | Se informo `OrderQty` para rescate por monto | `"OrderQty should not be sent if OrderType is 'M'"` |
| `473` | Se informo `CashOrderQty` para rescate por cantidad | `"CashOrderQty should not be sent if OrderType is 'C'"` |
| `474` | Error interno de aforo | `"Internal system error (haircut)"` |
| `475` | Rescate por monto no habilitado para el FCI | `"The selected instrument does not allow subscriptions by amount"` |
| `476` | Falta CBU para la cuenta de pago directo | `"No CBU defined for the selected account."` |
| `500` | Error interno general | `"Internal Server Error"` |

### Observaciones adicionales

- Los codigos `460` a `500` no salen del stored procedure directamente: los construye el controller con `MapError(...)`.
- Si el mercado esta cerrado a nivel de business logic antes de entrar a la procedure, el mensaje original es `"Market closed."`, que el controller no remapea explicitamente y puede devolver como `400`.
- La procedure contiene validaciones adicionales no mapeadas con codigo especifico, por ejemplo `"Solo se permiten ordenes para cuentas activas"` o `"La Cuenta no es valida"`. En esos casos el controller puede devolver `400` con el texto original o el mapeo puntual si existe.
- La documentacion historica describe el contrato funcional, pero la ruta real vigente es la del procesamiento por lista con una sola orden.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `POST /Trade/NewOrderSingle`, alineado con controller, business logic, roles relevados y stored procedure vigente. |
