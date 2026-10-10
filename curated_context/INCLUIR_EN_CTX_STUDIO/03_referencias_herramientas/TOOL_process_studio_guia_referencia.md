---
id: "tool-process-studio-guia-referencia"
title: "Process Studio — Guía de referencia (producto, artefactos, ventajas)"
category: tooling
document_type: "reference"
source_path: "context_base/ns_development_tools/process_studio_guia_referencia.md"
source_identifier: "process_studio_guia_referencia"
authority: reference
curation_status: needs_review
version: "1.0"
curated_on: "2026-10-10"
usage: "grounding"
---

> **Estado de curación:** Este contenido conserva el texto de origen y ha sido normalizado para su ingestión. Contiene afirmaciones o campos pendientes de validación; no debe usarse como fuente definitiva sin contrastar la procedencia indicada.

# Process Studio — Guía de referencia

> Guía de referencia extensa del producto **Process Studio** de IBM Consulting: qué es, qué hace, qué productos (artefactos) genera, sus ventajas, su flujo de trabajo y su arquitectura técnica. Elaborada a partir del *Process Studio User Guide* (v1.5 · Beta) del portal EA Learning de IBM Consulting (DTT · AI Integration Services).
>
> **Posición dentro de IBM Consulting Advantage (ICA):** Process Studio es uno de los estudios de **ICA**, junto con **Context Studio** (contexto empresarial) y **Agentic App Studio** (constructor/orquestador de aplicaciones agénticas, a veces referido como "Agent Studio"). En la documentación de plataforma de ICA, Process Studio, Context Studio y Agent Studio aparecen como **herramientas lanzables** para un elemento seleccionado, y comparten el sustrato común de ICA (contexto vía **MCP**, gobierno y observabilidad). En la cadena de valor: **Context Studio fundamenta → Process Studio diseña el blueprint agéntico → Agentic App Studio construye/ejecuta** las aplicaciones. Ver `TOOL_context_studio_guia_referencia.md` y `TOOL_agentic_app_studio_guia_referencia.md`.
>
> **Nota de licencia/atribución:** el contenido resume y reformula el material original de IBM (acceso protegido por w3id). El texto ha sido reformulado y estructurado para cumplir con las restricciones de licencia; las citas de nombres de producto, funciones y marcas se conservan por fidelidad técnica. Es un documento de **contexto interno**; confirma los detalles (modelos, límites, disponibilidad de funciones opt-in) contra tu despliegue concreto.

---

## 1. Qué es Process Studio

**Process Studio** es un **estudio low-code** en el que **agentes de IA —fundamentados en un método de ingeniería probado— trabajan junto al consultor** para convertir *cómo funciona realmente* una función de negocio en un **plan implementable de transformación agéntica**.

El reparto de responsabilidades es explícito: **tú aportas el contexto y el criterio; el estudio hace el trabajo pesado** de análisis, diseño y generación de artefactos. Está pensado para **consultores y expertos de dominio**.

Su promesa combina dos mitades:
- **Conocimiento de industria** (frameworks de proceso estándar + IP de IBM integrada).
- **Realidad del cliente** (contexto específico autorizado en Context Studio).

El lema del método resume el arco de trabajo: **Ground → Assess → Re-imagine → Simulate** (Fundamentar → Evaluar → Reimaginar → Simular).

### 1.1 Vocabulario y conceptos clave

| Término | Qué significa |
|---|---|
| **Project (Proyecto)** | Unidad de trabajo. Contiene los vínculos de contexto, los frameworks de proceso en alcance, los colaboradores y todo lo generado. Cada artefacto hereda la fundamentación (*grounding*) del proyecto. |
| **Context (Contexto)** | Conocimiento específico del cliente, autorizado en **Context Studio** (ontologías + documentos curados). Un proyecto se vincula a uno o varios contextos para que los blueprints estén fundamentados en la realidad del cliente. |
| **Blueprint** | El **entregable central**: un documento compuesto que descompone un proceso en pasos atómicos, agentes, herramientas, productos de datos, puntos de control humano (*checkpoints*) y controles. |
| **Skill (Habilidad)** | Unidad empaquetada de "método como código" (*method as code*) que el agente ejecuta de forma determinista cuando es posible (p. ej. una pasada de análisis o un generador de artefactos). |
| **Artifact (Artefacto)** | Una salida generada: Markdown, Word, PowerPoint, HTML, BPMN, suites de evaluación, un Enterprise App Kit, etc. |
| **Phase (Fase)** | El método avanza en fases ordenadas: **deconstruct → design → engineer → harden → activate**. Blueprints y análisis siguen esa misma estructura de fases. |

---

## 2. El flujo de cuatro pasos

Todo encargo sigue el mismo arco:

1. **Ground (Fundamentar el proyecto)** — enmarcar el trabajo en el contexto real del cliente: sistemas, restricciones, resultados objetivo.
2. **Identify & Assess (Identificar y evaluar)** — analizar uno o cientos de procesos a la vez para encontrar dónde la IA agéntica crea valor.
3. **Re-imagine (Reimaginar)** — rediseñar el proceso como un flujo de trabajo agéntico gobernado: **el blueprint**.
4. **Simulate & Ship (Simular y entregar)** — probar el proceso objetivo y luego generar artefactos de ingeniería listos para construir.

