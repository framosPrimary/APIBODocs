# Resumen Global de Custodia (CustodyGlobalStock - GET)

## Objetivo

El objetivo del endpoint es consultar el resumen global de custodia a traves de `PosTrade/CustodyGlobalStock`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CustodyGlobalStock` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/CustodyGlobalStock?date={{date}}` |

## Roles que pueden utilizarlo

- `GET`: `MC`, `Clearing`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha en formato `yyyyMMdd`. Si no se informa, usa la fecha actual. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/CustodyGlobalStock?date=20260610
Authorization: Bearer {{token}}
```

## Filtros y parametros

### Observaciones tecnicas

- Si `date` no se informa se consulta con la fecha del dia.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Datos de custodia. |
| `400 BadRequest` | Fecha invalida | `"Date format not valid."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `GET` para `CustodyGlobalStock`, alineado con controller y roles observados. |
