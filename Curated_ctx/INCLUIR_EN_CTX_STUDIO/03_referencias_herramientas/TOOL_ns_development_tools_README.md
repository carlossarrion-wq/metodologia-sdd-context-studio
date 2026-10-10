---
id: "tool-ns-development-tools-readme"
title: "ns_development_tools — Herramientas de desarrollo y método IBM"
category: tooling
document_type: "reference"
source_path: "Context/ns_development_tools/ns_development_tools_README.md"
source_identifier: "ns_development_tools_readme"
authority: reference
curation_status: curated
version: "1.0"
curated_on: "2026-10-10"
usage: "grounding"
---

> **Estado de curación:** Este contenido conserva el texto de origen y ha sido normalizado para su ingestión. No se han detectado marcadores explícitos de validación pendiente en el texto de origen.

# ns_development_tools — Herramientas de desarrollo y método IBM

## Propósito

Este namespace reúne las **guías de referencia de las herramientas de desarrollo y consultoría** del método IBM Consulting que se utilizan (o se pueden utilizar) en el proyecto **CRM AI-native de UFD**: qué son, qué hacen, qué artefactos generan, sus ventajas, su flujo de trabajo y su arquitectura técnica.

A diferencia de `ns_methodology` (que describe el **método de trabajo** y prácticas como Context as Code), este namespace documenta las **herramientas concretas** que soportan ese método —el "producto"— para que el equipo sepa cómo encajan en el ciclo de análisis → diseño → construcción → entrega.

## Relación con el ecosistema del proyecto

Estas herramientas se enmarcan en **IBM Consulting Advantage (ICA)**, la plataforma de entrega potenciada por IA de IBM Consulting. Los tres estudios de ICA —**Context Studio, Process Studio y Agentic App Studio**— comparten el sustrato común (contexto vía **MCP**, gobierno y observabilidad) y forman una **cadena de valor** coherente. **Product Studio** (antes *Product Workbench*) es el sistema que orquesta el ciclo de vida completo por encima de los agentes de codificación.

- **Context Studio (ICA)** — solución de **gestión de contexto empresarial**: modela, integra, gestiona y entrega el contexto (ontologías/schemas + documentos + knowledge graph) que consumen los agentes vía MCP. Es la fuente de *grounding* de todo lo demás. En este repositorio, el propio feed (`IBM-context-studio-feed`) es el material que alimentaría un Context Studio.
- **Process Studio (ICA)** — estudio low-code donde agentes de IA, fundamentados en un método probado, convierten cómo funciona una función de negocio en un **blueprint** agéntico gobernado y en artefactos listos para construir (incluido el **Enterprise App Kit**). Se **fundamenta** en Context Studio.
- **Agentic App Studio (ICA)** (coloquialmente *"Agent Studio"*) — constructor/orquestador **low-code de aplicaciones agénticas** (Builder Studio, Agentic AI Core, Control Tower). **Construye y ejecuta** los agentes que **consumen** el contexto de Context Studio vía MCP.
- **Product Studio** (antes *Product Workbench*) — sistema gobernado que se sitúa *por encima* de los agentes de codificación y orquesta el **PDLC completo** (personas + IA + métodos + flujos + artefactos), fundamentado en Context Studio y codificando el método DPDE/DPSE con compuertas human-in-the-loop. Consume el **Enterprise App Kit** (el *handoff* de construcción producido por Process Studio).

> **Cadena de valor:** Context Studio **fundamenta** → Process Studio **diseña** el blueprint → Agentic App Studio **construye/ejecuta** los agentes → Product Studio **orquesta** el ciclo de vida end-to-end.

## Índice de documentos

| Documento | Herramienta | Estado | Prioridad |
|---|---|---|---|
| `TOOL_ica_ibm_consulting_advantage_guia_referencia.md` | **IBM Consulting Advantage (ICA)** — guía de referencia de la plataforma que enmarca los tres estudios (qué es, assets, assistants/agents, piezas) | disponible | tier1 |
| `TOOL_process_studio_guia_referencia.md` | **Process Studio** (v1.5 · Beta) — guía de referencia extensa | disponible | tier1 |
| `TOOL_context_studio_guia_referencia.md` | **Context Studio (IBM ICA)** — guía de referencia e implementación (definición oficial ICA, arquitectura por capas, stack, ontología/schema JSON-LD, knowledge graph, exposición MCP, integración con Bob) | disponible | tier1 |
| `TOOL_agentic_app_studio_guia_referencia.md` | **Agentic App Studio (IBM ICA)** — guía de referencia (el "Agent Studio": Builder Studio, Agentic AI Core, Control Tower, MCP/A2A, orquestación low-code, gobierno, casos de uso) | disponible | tier1 |
| `TOOL_product_studio_guia_referencia.md` | **Product Studio** (antes *Product Workbench*) — guía de referencia (orquestación agéntica del PDLC end-to-end, Deep Agents, método DPDE/DPSE, incluye notas del ponente) | disponible | tier1 |

## Documentos previstos

- Fichas de integración método↔herramienta (cómo se conecta con `ns_methodology` y con los artefactos TO-BE de `ns_tobe`).

*(El Enterprise App Kit se descarta como documento propio: no es relevante para el caso de UFD.)*

## Convenciones

- Un documento por herramienta, en snake_case (`[herramienta]_guia_referencia.md`).
- Cada guía conserva la **terminología de producto original** (nombres de funciones, artefactos y marcas) por fidelidad técnica, con nota de atribución/licencia cuando la fuente es material interno de IBM protegido.
- Las cifras o capacidades sujetas a versión se marcan como dependientes del despliegue (confirmar contra la instancia concreta).
