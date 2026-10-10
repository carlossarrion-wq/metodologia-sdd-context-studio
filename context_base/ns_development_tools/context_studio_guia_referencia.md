---
id: context_studio_guia_referencia
namespace: ns_development_tools
title: "Context Studio (IBM ICA) — Guía de referencia e implementación"
type: methodology
tags: [ContextStudio, IBM, ICA, IBMConsultingAdvantage, ontologia, JSON-LD, KnowledgeGraph, MCP, schema, Bob, AgenticAppStudio, semantic-search, enterprise-knowledge, guia-referencia]
source_documents:
  - "IBM ICA Context Studio — Complete Documentation and Implementation Guide (Vinod Kumar Munagala, IBM Consulting, 39 pp.)"
  - "IBM Consulting Advantage — Mastermap (ica-mastermap 13/08/2026): secciones Context Studio y Agentic App Studio"
related_docs: [process_studio_guia_referencia, agentic_app_studio_guia_referencia, met_11]
status: available
priority: tier1_critical
created: "2026-09-02"
version: "1.1"
---

# Context Studio (IBM ICA) — Guía de referencia e implementación

> Guía de referencia extensa de **IBM ICA Context Studio**: qué es, qué resuelve, su posición dentro de **IBM Consulting Advantage (ICA)**, sus conceptos y arquitectura, qué se puede hacer con él, la guía de configuración paso a paso, la exposición como servidor **MCP**, la integración con **IBM Bob**, buenas prácticas y resolución de problemas.
>
> **Fuentes:** (1) *IBM ICA Context Studio — Complete Documentation and Implementation Guide* (Vinod Kumar Munagala, IBM Consulting; 39 pp.); (2) *IBM Consulting Advantage — Mastermap* (documentación oficial de la plataforma ICA, 13/08/2026), secciones de Context Studio. Clasificación original: *IBM Internal*. Contenido reformulado y estructurado para el feed; se conservan los nombres de producto, campos y componentes por fidelidad técnica.
>
> **Complementa:** `process_studio_guia_referencia.md`, `agentic_app_studio_guia_referencia.md` (los tres productos —Context Studio, Process Studio y Agentic App Studio— se enmarcan en **ICA**) y `../ns_methodology/met_11_playbook_context_process_studio.md` (el método de trabajo AS-IS/TO-BE).

---

## 1. Qué es Context Studio

**Context Studio** es la **plataforma de modelado de conocimiento empresarial** de IBM ICA que transforma información de negocio dispersa en **inteligencia empresarial estructurada y consumible por IA**.

### 1.1 El problema que resuelve

| Sin Context Studio | Con Context Studio |
|---|---|
| Respuestas de IA genéricas | Respuestas empresariales fundamentadas (*grounded*) |
| Sin contexto de negocio | Contexto semántico de negocio rico |
| Respuestas alucinadas | Respuestas ancladas en hechos |
| Conocimiento desconectado | Grafo de conocimiento conectado |
| Comportamiento de chatbot genérico | Comportamiento de IA consciente de la empresa |

Sin Context Studio la IA solo conoce el saber general de internet, no entiende el negocio, no razona sobre los datos de la empresa y da respuestas genéricas y poco fiables. Con Context Studio la IA entiende **tus** entidades de negocio, conoce **tus** relaciones y flujos, razona sobre **tus** documentos y da respuestas fundamentadas y específicas.

### 1.2 Definición oficial (ICA) y posición dentro de IBM Consulting Advantage

Según la documentación oficial de la plataforma **IBM Consulting Advantage (ICA)**, Context Studio es una **solución de gestión de contexto empresarial** que permite **modelar, integrar, gestionar y entregar** datos contextuales para la **planificación y ejecución de aplicaciones agénticas**. Define **schemas estructurados** que representan conocimiento de industria, dominio y organización; sobre ellos alinea e integra datos de múltiples fuentes creando una representación **basada en grafo** que garantiza **acceso determinista** a información relevante y precisa. En tiempo de ejecución, entrega el contexto a los agentes durante sus fases de planificación y ejecución, exponiéndolo mediante un conjunto estandarizado de **herramientas MCP**.

