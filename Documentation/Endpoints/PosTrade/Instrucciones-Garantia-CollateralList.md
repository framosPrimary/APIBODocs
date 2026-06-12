# Activos Aceptados en Garantia (CollateralList)

## Objetivo

El objetivo del metodo es consultar la lista de activos aceptados en garantia a traves del endpoint `GET /PosTrade/CollateralList`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CollateralList` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/CollateralList?date={{date}}` |

## Roles que pueden utilizarlo

El controller expone el metodo con `[WAAuthorize]`, pero no define en esta capa una lista explicita de roles.

Por lo tanto:

- el acceso depende de la autorizacion configurada en la aplicacion
- no se relevaron filtros adicionales por usuario en la consulta del endpoint

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha de publicacion a consultar, en formato `yyyyMMdd`. Si no se informa, devuelve la ultima publicacion disponible. Si se informa, devuelve la ultima publicacion menor o igual a esa fecha. | No |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Consultar los activos aceptados en garantia tomando como referencia la fecha `20260521`.

```http
GET {{url}}/PosTrade/CollateralList?date=20260521
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "InternalInstrumentCode": 498,
    "Asset": "FCI Consultatio Balance Fund (B)",
    "InstrumentCode": "498",
    "ExpirationDate": null,
    "PriceDate": "2026-05-20T00:00:00",
    "Price": 37.3061,
    "Currency": "Pesos",
    "Haircut": 85.00,
    "Reference": "Margenes",
    "Isin": "AR0000000000",
    "SecuritySubType": "FCI",
    "SecuritySubTypeCode": 1
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Activos].[GetReporteActivoPublicacionByFechaCotizacion]`

### Invocacion SQL

```sql
SELECT *
FROM [Activos].[GetReporteActivoPublicacionByFechaCotizacion] (
    @fechaPublicacion
);
```

### Tabla de salida de la consulta

| Columna fn / modelo | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `ActivoID` | `InternalInstrumentCode` | `int` | Se expone como identificador interno del instrumento. |
| `ActivoDescripcion` | `Asset` | `varchar(100)` | Descripcion del activo. |
| `ActivoCodigo` | `InstrumentCode` | `varchar(20)` | Codigo del instrumento. |
| `FechaVencimiento` | `ExpirationDate` | `date` | Puede venir `null`. |
| `FechaCotizacion` | `PriceDate` | `date` | Fecha de cotizacion expuesta por la API. |
| `Precio` | `Price` | `decimal(18,6)` | Precio del activo. |
| `MonedaDescripcion` | `Currency` | `varchar(100)` | Descripcion de la moneda. |
| `Aforo` | `Haircut` | `decimal(6,2)` | Aforo aplicado al activo. |
| `FinalidadDescripcion` | `Reference` | `varchar(100)` | Finalidad con la que el activo es aceptado en garantia. |
| `Isin` | `Isin` | `varchar` | Surge del modelo expuesto por la API. |
| `ClasificacionActivoDescripcion` | `SecuritySubType` | `varchar` | Descripcion de la clasificacion del activo. |
| `ClasificacionActivoID` | `SecuritySubTypeCode` | `tinyint` | Codigo de clasificacion del activo. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes de SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@fechaPublicacion` | `date` | Proviene del parametro `date` del request, o `null` si no se informa. |

### Observaciones tecnicas

- La funcion SQL devuelve la publicacion cuya `FechaPublicacion` sea la maxima menor o igual a `@fechaPublicacion`.
- Si `@fechaPublicacion` es `null`, la funcion devuelve la ultima `FechaPublicacion` disponible en la tabla `[Activos].[ActivoPublicacionFinalidad]`.
- El endpoint no aplica filtros adicionales por miembro compensador, cuenta o comitente en el codigo relevado.
- La capa de negocio remapea la salida SQL y expone solo el subconjunto de campos publicado por la API.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de activos aceptados en garantia. |
| `400 BadRequest` | El parametro `date` no respeta el formato `yyyyMMdd` | `"Parameter 'date' malformed. (YYYYMMDD)"` |

### Observaciones adicionales

- El parametro `date` es opcional.
- Si no hay publicaciones para la fecha informada ni anteriores, la salida puede ser una lista vacia.
- El controller solo valida el formato de `date`; no implementa otras validaciones explicitas.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-05-21` | Documento inicial del metodo `GET /PosTrade/CollateralList`, alineado con controller, business logic y funcion SQL vigentes. |
