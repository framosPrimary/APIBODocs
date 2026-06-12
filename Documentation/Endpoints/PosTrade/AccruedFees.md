# Tarifas Devengadas (AccruedFees)

## Objetivo

El objetivo del metodo es consultar los devengamientos de tarifas cobradas por las entidades a traves del endpoint `GET /PosTrade/AccruedFees`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccruedFees` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/AccruedFees?date={{date}}&accountCode={{accountCode}}&symbol={{symbol}}&productAlias={{productAlias}}&tradingSession={{tradingSession}}&execType={{execType}}&entity={{entity}}` |

## Roles que pueden utilizarlo

- `Multi Cel`
- `MC RM`
- `Comitente`
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
| `date` | `string` | Fecha a consultar en formato `yyyyMMdd`. | Obligatorio |
| `accountCode` | `string` | Codigo de cuenta de registro a filtrar. | No |
| `symbol` | `string` | Instrumento o contrato a filtrar. | No |
| `productAlias` | `string` | Alias del producto. Ejemplo: `DLR`. | No |
| `tradingSession` | `string` | Tipo de rueda a filtrar. | No |
| `execType` | `string` | Tipo de ejecucion a filtrar. | No |
| `entity` | `string` | Entidad a filtrar. Ejemplos documentados: `ACSA`, `A3 Mercados`, `PRIMARY`, `UFEX`. | No |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Consultar las tarifas devengadas del dia `20260521` para la cuenta `1234` y el instrumento `DLR122026`.

```http
GET {{url}}/PosTrade/AccruedFees?date=20260521&accountCode=1234&symbol=DLR122026
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "FeeDescription": "Derechos de Clearing DLR A3 Mercados",
    "ClearingMemberCode": "234",
    "ClearingMember": "AgA3 Mercados S.A.",
    "BillingAccountCode": "1234",
    "BillingAccount": "AgA3 Mercados S.A.",
    "AccountCode": "1234",
    "Account": "AgA3 Mercados S.A.",
    "ClearingAccountCode": "1234",
    "ClearingAccount": "AgA3 Mercados S.A.",
    "Symbol": "DLR122026",
    "ProductAlias": "DLR",
    "Product": "DLR",
    "TradingSession": "Fuera de Rueda",
    "ExecType": "Asignacion BT",
    "OrderType": "Combinada",
    "FeeCurr": "USD",
    "Date": "2026-05-21T00:00:00",
    "FeeType": "Facturable",
    "Trade": [
      {
        "TradeNumber": 9062221,
        "ExecId": 9062221,
        "Qty": 240000,
        "FeeAmt": 0,
        "BaseCurrencyTotal": 0
      }
    ],
    "FeeID": 100643,
    "Entity": "ACSA",
    "Status": "Facturado",
    "Rate": 1,
    "InvoiceNumber": 123456,
    "InvoiceCompleteNumber": "0001-00012345"
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Productos].[GetReporteTarifaDevengada]`

### Invocacion SQL

```sql
SELECT *
FROM [Productos].[GetReporteTarifaDevengada] (
    @CuentaRegistroCodigo,
    @ContratoDescripcion,
    @ProductoAlias,
    @TipoRuedaDescripcion,
    @EjecucionDescripcion,
    @Fecha,
    @EntidadDescripcion,
    @MiembroCompensadorCodigo,
    @CuentaCompensacionCodigo,
    @CuentasRegistroCodigo
);
```

### Tabla de salida de la consulta

