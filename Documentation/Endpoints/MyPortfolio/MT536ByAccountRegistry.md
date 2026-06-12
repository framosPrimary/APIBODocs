# MT536 por Cuenta de Registro (MT536ByAccountRegistry)

## Objetivo

El objetivo del endpoint es consultar movimientos MT536 por cuenta de registro a traves de `MyPortfolio/MT536ByAccountRegistry`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `MT536ByAccountRegistry` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/MyPortfolio/MT536ByAccountRegistry?dateFrom={{dateFrom}}&dateTo={{dateTo}}&pageNumber={{pageNumber}}` |

## Roles que pueden utilizarlo

- `GET`: `Administrador`, `Multicomitente`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde. | Obligatorio |
| `dateTo` | `string` | Fecha hasta. | Obligatorio |
| `pageNumber` | `int` | Numero de pagina. Default `1`. | Opcional |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/MyPortfolio/MT536ByAccountRegistry?dateFrom=20260601&dateTo=20260610&pageNumber=1
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
{
  "TotalPageCount": 2,
  "Value": [
    {
      "ClearingBusinessDate": "2026-06-10T00:00:00",
      "Asset": "AL30",
      "Quantity": 100
    }
  ]
}
```

## Funcion de la BD utilizada

- Funcion observada: `OwnData.GetReporteMovimientosActivoPublicacionPorComitente(...)`

## Filtros y parametros

### Observaciones tecnicas

- La BL toma las cuentas de registro desde el filtro del usuario.
- Si `pageNumber` no se informa, la BL usa `1`.
- La salida se devuelve como `TotalPageCount` mas `Value`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Objeto paginado con movimientos MT536. |
| `400 BadRequest` | Faltan `dateFrom` o `dateTo` | `"Date from and date to cannot be null"` |
| `400 BadRequest` | Fechas invalidas | Mensaje de parseo generado por `TryParseParameter`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `MT536ByAccountRegistry`, alineado con controller, BL y roles observados. |
