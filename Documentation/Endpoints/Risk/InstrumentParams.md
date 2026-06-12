# Parametros de Instrumentos (InstrumentParams)

## Objetivo

El objetivo del endpoint es consultar los parametros de instrumentos de riesgo expuestos por `Risk/InstrumentParams`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `InstrumentParams` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/Risk/InstrumentParams` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `MC`, `API Publico`

## Parametros

El metodo no expone parametros de entrada.

## Ejemplo

### Ejemplo de request

```http
GET {{url}}/Risk/InstrumentParams
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
{
  "Value": [
    {
      "Instrument": "DLR",
      "ContractSize": 1000
    }
  ]
}
```

## Funcion de la BD utilizada

- El endpoint no usa SQL local directo.
- La BL consume el servicio externo `Winterfell.API.Rest`.
- Recurso observado: `/api/rima/getinstrumentparams`

## Filtros y parametros

### Observaciones tecnicas

- La respuesta se parsea como `JObject` y se devuelve tal como la informa Winterfell.
- Si falla la llamada externa, la BL intenta parsear el mensaje de excepcion como JSON.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | JSON de parametros de instrumentos. |
| `400 BadRequest` | Error tecnico capturado por controller | Mensaje de error. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `InstrumentParams`, alineado con controller, BL y roles observados. |
