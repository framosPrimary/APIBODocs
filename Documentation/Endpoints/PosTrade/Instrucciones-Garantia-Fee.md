# Tarifas (Fee)

## Objetivo

El objetivo del metodo es consultar el listado de tarifas publicadas a traves del endpoint `GET /PosTrade/Fee`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `Fee` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/Fee?entity={{entity}}&tradingSession={{tradingSession}}&execType={{execType}}&product={{product}}&securityType={{securityType}}` |

## Roles que pueden utilizarlo

- `Multi Cel`
- `MC RM`
- `Administrador`
- `AN`
- `MC`
- `CEL`
- `xComitente`
- `Clearing`
- `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `entity` | `string` | Entidad tarifaria a filtrar. Ejemplos documentados: `ACSA`, `A3 Mercados`, `A3 Mercados BT`, `UFEX`. | No |
| `tradingSession` | `string` | Tipo de rueda a filtrar. | No |
| `execType` | `string` | Tipo de ejecucion a filtrar. | No |
| `product` | `string` | Producto a filtrar. | No |
| `securityType` | `string` | Tipo de producto o contrato a filtrar. | No |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Consultar las tarifas de la entidad `ACSA` para el producto `DLR`.

```http
GET {{url}}/PosTrade/Fee?entity=ACSA&product=DLR
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "FeeID": 100748,
    "Entity": "ACSA",
    "TradingSession": "Rueda Electronica",
    "ExecType": "BT",
    "OrderType": "Simple",
    "FeeBasis": 1,
    "FeeCurr": "USD R",
    "FeeGroup": "DLR Tarifa Bonificada MMK 50%",
    "SecurityType": "Futuro",
    "Product": "DLR",
    "FeeAmt": 0.025
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Productos].[GetReporteTarifaPublicacion]`

### Invocacion SQL

```sql
SELECT *
FROM [Productos].[GetReporteTarifaPublicacion] (
    @EntidadTarifariaDescripcion,
    @TipoRuedaDescripcion,
    @EjecucionDescripcion,
    @ProductoDescripcion,
    @TipoContratoDescripcion
);
```

### Tabla de salida de la consulta

| Columna fn / modelo | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `TarifaID` | `FeeID` | `int` | Identificador de la tarifa. |
| `EntidadTarifariaDescripcion` | `Entity` | `varchar` | Entidad tarifaria. |
| `TipoRuedaDescripcion` | `TradingSession` | `varchar` | Tipo de rueda. |
| `EjecucionDescripcion` | `ExecType` | `varchar` | Tipo de ejecucion. |
| `TipoOrdenDescripcion` | `OrderType` | `varchar` | Tipo de orden. |
| `EsPorcentaje + 1` | `FeeBasis` | `int` | Se expone transformado en SQL. |
| `Sigla` | `FeeCurr` | `varchar(20)` | Moneda de la tarifa. |
| `ListaPrecioDescripcion` | `FeeGroup` | `varchar(100)` | Grupo o lista de tarifa. |
| `TipoContratoDescripcion` | `SecurityType` | `varchar` | Tipo de contrato o producto. |
| `ProductoDescripcion` | `Product` | `varchar` | Producto. |
| `Valor` | `FeeAmt` | `decimal(18,6)` | Importe de la tarifa. |
| `CeilingLevel` | `CeilingLevel` | `decimal` | Existe en el modelo expuesto por la API, pero no se relevo mapeado en la funcion SQL actual revisada. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes de SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@EntidadTarifariaDescripcion` | `varchar(100)` | Proviene de `entity` en el request. |
| `@TipoRuedaDescripcion` | `varchar(100)` | Proviene de `tradingSession` en el request. |
| `@EjecucionDescripcion` | `varchar(100)` | Proviene de `execType` en el request. |
| `@ProductoDescripcion` | `varchar(100)` | Proviene de `product` en el request. |
| `@TipoContratoDescripcion` | `varchar(100)` | Proviene de `securityType` en el request. |

### Observaciones tecnicas

- El controller no aplica validaciones explicitas sobre los parametros del request.
- La consulta filtra por igualdad exacta sobre cada parametro solo cuando ese parametro viene informado.
- Si un parametro viene `null`, la funcion SQL no filtra por ese criterio.
- En el codigo relevado no se aplican filtros adicionales por usuario autenticado ni por cuentas sobre este endpoint.
- La capa de negocio no agrega transformaciones manuales; el endpoint expone la salida de la funcion con el contrato definido en el modelo.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de tarifas. |

### Observaciones adicionales

- Todos los parametros del endpoint son opcionales.
- Si no se informa ningun filtro, la funcion puede devolver el universo completo de tarifas publicadas.
- No se relevo manejo de errores especifico ni validaciones de formato en esta capa.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-05-21` | Documento inicial del metodo `GET /PosTrade/Fee`, alineado con controller, roles relevados y funcion SQL vigente. |
