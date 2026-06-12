# Garantias (MT506)

## Objetivo

El objetivo del metodo es brindar informacion sobre las garantias integradas de cada agente a traves del endpoint `GET /PosTrade/MT506`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `MT506` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/MT506?date={{date}}&alyc={{alyc}}&cim={{cim}}&nettingAccountCode={{nettingAccountCode}}&assetCode={{assetCode}}&format={{format}}&pageNumber={{pageNumber}}&pageSize={{pageSize}}` |

## Roles que pueden utilizarlo

Los roles que pueden consultar este metodo son los siguientes:

- `ADMIN`
- `CEL`
- `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha en formato `yyyyMMdd`. Si se informa una fecha anterior al dia actual, la API consulta la funcion historica. | Opcional |
| `alyc` | `string` | Filtro por miembro compensador. Se corresponde con `miembroCompensadorCodigoFromFilter` en la capa de negocio/SQL. | Opcional |
| `cim` | `string` | Filtro por cuenta de compensacion. Se corresponde con `cuentaCompensacionCodigoFromFilter`. | Opcional |
| `nettingAccountCode` | `string` | Filtro por cuenta de neteo. Se corresponde con `cuentaNeteoCodigoFromFilter`. | Opcional |
| `assetCode` | `string` | Filtro por codigo de activo. | Opcional |
| `format` | `string` | Formato de salida. Valores admitidos: `json`, `swift`. Si no se informa, la API usa `json`. | Opcional |
| `pageNumber` | `integer` | Numero de pagina. Si viene `null` o `0`, la API usa `1`. | Opcional |
| `pageSize` | `integer` | Cantidad de registros por pagina. Si viene `null` o `0`, la API usa `int.MaxValue`. | Opcional |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Consultar las garantias del dia `20260518` para el ALyC `ACSA`, la cuenta de compensacion `CIM001` y la cuenta de neteo `NET123`, en formato `json`.

**Request**

```http
GET {{url}}/PosTrade/MT506?date=20260518&alyc=ACSA&cim=CIM001&nettingAccountCode=NET123&format=json&pageNumber=1&pageSize=50
```

### Ejemplo de respuesta `json`

```json
[
  {
    "Reference": "Margenes",
    "Asset": "Deposito U$S",
    "TradeDate": "2026-05-18T00:00:00",
    "Party": "CIM001\\NET123",
    "Amount": 1500000.5000000000,
    "Narrative": "FGOT",
    "MRKT": 1.000000,
    "InstrumentCode": "DOLAR",
    "InternalInstrumentCode": "1",
    "SECV": 105000.0000000000,
    "SHAI": 100.00,
    "CurrencyCode": "ARS",
    "DepositoryAccount": "Banco HSBC CE u$s 0718-07636/6",
    "DepositoryAccountCode": "30",
    "ExpirationDate": "2026-05-31T00:00:00",
    "Isin": "ARXXXXXXX123",
    "ExternalAccountCode": "EXT-9988",
    "StockSourceCode": 1,
    "ClearingMemberCode": "ACSA"
  }
]
```

### Ejemplo de respuesta `swift`

La salida `swift` sigue devolviendo JSON, pero con nombres de campos mapeados a tags tipo SWIFT:

```json
[
  {
    ":20C:": "Margenes",
    ":22a:": "Deposito U$S\\1",
    ":98A:": "2026-05-18T00:00:00",
    ":95a:": "CIM001\\NET123",
    ":19A:": 1500000.5000000000,
    ":70D:": "FGOT",
    ":19B:": 1.000000,
    ":35B:": "DOLAR",
    ":36B:": 105000.0000000000,
    ":92a:": 100.00,
    "DepositoryAccountCode": "30",
    "ExpirationDate": "2026-05-31T00:00:00",
    "Isin": "ARXXXXXXX123",
    "ExternalAccountCode": "EXT-9988",
    "StockSourceCode": 1,
    "ClearingMemberCode": "ACSA",
    "16S": ""
  }
]
```

### Mapeo de campos en salida `swift`

