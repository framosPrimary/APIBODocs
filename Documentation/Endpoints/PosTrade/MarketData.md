# Cotizaciones (MarketData)

## Objetivo

El objetivo del metodo es consultar informacion de mercado, precios, volumenes, intereses abiertos, cartera de indice y variantes proyectadas o transitorias a traves del endpoint `GET /PosTrade/MarketData`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `MarketData` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/MarketData?mdEntryType={{mdEntryType}}&ClearingBusinessDate={{ClearingBusinessDate}}&CFICode={{CFICode}}&MarketID={{MarketID}}&MarketSegmentID={{MarketSegmentID}}&Symbol={{Symbol}}&SecurityGroup={{SecurityGroup}}&SecurityType={{SecurityType}}&SecurityIDSource={{SecurityIDSource}}&Projected={{Projected}}&IncludeTransitory={{IncludeTransitory}}&SegmentID={{SegmentID}}` |

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
- `API Público`
- `EntidadBursatil`
- `CEL RM`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `mdEntryType` | `string` | Tipo de dato de mercado a consultar. | Obligatorio |
| `ClearingBusinessDate` | `string` | Fecha en formato `yyyyMMdd`. | Obligatorio |
| `CFICode` | `string` | Codigo CFI del contrato. | No |
| `MarketID` | `string` | Mercado. Ejemplos historicos: `ROFX`, `XMAB`, `UFEX`. | No |
| `MarketSegmentID` | `string` | Segmento de mercado. | No |
| `Symbol` | `string` | Simbolo del contrato. | No |
| `SecurityGroup` | `string` | Grupo de instrumento. | No |
| `SecurityType` | `string` | Tipo de instrumento. | No |
| `SecurityIDSource` | `string` | Fuente del identificador. | No |
| `Projected` | `boolean` | Si vale `true`, usa la variante proyectada. Default: `false`. | No |
| `IncludeTransitory` | `boolean` | Si vale `true`, usa la variante con ajustes transitorios. Default: `false`. | No |
| `SegmentID` | `string` | Segmento de negociacion. | No |

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/PosTrade/MarketData?mdEntryType=6&ClearingBusinessDate=20240313
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "MarketDepth": "0",
    "ClearingBusinessDate": "2024/03/13",
    "MarketID": "ROFX",
    "MarketSegmentID": "Monedas",
    "SegmentID": "MONE",
    "Instrument": [
      {
        "Symbol": "DLR072024",
        "SecurityID": "DLR072024",
        "SecurityIDSource": "4",
        "SecurityGroup": "DLR",
        "CFICode": "FXXXSX",
        "SecurityType": "FUT",
        "SettlType": "0",
        "MDFullGrp": [
          {
            "MDEntryType": "6",
            "MDEntryPx": 1105,
            "MDEntryDate": "2024/03/13",
            "MDEntryTime": "18:00:00",
            "Currency": "ARS",
            "CurrencyName": "Pesos",
            "PriceType": 1
          }
        ]
      }
    ]
  }
]
```

## Funcion de la BD utilizada

El endpoint usa una de estas tres funciones segun los flags del request:

- Funcion base: `[MarketData].[GetDatosDeMercado]`
- Funcion proyectada: `[MarketData].[GetDatosDeMercadoProyectado]`
- Funcion con ajustes transitorios: `[MarketData].[GetDatosDeMercadoAjustesTransitorios]`

### Regla de seleccion

- Si `Projected = true`, usa `[MarketData].[GetDatosDeMercadoProyectado]`.
- Si `Projected = false` e `IncludeTransitory = false`, usa `[MarketData].[GetDatosDeMercado]`.
- Si `Projected = false` e `IncludeTransitory = true`, usa `[MarketData].[GetDatosDeMercadoAjustesTransitorios]`.

### Columnas de salida mas relevantes

