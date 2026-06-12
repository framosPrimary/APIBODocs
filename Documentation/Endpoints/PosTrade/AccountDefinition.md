# Definicion de Cuenta de Registro (AccountDefinition)

## Objetivo

El objetivo del metodo es consultar la definicion resumida de una cuenta de registro a traves del endpoint `GET /PosTrade/AccountDefinition`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccountDefinition` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/AccountDefinition?accountRegistryCode={{accountRegistryCode}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `MC`
- `Clearing`
- `EntidadBursatil`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `accountRegistryCode` | `string` | Codigo de cuenta de registro. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/AccountDefinition?accountRegistryCode=22300
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "CuentaRegistroDescripcion": "Cuenta de Registro 22300",
    "CuentaRegistroCodigo": "22300",
    "CuentaRegistroCodigoPropioMiembroCompensador": "22300-356",
    "CuitCuil": "20-12345678-9",
    "EstadoDescripcion": "Activa",
    "ParticipanteCodigo": "9001",
    "ParticipanteDescripcion": "Cliente Demo",
    "MiembroCompensadorCodigo": "356",
    "MiembroCompensadorDescripcion": "Alyc Demo",
    "TipoCuentaNegociacionDescripcion": "Comitente",
    "EsCuentaAlgoritmica": false,
    "Email": "cliente@demo.com"
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Personas].[GetReporteCuentaRegistroPublicacion]`

### Invocacion SQL

```sql
SELECT *
FROM [Personas].[GetReporteCuentaRegistroPublicacion] (
    @cuentaRegistroCodigo
);
```

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@cuentaRegistroCodigo` | `varchar` | Proviene de `accountRegistryCode`. |

### Observaciones tecnicas

- La implementacion observada no agrega filtros adicionales de usuario en la llamada a la funcion.
- La respuesta expone directamente el resultado de la funcion SQL.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista con la definicion de la cuenta. |
| `400 BadRequest` | `accountRegistryCode` vacio o nulo | `"Parameter accountRegistryCode cannot be null."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PosTrade/AccountDefinition`, alineado con controller, roles relevados y funcion SQL vigente. |
