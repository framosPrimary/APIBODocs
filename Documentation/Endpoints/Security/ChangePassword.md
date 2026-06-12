# Cambio de Contrasena (ChangePassword)

## Objetivo

El objetivo del metodo es permitir el cambio de contrasena del usuario a traves del endpoint `POST /Security/ChangePassword`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `ChangePassword` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/Security/ChangePassword` |

## Roles que pueden utilizarlo

- `Administrador`
- `xComitente`
- `Multicomitente`
- `EntidadBursatil`

## Parametros

Este metodo recibe un `body` JSON.

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `NewPassword` | `string` | Nueva contrasena del usuario. | Obligatorio |
| `CurrentPassword` | `string` | Contrasena actual del usuario autenticado. | Condicional |
| `Token` | `string` | Token de recuperacion/cambio de contrasena. | Condicional |
| `UserName` | `string` | No es utilizado por `ChangePassword`. Pertenece al flujo de `ResetPassword`. | No |

### Reglas del body

- `NewPassword` siempre es obligatorio.
- Debe informarse al menos uno entre `CurrentPassword` o `Token`.
- Si se informa `Token`, la business logic intenta resolver el usuario por token y valida que el token pertenezca a la aplicacion actual.
- Si no se informa `Token`, el cambio se realiza sobre el usuario autenticado usando `CurrentPassword`.

## Ejemplo

### Ejemplo de request

**Objetivo**  
Cambiar la contrasena del usuario autenticado usando su contrasena actual.

```http
POST {{url}}/Security/ChangePassword
Authorization: Bearer {{token}}
Content-Type: application/json

{
  "NewPassword": "NuevaPassword1$",
  "CurrentPassword": "P@sswordActual208$"
}
```

### Ejemplo de respuesta

```json
"Password changed successfully"
```

## Funcion de la BD utilizada

Este endpoint no invoca una funcion SQL de negocio ni una stored procedure propia relevada en el repo.

### Capa tecnica utilizada

- El controller llama a `PasswordManagementService.ChangePassword`.
- La service delega en `PasswordManagementBL.ChangePassword`.
- La business logic valida el JSON y luego usa `Valquiria.Fwk.Web.Main.BaseLoginPage.ChangePassword(...)`.
- Si el cambio se realiza por token, primero consulta `UserApplicationTokenBL` para validar que el token exista y pertenezca a la aplicacion actual.

### Persistencia y componentes relacionados

| Componente | Uso |
|---|---|
| `BaseLoginPage.ChangePassword(currentPassword, newPassword, userName)` | Cambio de contrasena para usuario autenticado. |
| `BaseLoginPage.ChangePassword(token, newPassword)` | Cambio de contrasena usando token. |
| `UserApplicationTokenBL.GetUserApplicationTokenByToken(token)` | Resolucion y validacion del token. |
| `AspNetUsersBL.GetAspNetUserById(...)` | Obtencion del `UserName` cuando el flujo entra por token. |

## Filtros y parametros

En esta seccion no se repiten los parametros publicos del contrato.  
Los parametros expuestos al consumidor ya quedaron documentados en `Parametros`.

### Parametros internos relevantes

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `newPassword` | `string` | Proviene de `NewPassword` en el body. |
| `currentPassword` | `string` | Proviene de `CurrentPassword` en el body cuando no se usa token. |
| `token` | `string` | Proviene de `Token` en el body cuando se usa flujo con token. |
| `usr` | `string` | Surge del usuario autenticado o del usuario asociado al token valido. |
| `appID` | `int` | Se obtiene de `WorkContext.Instance.CurrentApplication.ID` para validar el token. |

### Observaciones tecnicas

- El body se deserializa contra `ApiBO.Model.Proxy.PasswordManagement`.
- El metodo soporta dos variantes funcionales:
  - cambio de contrasena con `CurrentPassword`,
  - cambio de contrasena con `Token`.
- Cuando se usa `Token`, no se utiliza el usuario autenticado para resolver la cuenta objetivo; se toma el usuario asociado al token.
- El endpoint sigue estando protegido con `[WAAuthorize]`, aun cuando la business logic contempla un flujo por token.
- Los errores de reglas de password no se arman en controller: provienen del resultado devuelto por `BaseLoginPage.ChangePassword(...)` y se traducen con `HandleResponseErrors`.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Cambio exitoso | `"Password changed successfully"` |
| `400 BadRequest` | `NewPassword` ausente | `"Field newPassword is required"` |
| `400 BadRequest` | No se informa `Token` ni `CurrentPassword` | `"At least one field, token or currentPassword, are required"` |
| `400 BadRequest` | Token inexistente o de otra aplicacion | `"Invalid token."` |
| `400 BadRequest` | Password actual incorrecta | `"Incorrect Password"` |
| `400 BadRequest` | La nueva password ya fue usada | `"Password Already Exists."` |
| `400 BadRequest` | La nueva password no cumple largo minimo | `"Passwords must be at least 6 characters."` |
| `400 BadRequest` | La nueva password no contiene caracter no alfanumerico | `"Passwords must have at least one non letter or digit character."` |
| `400 BadRequest` | La nueva password no contiene minusculas | `"Passwords must have at least one lowercase ('a'-'z')."` |
| `400 BadRequest` | La nueva password no contiene mayusculas | `"Passwords must have at least one uppercase ('A'-'Z')."` |
| `503 ServiceUnavailable` | La capa inferior lanza `InvalidOperationException` | Mensaje de la excepcion recibida. |

### Observaciones adicionales

- El controller intenta validar JSON con `if (!json.HasValues && String.IsNullOrEmpty(json.ToString()))`, pero si el body llega `null` puede producir una `NullReferenceException` y terminar igualmente en `400 BadRequest`.
- La documentacion historica solo muestra el flujo con `CurrentPassword`, pero el codigo vigente tambien soporta el flujo con `Token`.
- `UserName` existe en el modelo compartido, pero no participa en `ChangePassword`; se usa en `ResetPassword`.
- Si `BaseLoginPage.ChangePassword(...)` devuelve errores no contemplados por `HandleResponseErrors`, la API responde `400` con mensaje vacio o incompleto segun el texto recibido.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del metodo `POST /Security/ChangePassword`, alineado con controller, business logic, roles relevados y componentes de seguridad vigentes. |
