# MT506 por Cuenta de Registro (MT506ByAccountRegistry)

## Objetivo

El objetivo del endpoint es consultar el detalle MT506 por cuenta de registro a traves de `MyPortfolio/MT506ByAccountRegistry`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `MT506ByAccountRegistry` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/MyPortfolio/MT506ByAccountRegistry?date={{date}}&originType={{originType}}` |

## Roles que pueden utilizarlo

- `GET`: `Administrador`, `Multicomitente`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha del reporte. Si es menor al dia actual, la BL considera historico. | Opcional |
| `originType` | `int` | Origen de datos. `1` AP5 / local, `2` Kattegat. Default `1`. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/MyPortfolio/MT506ByAccountRegistry?date=20260610&originType=1
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "NettingAccountCode": "001",
    "ClearingMember": "ALYC",
    "Asset": "AL30",
    "Qty": 1000
  }
]
```

## Funcion de la BD utilizada

- Fuente local actual: `[Activos].[GetReportePortfolioDetalleRofexPorComitente]`
- Fuente historica local: `[Activos].[GetReportePortfolioDetalleRofexPorComitenteHistorico]`
- Fuente Kattegat: `KattegatMT506ByAccountRegistry`

## Filtros y parametros

### Observaciones tecnicas

- La BL toma `AccountRegistry` desde los filtros del usuario.
- Si `originType = 1`, consulta AP5 local y decide actual vs historico segun la fecha.
- Si `originType = 2`, consulta Kattegat con JWT y cuenta de registro.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista MT506 agrupada. |
| `400 BadRequest` | Fecha invalida | Mensaje de parseo de `DateTime.ParseExact`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `MT506ByAccountRegistry`, alineado con controller, BL, origenes de datos y roles observados. |
