---
id: "tool-ica-ibm-consulting-advantage-guia-referencia"
title: "IBM Consulting Advantage (ICA) — Guía de referencia (plataforma y piezas)"
category: tooling
document_type: "reference"
source_path: "Context/ns_development_tools/ica_ibm_consulting_advantage_guia_referencia.md"
source_identifier: "ica_ibm_consulting_advantage_guia_referencia"
authority: reference
curation_status: curated
version: "1.0"
curated_on: "2026-10-10"
usage: "restricted_grounding"
information_classification: "IBM Internal / restringido a usuarios autorizados"
---

> **Estado de curación:** Este contenido conserva el texto de origen y ha sido normalizado para su ingestión. No se han detectado marcadores explícitos de validación pendiente en el texto de origen.

# IBM Consulting Advantage (ICA) — Guía de referencia

> Guía breve de referencia de **IBM Consulting Advantage (ICA)**: qué es, para qué sirve, de qué piezas se compone (assets, assistants/agents, y los estudios Context Studio, Process Studio y Agentic App Studio) y cómo encajan entre sí. Sirve como marco general para las guías detalladas de cada producto de este namespace.
>
> **Fuente:** *IBM Consulting Advantage — Mastermap* (documentación oficial de la plataforma, 13/08/2026). Clasificación original: *IBM Internal*. Contenido reformulado y estructurado para el feed; se conservan los nombres de producto y componentes por fidelidad técnica. Confirmar disponibilidad/versiones contra el despliegue concreto.

---

## 1. Qué es ICA

**IBM Consulting Advantage (ICA)** es la **plataforma de entrega potenciada por IA** de IBM Consulting, diseñada para **acelerar la creación de valor a escala**. Da a los equipos acceso **curado, personalizado y basado en roles** a:
- **IBM Consulting Assistants** de IA generativa,
- **assets y modelos** de IA con propósito específico,
- y **métodos de entrega** flexibles.

Su objetivo es **simplificar cómo se consumen e integran** las soluciones críticas para el trabajo de consultoría (transformación de cliente, implantaciones SAP, migraciones a Azure/AWS, desarrollo de aplicaciones, BPO, seguridad, etc.), alineadas con el entorno y los procesos actuales y objetivo del cliente. En el proyecto de UFD, ICA es el **marco donde se enmarcan** las herramientas Context Studio, Process Studio y Agentic App Studio.

**Rasgos de plataforma:**
- **Despliegue:** instancias **global** y **regionales** de ICA; soporta además infraestructura gestionada por el cliente.
- **Modelos:** optimizada para **watsonx** y sus modelos predefinidos; admite despliegue con modelos de terceros o **BYO LLM** (en ese caso, la validación, integración y rendimiento del modelo son responsabilidad del cliente y IBM no garantiza la funcionalidad de los assistants fuera de watsonx).
- **Estándares:** interoperabilidad vía **MCP (Model Context Protocol)** y APIs; gobierno, seguridad y observabilidad transversales.

---

## 2. Piezas que componen ICA

### 2.1 Assets de ICA

Los **assets** son soluciones software ampliamente adoptadas que aumentan la productividad y generan resultados medibles. Tipos:

| Asset | Descripción |
|---|---|
| **Assistants** | Prompts de texto preconstruidos que ejecutan tareas concretas y aportan productividad al consultor/cliente. Integrados en ICA y sus extensiones. |
| **Assistant Packs** | Colecciones de assistants y agents organizados por dominio, industria o tecnología y alineados a una etapa del ciclo (advisory, design, development, operations). P. ej. *Everyday Assistants*. |
| **Agents** | Assistants "codificados" que usan múltiples **herramientas** (acceden a aplicaciones y fuentes de datos) para completar tareas por chat; seleccionan e invocan tools de forma autónoma según el prompt. |
| **Agentic Applications** | Software compuesto por **varios agentes autónomos** que razonan, actúan y colaboran para entregar resultados de negocio; siguen workflows predefinidos o deciden dinámicamente cómo proceder, delegar y adaptarse. |
| **Applications** | Programas para tareas específicas, con UI, workflows y APIs; horizontales (multi-uso) o verticales (por industria). |

### 2.2 Assistants y Agents (el ecosistema conversacional)