Su propósito primario es **eliminar la fragmentación de contexto** y dar a los agentes información estructurada y relevante para mejorar el razonamiento y la toma de decisiones. Está construido para entornos empresariales, con énfasis en **seguridad, escalabilidad y cumplimiento**; disponible en **IBM Cloud** y con soporte **multi-cloud y on-premises**. Al centralizar y operacionalizar el conocimiento contextual, **mejora la precisión de la IA, reduce las alucinaciones** y refuerza la alineación entre objetivos de negocio y ejecución técnica.

**Posición dentro de ICA.** Context Studio es uno de los componentes de **IBM Consulting Advantage**, la plataforma de entrega potenciada por IA de IBM Consulting. Dentro de ICA convive y se integra con:
- **Agentic App Studio** (constructor low-code de aplicaciones agénticas; ver `agentic_app_studio_guia_referencia.md`) — sus agentes **consumen** el contexto de Context Studio vía MCP.
- **Process Studio** (diseño de la transformación agéntica de procesos; ver `process_studio_guia_referencia.md`) — se **fundamenta** en los contextos de Context Studio.
- **Assets de ICA** (Assistants, Agents, Agentic Applications) y modelos (optimizados para **watsonx**, con soporte de terceros).

Context Studio está **desplegado en las instancias global y regionales** de ICA y ha alcanzado **disponibilidad general (GA)**. Soporta la colaboración entre Knowledge Engineers, Developers, Architects, Product Managers y agentes de IA sobre una **base contextual compartida**.

> **Nota de nomenclatura:** la guía práctica de instalación (sección 4 en adelante) usa el término **"IBM ICA Context Studio"** y ejemplos con **IBM Bob** como IDE/agente. En la documentación de plataforma, el motor de construcción de agentes de ICA se denomina **"Agentic App Studio"** (a veces referido coloquialmente como "Agent Studio").

### 1.2 Concepto más importante
Context Studio **no es una herramienta de base de datos**. Es una **plataforma de modelado de conocimiento empresarial** que ayuda a la IA a entender el **significado de negocio, las relaciones y el contexto empresarial**.

---

## 2. Conceptos clave y arquitectura

### 2.1 Los cinco pilares
- **ONTOLOGY SCHEMA** — define entidades de negocio y relaciones.
- **CONTEXT WORKSPACE** — organiza el conocimiento empresarial por dominio.
- **KNOWLEDGE GRAPH** — conecta entidades mediante relaciones semánticas.
- **DOCUMENT STORE** — almacena documentos empresariales para recuperación por IA.
- **MCP EXPOSURE** — expone el conocimiento a través de la capa de protocolo de IA.

### 2.2 Terminología
- **Ontology (Ontología):** representación estructurada del conocimiento de negocio; define qué objetos existen (**Entities**), cómo se relacionan (**Relationships**), qué reglas los gobiernan (**Constraints/Invariants**) y qué acciones/flujos existen (**Operations**).
- **Schema:** el plano formal de la ontología expresado en **JSON-LD** que Context Studio importa y entiende.
- **Context (Contexto):** espacio de trabajo de conocimiento de negocio que contiene el schema publicado, los documentos empresariales subidos, el grafo de conocimiento generado y las reglas/guías de negocio.
- **Knowledge Graph:** red conectada de entidades y relaciones que la IA puede recorrer y sobre la que puede razonar.
- **MCP (Model Context Protocol):** el protocolo que expone el conocimiento de Context Studio a agentes de IA como Bob, Claude y otras herramientas compatibles con MCP.

### 2.3 Flujo de arquitectura global

```
Documentos empresariales / APIs / Políticas / Docs de arquitectura
        │
        ▼
   CONTEXT STUDIO
   - Schema / Ontología
   - Knowledge Graph
   - Document Store
        │  Expose via MCP
        ▼
   MCP SERVER (Enterprise Gateway)
        │
   ┌────────────┼────────────┐
   ▼            ▼            ▼
  BOB       AI Agents     Workflows
(IBM Assist) (ICA Studio) (Orquestación)
   └────────────┼────────────┘
                ▼
   Respuestas de IA conscientes de la empresa
```

### 2.4 "Database Thinking" vs "ICA Thinking"

