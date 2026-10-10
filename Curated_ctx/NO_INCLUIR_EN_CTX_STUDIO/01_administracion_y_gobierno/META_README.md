---
id: "meta-readme"
title: "Curated_ctx — índice y guía de ingestión"
category: "metadata"
document_type: "repository_guide"
authority: "operational"
curation_status: "curated"
version: "1.0"
curated_on: "2026-10-10"
usage: "grounding"
---

# Curated_ctx — Paquete de contexto para grounding

Esta carpeta contiene una versión normalizada de `Context/` preparada para su carga en Context Studio. Los documentos preservan el contenido fuente, incorporan metadatos homogéneos y separan explícitamente la autoridad de una norma de la utilidad de una referencia.

## Convención de nombres y categorías

Todos los nombres son únicos en toda la carpeta y siguen `<PREFIJO>_<nombre-descriptivo>.md`:

| Prefijo | Categoría | Uso durante el grounding | Autoridad |
|---|---|---|---|
| `BUS` | Contexto de negocio | Dominio, organización, glosario y restricciones sectoriales | Contextual; contrastar datos corporativos cambiantes |
| `NORM` | Gobierno y normativa | Reglas obligatorias y arquitectura/seguridad de referencia | `governing` para políticas; el libro blanco es una referencia técnica |
| `SDD` | Metodología | Proceso OpenSpec, artefactos, guías y controles | Metodológica |
| `TOOL` | Herramientas | Capacidades, límites e integración de productos | Referencia; verificar disponibilidad y versiones |
| `META` | Gestión del paquete | Índice, constitución y auditoría | Operacional |
| `AUD` | Auditoría | Hallazgos de curation; no usar como evidencia de negocio | Operacional |

## Esquema de metadatos

Cada activo derivado de `Context/` incluye YAML con: `id`, `title`, `category`, `document_type`, `source_path`, `source_identifier`, `authority`, `curation_status`, `version`, `curated_on` y `usage`.

- `curation_status: curated`: no se encontraron marcadores explícitos de pendiente en el origen.
- `curation_status: provisional`: decisión operativa con propietario y vigencia definidos, pendiente de ratificación formal.
- `curation_status: needs_review`: hay contenido incompleto, ilustrativo, no trazable o con indicadores como `[VALIDAR]` y `[ADAPTAR]`. Puede recuperarse como hipótesis, pero no como hecho definitivo.
- `source_path` mantiene trazabilidad con el documento original; los activos administrativos `META` y `AUD` no tienen fuente de contenido. La carpeta `Context/` no se modifica.

## Inventario de carga recomendado

| Lote | Archivos | Propósito |
|---|---:|---|
| Base de gobierno | 4 `NORM` + `META_CONSTITUTION.md` | Aplicar primero; prevalece ante conflicto |
| Dominio corporativo | 6 `BUS` | Aportar vocabulario y límites de negocio |
| Método de entrega | 7 `SDD` | Guiar análisis, especificación y validación |
| Herramientas | 6 `TOOL` | Recuperar sólo cuando la consulta trate la herramienta |
| Gestión | `META_README.md` + `AUD_curation_findings.md` | Administración y revisión del corpus |

Para consultas generales, filtre por categoría y recupere fragmentos antes de incluir el libro blanco `NORM_aws_agentcore_whitepaper_v1_5.md`, por su tamaño. No cargue el informe de auditoría como evidencia funcional.

## Orden de resolución de conflictos

1. Constitución (`META_CONSTITUTION.md`) para reglas de uso del contexto.
2. Normativa `NORM` aplicable y vigente.
3. Fuentes oficiales verificadas y más recientes, si están disponibles.
4. Especificaciones aprobadas y metodología `SDD`.
5. Contexto de negocio `BUS` y documentación de herramienta `TOOL`.
6. Ejemplos, contenido ilustrativo o marcado `needs_review`.

Las discrepancias se tratan como incertidumbre: se citan, no se resuelven inventando datos, y se elevan para validación.

## Mantenimiento

Al incorporar un documento: asígnele un prefijo de categoría, un `id` globalmente único, todos los metadatos del esquema y un estado de curación. Actualice este índice y `AUD_curation_findings.md` cuando cambie el inventario o se resuelva un hallazgo. No sobrescriba la fuente: regenere el derivado curado con trazabilidad.


## Diseño de metodología e ingestión

Para diseñar la metodología, comenzar por `METH_sdd_context_process_playbook.md`. Para la carga en Context Studio y las pruebas antes de exponer MCP, aplicar `META_context_studio_ingestion_plan.md`. No cargar todo el corpus como un único contexto ni tratar los activos `provisional`, `needs_review` o `restricted_grounding` como hechos o reglas definitivas.
