# Proceso de actualizacion de documentacion API

## Objetivo

Este documento deja asentado el proceso de trabajo para actualizar documentacion funcional/técnica de endpoints de negocio a partir de:

- un archivo `.doc` exportado desde Confluence
- opcionalmente un `.md` o `.pdf` general de producto/API, por ejemplo `PrimaryAPI-BO_v1.6.4.md`
- un archivo de roles por endpoint, por ejemplo `EnpointsRoles.txt`
- el codigo de la API
- el script de base de datos (`script.sql`)

La idea es reutilizar siempre este flujo para arrancar mas rapido y mantener consistencia entre documentacion, backend y SQL.

## Aclaracion sobre Git y estructura de carpetas

En este workspace hay que distinguir dos ubicaciones:

- carpeta de documentacion / trabajo: `C:\Users\framos\Documents\APIBO`
- carpeta del repositorio Git de la solucion: `C:\Users\framos\Documents\APIBO\APIBO`

Regla operativa:

- los archivos `.md` de endpoint deben mantenerse en `C:\Users\framos\Documents\APIBO\Documentation\Endpoints`
- los documentos de proceso deben mantenerse en `C:\Users\framos\Documents\APIBO\process`
- los documentos de control deben mantenerse en `C:\Users\framos\Documents\APIBO\governance`
- las consultas de versionado (`git status`, `git diff`, `git log`, etc.) deben ejecutarse dentro de `C:\Users\framos\Documents\APIBO\APIBO`
- no se debe ejecutar `git push` en ningun caso por el momento; no queremos subir cambios al remoto

Esto evita falsos errores del tipo "Not a git repository" cuando se esta trabajando desde la raiz documental.

## Escenario esperado

En las proximas iteraciones, el usuario va a entregar:

- un `.doc` o documento exportado desde Confluence con documentacion desactualizada
- opcionalmente un documento general convertido desde PDF, por ejemplo `PrimaryAPI-BO_v1.6.4.md`
- un archivo con la matriz de roles por endpoint, por ejemplo `EnpointsRoles.txt`
- el codigo fuente de la API
- el script SQL de la base

El objetivo sera rehacer o corregir la documentacion manteniendo el formato/estructura del documento original, pero actualizando el contenido en base al comportamiento real del sistema.

## Flujo de trabajo recomendado

### 1. Leer el documento funcional original

Primero inspeccionar el `.doc` entregado para entender:

- titulo del metodo
- estructura de secciones
- tablas existentes
- ejemplos viejos
- supuestos funcionales ya documentados

Si existe un documento general de producto/API convertido desde PDF a Markdown, usarlo como apoyo secundario para:

- ubicar el endpoint dentro del contexto funcional general
- recuperar nombres funcionales de secciones o capitulos
- detectar si existe una documentacion macro del modulo

Observacion importante:

- varios `.doc` exportados desde Confluence pueden ser en realidad HTML empaquetado como Word
- eso permite leerlos desde texto plano y rescatar la estructura sin depender de Word
- un documento como `PrimaryAPI-BO_v1.6.4.md` puede servir como contexto adicional, pero debe tratarse como fuente opcional y de menor prioridad que el codigo y SQL

Buscar especialmente:

- `Objetivo`
- `Detalle`
- `Roles`
- `Parametros`
- `Ejemplo`
- `Funcion de la BD utilizada`
- `Filtros y parametros`
- `Validaciones/comentarios`
- `Versionado`

Regla importante para la seccion `Roles`:

- no inferir los roles solamente desde `[WAAuthorize]`
- usar como fuente principal el archivo `EnpointsRoles.txt` cuando este disponible
- documentar los roles aplicables al endpoint como bullets simples
- si el endpoint expone mas de un verbo, validar los roles por verbo (`GET`, `POST`, etc.)

Regla importante para endpoints con multiples verbos:

- si un mismo nombre funcional expone mas de un verbo con diferencias de entrada, validaciones, roles, respuesta o comportamiento, no documentarlo en un unico archivo
- en esos casos se deben generar archivos separados por verbo, por ejemplo `AccountRegistration-GET.md` y `AccountRegistration-POST.md`
- el nombre del archivo debe dejar claro que alcance cubre cada documento
- esta separacion aplica incluso si la ruta base es la misma
- el roadmap tambien debe reflejar esa separacion indicando el verbo documentado en cada caso

Si se usa un documento general como `PrimaryAPI-BO_v1.6.4.md`, revisar tambien:

- indice general
- nombre funcional del metodo
- seccion del modulo
- paginas o apartados relacionados

## 2. Encontrar el endpoint real en el codigo

Buscar en el repo el endpoint por nombre funcional, por ruta o por tag Swagger.

Comandos utiles:

```powershell
rg -n "PosTrade|MT506|Route\\(\" .
rg -n "NombreMetodo|NombreEndpoint" .
git status --short   # ejecutar dentro de C:\Users\framos\Documents\APIBO\APIBO
```

Relevar desde el controller:

- ruta real
- verbo HTTP
- firma del metodo
- nombres de parametros expuestos por API
- defaults
- validaciones
- codigos/mensajes de error

Adicionalmente, cruzar el nombre funcional del endpoint con `EnpointsRoles.txt` para obtener los roles exactos del metodo y verbo documentados.

## 3. Relevar la capa de negocio

Leer la business logic asociada para entender:

- como se procesan los parametros
- como se decide actual vs historico
- como se construye la respuesta
- si hay transformaciones sobre campos
- diferencias entre salida `json` y `swift`

Puntos a revisar siempre:

- armado del objeto de salida
- defaults de paginacion
- serializacion especial
- campos omitidos o renombrados
- composicion de campos derivados, por ejemplo `Party`

## 3.bis. Relevar roles desde archivo fuente

Si existe `EnpointsRoles.txt`, usarlo como fuente oficial para la seccion de roles del documento.

Formato esperado del archivo:

- `Name`
- `Roles`
- `HttpVerb`

Reglas de uso:

- buscar por `Name` usando el nombre funcional del endpoint, por ejemplo `CollateralList` o `NewCollateralReport`
- filtrar por `HttpVerb` para separar correctamente accesos `GET`, `POST`, etc.
- si un endpoint tiene mas de un verbo, documentar solo los roles del verbo correspondiente al documento que se este armando
- presentar el resultado en la seccion `Roles que pueden utilizarlo` como bullets simples
- si el archivo contradice una inferencia hecha desde controller o documentacion vieja, priorizar `EnpointsRoles.txt`
- si existe `GET` y `POST` para un mismo endpoint, relevar y documentar cada verbo por separado, en archivos distintos

## 4. Identificar la capa de datos y la funcion SQL usada

Buscar:

- clase ADO / Data Access
- llamada a `OwnData`
- invocacion a funciones SQL
- variantes historicas

Objetivo:

- determinar exactamente que funcion de BD usa el endpoint
- confirmar si hay una funcion actual y otra historica
- detectar si alguna documentacion vieja referencia funciones incorrectas

## 5. Relevar la funcion en `script.sql`

Buscar la definicion real de la funcion:

```powershell
rg -n "CREATE FUNCTION \\[Esquema\\].\\[NombreFuncion\\]" script.sql
```

Documentar:

- firma completa
- parametros
- `SELECT` de salida
- tablas/vistas origen
- filtros usados en `WHERE`
- paginacion
- diferencias entre actual e historica

Revisar especialmente:

- parametros que existen en la firma pero no se usan
- comentarios de codigo que indiquen logica deshabilitada
- paginacion comentada
- diferencias de precision/escala entre funciones

## 6. Relevar metadata del modelo

Usar el modelo `.edmx` y/o clases `Result` para recuperar:

- nombres de columnas
- tipos de dato
- largo
- precision
- escala
- nulabilidad

Esto sirve para armar tablas de documentacion mas precisas sin depender solo de leer el SQL a ojo.

Fuentes utiles:

- `Acsa_Publicacion.edmx`
- `Get..._Result.cs`
- DTOs de salida

## 7. Comparar documento viejo vs comportamiento real

Armar una lista mental o explicita de diferencias detectadas:

- nombre del metodo incorrecto
- parametros marcados como obligatorios cuando no lo son
- tipos de dato mal documentados
- ejemplos viejos
- funcion historica incorrecta
- respuesta incompleta
- validaciones faltantes

Si hay documento general (`PrimaryAPI-BO_v1.6.4.md` o similar), compararlo tambien, pero con esta prioridad de fuentes:

1. controller y business logic
2. funciones SQL y metadata del modelo
3. `.doc` funcional especifico
4. documento general de producto/API convertido desde PDF

Este paso ayuda a no reescribir a ciegas y a justificar cada correccion.

## 8. Generar un borrador Markdown

Crear un archivo `.md` nuevo dentro de `Documentation/Endpoints/<GrupoAPI>/`, usando como fuente principal de clasificacion el `Request HTTP` real del endpoint.

Mantener la estructura original del documento siempre que sea posible:

1. Objetivo
2. Detalle
3. Roles que pueden utilizarlo
4. Parametros
5. Ejemplo
6. Funcion de la BD utilizada
7. Filtros y parametros
8. Validaciones/comentarios en la API
9. Versionado

Si el endpoint tiene mas de un verbo:

- crear un documento por verbo en lugar de mezclar `GET`, `POST`, etc. en un solo archivo
- cada documento debe contener solamente parametros, ejemplos, validaciones, roles y respuestas del verbo que cubre
- si existe documentacion previa combinada, usarla solo como insumo y luego separar el contenido final

El objetivo no es “embellecer” sino:

- corregir
- completar
- dejar trazabilidad
- facilitar el pegado posterior en Confluence o Word

Regla de redaccion importante:

