# Movimientos Activos (MT536)

## Objetivo

El objetivo del metodo es brindar informacion sobre los movimientos de activos integrados en garantia a traves del endpoint `GET /PosTrade/MT536`.

El endpoint tiene dos variantes funcionales:

- camino estandar
- camino FCI / entidad bursatil

La seleccion entre ambos no depende de una ruta distinta, sino de la logica interna del backend.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `MT536` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/MT536?dateFrom={{dateFrom}}&dateTo={{dateTo}}&alyc={{alyc}}&clearingAccount={{clearingAccount}}&classification={{classification}}&format={{format}}&marketFCI={{marketFCI}}` |

## Roles que pueden utilizarlo

El controller expone el metodo con `[WAAuthorize]`, pero no define en esta capa una lista explicita de roles.

Por lo tanto:

- el acceso depende de la autorizacion configurada en la aplicacion
- si el usuario posee filtro de `Exchange`, la logica interna fuerza el camino FCI

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `dateFrom` | `string` | Fecha desde. Se parsea con `TryParseParameter`. | Obligatorio |
| `dateTo` | `string` | Fecha hasta. Se parsea con `TryParseParameter`. | Obligatorio |
| `alyc` | `string` | Filtro por miembro compensador. Internamente se parsea a `int?`. | Opcional |
| `clearingAccount` | `string` | Filtro por cuenta de compensacion. Internamente se parsea a `int?`. | Opcional |
| `classification` | `string` | Filtro por clasificacion del movimiento. Internamente se parsea a `byte?`. | Opcional |
| `format` | `string` | Formato de salida. Valores admitidos: `json`, `swift`. Si no se informa, la API usa `json`. | Opcional |
| `marketFCI` | `boolean` | Si vale `true`, fuerza el camino FCI aun cuando el usuario no sea entidad bursatil. Default: `false`. | Opcional |

## Ejemplo

### Ejemplo de request estandar

**Objetivo**  
Consultar movimientos de activos entre `2026-05-01` y `2026-05-18` para un ALyC y una cuenta de compensacion, en formato `json`.

**Request**

```http
GET {{url}}/PosTrade/MT536?dateFrom=2026-05-01&dateTo=2026-05-18&alyc=22&clearingAccount=1022&classification=1&format=json
```

### Ejemplo de respuesta `json` estandar

```json
[
  {
    "ClearingBusinessDate": "2026-05-18",
    "CollMovTransType": "Ingreso",
    "CollMovDescription": "Integracion de garantias",
    "Party": [
      {
        "ClearingMember": "22",
        "CompensationAccount": "1022",
        "NettingAccount": "1022"
      }
    ],
    "InstrumentCode": "DOLAR",
    "Instrument": "Deposito U$S",
    "DepositoryAccount": "Banco HSBC CE u$s 0718-07636/6",
    "Narrative": "FGOT",
    "Reference": "Margenes",
    "Quantity": 105000.0000000000,
    "SettlDate": "2026-05-18",
    "MRKT": 1.000000,
    "SHAI": 100.00,
    "Amount": 105000.0000000000,
    "InternalInstrumentCode": 1,
    "CurrencyCode": "ARS",
    "TrdGroup": 7781,
    "Isin": "ARXXXXXXX123",
    "Trades": [
      {
        "TradeNumber": 456789
      }
    ]
  }
]
```

### Ejemplo de request FCI

**Objetivo**  
Consultar movimientos por el camino FCI forzando `marketFCI=true`.

**Request**

```http
GET {{url}}/PosTrade/MT536?dateFrom=2026-05-01&dateTo=2026-05-18&alyc=22&clearingAccount=1022&classification=1&format=json&marketFCI=true
```

### Ejemplo de respuesta `json` FCI

```json
[
  {
    "ClearingBusinessDate": "2026-05-18",
    "CollMovTransType": "Ingreso",
    "CollMovDescription": "Integracion de garantias FCI",
    "Party": [
      {
        "ClearingMember": "22",
        "CompensationAccount": "1022",
        "NettingAccount": "1022"
      }
    ],
    "InstrumentCode": "FCI001",
    "Instrument": "Fondo Comun de Inversion Demo",
    "DepositoryAccount": "Cuenta CVSA 001",
    "Narrative": "FCI",
    "Reference": "Margenes",
    "Quantity": 1500.0000000000,
    "SettlDate": "2026-05-18",
    "MRKT": 1.250000,
    "SHAI": 95.00,
    "Amount": 1781.2500000000,
    "InternalInstrumentCode": 45,
    "CurrencyCode": "ARS",
    "TrdGroup": 9901,
    "Isin": "ARFCI0000123",
    "Trades": [
      {
        "TradeId": 998877
      }
    ]
  }
]
```

### Ejemplo de respuesta `swift`

La salida `swift` sigue devolviendo JSON, pero con propiedades renombradas a tags tipo SWIFT:

```json
[
  {
    ":98a:": "2026-05-18",
    ":70e:": "Ingreso//Integracion de garantias",
    ":95a:": "22//1022//1022",
    ":35b:": "DOLAR//Deposito U$S//1",
    ":97a:": "Banco HSBC CE u$s 0718-07636/6",
    ":20c:": "Margenes//FGOT",
    ":36b:": 105000.0000000000,
    ":99a:": "2026-05-18",
    ":90a:": "ARS//1.000000",
    ":19a:": 105000.0000000000,
    ":69a:": "2026-05-01//2026-05-18"
  }
]
```

### Mapeo de campos en salida `swift`

| Campo API | Tag SWIFT aplicado por la capa de negocio |
|---|---|
| `ClearingBusinessDate` | `:98a:` |
| `CollMovTransType + "//" + CollMovDescription` | `:70e:` |
| `Party` | `:95a:` |
| `InstrumentCode + "//" + Instrument + "//" + InternalInstrumentCode` | `:35b:` |
| `DepositoryAccount` | `:97a:` |
| `Reference + "//" + Narrative` | `:20c:` |
| `Quantity` | `:36b:` |
| `SettlDate` | `:99a:` |
| `CurrencyCode + "//" + MRKT` | `:90a:` |
| `Amount` | `:19a:` |
| `dateFrom + "//" + dateTo` | `:69a:` |

## Funcion de la BD utilizada

El endpoint usa una de estas dos funciones, segun la logica del negocio:

- Funcion estandar: `[Activos].[GetReporteMovimientosActivoPublicacion]`
- Funcion FCI / entidad bursatil: `[Activos].[GetReporteMovimientosActivoFCIPublicacion]`

### Regla de seleccion

- Si el usuario tiene filtro de `Exchange`, la API usa `[Activos].[GetReporteMovimientosActivoFCIPublicacion]`.
- Si `marketFCI = true`, la API usa `[Activos].[GetReporteMovimientosActivoFCIPublicacion]`.
- En cualquier otro caso, la API usa `[Activos].[GetReporteMovimientosActivoPublicacion]`.

### Invocacion SQL de la funcion estandar

```sql
SELECT *
FROM [Activos].[GetReporteMovimientosActivoPublicacion] (
   @fechaDesde,
   @fechaHasta,
   @miembroCompensadorCodigo,
   @cuentaCompensacionCodigo,
   @clasificacionID,
   @miembrosCompensadoresCodigo,
   @cuentasCompensacionCodigo
);
```

### Invocacion SQL de la funcion FCI

```sql
SELECT *
FROM [Activos].[GetReporteMovimientosActivoFCIPublicacion] (
   @FechaDesde,
   @FechaHasta,
   @MiembroCompensadorCodigo,
   @CuentaCompensacionCodigo,
   @ClasificacionID,
   @MiembrosCompensadoresCodigo,
   @CuentasCompensacionCodigo,
   @EntidadesBursatilesCodigos
);
```

### Tabla de salida de la funcion

| Columna fn | Campo API final | Tag SWIFT | Tipo dato SQL | Largo | Precision | Escala |
|---|---|---|---|---:|---:|---:|
| `MovimientoID` | no expuesto en salida agrupada |  | `int` |  | 10 | 0 |
| `Fecha` | `ClearingBusinessDate` | `:98a:` | `varchar` | 100 |  |  |
| `TipoMovimientoActivoDescripcion` | `CollMovTransType` | parte de `:70e:` | `varchar` | 100 |  |  |
| `ClasificacionMovimientoActivoDescripcion` | `CollMovDescription` | parte de `:70e:` | `varchar` | 100 |  |  |
| `CuentaCompensacionCodigo` | parte de `Party` | `:95a:` | `varchar` | 20 |  |  |
| `CuentaCompensacionDescripcion` | no expuesto en salida agrupada |  | `varchar` | 100 |  |  |
| `ActivoCodigo` | `InstrumentCode` | parte de `:35b:` | `varchar` | 20 |  |  |
| `ActivoDescripcion` | `Instrument` | parte de `:35b:` | `varchar` | 100 |  |  |
| `CuentaDepositariaDescripcion` | `DepositoryAccount` | `:97a:` | `varchar` | 100 |  |  |
| `FondoAlias` | `Narrative` | parte de `:20c:` | `varchar` | 20 |  |  |
| `FinalidadDescripcion` | `Reference` | parte de `:20c:` | `varchar` | 100 |  |  |
| `Cantidad` | `Quantity` | `:36b:` | `decimal` |  | 30 | 10 |
| `FechaVencimiento` | `SettlDate` | `:99a:` | `varchar` | 100 |  |  |
| `PrecioMercado` | `MRKT` | parte de `:90a:` | `decimal` |  | 18 | 6 |
| `Aforo` | `SHAI` | no se serializa en `swift` en forma aislada | `decimal` |  | 6 | 2 |
| `MontoCubierto` | `Amount` | `:19a:` | `decimal` |  | 30 | 10 |
| `MonedaIntegracionGarantiaDescripcion` | no expuesto en salida agrupada |  | `varchar` | 100 |  |  |
| `CuentaNeteoCodigo` | parte de `Party` | `:95a:` | `varchar` | 20 |  |  |
| `CuentaNeteoDescripcion` | no expuesto en salida agrupada |  | `varchar` | 100 |  |  |
| `MiembroCompensadorCodigo` | parte de `Party` | `:95a:` | `varchar` | 20 |  |  |
| `MiembroCompensadorDescripcion` | no expuesto en salida agrupada |  | `varchar` | 100 |  |  |
| `UltimoAforo` | no expuesto en salida agrupada |  | `decimal` |  | 6 | 2 |
| `UltimoPrecioMercado` | no expuesto en salida agrupada |  | `decimal` |  | 18 | 6 |
| `ActivoRelacionadoCodigo` | no expuesto en salida agrupada |  | `varchar` | 20 |  |  |
| `ActivoRelacionadoDescripcion` | no expuesto en salida agrupada |  | `varchar` | 100 |  |  |
| `CuentaRegistroEntidadBursatilCodigo` | no expuesto en salida agrupada |  | `varchar` | 100 |  |  |
| `ActivoId` | `InternalInstrumentCode` | parte de `:35b:` | `int` |  | 10 | 0 |
| `MonedaCodigo` | `CurrencyCode` | parte de `:90a:` | `varchar` | 20 |  |  |
| `GrupoOperacionCarteraID` | `TrdGroup` | no mapeado a `swift` | `int` |  | 10 | 0 |
| `OperacionCarteraNumero` | `Trades` | no mapeado a `swift` | `int` |  | 10 | 0 |
| `Isin` | `Isin` | no mapeado a `swift` | `varchar` | 100 |  |  |

### Diferencias funcionales entre camino estandar y FCI

- En el camino estandar, `Trades` se arma como lista de objetos con `TradeNumber`.
- En el camino FCI, `Trades` se arma como lista de objetos con `TradeId`.
- En la funcion FCI, `ActivoCodigo` puede resolverse como `FondoComunInversionCodigo` cuando el usuario es entidad bursatil.
- En la funcion FCI, el filtro contempla `EntidadesBursatilesCodigos`.

## Filtros y parametros

En esta seccion no se repiten los parametros publicos de la API.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos de la funcion SQL

#### Camino estandar

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@fechaDesde` | `date` | Se alimenta con el parametro API `dateFrom`. |
| `@fechaHasta` | `date` | Se alimenta con el parametro API `dateTo`. |
| `@miembroCompensadorCodigo` | `varchar(max)` | Se alimenta con el parametro API `alyc`. |
| `@cuentaCompensacionCodigo` | `varchar(max)` | Se alimenta con el parametro API `clearingAccount`. |
| `@clasificacionID` | `tinyint` | Se alimenta con el parametro API `classification`. |
| `@miembrosCompensadoresCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`CompensationMember`). |
| `@cuentasCompensacionCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`CompensationAccount`). |

