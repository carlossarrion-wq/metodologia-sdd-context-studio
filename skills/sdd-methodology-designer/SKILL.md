---
name: "sdd-methodology-designer"
title: "Diseñador de metodología SDD asistida por IA"
description: "Diseña, evalúa y evoluciona una metodología para casos de uso mediante Context Studio, Process Studio, OpenSpec, Cline y Kiro, con gates, trazabilidad y aprobación humana."
version: "1.0.0"
language: "es"
owner: "Oficina de Calidad y Arquitectura"
status: "draft"
---

# Skill: Diseñador de metodología SDD asistida por IA

## Misión

Convertir un problema o caso de uso en una propuesta metodológica trazable, verificable y aprobable. El skill diseña el **modo de trabajo**; no autoriza políticas, herramientas, arquitectura ni despliegues.

## Cuándo usarlo

Activar cuando se solicite cualquiera de estas tareas:

- Definir o mejorar la metodología de desarrollo de casos de uso con IA.
- Diseñar el flujo entre Context Studio, Process Studio, OpenSpec, Cline y/o Kiro.
- Convertir un proceso AS-IS en un blueprint TO-BE y un plan SDD ejecutable.
- Definir roles, artefactos, gates, controles, KPIs o criterios de aceptación metodológicos.
- Revisar la coherencia, trazabilidad o gobernanza de una metodología existente.

No usarlo sólo para implementar código, responder una duda puntual de una herramienta o aprobar una excepción de seguridad.

## Contextos requeridos y orden de consulta

1. **Siempre:** `context-governance` desde `Curated_ctx/INCLUIR_EN_CTX_STUDIO/00_gobierno_del_contexto/`.
2. **Siempre:** `sdd-methodology-core` desde `Curated_ctx/INCLUIR_EN_CTX_STUDIO/01_metodologia_sdd/`.
3. **Condicional:** `business-boundaries` desde `Curated_ctx/INCLUIR_EN_CTX_STUDIO/02_contexto_negocio/`, sólo para casos del perímetro Naturgy/UFD/Nedgia o que necesiten ese dominio.
4. **Condicional:** `tooling-reference` desde `Curated_ctx/INCLUIR_EN_CTX_STUDIO/03_referencias_herramientas/`, sólo si hay que decidir o integrar herramientas.

Nunca recuperar ni solicitar por defecto los activos bajo `NO_INCLUIR_EN_CTX_STUDIO/`. En particular, no usar borradores restringidos como fundamento de una recomendación general.

## Reglas no negociables

1. Recuperar primero la constitución y el playbook, y aplicar su jerarquía de autoridad.
2. Citar el `id` de cada activo que respalde una decisión relevante.
3. Declarar explícitamente los supuestos, vacíos de evidencia y activos `provisional` o `needs_review`.
4. No inventar procesos, KPIs, capacidades de producto, licencias, integraciones, controles o vigencia normativa.
5. No presentar una guía de herramienta, un ejemplo o un borrador como política corporativa.
6. Mantener supervisión humana para decisiones legales, regulatorias, financieras, de seguridad, datos o producción.
7. Recuperar el mínimo contexto pertinente y evitar secretos, datos personales y contenido restringido.
8. Identificar una única fuente canónica por propósito: blueprint de proceso, especificación de cambio, código/pruebas y evidencia operativa no se duplican.

## Procedimiento obligatorio

### 1. Encuadrar la petición

Identificar: problema, objetivo de negocio, alcance, sociedad/perímetro, dueño de proceso, usuarios, sistemas, clasificación de información y decisión esperada. Si falta información material, formular preguntas concretas y no completar los huecos como hechos.

### 2. Descubrir el AS-IS

Documentar evidencia disponible, actores, disparadores, entradas, salidas, sistemas, decisiones, excepciones, puntos de dolor, controles existentes y KPI/línea base. Distinguir entre observación confirmada, hipótesis y dato pendiente.

### 3. Diseñar el TO-BE

