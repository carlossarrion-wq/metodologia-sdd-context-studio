---
id: "norm-norm-ia-001"
title: "Política de uso de inteligencia artificial en el desarrollo y en las aplicaciones"
category: governance
document_type: "policy"
source_path: "Context/ns_marco_normativo/politica-uso-ia.md"
source_identifier: "NORM-IA-001"
authority: governing
curation_status: provisional
policy_status: provisional
policy_owner: "Comité de IA"
effective_date: "2026-10-10"
review_cycle: "annual"
next_review_date: "2027-10-10"
version: "1.1"
curated_on: "2026-10-10"
usage: "grounding"
---

> **Estado de gobierno:** Política operativa provisional, con vigencia desde el 2026-10-10. Propietario designado: **Comité de IA**. Requiere ratificación formal antes de tratarse como política corporativa definitiva.

# Política de uso de inteligencia artificial

## 1. Objeto y alcance

Esta política regula dos usos de la IA:

1. **IA como herramienta de desarrollo**: asistentes de codificación (Kiro, Cline), Process Studio, Context Studio y cualquier agente que participe en el proceso SDD.
2. **IA como componente de la aplicación**: casos de uso que incorporan modelos de IA generativa (chatbots, extracción de documentos, clasificación, resúmenes, agentes).

**Convención:** "DEBE" = obligatorio; "NO DEBE" = prohibido; "DEBERÍA" = recomendado. Identificadores IA-XX.

## 2. Principios

- **Responsabilidad humana**: la IA propone, una persona decide y responde del resultado.
- **Plataforma única y controlada**: el acceso a modelos se hace a través de Amazon Bedrock en la cuenta corporativa.
- **Protección de datos**: ningún dato sale del perímetro aprobado.
- **Transparencia**: las personas saben cuándo interactúan con IA o reciben contenido generado por IA.
- **Proporcionalidad**: los controles crecen con el riesgo del caso de uso.

## 3. Plataforma y regiones autorizadas

- **IA-01** Todo uso de modelos fundacionales DEBE realizarse a través de **Amazon Bedrock** en cuentas AWS corporativas.
- **IA-02** DEBEN usarse regiones de la UE o perfiles de inferencia entre regiones restringidos a la UE (p. ej., Irlanda `eu-west-1`, Fráncfort `eu-central-1`, España `eu-south-2`). [ADAPTAR a las regiones de la landing zone]
- **IA-03** NO DEBEN usarse herramientas de IA de consumo (chats públicos, extensiones no aprobadas, cuentas personales) con información Interna, Confidencial o Restringida.
- **IA-04** El registro de invocaciones de modelos (model invocation logging) DEBE configurarse según la clasificación de los datos, con acceso restringido a los logs.

## 4. Catálogo de modelos autorizados

Amazon Bedrock ofrece más de 100 modelos de una veintena de proveedores y el catálogo cambia casi cada mes. Este catálogo recoge las **familias** autorizadas; los identificadores exactos y su disponibilidad por región DEBEN consultarse en la página "Models at a glance" de la documentación de Bedrock y fijarse en el registro de modelos del proyecto.

| Familia (proveedor) | Uso recomendado | Uso en asistentes de código | Observaciones |
|---|---|---|---|
| **Anthropic Claude** – gama alta (Opus) | Diseño técnico complejo, análisis de specs, revisión de código, agentes con razonamiento largo | Sí, para planificación (modo Plan / fase design) | Mayor coste; reservar para tareas complejas |
| **Anthropic Claude** – gama media (Sonnet) | Modelo por defecto para desarrollo, generación de specs, agentes de proceso | **Sí, modelo por defecto** | Equilibrio calidad/coste |
| **Anthropic Claude** – gama ligera (Haiku) | Clasificación, extracción, enrutado, tareas de alto volumen en aplicaciones | Sí, para tareas simples | Baja latencia y coste |
| **Amazon Nova** (Micro, Lite, Pro, Premier) | Funcionalidades de aplicación de alto volumen, multimodal, resúmenes | Opcional | Modelo propio de AWS; buena relación coste |
| **OpenAI** (GPT y gpt-oss) | Alternativa para aplicaciones y agentes | [VERIFICAR aprobación] | Validar disponibilidad en regiones UE |
| **Meta Llama** / **Mistral** | Casos con requisito de modelo de pesos abiertos | No | Validar licencia del modelo |
| **Embeddings** (Amazon Titan Text Embeddings, Cohere Embed) | Bases de conocimiento RAG, búsqueda semántica (incluido Context Studio) | N/A | Usar el mismo modelo de embeddings en indexación y consulta |
| **Cohere Rerank** / **Amazon Rerank** | Mejora de relevancia en RAG | N/A | Opcional |

