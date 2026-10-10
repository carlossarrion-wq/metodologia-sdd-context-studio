---
id: "readme-sdd-metodologia"
title: "Carga del contexto de metodología SDD"
category: "metadata"
document_type: "directory_readme"
authority: "operational"
curation_status: "curated"
version: "1.0"
curated_on: "2026-10-10"
usage: "administration_only"
---

# Contexto: metodología SDD

## Decisión de carga

**INCLUIR en Context Studio.** Es el contexto funcional base para diseñar, especificar, implementar y validar casos de uso mediante SDD/OpenSpec, Cline y Kiro.

## Contenido

- `METH_sdd_context_process_playbook.md`: flujo AS-IS → TO-BE → OpenSpec → construcción → validación, handoffs y gates.
- `SDD_*.md`: principios, estructura de artefactos, configuración, guías de agentes, ejemplo y antipatrones.
- `NORM_*.md`: requisitos de seguridad, IA y calidad aplicables al proceso.

## Configuración recomendada

- **Nombre del contexto:** `sdd-methodology-core`.
- **Dependencia:** cargar siempre junto con `00_gobierno_del_contexto/`.
- **Uso:** propuestas de metodología, OpenSpec, planes, revisiones y controles de calidad.
- **Precedencia:** las reglas NORM prevalecen sobre SDD, ejemplos y guías de herramientas.

## Límites

Las políticas `NORM_*` son provisionales hasta su ratificación por el propietario. `SDD_sdd_06_ejemplo_cambio_ufd.md` es ilustrativo; no representa requisitos reales de producción ni datos confirmados.
