---
id: "meta-context-studio-ingestion-plan"
title: "Plan de ingestión y recuperación para Context Studio"
category: "governance"
document_type: "ingestion_plan"
authority: "operational"
curation_status: "curated"
owner: "Knowledge Engineer / Oficina de Calidad y Arquitectura"
version: "1.0"
curated_on: "2026-10-10"
usage: "administration_only"
---

# Plan de ingestión y recuperación para Context Studio

## Objetivo

Crear un contexto de diseño metodológico, no un repositorio indiferenciado. La ingesta debe permitir responder con evidencia y distinguir obligaciones, referencias, ejemplos y contenido restringido.

## Contextos lógicos y orden de carga

| Contexto | Activos | Uso permitido |
|---|---|---|
| `sdd-methodology-core` | `METH_*`, `SDD_*`, `NORM_politica-*`, `NORM_requisitos-calidad.md`, `META_CONSTITUTION.md` | Diseñar y ejecutar el método, indicando todo estado provisional o pendiente. |
| `business-boundaries` | `BUS_*` | Entender dominio y restricciones; no afirmar hechos sensibles sin citar fuente y fecha. |
| `tooling-reference` | `TOOL_*` | Comparar opciones e integración; nunca asumir capacidad, licencia o disponibilidad. |
| `architecture-draft-restricted` | `NORM_aws_agentcore_whitepaper_v1_5.md` | Sólo usuarios autorizados; referencia técnica no aprobatoria. |

No mezclar `architecture-draft-restricted` con un endpoint MCP de audiencia general. Si la plataforma no permite ACL por contexto/documento, excluirlo de ese endpoint.

## Metadatos y recuperación

Preservar en cada carga: `id`, `authority`, `curation_status`, `usage`, `version`, `source_path`, fecha y propietario. Configurar la instrucción del contexto para que cada respuesta cite `id` y estado; las respuestas que se apoyen en `needs_review` o `provisional` deben declarar la limitación y pedir validación humana cuando afecte a una decisión.

Prioridad de recuperación: (1) constitución y playbook, (2) norma aplicable, (3) SDD, (4) negocio, (5) herramienta, (6) ejemplos. Un ejemplo no prevalece sobre una norma y una guía de herramienta no prevalece sobre una restricción de datos.

## Pruebas de aceptación antes de exponer MCP

1. Preguntar qué artefacto es canónico entre blueprint, OpenSpec y código; debe devolver el playbook.
2. Preguntar si Kiro puede usarse con datos corporativos; debe devolver respuesta condicionada a validación.
3. Preguntar por un supuesto KPI de UFD; debe distinguir el ejemplo de un dato real.
4. Preguntar por una regla de seguridad; debe indicar que las normas son provisionales hasta ratificación.
5. Comprobar que un usuario no autorizado no recupera el libro blanco AWS ni fuentes IBM Internal.
6. Revisar diez respuestas: todas deben incluir fuente/ID, no inventar capacidades y señalar conflictos o ausencia de evidencia.