- **Assistants** — interactúan con el usuario por **lenguaje natural** (NLP/chatbot) para simplificar tareas (atención, RR.HH., asistencia a la codificación, etc.). Guían al modelo con preconditions/directives; ofrecen **fine-tuning sin código**; requieren input continuo del usuario.
- **Agents** — operan de forma **más autónoma**: estrategizan, descomponen problemas y ejecutan tareas sin input constante; usan **memoria persistente** y **planificación dinámica**. Aptos para tareas de decisión de alto nivel (investigación, monitorización, etc.).
- **IBM Consulting Assistants** — oferta **SaaS** de IBM Consulting para interactuar con modelos de IA generativa por chat; **chat unificado** que permite usar varios **modelos LLM, documentos y assistants en una misma conversación** conservando el contexto (chat con documentos, prompts y modelos). Se compone de piezas como modelo, assistant/agent, colección de documentos, system prompt, etc.
- **Everyday Assistants** — *assistant pack* con asistentes para tareas cotidianas de consultoría (generación de emails, market research, etc.).

### 2.3 Los tres estudios (foco del proyecto)

ICA incluye tres **estudios** que forman la **cadena de valor agéntica** del proyecto UFD (guías detalladas en este mismo namespace):

| Estudio | Rol en ICA | Guía |
|---|---|---|
| **Context Studio** | Gestión de **contexto empresarial**: modela/integra/gestiona/entrega el contexto (schemas + documentos + knowledge graph) que consumen los agentes vía **MCP**. Elimina la fragmentación de contexto y reduce alucinaciones. | `TOOL_context_studio_guia_referencia.md` |
| **Process Studio** | Estudio **low-code** que convierte cómo funciona una función de negocio en un **blueprint** agéntico gobernado y artefactos listos para construir. Se **fundamenta** en Context Studio. | `TOOL_process_studio_guia_referencia.md` |
| **Agentic App Studio** (coloq. *"Agent Studio"*) | Constructor/orquestador **low-code de aplicaciones agénticas** (Builder Studio, Agentic AI Core, Control Tower). **Construye y ejecuta** los agentes. | `TOOL_agentic_app_studio_guia_referencia.md` |

> **Cadena de valor:** **Context Studio** *fundamenta* → **Process Studio** *diseña* el blueprint → **Agentic App Studio** *construye/ejecuta* los agentes. (Adicionalmente, **Product Studio** —antes *Product Workbench*— orquesta el ciclo de vida de producto end-to-end por encima de los agentes de codificación; ver `TOOL_product_studio_guia_referencia.md`.)

---

## 3. Personas y gobierno

- **Personas clave de ICA:** **Demonstrator**, **Consumer** y **Contributor** (roles de uso/creación sobre la plataforma). Los estudios añaden personas específicas (p. ej. Knowledge Engineers, Architects, Developers, Product Managers y agentes de IA en Context Studio).
- **Gobierno por diseño:** identidad y acceso (SSO, RBAC, IAM), explicabilidad/auditabilidad, guardrails y validación de salida, privacidad del dato (linaje, masking, soberanía regional) y evaluación continua — capacidades que se materializan sobre todo en el **Control Tower** de Agentic App Studio y en los mecanismos MCP/seguridad transversales.

---

## 4. Cómo encaja ICA en el proyecto UFD

En el enfoque vigente (CRM a medida sobre la base fundacional **Wattyo**), ICA es el **entorno de herramientas y método** con el que el equipo:
1. **Estructura el contexto de UFD** en **Context Studio** (este propio feed es el material que lo alimentaría) y lo expone a agentes vía MCP.
2. **Reimagina/diseña** los procesos como blueprints agénticos en **Process Studio**.
3. **Construye y orquesta** los agentes que operan los *journeys* (SAC, SDC, PDS, comunicaciones) en **Agentic App Studio**, con gobierno y observabilidad (Control Tower) y despliegue **nativo en la nube del cliente (AWS)**, coherente con Wattyo.

> ICA es el **cómo se construye** (herramientas + método + IA); **Wattyo** es la **base fundacional del producto CRM**; y los **BBPs/`ns_bbps`** aportan el **qué hace** el negocio. El diseño Salesforce (`_deprecated_ns_dis_to_be_salesforce`) queda como referencia histórica no vigente.

---

## Referencias

- **Documento fuente:** *IBM Consulting Advantage — Mastermap* (ica-mastermap, 13/08/2026), secciones de plataforma, assets, assistants & agents y estudios. Clasificación original: *IBM Internal*. Contenido reformulado para el feed.
- **Guías detalladas (este namespace):** `TOOL_context_studio_guia_referencia.md`, `TOOL_process_studio_guia_referencia.md`, `TOOL_agentic_app_studio_guia_referencia.md`, `TOOL_product_studio_guia_referencia.md`.
- **Estándares/tecnologías citados:** watsonx, MCP (Model Context Protocol), A2A, IBM Consulting Assistants.
