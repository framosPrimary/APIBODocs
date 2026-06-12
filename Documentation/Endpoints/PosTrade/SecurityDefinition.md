# Simulacion de Escenarios (SecurityDefinition)

## Objetivo

El objetivo del endpoint es consultar parametros de simulacion de escenarios para garantias a traves de `PosTrade/SecurityDefinition`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `SecurityDefinition` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/SecurityDefinition?simulationDate={{simulationDate}}` |

## Roles que pueden utilizarlo

- `GET`: `Multi Cel`, `MC RM`, `Comitente`, `MC`, `Clearing`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `simulationDate` | `string` | Fecha de simulacion en formato `yyyyMMdd`. | Obligatorio en la practica |
| `symbol` | `string` | Simbolo del instrumento. | Opcional |
| `segment` | `string` | Segmento de mercado. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/SecurityDefinition?simulationDate=20260610&symbol=DLR122026
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

- Funcion observada: `OwnData.GetReporteParametrosSimulacionContrato(...)`

## Filtros y parametros

### Observaciones tecnicas

- La BL agrupa la salida por `MarketSegment` y `SimulationDate`.
- Dentro de cada grupo, arma `Params` y a su vez `Scenarios` por `ScenarioNumber` y `SimValue`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Simulaciones agrupadas por segmento y fecha. |
| `400 BadRequest` | Fecha invalida | `"Parameters 'SimulationDate' malformed. (YYYYMMDD)"` |
| `400 BadRequest` | Error funcional o tecnico capturado | Mensaje de error. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `SecurityDefinition`, alineado con controller, BL, SQL y roles observados. |