- **IA-05** Solo DEBEN usarse modelos del catálogo autorizado. La incorporación de un modelo nuevo requiere evaluación de [ADAPTAR – Comité de IA]: seguridad, licencia, disponibilidad en UE, coste y calidad en un conjunto de pruebas de referencia.
- **IA-06** Los modelos marcados como "legacy" o con fecha de retirada en Bedrock NO DEBEN usarse en desarrollos nuevos.
- **IA-07** El modelo y su versión DEBEN fijarse explícitamente en la configuración (no usar alias genéricos en producción) y registrarse en el diseño.
- **IA-08** El cambio de modelo en una aplicación en producción DEBE tratarse como un cambio funcional: requiere repetir las evaluaciones (ver NORM-CAL-001).

## 5. Datos permitidos por uso

| Clasificación del dato | Asistentes de código (Kiro, Cline) | Aplicaciones con IA vía Bedrock |
|---|---|---|
| Público | Permitido | Permitido |
| Interno (incluido código fuente) | Permitido con modelos autorizados vía Bedrock | Permitido |
| Confidencial | NO, salvo datos sintéticos o anonimizados | Permitido con Guardrails, logging controlado y aprobación en el diseño |
| Restringido | NO | Solo con aprobación expresa de Seguridad y DPO |

- **IA-09** Los asistentes de código DEBEN configurarse para usar Bedrock como proveedor de modelos (credenciales corporativas, no claves personales).
- **IA-10** NO DEBEN introducirse secretos ni datos personales reales en prompts, specs ni ficheros de contexto.

## 6. Uso de la IA en el proceso de desarrollo

- **IA-11** Todo artefacto generado por IA (spec, diseño, plan de tareas, código, tests) DEBE ser revisado y aprobado por una persona antes de considerarse válido. La persona que aprueba es la responsable del artefacto.
- **IA-12** Las pull requests con código generado mayoritariamente por IA DEBERÍAN etiquetarse (p. ej., `ai-assisted`) para trazabilidad y métricas.
- **IA-13** Los agentes NO DEBEN aprobar sus propias salidas: la verificación la hace una persona o un agente distinto con contexto independiente.
- **IA-14** El código generado DEBE revisarse por posibles problemas de licencia (fragmentos reproducidos de código con licencia incompatible) mediante la herramienta de SCA o de detección de similitud aprobada.
- **IA-15** El contexto corporativo usado por los agentes DEBE proceder de Context Studio o de los ficheros de reglas versionados del repositorio, no de fuentes no controladas.

## 7. AI Act (Reglamento (UE) 2024/1689)

### 7.1 Rol de la organización
- Al **usar** asistentes de código y modelos de Bedrock, la organización actúa como **responsable del despliegue** (deployer).
- Al **construir y poner en servicio** una aplicación que incorpora IA, la organización actúa como **proveedor del sistema de IA**.
- Los proveedores de los modelos (Anthropic, Amazon, OpenAI, etc.) son **proveedores de modelos de IA de uso general (GPAI)**. Sus obligaciones aplican desde el 2 de agosto de 2025 y recaen sobre ellos, salvo que la organización modifique sustancialmente un modelo (p. ej., fine-tuning de gran escala), en cuyo caso DEBE consultarse a Legal.

### 7.2 Calendario de aplicación (tras el Digital Omnibus, Reglamento (UE) 2026/1744)

| Fecha | Obligación | Estado |
|---|---|---|
| 2 feb 2025 | Prácticas prohibidas (art. 5) y alfabetización en IA (art. 4) | En vigor |
| 2 ago 2025 | Obligaciones de proveedores de modelos GPAI | En vigor |
| 2 ago 2026 | Obligaciones de transparencia (art. 50) y poderes de supervisión sobre GPAI | En vigor |
| 2 dic 2026 | Fin del periodo de gracia para el marcado de contenido generado por IA | [VERIFICAR alcance exacto con Legal] |
| 2 dic 2027 | Sistemas de alto riesgo independientes (anexo III) | Pendiente |
| 2 ago 2028 | Sistemas de alto riesgo integrados en productos regulados (anexo I) | Pendiente |