| Campo API | Tag SWIFT/FIX aplicado por la capa de negocio |
|---|---|
| `Reference` | `:20C:` |
| `Asset` | `:22a:` |
| `TradeDate` | `:98A:` |
| `Party` | `:95a:` |
| `Amount` | `:19A:` |
| `Narrative` | `:70D:` |
| `MRKT` | `:19B:` |
| `InstrumentCode` | `:35B:` |
| `SECV` | `:36B:` |
| `SHAI` | `:92a:` |

## Funcion de la BD utilizada

Hay dos funciones, dependiendo de la fecha ingresada:

- Funcion actual: `[Activos].[GetReportePortfolioDetalleRofex]`
- Funcion historica: `[Activos].[GetReportePortfolioDetalleRofexHistorico]`

### Regla de seleccion

- Si `date` es menor a la fecha actual, la API usa `[Activos].[GetReportePortfolioDetalleRofexHistorico]`.
- Si `date` es la fecha actual o no se informa, la API usa `[Activos].[GetReportePortfolioDetalleRofex]`.

### Invocacion SQL de la funcion actual

```sql
SELECT *
FROM [Activos].[GetReportePortfolioDetalleRofex] (
   @fecha,
   @miembroCompensadorCodigoFromFilter,
   @cuentaCompensacionCodigoFromFilter,
   @cuentaNeteoCodigoFromFilter,
   @activoCodigo,
   @cuentasRegistroCodigo,
   @miembrosCompensadoresCodigo,
   @cuentasCompensacionCodigo,
   @participanteIntermediariocodigo,
   @partipantescodigo,
   @entidadBursatilCodigo,
   @paginaNumero,
   @registrosPorPagina
);
```

### Invocacion SQL de la funcion historica

```sql
SELECT *
FROM [Activos].[GetReportePortfolioDetalleRofexHistorico] (
   @fecha,
   @miembroCompensadorCodigoFromFilter,
   @cuentaCompensacionCodigoFromFilter,
   @cuentaNeteoCodigoFromFilter,
   @activoCodigo,
   @cuentasRegistroCodigo,
   @miembrosCompensadoresCodigo,
   @cuentasCompensacionCodigo,
   @participanteIntermediariocodigo,
   @partipantescodigo,
   @entidadBursatilCodigo,
   @paginaNumero,
   @registrosPorPagina
);
```

### Tabla de salida de la funcion

| Columna fn | Campo API | Tag SWIFT/FIX | Tipo dato SQL actual | Largo | Precision | Escala |
|---|---|---|---|---:|---:|---:|
| `Reference` | `Reference` | `:20C:` | `varchar` | 100 |  |  |
| `Asset` | `Asset` | `:22a:` | `varchar` | 100 |  |  |
| `TradeDate` | `TradeDate` | `:98A:` | `date` |  |  |  |
| `CompensationAccount` | parte de `Party` |  | `varchar` | 20 |  |  |
| `NettingAccount` | parte de `Party` |  | `varchar` | 20 |  |  |
| `Amount` | `Amount` | `:19A:` | `decimal` |  | 30 | 10 |
| `Narrative` | `Narrative` | `:70D:` | `varchar` | 100 |  |  |
| `MRKT` | `MRKT` | `:19B:` | `decimal` |  | 18 | 6 |
| `InstrumentCode` | `InstrumentCode` | `:35B:` | `varchar` | 20 |  |  |
| `SECV` | `SECV` | `:36B:` | `decimal` |  | 30 | 10 |
| `SHAI` | `SHAI` | `:92a:` | `decimal` |  | 18 | 2 |
| `InternalInstrumentCode` | `InternalInstrumentCode` | se concatena en `:22a:` para `swift` | `int` |  | 10 | 0 |
| `ExpirationDate` | `ExpirationDate` |  | `date` |  |  |  |
| `CurrencyCode` | `CurrencyCode` |  | `varchar` | 20 |  |  |
| `DepositoryAccount` | `DepositoryAccount` |  | `varchar` | 100 |  |  |
| `DepositoryAccountCode` | `DepositoryAccountCode` |  | `varchar` | 20 |  |  |
| `Isin` | `Isin` |  | `varchar` | 100 |  |  |
| `ExternalAccountCode` | `ExternalAccountCode` |  | `varchar` | 20 |  |  |
| `StockSourceCode` | `StockSourceCode` |  | `tinyint` | 1 | 3 | 0 |
| `ClearingMemberCode` | `ClearingMemberCode` |  | `varchar` | 20 |  |  |