> **Recurso de arranque:** el *Client Context & Context Studio & Process Studio Master Playbook* (.docx) es un tutorial paso a paso para crear un caso de uso o demo específico de cliente de principio a fin: generar los activos de contexto (con prompts listos), construir y validar el contexto **AS-IS** en Context Studio, ejecutar el flujo **análisis → blueprint → BPMN → business case** en Process Studio, y publicar el contexto **TO-BE** para los agentes de entrega. Incluye una tabla de troubleshooting.

**Camino más rápido a valor:** crear un proyecto → vincular un contexto → ejecutar un *SOP scan* → abrir el blueprint.

---

## 3. Primeros pasos

### 3.1 Inicio de sesión
Tres modos según cómo esté configurada la organización:
- **Email/contraseña** estándar.
- **W3ID single sign-on** (identidad IBM).
- **Tile de ICA** lanzado desde el launchpad de **IBM Consulting Advantage**.

El acceso se autoriza contra tus **roles de Advantage**. Si no puedes iniciar sesión, confirma la asignación de rol con tu administrador.

### 3.2 La interfaz, de un vistazo
- **Sidebar** — cambiar entre proyectos y chats, y abrir la configuración del proyecto.
- **Chat** — la superficie conversacional donde pides al agente generar, personalizar, editar o explicar.
- **Canvas / visores** — donde se renderizan blueprints y artefactos (Markdown, BPMN, Mermaid, HTML, código).
- **Panels** — ingesta por lotes, análisis, artefactos y (donde esté habilitada) la vista de simulación.

### 3.3 Modo oscuro
Incluye temas claro y oscuro, basados en las paletas de **IBM Carbon** (Gray 10 claro / Gray 100 oscuro). El cambio se hace desde el control de tema en la cabecera (o en preferencias de usuario) y se recuerda.

---

## 4. Paso 1 — Ground (Fundamentar el proyecto)

Es el paso **más importante**: todo lo posterior lo hereda, así que invertir aquí es lo que separa un blueprint genérico de uno que encaja con el cliente.

### 4.1 Vincular el contexto del cliente
Se vinculan uno o varios contextos de **Context Studio** al proyecto. Un contexto es el conocimiento específico del cliente (particularidades del contact-center, modelo operativo, volúmenes, procedimientos) ingerido como conocimiento curado y ligado a un esquema de negocio. En el momento de responder, el agente **recupera del contexto vinculado** para mantener su razonamiento consistente con las definiciones del cliente.

- **Cómo conecta:** Process Studio habla con Context Studio como **servicio seguro**. Se puede fijar un contexto de forma global o sobrescribirlo por proyecto. Si se mantienen varios contextos (p. ej. uno por dominio), se pueden vincular todos.

### 4.2 Elegir los insumos de fundamentación
Al configurar el proyecto también se elige de qué se nutren los agentes:
- Los **frameworks de proceso** contra los que trabajar.
- Las **colecciones de IP de IBM** a consultar (**CBM, AI Use Cases, IBV**).
- Los **tipos de artefacto** a generar.

### 4.3 Importar desde process mining
Si el cliente ya tiene modelos de proceso minados, se pueden importar desde **SAP Signavio** o **Celonis**, para fundamentar el blueprint en la realidad observada, no solo en documentos. Una vez conectado en ajustes, se navega y selecciona los procesos concretos a adjuntar.

### 4.4 Seleccionar el modelo (model-agnostic)
Process Studio es **agnóstico al modelo**. Se elige el modelo que hará el razonamiento y, donde haga falta, se puede sobrescribir el modelo por rol dentro de una ejecución (p. ej. un modelo distinto para clasificación frente a síntesis).

| Proveedor | Se ejecuta sobre |
|---|---|
| **Anthropic** | Modelos Claude nativos |
| **AWS** | Bedrock |
| **Azure** | OpenAI / AI Foundry |
| **GCP** | Vertex AI |
| **LiteLLM** | Gateway/router entre proveedores |

**Orden de precedencia del modelo:** elección explícita por ejecución **>** modelo del proyecto **>** tu default personal **>** default del sistema (admin).

### 4.5 Cómo crear un proyecto
1. Abrir el selector de proyectos en el sidebar → **New project**.
2. Nombrarlo y describir el objetivo (p. ej. *"BMW CIC · Transform contact center"*). La descripción ayuda al agente a entender la intención.
3. Fijar la fundamentación: vincular contexto, elegir frameworks y tipos de artefacto.
4. Crear el proyecto. Todo lo que se genere queda anclado a la realidad de ese cliente.

---

## 5. Proyectos, acceso y colaboración

Un **proyecto** es donde vive todo. Se crea uno por transformación.

| Nivel | Quién lo ve | Permisos |
|---|---|---|
| **Owner (Propietario)** | Tú (creador) | Control total, incluidos borrado y compartición. |
| **Project collaborator** | Personas añadidas al proyecto | Editor (lectura-escritura) o Viewer (solo lectura) en todos los chats. |
| **Chat collaborator** | Personas añadidas a una conversación | Acceso a ese chat concreto sin compartir todo el proyecto. |

El acceso se fija en **Private** o **Team** al crear el proyecto. Se cambia de proyecto con el selector; se renombra en línea desde el título; se gestionan colaboradores o se elimina desde ajustes (solo owner).