| Concepto | Database Thinking | ICA Thinking |
|---|---|---|
| Propósito | Almacenar registros | Modelar conocimiento de negocio |
| Estructura | Tablas y columnas | Entidades y relaciones |
| Conexiones | Claves foráneas | Relaciones semánticas |
| Consultas | SQL | Inteligencia de conocimiento |
| Salida | Datos en bruto | Comprensión por IA |
| Valor | Recuperación de datos | Razonamiento empresarial |

### 2.5 Arquitectura por capas (definición oficial ICA)

La documentación de ICA describe Context Studio como un **sistema en capas** que habilita el flujo del dato contextual desde el origen hasta el consumo, articulado en torno a tres componentes:
- **Knowledge Base Manager** — representación estructurada/esquemática del conocimiento; los schemas definen entregables, establecen las fronteras de extracción de datos entre fuentes y actúan como mapas de navegación para recuperar el contexto relevante.
- **Data Ingestion** — define la frontera del contexto y extrae datos de múltiples fuentes (documentos y artefactos); los ingiere y **almacena en grafo y en vectores** para recuperación y procesado eficientes.
- **Context Broker** — recupera el contexto relevante según las peticiones de agentes y aplicaciones agénticas, y lo **expone mediante herramientas MCP**.

| Capa | Descripción |
|---|---|
| **Ingestion** | Conecta con fuentes de datos diversas y extrae conocimiento para su procesado. |
| **Processing** | Genera entidades, relaciones y *vector embeddings* para dar estructura y significado. |
| **Storage** | Almacena en **bases de datos de grafo, de vectores y repositorios de metadatos**. |
| **Service** | *Context Broker*: enruta consultas y agrega datos de múltiples fuentes para entregar contexto unificado. |
| **API** | Acceso estandarizado mediante **servidor MCP y APIs REST**. |
| **Presentation** | Interfaz web para gestionar, explorar e interactuar con el contexto. |

**Stack tecnológico (oficial ICA):** Backend **Python FastAPI**; Frontend **React/Next.js**; Graph DB **PostgreSQL + AGE**; Vector DB **PostgreSQL + PgVector**; Pipelines **Prefect**; extracción documental **Docling** (extrae metadatos y entidades automáticamente).

### 2.6 Conceptos núcleo (ampliado, ICA)

- **Context (Contexto):** colección estructurada de conocimiento centrada en un dominio/proyecto/tema; reúne **Entities** (componentes, features, requisitos), **Concepts** (ideas, principios, patrones de diseño), **Relationships** (dependencias, implementaciones, asociaciones) y **Metadata** (fuente, versión, ownership).
- **Knowledge graph:** red de nodos y aristas que permite visualizar conexiones, navegar información relacionada, descubrir patrones ocultos y dar comprensión semántica profunda.
- **Vector embeddings:** representaciones numéricas del texto que capturan significado; habilitan búsqueda por **similitud semántica** (más allá de keywords), comprensión de lenguaje natural, ranking por relevancia contextual y soporte multi-idioma.
- **Model Context Protocol (MCP):** marco estandarizado para entregar contexto a los modelos con interfaz consistente entre herramientas, integración con IDEs, compatibilidad con agentes/automatización e interacciones de datos fiables y *type-safe*. Es el puente entre el contexto estructurado y los sistemas inteligentes.

### 2.7 Capacidades clave (según ICA)

- **Schema management** — schemas de dominio/industria/metodología/proceso, agrupables en (a) schemas para planificación y ejecución agéntica y (b) schemas para gestionar/streamlinear la ingesta de datos.
- **Multi-source data ingestion** — ingesta y unificación desde GitHub, JIRA, SharePoint y cargas manuales; con **Docling** se extraen metadatos y entidades automáticamente.
- **Knowledge graph management** — construcción y visualización del grafo con exploración interactiva (traversal, pattern matching).
- **Intelligent context retrieval** — consulta por grafo, búsqueda vectorial o híbrida; resultados adaptados a la **persona y rol** del agente (relevancia, menos ruido).
- **MCP server integration** — integración con IDEs y agentes vía MCP (entrega estructurada del contexto en el momento preciso).
- **Chat with context** — interfaz de chat con IA para **previsualizar/evaluar** cómo se recupera el contexto (el consumo principal, no obstante, es vía aplicaciones agénticas).

