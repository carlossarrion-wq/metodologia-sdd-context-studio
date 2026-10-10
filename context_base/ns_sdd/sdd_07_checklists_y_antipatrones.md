---
id: sdd_07
namespace: ns_sdd
title: "SDD con OpenSpec: checklists de revisión, antipatrones y métricas"
type: methodology
tags: [checklist, revisión, antipatrones, buenas-prácticas, métricas, OpenSpec]
entities: [OpenSpec]
source: "Buenas prácticas de la metodología"
status: available
priority: tier1_critical
created: "2026-10-10"
---

# Checklists, antipatrones y métricas

## 1. Checklist G1 – Revisión de la propuesta

**proposal.md**
- [ ] Proceso de negocio y KPI identificados
- [ ] "What Changes" concreto; cambios incompatibles marcados como **BREAKING**
- [ ] "Out of Scope" explícito
- [ ] Métrica de valor medible

**Delta specs**
- [ ] Cada requisito contiene SHALL o MUST
- [ ] Cada requisito tiene al menos un escenario GIVEN/WHEN/THEN
- [ ] Hay escenarios de error, límite y permisos
- [ ] No hay términos vagos sin cifra
- [ ] Los MODIFIED incluyen el requisito completo
- [ ] `openspec validate <id> --strict` sin errores

**compliance.md**
- [ ] Datos clasificados y minimizados
- [ ] Separación regulatoria justificada (si es UFD o Nedgia)
- [ ] Clasificación AI Act (si usa IA)
- [ ] Aprobaciones adicionales identificadas

**design.md**
- [ ] Solo servicios del catálogo aprobado, o ADR justificando la desviación
- [ ] Modelado de amenazas
- [ ] Estrategia de pruebas por escenario
- [ ] Si usa IA: modelo y versión, Guardrails, conjunto de evaluación, coste y latencia

**tasks.md**
- [ ] Tareas de menos de un día, trazadas a requisitos o escenarios
- [ ] Tests como tareas explícitas
- [ ] Sección final de Verificación

## 2. Checklist G2 – Revisión de código

- [ ] Todas las tareas de `tasks.md` marcadas
- [ ] Cada escenario tiene al menos un test que lo verifica (no solo que ejecuta código)
- [ ] El código no implementa comportamiento que no esté en la spec
- [ ] `/opsx-verify` sin discrepancias
- [ ] Pipeline en verde (NORM-CAL-001 §12)
- [ ] Sin secretos ni datos personales reales en código, tests o fixtures
- [ ] Dependencias nuevas verificadas (existen, mantenidas, licencia compatible) (SEC-39)

## 3. Ejemplo de spec buena frente a mala

**Mala**:
```markdown
### Requirement: Alertas
El sistema debe avisar rápido de los expedientes que van a vencer.
```
Problemas: sin SHALL/MUST, sin escenarios, "rápido" sin cifra, no dice a quién ni cuándo.

**Buena**: ver `sdd_06`, requisito "Alerta de vencimiento próximo": plazo concreto (2 días hábiles), hora (08:00), destinatario (tramitador o supervisor), escenarios positivos, negativos y de límite.

## 4. Antipatrones

| Antipatrón | Consecuencia | Corrección |
|---|---|---|
| Generar código sin cambio aprobado ("vibe coding") | Código sin trazabilidad ni revisión de negocio | Gate G1 obligatorio |
| Aprobar artefactos generados sin leerlos | Specs incorrectas que se convierten en código incorrecto | La persona que aprueba responde del artefacto (IA-11) |
| Cambios enormes (> 15 tareas) | Contexto saturado, IA imprecisa, revisión imposible | Dividir en varios cambios |
| Corregir el código sin actualizar la spec | La spec deja de ser fuente de verdad | Primero el delta spec, después el código |
| Editar `openspec/specs/` a mano | Historial roto | Toda modificación pasa por un change y `archive` |
| Usar a la vez specs nativas de Kiro y OpenSpec | Dos verdades divergentes | Solo OpenSpec |
| Solo escenarios de camino feliz | Errores en producción en los casos límite | Regla obligatoria de escenarios de error |
| No archivar los cambios terminados | Specs vigentes desactualizadas | Archivar forma parte de la Definition of Done |
| Una sesión de IA para todo el ciclo | Contexto contaminado | Sesión o tarea nueva por paso |

## 5. Métricas del proceso SDD

| Métrica | Cálculo | Objetivo orientativo |
|---|---|---|
| Cambios con propuesta aprobada antes del código | Cambios con PR de propuesta aprobada / total | 100 % |
| Retrabajo de specs | Cambios cuya spec se modifica tras G1 / total | < 20 % |
| Cobertura de escenarios | Escenarios con test / total de escenarios | 100 % |
| Lead time del cambio | Desde `propose` hasta `archive` | [ADAPTAR] |
| Defectos atribuibles a la spec | Defectos en producción por requisito ausente o erróneo | Tendencia a la baja |
| Cambios archivados | Cambios archivados / cambios desplegados | 100 % |
