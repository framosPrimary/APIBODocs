# Instrumento Digital (DigitalInstrument - POST)

## Objetivo

El objetivo del endpoint es informar instrumentos digitales a traves de `PreTrade/DigitalInstrument`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `DigitalInstrument` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/PreTrade/DigitalInstrument` |

## Roles que pueden utilizarlo

- `POST`: `MC RM`, `Administrador`, `MC`, `EntidadBursatil`

## Parametros

El metodo acepta un `body` JSON con un objeto.

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/PreTrade/DigitalInstrument
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "SecurityReqID": "9001",
  "Symbol": "PAGARE01",
  "SecurityType": "PD"
}
```

## Funcion de la BD utilizada

- Persistencia: `InsertInstrumentoDigitalBL().InsertInstrumentoDigital(...)`

## Filtros y parametros

### Observaciones tecnicas

- La BL completa `User` y, si `MarketID` no viene informado, asigna `XROX`.
- La BL valida reglas especificas segun el tipo de instrumento (`FCE`, `ECH` o `PD`).
- Si `PrimaryCustody` no se informa, la BL lo normaliza a `true`.
- Luego de persistir, el alta se envia a `ClearingInterface.ContribucionInstrumentoDigital`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | `null` en alta correcta o respuesta de negocio. |
| `400 BadRequest` | Body nulo o JSON invalido | `"Json format invalid"` |
| `400 BadRequest` | Error de validacion funcional | Mensaje o arreglo JSON de negocio. |
| `503 ServiceUnavailable` | Error tecnico controlado | `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `POST` para `DigitalInstrument`, alineado con controller, BL y roles observados. |