> **Personas ICA:** Knowledge Engineers, Developers, Architects, Product Managers y agentes de IA, trabajando sobre una base contextual compartida.

---

## 3. Qué se puede hacer con Context Studio

### 3.1 Capacidades núcleo
- **Modelado de ontología y schema:** crear schemas JSON-LD de entidades de negocio; definir atributos, relaciones, constraints y operations; importar schemas de fichero o generarlos con Bob; publicarlos para consumo por IA.
- **Gestión de documentos empresariales:** subir PDF, Markdown, Word; almacenar políticas, docs de arquitectura, runbooks; búsqueda semántica; fundamentar respuestas en documentos reales.
- **Visualización del knowledge graph:** ver relaciones de entidades, entender cómo se relacionan, consultar con asistencia de IA, navegar la ontología.
- **Creación de contexto de negocio:** crear workspaces de conocimiento con nombre, descripción y schema vinculado; definir reglas de negocio y guías de uso; organizar por dominio o proyecto.
- **Exposición como servidor MCP:** exponer cualquier contexto como endpoint compatible con MCP; generar **MCP Gateway Tokens** seguros; habilitar cualquier herramienta de IA compatible con MCP; crear APIs de conocimiento reutilizables.
- **Búsqueda y recuperación semántica:** búsqueda vectorial sobre documentos; recuperación contextual que reduce alucinaciones; respuestas específicas de la empresa.

### 3.2 Casos de uso por industria

| Industria | Caso de uso |
|---|---|
| Ingeniería de software | Ontología de requisitos, mapeo de dependencias, conocimiento de arquitectura |
| RR.HH. y plantilla | Políticas de empleados, gestión de permisos, ontologías de talento |
| Finanzas | Políticas financieras, reglas de cumplimiento, ontologías de riesgo |
| Sanidad | Flujos de paciente, conocimiento clínico, ontologías de tratamiento |
| Cadena de suministro | Entidades de aprovisionamiento, relaciones con proveedores, ontologías logísticas |
| Ciberseguridad | Modelos de amenaza, marcos de cumplimiento, políticas de seguridad |
| Modernización de aplicaciones | Inventario de apps, dependencias de migración, planes de modernización |

### 3.3 Qué habilita para la IA
Sin Context Studio, "¿Qué es una solicitud de permiso?" da una respuesta genérica de LLM. Con Context Studio, "Muéstrame todas las solicitudes de permiso PENDING de Ingeniería" da una respuesta empresarial fundamentada; "¿Qué reglas de negocio aplican a la aprobación por el Manager de una baja por enfermedad?" da la respuesta exacta desde tu ontología.

---

## 4. Guía de configuración paso a paso

### Fase 1 — Creación de schema y ontología
1. **Crear la carpeta del proyecto** local y abrirla en VS Code con Bob activado (`mkdir my-enterprise-context`).
2. **Generar el schema JSON-LD con Bob** mediante un prompt del dominio (ejemplos: Software Requirements, HR Leave Management, Application Modernization), pidiendo entidades con atributos, relaciones, constraints y operations en formato ICA Context Studio.
3. **Revisar y validar** el JSON-LD generado. Elementos requeridos en cada **Entity**: `id` (`namespace:EntityName`), `type: "Entity"`, `name`, `description`, `identityKey` (p. ej. `uniqueFieldName: UUID`), `humanRef`, `attributes` e `invariant` (reglas de negocio). En cada **Operation**: `id`, `type: "Operation"`, `name`, `description`, `from`, `to`, `precondition`, `postcondition`. Requiere un bloque **`@context`** con los namespaces (`domain`, `schema`) y los mapeos de `relatesTo`, `from`, `to`, `Entity`, `Operation`, etc.
4. **Guardar** el fichero con extensión `.jsonld` (p. ej. `employee-leave-schema.jsonld`).
5. **Validar antes de importar** pidiendo a Bob que compruebe: `@context` válido, `@id`/`@type` correctos, sin IDs duplicados, sin referencias circulares, `relatesTo` válidos, referencias `from`/`to` correctas y campos requeridos.

