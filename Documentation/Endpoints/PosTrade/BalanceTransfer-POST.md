# Transferencia de Saldos (BalanceTransfer - POST)

## Objetivo

El objetivo del endpoint es registrar transferencias de saldos a traves de `PosTrade/BalanceTransfer`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `BalanceTransfer` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PosTrade/BalanceTransfer` |

## Roles que pueden utilizarlo

- `POST`: `MC RM`, `Administrador`, `MC`

## Parametros

Este metodo recibe un `body` JSON.

Observaciones relevadas:

- la BL agrega `UserName`, `UserMail` y `RootParties`
- la validacion depende del `TransferType`
- luego de guardar se construye un mensaje y se envia a `ClearingInterface.ContribucionMensajes`

## Funcion de la BD utilizada

- Stored procedure utilizada: `[Contribucion].[InsertBalanceTransfer]`

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | ID interno o respuesta de negocio. |
| `400 BadRequest` | Body nulo o JSON invalido | `"Json format invalid"` |
| `400 BadRequest` | Campos obligatorios faltantes | Mensajes como `"Field TransferType cannot be null."` |
| `400 BadRequest` | Validacion de cuentas o depositarias | `"Invalid OriginAccount"`, `"Invalid DestinationSettlementAccountCode"`, etc. |
| `400 BadRequest` | Miembro compensador inconsistente | `"Clearing member does not match settlement accounts"` |
| `503 ServiceUnavailable` | Error tecnico en persistencia | Mensaje de `InvalidOperationException`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `POST` para `BalanceTransfer`, alineado con controller, BL, roles y procedure observada. |