| Columna fn / DTO | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `MarketDepth` | `MarketDepth` | `int/varchar` | Nivel de profundidad informado por la funcion. |
| `ClearingBusinessDate` | `ClearingBusinessDate` | `date/string` | La BL lo expone agrupado en la cabecera. |
| `MarketID` | `MarketID` | `varchar` | Mercado. |
| `MarketSegmentID` | `MarketSegmentID` | `varchar` | Segmento de mercado. |
| `SegmentID` | `SegmentID` | `varchar` | Segmento de negociacion. |
| `Symbol` | `Instrument[].Symbol` | `varchar` | Agrupado dentro de `Instrument`. |
| `SecurityID` | `Instrument[].SecurityID` | `varchar` | Identificador del instrumento. |
| `SecurityIDSource` | `Instrument[].SecurityIDSource` | `varchar` | Fuente del identificador. |
| `SecurityGroup` | `Instrument[].SecurityGroup` | `varchar` | Grupo del instrumento. |
| `CFICode` | `Instrument[].CFICode` | `varchar` | Codigo CFI. |
| `SecurityType` | `Instrument[].SecurityType` | `varchar` | Tipo de instrumento. |
| `SettlType` | `Instrument[].SettlType` | `varchar` | Tipo de liquidacion. |
| `MDEntryType` | `MDFullGrp[].MDEntryType` | `varchar` | Tipo de dato de mercado solicitado. |
| `MDEntryPx` | `MDFullGrp[].MDEntryPx` | `decimal` | Precio/valor informado. |
| `MDEntrySize` | `MDFullGrp[].MDEntrySize` | `decimal` | Tamano o volumen. |
| `MDEntryDate` | `MDFullGrp[].MDEntryDate` | `date/string` | Fecha del dato de mercado. |
| `MDEntryTime` | `MDFullGrp[].MDEntryTime` | `string` | Hora del dato de mercado. |
| `Currency` | `MDFullGrp[].Currency` | `varchar` | Moneda. |
| `PriceType` | `MDFullGrp[].PriceType` | `int` | Tipo de precio. |
| `IsFinal` | `MDFullGrp[].IsFinal` | `bit` | Solo aparece en la variante con transitorios. |

## Filtros y parametros

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@MDEntryType` | `varchar` | Proviene de `mdEntryType`. |
| `@ClearingBusinessDate` | `date` | Proviene de `ClearingBusinessDate`. |
| `@CFICode` | `varchar` | Proviene de `CFICode`. |
| `@MarketID` | `varchar` | Proviene de `MarketID`. |
| `@MarketSegmentID` | `varchar` | Proviene de `MarketSegmentID`. |
| `@Symbol` | `varchar` | Proviene de `Symbol`. |
| `@SecurityGroup` | `varchar` | Proviene de `SecurityGroup`. |
| `@SecurityType` | `varchar` | Proviene de `SecurityType`. |
| `@SecurityIDSource` | `varchar` | Proviene de `SecurityIDSource`. |
| `@Projected` | `bit` | Proviene de `Projected`. |
| `@SegmentID` | `varchar` | Proviene de `SegmentID`. |

### Observaciones tecnicas

- El controller exige `mdEntryType` y `ClearingBusinessDate`.
- La BL agrupa la salida en tres niveles: cabecera de mercado, `Instrument` y `MDFullGrp`.
- La variante `IncludeTransitory=true` agrega `IsFinal` dentro de `MDFullGrp`.
- El parametro `Projected` tiene prioridad sobre `IncludeTransitory`; si ambos se envian en `true`, la BL toma la funcion proyectada.
- Si no hay datos, la BL devuelve `null`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Estructura agrupada por mercado e instrumento. |
| `400 BadRequest` | `mdEntryType` ausente | `"Parameter 'MDEntryType' is required."` |
| `400 BadRequest` | `ClearingBusinessDate` ausente | `"Parameter 'ClearingBusinessDate' is required."` |
| `400 BadRequest` | `ClearingBusinessDate` invalida | `"Parameter 'ClearingBusinessDate' malformed. (YYYYMMDD)"` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `GET /PosTrade/MarketData`, alineado con controller, business logic, roles relevados y funciones SQL vigentes. |
