# Lista de Monedas (CurrencyList)

## Objetivo

El objetivo del metodo es consultar la lista de monedas cargadas en el sistema a traves del endpoint `GET /PreTrade/CurrencyList`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CurrencyList` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/CurrencyList` |

## Roles que pueden utilizarlo

- `MC RM`
- `Comitente`
- `Administrador`
- `AN`
- `MC`
- `CEL`
- `Clearing`
- `API Público`
- `CEL RM`

## Parametros

Este metodo no requiere parametros.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PreTrade/CurrencyList
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "CurrencyDescription": "Peso Argentino",
    "CurrencyCode": "032",
    "Currency": "ARS",
    "CurrencySymbol": "$",
    "IsCurrencyBase": true,
    "FiscalCode": "PES"
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[General].[GetReporteMonedas]`

### Invocacion SQL

```sql
SELECT CurrencyDescription, CurrencyCode, Currency, CurrencySymbol, IsCurrencyBase, FiscalCode
FROM [General].[GetReporteMonedas] ();
```

## Filtros y parametros

### Observaciones tecnicas

- La implementacion vigente no agrega filtros por usuario autenticado.
- La API devuelve directamente el resultado del ADO sin agrupaciones adicionales.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de monedas. |

### Observaciones adicionales

- No se relevaron validaciones adicionales en controller.
- Si no hubiera datos, la respuesta esperable es `200 OK` con lista vacia.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PreTrade/CurrencyList`, alineado con controller, business logic, roles relevados y consulta SQL vigente. |
