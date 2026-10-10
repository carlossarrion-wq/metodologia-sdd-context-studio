---
id: "aud-curation-findings"
title: "Hallazgos de curación e inconsistencias detectadas"
category: "audit"
document_type: "curation_report"
authority: "operational"
curation_status: "curated"
version: "1.1"
curated_on: "2026-10-10"
usage: "administration_only"
---

# Hallazgos de curación e inconsistencias detectadas

## Cierre de revisión 2026-10-10

Los 23 documentos de origen permanecen preservados y trazables. Los siguientes hallazgos se han resuelto mediante decisiones operativas explícitas y referencias de fuente; una decisión provisional no equivale a aprobación corporativa formal.

| ID | Estado | Resolución |
|---|---|---|
| AUD-01 | Resuelto | El libro blanco AWS dispone ahora de ficha de gobierno: propietario **Arquitectura Cloud**, clasificación **Interna**, revisión trimestral, próxima revisión 2027-01-10 y estado explícito `draft_not_approved`. Su `authority` se redujo a `reference`; no puede autorizar despliegues ni herramientas. |
| AUD-02 | Resuelto provisionalmente | Se designaron propietarios y vigencia operativa desde 2026-10-10: **CISO** (NORM-SEC-001), **Comité de IA** (NORM-IA-001) y **Oficina de Calidad y Arquitectura** (NORM-CAL-001). Revisión anual y próxima revisión 2027-10-10. Su ratificación formal sigue siendo necesaria. |
| AUD-03 | Resuelto con fuente fechada | Se sustituyeron los marcadores `[VALIDAR]` de BUS_nat_01, BUS_nat_02 y BUS_nat_03 por atribuciones al *Informe Anual Integrado 2024* (publicado en 2025), *Plan Estratégico 2025-2027* (febrero de 2025) y *Plan de Transición Climática 2025* de Naturgy. Los datos sujetos a cambios siguen exigiendo comprobar la publicación corporativa/CNMV más reciente. |
| AUD-04 | Abierto, controlado | BUS_nat_05 es un modelo genérico; se mantiene `needs_review` y sólo es apto para descubrimiento, no para describir la organización real. |
| AUD-05 | Cerrado | Se mantiene la advertencia de contenido ilustrativo de Process Studio como restricción de uso. |
| AUD-06 | Resuelto | Se corrigió la procedencia del ejemplo SDD: reemplaza los inexistentes `bus_03`, `bus_04` y `bus_06` por BUS_nat_03, BUS_nat_04 y BUS_nat_06. |
| AUD-07 | Resuelto | El esquema de metadatos curados está normalizado; `source_path` se ha restaurado como ruta original bajo `Context/`. |
| AUD-08 | Resuelto | Todos los activos usan prefijos globales y nombres únicos. |

## Control residual

Los activos con `curation_status: provisional` requieren ratificación antes de que un asistente los presente como norma corporativa definitiva. Los activos `needs_review` conservan restricciones específicas de evidencia o contenido ilustrativo; el motor de grounding debe comunicar estas limitaciones.

## Segunda revisión crítica: aptitud para diseñar la metodología

| ID | Estado | Hallazgo y control aplicado |
|---|---|---|
| AUD-09 | Resuelto | El corpus carecía de un puente operativo entre Context Studio, Process Studio y OpenSpec. Se añadió `METH_sdd_context_process_playbook.md`, que define fases, artefactos canónicos, handoffs y gates. Es una línea base de diseño, no una metodología aprobada. |
| AUD-10 | Resuelto | Existían enlaces a un playbook ausente y referencias abreviadas ambiguas. Se reemplazaron por nombres de activos existentes. |
| AUD-11 | Resuelto | Varias guías se marcaban `curated` pese a contener afirmaciones de producto, comandos o normativa sin evidencia primaria integrada. Se reclasificaron como `needs_review`; las herramientas IBM Internal pasan a `restricted_grounding`. |
| AUD-12 | Resuelto | No había segmentación para recuperar el contexto con seguridad y baja ambigüedad. Se añadió `META_context_studio_ingestion_plan.md` con contextos lógicos, precedencia, ACL y pruebas de aceptación MCP. |

### Riesgo residual prioritario

El mayor riesgo ya no es de formato, sino de evidencia y operación: confirmar capacidades en la instancia concreta de ICA/Process Studio, validar OpenSpec/Cline/Kiro instalados, ratificar las tres políticas provisionales y nombrar dueños de proceso para cada caso de uso antes de declarar la metodología como corporativa.
