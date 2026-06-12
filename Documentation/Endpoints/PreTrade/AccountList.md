# Listado de Cuentas de Neteo (AccountList)

## Objetivo

El objetivo del metodo es consultar, en forma resumida o detallada, las cuentas dadas de alta en una fecha determinada a traves del endpoint `GET /PreTrade/AccountList`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccountList` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/AccountList?date={{date}}&viewDetails={{viewDetails}}&partyId={{partyId}}&status={{status}}&externalCode={{externalCode}}&pageNumber={{pageNumber}}&pageSize={{pageSize}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `MC`
- `MC Consulta`
- `MC Admin`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha en formato `yyyyMMdd`. | Obligatorio |
| `viewDetails` | `boolean` | Si vale `true`, devuelve detalle. Default: `false`. | No |
| `partyId` | `string` | Identificador de parte. Solo aplica con `viewDetails=true`. | No |
| `status` | `integer` | Estado de contribucion. Solo aplica con `viewDetails=true`. | No |
| `externalCode` | `string` | Codigo externo. Solo aplica con `viewDetails=true`. | No |
| `pageNumber` | `integer` | Numero de pagina. Default interno: `1`. | No |
| `pageSize` | `integer` | Cantidad de registros por pagina. Default interno: `int.MaxValue`. | No |

## Ejemplo

### Ejemplo de request resumido

```http
GET {{url}}/PreTrade/AccountList?date=20020629
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta resumida

```json
[
  {
    "Date": "2002-06-29",
    "Quantity": 15
  }
]
```

### Ejemplo de request detallado

```http
GET {{url}}/PreTrade/AccountList?date=20020629&viewDetails=true&partyId=20123456789&status=1
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta detallada

```json
[
  {
    "Date": "2002-06-29T00:00:00",
    "ExternalCode": "EXT-001",
    "PartyId": "20123456789",
    "AccountCode": "22300",
    "Status": 1,
    "PartySubGrp": [
      [
        {
          "PartySubID": "3415551234",
          "PartySubIdSource": 7
        },
        {
          "PartySubID": "mail@demo.com",
          "PartySubIdSource": 8
        }
      ]
    ]
  }
]
```

## Funcion de la BD utilizada

- Resumen: `[Contribucion].[GetReporteConciliacionCuentasResumen]`
- Detalle: `[Contribucion].[GetReporteConciliacionCuentasDetalle]`

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@Fecha` | `date` | Proviene de `date`. |
| `@MiembroCompensadorCodigo` | `varchar(max)` | Filtro del usuario autenticado. |
| `@Cuit` | `varchar` | Proviene de `partyId` en modo detalle. |
| `@EstadoContribucion` | `int` | Proviene de `status` en modo detalle. |
| `@CodigoExterno` | `varchar` | Proviene de `externalCode` en modo detalle. |
| `@paginaNumero` | `int` | Proviene de `pageNumber`. |
| `@registrosPorPagina` | `int` | Proviene de `pageSize`. |

### Observaciones tecnicas

- Cuando `viewDetails=false`, no se permiten `partyId`, `status` ni `externalCode`.
- En modo detalle, si `status` no se informa, la BL usa `0`.
- En modo detalle, la BL arma `PartySubGrp` con telefono (`7`) y email (`8`).

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Resumen o detalle segun `viewDetails`. |
| `400 BadRequest` | `date` ausente | `"Date is required."` |
| `400 BadRequest` | Fecha invalida | `"Date format not valid."` |
| `400 BadRequest` | `partyId` contiene espacios o `-` en detalle | `"PartyId format not valid"` |
| `400 BadRequest` | `status` fuera de `0..4` | `"Status not valid."` |
| `400 BadRequest` | Se envian parametros no permitidos en modo resumen | Mensajes como `"PartyId, Status not allowed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PreTrade/AccountList`, alineado con controller, business logic, roles relevados y funciones SQL vigentes. |
