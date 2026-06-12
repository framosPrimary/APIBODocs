# Productos con Spread Margins (ProductsSpreadMargins)

## Objetivo

El objetivo del endpoint es consultar los productos con spread margins a traves de `Risk/ProductsSpreadMargins`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ProductsSpreadMargins` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Risk/ProductsSpreadMargins` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `MC`, `API Publico`

## Parametros

El metodo no expone parametros de entrada.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Risk/ProductsSpreadMargins
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

- El endpoint no usa SQL local directo.
- La BL consume `Winterfell.API.Rest`.
- Recurso observado: `/api/rima/getproductsspreadmargins`

## Filtros y parametros

### Observaciones tecnicas

- La respuesta se parsea como `JObject` y se devuelve sin reprocesamiento adicional.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | JSON con productos y spreads. |
| `400 BadRequest` | Error tecnico capturado por controller | Mensaje de error. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `ProductsSpreadMargins`, alineado con controller, BL y roles observados. |
