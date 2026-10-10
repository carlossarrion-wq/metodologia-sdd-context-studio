---
id: "meta-ctx-studio-structure"
title: "Estructura de carga para Context Studio"
category: "governance"
document_type: "manifest"
authority: "operational"
curation_status: "curated"
version: "1.0"
curated_on: "2026-10-10"
usage: "administration_only"
---

# Estructura de carga para Context Studio

## `INCLUIR_EN_CTX_STUDIO/`

Estos activos se han separado por contexto lógico. Cargar **cada subdirectorio como un contexto independiente**, no como un único corpus.

| Subdirectorio | Finalidad | Regla de uso |
|---|---|---|
| `00_gobierno_del_contexto/` | Constitución y reglas de comportamiento del grounding. | Cargar primero y con prioridad máxima. |
| `01_metodologia_sdd/` | Diseñar y ejecutar la metodología SDD con OpenSpec, Cline y Kiro. | Carga inicial obligatoria, junto al gobierno. Las normas son provisionales hasta ratificación. |
| `02_contexto_negocio/` | Contexto de Naturgy/UFD/Nedgia, glosario y restricciones de negocio. | Cargar sólo para casos pertenecientes a ese perímetro. |
| `03_referencias_herramientas/` | Context Studio, Process Studio, ICA y herramientas relacionadas. | Cargar como referencia separada; validar capacidades en la instancia concreta. |

## `NO_INCLUIR_EN_CTX_STUDIO/`

No cargar estos activos en endpoints MCP o contextos de consulta general.

| Subdirectorio | Motivo |
|---|---|
| `01_administracion_y_gobierno/` | README, plan operativo de ingesta y auditoría: sirven para administrar y validar la carga, no para fundamentar recomendaciones funcionales. |
| `02_borradores_restringidos/` | Borrador AWS no aprobado y de acceso restringido. Sólo puede cargarse en un contexto aislado con ACL específica y usuarios autorizados. |

## Orden recomendado

1. Crear `context-governance` desde `00_gobierno_del_contexto/`.
2. Crear `sdd-methodology-core` desde `01_metodologia_sdd/`.
3. Crear `business-boundaries` desde `02_contexto_negocio/` sólo si procede.
4. Crear `tooling-reference` desde `03_referencias_herramientas/`.
5. Aplicar las pruebas del plan de ingesta antes de exponer cualquier contexto por MCP.
