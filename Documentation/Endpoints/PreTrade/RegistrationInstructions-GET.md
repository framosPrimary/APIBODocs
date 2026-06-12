# Registro de Warrant y Certificado de Deposito (RegistrationInstructions - GET)

## Objetivo

El objetivo del metodo es consultar instrucciones de warrant y certificado de deposito a traves del endpoint `PreTrade/RegistrationInstructions`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `RegistrationInstructions` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/RegistrationInstructions?registID={{registID}}&marketID={{marketID}}` |

## Roles que pueden utilizarlo

- `GET`: `Administrador`, `MC`, `Clearing`, `EntidadBursatil`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `registID` | `integer` | Identificador externo de la instruccion/certificado. | Obligatorio |
| `marketID` | `string` | Codigo de mercado o entidad bursatil. | Condicional |

Regla observada:

- `marketID` es obligatorio para usuarios con filtro de miembro compensador si no tienen filtro de entidad bursatil

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PreTrade/RegistrationInstructions?registID=12345&marketID=CU
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "RegistID": 12345,
    "MarketID": "CU",
    "ClearingMemberCode": "356",
    "SettlInstSource": 0,
    "DocumentType": "Warrant",
    "RegistRefID": "REF-1001",
    "SeriesName": "Serie A",
    "Comments": "Carga inicial",
    "DepositPeriod": 180,
    "MaturityDate": "2026-12-10T00:00:00",
    "IssuanceDate": "2026-06-10T00:00:00",
    "Status": "Pending"
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `Mensajes.[GetMensajeCertificadoDepositoWarrantById]`

## Filtros y parametros

### Observaciones tecnicas

- Si el usuario tiene filtro de entidad bursatil, ese valor pisa al `marketID` recibido.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Instruccion consultada. |
| `400 BadRequest` | `MarketID` faltante para ciertos perfiles | `"MarketID is required."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `GET` para `RegistrationInstructions`, alineado con controller, BL y roles observados. |