**Problemas comunes de validación:** falta de `@context` (import falla), IDs duplicados (error de import), referencias circulares (falla el grafo), referencias de Operation rotas, falta de `identityKey`, estructura `@graph` inválida (envolver todas las entidades en un array `@graph`).

### Fase 2 — Acceso a Context Studio
6. **Login en la plataforma ICA** (usar siempre la **ICA Global Instance**, no instancias regionales/locales, para envíos Bob-a-thon).
7. **Abrir/crear el equipo ICA** con permisos **Editor** o **Admin** y con Context Studio habilitado.
   - **Permisos:** *Viewer* (solo ver schemas/contextos), *Editor* (crear y editar), *Admin* (acceso total, incluida exposición MCP y generación de tokens).
8. **Navegar a Context Studio** desde el dashboard del equipo (menú lateral) y verificar las secciones **Schemas** y **Contexts**. Si no es visible: confirmar que el equipo lo tiene habilitado, contactar al administrador ICA o abrir una solicitud de acceso.

### Fase 3 — Importar el schema
9. **New Schema → Import Schema** en la sección Schemas.
10. **Subir el `.jsonld`**, poner un **Schema Name** con formato `[Domain] [Purpose] Schema`, elegir la categoría de dominio, añadir descripción y **Save/Import**.
11. **Verificar el import**: la sección **Entities** muestra todas las entidades con atributos e identity keys; **Relationships** muestra las conexiones `relatesTo` con origen/destino; **Operations** muestra las operaciones con `from`/`to`, pre y post condiciones; **Constraints** muestra los `invariant`.
12. **Revisar y publicar** (checklist de entidades, relaciones, operaciones y constraints) → **Publish**. Publicar habilita: disponibilidad para crear contextos, consulta vía MCP, referencia de entidades por agentes y generación del knowledge graph.

### Fase 4 — Crear el contexto de negocio
13. **Contexts → New Context.**
14. **Configurar el contexto:** **Context Name** descriptivo; **Description** (qué dominio cubre, qué preguntas responde, qué documentos contiene, quién lo usa); **Linked Schema** (el schema publicado); **Business Rules** (reglas que la IA debe seguir); **Usage Guidelines** (cómo deben usarlo los agentes). **Save**.
15. **Subir documentos empresariales** (pestaña **Source and Data → Upload Documents/Add Source**). Tipos recomendados: políticas (PDF/MD), docs de arquitectura, flujos de proceso, documentación de API, runbooks, planes de migración, especificaciones de requisitos, documentación de ontología, glosario de negocio, marcos de cumplimiento. Se pueden **generar con Bob** en Markdown y subirlos.
    - *Por qué importan:* el schema solo da estructura/relaciones/reglas básicas; **schema + documentos** aporta conocimiento de dominio profundo, políticas, detalles de proceso, casos límite y recuperación semántica rica.
16. **Esperar el procesamiento** de cada documento hasta estado **Ready** (no exponer MCP hasta que todos estén Ready). Estados: Uploading, Processing, **Ready**, Failed, Pending. Si se atasca: re-subir, convertir PDF→Markdown, partir documentos grandes, verificar formato, comprobar que no esté protegido/corrupto.
17. **Revisar el Knowledge Graph** (pestaña): verificar nodos de entidad y aristas de relación; consultar con asistencia de IA (p. ej. *"Show me all relationships between Employee and LeaveRequest"*).
18. **Validar la calidad de recuperación** con preguntas de dominio, comprobando que las respuestas se fundamentan en los documentos subidos y que las relaciones se entienden correctamente.

---

## 5. Exponer Context Studio como servidor MCP

### Fase 5 — Configuración de la exposición MCP
19. **Overview → MCP Exposure / Expose as MCP.**
20. **Habilitar** el toggle de exposición MCP y esperar a que se genere el endpoint (URL única del contexto).
21. **Guardar de forma segura** tres valores críticos:
    - **`CONTEXT_STUDIO_MCP_URL`** — endpoint al que se conectan los agentes (`https://[ica-instance]/mcp/[context-id]/...`).
    - **`CONTEXT_ID`** — identificador único del contexto (`ctx_[alfanumérico]`).
    - **`MCP_GATEWAY_TOKEN`** — token *bearer* de autenticación (credencial sensible; **nunca** commitear a GitHub ni compartir).
    - Almacenamiento: `.env` local (dev; añadir a `.gitignore`), variables de entorno (deploy) o gestor de secretos (prod: OpenShift Secrets, AWS Secrets Manager, Azure Key Vault, HashiCorp Vault).
