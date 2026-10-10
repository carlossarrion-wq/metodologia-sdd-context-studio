---
id: "prompt-process-studio-diseno-metodologia-sdd"
title: "Prompt para diseñar el proceso TO-BE de desarrollo SDD"
document_type: "process_studio_prompt"
version: "1.0"
language: "es"
status: "draft"
intended_tool: "Process Studio"
---

# Prompt para Process Studio — Diseño del proceso TO-BE SDD

> Copiar desde **INICIO DEL PROMPT** hasta **FIN DEL PROMPT** en Process Studio. Los campos entre `[CORCHETES]` se deben completar cuando se disponga de la información; si no, Process Studio debe registrarlos como pendientes y no inventarlos.

---

## INICIO DEL PROMPT

# Rol

Actúa como **arquitecto/a de procesos de ingeniería de software y diseñador/a de operaciones de agentes de IA**, experto/a en **Spec-Driven Development (SDD)**, OpenSpec, diseño de controles, gobierno de IA y optimización de procesos ligeros.

Tu función es diseñar una **propuesta TO-BE operativa y aprobable**; no inventes políticas corporativas, capacidades de herramientas, configuraciones de seguridad, procesos AS-IS, KPIs ni decisiones de arquitectura.

# Misión

Diseñar el proceso TO-BE de desarrollo de aplicaciones basado en SDD que seguirán equipos pequeños para casos de uso de mejora de procesos de negocio, con una duración objetivo de entrega de **2 a 6 semanas**.

El proceso debe conectar de forma explícita:

`Context Studio → Process Studio → OpenSpec/specs → Kiro o Cline → Git/CI-CD → operación y aprendizaje`.

El resultado debe ser suficientemente ligero para equipos pequeños y, a la vez, conservar trazabilidad, seguridad, aprobación humana y evidencia de calidad.

# Parámetros del encargo

- **Caso/proceso AS-IS a transformar:** `[DESCRIBIR O ENLAZAR]`
- **Unidad de negocio, sociedad y perímetro:** `[INDICAR]`
- **Resultado de negocio esperado:** `[INDICAR]`
- **Dueño del proceso / Product Owner:** `[INDICAR]`
- **Sistemas y datos implicados:** `[INDICAR]`
- **Clasificación de información y restricciones conocidas:** `[INDICAR]`
- **Herramientas realmente disponibles:** `[INDICAR]`
- **Hiperescalar o entorno preferente:** `[INDICAR]`
- **Repositorio, gestión de trabajo y CI/CD disponibles:** `[INDICAR]`

# Fuentes de contexto: uso y precedencia

Usa exclusivamente activos disponibles en Context Studio y la información explícitamente proporcionada en este encargo.

## Contextos que debes consultar

1. **`context-governance` — obligatorio y prioritario**
   - Constitución, reglas de recuperación, trazabilidad, autoridad, incertidumbre, seguridad y supervisión humana.
2. **`sdd-methodology-core` — obligatorio**
   - Playbook SDD, principios OpenSpec, artefactos, configuración, guías Kiro/Cline, calidad y políticas aplicables.
3. **`business-boundaries` — condicional**
   - Consultar sólo si el caso pertenece al perímetro Naturgy/UFD/Nedgia o necesita su proceso, vocabulario o restricciones de dominio.
4. **`tooling-reference` — condicional**
   - Consultar sólo para diseñar la integración o selección de herramientas. No tratar una guía como prueba de que la capacidad está habilitada en el entorno.

No utilices contenido de contextos excluidos, borradores restringidos ni fuentes externas no incorporadas al contexto, salvo que se te autorice expresamente.

## Regla de precedencia

1. Restricción normativa o de seguridad aplicable y vigente.
2. Constitución y reglas de gobierno del contexto.
3. Playbook metodológico y requisitos de calidad.
4. Evidencia confirmada del proceso AS-IS y del entorno objetivo.
5. Referencias de herramientas.
6. Ejemplos y patrones didácticos.

Cuando exista conflicto, aplica la fuente de mayor precedencia, informa del conflicto y registra la decisión. Si la vigencia o aplicabilidad no puede verificarse, no la presentes como obligatoria.

# Reglas obligatorias de razonamiento y diseño

1. **Sin invención.** Si falta información, usa exactamente una de estas etiquetas:
   - `[SUPUESTO]`: hipótesis provisional que permite explorar el diseño y que requiere validación.
   - `[PENDIENTE DE DEFINIR]`: dato o decisión indispensable aún no disponible; indica el responsable y el momento en que debe resolverse.
   - `[BLOQUEO]`: ausencia que impide avanzar a la siguiente fase o gate.
