# [Titulo funcional del metodo]

## Objetivo

Describir brevemente el objetivo funcional del metodo y que informacion expone a traves del endpoint `[VERBO] /[ruta]`.

## Detalle

| Detalle | Valor |
|---|---|
| Nombre del metodo | `[NombreMetodo]` |
| Tipo de request | `[GET/POST/PUT/DELETE]` |
| Request HTTP | `{{url}}/[ruta]?[param1]={{valor1}}&[param2]={{valor2}}` |

## Roles que pueden utilizarlo

Los roles que pueden consultar este metodo son los siguientes:

- `[ROL_1]`
- `[ROL_2]`
- `[ROL_3]`

## Parametros

| Nombre | Tipo de dato | Descripcion | Es obligatorio |
|---|---|---|---|
| `[parametro]` | `[string/integer/date/boolean/etc.]` | `[descripcion funcional y tecnica]` | `[Obligatorio/Opcional]` |
| `[parametro]` | `[string/integer/date/boolean/etc.]` | `[descripcion funcional y tecnica]` | `[Obligatorio/Opcional]` |

> Completar en esta seccion:
>
> - formato esperado de fechas
> - valores admitidos
> - defaults
> - reglas de negocio visibles desde API

## Ejemplo

### Ejemplo de request

**Objetivo**  
Describir el caso de uso del ejemplo.

**Request**

```http
[VERBO] {{url}}/[ruta]?[param1]=[valor1]&[param2]=[valor2]
```

### Ejemplo de respuesta `json`

```json
[
  {
    "Campo1": "valor",
    "Campo2": 123,
    "Campo3": "2026-05-19T00:00:00"
  }
]
```

### Ejemplo de respuesta alternativa

Si el endpoint soporta otro formato de salida o una variante funcional, documentarla aca.

```json
[
  {
    "CampoMapeado": "valor"
  }
]
```

### Mapeo de campos en salida alternativa

| Campo API | Tag / Campo externo / Alias |
|---|---|
| `Campo1` | `[tag/alias]` |
| `Campo2` | `[tag/alias]` |

## Funcion de la BD utilizada

Indicar si el endpoint usa una o varias funciones/procedures y explicar brevemente la regla de seleccion.

- Funcion actual: `[Esquema].[FuncionActual]`
- Funcion historica: `[Esquema].[FuncionHistorica]`

### Regla de seleccion

- Si `[condicion]`, la API usa `[FuncionHistorica]`.
- Si `[condicion]`, la API usa `[FuncionActual]`.

### Invocacion SQL de la funcion actual

```sql
SELECT *
FROM [Esquema].[FuncionActual] (
   @param1,
   @param2,
   @param3
);
```

### Invocacion SQL de la funcion historica

```sql
SELECT *
FROM [Esquema].[FuncionHistorica] (
   @param1,
   @param2,
   @param3
);
```

### Tabla de salida de la funcion

| Columna fn | Campo API | Tag / Alias | Tipo dato SQL | Largo | Precision | Escala |
|---|---|---|---|---:|---:|---:|
| `[ColumnaSQL]` | `[CampoApi]` | `[tag/alias]` | `[varchar/decimal/int/date/etc.]` | `[n]` | `[p]` | `[s]` |
| `[ColumnaSQL]` | `[CampoApi]` | `[tag/alias]` | `[varchar/decimal/int/date/etc.]` | `[n]` | `[p]` | `[s]` |

> Si hay diferencias entre funcion actual e historica, aclararlas debajo de la tabla.

## Filtros y parametros

> Importante:
>
> - no volver a listar aca los parametros publicos de la API
> - esos parametros ya deben quedar documentados una unica vez en la seccion `Parametros`
> - esta seccion debe usarse solo para explicar filtros internos, traduccion a SQL, origen de filtros de usuario y observaciones tecnicas

### Parametros internos de la funcion SQL

| Nombre | Tipo de dato | Origen |
|---|---|---|
| `@parametroSql` | `[tipo SQL]` | `[filtro de usuario / valor calculado / correlacion con parametro API ya documentado]` |
| `@parametroSql` | `[tipo SQL]` | `[filtro de usuario / valor calculado / correlacion con parametro API ya documentado]` |

### Observaciones sobre filtros

- Aclarar composicion de campos derivados.
- Aclarar parametros en firma que no se usan actualmente.
- Explicar como se traducen los parametros API a filtros SQL sin repetir la tabla de `Parametros`.
- Aclarar paginacion, ordenamiento o defaults tecnicos relevantes.

## Validaciones/comentarios en la API

- Documentar errores de validacion y mensajes.
- Documentar defaults.
- Documentar comportamiento cuando no hay resultados.
- Documentar diferencias entre formato real y formato nominal si aplica.
- Documentar rarezas tecnicas actuales que negocio o QA deberian conocer.

## Versionado

| Version | Fecha | Detalle |
|---|---|---|
| `1.0` | `[YYYY-MM-DD]` | Version inicial del documento. |
| `[x.y]` | `[YYYY-MM-DD]` | `[detalle del ajuste realizado]` |

## Checklist de validacion antes de cerrar

- Verificar que cada parametro documentado exista en la firma del controller.
- Verificar que cada campo del ejemplo exista en el objeto real de salida.
- Verificar que las funciones SQL nombradas coincidan con las invocadas por la aplicacion.
- Verificar que no queden referencias viejas copiadas del documento anterior.
- Verificar que tipos, defaults y mensajes de error coincidan con el codigo vigente.
