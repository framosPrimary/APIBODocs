# Setup de migracion a Confluence Cloud

## Objetivo

Este documento define la configuracion operativa necesaria para publicar la documentacion de APIBO en Confluence Cloud de manera controlada, repetible y no destructiva.

Debe leerse antes de ejecutar cualquier automatizacion de publicacion.

La idea es dejar asentado:

- que informacion debe configurarse
- como se detecta el destino correcto de cada endpoint
- como se construye el nombre final de la pagina
- como se protege contenido existente en Confluence
- que archivos no deben publicarse

## Raiz documental

La raiz documental de trabajo para la migracion pasa a ser:

- `Documentation/Endpoints/`

Dentro de esa raiz se espera una organizacion por grupos funcionales de API, por ejemplo:

- `Documentation/Endpoints/PreTrade/`
- `Documentation/Endpoints/PosTrade/`
- `Documentation/Endpoints/Trade/`
- `Documentation/Endpoints/Risk/`
- `Documentation/Endpoints/Security/`
- `Documentation/Endpoints/MyPortfolio/`

Si existen otras carpetas de soporte, no deben publicarse automaticamente salvo que esten explicitamente incluidas en la configuracion.

## Regla de clasificacion por grupo API

Cada endpoint debe quedar asignado al grupo correcto segun su ruta real.

La clasificacion no debe depender solamente de la carpeta donde esta el archivo Markdown. Debe validarse tambien contra:

- la ruta real observada en codigo
- el valor `Request HTTP` documentado en el `.md`

Segmentos validos esperados:

- `/PreTrade/`
- `/PosTrade/`
- `/Trade/`
- `/Risk/`

Regla operativa:

1. tomar el grupo sugerido por la carpeta del archivo
2. extraer el grupo real desde `Request HTTP` o desde la ruta real del endpoint
3. comparar ambos valores
4. si no coinciden, reportar inconsistencia
5. usar como fuente principal el grupo detectado desde la ruta real o `Request HTTP`

Esto evita publicar documentos en una seccion incorrecta de Confluence por un error de ubicacion local.

## Archivos publicables y no publicables

La publicacion debe recorrer archivos `.md` dentro de `Documentation/Endpoints/`, pero no todo Markdown del workspace debe terminar en Confluence.

### Archivos publicables

Se consideran publicables:

- documentos de endpoint finales
- documentos separados por verbo cuando reflejan comportamiento real distinto

Ejemplos:

- `DigitalInstrument-GET.md`
- `DigitalInstrument-POST.md`
- `AccountBalance.md`
- `TradeMatchReport.md`

### Archivos no publicables

Deben excluirse explicitamente:

- playbooks de proceso
- templates
- roadmap
- documentos de trabajo interno
- paginas contenedoras o indice que solo derivan a archivos por verbo

Ejemplos de exclusion:

- `ConfluenceMigration/**`
- `process/**`
- `governance/**`
- `Documentation/Indexes/**`
- `Documentation/Legacy/**`
- `Documentation/Context/**`

Regla importante:

- si un metodo tiene documentacion separada por verbo, la pagina contenedora local no se publica en Confluence

## Convencion de nombre de pagina en Confluence

El nombre final de la pagina debe seguir esta forma:

- `NombreMetodo (Ingles) - Descripcion (Español)`

Cuando existe mas de un verbo para el mismo metodo:

- `NombreMetodo (Ingles) - Descripcion (Español) - GET`
- `NombreMetodo (Ingles) - Descripcion (Español) - POST`

El sufijo del verbo solo se agrega si hay ambiguedad real entre dos o mas documentos del mismo metodo.

### Obtencion del nombre del metodo

Orden recomendado:

1. usar `Nombre del metodo` si el documento lo declara
2. si no existe, inferirlo desde la ruta del endpoint
3. si tampoco alcanza, usar el nombre del archivo sin extension y sin sufijo de verbo

### Obtencion de la descripcion en espanol

La descripcion en espanol debe derivarse del heading principal `#`.

Ejemplo:

- heading local: `# Instrumento Digital (DigitalInstrument - GET)`
- titulo Confluence esperado: `DigitalInstrument - Instrumento Digital - GET`

Para construir la descripcion:

- remover el nombre tecnico del metodo entre parentesis si aparece
- remover el sufijo `- GET` o `- POST` del heading cuando corresponda
- conservar la descripcion funcional limpia en espanol

## Estructura destino en Confluence

La publicacion no debe crear paginas directamente al nivel superior del espacio.

Estructura esperada:

1. pagina raiz configurada manualmente
2. paginas intermedias por grupo API
3. paginas finales por endpoint

Jerarquia objetivo:

- `ROOT_PAGE`
  - `PreTrade`
  - `PosTrade`
  - `Trade`
  - `Risk`

Cada endpoint debe publicarse debajo de la pagina del grupo correspondiente.

## Configuracion requerida

La automatizacion debe tomar al menos estos datos:

- `CONFLUENCE_BASE_URL`
- `CONFLUENCE_EMAIL`
- `CONFLUENCE_API_TOKEN`
- `CONFLUENCE_SPACE_KEY`
- `ROOT_PAGE_ID`
- `DOCS_SOURCE_DIR`
- `INCLUDE_GLOB`
- `EXCLUDE_GLOBS`

