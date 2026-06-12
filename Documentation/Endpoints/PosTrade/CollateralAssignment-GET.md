# Distribucion de Activos (CollateralAssignment - GET)

## Objetivo

El objetivo del endpoint es consultar distribuciones de activos a traves de `PosTrade/CollateralAssignment`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `CollateralAssignment` |
| Tipo de request | `GET` |
| Request HTTP | `{{url}}/PosTrade/CollateralAssignment?CollReqID={{CollReqID}}` |

## Roles que pueden utilizarlo

- `GET`: `MC RM`, `MC`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `CollReqID` | `string` | Identificador de requerimiento de colateral. | Obligatorio |

## Funcion de la BD utilizada

- La BL delega en `GetEstadoContribucionByCollReqID(...)`

## Validaciones/comentarios en la API

### Status code y responses posibles

| HTTP status | Cuando ocurre | Response posible |
|---|---|---|
| `200 OK` | Consulta exitosa | Estado de contribucion. |

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `2026-06-11` | Documento separado del verbo `GET` para `CollateralAssignment`, alineado con controller y roles observados. |
