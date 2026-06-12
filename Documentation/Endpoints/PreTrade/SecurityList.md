# Lista de Instrumentos (SecurityList)

## Objetivo

El objetivo del metodo es consultar la lista de instrumentos disponibles a traves del endpoint `GET /PreTrade/SecurityList`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `SecurityList` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PreTrade/SecurityList?securityIDSource={{securityIDSource}}&securityType={{securityType}}&securityExchange={{securityExchange}}&SecurityStatus={{SecurityStatus}}&securityGroup={{securityGroup}}&marketID={{marketID}}&marketSegmentID={{marketSegmentID}}&cFICode={{cFICode}}&symbol={{symbol}}&securityID={{securityID}}&viewSpecs={{viewSpecs}}&segmentID={{segmentID}}` |

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
| `securityIDSource` | `string` | Origen del identificador del instrumento. | No |
| `securityType` | `string` | Tipo de security a filtrar. | No |
| `securityExchange` | `string` | Mercado o entidad bursatil a filtrar. | No |
| `SecurityStatus` | `string` | Estado de la security. | No |
| `securityGroup` | `string` | Grupo de producto a filtrar. | No |
| `marketID` | `string` | Mercado a consultar. Si no se informa, la logica toma un mercado por defecto. | No |
| `marketSegmentID` | `string` | Segmento de mercado a filtrar. | No |
| `cFICode` | `string` | Codigo CFI del instrumento. | No |
| `symbol` | `string` | Simbolo o descripcion corta del contrato. | No |
| `securityID` | `string` | Identificador del instrumento segun el origen indicado en `securityIDSource`. | No |
| `viewSpecs` | `boolean` | Indica si la consulta debe devolver la vista detallada (`true`) o la vista resumida (`false`). | No |
| `segmentID` | `string` | Codigo de segmento del instrumento. | No |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Consultar la lista de instrumentos del mercado `ROFX` para el simbolo `DLR`, sin vista detallada.

```http
GET {{url}}/PreTrade/SecurityList?marketID=ROFX&symbol=DLR&viewSpecs=false
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "MarketID": "ROFX",
    "MarketSegmentID": "Futuros Financieros",
    "SecurityRequestResult": 0,
    "SecListGrp": [
      {
        "Currency": "Pesos",
        "MarginAmt": null,
        "FixRange": null,
        "TempCharge": null,
        "SegmentID": "DLR",
        "Instrument": [
          {
            "Symbol": "DLR052026",
            "SecurityID": "DLR052026",
            "SecurityIDSource": "",
            "CFICode": "FXXXXX",
            "SecurityType": "Futuro",
            "SecuritySubType": "",
            "MaturityMonthYear": "202605",
            "MaturityDate": "2026-05-31",
            "StrikePrice": "0",
            "SecurityExchange": "A3 Mercados",
            "SecurityDesc": "DLR MAY 26",
            "SecurityStatus": "1",
            "UnitOfMeasureQty": 1000,
            "UnitOfMeasure": "USD",
            "ContractMultiplier": 1,
            "SecurityGroup": "Dolar",
            "SettlMethod": "C",
            "RelatedSymbol": null,
            "MinVolatility": null,
            "MidVolatility": null,
            "MaxVolatility": null,
            "RiskSecurityGroup": "Dolar",
            "ISINCode": null
          }
        ],
        "InstrmtLegSecListGrp": []
      }
    ]
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada cuando `viewSpecs = false`: `[Productos].[GetListaContratos]`
- Funcion utilizada cuando `viewSpecs = true`: `[Productos].[GetListaContratosDetallado]`

### Invocacion SQL

```sql
SELECT *
FROM [Productos].[GetListaContratos] (
    @SecurityIDSource,
    @SecurityType,
    @SecurityExchange,
    @SecurityStatus,
    @SecurityGroup,
    @MarketID,
    @MarketSegmentID,
    @CFICode,
    @Symbol,
    @SecurityID,
    @SegmentID
);
```

```sql
SELECT *
FROM [Productos].[GetListaContratosDetallado] (
    @SecurityIDSource,
    @SecurityType,
    @SecurityExchange,
    @SecurityStatus,
    @SecurityGroup,
    @MarketID,
    @MarketSegmentID,
    @CFICode,
    @Symbol,
    @SecurityID,
    @SegmentID
);
```

### Tabla de salida de la consulta