---

## 6. Método integrado — Process Frameworks

Process Studio tiene varios **frameworks de proceso estándar de industria totalmente integrados**, de modo que cada blueprint se ancla a un modelo de referencia reconocido en lugar de partir de una página en blanco. Se seleccionan al configurar el proyecto.

| Framework | Qué aporta |
|---|---|
| **APQC Process Classification Framework (PCF)** | Taxonomía transversal de procesos de negocio. Se usa para descubrir y enumerar los procesos y dominios de una función, y para estructurar blueprints de forma consistente. |
| **APQC industry frameworks** | Variantes por industria (Banca, Consumer Products, Telco, Corporate Operations…) para taxonomías de mejor ajuste. |
| **SCOR** | *Supply Chain Operations Reference* para procesos de cadena de suministro. |
| **IBM industry operations models** | Modelos operativos de referencia de IBM (p. ej. Banking Operations) que complementan las taxonomías APQC. |

**Uso de un framework:** se seleccionan los relevantes para la industria/función; el agente **mapea los procesos sobre el framework** para que la estructura del blueprint sea completa y *benchmarkable*. Donde no exista blueprint de fábrica, el agente usa el **APQC PCF** para descubrir los dominios relevantes y generarlo desde el nombre de la función.

**Por qué importa:** un framework compartido da **consistencia** entre encargos, **completitud** (no se omite nada importante) y un **vocabulario común** para hacer benchmarking.

---

## 7. IP de IBM y activos integrados

Process Studio está enriquecido con **propiedad intelectual de IBM**, integrada y consultable mientras los agentes analizan y diseñan. Se eligen las colecciones a vincular al fundamentar el proyecto; los agentes de análisis y de preguntas recuperan de ellas y **citan lo que usan** (pie de "Sources").

- **Component Business Model (CBM):** descompone la empresa en un mapa de componentes de negocio discretos (qué hace un negocio, independiente de su estructura organizativa) para localizar brechas de capacidad y dirigir la transformación donde crea más valor. Da a la evaluación de oportunidades un marco riguroso basado en capacidades.
- **IBM AI Use Case Library:** catálogo curado de casos de uso de IA y agénticos, integrado como fuente de recuperación; siembra la identificación de oportunidades y permite *benchmarking* contra patrones probados.
- **IBV benchmark data:** datos de investigación y benchmark del **IBM Institute for Business Value**, para fundamentar el *business case* en evidencia externa (payback, coste, rendimiento contra benchmarks reales).

> Estos son **activos de IBM**: úsalos según tus términos de uso de IBM y mantenlos separados de cualquier material confidencial del cliente según las reglas de manejo de datos.

---

## 8. Paso 2 — Assess (Evaluar)

### 8.1 Ingesta de documentos — el SOP Scan
Donde el chat genera **un blueprint a la vez**, la **ingesta por lotes** maneja *corpora* enteros: importar muchos documentos, parsearlos (incluidas imágenes) y analizarlos a escala. Se conduce como un **SOP scan** con tres pestañas: **Configure → Ingest → Analyze**.

**Configure:**
- **Basics:** nombre y descripción corta ("¿quién usará la salida?").
- **Source & Sink:**
  - *Source*: subida desde tu equipo, o lectura desde un conector (almacenamiento de ficheros S3/Azure Blob, SharePoint, Box, GitHub). Los conectores se configuran en **Settings → Connections**.
  - *Sink*: **Managed** (recomendado) almacena la salida cifrada en reposo y descargable como `.zip`. La salida es un **wiki** — Markdown limpio e interconectado, una página por documento de origen.
- **Límites de subida:** hasta **10 MB por fichero**, **500 MB total**, **2000 ficheros** por subida.
- **Document types:** opcionalmente sugerir al clasificador los tipos esperados (SOP, Policy, Form…) para mejorar la categorización.
- **Limits:** *Parallelism* 1–32 documentos concurrentes (4 es un default seguro); *Max tokens per run* (tope de seguridad opcional).
- **Phases** (cada una es un *toggle* independiente):

| Fase | Qué hace |
|---|---|
| **Full document text on wiki** | Guarda el Markdown convertido completo (primeros ~32k caracteres en la página wiki). |
| **Incremental runs** | Salta ficheros que no han cambiado desde la última ejecución. |
| **Vision** | Describe imágenes embebidas en PDF/PPTX con un modelo de visión (selector de calidad de imagen; High ≈ 216 DPI). |
| **Synthesizer** | Produce un resumen cruzado que hila el corpus (`wiki/synthesis.md`). |
| **Contradictions** | Extrae *claims* estructurados y marca conflictos en el corpus (`wiki/_contradictions.md`). |

- **Auto-analysis:** opcionalmente ejecutar una skill de análisis por lotes automáticamente tras cada ingesta exitosa.

**Ingest (ejecutar):** asignar el modelo adecuado a cada trabajo (clasificación, lectura de páginas, alineación de figuras y resolución de contradicciones pueden usar cada una un modelo ajustado). Se hace un *dry-run* primero para validar la fuente, luego se ejecuta con el log en vivo y estado por fichero, e inspección de lo extraído.

