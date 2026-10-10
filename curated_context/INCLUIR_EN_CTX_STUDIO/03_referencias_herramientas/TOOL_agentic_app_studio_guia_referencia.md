---
id: "tool-agentic-app-studio-guia-referencia"
title: "Agentic App Studio (IBM ICA) — Guía de referencia"
category: tooling
document_type: "reference"
source_path: "context_base/ns_development_tools/agentic_app_studio_guia_referencia.md"
source_identifier: "agentic_app_studio_guia_referencia"
authority: reference
curation_status: needs_review
version: "1.0"
curated_on: "2026-10-10"
usage: "restricted_grounding"
information_classification: "IBM Internal / restringido a usuarios autorizados"
---

> **Estado de curación:** Contenido de referencia normalizado para grounding. Sus afirmaciones sobre producto, normativa, comandos o configuración deben validarse contra la versión oficial, contrato/licencia aplicable y entorno objetivo antes de convertirlas en una regla de la metodología.

# Agentic App Studio (IBM ICA) — Guía de referencia

> Guía de referencia de **IBM ICA Agentic App Studio** (el componente de ICA para **construir y orquestar aplicaciones agénticas**; a veces referido coloquialmente como **"Agent Studio"**): qué es, su posición dentro de **IBM Consulting Advantage (ICA)**, sus componentes núcleo, capacidades clave, gobierno y casos de uso.
>
> **Fuente:** *IBM Consulting Advantage — Mastermap* (documentación oficial de la plataforma ICA, 13/08/2026), secciones de Agentic App Studio, Builder Studio, Agentic AI Core y Control Tower. Clasificación original: *IBM Internal*. Contenido reformulado y estructurado para el feed; se conservan nombres de producto y componentes por fidelidad técnica.
>
> **Nota de nomenclatura:** en ICA, el motor de construcción/orquestación de agentes se denomina **"Agentic App Studio"**. Cuando en el proyecto se habla de **"Agent Studio"** suele referirse a este producto. (Existe también **"Agentic App Studio 2.0 [Beta]"**, una re-arquitectura que introduce **Temporal** como motor de workflow junto a **Langflow** y separa dónde se definen las aplicaciones de dónde ejecutan los agentes.)
>
> **Complementa:** `TOOL_context_studio_guia_referencia.md` (los agentes consumen el contexto de Context Studio vía MCP), `TOOL_process_studio_guia_referencia.md` y `TOOL_product_studio_guia_referencia.md`.

---

## 1. Qué es Agentic App Studio

**Agentic App Studio** es una **plataforma y toolkit empresarial** dentro de ICA donde se **diseñan, configuran y despliegan aplicaciones agénticas** con una **interfaz low-code**. Permite a arquitectos, desarrolladores y usuarios de negocio **componer visualmente workflows agénticos** con **gobierno y observabilidad**, combinando **agentes de IA, lógica determinista y data products**.

Es un **constructor low-code de aplicaciones agénticas** diseñado para **escalar la automatización multi-agente** sobre los procesos core del negocio: habilita la ejecución end-to-end de procesos mediante la orquestación de agentes autónomos y workflows predefinidos, con dashboards de observabilidad para monitorización y rendimiento en tiempo real.

- Está **desplegado en las instancias global y regional** de ICA. *(Nota: el framework Autogen no está disponible en instancias regionales.)*
- Está diseñado para **ejecutarse nativamente en la nube de elección** del cliente con sus proveedores preferidos, integrándose con el tooling nativo y minimizando el *vendor lock-in*.
- En una aplicación se pueden crear agentes en **distintas plataformas cloud** y orquestarlos para construir workflows complejos que escalan la automatización dirigida por agentes a bajo coste.

**Infraestructura que aporta** para ejecutar agentes de forma segura, eficiente y a escala:
- una **capa única de orquestación** que coordina todos los agentes;
- **comprensión de negocio integrada** (los agentes trabajan con tus datos, sistemas y procesos);
- **gobierno y supervisión** fuertes (comportamiento responsable y conforme a políticas);
- **implantación más rápida** con plantillas listas y bloques reutilizables.

### 1.1 Posición dentro de IBM Consulting Advantage

Agentic App Studio es uno de los componentes de **ICA**, junto con **Context Studio** (contexto empresarial que los agentes consumen vía MCP) y **Process Studio** (diseño de la transformación agéntica de procesos), y con los **assets de ICA** (Assistants, Agents, Agentic Applications) y modelos (optimizados para **watsonx**, con soporte de terceros). En la cadena de valor: Context Studio **fundamenta** → Process Studio **diseña** el blueprint → Agentic App Studio **construye y ejecuta** las aplicaciones agénticas.

### 1.2 Usuarios previstos
- **Enterprise architects** — diseñan y escalan la aplicación en entornos cliente.
- **Developers y AI engineers** — configuran agentes, orquestaciones e integraciones.
- **Consultores y SMEs de negocio** — definen blueprints de proceso y seleccionan plantillas.
- **Equipos de operaciones y gobierno** — gestionan cumplimiento, telemetría y mejora continua.

---

## 2. Componentes núcleo