| Columna fn / modelo | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `ComisionDetalle` | `FeeDescription` | `varchar` | Descripcion de la tarifa devengada. |
| `MiembroCompensadorCodigo` | `ClearingMemberCode` | `varchar` | Codigo del miembro compensador. |
| `MiembroCompensadorDescripcion` | `ClearingMember` | `varchar` | Descripcion del miembro compensador. |
| `CuentaFacturacionCodigo` | `BillingAccountCode` | `varchar` | Codigo de la cuenta de facturacion. |
| `CuentaFacturacionDescripcion` | `BillingAccount` | `varchar` | Descripcion de la cuenta de facturacion. |
| `CuentaRegistroCodigo` | `AccountCode` | `varchar(20)` | Codigo de la cuenta de registro. |
| `CuentaRegistroDescripcion` | `Account` | `varchar` | Descripcion de la cuenta de registro. |
| `CuentaCompensacionCodigo` | `ClearingAccountCode` | `varchar` | Codigo de la cuenta de compensacion. |
| `CuentaCompensacionDescripcion` | `ClearingAccount` | `varchar` | Descripcion de la cuenta de compensacion. |
| `ContratoDescripcion` | `Symbol` | `varchar(100)` | Instrumento o contrato. |
| `ProductoAlias` | `ProductAlias` | `varchar(100)` | Alias del producto. |
| `ProductoDescripcion` | `Product` | `varchar` | Descripcion del producto. |
| `TipoRuedaDescripcion` | `TradingSession` | `varchar(100)` | Tipo de rueda. |
| `EjecucionDescripcion` | `ExecType` | `varchar(100)` | Tipo de ejecucion. |
| `TipoOrdenDescripcion` | `OrderType` | `varchar(100)` | Tipo de orden. |
| `Cantidad` | `Qty` | `decimal` | Cantidad de la operacion. |
| `MonedaCodigoIso` | `FeeCurr` | `varchar(20)` | Moneda de la tarifa. |
| `Fecha` | `Date` | `date` | Fecha del devengamiento. |
| `Total` | `FeeAmt` | `decimal(18,6)` | Importe devengado. |
| `OperacionCarteraNumero` | `TradeNumber` | `int` | Numero de operacion. |
| `OperacionCarteraID` | `ExecId` | `int` | Identificador de ejecucion. |
| `TarifaID` | `FeeID` | `int` | Identificador de tarifa. |
| `EntidadDescripcion` | `Entity` | `varchar(100)` | Entidad. |
| `Cotizacion` | `Rate` | `decimal` | Cotizacion aplicada. |
| `TotalMonedaBase` | `BaseCurrencyTotal` | `decimal` | Total en moneda base. |
| `NumeroComprobante` | `InvoiceNumber` | `int` | Numero de comprobante. |
| `NumeroCompleto` | `InvoiceCompleteNumber` | `varchar` | Numero completo del comprobante. |
| `CASE EsFacturable ...` | `FeeType` | `varchar` | Informa si la tarifa es facturable o no facturable. |
| `CASE EstaFacturado ...` | `Status` | `varchar` | Informa si la tarifa esta facturada o no. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes de SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@CuentaRegistroCodigo` | `varchar(20)` | Proviene de `accountCode` en el request. |
| `@ContratoDescripcion` | `varchar(100)` | Proviene de `symbol` en el request. |
| `@ProductoAlias` | `varchar(100)` | Proviene de `productAlias` en el request. |
| `@TipoRuedaDescripcion` | `varchar(100)` | Proviene de `tradingSession` en el request. |
| `@EjecucionDescripcion` | `varchar(100)` | Proviene de `execType` en el request. |
| `@Fecha` | `date` | Proviene de `date` en el request. |
| `@EntidadDescripcion` | `varchar(100)` | Proviene de `entity` en el request. |
| `@MiembroCompensadorCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@CuentaCompensacionCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@CuentasRegistroCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |

### Observaciones tecnicas

- El controller parsea `date` con formato `yyyyMMdd`.
- La capa de negocio agrega filtros del usuario autenticado por miembro compensador, cuenta de compensacion y cuenta de registro.
- La funcion SQL filtra siempre por `Fecha = @Fecha`.
- Los filtros publicos opcionales (`accountCode`, `symbol`, `productAlias`, `tradingSession`, `execType`, `entity`) se aplican por igualdad exacta solo cuando vienen informados.
- La respuesta de la API no devuelve la fila SQL plana: agrupa por los datos generales de la tarifa y construye un arreglo `Trade` con `TradeNumber`, `ExecId`, `Qty`, `FeeAmt` y `BaseCurrencyTotal`.
- En la version vigente de `script.sql`, para el rol multicel el filtro de miembro compensador puede venir vacio y la funcion lo contempla explicitamente.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de tarifas devengadas agrupadas. |
| `400 BadRequest` | `date` no respeta el formato `yyyyMMdd` | `"Parameter 'Date' malformed. (YYYYMMDD)"` |

### Observaciones adicionales

- Aunque `date` figura como parametro obligatorio en la firma del controller, la validacion explicita observada es de formato, no de presencia.
- Si `date` llegara vacio o `null`, la capa de servicio enviaria `null` a la consulta; sin embargo, la funcion SQL compara `Fecha = @Fecha`, por lo que en la practica no deberia devolver resultados utiles sin fecha.
- No se relevo manejo de errores adicional mas alla del parseo de fecha.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-05-21` | Documento inicial del metodo `GET /PosTrade/AccruedFees`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
