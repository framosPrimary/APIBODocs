# Registro de Warrant y Certificado de Deposito (RegistrationInstructions - POST)

## Objetivo

El objetivo del metodo es registrar instrucciones de warrant y certificado de deposito a traves del endpoint `PreTrade/RegistrationInstructions`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `RegistrationInstructions` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PreTrade/RegistrationInstructions` |

## Roles que pueden utilizarlo

- `POST`: `Administrador`, `MC`

## Parametros

Este metodo recibe un `body` JSON.

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `RegistID` | `integer` | Identificador externo. | Obligatorio |
| `MarketID` | `string` | Mercado o entidad bursatil. | Obligatorio |
| `SettlInstSource` | `integer` | Fuente de la instruccion. Valores definidos: `0`, `1`, `2`. | Obligatorio |
| `DocumentType` | `string` | Tipo documental. | Obligatorio |
| `RegistRefID` | `string` | Referencia externa. | Obligatorio |
| `SeriesName` | `string` | Nombre de la serie. | Obligatorio |
| `Parties` | `array` | Debe contener exactamente 4 objetos. | Obligatorio |
| `Product` | `object` | Datos del producto. | Obligatorio |
| `Comments` | `string` | Comentarios. | Obligatorio |
| `DepositPeriod` | `integer` | Cantidad de dias de deposito. | Obligatorio |
| `MaturityDate` | `string` | Fecha `yyyy-MM-dd`. | Obligatorio |
| `IssuanceDate` | `string` | Fecha `yyyy-MM-dd`. | Obligatorio |
| `DebtData` | `array` | Deudas asociadas. | Obligatorio |
| `WarrantOriginId` | `integer` | Origen del warrant. | Obligatorio |
| `ClearingMemberCode` | `string` | Puede enviarse en el body o completarse desde filtros del usuario. | Condicional |

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PreTrade/RegistrationInstructions
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "RegistID": 12345,
  "MarketID": "CU",
  "SettlInstSource": 0,
  "DocumentType": "Warrant",
  "RegistRefID": "REF-1001",
  "SeriesName": "Serie A",
  "Comments": "Carga inicial",
  "DepositPeriod": 180,
  "MaturityDate": "2026-12-10",
  "IssuanceDate": "2026-06-10",
  "WarrantOriginId": 1,
  "Parties": [
    { "PartyID": 1, "PartyRole": "Owner", "Name": "Parte 1", "Address": { "Province": "SF", "City": "Rosario", "PostalCode": "2000", "Street": "Cordoba", "Number": "100" } },
    { "PartyID": 2, "PartyRole": "Depositary", "Name": "Parte 2", "Address": { "Province": "SF", "City": "Rosario", "PostalCode": "2000", "Street": "Cordoba", "Number": "200" } },
    { "PartyID": 3, "PartyRole": "Insurer", "Name": "Parte 3", "Address": { "Province": "SF", "City": "Rosario", "PostalCode": "2000", "Street": "Cordoba", "Number": "300" } },
    { "PartyID": 4, "PartyRole": "Signer", "Name": "Parte 4", "Address": { "Province": "SF", "City": "Rosario", "PostalCode": "2000", "Street": "Cordoba", "Number": "400" } }
  ],
  "Product": {
    "Class": "Soja",
    "UnitOfMeasure": 1,
    "Presentation": "Granel",
    "Quality": "Camara",
    "Quantity": 100.0,
    "UnitPrice": 250.0,
    "Currency": "USD",
    "TotalValue": 25000.0
  },
  "DebtData": [
    {
      "DebtId": 1,
      "DebtRole": "Main",
      "Amount": 5000.0,
      "Currency": "USD"
    }
  ]
}
```

### Ejemplo de respuesta

```json
{
  "CertificateID": "98765",
  "CertificateNumber": "WAR-2026-0001"
}
```

## Funcion de la BD utilizada

- Stored procedure de validacion: `[MensajesGeneral].[ValidacionesWarrant]`
- Stored procedure de insercion: `[MensajesGeneral].[InsertMensajeWarrant]`

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `CompesationMemberAccount` | `string` | Lo completa la BL con `ClearingMemberCode` o con el filtro del usuario. |
| `CompensationAccount` | `string` | Lo completa la BL con el filtro de cuenta de compensacion. |
| `AuditoriaUsuarioInsercion` | `string` | Lo completa la BL con el usuario autenticado. |
| `UserEmail` | `string` | Lo completa la BL con el email del usuario autenticado. |

### Observaciones tecnicas

- El controller realiza una validacion estructural fuerte del body antes de llegar a la BL.
- La BL enriquece el JSON con datos internos del usuario y ejecuta la validacion de negocio.
- Si la insercion es exitosa, la instruccion se serializa y se envia a la cola `IE.Instrucciones`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | Identificadores del certificado generado. |
| `400 BadRequest` | Falta un campo obligatorio del body | Mensajes como `"RegistID is required."` |
| `400 BadRequest` | Tipo de dato invalido en el body | Mensajes como `"SettlInstSource must be an integer."` |
| `400 BadRequest` | Fecha invalida | `"MaturityDate must be a valid date in the format yyyy-MM-dd."` |
| `400 BadRequest` | `Parties` no contiene exactamente 4 elementos | `"Parties must contain exactly 4 objects."` |
| `400 BadRequest` | `PartyID` repetidos | `"PartyID values must be unique."` |
| `400 BadRequest` | `SettlInstSource` fuera del enum permitido | `"SettlInstSource must be one of the defined values: 0, 1, 2."` |
| `400 BadRequest` | `UnitOfMeasure` fuera del enum permitido | Mensaje con valores permitidos `1`, `32`, `29`, `39`, `40`. |
| `400 BadRequest` | Falta `ClearingMemberCode` y el usuario no lo aporta por filtro | `"ClearingMemberCode is required."` |
| `503 ServiceUnavailable` | Error tecnico encapsulado como `InvalidOperationException` | `"Internal Error."` o `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `POST` para `RegistrationInstructions`, alineado con controller, BL, roles relevados y componentes vigentes. |