22. **Verificar el endpoint** con cURL (`GET` con `Authorization: Bearer …`). Esperado: **HTTP 200** con JSON de tools. Si **302** (redirección: revisar formato de URL/ruta/token), **401** (auth fallida: verificar/regenerar token), **404** (endpoint no encontrado: verificar URL, exposición habilitada y context ID).
23. **Herramientas MCP disponibles:**

| Tool | Propósito | Cuándo la usa la IA |
|---|---|---|
| `vector_query` | Búsqueda semántica sobre documentos | Al responder preguntas de dominio |
| `entity_search` | Buscar entidades concretas | Al buscar objetos específicos |
| `relationship_query` | Consultar relaciones entre entidades | Al explorar conexiones |
| `schema_introspect` | Entender la estructura de la ontología | Al entender el modelo de dominio |
| `document_retrieve` | Recuperar documentos concretos | Al necesitar el contenido completo |
| `context_summary` | Obtener un resumen del contexto | Al orientarse en un dominio nuevo |

### Fase 6 — Registro del servidor MCP en Agentic App Studio
24. **Agentic App Studio → MCP Servers → Access MCP Gateway / Add MCP Server.**
25. **Registrar** con: **Server Name** (p. ej. `Employee-Leave-Context-MCP`), **MCP URL** (`CONTEXT_STUDIO_MCP_URL`), **Authentication Type = Bearer Token**, **Authentication Value = `MCP_GATEWAY_TOKEN`**, **Transport Type = Streamable HTTP**, tags y descripción. **Save/Register**.
26. **Verificar el estado** del servidor: Active (listo), Connecting (esperar 30-60 s), Inactive (revisar URL/token), Error (verificar credenciales), Timeout (revisar red/URL).
27. **Configurar el Virtual MCP Server** (capa de abstracción que expone los tools a los agentes): en la sección Virtual Server, editar el Default Virtual Server, habilitar los tools de Context Studio y guardar. Ventajas: punto único de conexión, gestión centralizada de varios MCP, mejor control de seguridad y reutilización entre agentes.
28. **Verificar que los tools son visibles** (`context-studio/vector_query`, `context-studio/entity_search`, `context-studio/relationship_query`, `context-studio/schema_introspect`, `context-studio/document_retrieve`, `context-studio/context_summary`).

---

## 6. Integración con IBM Bob

### Fase 7 — Configuración MCP de Bob
29. **Concepto:** Bob soporta MCP nativamente. Conectado a Context Studio, pasa de asistente de código general a **asistente consciente de la empresa** que conoce tus entidades, relaciones de ontología, documentos y reglas de negocio.
30. **Crear el fichero de configuración** en `.bob/mcp.json` (crear la carpeta `.bob` si no existe).
31. **Configurar la conexión** (una o varias contextos):
    ```json
    {
      "mcpServers": {
        "context-studio": {
          "type": "streamable-http",
          "url": "YOUR_CONTEXT_STUDIO_MCP_URL",
          "headers": { "Authorization": "Bearer YOUR_MCP_GATEWAY_TOKEN" },
          "disabled": false
        }
      }
    }
    ```
    Campos requeridos: `type`, `url`, `headers`, `Authorization`, `disabled`.
32. **Asegurar credenciales:** referencias a variables de entorno (`${CONTEXT_STUDIO_MCP_URL}`, `${MCP_GATEWAY_TOKEN}`), añadir `.bob/mcp.json` y `.env` a `.gitignore`, o publicar un `mcp.json.example` con placeholders.
33. **Reiniciar Bob** (cerrar y reabrir en VS Code) para que cargue la configuración; los servidores MCP conectan en el arranque.
34. **Verificar la conexión** pidiendo a Bob que liste los servidores MCP o los tools de `context-studio`.
35. **Probar la integración** con consultas que **incluyan el context id** (recuperación básica, pregunta de dominio, relación, recuperación de documento, introspección de schema). Con MCP conectado, Bob llama `vector_query`/`entity_search`, recupera del contexto y fundamenta la respuesta.
36. **Generación de código empresarial con contexto:** REST API (Spring Boot) desde la ontología, UI React desde las entidades, DDL PostgreSQL desde entidades/relaciones y casos de test (JUnit) desde las reglas de negocio.