El Omnibus también añadió nuevas prácticas prohibidas (generación de imágenes íntimas realistas sin consentimiento y material de abuso sexual infantil) y modificó aspectos de la alfabetización en IA. [VERIFICAR con Legal la redacción vigente del art. 4]

### 7.3 Clasificación de riesgo obligatoria
- **IA-16** Toda spec de un caso de uso que incorpore IA DEBE incluir una **ficha de clasificación AI Act** con: finalidad del sistema, usuarios y personas afectadas, categoría de riesgo y justificación.
- **IA-17** Categorías y tratamiento:

| Categoría | Ejemplos | Tratamiento en el proceso |
|---|---|---|
| **Prohibido** | Manipulación subliminal, puntuación social, reconocimiento de emociones en el trabajo, explotación de vulnerabilidades | **No se desarrolla.** Se rechaza en la fase de intake |
| **Alto riesgo** (anexo III) | Selección de personal y evaluación de empleados, acceso a educación, evaluación de solvencia o seguros, infraestructuras críticas | **Fuera del alcance del proceso estándar.** Escalar a Comité de IA y Legal |
| **Riesgo de transparencia** (art. 50) | Chatbots, generación de texto o imagen para terceros, deepfakes | Proceso estándar + requisitos IA-18 a IA-20 |
| **Riesgo mínimo** | Clasificación interna de documentos, resúmenes para uso interno, asistentes de código | Proceso estándar + buenas prácticas |

Los casos de uso "sencillos de mejora de procesos de negocio" DEBERÍAN situarse en riesgo mínimo o de transparencia. Si un caso toca decisiones sobre personas (empleo, crédito, acceso a servicios esenciales), DEBE escalarse aunque parezca sencillo.

### 7.4 Obligaciones de transparencia (art. 50)
- **IA-18** Los sistemas que interactúan con personas (chatbots, asistentes) DEBEN informar claramente de que el usuario está interactuando con una IA, salvo que sea evidente.
- **IA-19** El contenido sintético (texto, imagen, audio, vídeo) generado para terceros DEBE marcarse de forma detectable según las guías de la Comisión y los estándares aplicables.
- **IA-20** El texto generado por IA que se publique para informar al público sobre asuntos de interés público DEBE indicar que ha sido generado por IA, salvo revisión editorial humana con responsabilidad editorial.

### 7.5 Alfabetización en IA
- **IA-21** Todo miembro de los equipos que use herramientas de IA DEBE completar la formación de [ADAPTAR – itinerario formativo] antes de usarlas, con registro de la formación.

### 7.6 Inventario de sistemas de IA
- **IA-22** Toda aplicación con IA puesta en producción DEBE registrarse en el inventario corporativo de sistemas de IA con: propietario, finalidad, modelos usados, categoría de riesgo, datos tratados y fecha de última evaluación.

## 8. Relación con el RGPD

- **IA-23** Si el sistema toma o apoya decisiones con efectos significativos sobre personas, DEBE garantizarse intervención humana real (art. 22 RGPD) y consultarse al DPO.
- **IA-24** Los tratamientos con IA de datos personales DEBEN tener base jurídica, información a los interesados y, si procede, EIPD.

## 9. Supervisión humana y control en aplicaciones con IA

- **IA-25** Las aplicaciones DEBEN permitir a un humano revisar, corregir o anular las salidas de la IA que tengan impacto en el proceso de negocio.
- **IA-26** Las aplicaciones DEBEN registrar las interacciones relevantes (sin datos sensibles en claro) para permitir auditoría.
- **IA-27** DEBE existir un mecanismo para desactivar la funcionalidad de IA sin detener el proceso de negocio (modo degradado).

## 10. Checklist de quality gate de IA

- [ ] Ficha de clasificación AI Act en la spec (IA-16)
- [ ] Caso no prohibido ni de alto riesgo, o escalado y aprobado (IA-17)
- [ ] Modelos del catálogo autorizado, versión fijada, región UE (IA-02, IA-05, IA-07)
- [ ] Datos tratados compatibles con la tabla de la sección 5
- [ ] Transparencia implementada si aplica (IA-18 a IA-20)
- [ ] Supervisión humana y modo degradado (IA-25, IA-27)
- [ ] Registrado en el inventario de sistemas de IA (IA-22)
- [ ] Equipo con formación en IA completada (IA-21)