Uso recomendado:

- credenciales y datos sensibles via variables de entorno
- configuracion documental y patrones via config local o argumentos del script

### Significado de cada parametro

- `CONFLUENCE_BASE_URL`: tenant base de Atlassian, por ejemplo `https://empresa.atlassian.net`
- `CONFLUENCE_EMAIL`: cuenta que ejecuta la API
- `CONFLUENCE_API_TOKEN`: token de esa cuenta
- `CONFLUENCE_SPACE_KEY`: identificador principal del espacio destino
- `ROOT_PAGE_ID`: pagina raiz bajo la cual se creara la jerarquia por grupos
- `DOCS_SOURCE_DIR`: carpeta raiz documental; para este flujo debe apuntar a `Documentation/Endpoints/`
- `INCLUDE_GLOB`: patron de inclusion de archivos
- `EXCLUDE_GLOBS`: lista de exclusiones obligatorias y opcionales

## Estrategia de actualizacion no destructiva

La automatizacion no debe borrar contenido manual ya existente en Confluence que no este presente en Markdown.

Objetivo:

- actualizar lo gestionado por sincronizacion
- preservar lo agregado manualmente fuera de ese alcance

### Regla general

Implementar merge por secciones gestionadas.

Esto implica:

- el script solo crea o actualiza bloques marcados como administrados
- cualquier bloque no gestionado queda intacto

### Criterio sugerido de secciones gestionadas

Tomar como candidatas las secciones estructurales del Markdown, por ejemplo:

- `Objetivo`
- `Detalle`
- `Roles que pueden utilizarlo`
- `Parametros`
- `Ejemplo`
- `Funcion de la BD utilizada`
- `Filtros y parametros`
- `Validaciones/comentarios en la API`
- `Versionado`

La implementacion puede envolver cada seccion gestionada con marcadores tecnicos para reconocerla en futuros syncs.

## Prevencion de duplicados

Hay que evitar duplicacion tanto de paginas como de contenido interno.

### Duplicacion de paginas

Regla recomendada:

1. si existe mapeo local `archivo -> pageId`, actualizar por id
2. si no existe, buscar por titulo dentro del grupo destino y del espacio
3. si hay una sola coincidencia, adoptarla
4. si hay varias coincidencias, abortar ese archivo y reportarlo
5. si no hay coincidencias, crear pagina nueva

### Duplicacion de contenido

Antes de insertar o actualizar una seccion gestionada:

- comparar por clave de seccion
- comparar por hash normalizado del contenido
- no volver a insertar tablas, ejemplos o headings ya administrados por el script

## Politica sobre paginas existentes

Si la pagina ya existe en Confluence:

- no borrar bloques manuales fuera de las secciones gestionadas
- no reemplazar el documento completo
- no eliminar notas, anexos o aclaraciones ajenas al sync
- si una seccion gestionada existe, actualizar solo esa seccion
- si una seccion gestionada no existe, agregarla sin alterar el resto

## Flujo operativo recomendado

1. confirmar que el `.md` final ya fue validado funcional y tecnicamente
2. verificar que el archivo este bajo `Documentation/Endpoints/` y en el grupo correcto
3. validar si el documento es publicable o si debe excluirse
4. derivar el titulo final de Confluence
5. resolver pagina grupo padre en Confluence
6. buscar pagina destino por mapeo o por titulo
7. crear o actualizar con merge no destructivo
8. registrar mapeo y resultado

## Casos que deben validarse antes de una corrida masiva

### Clasificacion

- un `.md` en `PreTrade` cuyo `Request HTTP` tambien sea `PreTrade`
- un `.md` mal ubicado en carpeta pero con `Request HTTP` correcto, para verificar que se reporte inconsistencia

### Naming

- metodo con un solo verbo, sin sufijo final
- metodo con `GET` y `POST`, con sufijo obligatorio en ambos titulos

### Publicacion

- exclusion de paginas contenedoras como `DigitalInstrument.md`
- creacion del arbol `Root -> Grupo -> Endpoint`
- adopcion de pagina existente con coincidencia unica
- deteccion de colision multiple por titulo

### Merge

- actualizacion de seccion gestionada existente
- agregado de seccion gestionada faltante
- preservacion de contenido manual fuera de las secciones gestionadas
- no duplicacion de bloques ya sincronizados

## Relacion con otros documentos del proceso

Este documento es la especificacion operativa del setup.

Documentos relacionados:

- playbook general de migracion: [ConfluenceMigration.md](ConfluenceMigration.md)
- proceso principal de documentacion: [../process/Proceso-Actualizacion-Documentacion-API.md](../process/Proceso-Actualizacion-Documentacion-API.md)

## Checklist minimo previo a publicar

Antes de ejecutar la migracion, verificar:

- que `DOCS_SOURCE_DIR` apunta a `Documentation/Endpoints/`
- que las exclusiones incluyen paginas contenedoras y documentos de proceso
- que `CONFLUENCE_SPACE_KEY` y `ROOT_PAGE_ID` son correctos
- que la cuenta configurada tiene permisos en el espacio
- que el naming esperado coincide con el metodo y verbo reales
- que el documento ya no requiere correcciones funcionales