### 8.2 Análisis — Procedure Eater
Una vez ingeridos los documentos, se ejecuta un análisis **sobre todos a la vez** para aflorar las oportunidades de automatización que comparte todo el portafolio. El análisis cruzado lo impulsa **Procedure Eater**.

- **Procedure Eater está totalmente integrado:** no es una herramienta aparte, sino un **conjunto de skills** dentro de Process Studio. Se elige el análisis en la pestaña **Analyze** y Procedure Eater corre sobre el wiki ingerido, aplicando el **método EAEF** para puntuar y agrupar oportunidades.
- Las ejecuciones completadas aparecen en **Analysis history** con estado, número de páginas y enlaces para abrir el **Portfolio Dashboard** o descargar todo en `.zip`.
- **Por qué por lotes:** analizar cada procedimiento junto es lo que permite a Procedure Eater encontrar **clusters de capacidad compartida** — la base para una jugada de **plataforma consolidada** en lugar de muchas automatizaciones sueltas.

---

## 9. Paso 3 — Re-imagine (Trabajar con Blueprints)

El **blueprint** es el corazón de Process Studio: un rediseño gobernado del proceso como flujo de trabajo agéntico. Se puede generar uno nuevo, partir de una plantilla o refinarlo conversacionalmente.

### 9.1 El explorador de blueprints
Los blueprints se renderizan en un explorador interactivo con un resumen de ejecución y el árbol de secciones canónico. Un blueprint es **un único documento compuesto de siete secciones**:

| Sección | Propósito |
|---|---|
| **Atomic Thinking Step Register** | El proceso descompuesto en los pasos de razonamiento/decisión más pequeños. |
| **Human-in-the-Loop Specification** | Dónde un humano revisa, valida o anula, y bajo qué reglas. |
| **Agent Definition Register** | Los agentes, sus objetivos, pasos que poseen, orquestación, herramientas y zona de autonomía. |
| **Data Products** | Los datos que cada paso consume y produce. |
| **Integration with Systems of Record** | Cómo el flujo toca SAP, ServiceNow, Workday, etc. |
| **MCP Tool Register** | Las herramientas/acciones que los agentes pueden invocar. |
| **Skills Library** | Las skills reutilizables de las que se nutre el flujo. |

### 9.2 Human-in-the-Loop (HITL)
La sección HITL se renderiza como una tabla por cada *checkpoint*: rol revisor, alcance de validación, autoridad de decisión, SLA de revisión y reglas de anulación. Es donde se **ajusta cuánta autonomía** concede el diseño y dónde un humano debe mantener el control.

### 9.3 Edición, anotaciones y publicación
- Se pueden **editar secciones en línea** y dejar feedback (pulgar arriba/abajo + comentario) por sección. Las ediciones se guardan por usuario y se fusionan en el blueprint servido.
- **JSON es la fuente de verdad.** El blueprint se entrega como **HTML** (referencia interactiva), **Markdown** y **JSON**. Se edita el JSON y se re-renderiza; **no** editar el HTML a mano (se sobrescribe en la siguiente generación).
- **Publicación:** los blueprints generados se pueden publicar (p. ej. de vuelta a una ubicación de GitHub bajo tu prefijo) para convertirse en referencia compartible.

---

## 10. Paso 4 — Ship (Generar artefactos)

Desde un blueprint, Process Studio genera un abanico de **artefactos listos para ejecución**. Se eligen al configurar el proyecto (*Auto-Generate Artifacts*) y se generan más bajo demanda desde el chat.

### 10.1 Tipos de artefacto

| Artefacto | Formato | Para qué sirve |
|---|---|---|
| **Blueprint document** | Word (.docx), HTML interactivo, Markdown, JSON | El diseño agéntico completo del proceso. JSON es la fuente de verdad; HTML es la referencia interactiva. |
| **Executive deck** | PowerPoint (.pptx) | Resumen de la transformación listo para liderazgo. |
| **Process diagrams** | BPMN 2.0 (XML), Mermaid | Modelos visuales de proceso/flujo para tooling y revisión. |
| **Agent Evaluation Plan** | Markdown | Cómo se probarán los agentes y qué significa "bueno". |
| **Evaluation suites** | Markdown, CSV | Casos de evaluación funcionales, de comportamiento y adversariales. |
| **Risk Register** | Documento estructurado | Riesgos, mitigaciones y controles del proceso objetivo. |
| **Portfolio Dashboard** | HTML interactivo | Portafolio de oportunidades, economía, clusters y roadmap. |
| **Target Operating Model** | HTML interactivo / documento | Roles, FTEs objetivo, RACI y vista *today-vs-tomorrow*. |
| **Enterprise App Kit** | Carpeta `ent-app-kit/` | El paquete de *handoff* de construcción para un coding agent. |
| **Audit trace** | JSON | Registro trazable de cómo se produjeron los artefactos. |

> **Fíjalo una vez:** al elegir los tipos de artefacto en el setup del proyecto, cada generación queda consistente — el blueprint, su deck, sus diagramas y su suite de evals cuentan la misma historia.

Los artefactos se abren en el visor adecuado (Markdown, BPMN, Mermaid, HTML, código fuente) en línea; se exportan individualmente o todo en `.zip`.

