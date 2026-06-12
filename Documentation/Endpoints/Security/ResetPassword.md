# Reseteo de Contrasena (ResetPassword)

## Objetivo

El objetivo del metodo es iniciar el flujo de reseteo de contrasena a traves del endpoint `POST /Security/ResetPassword`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ResetPassword` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/Security/ResetPassword` |

## Roles que pueden utilizarlo

- `Administrador`
- `Multicomitente`
- `EntidadBursatil`

## Parametros

Este metodo recibe un `body` JSON.

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `UserName` | `string` | Usuario sobre el que se solicita el reseteo de contrasena. | Obligatorio |
| `CurrentPassword` | `string` | No es utilizado por `ResetPassword`. Pertenece al flujo de `ChangePassword`. | No |
| `NewPassword` | `string` | No es utilizado por `ResetPassword`. Pertenece al flujo de `ChangePassword`. | No |
| `Token` | `string` | No es utilizado por `ResetPassword`. El token se genera internamente. | No |

## Ejemplo

### Ejemplo de request

**Objetivo**  
Disparar el flujo de reseteo para el usuario `usuario@dominio.com`.

```http
POST {{url}}/Security/ResetPassword
Content-Type: application/json

{
  "UserName": "usuario@dominio.com"
}
```

### Ejemplo de respuesta

```json
"OK"
```

## Funcion de la BD utilizada

Este endpoint no invoca una funcion SQL de negocio ni una stored procedure propia relevada en el repo.

### Capa tecnica utilizada

- El controller llama a `PasswordManagementService.ResetPassword`.
- La service delega en `PasswordManagementBL.ResetPassword`.
- La business logic genera un token con `AuthTokenManager.GetAuthTokenByUser(userName, applicationID)`.
- Luego busca el endpoint `WebhookAltaUsuarioMyPortfolio` y envia un webhook con el payload `{ email, token }`.

### Componentes relacionados

| Componente | Uso |
|---|---|
| `AuthTokenManager.GetAuthTokenByUser(...)` | Genera el token para el usuario y la aplicacion actual. |
| `EndPointsBL.GetEndPointsByApplicationID(...)` | Resuelve la URL del webhook configurado. |
| `AspNetUsersBL.GetAspNetUsersByUserName(...)` | Ajusta flags del usuario en el flujo auxiliar `UserForceChangePasswordAndLocked`. |
| `HttpClient.PostAsync(...)` | Envia el webhook externo. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `userName` | `string` | Proviene de `UserName` en el body. |
| `applicationID` | `int` | Se obtiene de `WorkContext.Instance.CurrentApplication.ID`. |
| `token` | `string` | Se genera internamente con `GetAuthTokenByUser(...)`. |
| `url` | `string` | Surge de la configuracion del endpoint `WebhookAltaUsuarioMyPortfolio`. |
| `UserForceChangePasswordAndLockedTime` | `appSetting` | Configuracion usada para desbloqueo y limpieza de flags del usuario. |

### Observaciones tecnicas

- El body se deserializa contra `ApiBO.Model.Proxy.PasswordManagement`, pero para este endpoint solo se usa `UserName`.
- El endpoint no devuelve el token al consumidor. El token se envia al webhook externo y la respuesta de la API queda en `"OK"`.
- Si se produce un `TargetException`, la business logic registra el error y devuelve igualmente `"OK"`. El comentario indica que esto contempla usuarios que no existen en la API.
- Si el webhook responde con `errorMessages`, esos errores se loguean pero no se propagan al consumidor.
- Antes de enviar el webhook, la business logic ejecuta `UserForceChangePasswordAndLocked(false, userName)` para limpiar flags del usuario segun el estado y la configuracion vigente.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Flujo aceptado | `"OK"` |
| `400 BadRequest` | JSON invalido | `"Invalid JSON format."` |
| `400 BadRequest` | `UserName` ausente | `"userName field is required."` |
| `503 ServiceUnavailable` | La capa inferior lanza `InvalidOperationException` | Mensaje de la excepcion recibida. |

### Observaciones adicionales

- El endpoint no tiene `[WAAuthorize]` en el controller.
- El controller valida el body con `string.IsNullOrEmpty(json.ToString())`; si el body llega `null`, puede terminar en `400` por excepcion general.
- Aunque historicamente el negocio gira alrededor de un token de recuperacion, el contrato vigente de la API no expone ese token en la respuesta.
- La respuesta `"OK"` no garantiza que el webhook externo haya procesado correctamente la solicitud; solo indica que la API acepto el flujo.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `POST /Security/ResetPassword`, alineado con controller, business logic, roles relevados y flujo tecnico vigente. |
