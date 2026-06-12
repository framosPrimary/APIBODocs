# Participantes (PartyDetails)

## Objetivo

El objetivo del metodo es consultar participantes del mercado e informacion asociada a traves del endpoint `GET /PreTrade/PartyDetails`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `PartyDetails` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/PartyDetails?classification={{classification}}` |

## Roles que pueden utilizarlo

- `MC RM`
- `Administrador`
- `AN`
- `MC`
- `CEL`
- `Clearing`
- `API Público`
- `EntidadBursatil`
- `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `classification` | `string` | Clasificacion de las partes a consultar. La BL la normaliza a minusculas. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PreTrade/PartyDetails?classification=participante
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "PartyDetail": {
      "PartyDetailID": "356",
      "PartyDetailIDSource": "D",
      "PartyDetailRole": 4,
      "PartyDetailGrp": [
        {
          "PartySubIDType": 6,
          "PartySubID": "A3 Mercados"
        }
      ],
      "ContextParties": [
        {
          "PartyContextualID": "1234",
          "PartyContextualSource": "D",
          "PartyContextualRole": 24,
          "ContextPartySubGroup": [
            {
              "PartySubIDContextual": 7,
              "PartySubIDTypeContextual": "Account"
            }
          ]
        }
      ]
    }
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Personas].[GetListaPartes]`

### Invocacion SQL

```sql
SELECT *
FROM [Personas].[GetListaPartes] (
    @clasificacion,
    @CuentaRegistroCodigo,
    @ParticipanteCodigo,
    @ParticipanteIntermediarioCodigo,
    @CuentaCompensacionCodigo,
    @MiembroCompensadorCodigo,
    @PersonaID,
    @CuentaRegistroACVNCodigo,
    @MercadoCodigo
);
```

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@clasificacion` | `varchar` | Proviene de `classification`. |
| filtros de usuario | `varchar(max)` | Cuenta registro, participante, registrante, cuenta compensacion, miembro compensador, persona, ACVN y mercado. |

### Observaciones tecnicas

- Para ciertas clasificaciones (`cuentaeb`, `participantemav`, `participante`) la BL usa cache en memoria.
- La respuesta se arma agrupando `PartyDetail`, `PartyDetailGrp` y `ContextParties`.
- Si la clasificacion es `Cuenta de Registro` y el usuario tiene comodin de miembro compensador, la BL usa un agrupado especial.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada de participantes. |

### Observaciones adicionales

- El controller no valida `classification`; si llega vacio o invalido, el comportamiento final depende de la capa de datos.
- Si no hay coincidencias, la respuesta practica es `200 OK` con lista vacia.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PreTrade/PartyDetails`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