### Fase 8 — Integración avanzada
37. **Inicializar Bob con conciencia de contexto:** `/init` (genera `AGENTS.md`), indicar el context id y la instrucción de recuperar de MCP antes de responder o generar código, y cambiar Bob a **Advanced mode** (habilita el uso de tools MCP).
38. **Crear `AGENTS.md`** con instrucciones de MCP (servidor, context id, "recuperar de MCP antes de responder", guías de arquitectura/stack, reglas de negocio del contexto).
39. **Workflow de desarrollo dirigido por contexto:** A) entender el dominio; B) planificar arquitectura; C) generar código (entidades → servicio → API → frontend → tests); D) validar e iterar contra la ontología.
40. **Conectar Bob a la Workflow API de Agentic App Studio:** obtener la URL de la workflow (sustituir `agentstudio.servicesessentials.ibm.com` por `langflow.servicesessentials.ibm.com`), generar una **API Key** (guardar como `ICA_API_KEY`), probar la API con Bob y generar la integración backend (p. ej. FastAPI con `POST /api/chat`, auth bearer, reintentos y manejo de errores).

---

## 7. Buenas prácticas

### 7.1 Diseño de schema
- **Schemas modulares:** uno por dominio (HR, Engineering, Cloud), no un schema gigante mezclado.
- **Nombres claros y consistentes:** entidades en `PascalCase`, atributos en `camelCase`, operaciones `VerbNoun`, prefijo de namespace corto y consistente (`leave:`, `req:`, `app:`).
- **Identity keys claras:** UUID + campo humano legible (`identityKey: "employeeId: UUID"`, `humanRef: "employeeName"`).
- **Reglas de negocio como `invariant`** (guían el razonamiento, generan validaciones, fundamentan respuestas y mejoran la recuperación).
- **Operations con pre/post condiciones claras** (cuándo se disparan, qué cambian, qué validar).

### 7.2 Subida de documentos
- Estructurar en documentos de política, de proceso y de referencia; usar Markdown estructurado con headings claros.
- Recomendaciones: Markdown mejor que PDF; < 50 páginas por documento; headings claros; evitar PDFs escaneados y contenido duplicado; incluir ejemplos; actualizar al cambiar políticas; terminología consistente.

### 7.3 Configuración de contexto
- **Un contexto por dominio** (mejora la precisión de recuperación).
- **Descripciones detalladas** del contexto (qué contiene, qué responde, cómo usarlo).
- **Reglas de negocio significativas** en el contexto (guían respuestas conformes a política).

### 7.4 Integración MCP
- **Especificar siempre el context id** en las consultas (sin él, el agente puede consultar el contexto equivocado o caer en conocimiento general).
- **Rotar tokens** regularmente (cada 30 días), actualizar `.env`/`mcp.json`, no compartir por Slack/email, no commitear, usar gestor de secretos en producción.
- **Probar la conexión tras cada rotación** de token (actualizar, reiniciar Bob, verificar tools y respuesta fundamentada).

### 7.5 Integración con Bob
- **Usar siempre Advanced Mode** para MCP (Ask/Plan mode no soportan el tool calling completo).
- **Incluir el context id en cada consulta de dominio.**
- **Validación continua:** pedir a Bob que verifique que el código implementa las reglas de la ontología (bucle continuo código ↔ ontología ↔ reglas).

---

## 8. Problemas comunes y soluciones

### 8.1 Import de schema
- **Import falla:** JSON-LD inválido (validar/arreglar con Bob), falta `@context` (primer elemento con namespaces), falta `@graph` (envolver entidades), IDs duplicados (únicos con prefijo), `relatesTo` circular (unidireccional), referencias de Operation rotas (`from`/`to` deben coincidir con IDs).
- **Publica pero sin entidades:** entidades no envueltas en `@graph`; `type` incorrecto (usar exactamente `"Entity"`, es *case sensitive*); faltan campos requeridos; **mismatch de namespace** (el prefijo en `@context` debe coincidir con el usado en los IDs).