#### Camino FCI

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@FechaDesde` | `date` | Se alimenta con el parametro API `dateFrom`. |
| `@FechaHasta` | `date` | Se alimenta con el parametro API `dateTo`. |
| `@MiembroCompensadorCodigo` | `varchar(max)` | Se alimenta con el parametro API `alyc`. |
| `@CuentaCompensacionCodigo` | `varchar(max)` | Se alimenta con el parametro API `clearingAccount`. |
| `@ClasificacionID` | `tinyint` | Se alimenta con el parametro API `classification`. |
| `@MiembrosCompensadoresCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`CompensationMember`). |
| `@CuentasCompensacionCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado (`CompensationAccount`). |
| `@EntidadesBursatilesCodigos` | `varchar(max)` | Proviene de filtros del usuario autenticado (`Exchange`). |

### Observaciones sobre filtros

- Si `dateFrom` o `dateTo` faltan, la API responde error.
- La capa de negocio obtiene filtros del usuario autenticado antes de ejecutar SQL.
- El camino FCI no depende solo del request: tambien puede activarse por filtros de `Exchange`.
- En SQL, `classification` no filtra cuando vale `0` o `null`.

## Validaciones/comentarios en la API

- Si `dateFrom` no se informa, la API devuelve `400 Bad Request` con `Invalid entry. Date From is required.`.
- Si `dateTo` no se informa, la API devuelve `400 Bad Request` con `Invalid entry. Date To is required.`.
- Si `format` no es `json` ni `swift`, la API devuelve `400 Bad Request` con `Parameter 'Format' malformed.`.
- Si no se informa `format`, la API usa `json`.
- `alyc`, `clearingAccount` y `classification` se parsean con `TryParseParameter`, por lo que un valor invalido tambien deriva en error `400`.
- La salida `swift` no devuelve un mensaje SWIFT plano: devuelve JSON con propiedades renombradas.
- En `swift`, solo se serializan los campos mapeados por `CustomDataContractResolverReporteMovimientosActivoPublicacion`; el resto se omite.
- La salida `json` esta agrupada, por lo que no expone todos los campos crudos que devuelve la funcion SQL.
- El endpoint puede devolver forma distinta del nodo `Trades` segun el camino:
  - estandar: `TradeNumber`
  - FCI: `TradeId`

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | 2026-05-19 | Documento inicial del metodo `PosTrade/MT536`, alineado con controller, business logic, salida `json`/`swift` y funciones SQL estandar y FCI vigentes. |
