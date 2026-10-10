---
id: "sdd-sdd-03"
title: "OpenSpec: configuración y schema corporativos"
category: methodology
document_type: "reference"
source_path: "Context/ns_sdd/sdd_03_configuracion_corporativa_openspec.md"
source_identifier: "sdd_03"
authority: methodological
curation_status: needs_review
version: "1.0"
curated_on: "2026-10-10"
usage: "grounding"
---

> **Estado de curación:** Este contenido conserva el texto de origen y ha sido normalizado para su ingestión. Contiene afirmaciones o campos pendientes de validación; no debe usarse como fuente definitiva sin contrastar la procedencia indicada.

# Configuración y schema corporativos de OpenSpec

OpenSpec admite tres niveles de personalización. La metodología usa los dos primeros:

| Nivel | Fichero | Uso en la metodología |
|---|---|---|
| **Project config** | `openspec/config.yaml` | **Obligatorio**: contexto corporativo + reglas por artefacto |
| **Custom schema** | `openspec/schemas/naturgy-sdd/` | **Obligatorio**: añade el artefacto `compliance` |
| Global overrides | `~/.local/share/openspec/schemas/` | No se usa (se prefiere versionar en el repo) |

**Funcionamiento**: al generar cualquier artefacto, OpenSpec inyecta el `context` en el prompt de la IA. Las `rules` se inyectan solo en el artefacto correspondiente.

## 1. `openspec/config.yaml` (plantilla corporativa)

```yaml
# openspec/config.yaml — Plantilla corporativa v0.1
schema: naturgy-sdd

context: |
  Organización: Grupo Naturgy. [ADAPTAR: sociedad del proyecto, p. ej., UFD Distribución Eléctrica]
  Proceso de negocio afectado: [ADAPTAR, p. ej., SDC-01 Nuevo suministro BT baremo]
  Tipo de proyecto: caso de uso sencillo de mejora de procesos de negocio (2-6 semanas).

  Stack: [ADAPTAR, p. ej., Python 3.12 + FastAPI, React + TypeScript, PostgreSQL]
  Cloud: AWS, landing zone corporativa, regiones UE. IaC con [ADAPTAR: Terraform/CDK].
  IA en la aplicación: solo Amazon Bedrock con modelos del catálogo autorizado (NORM-IA-001).
  Testing: [ADAPTAR, p. ej., pytest, Playwright]. Cobertura mínima en código nuevo: 80 % líneas.

  Normativa que SIEMPRE aplica (citar por identificador):
  - NORM-SEC-001 Seguridad (SEC-XX)
  - NORM-IA-001 Uso de IA y AI Act (IA-XX)
  - NORM-CAL-001 Calidad (CAL-XX)
  - nat_04 Separación de actividades reguladas (REG-XX)
  Idioma de los artefactos: castellano. Palabras clave SHALL/MUST y GIVEN/WHEN/THEN en inglés.
  Glosario de negocio: bus_05 (UFD) y nat_06 (grupo).

rules:
  proposal:
    - Identificar el proceso de negocio (código SAC-XX, SDC-XX, etc.) y el KPI que se mejora.
    - Incluir la sección "Out of Scope".
    - Incluir la sección "Value" con una métrica de éxito medible.
    - Si el cambio supera ~15 tareas o 2 semanas, proponer dividirlo.
  specs:
    - Cada requisito contiene SHALL o MUST y tiene al menos un escenario.
    - Incluir escenarios de error, límite y permisos, no solo el camino feliz.
    - Prohibido usar términos vagos sin cifra (rápido, adecuado, intuitivo).
    - Los plazos regulatorios se expresan en días hábiles con la norma de referencia.
    - Incluir requisitos de seguridad (autorización por rol, auditoría) como escenarios (SEC-03, SEC-04).
  compliance:
    - Clasificar todos los datos tratados (SEC-01).
    - Si hay datos personales, indicar base jurídica y minimización (SEC-22).
    - Si el sistema usa IA, completar la clasificación AI Act (IA-16, IA-17). Si es alto riesgo o prohibido, detener y escalar.
    - Si afecta a UFD o Nedgia, justificar el cumplimiento de REG-01 a REG-04.
  design:
    - Usar solo servicios y patrones del catálogo de arquitectura; desviaciones con ADR (CAL-09).
    - Incluir modelado de amenazas ligero (SEC-05).
    - Si usa IA: modelo y versión fijados, región UE, Guardrails, conjunto de evaluación, coste y latencia (IA-07, CAL-27 a CAL-30).
    - Incluir la estrategia de pruebas por escenario.
  tasks:
    - Tareas de menos de un día, cada una con referencia al requisito o escenario.
    - Los tests son tareas explícitas.
    - Última sección "Verificación": /opsx:verify, openspec validate --strict, pipeline, documentación.
```

## 2. Schema corporativo `naturgy-sdd`

Se crea a partir del schema estándar:

```bash
openspec schema fork spec-driven naturgy-sdd
```

Después se edita `openspec/schemas/naturgy-sdd/schema.yaml` para añadir el artefacto `compliance` entre las specs y el diseño:

```yaml
# openspec/schemas/naturgy-sdd/schema.yaml (extracto: artefactos añadidos o modificados)
name: naturgy-sdd
version: 1
description: Flujo SDD corporativo con revisión de cumplimiento

artifacts:
  # proposal y specs: se mantienen como en spec-driven

  - id: compliance
    generates: compliance.md
    description: Clasificación de datos, RGPD, separación regulatoria y AI Act
    template: compliance.md
    instruction: |
      Analiza la propuesta y las specs. Completa cada sección de la plantilla
      citando los identificadores de la normativa (SEC-XX, IA-XX, REG-XX).
      Si detectas un uso prohibido o de alto riesgo según el AI Act, indícalo
      en "Approvals Needed" y recomienda NO continuar sin aprobación.
    requires:
      - proposal
      - specs

  - id: design
    # ... configuración original de design ...
    requires:
      - proposal
      - specs
      - compliance      # el diseño se apoya en el análisis de cumplimiento
```

Además se añade `templates/compliance.md` con la plantilla de sdd_02 §3.5. Se pueden ajustar también las plantillas `proposal.md` y `design.md` con las secciones corporativas de sdd_02.

Validación del schema:

```bash
openspec schema validate naturgy-sdd
openspec schema which naturgy-sdd
```

> Los nombres de campo del `schema.yaml` deben contrastarse con el fichero generado por `openspec schema fork` en la versión instalada. [VERIFICAR]

## 3. Repositorio plantilla corporativo

Para que todos los proyectos arranquen igual, se mantiene un **repositorio plantilla** con:

- `openspec/config.yaml` (sección 1)
- `openspec/schemas/naturgy-sdd/` (sección 2)
- Configuración de la herramienta: `.clinerules/` (ver `SDD_sdd_05_guia_cline.md`) o `.kiro/steering/` (ver `SDD_sdd_04_guia_kiro.md`)
- Pipeline CI con `openspec validate --strict` y los quality gates de NORM-CAL-001
- `docs/adr/` con la plantilla de ADR

## 4. Validación en CI

```yaml
# Paso de pipeline (pseudocódigo)
- name: Validar specs OpenSpec
  run: |
    npm install -g @fission-ai/openspec@<versión fijada>
    export OPENSPEC_TELEMETRY=0
    for change in $(ls openspec/changes | grep -v archive); do
      openspec validate "$change" --strict
    done
```

La versión de OpenSpec DEBE fijarse en el pipeline y en el repositorio plantilla, y actualizarse de forma controlada.
