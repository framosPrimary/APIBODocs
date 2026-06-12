# Transferencia de Saldos (BalanceTransfer - GET)

## Objetivo

El objetivo del endpoint es consultar el estado de una transferencia de saldos a traves de `PosTrade/BalanceTransfer`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `BalanceTransfer` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/BalanceTransfer?TransferID={{TransferID}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `Administrador`, `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `TransferID` | `string` | Identificador externo de la transferencia. | Obligatorio |

## Funcion de la BD utilizada

- Funcion utilizada: `GetEstadoContribucionBalanceTransferByCodigoExterno(...)`

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Estado de contribucion. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `GET` para `BalanceTransfer`, alineado con controller, BL y roles observados. |