| Columna fn / modelo | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `MarketID` | `MarketID` | `varchar(10)` | Mercado agrupador de primer nivel. |
| `MarketSegmentID` | `MarketSegmentID` | `varchar(100)` | Segmento de mercado agrupador de primer nivel. |
| `SecurityRequestResult` | `SecurityRequestResult` | `int` | Resultado de la consulta de securities. |
| `Currency` | `SecListGrp[].Currency` | `varchar(100)` | Moneda de cotizacion. |
| `MarginAmt` | `SecListGrp[].MarginAmt` | `decimal` | En la vista resumida hoy puede venir `null` por definicion SQL. |
| `FixRange` | `SecListGrp[].FixRange` | `decimal` | Rango fijo del instrumento. |
| `TempCharge` | `SecListGrp[].TempCharge` | `decimal` | Cargo temporal. |
| `SegmentID` | `SecListGrp[].SegmentID` | `varchar(20)` | Segmento del instrumento. |
| `Symbol` | `Instrument[].Symbol` | `varchar(100)` | Simbolo o lista contrato. |
| `SecurityID` | `Instrument[].SecurityID` | `varchar(100)` | Identificador construido segun `securityIDSource`. |
| `SecurityIDSource` | `Instrument[].SecurityIDSource` | `varchar(100)` | Origen del identificador. |
| `CFICode` | `Instrument[].CFICode` | `varchar(100)` | Codigo CFI. |
| `SecurityType` | `Instrument[].SecurityType` | `varchar(100)` | Tipo de security. |
| `SecuritySubType` | `Instrument[].SecuritySubType` | `varchar(100)` | Subtipo o tipo de combinada. |
| `MaturityMonthYear` | `Instrument[].MaturityMonthYear` | `varchar(6)` | Mes y anio de vencimiento. |
| `MaturityDate` | `Instrument[].MaturityDate` | `varchar(100)` | Fecha de vencimiento. |
| `StrikePrice` | `Instrument[].StrikePrice` | `varchar(20)` | Precio de ejercicio. |
| `SecurityExchange` | `Instrument[].SecurityExchange` | `varchar` | Entidad bursatil o mercado. |
| `SecurityDesc` | `Instrument[].SecurityDesc` | `varchar(100)` | Descripcion del instrumento. |
| `SecurityStatus` | `Instrument[].SecurityStatus` | `char(1)` | Estado del instrumento. |
| `UnitOfMeasureQty` | `Instrument[].UnitOfMeasureQty` | `int` | Tamano del contrato. |
| `UnitOfMeasure` | `Instrument[].UnitOfMeasure` | `varchar(100)` | Unidad de medida. |
| `ContractMultiplier` | `Instrument[].ContractMultiplier` | `decimal` | Multiplicador del contrato. |
| `SecurityGroup` | `Instrument[].SecurityGroup` | `varchar(100)` | Grupo de producto. |
| `SettlMethod` | `Instrument[].SettlMethod` | `char(1)` | Metodo de liquidacion. |
| `RelatedSymbol` | `Instrument[].RelatedSymbol` | `varchar` | Simbolo relacionado, por ejemplo referencia OTC. |
| `MinVolatility` | `Instrument[].MinVolatility` | `decimal` | Volatilidad minima. |
| `MidVolatility` | `Instrument[].MidVolatility` | `decimal` | Volatilidad media. |
| `MaxVolatility` | `Instrument[].MaxVolatility` | `decimal` | Volatilidad maxima. |
| `RiskSecurityGroup` | `Instrument[].RiskSecurityGroup` | `varchar` | Grupo de riesgo. |
| `ISINCode` | `Instrument[].ISINCode` | `varchar` | Codigo ISIN. |
| `SettlementDate` | `Instrument[].SettlementDate` | `varchar` | Solo aplica cuando `viewSpecs = true`. |
| `CurrencySymbol` | `Instrument[].CurrencySymbol` | `varchar` | Solo aplica cuando `viewSpecs = true`. |
| `OptionStyle` | `Instrument[].OptionStyle` | `varchar` | Solo aplica cuando `viewSpecs = true`. |
| `MarketDataReference` | `Instrument[].MarketDataReference` | `bit` | Solo aplica cuando `viewSpecs = true`. |
| `Leg...` | `InstrmtLegSecListGrp[]` | varios | Datos de patas para instrumentos combinados. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes de SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@SecurityIDSource` | `varchar(100)` | Proviene de `securityIDSource` en el request. |
| `@SecurityType` | `varchar(100)` | Proviene de `securityType` en el request. |
| `@SecurityExchange` | `varchar(10)` | Proviene de `securityExchange` en el request. |
| `@SecurityStatus` | `char(1)` | Proviene de `SecurityStatus` en el request. |
| `@SecurityGroup` | `varchar(100)` | Proviene de `securityGroup` en el request. |
| `@MarketID` | `varchar(10)` | Proviene de `marketID` en el request. |
| `@MarketSegmentID` | `varchar(100)` | Proviene de `marketSegmentID` en el request. |
| `@CFICode` | `varchar(100)` | Proviene de `cFICode` en el request. |
| `@Symbol` | `varchar(100)` | Proviene de `symbol` en el request. |
| `@SecurityID` | `varchar(100)` | Proviene de `securityID` en el request. |
| `@SegmentID` | `varchar(20)` | Proviene de `segmentID` en el request. |

### Observaciones tecnicas

- El controller no implementa validaciones explicitas de formato ni obligatoriedad sobre estos parametros.
- Cuando `viewSpecs = false`, la API usa `[Productos].[GetListaContratos]`.
- Cuando `viewSpecs = true`, la API usa `[Productos].[GetListaContratosDetallado]`.
- Si `marketID` no se informa, ambas funciones determinan un mercado por defecto tomando la primera `EntidadBursatil` con `TipoEntidadBursatilID = 1`.
- Ambas funciones filtran por igualdad exacta solo cuando el parametro viene informado; si viene vacio o `null`, ese filtro no se aplica.
- La capa de negocio agrupa la salida en tres niveles principales: resultado general, `SecListGrp`, `Instrument` y `InstrmtLegSecListGrp`.
- No se relevaron filtros adicionales por usuario autenticado para este endpoint.
- Si no hay resultados, la capa de negocio devuelve `null` en lugar de una lista vacia.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Estructura agrupada de instrumentos o `null` si no hay resultados. |

### Observaciones adicionales

- `viewSpecs` es opcional y su valor por defecto es `false`.
- `SecurityStatus` llega con mayuscula inicial en el querystring del controller actual.
- La documentacion historica no siempre muestra `securityID` ni `segmentID`, pero ambos parametros hoy forman parte del contrato real.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-05-21` | Documento inicial del metodo `GET /PreTrade/SecurityList`, alineado con controller, capa de negocio, roles relevados y funciones SQL vigentes. |
