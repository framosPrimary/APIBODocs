# Instrucciones de Garantia (NewCollateralReport - POST)

## Objetivo

El objetivo del metodo es permitir el alta de instrucciones de garantia a traves del endpoint `POST /PosTrade/NewCollateralReport`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `NewCollateralReport` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PosTrade/NewCollateralReport` |

## Roles que pueden utilizarlo

El controller expone el metodo con `[WAAuthorize]`, pero no define en esta capa una lista explicita de roles.

Por lo tanto:

- el acceso depende de la autorizacion configurada en la aplicacion
- la BL completa `RootParties` usando los filtros del usuario autenticado
- la obligatoriedad de `CompensationAccount` depende tambien del contexto funcional del usuario autenticado (`MC`, multi-CEL, FCI)

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `InternalInstrumentCode` | `integer` | Codigo interno del activo. | Obligatorio |
| `Currency` | `string` | Codigo de moneda. Debe existir como moneda activa. | Obligatorio |
| `CollAppIType` | `integer` | Finalidad de la instruccion. | Obligatorio |
| `OriginDepositoryAccountCode` | `integer` | Cuenta depositaria origen. | Obligatorio |
| `DestinationDepositoryAccountCode` | `integer` | Cuenta depositaria destino. | Obligatorio |
| `Side` | `integer` | Lado de la instruccion. Valores admitidos: `1` = ingreso, `2` = egreso. | Obligatorio |
| `OriginType` | `integer` | Tipo de movimiento. La combinacion valida depende de `Side`. | Obligatorio |
| `ExternalCollRptID` | `string` | Identificador externo de la instruccion en el sistema cliente. | Obligatorio |
| `CompensationAccount` | `string` | Cuenta de compensacion. La BL la exige para contexto `MC` y para usuarios multi-CEL. | Condicional |
| `Notes` | `string` | Observaciones de la instruccion. | Opcional |
| `ShareholderNumber` | `string` | Numero de cuotapartista. Se exige cuando el activo/finalidad corresponde a FCI. | Condicional |
| `ShareholderBusinessName` | `string` | Razon social del cuotapartista. Se exige cuando el activo/finalidad corresponde a FCI. | Condicional |
| `ShareholderTIN` | `string` | CUIT/CUIL del cuotapartista. Se exige cuando el activo/finalidad corresponde a FCI. | Condicional |
| `Details` | `array` | Lista de detalles de la instruccion. | Obligatorio en la practica funcional |
| `Details[].Account` | `string` | Cuenta de neteo/cuenta de registro del detalle. | Obligatorio |
| `Details[].Fund` | `string` | Fondo asociado al detalle. | Opcional |
| `Details[].Qty` | `string` | Cantidad del detalle. Existe en el modelo y se usa en SQL, aunque la validacion local no la marca como obligatoria. | Opcional segun validacion API |

### Valores admitidos para `OriginType`

| `Side` | `OriginType` valido | Significado |
|---|---:|---|
| `1` | `1` | `IngresoTransferenciaNueva` |
| `1` | `2` | `IngresoCompraInterconexion` |
| `2` | `3` | `EgresoTransferenciaNueva` |
| `2` | `4` | `EgresoVentaValores` |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Ingresar una instruccion de garantia de ingreso para el activo `4`, con identificador externo `GAR-20260519-001`.

```http
POST {{url}}/PosTrade/NewCollateralReport
Content-Type: application/json
Authorization: Bearer {{token}}
```

```json
{
  "InternalInstrumentCode": 4,
  "Side": 1,
  "ExternalCollRptID": "GAR-20260519-001",
  "Currency": "ARS",
  "CollAppIType": 2,
  "OriginType": 1,
  "OriginDepositoryAccountCode": 6649,
  "DestinationDepositoryAccountCode": 8051,
  "Notes": "Alta de garantia de prueba",
  "CompensationAccount": "1001",
  "Details": [
    {
      "Account": "1001",
      "Fund": "FGOT",
      "Qty": "1000"
    }
  ]
}
```

### Ejemplo de respuesta

La respuesta exitosa del `POST` es un entero simple con el `CollRptID` generado internamente:

```json
123456
```

## Funcion de la BD utilizada

- Procedure utilizada: `[Contribucion].[InsertCollateralReport]`

### Invocacion SQL

```sql
EXEC [Contribucion].[InsertCollateralReport]
    @CollateralReport = @json;
```

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes de SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@CollateralReport` | `varchar(max)` | JSON serializado desde el body del `POST`. |

### Observaciones tecnicas