### 10.2 Enterprise App Kit (el paquete de entrega)
El **Enterprise App Kit** es un paquete de entrega listo para usar. Process Studio empaqueta el blueprint aprobado, decisiones, reglas, contratos, definiciones de agentes y herramientas, y la estructura de la aplicación objetivo en una carpeta `ent-app-kit/` que le dice a un **coding agent** compatible qué construir y qué preservar.

- Prepara además las **skills de agente** y el **método de entrega** para ejecutar el trabajo con enfoques *spec-driven* como **Spec Kit** u **OpenSpec**.
- Reduce la pérdida en el *handoff*, mantiene la entrega trazable al blueprint y hace visibles —antes de terminar— los flujos omitidos, reglas debilitadas, integraciones saltadas y atajos "solo para demo".

**Por qué no basta con pasar los documentos a un agente:** el App Kit prepara la carpeta del proyecto, instala las instrucciones de agente correctas, aplica un método de entrega estructurado, preserva el contexto para sesiones posteriores y **verifica el resultado contra el blueprint** — más consistencia, trazabilidad y control que un prompt suelto con documentos adjuntos.

**Cómo usarlo (flujo local):**
1. **Exportar el App Kit** desde Process Studio (pedirlo desde el blueprint, p. ej. *"Generate the Enterprise App Kit for this blueprint"*) y descomprimir el bundle.
2. **Crear la carpeta principal** del proyecto y mover dentro la carpeta `ent-app-kit/` (que debe quedar directamente en la raíz).
3. **Configurar la carpeta:** terminal en la raíz y ejecutar `npx --yes --package ./ent-app-kit ent-app-kit setup`. El setup pregunta por el coding agent, el método de entrega y el modo de trabajo; prepara la estructura, instala las instrucciones y valida la carpeta.
4. **Arrancar el coding agent** en la misma carpeta y darle: *"Build the app kit."*

**Qué hace el coding agent:** lee el App Kit, crea una base de conocimiento reutilizable a partir del material fuente, parte el trabajo en *delivery slices* y los implementa mediante el método elegido. Las skills empaquetadas mantienen al agente alineado con expectativas de frontend, backend, integración, auditoría, accesibilidad, arranque y verificación.

**Con Product Studio:** en lugar del flujo local, se puede crear un proyecto de **Product Studio** y usar su workflow integrado. Para ello se suben todos los ficheros de `ent-app-kit/payload/` a Context Studio, y se selecciona ese contexto en el nuevo proyecto de Product Studio (le da blueprint, decisiones, reglas, integraciones, inputs de diseño y alcance).

### 10.3 Simulate (Digital Twin) — solo Alpha
La vista **Simulate** permite **probar un proceso objetivo antes de escribir código**. Compila un blueprint en una **especificación ejecutable**, construye agentes ligados a versiones *mock* de las herramientas reales y ejecuta escenarios para ver moverse los KPIs y detectar problemas de diseño de forma barata.

- **Disponibilidad:** capacidad *opt-in*; puede no estar habilitada en todos los despliegues (consultar al administrador).
- **Flujo:** fijar baseline y objetivos → compilar el blueprint en spec ejecutable (agentes, reglas, pasos atómicos, KPIs) → crear un escenario (editar variantes, distribución de datos semilla, ajustar HITL) → lanzar la ejecución (los agentes llaman herramientas *mock*, nada toca producción) → observar la consola de ejecución en streaming → comparar resultados contra el baseline e inspeccionar el snapshot de KPIs.
- Es, en espíritu, un **test harness para todo el proceso** (como hacer *unit-testing* de un agente, aplicado al diseño end-to-end).

---

## 11. Revisar salidas (vistas de liderazgo)

### 11.1 Portfolio Dashboard
La vista de scan completo, organizada en pestañas: **Overview · Portfolio · Priorities · Heatmap · Clusters · Dep Graph · Economics · Roadmap · Conflicts · Risks · Recommendation**.

Puntúa cada caso de uso (**go / conditional / no-go**), estima ahorros y payback, agrupa clusters de capacidad compartida y **recomienda un camino** — típicamente un híbrido de plataforma consolidada para las categorías top más trabajo por-proceso para la *long tail*. Es donde se prioriza **con evidencia, no con intuición**.

### 11.2 Target Operating Model (TOM)
Aplica una **taxonomía de roles** a los pasos atómicos del blueprint (quién hace qué en el estado futuro). Define clases de rol humanas y de agente de IA, con *headcount* objetivo por rol, RACI y un roadmap de transición. La vista **"Today vs Tomorrow"** muestra departamentos legados funcionalmente especializados colapsando en dominios transversales donde una **capa humana fina** trabaja junto a un **núcleo agéntico**.

**Taxonomía de roles:**

| Clase de rol | Tipo | Rol en el estado futuro |
|---|---|---|
| **Chief Accountability Officer** | Humano | Posee los resultados y la rendición de cuentas del proceso. |
| **System Architect** | Humano | Diseña y mantiene el sistema agéntico. |
| **Relationship Expert** | Humano | Posee las interacciones humanas de alto valor y las escalaciones. |
| **Validator** | Humano | Aseguramiento de calidad por calibración sobre la salida del agente. |
| **Reasoning Agent** | IA | Planifica, decide y razona dentro de su zona de autonomía. |
| **Action Agent** | IA | Ejecuta llamadas a herramientas y acciones de sistema. |

---

## 12. Chat e iteración

