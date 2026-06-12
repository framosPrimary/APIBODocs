# Detalles de Cuentas (AccountDetails)

## Objetivo

El objetivo del metodo es consultar el detalle de una cuenta particular a traves del endpoint `GET /PreTrade/AccountDetails`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccountDetails` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/AccountDetails?accountCode={{accountCode}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `MC`
- `CEL`
- `Clearing`
- `EntidadBursatil`
- `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `accountCode` | `string` | Codigo de la cuenta a consultar. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PreTrade/AccountDetails?accountCode=22300
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
 {
    "Status": "OK",
    "Code": "200",
    "Value": [
        {
            "AccountCode": "15200",
            "Account": "BAGUI SA",
            "CompensationAccountCode": "1321",
            "CompensationAccount": "PUENTE HNOS. S.A.",
            "NettingAccountCode": "15200",
            "PartyId": "30692857611",
            "CreationDate": "2015-09-30T00:00:00",
            "ClearingMemberCode": "321",
            "ClearingMember": "PUENTE HNOS. S.A.",
            "AccountType": 1,
            "UnderlyingOwner": true,
            "PosTransType": [
                [
                    {
                        "PosTransTypeID": "",
                        "PosTransTypeIDSource": 1
                    },
                    {
                        "PosTransTypeID": "1",
                        "PosTransTypeIDSource": 2
                    }
                ]
            ],
            "PartySubGrp": [
                [
                    {
                        "PartySubID": "",
                        "PartySubIDSource": 5
                    },
                    {
                        "PartySubID": "DIAZ VELEZ AV. 4436 (1200)",
                        "PartySubIDSource": 6
                    },
                    {
                        "PartySubID": "+54 (11) 4958-7717",
                        "PartySubIDSource": 7
                    },
                    {
                        "PartySubID": "FEDERICOB@BAGUI.COM.AR",
                        "PartySubIDSource": 8
                    },
                    {
                        "PartySubID": "",
                        "PartySubIDSource": "B1"
                    },
                    {
                        "PartySubID": "",
                        "PartySubIDSource": "B2"
                    },
                    {
                        "PartySubID": "BAGUI S A",
                        "PartySubIDSource": 2
                    },
                    {
                        "PartySubID": 2,
                        "PartySubIDSource": 4009
                    },
                    {
                        "PartySubID": 2,
                        "PartySubIDSource": 4003
                    },
                    {
                        "PartySubID": true,
                        "PartySubIDSource": 4005
                    },
                    {
                        "PartySubID": 2,
                        "PartySubIDSource": 4022
                    }
                ]
            ]
        }
    ]
}
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Personas].[GetReporteCuentaRegistroPublicacionDetalle]`
- Tabla Consultada: `[Personas].[CuentaRegistroPublicacion]`

### Invocacion SQL

```sql
SELECT *
FROM [Personas].[GetReporteCuentaRegistroPublicacionDetalle] (
    @cuentaRegistroCodigo,
    @miembrosCompensadoresCodigoFilter,
    @cuentasCompensacionCodigoFilter
);
```

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@cuentaRegistroCodigo` | `varchar` | Proviene de `accountCode`. |
| `@miembrosCompensadoresCodigoFilter` | `varchar(max)` | Filtro del usuario autenticado. |
| `@cuentasCompensacionCodigoFilter` | `varchar(max)` | Filtro del usuario autenticado. |

### Observaciones tecnicas

- La BL agrupa la salida y arma `PosTransType` y `PartySubGrp`.
- En `PartySubGrp` se exponen varios subdatos con fuentes como `5`, `6`, `7`, `8`, `B1`, `B2`, `4009`, `4003`, `4005` y `4022`.
- El endpoint limita la salida al alcance del usuario por miembro compensador y cuenta de compensacion.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada de detalles de cuenta. |
| `400 BadRequest` | `accountCode` ausente | `"accountCode is required"` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PreTrade/AccountDetails`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
