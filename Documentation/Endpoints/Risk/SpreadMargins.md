# Spread Margins (SpreadMargins)

## Objetivo

El objetivo del endpoint es consultar la configuracion de spread margins a traves de `Risk/SpreadMargins`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `SpreadMargins` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Risk/SpreadMargins` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `MC`, `API Publico`

## Parametros

El metodo no expone parametros de entrada.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Risk/SpreadMargins
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

- El endpoint no usa SQL local directo.
- La BL consume `Winterfell.API.Rest`.
- Recurso observado: `/api/rima/getspreadmargins`

## Filtros y parametros

### Observaciones tecnicas

- La respuesta se parsea como `JObject` y se devuelve sin transformaciones adicionales.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | JSON con definiciones de spread margins. |
| `400 BadRequest` | Error tecnico capturado por controller | Mensaje de error. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `SpreadMargins`, alineado con controller, BL y roles observados. |
