# Distribucion de Activos (CollateralAssignment - POST)

## Objetivo

El objetivo del endpoint es registrar distribuciones de activos a traves de `PosTrade/CollateralAssignment`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CollateralAssignment` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PosTrade/CollateralAssignment` |

## Roles que pueden utilizarlo

- `POST`: `MC RM`, `MC`

## Parametros

Este metodo recibe un `body` JSON.

## Funcion de la BD utilizada

- Stored procedure observada: `[Contribucion].[InsertDistribucionActivo]`

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | Identificador generado o respuesta de negocio. |
| `400 BadRequest` | Body nulo o JSON invalido | `"Json format invalid"` |
| `400 BadRequest` | Error de negocio | Mensaje de excepcion. |
| `503 ServiceUnavailable` | Error tecnico | Mensaje de `InvalidOperationException`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `POST` para `CollateralAssignment`, alineado con controller, roles y persistencia observada. |
