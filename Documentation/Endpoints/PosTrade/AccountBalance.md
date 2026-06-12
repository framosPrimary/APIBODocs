# Balance de Saldos (AccountBalance)

## Objetivo

El objetivo del metodo es consultar el balance de saldos contables acumulados por agente y cuenta a traves del endpoint `GET /PosTrade/AccountBalance`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `AccountBalance` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/AccountBalance?date={{date}}&alyc={{alyc}}&clearingAccount={{clearingAccount}}&currency={{currency}}&accountTypeCode={{accountTypeCode}}&showAccountingEndOfDay={{showAccountingEndOfDay}}` |

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
| `date` | `string` | Fecha de consulta en formato `yyyyMMdd`. | Obligatorio |
| `alyc` | `string` | Codigo de ALyC / miembro compensador a filtrar. | No |
| `clearingAccount` | `string` | Codigo de cuenta de compensacion a filtrar. | No |
| `currency` | `string` | Moneda a filtrar. Ejemplos documentados: `ARS`, `USD G`, `USD R`, `USD D`, `USD C`, `EUR`, `UYU`. | No |
| `accountTypeCode` | `string` | Codigo del rubro o cuenta administrativa. Ejemplos documentados: `11`, `12`, `21`, `22`, `31`, `41`, `43`, `47`, `90`. | No |
| `showAccountingEndOfDay` | `boolean` | Si vale `true`, muestra el saldo del dia completo consultado. Si vale `false`, para la fecha actual corta al horario actual del sistema. Default: `false`. | No |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Consultar los balances de saldos de margenes en pesos para el ALyC `123` al `20260521`.

```http
GET {{url}}/PosTrade/AccountBalance?date=20260521&alyc=123&currency=ARS&accountTypeCode=21
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
[
  {
    "ClearingMemberCode": "123",
    "ClearingMember": "SOCIEDAD DE BOLSA S.A.",
    "ClearingAccountCode": "10123",
    "Currency": "ARS",
    "AccountTypeCode": "21",
    "AccountType": "Margenes",
    "AccountingAccountCode": "42132510123",
    "AccountingAccount": "Margenes $ SOCIEDAD DE BOLSA S.A.",
    "Balance": 7554123,
    "AccountOwner": "123/1325/10123"
  }
]
```

## Funcion de la BD utilizada

- Funcion utilizada: `[Contabilidad].[GetReporteSaldosContableFecha]`

### Invocacion SQL

```sql
SELECT *
FROM [Contabilidad].[GetReporteSaldosContableFecha] (
    @Fecha,
    @MiembroCompensadorCodigo,
    @CuentaCompensacionCodigo,
    @RubroCuentaAdministrativaCodigo,
    @MonedaSigla,
    @MostrarContabilidadFinDelDia,
    @cuentaCompensacionCodigoFiltroUsuario,
    @MiembroCompensadorCodigoFiltroUsuario,
    @CuentaRegistroCodigoFiltroUsuario
);
```

### Tabla de salida de la consulta

| Columna fn / alias SQL | Campo API | Tipo dato | Observaciones |
|---|---|---|---|
| `ClearingMemberCode` | `ClearingMemberCode` | `varchar` | Codigo del miembro compensador. |
| `ClearingMember` | `ClearingMember` | `varchar` | Descripcion del miembro compensador. |
| `ClearingAccountCode` | `ClearingAccountCode` | `varchar` | Codigo de la cuenta de compensacion. |
| `Currency` | `Currency` | `varchar` | Moneda del saldo. |
| `AccountTypeCode` | `AccountTypeCode` | `varchar` | Codigo del rubro contable. |
| `AccountType` | `AccountType` | `varchar` | Descripcion del rubro contable. |
| `AccountingAccountCode` | `AccountingAccountCode` | `varchar` | Codigo de la cuenta administrativa/contable. |
| `AccountingAccount` | `AccountingAccount` | `varchar` | Descripcion de la cuenta administrativa/contable. |
| `Balance` | `Balance` | `decimal` | Saldo acumulado calculado como `SUM(Haber - Debe)`. |
| `AccountOwner` | `AccountOwner` | `varchar` | Composicion `Participante/CuentaCompensacion/CuentaRegistro`. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes de SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@Fecha` | `date` | Proviene de `date` en el request. |
| `@MiembroCompensadorCodigo` | `varchar(20)` | Proviene de `alyc` en el request. |
| `@CuentaCompensacionCodigo` | `varchar(20)` | Proviene de `clearingAccount` en el request. |
| `@RubroCuentaAdministrativaCodigo` | `varchar(20)` | Proviene de `accountTypeCode` en el request. |
| `@MonedaSigla` | `varchar(20)` | Proviene de `currency` en el request. |
| `@MostrarContabilidadFinDelDia` | `bit` | Proviene de `showAccountingEndOfDay` en el request. |
| `@cuentaCompensacionCodigoFiltroUsuario` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@MiembroCompensadorCodigoFiltroUsuario` | `varchar(max)` | Proviene de filtros del usuario autenticado. |
| `@CuentaRegistroCodigoFiltroUsuario` | `varchar(max)` | Proviene de filtros del usuario autenticado. |

### Observaciones tecnicas

- La capa de negocio agrega filtros del usuario autenticado por cuenta de compensacion, miembro compensador y cuenta de registro.
- La funcion SQL consulta `Contabilidad.MovimientoPublicacionHistorico` y calcula saldo acumulado con `SUM(Haber - Debe)`.
- El filtro de fecha no busca solo movimientos del dia: devuelve saldo acumulado hasta la fecha consultada.
- Si `date` es una fecha anterior a hoy, la funcion incluye movimientos con `Fecha < @Fecha` y tambien `Fecha = @Fecha`.
- Si `date` es la fecha actual y `showAccountingEndOfDay = false`, la funcion incluye los movimientos del dia solo hasta `Hora <= GETDATE()`.
- Si `date` es la fecha actual y `showAccountingEndOfDay = true`, la funcion incluye el cierre completo del dia consultado.
- El filtro por moneda (`@MonedaSigla`) se aplica en el `SELECT` final, despues del agrupamiento previo.
- En la funcion SQL, los filtros `alyc` y `clearingAccount` contemplan tambien los valores `NULL` o `0` como equivalentes a "sin filtro".
- `AccountOwner` no existe como campo persistido: se construye con `ParticipanteCodigo`, `CuentaCompensacionCodigo` y `CuentaCodigo`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Lista de balances de saldos. |
| `400 BadRequest` | `date` no respeta el formato `yyyyMMdd` | `"Parameters 'ClearingBusinessDate' malformed. (YYYYMMDD)"` |

### Observaciones adicionales

- Aunque el endpoint publico expone el parametro `date`, el mensaje de error sigue mencionando `ClearingBusinessDate`.
- `showAccountingEndOfDay` tiene default `false` en la firma del controller.
- Aunque `date` debe considerarse obligatorio funcionalmente, la validacion explicita observada en controller es de formato cuando viene informado.
- Si `date` llega vacio o `null`, el controller no devuelve `400`; sin embargo, la funcion SQL compara contra `@Fecha`, por lo que no deberian obtenerse resultados utiles.
- No se relevo logica adicional de validacion para `alyc`, `clearingAccount`, `currency` ni `accountTypeCode`.
- Si no hay coincidencias, la API responde `200 OK` con lista vacia.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-03` | Documento inicial del metodo `GET /PosTrade/AccountBalance`, alineado con controller, business logic, roles relevados y funcion SQL vigente. |