El chat es la vía principal para dirigir al agente. Lee cada mensaje, deduce la intención y enruta al comportamiento adecuado (generar blueprint, personalizar, editar sección, responder, ejecutar skill o producir un documento).

**Para buenos resultados:**
- Ser específico sobre función y dominio ("Procurement — supplier onboarding").
- Pedir **una cosa a la vez** al editar; el agente propone ediciones y documentos y los aplica tras confirmar.
- Referenciar el contexto de cliente vinculado; el agente recuperará y citará fuentes.

**Plan-then-confirm:** para ediciones y documentos nuevos, el agente muestra primero un plan corto (qué secciones tocará, o un esquema) y **espera tu visto bueno** antes de aplicar. Se puede interrumpir un flujo en curso para preguntar y luego retoma donde iba.

**Conciencia del tamaño de contexto:** las conversaciones largas se resumen en segundo plano para no perder el hilo. Si un chat se ha alargado mucho, iniciar uno nuevo en el mismo proyecto conserva la fundamentación con una pizarra limpia.

---

## 13. Extender y configurar

- **Bring Your Own Skill (BYOS) — solo Alpha:** además de las skills integradas, puedes registrar las tuyas —skills empaquetadas y *sandboxed* que codifican procedimientos propietarios del cliente o un análisis a medida. Se añade una fuente de skill, se sincroniza, se escanea por seguridad y se habilita en el proyecto.
- **Connectors & settings** (los configura el administrador una vez y quedan disponibles sin redespliegue): **Context Studio** (conocimiento del cliente), **Search** (web/documentos para el agente de preguntas), **SAP Signavio** (import de process-mining), **Research collections** (IBV y la use-case library), **Cloud accounts & storage**. Todas las credenciales se cifran en reposo y se enmascaran en la interfaz.
- **User preferences:** defaults personales (modelo preferido, tema claro/oscuro), salvo que un proyecto o ejecución los sobrescriba.

---

## 14. Arquitectura técnica

Process Studio es una aplicación **cloud-native**: cuatro servicios pequeños más una librería compartida, desplegados en infraestructura de hyperscaler con **GitOps**, y conectados a proveedores de LLM *pluggable*.

### 14.1 Los servicios

| Servicio | Stack | Responsabilidad |
|---|---|---|
| **Frontend** | Next.js (TypeScript) | La SPA: chat, visores, panels, ajustes. |
| **API** | FastAPI (Python) | Backbone REST: auth, proyectos, chats, subidas, blueprints, ajustes, integraciones. |
| **Streamer** | FastAPI + SSE | Acepta envíos de chat y transmite eventos de ejecución al navegador vía Server-Sent Events. |
| **Worker** | Python · LangGraph | El motor agéntico: enrutamiento de intención, generación de blueprints, skills, análisis. Pool escalado horizontalmente que consume una cola de tareas en Redis. |
| **Shared library** | Python | La superficie de contrato que consume cada backend: configuración, abstracciones de BD y almacenamiento, auth, *run store*. |

**Recorrido de un mensaje de chat:** navegador → *streamer* (se crea la ejecución y se encola en Redis) → un *worker* la recoge y conduce el agente → los eventos vuelven vía Redis pub/sub al navegador como SSE. Si el navegador se desconecta, el stream **se reanuda desde el último evento recibido** (sin pérdida de salida).

### 14.2 Diseño del agente
El worker ejecuta el agente como una **máquina de estados LangGraph (~16 nodos)**. Cada mensaje pasa primero por un **router** (clasificador de intención):

| Intención | Qué hace el agente |
|---|---|
| **Generate** | Crea un blueprint desde cero, estructurado contra los frameworks seleccionados. |
| **Customize** | Adapta un blueprint existente o plantilla al contexto del cliente. |
| **Edit** | Cambios dirigidos al blueprint actual — *plan-then-confirm* antes de aplicar. |
| **Question** | Q&A fundamentado — recupera de contextos vinculados, IP de IBM y search, y cita fuentes. |
| **Clarify** | Pide la información que falta cuando la petición es ambigua, en vez de adivinar. |
| **Skill execution** | Ejecuta una skill empaquetada (integrada o BYOS) como sesión multi-turno. |
| **Artifact generation** | Produce documentos independientes: decks, BPMN, suites de eval y el Enterprise App Kit. |
| **Chitchat** | Conversación simple, sin invocar los flujos pesados. |

**Cómo se usan las skills:** son el "método como código" del estudio — paquetes markdown versionados, sincronizados desde un repo de GitHub gobernado (**SHA-delta sync**, sin redespliegue). Cuando el router selecciona una skill, su contenido se convierte en las instrucciones de trabajo del agente para la sesión, ejecutadas con un *tool belt* controlado (recuperación de contexto de cliente, IP de IBM, Signavio, web/MCP search). Las sesiones de skill llevan estado entre turnos. Las BYOS se **escanean por seguridad** antes de habilitarse.

**Dos principios de diseño:** el método se codifica en skills para que el comportamiento sea **determinista donde es posible** (el LLM razona; la skill dicta pasos y contratos de salida), y **todo flujo transmite su progreso** (marcadores de paso, texto generado y ficheros llegan como eventos). La salida pasa por los *guardrails* antes de llegar al usuario.

### 14.3 Almacenes con estado

