# Alta de Usuario de Portfolio (CreateNewUser)

## Objetivo

El objetivo del endpoint es generar un alta de usuario para MyPortfolio a traves de `MyPortfolio/CreateNewUser`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CreateNewUser` |
| Tipo de request | `POST` |
| Request HTTP | `{{url}}/MyPortfolio/CreateNewUser?email={{email}}` |

## Roles que pueden utilizarlo

- `POST`: `Multicomitente`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `email` | `string` | Correo electronico del nuevo usuario. | Obligatorio |

## Ejemplo

### Ejemplo de request

```http
POST {{url}}/MyPortfolio/CreateNewUser?email=usuario@dominio.com
Authorization: Bearer {{token}}
```

### Ejemplo de respuesta

```json
true
```

## Funcion de la BD utilizada

- La logica se apoya en servicios internos de portfolio y validaciones de negocio.

## Filtros y parametros

### Observaciones tecnicas

- El endpoint no recibe body JSON; el alta se dispara con el parametro `email`.
- La validacion de email ocurre antes de delegar la operacion a la capa de negocio.

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Alta exitosa | Resultado booleano o respuesta de negocio equivalente. |
| `400 BadRequest` | `email` ausente | `"Parameter 'email' is required."` |
| `400 BadRequest` | `email` invalido | `"Parameter 'email' malformed."` |
| `400 BadRequest` | `email` con caracteres peligrosos | `"The 'email' contains potentially dangerous characters."` |
| `503 ServiceUnavailable` | Error tecnico controlado | `"The operation could not be performed."` |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-10` | Documento inicial del endpoint `CreateNewUser`, alineado con controller, BL y roles observados. |
