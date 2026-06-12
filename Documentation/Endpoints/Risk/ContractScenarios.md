# Escenarios de Contratos (ContractScenarios)

## Objetivo

El objetivo del endpoint es consultar los escenarios de riesgo de contratos disponibles a traves de `Risk/ContractScenarios`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ContractScenarios` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Risk/ContractScenarios` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `MC`, `API Publico`

## Parametros

El metodo no expone parametros de entrada.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Risk/ContractScenarios
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "ScenarioCode": "1",
    "ScenarioDescription": "Escenario base"
  }
]
```

## Funcion de la BD utilizada

- El endpoint no consulta SQL local en forma directa.
- La BL consume el servicio externo `Winterfell.API.Rest`.
- Recurso observado: `/api/rima/getcontractscenarios`

## Filtros y parametros

### Observaciones tecnicas

- La BL actua como proxy del servicio de escenarios.
- La respuesta se deserializa y se devuelve agrupada segun la estructura del servicio externo.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de escenarios. |
| `500 InternalServerError` | Error tecnico no controlado | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `ContractScenarios`, alineado con controller, BL y roles observados. |
