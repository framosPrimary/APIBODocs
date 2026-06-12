# Margenes requeridos (MarginRequirementReport)

## Objetivo

El objetivo del metodo es consultar los margenes requeridos publicados por ACyRSA a traves del endpoint `GET /PosTrade/MarginRequirementReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `MarginRequirementReport` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/MarginRequirementReport?date={{date}}&viewDetails={{viewDetails}}` |

## Roles que pueden utilizarlo

- `Multi Cel`
- `MC RM`
- `Administrador`
- `AN`
- `MC`
- `xComitente`
- `Clearing`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `date` | `string` | Fecha a consultar en formato `yyyyMMdd`. | Obligatorio |
| `viewDetails` | `boolean` | Indica si la salida debe venir desglosada por grupo de producto. `false` devuelve la vista resumida y `true` la detallada. | No |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Consultar los margenes requeridos del dia `20260521` sin desglose por grupo de producto.

```http
GET {{url}}/PosTrade/MarginRequirementReport?date=20260521&viewDetails=false
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "ClearingMemberCode": "123",
    "ClearingMember": "Agente",
    "Date": "2026-05-21",
    "ProductGroupCurrencyQuotation": 1.000000,
    "ProductGroupCurrencyTotal": -16800000.000000,
    "ProductGroupCurrency": "Pesos",
    "Accounts": [
      {
        "CompensationAccount": "Agente",
        "CompensationAccountCode": "54500",
        "SubAccounts": [
          {
            "NettingAccount": "COMITENTE",
            "NettingAccountCode": "28596",
            "References": [
              {
                "Reference": "Margenes",
                "Currency": "Pesos",
                "Margin": -16800000.000000,
                "OptionAmount": -111111.000000,
                "InterTempAmount": -1253.550000
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

- Funcion utilizada cuando `viewDetails = false`: `[Compensacion].[GetReporteMargenPublicacionByFecha]`
- Funcion utilizada cuando `viewDetails = true`: `[Compensacion].[GetReporteMargenPublicacionDetalleByFecha]`

### Invocacion SQL

```sql
SELECT *
FROM [Compensacion].[GetReporteMargenPublicacionByFecha] (
    @fecha,
    @miembroCompensadorCodigo,
    @CuentaCompensacionCodigo
);
```

```sql
SELECT *
FROM [Compensacion].[GetReporteMargenPublicacionDetalleByFecha] (
    @fecha,
    @miembroCompensadorCodigo,
    @CuentaCompensacionCodigo
);
```

### Tabla de salida de la consulta

| Columna fn / modelo | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `ClearingMemberCode` | `ClearingMemberCode` | `varchar` | Codigo del miembro compensador. |
| `ClearingMember` | `ClearingMember` | `varchar` | Descripcion del miembro compensador. |
| `Date` | `Date` | `date` | Se expone formateada como `yyyy-MM-dd` en la agrupacion final. |
| `ProductGroupCurrencyQuotation` | `ProductGroupCurrencyQuotation` | `decimal` | Cotizacion de la moneda de publicacion. |
| `ProductGroupCurrencyTotal` | `ProductGroupCurrencyTotal` | `decimal` | Total en moneda de publicacion. |
| `ProductGroupCurrency` | `ProductGroupCurrency` | `varchar` | Moneda de publicacion. |
| `CompensationAccount` | `Accounts[].CompensationAccount` | `varchar` | Descripcion de la cuenta de compensacion. |
| `CompensationAccountCode` | `Accounts[].CompensationAccountCode` | `varchar` | Codigo de la cuenta de compensacion. |
| `NettingAccount` | `Accounts[].SubAccounts[].NettingAccount` | `varchar` | Descripcion de la cuenta de neteo. |
| `NettingAccountCode` | `Accounts[].SubAccounts[].NettingAccountCode` | `varchar` | Codigo de la cuenta de neteo. |
| `Reference` | `Accounts[].SubAccounts[].References[].Reference` | `varchar` | Finalidad del margen. |
| `Currency` | `Accounts[].SubAccounts[].References[].Currency` | `varchar` | Moneda del margen. |
| `Margin` | `Accounts[].SubAccounts[].References[].Margin` | `decimal` | En la vista resumida se informa sumado por agrupacion; en la detallada se expone por grupo de producto. |
| `OptionAmount` | `Accounts[].SubAccounts[].References[].OptionAmount` | `decimal` | Monto de opcion. |
| `InterTempAmount` | `Accounts[].SubAccounts[].References[].InterTempAmount` | `decimal` | Monto intertemporal. |
| `ProductGroup` | `ProductGroup` | `varchar` | Solo aplica cuando `viewDetails = true`. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes de SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@fecha` | `date` | Proviene de `date` en el request. |
| `@miembroCompensadorCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@CuentaCompensacionCodigo` | `varchar(max)` | Proviene de filtros del usuario autenticado. |

### Observaciones tecnicas

- El controller exige que `date` venga informado; no toma fecha actual ni ultima fecha disponible cuando se omite.
- Cuando `viewDetails = false`, la API consulta `[Compensacion].[GetReporteMargenPublicacionByFecha]` y agrupa la salida por miembro compensador, cuenta de compensacion, cuenta de neteo, moneda y referencia.
- Cuando `viewDetails = true`, la API consulta `[Compensacion].[GetReporteMargenPublicacionDetalleByFecha]` e incorpora `ProductGroup` en el agrupamiento de primer nivel.
- Ambas funciones SQL filtran por `Fecha = @fecha`.
- Ambas funciones SQL restringen resultados segun los filtros del usuario autenticado por miembro compensador y/o cuenta de compensacion.
- La funcion resumida calcula `Margin` con una logica agregada: si la suma de `SumaMonto` es positiva, devuelve `0`; en caso contrario devuelve la suma negativa.
- La funcion detallada expone `Margin = ISNULL(SumaMonto, 0)` sin aplicar esa normalizacion a cero.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista agrupada de margenes requeridos. |
| `400 BadRequest` | Falta el parametro `date` | `"Invalid entry. Date is required."` |
| `400 BadRequest` | `date` no respeta el formato `yyyyMMdd` | `"Parameter 'date' malformed. (YYYYMMDD)"` |

### Observaciones adicionales

- `viewDetails` es opcional y su valor por defecto es `false`.
- El response de la API no expone la fila SQL plana, sino una estructura agrupada y anidada.
- El documento general `PrimaryAPI-BO_v1.6.4.md` describe `date` como obligatorio, y en este endpoint eso coincide con el comportamiento real del controller.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-05-21` | Documento inicial del metodo `GET /PosTrade/MarginRequirementReport`, alineado con controller, business logic, roles relevados y funciones SQL vigentes. |
