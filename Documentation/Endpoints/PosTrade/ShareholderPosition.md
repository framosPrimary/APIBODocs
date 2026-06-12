# Posicion de Cuotapartistas (ShareholderPosition)

## Objetivo

El objetivo del endpoint es consultar la posicion de cuotapartistas a traves de `PosTrade/ShareholderPosition`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ShareholderPosition` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/ShareholderPosition?date={{date}}` |

## Roles que pueden utilizarlo

- `GET`: `Administrador`, `Clearing`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha a consultar. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/ShareholderPosition?date=20260610
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

- Funcion observada: `[Contribucion].[GetPosicionCuotaPartista]`

## Filtros y parametros

### Observaciones tecnicas

- El endpoint esta marcado con `ApiExplorerSettings(IgnoreApi = true)`, por lo que puede quedar oculto del explorador Swagger aun estando implementado.
- La respuesta viene desde `GetPosicionCuotaPartistaAsync(fecha)`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Posiciones de cuotapartistas. |
| `400 BadRequest` | Falta `date` | `"Parameter date cannot be null."` |
| `400 BadRequest` | Fecha invalida | Mensaje de parseo generado por `TryParseParameter`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `ShareholderPosition`, alineado con controller, BL, SQL y roles observados. |
