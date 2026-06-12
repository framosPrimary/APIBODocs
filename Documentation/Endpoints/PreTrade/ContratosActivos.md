# Contratos Activos (ContratosActivos)

## Objetivo

El objetivo del endpoint es consultar el listado de contratos activos publicado por la API a traves de `PreTrade/ContratosActivos`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ContratosActivos` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/ContratosActivos` |

## Roles que pueden utilizarlo

- `GET`: `API Publico`

## Parametros

El metodo no expone parametros de entrada.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PreTrade/ContratosActivos
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "ProductCode": "DLR",
    "MaturityMonthYear": "202612",
    "SecurityDesc": "Futuro Dolar"
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada por la capa de datos: `[Productos].[GetReporteContratosActivosInterna]()`

## Filtros y parametros

### Observaciones tecnicas

- El controller invoca `GetReporteContratosActivosInterna()` a traves de la BL.
- La salida corresponde al universo de contratos activos publicados internamente.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de contratos activos. |
| `500 InternalServerError` | Error tecnico no controlado | Mensaje de error generado por la API. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `ContratosActivos`, alineado con controller, BL, SQL y roles observados. |