| Componente | Descripción |
|---|---|
| **Marketplace** | Plantillas de aplicaciones agénticas preconstruidas y específicas por industria (p. ej. *document intelligence*, *customer self-service*), que cubren gran parte de los requisitos comunes. |
| **Builder Studio** | Entorno **low-code** para diseñar, configurar y lanzar apps agénticas, corriendo nativamente sobre los proveedores cloud del cliente e integrándose con sus herramientas. Orquesta con **Langflow** (diseñar, validar en *playground*, publicar endpoints de workflows). |
| **Agentic AI Core** | Infraestructura fundacional que gestiona el ciclo de vida de los agentes, la comunicación, la seguridad y la memoria, para que las aplicaciones multi-agente corran de forma fiable y a escala. |
| **Control Tower** | Interfaz centralizada de **gobierno y observabilidad** (logging prompt/salida, *cost tracking*, *drift detection*, *policy enforcement* y *human-in-the-loop*), integrada con la herramienta de Observability para *tracing* y métricas. |

### 2.1 Builder Studio (detalle)
Lienzo low-code donde arquitectos, desarrolladores y usuarios de negocio **componen visualmente** workflows agénticos orquestando agentes, lógica y objetivos de negocio, y data products. Conecta la **intención de negocio con la ejecución técnica**.

### 2.2 Agentic AI Core (detalle)
Espina dorsal arquitectónica de Agentic App Studio: servicios fundacionales y runtime modular, cloud-native y reutilizable para acelerar e industrializar el desarrollo agéntico. Responsable de fiabilidad, escalabilidad y cumplimiento de políticas. Capacidades:
- **Agent lifecycle management** — registrar, configurar y gestionar el ciclo de vida de agentes nativos o contenedorizados.
- **Multi-agent orchestration** — secuenciación de tareas, resolución de dependencias, manejo de excepciones y coordinación por eventos.
- **MCP & A2A gateways** — soporte de **Model Context Protocol (MCP)** y **Agent-to-Agent (A2A)** para comunicación segura y asíncrona entre agentes, incluidos agentes de terceros o en plataformas distintas.
- **Security & identity management** — autenticación/autorización unificada, *policy enforcement* y gestión de tokens.
- **Data contracts** — memoria a largo plazo, *prompt stores* y persistencia de configuración para comportamiento contextual y autónomo.

### 2.3 Control Tower (detalle)
Capa centralizada de gobierno, evaluación y observabilidad: visibilidad profunda del comportamiento del agente (logging prompt-to-output, *cost tracking*, *drift detection*), con *policy enforcement* y *human-in-the-loop*. Mide el uso de LLM, **asigna costes por workflow o unidad de negocio** y sigue el valor realizado (FinOps).

---

## 3. Capacidades clave

- **Orquestación de workflows agénticos low-code:** lienzo visual *drag-and-drop* para componer workflows multi-agente (agentes fundacionales y funcionales); soporta lógica de orquestación autónoma (planners/controllers) y flujos deterministas basados en reglas; define dependencias de tareas, orden de ejecución, paso de contexto y manejo de excepciones; edición colaborativa y control de versiones.
- **Crear, conectar y desplegar agentes entre frameworks:** integración nativa con **Azure AI Foundry (Semantic Kernel)**, **AWS Bedrock Agents**, **IBM watsonx Orchestrate**, **IBM Consulting Advantage** y frameworks open-source (**Autogen, Strands, LangGraph**); añadir/importar agentes o conectar catálogos de terceros; despliegue contenedorizado sobre el Agentic AI Core o en la nube preferida.
- **Conectar agentes a data products y contexto de negocio:** acceso a data products gobernados de la *enterprise data foundation*; consumo de datos operacionales/analíticos de confianza; integración con **knowledge graphs** empresariales.
- **Conectividad empresarial segura vía MCP Gateway:** comunicación segura y auditable entre agentes y sistemas (SAP, Salesforce, ServiceNow, Microsoft 365, Workday); aplica políticas de seguridad (IAM, OAuth, API keys); abstrae conectores para reutilizar agentes y minimizar lock-in.
- **Evaluación, guardrails y observabilidad embebidos:** monitorización/evaluación en tiempo real (comportamiento, precisión, sesgo, calidad); guardrails (filtros de contenido, reglas de escalado, validación de salida); visualización de *traces* (acciones, razonamiento, *tool calls*); integración con Control Tower (success rate, latencia, drift, ROI por workflow).
- **Orquestación multi-agente unificada en YAML:** el Builder Studio genera un **YAML** low-code que describe la orquestación entre frameworks/plataformas.

---

## 4. Gobierno, confianza y cumplimiento

Agentic App Studio incorpora **gobierno por diseño** en cada componente:
- **Identidad y control de acceso:** SSO, RBAC y permisos granulares vía IAM.
- **Explicabilidad y auditabilidad:** trazabilidad completa de cada acción agéntica.
- **Seguridad y control de riesgo:** validación de salida, reglas de escalado y *human override*.
- **Privacidad del dato:** linaje, *masking* y soberanía regional.
- **Evaluación continua:** *performance scoring*, detección de sesgo y monitorización de cumplimiento integradas en el runtime.

