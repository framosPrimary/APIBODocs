# Fechas de Procesos (ClosingProcesses)

## Objetivo

El objetivo del metodo es consultar la fecha y hora de finalizacion de procesos a traves del endpoint `GET /PosTrade/ClosingProcesses`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ClosingProcesses` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/ClosingProcesses?EntryDate={{EntryDate}}&ProcessTypeCode={{ProcessTypeCode}}` |

## Roles que pueden utilizarlo

- `Multi Cel`
- `MC RM`
- `Administrador`
- `AN`
- `MC`
- `CEL`
- `xComitente`
- `Clearing`
- `API Público`
- `EntidadBursatil`
- `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `EntryDate` | `string` | Fecha en formato `yyyyMMdd`. | Obligatorio |
| `ProcessTypeCode` | `integer` | Tipo de proceso a filtrar. | No |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/ClosingProcesses?EntryDate=20180903&ProcessTypeCode=21
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "Processes": [
      {
        "ProcessType": "Movimientos de Custodia",
        "ProcessTypeCode": 21,
        "Entries": [
          {
            "EntryID": 56933,
            "EntryType": "Todos",
            "EntryDate": "2018-08-10",
            "EntryTime": "11:16:35",
            "Params": [
              {
                "ParamType": "1",
                "ParamValue": "ROFEX"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[General].[GetReporteProceso]`

### Invocacion SQL

```sql
SELECT *
FROM [General].[GetReporteProceso] (
    @Fecha,
    @TipoProcesoID
);
```

### Tabla de salida relevante

| Columna fn / modelo | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `ProcessTypeCode` | `ProcessTypeCode` | `int` | Tipo de proceso. |
| `ProcessType` | `ProcessType` | `varchar` | Descripcion del proceso. |
| `EntryID` | `EntryID` | `int` | Identificador de ejecucion/proceso. |
| `EntryType` | `EntryType` | `varchar` | Tipo o descripcion del entry. |
| `EntryDate` | `EntryDate` | `string` | Fecha del proceso ya formateada por SQL. |
| `EntryTime` | `EntryTime` | `string` | Hora del proceso ya formateada por SQL. |
| `ParamType` | `Params[].ParamType` | `string` | Codigo normalizado del parametro. |
| `ParamValue` | `Params[].ParamValue` | `string` | Valor del parametro. |

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@Fecha` | `date` | Proviene de `EntryDate` en el request. |
| `@TipoProcesoID` | `int` | Proviene de `ProcessTypeCode` en el request. |

### Observaciones tecnicas

- El controller valida presencia de `EntryDate` y luego usa `TryParseParameter` para convertirlo.
- La BL no devuelve la tabla SQL plana: agrupa por proceso y luego por entry, armando `Processes -> Entries -> Params`.
- La funcion SQL mapea varios nombres de parametro a codigos numericos de `ParamType`.
- En SQL, los valores `"1"` y `"0"` de algunos parametros se exponen como `"True"` y `"False"` en `ParamValue`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Estructura agrupada por proceso. |
| `400 BadRequest` | `EntryDate` ausente | `"Invalid entry. EntryDate is required."` |
| `400 BadRequest` | `EntryDate` invalida | Mensaje de parseo devuelto por `TryParseParameter`. |

### Observaciones adicionales

- `ProcessTypeCode` es opcional y permite acotar la consulta a un proceso puntual sin cambiar la estructura de la respuesta.
- La documentacion historica lista procesos como `1`, `2`, `8`, `9`, `11`, `16`, `19`, `20`, `21`, `23` y `26`; el codigo vigente no hardcodea esa lista, sino que la resuelve desde la funcion SQL.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PosTrade/ClosingProcesses`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
