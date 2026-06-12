# Contribucion de Posiciones de Cuotapartistas (ShareHolderPositionSG)

## Objetivo

El objetivo del endpoint es registrar posiciones de cuotapartistas informadas por el emisor a traves de `PosTrade/ShareHolderPositionSG`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ShareHolderPositionSG` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PosTrade/ShareHolderPositionSG` |

## Roles que pueden utilizarlo

- `POST`: `Emisor`

## Parametros

El metodo acepta un `body` JSON con un arreglo de objetos.

Campos relevantes del modelo:

- `FundCode`
- `ShareholderCode`
- `TotalShares`
- `BlockedShares`
- `ValuedShares`
- `Price`
- `Date`
- `InternalEmmiterCode`

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PosTrade/ShareHolderPositionSG
Authorization: Bearer {{token}}
Content-Type: application/json

[
  {
    "FundCode": "F001",
    "ShareholderCode": "SH001",
    "TotalShares": 1000,
    "BlockedShares": 10,
    "ValuedShares": 990,
    "Price": 123.45,
    "Date": "2026-06-10T00:00:00"
  }
]
```

## Funcion de la BD utilizada

- Persistencia observada: `[Contribucion].[InsertPosicionCuotapartista]`
- Cola de integracion observada: `ClearingInterface.ContribucionPosicionesCuotapartistas`

## Filtros y parametros

### Observaciones tecnicas

- La BL toma el codigo emisor desde el filtro `Transmitter` y lo asigna a `EmmiterCode`.
- Antes de persistir valida obligatoriedad, longitudes y reglas de negocio.
- Luego serializa el lote completo y lo persiste en una sola llamada.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | ID o resultado devuelto por la persistencia. |
| `400 BadRequest` | Body invalido o regla de negocio incumplida | Mensaje de `ShareHolderPositionException`. |
| `500 InternalServerError` | Error tecnico | `"Internal Server Error."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `ShareHolderPositionSG`, alineado con controller, BL, persistencia y roles observados. |