---

## 5. Beneficios

| Beneficio | Detalle |
|---|---|
| **Entrega más rápida** | Plantillas preconstruidas cubren **50-80%** de requisitos comunes (p. ej. *DocuFlow* para revisión/enrutamiento documental); mejora la contención de *self-service*; Builder Studio + YAML reducen setup, esfuerzo de orquestación y coste. |
| **Orquestación cross-platform low-code** | Orquestación por chat en la nube preferida que genera YAML low-code; conecta y gobierna workflows entre SAP, Salesforce, ServiceNow y ecosistemas de hyperscalers. |
| **Catálogo central de agentes** | Registro unificado con metadatos de agente (YAML) y versionado. |
| **Mayor calidad y cumplimiento** | *Policy enforcement*, logging, *cost tracking*, *drift detection*; agentes de validación comprueban completitud, consistencia y adherencia a estándares. |
| **Gestión del ciclo de vida (AgentOps)** | Ciclo completo: diseño → despliegue → monitorización → evaluación → mejora continua. |
| **Data product foundation** | Data assets modulares y gobernados como contexto compartido y memoria a largo plazo. |
| **Integración por estándares abiertos** | Compatibilidad **MCP, A2A y REST** para interoperabilidad segura y escalable. |
| **Escalabilidad y transparencia** | Workflows multi-agente orquestados end-to-end con observabilidad total (spans, traces, acciones LLM, métricas). |
| **Observabilidad y FinOps embebidos** | Dashboards unificados de comportamiento, cumplimiento y transparencia de coste. |

---

## 6. Casos de uso (ejemplos ICA)

| Dominio | Ejemplo | Interacción de plataforma |
|---|---|---|
| **Atención al cliente** | App agéntica de **self-service** que combina asistentes conversacionales y automatización backend para resolver consultas end-to-end (order tracking, reembolsos, actualizaciones de estado) sin intervención humana. | Salesforce Service Cloud + Azure AI Foundry + ICA Agentic AI Core |
| **Finanzas** | **Procure-to-Pay**: resolución de anomalías, *matching* de facturas, detección de discrepancias y comunicación con proveedores mediante agentes conectados. | AWS Bedrock + SAP Joule |
| **RR.HH.** | Movilidad y retención de talento: identificar *flight risks*, sugerir roles internos y orquestar compensación/aprobaciones. | SAP SuccessFactors + Azure AI Foundry |
| **Cumplimiento** | Análisis de impacto de cambios regulatorios: evaluación de nuevas regulaciones, contratos afectados y flujos de remediación. | Google Vertex AI + ServiceNow |
| **Reporting regulatorio** | **Regulate-AI**: automatiza reporting ESG/financiero/estatutario con orquestación agéntica que valida, agrega y envía datos entre sistemas con trazabilidad. | SAP + Workiva + ICA Agentic AI Core |
| **Seguros** | **Claims-AI**: automatiza el ciclo de siniestros (intake, validación, adjudicación, liquidación) integrando datos internos y de terceros. | Guidewire + SAP + Bedrock |

> Estos ejemplos ilustran orquestación cross-platform (SAP, Salesforce, ServiceNow, Azure, AWS, Google Cloud) manteniendo gobierno, seguridad y observabilidad a escala.

---

## 7. Relevancia para el proyecto UFD

En el enfoque vigente (CRM a medida sobre la base fundacional **Wattyo**), Agentic App Studio es la herramienta de ICA para **construir y orquestar los agentes** que operan los procesos de UFD:
- **Consume el contexto de UFD** (ontologías + documentos) publicado en **Context Studio** vía MCP, para que los agentes razonen sobre la realidad del cliente.
- Ejecuta la orquestación multi-agente de los *journeys* (SAC, SDC, PDS, comunicaciones) con **gobierno, guardrails y observabilidad** (Control Tower), alineado con la exigencia de UFD de IA con **control y trazabilidad** y de que el beneficio supere el coste (FinOps/coste de inferencia).
- Se integra con sistemas empresariales vía **MCP Gateway** (patrón coherente con la capa de integración del proyecto), y puede desplegarse **nativamente sobre la nube del cliente** (AWS), en línea con el despliegue de Wattyo.

---

## Referencias

- **Documento fuente:** *IBM Consulting Advantage — Mastermap* (ica-mastermap, 13/08/2026), secciones Agentic App Studio, Builder Studio, Agentic AI Core, Control Tower y casos de uso. Clasificación original: *IBM Internal*. Contenido reformulado para el feed.
- **Guías relacionadas:** `TOOL_context_studio_guia_referencia.md`, `TOOL_process_studio_guia_referencia.md`, `TOOL_product_studio_guia_referencia.md`, `METH_sdd_context_process_playbook.md`.
- **Estándares/tecnologías citados:** MCP (Model Context Protocol), A2A (Agent-to-Agent), Langflow, Temporal (AAS 2.0), YAML, watsonx Orchestrate, Azure AI Foundry, AWS Bedrock, LangGraph, Autogen, Strands.
