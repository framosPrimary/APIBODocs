# Instrucciones de Garantia (NewCollateralReport - GET)

## Objetivo

El objetivo del metodo es consultar el estado de contribucion de una instruccion de garantia a traves del endpoint `GET /PosTrade/NewCollateralReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `NewCollateralReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/NewCollateralReport?CollRptID={{CollRptID}}` |

## Roles que pueden utilizarlo

El controller expone el metodo con `[WAAuthorize]`, pero no define en esta capa una lista explicita de roles.

Por lo tanto:

- el acceso depende de la autorizacion configurada en la aplicacion
- la consulta se restringe por los filtros del usuario autenticado

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `CollRptID` | `string` | Codigo externo de la instruccion a consultar. | Obligatorio |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Consultar el estado de contribucion de la instruccion externa `GAR-20260519-001`.

```http
GET {{url}}/PosTrade/NewCollateralReport?CollRptID=GAR-20260519-001
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "CollRptID": "GAR-20260519-001",
    "Status": "Inicial"
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Contribucion].[GetEstadoContribucionCollateralReportByCodigoExterno]`

### Invocacion SQL

```sql
SELECT *
FROM [Contribucion].[GetEstadoContribucionCollateralReportByCodigoExterno] (
    @CodigoExterno,
    @MiembroCompensadorCodigo,
    @CuentaCompensacionCodigo
);
```

### Tabla de salida de la consulta

| Columna fn | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `CodigoExterno` | `CollRptID` | `varchar(20)` | Se devuelve con alias `CollRptID`. |
| `EstadoAprobacionDescripcion` | `Status` | `varchar` | Surge de `general.EstadoAprobacion`. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes de SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@CodigoExterno` | `varchar(20)` | Proviene de `CollRptID` en el request. |
| `@MiembroCompensadorCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@CuentaCompensacionCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |

### Observaciones tecnicas

- La funcion SQL filtra por `Fecha = CONVERT(DATE, GETDATE())`, por lo que la consulta devuelve solo resultados del dia actual.
- La consulta tambien filtra por los permisos del usuario autenticado usando miembro compensador y/o cuenta de compensacion.
- El response del `GET` expone solo `CollRptID` y `Status`; no devuelve el detalle completo de la instruccion.
- No se relevo en el codigo revisado un catalogo cerrado de estados para `Status`; el valor surge de la descripcion configurada en `general.EstadoAprobacion`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de objetos con `CollRptID` y `Status`. |

### Observaciones adicionales

- El controller no implementa validaciones explicitas ni manejo de errores especifico para `CollRptID`.
- Si la consulta no encuentra resultados, la salida puede ser una lista vacia o sin elementos visibles segun el comportamiento de la capa de servicio/datos.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | 2026-05-19 | Documento inicial del metodo `GET /PosTrade/NewCollateralReport`, alineado con controller y funcion de consulta de estado vigentes. |