### 8.2 Creación de contexto
- **Documentos atascados en Processing:** PDF corrupto/protegido (convertir a Markdown), fichero demasiado grande (partir; máx. ~10 MB / ~50 páginas), caracteres no soportados (UTF-8), retraso de plataforma (esperar 15-20 min), PDF escaneado sin capa de texto (OCR/convertir).
- **Recuperación vacía:** documentos no Ready; context id equivocado (copiar exacto del Overview); consulta demasiado específica (empezar amplia); schema no vinculado (re-vincular/republicar); exposición MCP no habilitada.

### 8.3 Conexión MCP
- **Servidor Inactive/Error:** URL con formato incorrecto (usar la exacta del Overview, sin barras/parámetros extra); token expirado/inválido (regenerar, sin espacios, actualizar `mcp.json`, reiniciar Bob); tipo de auth equivocado (**Bearer Token**); transport equivocado (**Streamable HTTP**); red/firewall (permitir HTTPS saliente, VPN, probar con cURL).
- **Bob no ve los tools:** no está en Advanced Mode; `mcp.json` en ubicación incorrecta (debe ser `.bob/mcp.json`); errores de sintaxis JSON; Bob no reiniciado por completo; `disabled` no está en `false`; Virtual MCP Server sin configurar.
- **HTTP 302:** base URL incorrecta / falta de ruta final / HTTP en vez de HTTPS.
- **HTTP 401:** token con espacios, expirado, token equivocado (usar el **MCP Gateway Token**, no el ICA API token) o revocado (regenerar).

### 8.4 Integración con Bob
- **Bob responde con conocimiento general:** falta el context id en la consulta; no está en Advanced Mode; servidor MCP no conectado; datos estáticos embebidos en el prompt del agente; `AGENTS.md` sin instrucciones de MCP.
- **Consultas MCP con timeout:** demasiados documentos grandes (partir/limpiar); latencia de red/VPN; plataforma ICA saturada (reintentar); consulta demasiado compleja (dividir en preguntas simples).
- **Código generado que ignora reglas de negocio:** Bob no recupera de Context Studio antes de codificar (instruir recuperación primero); demasiadas instrucciones a la vez (dividir en pasos: ontología → modelos → validaciones → servicio → API); `AGENTS.md` sin referencia a Context Studio.

---

## 9. Síntesis — ventajas clave

- **De IA genérica a IA consciente de la empresa:** respuestas fundamentadas en las entidades, relaciones, documentos y reglas reales del cliente, reduciendo alucinaciones.
- **Modelado semántico, no base de datos:** ontología (entidades/relaciones/constraints/operations) en JSON-LD + knowledge graph navegable.
- **Contexto por dominio, reutilizable:** workspaces con schema + documentos + reglas, organizados por dominio.
- **Exposición estándar vía MCP:** cualquier agente compatible (Bob, Claude, workflows de ICA) consume el conocimiento con seis tools estándar y un Virtual MCP Server como capa de abstracción.
- **Integración profunda con Bob:** de asistente general a generador de código empresarial *grounded* (REST API, UI, DDL, tests) con bucle de validación continua contra la ontología.
- **Gobierno y seguridad:** permisos por rol (Viewer/Editor/Admin), tokens rotables, credenciales en gestor de secretos, y buenas prácticas de datos (UTF-8, Markdown, documentos < 50 páginas).

---

## Referencias

- **Documento fuente:** *IBM ICA Context Studio — Complete Documentation and Implementation Guide* (Vinod Kumar Munagala, IBM Consulting; 39 pp.). Clasificación original: *IBM Internal — For Consulting Delivery Teams*. Contenido reformulado para el feed.
- **Guías relacionadas:** `process_studio_guia_referencia.md` (Process Studio) y `../ns_methodology/met_11_playbook_context_process_studio.md` (playbook de método AS-IS/TO-BE).
- **Productos/tecnologías citados:** IBM Consulting Advantage (ICA), Agentic App Studio, IBM Bob, MCP (Model Context Protocol), JSON-LD.
