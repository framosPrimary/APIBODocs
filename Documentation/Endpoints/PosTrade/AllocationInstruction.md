# Ofertas de Entrega (AllocationInstruction)

## Objetivo

El objetivo del endpoint es consultar ofertas de entrega a traves de `GET /PosTrade/AllocationInstruction`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AllocationInstruction` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/AllocationInstruction?date={{date}}&status={{status}}&side={{side}}&tradeNumber={{tradeNumber}}&secondaryAllocId={{secondaryAllocId}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `AN`
- `MC`
- `CEL`
- `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha en formato `yyyyMMdd`. Si no se informa, usa hoy. | No |
| `status` | `string` | Estado a filtrar. | No |
| `side` | `string` | Lado de la operacion. | No |
| `tradeNumber` | `string` | Numero de trade. | No |
| `secondaryAllocId` | `string` | Identificador secundario de asignacion. | No |

## Funcion de la BD utilizada

- La BL delega en `GetReporteReservaOfertaEntregaPublicacion(...)`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de ofertas de entrega. |
| `400 BadRequest` | Fecha invalida | `"Date format not valid."` |
| `400 BadRequest` | Otros errores | Mensaje de excepcion. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PosTrade/AllocationInstruction`, alineado con controller, roles y capa de negocio observada. |
