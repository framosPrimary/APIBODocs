# Resumen Global de Custodia (CustodyGlobalStock - POST)

## Objetivo

El objetivo del endpoint es informar el resumen global de custodia a traves de `PosTrade/CustodyGlobalStock`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CustodyGlobalStock` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PosTrade/CustodyGlobalStock` |

## Roles que pueden utilizarlo

- `POST`: `MC RM`, `MC`

## Parametros

El metodo acepta un `body` JSON con un objeto.

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PosTrade/CustodyGlobalStock
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "CompensationAccountCode": "0001",
  "InstrumentCode": "AL30",
  "CumQty": 1000
}
```

## Funcion de la BD utilizada

- Persistencia de alta: `[Contribucion].[InsertResumenCustodia]`
- La BL tambien envia el resultado a `ClearingInterface.ContribucionResumenCustodia`.

## Filtros y parametros

### Observaciones tecnicas

- La BL valida campos numericos y consistencia entre `CumQty`, `DayCumQty` y movimientos.
- El payload se complementa con datos del usuario autenticado antes de persistir.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | `null` o respuesta de negocio. |
| `400 BadRequest` | Body nulo o JSON invalido | `"Json format invalid"` |
| `400 BadRequest` | Error de validacion funcional | Mensaje de negocio o arreglo JSON. |
| `503 ServiceUnavailable` | Error tecnico controlado | `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `POST` para `CustodyGlobalStock`, alineado con controller, BL, SQL y roles observados. |