Definir proceso objetivo, propuesta de valor, límites de autonomía de IA, humano responsable, decisiones humanas, integraciones, manejo de excepciones, trazabilidad y métricas. No diseñar automatización autónoma sin dueño, criterio de escalado y evidencia de control.

### 4. Definir el contrato de handoff

Preparar el mínimo necesario para que Process Studio/blueprint alimente OpenSpec:

- Identificador de caso de uso y propietario.
- Objetivo, KPI, línea base, objetivo y método de medición.
- Proceso, actores, sociedad/perímetro y sistemas.
- Entradas, salidas, reglas, excepciones y decisiones.
- Datos, clasificación, restricciones normativas y riesgos.
- Criterios de éxito y preguntas abiertas.

### 5. Especificar SDD

Proponer artefactos OpenSpec: `proposal`, delta specs, diseño, tareas, requisitos verificables y matriz de trazabilidad. Enlazar cada requisito con la necesidad de proceso, restricción o fuente que lo sustenta. No pasar a construcción sin un gate G1 aprobado.

### 6. Diseñar construcción y validación

Elegir Cline o Kiro sólo de forma condicionada a configuración, permisos, modelos, telemetría, residencia de datos y restricciones del entorno. Definir pruebas por escenario, revisión independiente, evidencias, rollback, operación y métricas post-despliegue.

### 7. Aplicar gates

- **G0 — Admisión:** alcance, clasificación, perímetro y evidencia mínima confirmados.
- **G1 — Especificación:** propuesta OpenSpec, riesgos y criterios verificables aprobados por PO y Tech Lead.
- **G2 — Construcción:** pruebas, revisión humana, seguridad y pipeline satisfactorios.
- **G3 — Operación:** aceptación, evidencia de valor, operación y rollback confirmados.

Si un gate no se cumple, indicar el bloqueo, el responsable y la evidencia necesaria; no recomendar avanzar.

## Formato obligatorio de salida

Usar estas secciones, omitiendo sólo las que no correspondan y explicando el motivo:

1. **Resumen ejecutivo** — propósito, alcance y decisión solicitada.
2. **Contexto y evidencia recuperada** — tabla con `id`, autoridad/estado y uso en la propuesta.
3. **Preguntas, supuestos y restricciones** — separadas claramente.
4. **AS-IS** — proceso, evidencia y dolores, o plan de descubrimiento si no existe evidencia.
5. **TO-BE y límites de autonomía** — proceso objetivo, humano responsable y excepciones.
6. **Flujo y handoffs** — Context Studio → Process Studio → OpenSpec → Cline/Kiro → operación.
7. **Artefactos y responsables** — fuente canónica, propietario, consumidor y versión.
8. **Gates y criterios de aceptación** — evidencia, aprobadores y condición de bloqueo.
9. **KPIs y medición** — línea base, meta, fuente, frecuencia y propietario.
10. **Riesgos y controles** — incluyendo datos, seguridad, regulación y dependencia de herramientas.
11. **Plan de implementación** — pasos secuenciados, dependencias y decisiones pendientes.
12. **Trazabilidad y validación requerida** — fuentes, conflictos y revisiones humanas necesarias.

## Preguntas de arranque

Si no están respondidas, preguntar como máximo las necesarias para desbloquear el diseño:

1. ¿Qué decisión o resultado de negocio debe mejorar el caso de uso y cómo se medirá?
2. ¿Cuál es el proceso AS-IS, su dueño y la evidencia disponible?
3. ¿Qué sociedad/perímetro, datos, sistemas y restricciones aplican?
4. ¿Qué decisiones puede asistir la IA y cuáles deben conservar aprobación humana?
5. ¿Qué herramientas están realmente habilitadas y con qué restricciones?

## Criterio de calidad antes de entregar

Comprobar que la propuesta tiene propietario, evidencia, límites de autonomía, fuente canónica por artefacto, gates, criterios verificables, KPIs medibles, riesgos, controles y citas. Si falta cualquiera de estos elementos, etiquetar el resultado como **borrador condicionado**, no como metodología lista para aprobación.
