# Reporte de Operaciones por Cuenta de Registro (TradeCaptureReportByAccountRegistry)

## Objetivo

El objetivo historico del endpoint era consultar operaciones por cuenta de registro mediante `MyPortfolio/TradeCaptureReportByAccountRegistry`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `TradeCaptureReportByAccountRegistry` |
| Estado actual en el repo | `Inactivo / comentado` |
| Ruta historica observada | `MyPortfolio/TradeCaptureReportByAccountRegistry` |
| Tipo de request historico | `GET` |

## Roles que pueden utilizarlo

- `GET`: `Administrador`, `Multicomitente`

## Parametros

Parametros observados en el controller comentado:

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde. | Obligatorio |
| `dateTo` | `string` | Fecha hasta. | Obligatorio |
| `pageNumber` | `int` | Numero de pagina. Default `1`. | Opcional |

## Ejemplo

### Observacion

En el repo actual el controller completo esta comentado, por lo que no hay un endpoint activo expuesto en tiempo de ejecucion.

## Funcion de la BD utilizada

- BL observada: `GetReporteOperacionPublicacionPorComitenteBL`
- Funcion observada: `OwnData.GetReporteOperacionPublicacionPorComitente(fechaDesde, fechaHasta, paginaNumero, cuentasRegistroCodigos)`

## Filtros y parametros

### Observaciones tecnicas

- La BL sigue existiendo y devuelve `TotalPageCount` mas `Value`.
- El controller comentado exigia `dateFrom` y `dateTo`, y hacia parseo con `TryParseParameter`.

## Validaciones/comentarios en la API

### Estado actual

- No se detecto exposicion activa del endpoint en el controller compilable actual.
- Si se reactivara el controller comentado, las validaciones historicas relevadas eran:
  - `400 BadRequest` si faltan `dateFrom` o `dateTo`
  - `400 BadRequest` si las fechas tienen formato invalido
  - `200 OK` en consulta exitosa

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento de referencia del endpoint `TradeCaptureReportByAccountRegistry`, dejando asentado que el controller aparece comentado en el repo actual aunque su BL siga existiendo. |
