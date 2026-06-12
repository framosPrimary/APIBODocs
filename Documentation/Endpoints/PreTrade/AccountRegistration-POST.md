# Cuentas (AccountRegistration - POST)

## Objetivo

El objetivo del endpoint es dar de alta cuentas de registro a traves de `PreTrade/AccountRegistration`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccountRegistration` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PreTrade/AccountRegistration` |

## Roles que pueden utilizarlo

- `POST`: `MC RM`, `Administrador`, `MC`, `EntidadBursatil`

## Parametros

El metodo acepta un `body` JSON con un objeto o un arreglo de objetos.

Observaciones relevadas:

- si el body es `JObject`, procesa una unica cuenta
- si el body es `JArray`, procesa varias cuentas
- si el body no es objeto ni arreglo, responde `Json format invalid`

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PreTrade/AccountRegistration
Authorization: Bearer {{token}}
Content-Type: application/json

{
 "RegInsID":"5533",
 "RegistTransType":"0",
 "Party":{
 "PartyId":"23111111119",
 "PartyIdSource":"J",
 "PartyRole":"83",
 "OwnerType":"0",
 "PartySubGrp":[
 {
 "PartySubID":"1123412342",
 "PartySubIdSource":"7"
 },
 {
 "PartySubID":"mpi@primary.com.ar",
 "PartySubIdSource":"8"
 },
 {
 "PartySubID":"356",
 "PartySubIdSource":"11",
 "PartyRole":"4"
 },
 {
 "PartySubID":"356",
 "PartySubIdSource":"11",
 "PartyRole":"30"
 }
 ]

```

### Ejemplo de respuesta

```json
null
```

Si hay errores de validacion o duplicados, la API devuelve un arreglo JSON con errores agrupados.

## Funcion de la BD utilizada

- Stored procedures observadas:
  - `[Contribucion].[InsertCuenta]`
  - `[Contribucion].[InsertCuentas]`

## Filtros y parametros

### Observaciones tecnicas

- La BL agrega el usuario autenticado al payload antes de validar e insertar.
- Cuando el usuario tiene filtro de entidad bursatil, la BL fuerza `MarketID` con ese valor.
- Si hay cuentas validas, se envian a la cola `ClearingInterface.ContribucionCuenta`.
- La BL agrupa errores de validacion y contempla estados especiales de persistencia, por ejemplo cuentas duplicadas.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | `null` en alta correcta. |
| `208` | Se detectan cuentas duplicadas en alta por lote | Arreglo JSON con errores de duplicidad. |
| `400 BadRequest` | Body nulo o formato JSON invalido | `"Json format invalid"` |
| `400 BadRequest` | Errores de validacion de negocio | Arreglo JSON con errores. |
| `503 ServiceUnavailable` | Error tecnico en capa inferior | `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `POST` para `AccountRegistration`, alineado con controller, BL, roles y persistencia observada. |
