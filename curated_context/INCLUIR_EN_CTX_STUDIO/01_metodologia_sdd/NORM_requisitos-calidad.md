---
id: "norm-norm-cal-001"
title: "Requisitos de calidad en el desarrollo de aplicaciones"
category: governance
document_type: "policy"
source_path: "context_base/ns_marco_normativo/requisitos-calidad.md"
source_identifier: "NORM-CAL-001"
authority: governing
curation_status: provisional
policy_status: provisional
policy_owner: "Oficina de Calidad y Arquitectura"
effective_date: "2026-10-10"
review_cycle: "annual"
next_review_date: "2027-10-10"
version: "1.1"
curated_on: "2026-10-10"
usage: "grounding"
---

> **Estado de gobierno:** Política operativa provisional, con vigencia desde el 2026-10-10. Propietario designado: **Oficina de Calidad y Arquitectura**. Requiere ratificación formal antes de tratarse como política corporativa definitiva.

# Requisitos de calidad

## 1. Objeto y alcance

Este documento define los requisitos de calidad mínimos que DEBEN cumplir los artefactos del proceso SDD (specs, diseño, plan de tareas, código, tests) y las aplicaciones resultantes.

Los umbrales están pensados para **casos de uso sencillos de mejora de procesos de negocio**. Los equipos pueden ser más exigentes, nunca menos.

**Convención:** "DEBE" = obligatorio; "NO DEBE" = prohibido; "DEBERÍA" = recomendado. Identificadores CAL-XX.

## 2. Principios

- **La spec es la fuente de verdad**: si el código y la spec discrepan, uno de los dos es un defecto.
- **Trazabilidad**: necesidad → requisito → diseño → tarea → código → test.
- **Calidad verificable**: todo requisito de calidad se comprueba automáticamente en el pipeline o con un checklist explícito.
- **Misma exigencia para el código generado por IA** que para el escrito por personas.

## 3. Calidad de la especificación (requirements)

- **CAL-01** Cada requisito funcional DEBE tener un identificador único y estable.
- **CAL-02** Los requisitos DEBEN redactarse en formato **EARS** (Easy Approach to Requirements Syntax), por ejemplo:
  - *Ubicuo*: "El sistema DEBE …"
  - *Por evento*: "CUANDO <evento>, el sistema DEBE …"
  - *Por estado*: "MIENTRAS <estado>, el sistema DEBE …"
  - *No deseado*: "SI <condición>, ENTONCES el sistema DEBE …"
- **CAL-03** Cada historia de usuario DEBE tener criterios de aceptación verificables y no ambiguos (evitar "rápido", "intuitivo", "adecuado" sin cifra).
- **CAL-04** La spec DEBE incluir requisitos no funcionales cuantificados: rendimiento, disponibilidad, volumen, accesibilidad y seguridad.
- **CAL-05** La spec DEBE recoger qué queda **fuera de alcance**.
- **CAL-06** La spec DEBE ser aprobada por el responsable de negocio (Product Owner) antes de pasar a diseño.

**Checklist de spec:** identificadores · EARS · criterios verificables · RNF cuantificados · fuera de alcance · datos y clasificación · ficha AI Act si aplica · aprobación PO.

## 4. Calidad del diseño (design)

- **CAL-07** El diseño DEBE referenciar los requisitos que satisface (trazabilidad).
- **CAL-08** El diseño DEBE incluir: diagrama de arquitectura, modelo de datos, contratos de interfaces (OpenAPI para APIs REST), gestión de errores y estrategia de pruebas.
- **CAL-09** El diseño DEBE usar los patrones y servicios del catálogo de arquitectura de referencia; toda desviación DEBE justificarse en un ADR (Architecture Decision Record).
- **CAL-10** El diseño DEBE ser revisado por un arquitecto o persona designada antes de generar el plan de tareas.

## 5. Calidad del plan de tareas (tasks)

- **CAL-11** Cada tarea DEBE ser pequeña (completable e integrable en menos de un día), estar referenciada a uno o más requisitos e incluir sus tests asociados.
- **CAL-12** El plan DEBE ordenar las tareas por dependencias y permitir entregas incrementales verificables.

## 6. Calidad del código

| Métrica (código nuevo) | Umbral mínimo | Herramienta [ADAPTAR] |
|---|---|---|
| Bugs y vulnerabilidades críticos o bloqueantes | 0 | SonarQube / SonarCloud |
| Duplicación | ≤ 3 % | SonarQube |
| Complejidad cognitiva por función | ≤ 15 | SonarQube |
| Code smells mayores | 0 nuevos sin justificar | SonarQube |
| Linter y formateador | 0 errores | ESLint/Prettier, Ruff/Black, etc. |
| Deuda técnica (rating de mantenibilidad) | A | SonarQube |

- **CAL-13** El código DEBE pasar el quality gate de análisis estático antes de fusionarse.
- **CAL-14** El código DEBE seguir las convenciones del lenguaje y del libro blanco (nombres, estructura de proyecto, gestión de errores, logging).
- **CAL-15** Toda pull request DEBE tener al menos una revisión humana aprobatoria. NO DEBE fusionarse código no revisado, se haya generado por IA o no.
- **CAL-16** La pull request DEBE referenciar la tarea y los requisitos que implementa.

## 7. Pruebas