> Nota: en la funcion historica existen diferencias de precision/escala en algunos campos (`Amount`, `MRKT`, `SECV`, `SHAI`), pero la capa API expone el mismo contrato funcional del metodo `MT506`.

## Filtros y parametros

### Parametros expuestos por la API

| Nombre | Tipo de dato | Observaciones |
|---|---|---|
| `alyc` | `string` | Filtro del request; se traduce a `miembroCompensadorCodigoFromFilter`. |
| `cim` | `string` | Filtro del request; se traduce a `cuentaCompensacionCodigoFromFilter`. |
| `nettingAccountCode` | `string` | Filtro del request; se traduce a `cuentaNeteoCodigoFromFilter`. |
| `assetCode` | `string` | Filtro del request; se traduce a `activoCodigo`. |
| `date` | `string` | Fecha del request en formato `yyyyMMdd`. |
| `format` | `string` | Formato de salida (`json` o `swift`). |
| `pageNumber` | `integer` | Paginacion logica aplicada por la API. |
| `pageSize` | `integer` | Paginacion logica aplicada por la API. |

### Parametros internos de la funcion SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@fecha` | `date` | Proviene del request (`date`). |
| `@miembroCompensadorCodigoFromFilter` | `varchar(20)` | Proviene del request (`alyc`). |
| `@cuentaCompensacionCodigoFromFilter` | `varchar(20)` | Proviene del request (`cim`). |
| `@cuentaNeteoCodigoFromFilter` | `varchar(20)` | Proviene del request (`nettingAccountCode`). |
| `@activoCodigo` | `varchar(20)` | Proviene del request (`assetCode`). |
| `@cuentasRegistroCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`AccountRegistry`). |
| `@miembrosCompensadoresCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`CompensationMember`). |
| `@cuentasCompensacionCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`CompensationAccount`). |
| `@participanteIntermediariocodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`Registrant`). |
| `@partipantescodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`Part`). |
| `@entidadBursatilCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`Exchange`). |
| `@paginaNumero` | `int` | Proviene del request (`pageNumber`). |
| `@registrosPorPagina` | `int` | Proviene del request (`pageSize`). |

### Observaciones sobre filtros

- `Party` no viene de base como columna independiente: la capa de negocio la construye con `CompensationAccount` y `NettingAccount`.
- `participanteIntermediariocodigo` y `partipantescodigo` forman parte de la firma de la funcion, pero hoy no participan del `WHERE` en las funciones `GetReportePortfolioDetalleRofex` y `GetReportePortfolioDetalleRofexHistorico`.
- La funcion actual pagina en SQL con `OFFSET ... FETCH`.
- En la funcion historica, la paginacion se encuentra comentada actualmente.

## Validaciones/comentarios en la API

- Si `date` tiene un formato invalido, la API devuelve `400 Bad Request` con el mensaje: `Parameter 'Date' malformed. (YYYYMMDD)`.
- Si `format` no es `json` ni `swift`, la API devuelve `400 Bad Request` con el mensaje: `Parameter 'Format' malformed.`.
- Si no se informa `format`, la API usa `json`.
- Si `pageNumber` viene `null` o `0`, la API usa `1`.
- Si `pageSize` viene `null` o `0`, la API usa `int.MaxValue`.
- Si no hay resultados, la API puede devolver `200 OK` con `null`.
- La salida `swift` no devuelve texto SWIFT plano: devuelve JSON con nombres de propiedades mapeados a tags tipo SWIFT.
- En la implementacion actual de `swift`, `16R` no aparece y `16S` se serializa vacio.
- En la salida `swift`, `Asset` se transforma concatenando `Asset + "\\" + InternalInstrumentCode`.
- En la salida `swift`, `InternalInstrumentCode`, `CurrencyCode` y `DepositoryAccount` se omiten del payload.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` |  | Version inicial del documento. |
| `2.0` | 2026-05-18 | Documento alineado con el controller `PosTrade/MT506`, la capa de negocio `GetReportePortfolioDetalleRofexBL` y las funciones SQL vigentes `[Activos].[GetReportePortfolioDetalleRofex]` y `[Activos].[GetReportePortfolioDetalleRofexHistorico]`. |
