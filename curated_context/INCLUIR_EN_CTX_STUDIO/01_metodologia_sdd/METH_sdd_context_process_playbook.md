---
id: "meth-sdd-context-process-playbook"
title: "Playbook de diseño de metodología: Context Studio, Process Studio y SDD"
category: "methodology_design"
document_type: "playbook"
authority: "operational"
curation_status: "provisional"
methodology_status: "design_baseline"
owner: "Oficina de Calidad y Arquitectura"
version: "0.1"
curated_on: "2026-10-10"
review_cycle: "quarterly"
usage: "grounding"
---

# Playbook de diseño de metodología: Context Studio, Process Studio y SDD

## Propósito

Este activo no define una metodología corporativa aprobada. Es el punto de partida controlado para diseñarla mediante IA, usando el corpus curado sin convertir documentación de producto, ejemplos o políticas provisionales en hechos. Su resultado esperado es una metodología versionada, aprobada por los roles responsables y aplicable por caso de uso.

## Principios de diseño

1. **Contexto antes que generación.** Todo agente recupera sólo los activos pertinentes, con su autoridad y estado, antes de proponer una decisión.
2. **Un artefacto canónico por propósito.** Process Studio conserva el blueprint de proceso; OpenSpec conserva la especificación de cambio; el repositorio conserva código y pruebas. Ninguna herramienta duplica la fuente de verdad de otra.
3. **Descubrir, diseñar, especificar, construir, validar.** No se permite saltar desde una idea a código generado.
4. **Human-in-the-loop real.** La IA redacta, relaciona y comprueba; las personas autorizadas aprueban decisiones de negocio, riesgo, arquitectura, seguridad y despliegue.
5. **Evidencia trazable.** Todo requisito, decisión y control debe apuntar a su fuente, supuesto, propietario y evidencia de validación.

## Flujo objetivo y handoffs

| Fase | Herramienta principal | Entrada | Salida canónica | Aprobador / gate |
|---|---|---|---|---|
| 0. Admisión | Context Studio | Problema, sociedad, proceso, clasificación inicial | Ficha de caso y mapa de fuentes | Product Owner + Riesgo/Datos si aplica |
| 1. Descubrimiento AS-IS | Process Studio | Evidencia de proceso y contexto recuperado | Proceso AS-IS, KPI base, dolor y supuestos | Dueño de proceso |
| 2. Diseño TO-BE | Process Studio | AS-IS aprobado y restricciones | Blueprint TO-BE, autonomía, KPIs, riesgos, integraciones | Dueño de proceso + Arquitectura |
| 3. Especificación | OpenSpec + Cline/Kiro | Blueprint aprobado | proposal, delta specs, design, compliance y tasks | G1: PO + Tech Lead |
| 4. Construcción | Cline o Kiro + repositorio | Cambio OpenSpec aprobado | Código, tests, IaC y evidencias | G2: revisor independiente |
| 5. Validación y operación | Pipeline + Process Studio/Context Studio | Release candidata y métricas | Evidencia de aceptación, métricas y cambio archivado | G3: PO + operación |
| 6. Aprendizaje | Context Studio | Incidentes, métricas y decisiones | Contexto actualizado y propuesta de mejora | Dueño del contexto |

## Contrato mínimo entre Process Studio y OpenSpec

Antes de `propose`, el blueprint debe aportar: identificador de caso de uso; propietario; proceso y sociedad; objetivo/KPI y línea base; actores; decisiones y zona de autonomía; entradas/salidas; sistemas y datos; excepciones; riesgos; restricciones regulatorias; criterios de éxito. El `proposal.md` debe enlazar ese identificador y declarar cualquier supuesto no resuelto. Si falta una pieza, el agente formula una pregunta o crea una tarea de descubrimiento: no la inventa.

## Política de selección de herramientas

- **Context Studio:** fuente de grounding y relaciones; no sustituye la evidencia primaria ni autoriza decisiones.
- **Process Studio:** descubrimiento y blueprint de proceso. Sus capacidades deben verificarse en la instancia habilitada antes de convertirlas en un paso obligatorio.
- **Cline + OpenSpec:** opción base para repositorios cuando la configuración de modelo, telemetría, MCP y permisos cumpla las restricciones del proyecto.
- **Kiro + OpenSpec:** alternativa condicionada a validación de residencia de datos, modelos y configuración. No se usan sus specs nativas en paralelo.

## Gates no negociables

- **G0:** clasificación de datos, sociedad/perímetro y fuente de proceso confirmados.
- **G1:** propuesta OpenSpec aprobada; requisitos verificables, controles y riesgos trazados.
- **G2:** pruebas por escenario, revisión humana, seguridad y pipeline satisfactorios.
- **G3:** evidencia de valor y aceptación; rollback/operación definidos; cambio archivado.

## Preguntas que la IA debe resolver antes de recomendar

1. ¿Qué fuente primaria respalda el hecho y cuál es su fecha/propietario?
2. ¿La información se puede recuperar y exponer al agente según su clasificación y permisos?
3. ¿Cuál es la sociedad y existen restricciones de separación regulatoria?
4. ¿Qué decisión toma la IA, cuál una persona y qué evidencia queda?
5. ¿Cuál es el KPI base, objetivo, método de medición y periodo?
6. ¿Qué artefacto es canónico y dónde se versiona?

## Antipatrones específicos

- Cargar el corpus completo en cada consulta: incrementa ruido y conflictos.
- Tratar una guía de producto como garantía de capacidad instalada.
- Usar políticas `provisional` como obligaciones corporativas definitivas.
- Diseñar un TO-BE sin evidencia AS-IS ni dueño de proceso.
- Implementar desde un blueprint sin requisitos verificables OpenSpec.