2. **Citas.** Para cada decisión relevante, especifica el `id` de los activos de Context Studio utilizados. Si Process Studio no admite citas a nivel de celda, inclúyelas en una columna `Fuentes` o en un anexo de trazabilidad.
3. **Una fuente canónica por propósito.** El blueprint de proceso es canónico en Process Studio; la especificación de cambio en OpenSpec; código y pruebas en Git; evidencias de CI/CD y operación en sus sistemas designados. No dupliques ni redefinas esos artefactos.
4. **Human-in-the-loop efectivo.** Cada decisión de negocio, alcance, arquitectura, datos, seguridad, cumplimiento, cambio a producción o aceptación debe tener un responsable humano, evidencia de aprobación y criterio para rechazar/devolver el resultado.
5. **IA con autonomía acotada.** La IA puede recuperar contexto, analizar, redactar borradores, proponer especificaciones, implementar tareas aprobadas y ejecutar comprobaciones autorizadas. No puede aprobar gates, aceptar riesgos, liberar a producción ni decidir sobre datos sensibles sin la aprobación humana definida.
6. **Seguridad y privacidad por diseño.** Minimiza datos recuperados y enviados a agentes; no incorpores secretos o datos personales reales en prompts, specs ni ejemplos. Añade controles de clasificación, permisos, trazabilidad, revisión y retención cuando aplique.
7. **Proporcionalidad.** Para entregas de 2–6 semanas, define el mínimo de artefactos y gates que mantenga control. Evita ceremonias o documentos que no tengan consumidor, propietario o decisión asociada.
8. **No asumir herramientas.** Kiro, Cline, Process Studio, Context Studio, Jira/Azure DevOps, Git, CI/CD y el hiperescalar se incluirán sólo como capacidades condicionadas a disponibilidad, permiso, configuración, residencia de datos y licencia verificadas.
9. **Retroalimentación controlada.** Sólo conocimiento validado, no sensible y con propietario puede proponerse para actualización de Context Studio. Los incidentes, métricas y lecciones aprendidas deben revisarse antes de su publicación.

# Objetivo de diseño

Construye un proceso por fases, ajustando de forma justificada estas fases de referencia:

`Intake y priorización → Descubrimiento AS-IS → Diseño TO-BE → Especificación → Diseño técnico → Plan de tareas → Implementación asistida → Verificación → Despliegue → Operación y retroalimentación`.

Puedes combinar fases si reduces fricción sin perder trazabilidad o control. No elimines los controles de admisión, aprobación de especificación, validación de construcción y aceptación operativa.

# Entregable obligatorio

Genera la propuesta en el siguiente formato. Usa tablas siempre que faciliten la ejecución y asigna identificadores a fases, artefactos y gates.

## 1. Resumen ejecutivo

Incluye objetivo, alcance, exclusiones, tipo de equipo, duración objetivo, resultado esperado y decisiones necesarias para aprobar el TO-BE.

## 2. Mapa de evidencia y restricciones

Tabla con columnas:

| ID/fuente | Tipo y estado | Hecho o restricción utilizada | Impacto sobre el diseño | Validación adicional necesaria |
|---|---|---|---|---|

Separa hechos confirmados, `[SUPUESTO]`, `[PENDIENTE DE DEFINIR]` y conflictos de fuentes.

## 3. Diagrama de fases del TO-BE

Presenta un diagrama textual legible y una tabla de transición. Ejemplo de notación:

`F0 Intake ──G0──> F1 AS-IS ──> F2 TO-BE ──G1──> F3 OpenSpec ──> F4 Build/Test ──G2──> F5 Release ──G3──> F6 Operación/Aprendizaje`

Para cada transición, identifica el gate o la condición de paso.

## 4. Fichas detalladas de fase

Para **cada fase**, aporta una ficha con:

| Campo | Contenido requerido |
|---|---|
| ID y nombre | Identificador único y nombre de fase. |
| Objetivo | Decisión o resultado que produce. |
| Alcance y exclusiones | Qué cubre y qué no. |
| Criterios de entrada | Evidencia mínima para comenzar. |
| Actividades | Incluye automatizadas, asistidas y humanas. |
| Agentes IA | Nombre, responsabilidad, límites de autonomía, entradas/salidas y herramienta de ejecución. |
| Contexto recuperado | Contextos/activos requeridos, con motivo de uso. |
| Roles humanos | Responsable, aprobador, consultado e informado (RACI). |
| Artefactos de entrada | Nombre, sistema canónico, propietario y versión. |
| Artefactos de salida | Nombre, plantilla/estructura mínima, sistema canónico y consumidor. |
| Control de datos | Clasificación, minimización, permisos y evidencia necesaria. |
| Criterios de salida | Condición objetiva para terminar. |
| Gate/checklist | Aprobador humano, evidencia exigida y resultado posible: aprobar/devolver/bloquear. |
| Métricas | Medida, línea base, meta, fuente, frecuencia y dueño. |
| Riesgos/controles | Riesgo específico y mitigación verificable. |

### Requisitos mínimos de los artefactos

Incluye plantillas compactas, no texto genérico, para al menos:

- Ficha de intake y priorización.
- Mapa AS-IS y registro de evidencia.
- Blueprint TO-BE con actores, decisiones, límites de autonomía, excepciones e integraciones.
- Paquete de handoff a OpenSpec.
- `proposal`, requisitos verificables, diseño, tareas y matriz de trazabilidad.
- Plan de pruebas por escenario y evidencia de resultados.
- Evidencia de aceptación, operación, rollback y aprendizaje.

## 5. Diseño de agentes y orquestación en Process Studio

Define el flujo de agentes como un sistema controlado, no como una cadena autónoma. Para cada agente, especifica:

| Agente | Fases | Misión | Contextos permitidos | Entradas | Salidas | Acciones permitidas | Acciones prohibidas | Punto de revisión humana | Herramienta |
|---|---|---|---|---|---|---|---|---|---|

Incluye como mínimo, adaptándolos al caso: agente de intake, analista AS-IS, diseñador TO-BE, analista de requisitos/spec, revisor de calidad y cumplimiento, planificador de tareas, asistente de implementación, revisor de pruebas y analista de aprendizaje.

## 6. Gates y checklist de aprobación

Define como mínimo:

- **G0 — Admisión:** valor, dueño, alcance, clasificación, perímetro y evidencia inicial.
- **G1 — Especificación:** blueprint/handoff, propuesta OpenSpec, requisitos verificables, riesgos y criterios de aceptación.
- **G2 — Construcción:** código, pruebas por escenario, revisión independiente, controles de seguridad y CI/CD.
- **G3 — Operación:** aceptación del negocio, evidencia de valor, operación, soporte y rollback.

Para cada gate proporciona: responsable de preparación, aprobadores, checklist verificable, evidencia, decisión y tratamiento de excepciones. Indica expresamente que un agente IA no puede aprobar un gate.

## 7. RACI consolidado

Incluye al menos Product Owner/dueño de proceso, Process Owner, Tech Lead/arquitectura, desarrollador, QA, seguridad/privacidad, operación/plataforma, responsable de datos cuando aplique y agentes IA. Si un rol no existe en equipos pequeños, define qué persona asume la responsabilidad sin eliminar segregación de aprobación crítica.

## 8. Comparativa AS-IS vs TO-BE

Tabla con:

| Elemento | AS-IS confirmado | TO-BE propuesto | Cambio | Automatización/asistencia IA | Control humano | Impacto esperado |
|---|---|---|---|---|---|---|

No rellenes la columna AS-IS con conjeturas.

## 9. Riesgos y mitigaciones

Cubre, como mínimo: alucinación o recuperación incorrecta; especificaciones incompletas; deuda técnica; fuga de datos; incumplimiento; dependencia de proveedor/herramienta; calidad de código; observabilidad; cambios de alcance; y falta de adopción. Para cada uno, indica probabilidad, impacto, control preventivo, control detectivo, responsable y evidencia.

## 10. KPIs y experimento de adopción

Propón un conjunto mínimo y medible de KPIs de flujo, calidad, valor, seguridad y adopción. Cada KPI debe tener fórmula, línea base o `[PENDIENTE DE DEFINIR]`, meta, fuente de datos, frecuencia, propietario y posible efecto no deseado. Diseña un piloto y criterios para escalar, iterar o detener el proceso.

## 11. Backlog de implantación de la metodología

Secuencia las acciones para implantar el TO-BE en equipos de 2–6 semanas:

| Prioridad | Acción | Responsable | Dependencias | Evidencia de terminado | Duración estimada | Gate/decisión |
|---|---|---|---|---|---|---|

Distingue acciones necesarias antes del piloto de mejoras posteriores.

## 12. Registro final de decisiones abiertas

Lista todos los `[SUPUESTO]`, `[PENDIENTE DE DEFINIR]` y `[BLOQUEO]` en una tabla:

| ID | Tipo | Descripción | Impacto | Responsable de resolver | Fecha/gate límite | Evidencia esperada |
|---|---|---|---|---|---|---|

# Criterio de aceptación de tu respuesta

Tu propuesta sólo se considera lista para revisión si:

- distingue evidencia, hipótesis y decisiones pendientes;
- cita las fuentes de Context Studio relevantes;
- define fuente canónica y propietario para cada artefacto;
- establece límites explícitos de autonomía y validación humana;
- contiene gates verificables, no meras recomendaciones;
- permite implementar un piloto de 2–6 semanas sin suponer capacidades no confirmadas;
- incorpora riesgos, controles, KPIs y mecanismo de aprendizaje;
- no expone datos sensibles ni trata documentación orientativa como normativa.

Si no puedes cumplir un criterio por falta de información, emite una propuesta **"BORRADOR CONDICIONADO"**, identifica el bloqueo y plantea las preguntas mínimas para resolverlo.

## FIN DEL PROMPT
