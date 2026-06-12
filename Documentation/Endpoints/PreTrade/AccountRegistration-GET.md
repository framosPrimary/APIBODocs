# Cuentas (AccountRegistration - GET)

## Objetivo

El objetivo del endpoint es consultar cuentas de registro a traves de `PreTrade/AccountRegistration`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccountRegistration` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/AccountRegistration?regInsID={{regInsID}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `Administrador`, `MC`, `EntidadBursatil`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `regInsID` | `string` | Identificador de la instruccion o registro a consultar. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PreTrade/AccountRegistration?regInsID=12345
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

- Funcion utilizada por data layer: `GetCuenta(...)`

## Filtros y parametros

### Observaciones tecnicas

- La BL agrega filtros del usuario autenticado por miembro compensador, participante y entidad bursatil.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Datos de cuenta. |
| `400 BadRequest` | `regInsID` ausente | `"Invalid entry. regInsID is required."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `GET` para `AccountRegistration`, alineado con controller, BL, roles y persistencia observada. |
