# Referencia Legacy: contratos

## Objetivo

Este archivo deja documentado el caso `contratos` del inventario historico para no confundirlo con un endpoint activo de APIBO.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre relevado en `EnpointsRoles.txt` | `contratos` |
| Roles relevados | `GIX Administrador` (`GET` y `POST`), `GIX Interviniente` (`GET`) |
| Estado actual en APIBO | `No se detecto ruta activa con ese nombre` |

## Trazabilidad observada

- No se encontro un controller con ruta `contratos` en el repo actual de APIBO.
- En documentacion historica y capas de negocio aparecen metodos vinculados al universo de contratos, pero bajo nombres distintos.
- Los endpoints activos mas cercanos en la APIBO actual son:
  - [SecurityList.md](/c:/Users/framos/Documents/APIBO/Documentation/Endpoints/PreTrade/SecurityList.md)
  - [ContratosActivos.md](/c:/Users/framos/Documents/APIBO/Documentation/Endpoints/PreTrade/ContratosActivos.md)

## Observaciones

- Por los roles `GIX ...`, este registro parece corresponder a una nomenclatura legacy o a otra superficie funcional distinta de la APIBO actual.
- Para no inventar una ruta inexistente, se documenta como referencia historica y no como endpoint operativo.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento de referencia legacy para `contratos`, dejando asentado que no se encontro una ruta activa con ese nombre en APIBO. |
