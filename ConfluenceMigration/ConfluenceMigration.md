# Proceso de migracion de Markdown a Confluence Cloud

## Objetivo

Este documento define el subproceso posterior a la generacion o correccion de documentacion Markdown para publicar ese contenido en Confluence Cloud de forma repetible y controlada.

La idea es separar claramente:

- el proceso de relevamiento y redaccion funcional/técnica
- el proceso de migracion/publicacion en Confluence

De esa forma, primero se valida que cada `.md` refleje el comportamiento real del sistema y luego se lo sincroniza hacia Confluence sin mezclar ambas tareas.

La configuracion operativa detallada de ese proceso vive en:

- [SetupConfluence.md](SetupConfluence.md)

## Alcance de este subproceso

Este subproceso cubre:

- preparacion de archivos `.md` para publicacion
- definicion de estructura de paginas en Confluence
- creacion o actualizacion automatica de paginas por API
- reescritura de links entre documentos Markdown hacia links internos de Confluence
- registracion de mapeos locales entre archivo y pagina remota

Este subproceso no cubre en su primera version:

- edicion bidireccional entre Confluence y Markdown
- adjuntos
- macros complejas de Confluence
- comentarios inline
- permisos finos por pagina

## Estructura de trabajo sugerida

La carpeta `ConfluenceMigration/` concentra todo lo referido a esta automatizacion.

Estructura sugerida:

- `ConfluenceMigration/ConfluenceMigration.md`: playbook del proceso
- `ConfluenceMigration/SetupConfluence.md`: setup operativo de configuracion y reglas de publicacion
- `ConfluenceMigration/scripts/`: scripts de publicacion, validacion y soporte
- `ConfluenceMigration/config/`: archivos de configuracion local no sensible o plantillas
- `ConfluenceMigration/output/`: salidas auxiliares locales, logs o mapeos generados

Si alguno de estos directorios todavia no se usa, puede quedar reservado para mantener una estructura estable desde el inicio.

## Prerrequisitos

Antes de publicar en Confluence, cada documento Markdown debe estar funcionalmente cerrado:

- titulo correcto
- contenido actualizado contra codigo y SQL
- separacion por verbo cuando corresponda
- links locales consistentes
- ejemplos y tablas revisados

Tambien deben estar disponibles las credenciales y datos de destino:

- `CONFLUENCE_BASE_URL`
- `CONFLUENCE_EMAIL`
- `CONFLUENCE_API_TOKEN`
- `CONFLUENCE_SPACE_KEY`
- `ROOT_PAGE_ID`
- carpeta origen de `.md`

Y ademas debe revisarse el setup operativo antes de ejecutar el proceso:

- [SetupConfluence.md](SetupConfluence.md)

## Convenciones de publicacion

Las reglas operativas de naming, clasificacion, jerarquia, exclusiones y politica de actualizacion deben tomarse de:

- [SetupConfluence.md](SetupConfluence.md)

## Flujo recomendado de migracion

### 1. Seleccionar origen

Definir carpeta o conjunto de `.md` a publicar, usando como raiz documental el directorio configurado en el setup.

Se recomienda poder correr:

- una publicacion total del workspace documental
- una publicacion puntual por archivo
- una publicacion por subconjunto o patron

### 2. Normalizar contenido

Antes de llamar a Confluence:

- leer el Markdown
- extraer titulo
- clasificar el grupo API real
- resolver links relativos
- convertir Markdown a HTML compatible con `storage`
- sanear elementos no soportados o ambiguos

### 3. Resolver jerarquia

Determinar si el archivo:

- es publicable o excluido
- a que grupo API pertenece
- bajo que pagina de grupo debe publicarse
- cual es el titulo final esperado en Confluence

### 4. Crear o actualizar

Publicar via API de Confluence Cloud:

- crear la pagina si no existe
- actualizarla si existe
- preservar contenido manual fuera de las secciones gestionadas
- manejar versionado requerido por Confluence para updates

### 5. Registrar resultado

Guardar localmente al menos:

- ruta del archivo
- titulo publicado
- `pageId`
- fecha de ultima sincronizacion
- estado de la operacion
- advertencias por clasificacion, colision o exclusiones

## Mapeo local recomendado

Mantener un archivo local de mapeo para reducir ambiguedades.

Campos sugeridos:

- `sourcePath`
- `title`
- `pageId`
- `parentPageId`
- `spaceId`
- `lastPublishedAt`
- `contentHash`

Objetivos del mapeo:

- actualizar por `pageId` y no solo por titulo
- detectar renombres
- evitar duplicaciones
- permitir sincronizaciones incrementales

## Casos de validacion minima

Antes de considerar estable la automatizacion, validar al menos:

1. crear una pagina nueva desde un `.md` simple
2. actualizar una pagina existente sin duplicarla
3. excluir correctamente una pagina contenedora local
4. convertir correctamente headings, listas, bloques de codigo y tablas
5. preservar contenido manual fuera de secciones gestionadas
6. detectar link roto o colision de titulo sin romper toda la corrida

## Estrategia operativa recomendada

Para primeras ejecuciones:

- empezar con un subconjunto pequeno de documentos
- validar visualmente en Confluence el render
- ajustar conversion y jerarquia
- recien despues correr la publicacion masiva

Para mantenimiento:

- conservar los `.md` como fuente de verdad
- usar Confluence como destino publicado
- evitar editar manualmente las secciones gestionadas por el sync
- permitir contenido manual complementario fuera de las secciones gestionadas

## Relacion con el proceso principal

Este documento complementa al proceso principal de documentacion:

- proceso de relevamiento y redaccion: [../process/Proceso-Actualizacion-Documentacion-API.md](../process/Proceso-Actualizacion-Documentacion-API.md)
- setup operativo de publicacion: [SetupConfluence.md](SetupConfluence.md)
- subproceso posterior de publicacion: este archivo

Orden recomendado:

1. relevar codigo, SQL, roles y contratos reales
2. generar o corregir el `.md`
3. validar el documento final
4. revisar `SetupConfluence.md`
5. ejecutar el subproceso de migracion a Confluence

## Proximos pasos tecnicos

La implementacion inicial del automatizador deberia incluir:

- un script CLI local
- lectura de variables de entorno para credenciales
- conversion Markdown a `storage`
- create/update de paginas con merge no destructivo
- jerarquia padre/hijo basica
- mapeo local `archivo -> pageId`
- log resumido de resultados y errores
