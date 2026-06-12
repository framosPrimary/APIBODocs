# Cancelacion de Ordenes (CancelOrder)

## Objetivo

El objetivo del metodo es solicitar la anulacion de una orden existente a traves del endpoint `POST /Trade/CancelOrder`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CancelOrder` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/Trade/CancelOrder` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `MC`

## Parametros

Este metodo recibe un `body` JSON.

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `ClOrdID` | `integer` | Identificador de la orden que se desea cancelar. | Obligatorio |

### Campos internos completados por la API

Los siguientes datos no necesitan enviarse en el body porque la API los completa internamente antes de persistir la cancelacion:

- `OrderStatus = "4"` para marcar la operacion como anulacion.
- `User` con el usuario autenticado.
- `RootParties` con los filtros de usuario autenticado, especialmente miembro compensador y cuenta de compensacion.

## Ejemplo

### Ejemplo de request

**Objetivo**  
Cancelar la orden con identificador `4535`.

```http
POST {{url}}/Trade/CancelOrder
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "ClOrdID": 4535
}
```

### Ejemplo de respuesta

```json
"987654"
```

La respuesta es el `OrdenID` interno de la cancelacion generada, serializado como `string`.

## Funcion de la BD utilizada

- Stored procedure utilizado: `[Contribucion].[InsertNewOrder]`

### Regla de uso

- El controller llama a `InsertOrderService.CancelOrder`.
- La service reutiliza `InsertOrderBL.InsertOrder(..., "cancel")`.
- La business logic fuerza `OrderStatus = "4"` y delega la persistencia en `[Contribucion].[InsertNewOrder]`.
- Aunque existen objetos generados `InsertOrderCancel*`, el endpoint `POST /Trade/CancelOrder` no usa esa ruta tecnica en la implementacion vigente.

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
| `$.OrderStatus` | `char(1)` | Lo completa internamente la API con valor `"4"`. |
| `$.User` | `varchar(max)` | Lo completa internamente la API con el usuario autenticado. |
| `$.RootParties` | `json array` | Lo completa internamente la API a partir de los filtros de usuario autenticado. |

### Observaciones tecnicas

- El metodo deserializa el body contra `ApiBO.Model.Proxy.Orden`, aunque para cancelacion el campo funcionalmente relevante es `ClOrdID`.
- La validacion de pertenencia de la orden se apoya en el miembro compensador incluido en `RootParties` con `RootPartyRole = "4"`.
- El stored procedure busca la orden original por `IdentificadorOrden = ClOrdID` y por el miembro compensador del usuario autenticado.
- Si la anulacion es valida, el procedimiento copia el instrumento de la orden original, inserta una nueva orden con estado `4`, actualiza la orden original a estado `4`, copia el `OrdenLado` de la orden original e inserta las `RootParties` calculadas por la API.
- Luego de guardar la cancelacion, la business logic recupera la orden insertada y la envia a la cola `ClearingInterface.ContribucionOrdenes`.
- La implementacion vigente reutiliza el procedure de alta de ordenes; no hay una procedure exclusiva de cancelacion en la ruta efectiva del endpoint.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Cancelacion exitosa | `"987654"` |
| `400 BadRequest` | Body nulo o JSON invalido | `"Json format invalid"` |
| `400 BadRequest` | La orden no pertenece al alcance valido o no existe para el miembro compensador del usuario | `"La orden no existe."` |
| `400 BadRequest` | Se excedio la ventana permitida para anulacion | `"La orden no se puede anular."` |
| `400 BadRequest` | El instrumento no admite anulacion | `"El instrumento no permite anulacion."` |
| `400 BadRequest` | Rescate money market ya procesado con liquidacion inmediata | `"No se permite anular una Orden de Rescate ya procesada con liquidacion inmediata."` |
| `400 BadRequest` | Error tecnico durante la persistencia | `"The operation could not be performed."` |

### Observaciones adicionales

- El controller captura cualquier excepcion y siempre responde `400 BadRequest`; no hay mapeo diferenciado de status para errores de negocio.
- La API no valida explicitamente la presencia de `ClOrdID` en controller. Si el campo falta o no permite resolver una orden valida, el flujo termina devolviendo `"La orden no existe."`.
- La logica de horario o dia habil general no se aplica a cancelacion en la capa C#; el comentario en business logic indica que solo importa la ventana de anulacion de la orden original.
- La restriccion temporal efectiva se evalua en SQL usando `TiempoAnulacion` del mercado `FCI`.
- Si el stored procedure devuelve `OrdenID = 0`, la API expone el `Message` del procedimiento como texto plano en la respuesta.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `POST /Trade/CancelOrder`, alineado con controller, service, business logic, roles relevados y stored procedure vigente. |
