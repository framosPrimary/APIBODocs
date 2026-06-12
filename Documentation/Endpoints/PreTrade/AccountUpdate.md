# Modificacion de Cuentas de Registro (AccountUpdate)

## Objetivo

El objetivo del endpoint es modificar cuentas de registro existentes a traves de `POST /PreTrade/AccountUpdate`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccountUpdate` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PreTrade/AccountUpdate` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `MC`

## Parametros

El metodo acepta un `body` JSON con un objeto o un arreglo de objetos.

Observaciones relevantes:

- si el body es `JObject`, procesa una modificacion
- si el body es `JArray`, procesa un lote
- la BL agrega `UserInsert`

## Funcion de la BD utilizada

- Stored procedures observadas:
  - `[Contribucion].[InsertModificacionCuenta]`
  - `[Contribucion].[InsertModificacionCuentaRegistro]`

## Filtros y parametros

### Observaciones tecnicas

- La BL valida formato, negocio y CBU primario/secundario contra el `PartyId`.
- Si la modificacion es valida, recupera luego la cuenta modificada y la envia a la cola `ClearingInterface.ContribucionModificacionCuenta`.
- Los errores de lote se agrupan por mensaje y por `AccountCode`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Modificacion exitosa | `null` o `"OK"` segun flujo interno. |
| `208` | Se detectan cuentas duplicadas en lote | Arreglo JSON con duplicados. |
| `400 BadRequest` | Body invalido | `"Json format invalid"` |
| `400 BadRequest` | Cuenta invalida | Mensajes como `"Invalid AccountCode"` |
| `400 BadRequest` | CUIT/CUIL vacio o invalido | `"Empty CUITCUIL"` / `"Invalid CUITCUIL"` |
| `400 BadRequest` | Miembro o cuenta de compensacion invalidos | `"Invalid Compensation Member"` / `"Invalid Compensation Account"` |
| `503 ServiceUnavailable` | Error interno mapeado por BL | `"Internal Error"` o `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `POST /PreTrade/AccountUpdate`, alineado con controller, business logic, roles y persistencia observada. |
