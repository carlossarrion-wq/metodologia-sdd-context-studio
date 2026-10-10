---
id: "sdd-sdd-02"
title: "OpenSpec: estructura del repositorio y formato de los artefactos"
category: methodology
document_type: "reference"
source_path: "context_base/ns_sdd/sdd_02_estructura_y_artefactos.md"
source_identifier: "sdd_02"
authority: methodological
curation_status: needs_review
version: "1.0"
curated_on: "2026-10-10"
usage: "grounding"
---

> **Estado de curación:** Contenido de referencia normalizado para grounding. Sus afirmaciones sobre producto, normativa, comandos o configuración deben validarse contra la versión oficial, contrato/licencia aplicable y entorno objetivo antes de convertirlas en una regla de la metodología.

# Estructura del repositorio y formato de los artefactos

## 1. Estructura

```
mi-aplicacion/
├── openspec/
│   ├── config.yaml                  # Contexto y reglas corporativas (ver `SDD_sdd_03_configuracion_corporativa_openspec.md`)
│   ├── schemas/
│   │   └── naturgy-sdd/             # Schema corporativo (ver `SDD_sdd_03_configuracion_corporativa_openspec.md`)
│   ├── specs/                       # FUENTE DE VERDAD: comportamiento vigente
│   │   ├── alertas-plazos/
│   │   │   └── spec.md
│   │   └── gestion-documental/
│   │       └── spec.md
│   └── changes/                     # Cambios en curso
│       ├── add-alerta-plazo-sdc/
│       │   ├── proposal.md
│       │   ├── compliance.md        # Artefacto corporativo (schema naturgy-sdd)
│       │   ├── design.md
│       │   ├── tasks.md
│       │   └── specs/
│       │       └── alertas-plazos/
│       │           └── spec.md      # Delta: ADDED / MODIFIED / REMOVED / RENAMED
│       └── archive/
│           └── 2026-10-02-add-validacion-boletin/
├── src/
├── tests/
└── docs/adr/                        # Decisiones de arquitectura
```

## 2. Convenciones de nombres

| Elemento | Convención | Ejemplos |
|---|---|---|
| **Id de cambio** | kebab-case, empieza por verbo | `add-alerta-plazo-sdc`, `update-plantilla-respuesta`, `remove-envio-carta` |
| **Capacidad** | kebab-case, sustantivo, una responsabilidad | `alertas-plazos`, `clasificacion-correos`, `gestion-documental` |
| **Requisito** | Nombre corto y único dentro de la capacidad | `Requirement: Alerta de vencimiento próximo` |
| **Escenario** | Describe la situación concreta | `Scenario: Expediente a 2 días hábiles del vencimiento` |

**Granularidad**: un cambio = una necesidad de negocio entregable en 1-2 semanas. Si un cambio supera ~15 tareas, se divide.

## 3. Artefactos de un cambio

| Artefacto | Pregunta que responde | Aprueba |
|---|---|---|
| `proposal.md` | ¿Por qué y qué cambia? | Product Owner |
| `specs/**/spec.md` (deltas) | ¿Qué comportamiento exacto tendrá el sistema? | Product Owner |
| `compliance.md` | ¿Qué datos, riesgos y normativa aplican? | Tech Lead (y Seguridad/DPO si se indica) |
| `design.md` | ¿Cómo se implementa? | Tech Lead / Arquitecto |
| `tasks.md` | ¿En qué pasos se construye? | Desarrollador + Tech Lead |

### 3.1 proposal.md

```markdown
## Why
<!-- Problema de negocio, proceso afectado (p. ej., SDC-01) y KPI que se quiere mejorar -->

## What Changes
<!-- Lista concreta de capacidades nuevas o modificadas. Marcar **BREAKING** si rompe algo -->

## Out of Scope
<!-- Lo que explícitamente NO se hace -->

## Impact
<!-- Specs afectadas, sistemas, integraciones, usuarios y equipos -->

## Value
<!-- Métrica de éxito y valor esperado (horas, plazos, errores, NPS) -->
```

### 3.2 Delta spec (`specs/<capacidad>/spec.md` dentro del cambio)

Formato obligatorio:

```markdown
## ADDED Requirements

### Requirement: Alerta de vencimiento próximo
El sistema SHALL notificar al tramitador asignado cuando un expediente SDC
esté a 2 días hábiles o menos de su plazo regulatorio.

#### Scenario: Expediente BT baremo a 2 días hábiles del plazo
- **GIVEN** un expediente SDC-01 abierto con fecha de solicitud completa registrada
- **WHEN** faltan 2 días hábiles para el plazo de propuesta previa (5 días hábiles)
- **THEN** el sistema envía una alerta al tramitador asignado
- **AND** registra la alerta en el historial del expediente

#### Scenario: Expediente ya resuelto
- **WHEN** el expediente tiene la propuesta previa emitida
- **THEN** el sistema NO genera alerta
```