| Almacén | Tecnología | Guarda |
|---|---|---|
| **Database** | PostgreSQL (managed) | Proyectos, chats, usuarios, sesiones, ajustes, configuraciones de LLM, anotaciones. |
| **Cache & queue** | Redis (p. ej. MemoryDB) | Cola de tareas del worker, buffer/pub-sub de eventos SSE, checkpoints de estado del agente. |
| **Object storage** | S3 · Azure Blob · S3-compatible (p. ej. IBM COS) | Documentos subidos, artefactos generados, exports. |

### 14.4 Despliegue cloud (topología de referencia AWS)
- **Edge:** CloudFront con WAF; `/api/*` por API Gateway, páginas por un ALB hacia la VPC.
- **Network:** servicios en subredes privadas en dos zonas de disponibilidad; capa de datos alcanzable solo por VPC endpoints y private links.
- **Runtime:** Kubernetes; el pool de workers escala independiente de los servicios de cara al usuario.
- **Platform services:** Secrets Manager (API keys, tokens, credenciales), ECR (imágenes), CloudWatch (logs/métricas/alarmas), IAM, EFS.
- **DevOps:** GitHub Actions (CI), Argo CD (GitOps), Terraform (IaC).
- **Portable por diseño:** la topología AWS es la **referencia, no un requisito**; BD, object storage y acceso a LLM están tras abstracciones de proveedor, de modo que los mismos contenedores se despliegan contra otro hyperscaler en un engagement **Enterprise Advantage**.

### 14.5 Identidad y conectividad LLM
- **Sign-in:** los tres modos (estándar, W3ID, ICA launchpad); acceso autorizado contra roles de IBM Consulting Advantage; un IdP de cliente puede federarse en un despliegue Enterprise Advantage.
- **Modelos:** agnóstico por construcción: AWS Bedrock, Azure OpenAI, GCP Vertex AI, Anthropic directo, o un gateway LiteLLM (ICA). Credenciales cifradas en reposo y enmascaradas.

### 14.6 Guardrails y protección de datos
La salida del agente **nunca va directa al usuario**: cada ejecución pasa por un motor de *guardrails* (sobre **NeMo Guardrails** + rails a medida) en el worker:

| Rail | Dirección | Qué hace |
|---|---|---|
| **Self-check** | Entrada + salida | Chequeo de seguridad por LLM sobre el mensaje del usuario y el contenido generado, con timeouts estrictos. |
| **Scope enforcement** | Salida | Mantiene las respuestas dentro del dominio de consultoría del estudio; bloquea temas fuera de alcance. |
| **Sensitive-data & PI detection** | Salida | Escanea contenido generado en busca de información personal (emails, IDs nacionales, tarjetas) y secretos técnicos (API keys, credenciales cloud, claves privadas, URLs internas) y redacta coincidencias. |

- **Gobernanza de trazas PI/SPI — *fail-closed*:** cada traza de IA se clasifica por contenido PI/SPI y cada *span* exportado se sanea de forma determinista (permitido, redactado, bloqueado, o reducido a solo metadatos). Si el clasificador no está disponible, el sistema **falla en cerrado** (spans solo-metadatos, nunca contenido crudo). Se activa por entorno vía configuración de despliegue.

### 14.7 Observabilidad y evals
- **Tracing:** cada ejecución emite trazas **OpenTelemetry** que preservan la jerarquía del grafo del agente, exportadas a **Arize** (latencia, tokens, coste, comportamiento de llamadas a modelo). Sujetas a la gobernanza PI/SPI.
- **Design-time evals (en el blueprint):** cada blueprint incluye sus propios activos de evaluación — el **Agent Evaluation Plan** más suites generadas (funcionales, de comportamiento, adversariales).
- **Runtime evals — ICA Evals:** las ejecuciones completadas se puntúan además con el servicio **ICA Evals** de IBM Consulting Advantage, a dos niveles (por respuesta y por conversación), contra el catálogo de métricas habilitado (~27 métricas: task completion, groundedness & factuality, reasoning, coherence y chequeos de seguridad como alignment y data leakage), cada una 0–100. Los resultados se adjuntan a la traza.

### 14.8 Integraciones externas
**SAP Signavio** y **Celonis** (import de process-mining), **Context Studio** sobre **MCP** (conocimiento de cliente) y **GitHub Enterprise** (sync de skills y publicación de blueprints), sobre HTTPS desde la capa de aplicación.

---

## 15. Referencia — datos, residencia y gobernanza

### 15.1 Residencia y confidencialidad de datos
**Regla de datos confidenciales de cliente:** no introducir datos confidenciales del cliente en una instancia **global compartida** de Process Studio (solo datos sintéticos o públicos). Una instancia **regional** puede contenerlos solo con aprobación explícita del cliente y todos los requisitos de seguridad cumplidos. En un despliegue **Enterprise Advantage** (el estudio corre en la cloud del propio cliente) siempre está permitido.

| Despliegue | Corre sobre | ¿Datos confidenciales de cliente? |
|---|---|---|
| **Instancia IBM compartida (global)** | Cloud IBM, compartida | **No permitido** — solo sintético/público |
| **Instancia IBM regional** | Cloud IBM, alcance regional | **Permitido** — con aprobación explícita del cliente y requisitos de seguridad cumplidos |
| **Despliegue Enterprise Advantage** | Cloud propia del cliente | **Permitido** |

