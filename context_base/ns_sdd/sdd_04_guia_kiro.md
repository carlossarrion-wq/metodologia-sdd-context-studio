---
id: sdd_04
namespace: ns_sdd
title: "Guía de uso: OpenSpec con Kiro"
type: tool_guide
tags: [Kiro, OpenSpec, steering, hooks, MCP, configuración]
entities: [Kiro, OpenSpec, AWS, Context Studio]
source: "Documentación OpenSpec (supported tools) + documentación de Kiro (validar versión vigente)"
status: available
priority: tier2_important
created: "2026-10-10"
---

# Guía de uso: OpenSpec con Kiro

## 1. Principio

En Kiro, **OpenSpec es la única fuente de verdad de las especificaciones**. Kiro dispone de un sistema de specs propio (requirements.md, design.md, tasks.md en `.kiro/specs/`), pero NO DEBE usarse en proyectos de la metodología, para evitar dos especificaciones divergentes.

| Funcionalidad de Kiro | ¿Se usa? | Para qué |
|---|---|---|
| Specs nativas (`.kiro/specs/`) | **No** | Sustituidas por OpenSpec |
| Steering files (`.kiro/steering/`) | **Sí** | Normativa corporativa y contexto técnico permanente |
| Agent hooks | **Sí** | Automatizar validaciones (p. ej., `openspec validate`) |
| MCP | **Sí, solo servidores aprobados** | Context Studio y herramientas corporativas (SEC-35) |
| Modo vibe sin spec | Solo exploración | Nunca para código que vaya a producción |

> Requisito previo: validar con Seguridad y el Comité de IA la configuración de modelos y el tratamiento de datos de Kiro frente a NORM-IA-001 (IA-01, IA-02, IA-09). [VERIFICAR]

## 2. Instalación en un proyecto

```bash
npm install -g @fission-ai/openspec@<versión corporativa>
openspec config set telemetry.enabled false
cd mi-aplicacion
openspec init            # seleccionar "kiro"
openspec config profile  # seleccionar perfil ampliado (incluye verify)
openspec update
```

OpenSpec instala en el proyecto:

| Ruta | Contenido |
|---|---|
| `.kiro/skills/openspec-*/SKILL.md` | Skills del flujo OpenSpec |
| `.kiro/prompts/opsx-<id>.prompt.md` | Comandos de chat (`propose`, `apply`, `verify`, `archive`...) |

Después se copian del repositorio plantilla `openspec/config.yaml`, `openspec/schemas/naturgy-sdd/` y los steering files.

## 3. Steering files corporativos

| Fichero | Contenido |
|---|---|
| `.kiro/steering/product.md` | Proceso de negocio, usuarios, KPIs del caso de uso |
| `.kiro/steering/tech.md` | Stack, servicios AWS aprobados, librerías permitidas |
| `.kiro/steering/structure.md` | Estructura del repositorio y convenciones de código |
| `.kiro/steering/sdd-openspec.md` | Reglas del flujo (abajo) |
| `.kiro/steering/normativa.md` | Resumen de reglas SEC / IA / CAL / REG con sus identificadores |

Contenido mínimo de `sdd-openspec.md`:

```markdown
# Flujo de trabajo obligatorio
- Toda funcionalidad nueva o cambio de comportamiento empieza con /opsx:propose.
- NO uses las specs nativas de Kiro (.kiro/specs). La fuente de verdad es openspec/.
- NO implementes código sin que exista un cambio en openspec/changes/ aprobado por una persona.
- Durante /opsx:apply, implementa una tarea cada vez y márcala en tasks.md.
- Si durante la implementación cambia el comportamiento, actualiza primero el delta spec.
- Cada escenario de las specs debe tener al menos un test automatizado.
- Antes de cerrar: /opsx:verify y `openspec validate <id> --strict`.
- Nunca incluyas secretos ni datos personales reales en código, specs o prompts.
```

## 4. Hooks recomendados

| Hook | Disparador | Acción |
|---|---|---|
| Validar cambio | Al guardar ficheros en `openspec/changes/**` | Ejecutar `openspec validate <id> --strict` y mostrar errores |
| Recordar tests | Al guardar ficheros de `src/**` | Pedir al agente que compruebe que existe test para el escenario afectado |
| Detección de secretos | Antes de commit | Ejecutar el escáner corporativo de secretos |

## 5. Uso diario

| Paso | Acción en Kiro |
|---|---|
| Explorar | Invocar el comando `opsx-explore` en el chat |
| Proponer | `opsx-propose <id-cambio>` → revisar y editar los artefactos generados → abrir PR de propuesta |
| Implementar | Tras la aprobación: `opsx-apply` (sesión de chat nueva, contexto limpio) |
| Verificar | `opsx-verify` + pipeline |
| Archivar | `opsx-archive` tras fusionar la PR de código |

La forma exacta de invocar los comandos en Kiro la indica `openspec init`.

## 6. Buenas prácticas

- **Contexto limpio**: iniciar una sesión nueva para `apply` tras aprobar la propuesta.
- Revisar **cada** artefacto generado; la IA propone, la persona decide (IA-11).
- No aprobar ejecuciones de comandos sin leerlos (SEC-36).