| Sección delta | Uso | Contenido obligatorio |
|---|---|---|
| `## ADDED Requirements` | Comportamiento nuevo | Requisito completo + al menos un escenario |
| `## MODIFIED Requirements` | Cambia un requisito existente | El requisito **completo** en su nueva versión (no solo la diferencia) |
| `## REMOVED Requirements` | Elimina un requisito | Nombre, motivo y plan de migración |
| `## RENAMED Requirements` | Solo cambia el nombre | Nombre anterior y nuevo |

**Reglas de redacción**:
- Cada requisito contiene **SHALL** o **MUST**. Se mantienen las palabras clave en inglés aunque el texto esté en castellano, para que `openspec validate` las reconozca. [VERIFICAR el soporte multi-idioma de la versión instalada]
- Cada requisito tiene **al menos un escenario** con encabezado `#### Scenario:` (cuatro almohadillas).
- Los escenarios usan **GIVEN / WHEN / THEN / AND** en negrita, en formato de lista.
- Se incluyen escenarios de error y límite, no solo el camino feliz.
- Sin términos vagos ("rápido", "adecuado") sin cifra.
- Cada escenario será, como mínimo, un test automatizado (NORM-CAL-001, CAL-18).

> Relación con EARS: los requisitos siguen el espíritu de EARS (requisito ubicuo con SHALL) y los escenarios cubren los patrones por evento, por estado y no deseado. Este formato es el que aplica NORM-CAL-001 (CAL-02) en proyectos OpenSpec.

### 3.3 design.md

```markdown
## Context
<!-- Situación actual, restricciones, sistemas implicados -->

## Goals / Non-Goals

## Decisions
<!-- Decisión, alternativas consideradas y motivo. Las relevantes van además a docs/adr/ -->

## Architecture
<!-- Diagrama (Mermaid), componentes, servicios AWS del catálogo aprobado -->

## Data Model & Interfaces
<!-- Entidades, contratos OpenAPI, eventos -->

## Security & Threats
<!-- Modelado de amenazas ligero (STRIDE), autenticación, autorización, secretos -->

## AI Components (si aplica)
<!-- Modelo de Bedrock y versión, prompts versionados, Guardrails, evaluación, coste y latencia -->

## Testing Strategy
<!-- Cómo se prueba cada escenario: unitario, integración, e2e, evaluación IA -->

## Risks / Trade-offs
```

### 3.4 tasks.md

```markdown
## 1. Modelo de datos
- [ ] 1.1 Añadir campo `fecha_vencimiento` al expediente (Req: Alerta de vencimiento próximo)
- [ ] 1.2 Test unitario del cálculo de días hábiles (Scenario: ...a 2 días hábiles del plazo)

## 2. Servicio de alertas
- [ ] 2.1 ...
```

Reglas: tareas de menos de un día; cada tarea referencia el requisito o escenario que implementa; los tests son tareas explícitas; la última sección es siempre **Verificación** (`/opsx:verify`, pipeline, actualización de documentación).

### 3.5 compliance.md (artefacto corporativo)

```markdown
## Data Classification
<!-- Datos tratados y nivel: Público / Interno / Confidencial / Restringido (SEC-01) -->

## Personal Data (RGPD)
<!-- ¿Datos personales? Base jurídica, minimización, ¿consultar DPO? (SEC-22, SEC-23) -->

## Regulatory Separation
<!-- ¿Afecta a una distribuidora (UFD/Nedgia)? Cumplimiento REG-01 a REG-04 -->

## AI Act Classification (si usa IA)
<!-- Finalidad, personas afectadas, categoría de riesgo y justificación (IA-16, IA-17) -->

## Security Requirements
<!-- Requisitos de seguridad que se han incluido como escenarios en las specs (SEC-03, SEC-04) -->

## Approvals Needed
<!-- Seguridad, DPO, Comité de IA: sí/no y motivo -->
```

## 4. Specs vigentes (`openspec/specs/`)

- Se actualizan **solo** al archivar un cambio.
- Describen el sistema tal como es, por capacidades.
- En aplicaciones existentes sin specs, se crean progresivamente: el primer cambio que toca una capacidad documenta su comportamiento actual relevante.
