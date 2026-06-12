# Registro de Cupo de Entrega (DeliveryQuotaRegistration - GET)

## Objetivo

El objetivo del endpoint es consultar cupos de entrega a traves de `PosTrade/DeliveryQuotaRegistration`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DeliveryQuotaRegistration` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/DeliveryQuotaRegistration?allocID={{allocID}}` |

## Roles que pueden utilizarlo

- `GET`: `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `allocID` | `string` | Identificador de la asignacion. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/DeliveryQuotaRegistration?allocID=1500
Authorization: Bearer {{token}}
```

## Funcion de la BD utilizada

- La operatoria se integra con servicios externos configurados en:
  - `DeliveryQuotaRegistrationToken`
  - `DeliveryQuotaRegistration`

## Filtros y parametros

### Observaciones tecnicas

- La BL consulta el servicio remoto usando `pCuentaAlyc` y `pCaratula` derivados del usuario.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Respuesta del servicio externo. |
| `400 BadRequest` | Validacion de negocio o formato invalido | Mensaje detallado de validacion. |
| `500 InternalServerError` | Error tecnico | `"Internal Error."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `GET` para `DeliveryQuotaRegistration`, alineado con controller, BL y roles observados. |