### 15.2 La "Golden Rule" de gobernanza
El detalle sintético o ilustrativo es excelente para fundamentar el razonamiento y el diseño, **pero nunca debe presentarse como los números, nombres, proveedores o KPIs reales del cliente**. La investigación pública es *ground truth* de lo que existe externamente; el resto es un modelo de trabajo a validar con el cliente. **Trata toda cifra generada como ilustrativa hasta verificarla.**

---

## 16. Glosario

| Término | Definición |
|---|---|
| **Atomic step** | La unidad más pequeña de razonamiento o decisión en un proceso. |
| **Autonomy zone** | Cuánto puede hacer un agente sin visto bueno humano (p. ej. verde/ámbar). |
| **Blueprint** | El diseño compuesto y gobernado del flujo de trabajo agéntico. |
| **CBM** | *Component Business Model* — descomposición de la empresa por capacidades, de IBM. |
| **Context** | Conocimiento curado y específico del cliente autorizado en Context Studio. |
| **HITL** | *Human-in-the-loop* — checkpoint donde una persona revisa o anula. |
| **IBV** | *IBM Institute for Business Value* — datos de investigación y benchmark. |
| **Enterprise App Kit** | El paquete de *handoff* que lleva el material fuente del blueprint, la estructura de la app objetivo, instrucciones de setup, skills de agente y métodos de entrega a la implementación. |
| **MCP tool** | Herramienta/acción que un agente puede invocar para interactuar con un sistema. |
| **PCF** | *APQC Process Classification Framework* — taxonomía transversal de procesos. |
| **Procedure Eater** | El motor de análisis cross-procedure, integrado en Process Studio como skills. |
| **Skill** | "Método como código" empaquetado que el agente ejecuta de forma determinista donde es posible. |

---

## 17. FAQ y troubleshooting

- **¿Puedo usar datos reales del cliente?** En instancia global compartida, **no** (solo sintético/público). En instancia regional, **sí** con aprobación explícita y requisitos de seguridad cumplidos. En Enterprise Advantage (cloud del cliente), **sí**.
- **No puedo iniciar sesión:** el acceso se autoriza contra tus roles de IBM Consulting Advantage; confirma la asignación con un administrador.
- **Mis ediciones del blueprint desaparecieron:** recuerda que **JSON es la fuente de verdad**; no editar el HTML a mano (se sobrescribe al re-renderizar).
- **El agente no usa el conocimiento del cliente:** verifica que el **contexto de Context Studio** esté vinculado al proyecto.
- **Falta la vista Simulate:** es *opt-in*; puede no estar habilitada — consulta al administrador.
- **Una skill de análisis no aparece en Analyze:** solo aparecen las skills diseñadas para correr sobre un corpus completo.
- **Mi subida fue rechazada:** revisa los límites (10 MB/fichero, 500 MB total, 2000 ficheros; extensiones/tamaño admitidos).

---

## 18. Síntesis — ventajas clave del producto

- **Método probado + IA:** agentes fundamentados en un método de ingeniería, no en una página en blanco; el consultor aporta contexto y criterio.
- **Fundamentación en la realidad del cliente:** contextos de Context Studio + import de process-mining (Signavio/Celonis) para diseñar sobre lo observado, no solo lo documentado.
- **Anclaje a estándares:** APQC PCF, frameworks de industria APQC, SCOR y modelos operativos de IBM → consistencia, completitud y vocabulario común para benchmarking.
- **IP de IBM integrada y citada:** CBM (capacidades), AI Use Case Library (patrones) e IBV (benchmarks) para *business cases* con evidencia.
- **Escala:** de un blueprint conversacional a *corpora* enteros con el SOP scan + Procedure Eater, hallando clusters de capacidad compartida (plataforma consolidada vs. automatizaciones sueltas).
- **Entregables listos para construir:** blueprint gobernado (7 secciones), decks, BPMN/Mermaid, evals, Risk Register, Portfolio Dashboard, TOM, **Enterprise App Kit** y audit trace.
- **Del diseño a la construcción con trazabilidad:** el App Kit + Spec Kit/OpenSpec reducen la pérdida en el *handoff* y verifican el resultado contra el blueprint.
- **Prueba antes de codificar:** Simulate (digital twin) para mover KPIs contra herramientas *mock* sin tocar producción.
- **Gobierno y seguridad:** model-agnostic, guardrails (NeMo + custom), gobernanza PI/SPI *fail-closed*, observabilidad OpenTelemetry/Arize y ICA Evals; portabilidad multi-cloud y opción Enterprise Advantage para datos confidenciales.

---

## Referencias

- **Documento fuente:** *Process Studio User Guide* (v1.5 · Beta), EA Learning Portal — `pages.github.ibm.com/Consulting-DTT-AI-Integration-Services/ea-learning/docs/process-studio-use-guide.html` (acceso protegido por w3id). Contenido reformulado para cumplimiento de licencia.
- **Recursos citados en la guía:** *Client Context & Context Studio & Process Studio Master Playbook* (.docx); *Architecture & Detailed Design deck* (.pptx).
- **Productos relacionados:** Context Studio (conocimiento de cliente), Product Studio (build desde el Enterprise App Kit), IBM Consulting Advantage / ICA Evals.
