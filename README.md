# Workspace documental APIBO

Este workspace quedo organizado para separar claramente codigo, fuente documental, proceso, control y publicacion.

El objetivo de este repositorio es compartir el arnes documental y su metodologia de trabajo, no el backend tecnico ni los insumos sensibles usados durante el relevamiento.

## Zonas principales

- `APIBO/`: repositorio tecnico y codigo fuente de la solucion
- `Documentation/`: fuente documental y contexto funcional
- `process/`: playbooks y metodologia de trabajo documental
- `governance/`: inventario, seguimiento y control
- `ConfluenceMigration/`: setup, reglas y futura automatizacion de publicacion

## Regla operativa

La fuente de verdad para endpoints vive en:

- `Documentation/Endpoints/`

Los documentos indice o contenedores viven en:

- `Documentation/Indexes/`

Los documentos legacy o contextuales viven en:

- `Documentation/Legacy/`
- `Documentation/Context/`

Antes de publicar en Confluence, revisar:

- [ConfluenceMigration/SetupConfluence.md](/c:/Users/framos/Documents/APIBO/ConfluenceMigration/SetupConfluence.md)

Antes de rehacer o corregir documentacion, seguir:

- [process/Proceso-Actualizacion-Documentacion-API.md](/c:/Users/framos/Documents/APIBO/process/Proceso-Actualizacion-Documentacion-API.md)

## Prerequisitos para usar el agente documental

Para que el agente documental funcione correctamente en una clonacion nueva, hay insumos externos que deben existir localmente pero no se versionan en este repo:

- `APIBO/`: codigo fuente y repositorio tecnico de la solucion
- `script.sql`: script o fuente SQL principal usada para relevar funciones, firmas y filtros
- `EnpointsRoles.txt`: matriz de roles por endpoint y verbo
- `Documentation/Context/PrimaryAPI-BO_v1.6.4.md`: documento general de contexto, opcional y no contractual

Sin esos insumos, el repo sigue siendo util para revisar la metodologia, la estructura documental y los documentos ya preparados, pero no alcanza para rehacer todo el proceso completo de relevamiento tecnico desde cero.

## Que incluye este repo

- `Documentation/Endpoints/`: fuente documental canonica por endpoint
- `Documentation/Indexes/`: paginas contenedoras locales para endpoints multi-verbo
- `Documentation/Legacy/`: referencias historicas
- `process/`: playbooks y proceso documental
- `governance/`: roadmap e inventario
- `ConfluenceMigration/`: setup y base de la futura automatizacion de publicacion

## Que no incluye este repo

- el backend tecnico bajo `APIBO/`
- `script.sql`
- `EnpointsRoles.txt`
- el documento contextual `Documentation/Context/PrimaryAPI-BO_v1.6.4.md`
- configuraciones locales, secretos, mappings y artefactos operativos de publicacion
