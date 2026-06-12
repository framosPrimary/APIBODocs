# Registro de Cupo de Entrega (DeliveryQuotaRegistration - POST)

## Objetivo

El objetivo del endpoint es registrar cupos de entrega a traves de `PosTrade/DeliveryQuotaRegistration`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DeliveryQuotaRegistration` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PosTrade/DeliveryQuotaRegistration` |

## Roles que pueden utilizarlo

- `POST`: `MC`

## Parametros

El metodo acepta un `body` JSON con un arreglo de objetos.

Campos validados en la API:

- `AllocID` obligatorio, string numerico
- `QuotaDate` obligatorio, formato `yyyyMMdd`
- `DeliveryPoint` obligatorio
- si `AllocID = "0"`, tambien exige `Product`, `TaxNumberSellerAccount`, `SellerAccount` y `BuyerAccount`
- `Observations` opcional, maximo `300` caracteres

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PosTrade/DeliveryQuotaRegistration
Authorization: Bearer {{token}}
Content-Type: application/json

[
  {
    "AllocID": "1500",
    "QuotaDate": "20260615",
    "DeliveryPoint": "ROS",
    "Observations": "Cupo confirmado"
  }
]
```

## Funcion de la BD utilizada

- La operatoria se integra con servicios externos configurados en:
  - `DeliveryQuotaRegistrationToken`
  - `DeliveryQuotaRegistration`

## Filtros y parametros

### Observaciones tecnicas

- La fecha se transforma de `yyyyMMdd` a `dd/MM/yyyy` antes de enviar al servicio externo.
- La API requiere que el body sea un arreglo JSON no vacio.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | Respuesta del servicio externo. |
| `400 BadRequest` | Validacion de negocio o formato invalido | Mensaje detallado de validacion. |
| `500 InternalServerError` | Error tecnico | `"Internal Error."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `POST` para `DeliveryQuotaRegistration`, alineado con controller, BL y roles observados. |