- **CAL-17** Cobertura de tests unitarios sobre código nuevo ≥ **80 %** de líneas y ≥ **70 %** de ramas.
- **CAL-18** Cada criterio de aceptación de la spec DEBE tener al menos un test automatizado que lo verifique (test de aceptación o de integración).
- **CAL-19** DEBEN existir tests de integración para cada integración externa (APIs, bases de datos, colas, modelos de IA), con dobles de prueba en CI.
- **CAL-20** Los tests DEBEN ser deterministas; los tests inestables (flaky) DEBEN corregirse o aislarse en un plazo de [ADAPTAR] días.
- **CAL-21** Los tests generados por IA DEBEN revisarse para comprobar que verifican el comportamiento esperado y no solo reproducen la implementación.
- **CAL-22** Para flujos críticos de usuario DEBERÍA haber tests end-to-end.

## 8. Requisitos no funcionales de referencia

Valores por defecto si la spec no indica otros:

| Atributo | Valor por defecto |
|---|---|
| Tiempo de respuesta de API (p95) | < 500 ms (excluidas llamadas a modelos de IA) |
| Tiempo de respuesta con modelo de IA (p95) | < 10 s, con indicador de progreso o streaming |
| Disponibilidad | 99,5 % mensual en horario de negocio [ADAPTAR] |
| Accesibilidad | WCAG 2.2 nivel AA (EN 301 549) para interfaces de usuario |
| Navegadores | Últimas dos versiones de Chrome, Edge, Firefox y Safari |
| Idioma | [ADAPTAR – castellano y otros] |

- **CAL-23** Las interfaces DEBEN superar un análisis automático de accesibilidad (axe o equivalente) sin errores graves.

## 9. Observabilidad y operación

- **CAL-24** Las aplicaciones DEBEN emitir logs estructurados (JSON) con identificador de correlación, sin datos sensibles.
- **CAL-25** DEBEN definirse métricas técnicas (latencia, errores, uso) y alarmas mínimas en CloudWatch o la herramienta corporativa.
- **CAL-26** DEBE existir un runbook básico: arranque, parada, dependencias, alarmas y contacto de soporte.

## 10. Calidad de funcionalidades con IA generativa

- **CAL-27** Toda funcionalidad basada en un modelo de IA DEBE tener un **conjunto de evaluación** (mínimo 30 casos representativos, incluidos casos límite y adversarios) con el resultado esperado o criterios de valoración.
- **CAL-28** La spec DEBE fijar umbrales de calidad medibles, por ejemplo: precisión de extracción ≥ 95 %, tasa de respuestas fundamentadas (groundedness) ≥ 90 %, tasa de rechazo correcto de preguntas fuera de alcance.
- **CAL-29** Las evaluaciones DEBEN ejecutarse antes de producción y ante cualquier cambio de prompt, modelo o base de conocimiento (tests de regresión). Pueden usarse Amazon Bedrock Evaluations o el marco corporativo [ADAPTAR].
- **CAL-30** La spec DEBE fijar presupuestos de **coste por transacción** y **latencia**; el diseño DEBE justificar la elección del modelo frente a ellos.
- **CAL-31** Los prompts de sistema DEBEN versionarse en el repositorio y tratarse como código (revisión y tests).

## 11. Documentación

- **CAL-32** El repositorio DEBE contener: README (propósito, arranque local, despliegue), la spec, el diseño, los ADR y el runbook.
- **CAL-33** La spec y el diseño DEBEN mantenerse actualizados con cada cambio funcional (la documentación forma parte del Definition of Done).

## 12. Quality gates del pipeline CI/CD

| Gate | Momento | Bloqueante |
|---|---|---|
| Compilación, linter y formateo | Cada commit / PR | Sí |
| Tests unitarios y cobertura (CAL-17) | Cada PR | Sí |
| Análisis estático (CAL-13) | Cada PR | Sí |
| Seguridad: SAST, SCA, secretos, IaC (NORM-SEC-001) | Cada PR | Sí (críticas y altas) |
| Tests de integración y aceptación (CAL-18, CAL-19) | Antes de desplegar en preproducción | Sí |
| Evaluaciones de IA (CAL-29) | Antes de producción y ante cambios de prompt o modelo | Sí |
| Accesibilidad (CAL-23) | Antes de producción | Sí (errores graves) |
| DAST (si expuesta a Internet) | Antes de producción | Sí |

## 13. Definition of Done

Una tarea o caso de uso está terminado cuando:

- [ ] Cumple los criterios de aceptación de la spec y está trazado a sus requisitos
- [ ] Código revisado y aprobado por al menos una persona (CAL-15)
- [ ] Todos los quality gates del pipeline superados
- [ ] Tests automatizados para cada criterio de aceptación (CAL-18)
- [ ] Evaluaciones de IA superadas, si aplica (CAL-27 a CAL-29)
- [ ] Spec, diseño, ADR y runbook actualizados (CAL-33)
- [ ] Checklists de seguridad (NORM-SEC-001) e IA (NORM-IA-001) completados
- [ ] Desplegado en preproducción y validado por el Product Owner

## 14. Métricas del proceso

Para medir la eficacia del proceso SDD con IA, DEBERÍAN recogerse:

- **DORA**: frecuencia de despliegue, lead time de cambios, tasa de fallo de cambios, tiempo de recuperación.
- **Calidad de specs**: % de requisitos que cambian tras la aprobación; defectos atribuibles a la spec.
- **Uso de IA**: % de PRs asistidas por IA (IA-12), tasa de retrabajo sobre código generado, defectos en producción por origen.
- **Valor de negocio**: ahorro de horas o reducción de errores del proceso mejorado frente a lo estimado en el business case.
