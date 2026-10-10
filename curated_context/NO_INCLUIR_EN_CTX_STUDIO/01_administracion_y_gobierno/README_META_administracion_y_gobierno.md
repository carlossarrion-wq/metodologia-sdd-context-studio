---
id: "readme-meta-administracion"
title: "Administración de la curación"
category: "metadata"
document_type: "directory_readme"
authority: "operational"
curation_status: "curated"
version: "1.0"
curated_on: "2026-10-10"
usage: "administration_only"
---

# Administración y gobierno de la curación

## Decisión de carga

**NO INCLUIR** en contextos de grounding funcional ni en endpoints MCP de consulta general.

## Contenido y uso

| Archivo | Uso administrativo |
|---|---|
| `META_README.md` | Esquema documental, convenciones y procedencia. |
| `META_context_studio_ingestion_plan.md` | Plan de configuración, segmentación, ACL y pruebas de aceptación. |
| `AUD_curation_findings.md` | Hallazgos, inconsistencias detectadas y riesgos residuales. |

Estos archivos deben ser accesibles al Knowledge Engineer, propietario del contexto y responsables de gobierno para preparar cargas, auditar cambios y ejecutar pruebas.

## Excepción

No hay excepción de carga funcional: sus contenidos describen cómo gobernar el corpus, no la metodología ni el dominio de negocio.
