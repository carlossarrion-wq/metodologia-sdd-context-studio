---
id: sdd_05
namespace: ns_sdd
title: "Guía de uso: OpenSpec con Cline (herramienta por defecto)"
type: tool_guide
tags: [Cline, OpenSpec, Amazon-Bedrock, clinerules, workflows, plan-act, MCP, auto-approve]
entities: [Cline, OpenSpec, Amazon Bedrock, Context Studio]
source: "Documentación OpenSpec (supported tools) + documentación de Cline (validar versión vigente)"
status: available
priority: tier1_critical
created: "2026-10-10"
---

# Guía de uso: OpenSpec con Cline

Cline + OpenSpec + Amazon Bedrock es la **combinación por defecto** de la metodología.

## 1. Configuración del modelo (obligatoria)

| Parámetro | Valor |
|---|---|
| API Provider | **Amazon Bedrock** |
| Autenticación | Perfil AWS corporativo / SSO (nunca claves personales) (IA-09, SEC-17) |
| Región | UE (`eu-west-1`, `eu-central-1` o `eu-south-2`) o perfil de inferencia entre regiones UE (IA-02) |
| Modelo modo **Plan** | Claude gama alta u otro modelo de alto razonamiento del catálogo (explorar, proponer, diseñar) |
| Modelo modo **Act** | Claude gama media (implementar tareas) |
| Telemetría de Cline | Desactivada [VERIFICAR opción en la versión instalada] |

## 2. Instalación en un proyecto

```bash
npm install -g @fission-ai/openspec@<versión corporativa>
openspec config set telemetry.enabled false
cd mi-aplicacion
openspec init            # seleccionar "cline"
openspec config profile  # seleccionar perfil ampliado (incluye verify)
openspec update
```

OpenSpec instala en el proyecto:

| Ruta | Contenido |
|---|---|
| `.cline/skills/openspec-*/SKILL.md` | Skills del flujo OpenSpec |
| `.clinerules/workflows/opsx-<id>.md` | Workflows invocables desde el chat (`/opsx-propose`, `/opsx-apply`...) |

Después se copian del repositorio plantilla `openspec/config.yaml`, `openspec/schemas/naturgy-sdd/` y las reglas de `.clinerules/`.

## 3. Reglas corporativas (`.clinerules/`)

| Fichero | Contenido |
|---|---|
| `.clinerules/01-sdd-openspec.md` | Reglas del flujo (mismo contenido que `sdd-openspec.md` de la guía de Kiro, sdd_04 §3) |
| `.clinerules/02-normativa.md` | Resumen de reglas SEC / IA / CAL / REG con sus identificadores |
| `.clinerules/03-tech.md` | Stack, servicios AWS aprobados, convenciones de código |
| `.clinerules/04-negocio.md` | Proceso de negocio, glosario esencial y KPIs del caso de uso |
| `.clinerules/workflows/` | Generado por OpenSpec; no editar a mano (se regenera con `openspec update`) |

Todos estos ficheros se versionan y revisan como código (SEC-37).

## 4. Correspondencia Plan / Act con el flujo OpenSpec

| Paso OpenSpec | Modo Cline | Motivo |
|---|---|---|
| `/opsx-explore` | **Plan** | Analizar sin modificar ficheros |
| `/opsx-propose <id>` | **Act** (solo escribe en `openspec/changes/`) | Genera los artefactos |
| Revisión y ajuste de artefactos | **Plan** | Discutir cambios antes de aplicarlos |
| `/opsx-apply` | **Act**, tarea a tarea | Implementación controlada |
| `/opsx-verify` | **Plan** | Contrastar código frente a specs |
| `/opsx-archive` | **Act** | Fusiona deltas en `openspec/specs/` |

## 5. Auto-aprobación (SEC-36)

| Acción | Configuración |
|---|---|
| Leer ficheros del proyecto | Auto-aprobar |
| Editar ficheros del proyecto | Auto-aprobar **solo** dentro de `openspec/changes/` durante `propose`; en `apply`, aprobación manual o por tarea |
| Ejecutar comandos | Manual, salvo lista blanca: `openspec validate`, `openspec list`, `openspec show`, tests, linter, build |
| Navegador / MCP | Manual; solo servidores MCP aprobados (SEC-35) |
| Modo "YOLO" o equivalente | **Prohibido** |

## 6. MCP recomendados

| Servidor | Uso |
|---|---|
| **Context Studio** (si expone MCP) | Consultar normativa, contexto de negocio y ejemplos durante `explore` y `propose` |
| Gestor de tareas corporativo (Jira / Azure DevOps) | Vincular el cambio con la historia o ticket |
| Documentación AWS | Consultar servicios del catálogo aprobado |

## 7. Uso diario

1. **Crear rama** `feature/<id-cambio>`.
2. **Plan**: `/opsx-explore` con el Product Owner si el alcance no está claro.
3. **Act**: `/opsx-propose <id-cambio>`. Revisar los cinco artefactos, corregir y ejecutar `openspec validate <id> --strict`.
4. Abrir la **PR de propuesta** (gate G1) y esperar aprobación.
5. **Nueva tarea de Cline** (contexto limpio) → `/opsx-apply`, tarea a tarea, con tests.
6. `/opsx-verify` + pipeline → abrir la **PR de código** (gate G2).
7. Tras fusionar: `/opsx-archive` y commit de las specs actualizadas.

## 8. Buenas prácticas

- Usar **una tarea de Cline por paso** del flujo para mantener el contexto limpio.
- Vigilar el coste: el panel de Cline muestra tokens y coste por tarea; si un `apply` se dispara, revisar el tamaño del cambio.
- Si la IA se desvía de la spec, detener, corregir el delta spec o la tarea y reanudar.