- los parametros publicos de la API se documentan una sola vez en la seccion `Parametros`
- la seccion `Filtros y parametros` no debe repetir la tabla de parametros publicos
- en `Filtros y parametros` solo se debe explicar:
  - como esos parametros se traducen a SQL
  - que parametros internos usa la funcion
  - que filtros provienen del usuario autenticado
  - observaciones tecnicas relevantes

Regla especifica para la seccion `Roles que pueden utilizarlo`:

- reemplazar textos genericos del tipo "el controller expone el metodo con `[WAAuthorize]`" cuando exista `EnpointsRoles.txt`
- listar los roles concretos habilitados para ese endpoint y verbo
- usar bullets simples, por ejemplo:
  - `MC RM`
  - `MC`
  - `Clearing`
- solo dejar aclaraciones adicionales si el archivo de roles no existe o no contiene ese endpoint

## 9. Incluir siempre estos bloques

### Contrato API

- endpoint
- verbo
- parametros
- defaults
- errores de validacion

### Ejemplo de request

- usar parametros reales
- respetar nombres reales del controller

### Ejemplo de respuesta

- incluir `json`
- incluir `swift` si existe
- aclarar si `swift` sigue siendo JSON serializado con tags renombrados

### Capa SQL

- funcion actual
- funcion historica
- regla de seleccion entre ambas
- firma SQL
- explicacion de filtros internos sin duplicar la tabla de parametros API

### Tabla de salida de BD

Incluir, si es posible:

- columna fn
- campo API
- tag SWIFT/FIX
- tipo SQL
- largo
- precision
- escala

## 10. Verificaciones finales antes de cerrar

Siempre verificar:

- que cada parametro documentado exista en la firma del controller
- que los parametros publicos no hayan quedado repetidos en `Filtros y parametros`
- que cada campo del ejemplo exista en el objeto real de salida
- que las funciones SQL nombradas sean las que usa la aplicacion
- que la seccion de roles salga de `EnpointsRoles.txt` cuando ese archivo exista
- que no queden referencias viejas copiadas del documento original
- que cualquier validacion o consulta de Git se haya hecho desde `C:\Users\framos\Documents\APIBO\APIBO`

Buscar explicitamente cosas como:

- nombre viejo del metodo
- obligatoriedad incorrecta
- tipos incorrectos
- funciones equivocadas
- ejemplos que no coinciden con el contrato real

## Criterios de calidad para la documentacion

La documentacion final deberia:

- mantener la estructura funcional del negocio
- reflejar el comportamiento real del codigo
- ser util para negocio, QA y desarrollo
- dejar claras las diferencias entre lo expuesto por API y lo interno de SQL
- aclarar las excepciones o rarezas tecnicas actuales
- usar documentos generales como apoyo contextual, nunca como fuente principal cuando contradicen el codigo o SQL

## Convenciones sugeridas

- Entregable principal: `NombreMetodo.md`
- Documento de proceso reutilizable: este archivo
- Idioma: espanol claro, tecnico pero entendible para negocio
- Prioridad: exactitud sobre prolijidad estetica

## Playbook resumido

1. Leer `.doc` exportado de Confluence
2. Rescatar estructura y secciones
3. Encontrar controller y ruta real
4. Relevar firma, defaults y validaciones
5. Leer business logic y salida real
6. Encontrar funcion SQL actual e historica
7. Revisar `script.sql`
8. Completar tipos con `edmx` y clases `Result`
9. Comparar contra documentacion vieja
10. Redactar `.md` actualizado
11. Validar que no queden datos viejos o incorrectos

## Uso futuro

La proxima vez, si el usuario entrega:

- un `.doc` de Confluence
- opcionalmente un `.md` convertido desde un PDF general, por ejemplo `PrimaryAPI-BO_v1.6.4.md`
- opcionalmente `EnpointsRoles.txt` como fuente de roles por endpoint
- el codigo
- `script.sql`

se debe arrancar directamente siguiendo este proceso, sin rediscutir la metodologia salvo que el usuario pida un formato de salida diferente.

## Subproceso posterior: migracion a Confluence

Una vez que el `.md` final ya fue validado contra codigo, SQL y archivo de roles, se puede continuar con el subproceso de publicacion automatizada hacia Confluence Cloud.

Ese subproceso se documenta por separado en:

- [../ConfluenceMigration/ConfluenceMigration.md](../ConfluenceMigration/ConfluenceMigration.md)
- [../ConfluenceMigration/SetupConfluence.md](../ConfluenceMigration/SetupConfluence.md)

La separacion es intencional:

- este documento cubre el relevamiento y la redaccion correcta del contenido
- el documento de migracion cubre como transformar ese contenido en paginas de Confluence y sincronizarlo por API
- el setup de Confluence define la configuracion operativa concreta que debe revisarse antes de publicar

Orden recomendado de trabajo:

1. cerrar documentacion tecnica/funcional en Markdown
2. revisar links y estructura final
3. chequear [../ConfluenceMigration/SetupConfluence.md](../ConfluenceMigration/SetupConfluence.md) antes de publicar
4. ejecutar el subproceso de migracion/publicacion en Confluence
