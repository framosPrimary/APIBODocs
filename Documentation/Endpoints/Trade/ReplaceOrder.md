# Modificacion de Ordenes (ReplaceOrder)

## Objetivo

El objetivo del metodo es solicitar la modificacion de una orden existente a traves del endpoint `POST /Trade/ReplaceOrder`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ReplaceOrder` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/Trade/ReplaceOrder` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `MC`

## Parametros

Este metodo recibe un `body` JSON.

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `ClOrdID` | `integer` | Identificador de la orden que se desea modificar. | Obligatorio |
| `CashOrderQty` | `number` | Monto a modificar en suscripciones o rescates por monto, segun la documentacion historica. | Condicional |
| `OrderQty` | `number` | Cantidad de cuotapartes a modificar en rescates por cantidad, segun la documentacion historica. | Condicional |

### Campos internos completados por la API

Los siguientes datos no necesitan enviarse en el body porque la API los completa internamente:

- `OrderStatus = "E"` para marcar la operacion como reemplazo.
- `User` con el usuario autenticado.
- `RootParties` con los filtros de usuario autenticado, especialmente miembro compensador y cuenta de compensacion.

## Ejemplo

### Ejemplo de request

**Objetivo**  
Modificar la orden `4534` actualizando el monto solicitado.

```http
POST {{url}}/Trade/ReplaceOrder
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "ClOrdID": 4534,
  "CashOrderQty": 374.4
}
```

### Ejemplo de respuesta

```json
"987655"
```

La respuesta es el `OrdenID` interno del reemplazo generado, serializado como `string`.

## Funcion de la BD utilizada

- Stored procedure utilizado: `[Contribucion].[InsertNewOrder]`

### Regla de uso

- El controller llama a `InsertOrderService.ReplaceOrder`.
- La service reutiliza `InsertOrderBL.InsertOrder(..., "replace")`.
- La business logic fuerza `OrderStatus = "E"` y delega la persistencia en `[Contribucion].[InsertNewOrder]`.

### Invocacion SQL

```sql
EXEC [Contribucion].[InsertNewOrder]
    @NewOrder = @OrderJson;
```

### Tabla de salida de la consulta

| Columna SP / modelo | Campo API observable | Tipo dato | Observaciones |
|---|---|---|---|
| `OrdenID` | respuesta HTTP | `int` | Si la operacion es exitosa, la API devuelve este valor convertido a `string`. |
| `Message` | mensaje de error | `varchar` | Si `OrdenID = 0`, la API lanza el mensaje de negocio y responde `400 BadRequest`. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes de SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@NewOrder` | `varchar(max)` | JSON armado por la business logic a partir del body recibido y datos internos agregados por la API. |
| `$.ClOrdID` | `varchar(max)` | Proviene de `ClOrdID` enviado por el consumidor. |
| `$.OrderStatus` | `char(1)` | Lo completa internamente la API con valor `"E"`. |
| `$.User` | `varchar(max)` | Lo completa internamente la API con el usuario autenticado. |
| `$.RootParties` | `json array` | Lo completa internamente la API a partir de los filtros de usuario autenticado. |

### Observaciones tecnicas

- El metodo deserializa el body contra `ApiBO.Model.Proxy.Orden`.
- En SQL, el branch de reemplazo esta comentado como `Reemplazo orden (no se usa)`.
- En la version vigente del procedure, las asignaciones directas desde JSON para `@cashQty` y `@qty` estan comentadas, por lo que el reemplazo no muestra una lectura explicita del nuevo monto o nueva cantidad desde el body.
- En la practica, esto sugiere que el endpoint existe y responde, pero su implementacion tecnica vigente puede no reflejar completamente la intencion funcional historica de modificar importe o cantidad.
- Luego de guardar el reemplazo, la business logic recupera la orden insertada y la envia a la cola `ClearingInterface.ContribucionOrdenes`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Reemplazo aceptado | `"987655"` |
| `400 BadRequest` | Body nulo o JSON invalido | `"Json format invalid"` |
| `400 BadRequest` | Error tecnico durante la persistencia o inconsistencia en el reemplazo | `"The operation could not be performed."` |

### Observaciones adicionales

- El controller captura cualquier excepcion y responde `400 BadRequest`.
- A diferencia de `NewOrderSingle`, este endpoint no usa el mapeador `MapError`, por lo que no transforma mensajes de negocio a codigos especificos.
- No se relevo una validacion de negocio explicita tipo `"La orden no existe."` para reemplazo en el branch activo. Si el `ClOrdID` no se resuelve correctamente, el flujo puede terminar en un error tecnico generico.
- La documentacion historica lo presenta como modificacion de suscripcion o rescate, pero el stored procedure vigente deja una senal clara de implementacion parcial o no priorizada.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `POST /Trade/ReplaceOrder`, alineado con controller, business logic, roles relevados y stored procedure vigente. |
