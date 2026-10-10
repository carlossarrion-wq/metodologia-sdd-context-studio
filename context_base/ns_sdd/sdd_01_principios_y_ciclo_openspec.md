---
id: sdd_01
namespace: ns_sdd
title: "Spec Driven Development con OpenSpec: principios y ciclo de trabajo"
type: methodology
tags: [SDD, OpenSpec, OPSX, ciclo-de-vida, quality-gates, Kiro, Cline, roles]
entities: [OpenSpec, Kiro, Cline, Context Studio, Process Studio]
source: "Documentación oficial OpenSpec (openspec.dev, github.com/Fission-AI/OpenSpec) + normativa interna NORM-SEC-001, NORM-IA-001, NORM-CAL-001"
status: available
priority: tier1_critical
created: "2026-10-10"
---

# Spec Driven Development con OpenSpec

## 1. Qué es y por qué OpenSpec

**Spec Driven Development (SDD)**: antes de escribir código, el equipo y la IA acuerdan por escrito **qué** se va a construir (requisitos y escenarios) y **cómo** (diseño y tareas). El código se deriva de la especificación, y la especificación se mantiene viva junto al código.

**OpenSpec** es el marco SDD estándar de la metodología:

- Código abierto (licencia MIT), basado en Markdown plano dentro del repositorio.
- Funciona con más de 30 asistentes de IA, incluidos **Kiro** y **Cline**, mediante comandos `/opsx:*` y *skills*.
- Ligero e iterativo: cualquier artefacto se puede actualizar en cualquier momento.
- Pensado también para sistemas existentes (*brownfield*): se especifican **cambios** sobre el comportamiento actual.
- Personalizable con `openspec/config.yaml` (contexto y reglas corporativas) y con *schemas* propios.

## 2. Conceptos clave

| Concepto | Qué es | Dónde vive |
|---|---|---|
| **Spec** (especificación) | Comportamiento vigente del sistema, organizado por capacidad (*capability*) | `openspec/specs/<capacidad>/spec.md` |
| **Change** (cambio) | Propuesta de modificación: por qué, qué cambia, cómo y con qué tareas | `openspec/changes/<id-cambio>/` |
| **Delta spec** | Diferencias que un cambio introduce en las specs (ADDED / MODIFIED / REMOVED / RENAMED) | `openspec/changes/<id-cambio>/specs/` |
| **Archive** | Al cerrar un cambio, sus deltas se fusionan en las specs vigentes y el cambio se archiva | `openspec/changes/archive/AAAA-MM-DD-<id>/` |
| **Schema** | Define qué artefactos tiene un cambio y en qué orden | `openspec/schemas/` (corporativo: `naturgy-sdd`) |
| **Config** | Contexto y reglas que la IA recibe al generar cada artefacto | `openspec/config.yaml` |

> **Regla de oro**: las specs describen lo que el sistema **hace hoy**; los changes describen lo que **va a cambiar**. Nunca se edita una spec vigente a mano: se modifica mediante un change.

## 3. Ciclo de trabajo (flujo OPSX)

```
 Idea / necesidad de negocio
        │
        ▼
 [1] EXPLORAR ──────── /opsx:explore      (opcional; clarificar y acotar)
        │
        ▼
 [2] PROPONER ─────── /opsx:propose <id>  → proposal.md, specs/ (deltas), design.md, tasks.md
        │
        ▼
 [G1] REVISIÓN DE LA PROPUESTA (humana)   ◄── quality gate obligatorio
        │
        ▼
 [3] IMPLEMENTAR ──── /opsx:apply         → código + tests, marca tareas en tasks.md
        │
        ▼
 [4] VERIFICAR ────── /opsx:verify + pipeline CI   (implementación = artefactos)
        │
        ▼
 [G2] REVISIÓN DE CÓDIGO (PR) Y QUALITY GATES      ◄── quality gate obligatorio
        │
        ▼
 [5] ARCHIVAR ─────── /opsx:archive       → deltas fusionados en openspec/specs/
        │
        ▼
 [G3] DESPLIEGUE Y VALIDACIÓN DE NEGOCIO
```

| Paso | Comando | Responsable humano | Salida |
|---|---|---|---|
| 1. Explorar | `/opsx:explore` | Desarrollador + Product Owner | Alcance acotado, preguntas resueltas |
| 2. Proponer | `/opsx:propose <id>` | Desarrollador | Carpeta del cambio con los 4 artefactos |
| G1. Revisar propuesta | — | Product Owner (qué) + Tech Lead (cómo) | Propuesta aprobada en la PR de especificación |
| 3. Implementar | `/opsx:apply` | Desarrollador | Código, tests, tareas marcadas |
| 4. Verificar | `/opsx:verify`, `openspec validate --strict` | Desarrollador | Coherencia spec ↔ código |
| G2. Revisar código | — | Revisor (≠ autor) | PR aprobada, pipeline en verde |
| 5. Archivar | `/opsx:archive` | Desarrollador | Specs vigentes actualizadas |
| G3. Desplegar | Pipeline | Product Owner | Validación en preproducción |

