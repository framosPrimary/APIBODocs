# Instrumento Digital (DigitalInstrument - GET)

## Objetivo

El objetivo del endpoint es consultar instrumentos digitales a traves de `PreTrade/DigitalInstrument`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DigitalInstrument` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/DigitalInstrument?securityReqID={{securityReqID}}&symbol={{symbol}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `Administrador`, `MC`, `EntidadBursatil`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `securityReqID` | `string` | Identificador del requerimiento del instrumento. | Condicional |
| `symbol` | `string` | Simbolo del instrumento. | Condicional |

Debe informarse al menos uno de los dos parametros.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PreTrade/DigitalInstrument?securityReqID=9001
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

- Consulta: `GetInstrumentoDigitalBL().GetAllGrouped(...)`

## Filtros y parametros

### Observaciones tecnicas

- El controller exige `securityReqID` o `symbol`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Datos del instrumento. |
| `400 BadRequest` | Faltan `securityReqID` y `symbol` | `"Invalid entry. SecurityReqID or Symbol are required."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `GET` para `DigitalInstrument`, alineado con controller, BL y roles observados. |
