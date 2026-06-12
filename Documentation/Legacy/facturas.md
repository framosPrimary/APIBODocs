# Referencia Legacy: facturas

## Objetivo

Este archivo deja documentado el caso `facturas` del inventario historico y su correspondencia con el endpoint activo actual.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre relevado en `EnpointsRoles.txt` | `facturas` |
| Roles relevados | `GIX Administrador` (`GET` y `POST`), `GIX Interviniente` (`GET`) |
| Correspondencia actual en APIBO | `Invoices` |
| Ruta activa actual | `PosTrade/Invoices` |

## Trazabilidad observada

- En el repo actual no se detecto una ruta activa llamada `facturas`.
- La funcionalidad vigente equivalente esta implementada y expuesta como [Invoices.md](/c:/Users/framos/Documents/APIBO/Documentation/Endpoints/PosTrade/Invoices.md).
- La documentacion historica tambien describe esta funcionalidad como comprobantes, facturas, liquidaciones, retenciones y notas de credito.

## Observaciones

- Este nombre se mantiene en el roadmap como alias legacy para no perder trazabilidad con inventarios anteriores.
- La documentacion operativa y vigente del endpoint debe considerarse la de `Invoices`.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento de referencia legacy para `facturas`, enlazado con el endpoint activo `Invoices`. |
