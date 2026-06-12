# Cuentas Relacionadas (RelatedAccount)

## Objetivo

El objetivo del endpoint es consultar las cuentas relacionadas del comitente a traves de `MyPortfolio/RelatedAccount`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `RelatedAccount` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/MyPortfolio/RelatedAccount` |

## Roles que pueden utilizarlo

- `GET`: `Multicomitente`

## Parametros

El metodo no expone parametros de entrada.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/MyPortfolio/RelatedAccount
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "ClearingMember": "ALYC",
    "NettingAccount": "001",
    "Accounts": [
      {
        "AccountRelated": "10002"
      }
    ]
  }
]
```

## Funcion de la BD utilizada

- Implementacion actual activa: `KattegatRelatedAccount`
- Implementacion local disponible en BL: `OwnData.GetReporteCuentasPorComitente(...)`

## Filtros y parametros

### Observaciones tecnicas

- El controller vigente usa la variante Azure/Kattegat.
- La BL obtiene JWT desde `KattegatJWT`.
- La consulta remota se arma con `RegisterAccounts={cuentasRegistroCodigo}`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de cuentas relacionadas agrupadas. |
| `400 BadRequest` | Error tecnico o de integracion | Mensaje de error. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `RelatedAccount`, alineado con controller, BL e integracion actual observada. |