- `RootParties` no lo informa el cliente: la BL lo completa con filtros del usuario autenticado.
- Para usuarios con filtro de miembro compensador, la BL genera `RootPartyRole = "4"`.
- Para usuarios con filtro de cuenta de compensacion, la BL genera `RootPartyRole = "38"`.
- En contexto multi-CEL, la BL valida que `CompensationAccount` pertenezca al conjunto de cuentas permitidas para el usuario.
- En SQL se consume `Notes` como `Observaciones`; el nombre publico correcto del campo en el JSON actual es `Notes`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | Entero simple con el `CollRptID` generado. |
| `400 Bad Request` | Body nulo o JSON invalido para el controller | `"Json format invalid"` |
| `400 Bad Request` | Faltan campos obligatorios siempre | `"Field ... cannot be null."` |
| `400 Bad Request` | Faltan campos obligatorios condicionales (`FCI`, `MC`, multi-CEL`) | `"Field ... cannot be null."` |
| `400 Bad Request` | Combinacion invalida de `Side` y `OriginType` | `"The combination entered is invalid. Origin Type / Side ..."` |
| `400 Bad Request` | `CompensationAccount` invalida en multi-CEL | `"Compensation account ... invalid."` |
| `400 Bad Request` | `OriginDepositoryAccountCode` invalido | `"Invalid OriginDepositoryAccountCode."` |
| `400 Bad Request` | `DestinationDepositoryAccountCode` invalido | `"Invalid DestinationDepositoryAccountCode."` |
| `400 Bad Request` | `InternalInstrumentCode` invalido | `"Invalid InternalInstrumentCode."` |
| `400 Bad Request` | `Currency` invalida | `"Invalid Currency."` |
| `400 Bad Request` | Cuentas sin CV asignada | Mensaje del estilo `"The following/s account/s ... does not have the CV account assigned."` |
| `400 Bad Request` | Relacion cuenta de compensacion / cuenta de registro incorrecta | `"The Compensation Account-Registry Account relationship is not correct."` |
| `400 Bad Request` | Cuenta de compensacion inactiva | `"Inactive CompensationAccount"` |
| `503 ServiceUnavailable` | Falla operativa no mapeada en persistencia | `"The operation could not be performed."` |

### Observaciones adicionales de validacion

- La validacion de obligatoriedad se ejecuta en la BL antes de persistir.
- `Details[].Qty` existe en el modelo y es usado por SQL para calcular cantidades/montos, pero la validacion local del API solo marca `Details[].Account` como obligatorio.
- El PDF general viejo usa `Observations`, pero el contrato real actual usa `Notes`.
- La BL agrega `UserName` y `UserMail` internamente; no forman parte del contrato publico que el cliente debe enviar.

## Comentario sobre `trdType`

- Este endpoint no expone ni valida un campo `trdType`.
- No se encontro mapeo local de `trdType` en el controller, el modelo `ApiBO.Model.Proxy.CollateralReport` ni la procedure `[Contribucion].[InsertCollateralReport]`.
- Por lo tanto, no es posible documentar `trdType` como parte del contrato propio de `NewCollateralReport`.
- Como contexto general de la API Postrade, el documento `PrimaryAPI-BO_v1.6.4.md` incluye un diccionario global de `TrdType`, pero ese catalogo no es contractual para este endpoint.

### Referencia general no contractual de `TrdType` en `PrimaryAPI-BO_v1.6.4.md`

| `TrdType` | Significado segun documento general |
|---|---|
| `0` | Interferencia de ofertas |
| `1` | Block Trade |
| `2` | Apertura entrega |
| `3` | Asignacion / Asignacion BT |
| `5` | Registracion garantizado |
| `10` | Correccion |
| `11` | Correccion TIVA |
| `22` | Registracion Bilateral |
| `29` | Caida por vencimiento |
| `31` | Cierre oferta de entrega |
| `38` | Registracion Garantizado Corredor |
| `42` | Derivacion Garantizado |
| `45` | Ejercicio manual / Ejercicio de opcion |
| `51` | Asignacion Registracion Garantizado |
| `52` | Give Up Registracion Garantizado |
| `53` | Correccion Registracion Garantizado |
| `54` | OTC |
| `58` | Registracion Bilateral Corredor |
| `61` | Give Up / Give Up con cambio de precio / Give Up BT |
| `68` | Derivacion Bilateral |
| `90` | Liquidacion Provisional |
| `91` | Liquidacion Final |
| `92` | Licitacion |
| `221` | Asignacion Registracion Bilateral |
| `222` | Give Up Bilateral |
| `223` | Correccion Registracion Bilateral |
| `381` | Asignacion Registracion Garantizado |
| `383` | Correccion Registracion Garantizado con Corredor |
| `540` | Alta OTC |
| `541` | Baja por pago |
| `542` | Vencimiento PAF |
| `544` | Alta Entrega |
| `551` | Ajuste flujo bonos |
| `552` | Liquidacion |
| `554` | Registracion de Futuros |
| `555` | Derivacion |
| `556` | Evento Corporativo |
| `581` | Asignacion BT Bilateral |
| `582` | Give Up BT Bilateral |
| `583` | Correccion BT Bilateral |
| `9999` | El documento general lo repite con dos descripciones: `Pasaje DLR` y `Derivacion TRD`. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | 2026-05-19 | Documento inicial del metodo `POST /PosTrade/NewCollateralReport`, alineado con controller, BL y procedure de alta vigentes. |