**Perfil de comandos**: el perfil por defecto incluye `explore`, `propose`, `apply`, `sync` y `archive`. La metodología exige el **perfil ampliado** para disponer de `/opsx:verify`:

```bash
openspec config profile   # seleccionar perfil ampliado
openspec update           # regenerar comandos en el proyecto
```

## 4. Quality gates y su relación con la normativa

| Gate | Qué se comprueba | Normativa |
|---|---|---|
| **G1 – Propuesta** | Requisitos con escenarios verificables; datos y clasificación; ficha AI Act; seguridad en el diseño; fuera de alcance; tareas pequeñas y trazadas | NORM-CAL-001 §3-5, NORM-SEC-001 §4.1-4.2, NORM-IA-001 §7 |
| **G2 – Código** | Tests por escenario, quality gates del pipeline, revisión humana, `openspec validate --strict` sin errores | NORM-CAL-001 §6-7 y §12, NORM-SEC-001 §4.4 |
| **G3 – Despliegue** | Definition of Done, validación del Product Owner, cambio archivado | NORM-CAL-001 §13 |

> Recomendación: separar en dos PR distintas la **propuesta** (solo `openspec/changes/<id>/`) y la **implementación**. Así G1 se aprueba antes de generar código.

## 5. Roles

| Rol | Responsabilidad en SDD |
|---|---|
| **Product Owner** | Define la necesidad, aprueba `proposal.md` y los escenarios de las specs, valida en preproducción |
| **Desarrollador** | Conduce la IA en cada paso, revisa y corrige todos los artefactos generados, es responsable del resultado |
| **Tech Lead / Arquitecto** | Aprueba `design.md`, vela por los patrones de referencia y los ADR |
| **Revisor** | Revisa la PR de código frente a la spec (no puede ser el autor) |
| **Asistente IA (Kiro / Cline)** | Genera borradores de artefactos, implementa tareas, verifica coherencia. Nunca aprueba |

## 6. Kiro o Cline: criterio de elección

| Criterio | **Cline + OpenSpec** | **Kiro + OpenSpec** |
|---|---|---|
| Integración OpenSpec | Workflows en `.clinerules/workflows/` + skills en `.cline/skills/` | Prompts en `.kiro/prompts/` + skills en `.kiro/skills/` |
| Modelos | Configurable: **Amazon Bedrock** en región UE con credenciales corporativas | Modelos gestionados por Kiro [VERIFICAR opciones de residencia de datos y uso vía cuenta AWS corporativa] |
| Solapamiento | Ninguno | Kiro tiene su propio sistema de specs nativo (requirements/design/tasks): **no usarlo** para evitar dos fuentes de verdad |
| Reglas corporativas | `.clinerules/` | Steering files (`.kiro/steering/`) |
| Automatización | Hooks y MCP | Agent hooks y MCP |
| Modo planificación | Modos **Plan / Act** | Modo *spec* / *vibe* |

**Recomendación de la metodología**:
- **Herramienta por defecto: Cline + OpenSpec con Amazon Bedrock**, porque cumple directamente la política de uso de IA (IA-01, IA-02, IA-09).
- **Kiro + OpenSpec** se admite si se valida su configuración de modelos y datos. Se usará solo el flujo OpenSpec (no las specs nativas de Kiro) y los steering files para la normativa.

## 7. Comandos de referencia

| Dónde | Comando | Uso |
|---|---|---|
| Terminal | `npm install -g @fission-ai/openspec@latest` | Instalar o actualizar (requiere Node.js ≥ 20.19) |
| Terminal | `openspec init` | Inicializar el proyecto y elegir herramienta (Kiro / Cline) |
| Terminal | `openspec update` | Regenerar comandos tras actualizar o cambiar de perfil |
| Terminal | `openspec list` / `openspec view` | Ver cambios activos / panel interactivo |
| Terminal | `openspec show <id>` | Ver un cambio o spec |
| Terminal | `openspec validate <id> --strict` | Validar el formato de un cambio (usar en CI) |
| Terminal | `openspec archive <id>` | Archivar desde terminal |
| Terminal | `openspec config set telemetry.enabled false` | Desactivar la telemetría anónima (obligatorio en entorno corporativo) |
| Chat IA | `/opsx:explore`, `/opsx:propose`, `/opsx:apply`, `/opsx:verify`, `/opsx:archive` | Flujo de trabajo |

> La sintaxis exacta del comando en el chat depende de la herramienta (por ejemplo `/opsx:propose` o `/opsx-propose`). `openspec init` muestra la forma correcta para cada herramienta.
