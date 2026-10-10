# Desarrollo de Agentes en AWS AgentCore dentro de Naturgy
## Guía de Referencia para Implementación Empresarial

**Versión:** 1.5.0  
**Fecha:** 27 de Marzo de 2026  
**Autor:** Equipo de Arquitectura  
**Última Actualización:** Incorporación de decisiones tras la reunion con AWS 2026

---

## Tabla de Contenidos

1. [Introducción y Visión](#1-introducción-y-visión)
2. [Arquitectura de Referencia](#2-arquitectura-de-referencia)
3. [Autenticación y Autorización](#3-autenticación-y-autorización)
4. [Seguridad](#4-seguridad)
5. [Observabilidad](#5-observabilidad)
6. [Servidores MCP](#6-servidores-mcp)
7. [Organización y Nomenclatura](#7-organización-y-nomenclatura)
8. [Ciclo de Vida de un Agente](#8-ciclo-de-vida-de-un-agente)
9. [Casos de Uso](#9-casos-de-uso)
10. [Evaluación de Calidad de Modelos](#10-evaluación-de-calidad-de-modelos)
11. [Backup y Alta Disponibilidad](#11-backup-y-alta-disponibilidad)
12. [Apéndices](#12-apéndices)


---

## 1. Introducción y Visión

### 1.1 Objetivo del Libro Blanco

Este documento establece los estándares, mejores prácticas y patrones arquitectónicos para el desarrollo de agentes inteligentes en la plataforma AWS AgentCore. Su propósito es:

- **Estandarizar** el desarrollo de agentes para garantizar consistencia y calidad
- **Acelerar** el time-to-market mediante patrones reutilizables
- **Asegurar** la escalabilidad, seguridad y mantenibilidad de los agentes
- **Facilitar** la colaboración entre equipos mediante convenciones comunes

### 1.2 Visión de la Plataforma

AWS AgentCore/FactorIA es la plataforma empresarial para el desarrollo, despliegue y gestión de agentes inteligentes que automatizan procesos de negocio críticos. La plataforma proporciona:

**Capacidades Clave:**
- Orquestación de agentes multi-propósito
- Integración nativa con servicios AWS
- Gestión centralizada de identidad y acceso mediante FactorIA
- Observabilidad y telemetría avanzada con HIS (Here-I-Strands)
- Seguridad por diseño (Security by Design)
- Evaluación continua de calidad

**Casos de Uso Principales:**
- Automatización de soporte técnico (Nivel 1-2)
- Asistencia en desarrollo de software
- Análisis y resolución de incidencias
- Generación de documentación técnica
- Onboarding y capacitación
- Migración y modernización de sistemas

### 1.3 Principios Rectores

#### 1.3.1 Interoperabilidad
- Estándares abiertos (OpenTelemetry, OAuth2, SAML)
- APIs RESTful bien documentadas
- Integración con sistemas legacy y modernos
- Compatibilidad multi-cloud cuando sea necesario

#### 1.3.3 Seguridad
- Zero Trust Architecture
- Cifrado end-to-end (TLS 1.3 + KMS)
- Principio de menor privilegio
- Auditoría completa de acciones

#### 1.3.4 Facilidad de Mantenimiento
- Código como infraestructura (IaC con Terraform)
- Documentación automática
- Versionado semántico
- Gestión centralizada desde FactorIA

### 1.4 Matriz de Madurez de Agentes

| Nivel | Descripción | Características | Ejemplo |
|-------|-------------|-----------------|---------|
| **Nivel 1: Básico** | Agente reactivo simple | Respuestas predefinidas, sin contexto | FAQ Bot |
| **Nivel 2: Contextual** | Mantiene contexto de conversación | Multi-turn, memoria de sesión | Chatbot de soporte |
| **Nivel 3: Proactivo** | Anticipa necesidades | Análisis predictivo, sugerencias | Asistente de código |
| **Nivel 4: Autónomo** | Ejecuta acciones sin supervisión | Toma de decisiones, workflows | Agente de resolución |
| **Nivel 5: Cognitivo** | Aprendizaje continuo | RLHF, auto-mejora | Agente adaptativo |

---

## 2. Arquitectura de Referencia

### 2.1 Visión General de la Arquitectura

```
┌─────────────────────────────────────────────────────────────────┐
│                        CAPA DE PRESENTACIÓN                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐         │
│  │ FactorIA │  │  Mobile  │  │   CLI    │  │   API    │         │
│  │   Web    │  │   App    │  │  Client  │  │  Client  │         │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘         │
└─────────────────────────────────────────────────────────────────┘
                              ▼
┌────────────────────────────────────────────────────────────────┐
│                    CAPA DE AUTENTICACIÓN                       │
│  ┌──────────────┐         ┌──────────────┐                     │
│  │  Entra ID    │◄───────►│   Cognito    │                     │
│  │  (Azure AD)  │  SAML   │  User Pool   │                     │
│  │              │         │  (FactorIA)  │                     │
│  └──────────────┘         └──────┬───────┘                     │
│                                   │                            │
│                          ┌────────▼────────┐                   │
│                          │ Lambda Pre-Token│                   │
│                          │  (Enriquecedor) │                   │
│                          └─────────────────┘                   │
└────────────────────────────────────────────────────────────────┘
                              ▼
┌────────────────────────────────────────────────────────────────┐
│                      CAPA DE API                               │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Contenedores con balanceadores, cloudfront              │  │
│  │  - Validación JWT                                        │  │
│  │  - Rate Limiting                                         │  │
│  │  - Request Validation                                    │  │
│  │  - WAF Integration (Kyndryl)                             │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
                              ▼
┌────────────────────────────────────────────────────────────────┐
│                    CAPA DE ORQUESTACIÓN                        │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │    Agent Controller (Capa de control de agentes)	      │  │
│  │  - Gestión de timeouts                                   │  │
│  │  - Rate limiting y throttling                            │  │
│  │  - Circuit breaker                                       │  │
│  │  - Gestión de concurrencia                               │  │
│  │  - Session lifecycle management                          │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
                              ▼
┌────────────────────────────────────────────────────────────────┐
│                      CAPA DE AGENTES                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              AWS Agent Core Runtime                      │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐                │  │
│  │  │ Agent 1  │  │ Agent 2  │  │ Agent N  │                │  │
│  │  │(Strands +│  │(Strands +│  │(Strands +│                │  │
│  │  │   HIS )  │  │   HIS )  │  │   HIS )  │                │  │
│  │  └──────────┘  └──────────┘  └──────────┘                │  │
│  │                                                          │  │
│  │  - Sub-agentes dentro del runtime                        │  │
│  │  - Protocolo A2A para comunicación                       │  │
│  │  - Memoria de sesiones integrada                         │  │
│  │  - Bedrock Guardrails habilitado 		                  │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
                              ▼
┌────────────────────────────────────────────────────────────────┐
│                    CAPA DE SERVIDORES MCP                      │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  MCP Servers (HTTP/REST Streamable)                      │  │
│  │  - Tool Providers                                        │  │
│  │  - Resource Providers                                    │  │
│  │  - External Integrations                                 │  │
│  │  - Autenticación con token JWT del usuario               │  │
│  │  - Tool Authorization Layer (Cyber)                      │  │
│  │  - Parameter Validation (cyber)                          │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
                              ▼
┌───────────────────────────────────────────────────────────────┐
│                      CAPA DE DATOS                            │
│  ┌──────────┐  ┌──────────┐  ┌───────────┐    ┌──────────┐    │
│  │   RDS    │  │ DynamoDB │  │    S3     │    │ Bedrock  │    │
│  │(Permisos)│  │ (Trazas) │  │(Artifacts)│    │(G)│  │
│  └──────────┘  └──────────┘  └───────────┘    └──────────┘    │
└───────────────────────────────────────────────────────────────┘
                              ▼
┌───────────────────────────────────────────────────────────────┐
│                   CAPA DE OBSERVABILIDAD                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐       │
│  │CloudWatch│  │   HIS    │  │OpenTelem.│  │ FactorIA │       │
│  │  Logs    │  │ (Trazas) │  │ Metrics  │  │Dashboard │       │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘       │
└───────────────────────────────────────────────────────────────┘
```

### 2.2 Componentes Clave

#### 2.2.1 AgentCore Orchestrator

- **NO se usarán Lambdas para la lógica de agentes**
- Los agentes se ejecutarán directamente en **AWS Agent Core** con el framework **Strands**
- La orquestación se maneja dentro del propio agente mediante sub-agentes cuando sea necesario
- **Agent Controller** conjunto de funcionalidades que gestiona aspectos transversales

**Responsabilidades del Agent Controller:**
- Gestión de timeouts de sesiones
- Rate limiting y gestión de throttling de Bedrock
- Circuit breaker para servicios externos
- Gestión de concurrencia de agentes
- Detección y manejo de errores de throttling
- Session lifecycle management 
- Kill switch global para emergencias

**Implementación:**
- **Agent Core (AWS)** como runtime principal para todos los agentes
- **Strands Framework** para desarrollo de agentes (no LangChain/LangGraph)
- **HIS (Here-I-Strands)**: Wrapper sobre Strands para trazabilidad automática
- **Agent Controller**: Componente crítico a desarrollar (puede usar Step Functions o implementación custom)
- **Step Functions**: OPCIONAL, solo para flujos muy complejos que no puedan manejarse dentro del agente
- **EventBridge**: Para casos asíncronos y arquitecturas event-driven

**Consideraciones de Diseño:**

- **Preferencia por lógica en el agente**: Los agentes deben gestionar su propio flujo cuando sea posible
- **Step Functions como excepción**: Solo usar cuando la complejidad del flujo lo justifique (mejor experiencia de usuario con trazas en FactorIA vs. consola AWS)
- **Agent-to-Agent (A2A)**: Protocolo nativo de Agent Core para comunicación entre agentes
- **Sub-agentes**: Un agente puede tener sub-agentes definidos dentro del mismo runtime
- **Timeout máximo**: 8 horas por sesión (parametrizable, recomendado 1-2 horas)
- **Rollback**: No es un requisito genérico, depende del caso de uso (cada sistema destino tiene su estrategia de compensación)


El **Agent Controller** es un componente **OBLIGATORIO** antes de pasar a producción. Debe implementar:
- Rate limiting por usuario y por agente
- Circuit breaker con estados (Closed, Open, Half-Open)
- Kill switch global para deshabilitar agentes en emergencias
- Gestión de sesiones con timeouts configurables

#### 2.2.2 Agent Runtime

**Características:**
- Ejecución serverless en AWS Agent Core
- Aislamiento por agente
- Configuración dinámica
- Memoria de sesiones integrada (funcionalidad nativa de Agent Core)
- Protocolo Agent-to-Agent (A2A) para comunicación entre agentes
- Bedrock Guardrails habilitado por defecto

- **Filtros obligatorios:**
  **PROMPT_ATTACK**: Detección de inyección de prompts
  **PII_DETECTION**: Detección y redacción de información personal
  **TOXICITY_FILTER**: Filtrado de contenido tóxico
- La configuración de Guardrails debe documentarse en el README del agente


**Recursos por Agente:**
- **Timeout de sesión**: Máximo 8 horas (parametrizable, recomendado 1-2 horas)
- **Concurrency**: Gestionada por el Agent Controller
- **Almacenamiento efímero**: **NO SE RECOMIENDA USAR** - Toda persistencia debe ser externa
- **Autoescalado**: Gestionado por Agent Core (transparencia pendiente de confirmar con AWS)
- **Alta disponibilidad**: Despliegue multi-AZ automático por Agent Core
- **Recuperación ante fallos**: Reintentos automáticos con backoff exponencial

**CRÍTICO - Gestión de Almacenamiento:**
- **No recomendable** guardar información en almacenamiento local del runtime
- **Obligatorio** usar servicios externos:
  - **S3**: Para archivos y artefactos
    * Versionado habilitado para recuperación de versiones anteriores
    * Replicación cross-region para datos críticos (opcional)
    * Lifecycle policies para archivado automático
  - **DynamoDB**: Para estado y trazas (usado por HIS automáticamente)
    * Point-in-time recovery (PITR) habilitado
    * Backups automáticos on-demand antes de cambios críticos
  - **RDS**: Para datos estructurados (solo para permisos en FactorIA)
    * Backups automáticos diarios con retención configurable (7-30 días)
    * Read replicas para distribución de carga de lectura
- **Memoria de sesiones**: Usar funcionalidad nativa de Agent Core para compartir información entre sesiones

**Framework de Desarrollo:**
- **Strands** (AWS): Framework oficial para desarrollo de agentes
- **HIS (Here-I-Strands)**: Wrapper sobre Strands para trazabilidad y observabilidad
  - Publicado en PyPI (licencia MIT)
  - Registro automático de eventos en DynamoDB:
    * Inicio de sesión
    * Event loop
    * Invocaciones a tools
    * Invocaciones a modelos
  - Integración con plataforma FactorIA para visualización
  - Stream de eventos a Lambda para procesamiento


 - **Bedrock Guardrails** es **OBLIGATORIO** para todos los agentes en producción
 - Debe configurarse con filtros de: PROMPT_ATTACK, PII_DETECTION, TOXICITY_FILTER
 

#### 2.2.3 MCP Servers

**Tipos de Servidores:**
- **Tool Providers**: Exponen herramientas ejecutables
- **Resource Providers**: Proporcionan acceso a datos y recursos
- **Prompt Providers**: Plantillas de prompts reutilizables (gestionadas en FactorIA)

**Protocolo de Comunicación**
- **HTTP/REST streamable**: Protocolo estándar para comunicación con MCP servers
- **NO se usarán**: gRPC, WebSocket, SQS (descartados por simplicidad y compatibilidad)

**Despliegue:**
- **Dentro de Agent Core**: Opción preferida cuando sea posible
- **Servicios separados**: Cuando se requiera independencia o escalado diferenciado
- **Autenticación**: Reutilización del token JWT del usuario para llamadas a MCPs
- **Limitación actual**: Un usuario puede invocar un agente pero no necesariamente todos sus MCP servers (diseño cuidadoso de scopes necesario)

**Gestión de Prompts (Centralizada en FactorIA):**
- **Versionado**: Control de versiones de prompts
- **Testing**: Playground integrado para pruebas
- **Despliegue automatizado**: Desde FactorIA a cuentas AWS
- **Visibilidad cross-cuenta**: Gestión centralizada con despliegue distribuido
- **Catálogo**: Repositorio de prompts reutilizables

### 2.3 Patrones Arquitectónicos Recomendados

#### 2.3.1 Patrón: Agent con Sub-agentes

```
Usuario → API → Agent Core Runtime
                     ↓
              Agente Principal
                     ↓
        ┌────────────┼────────────┐
        ▼            ▼            ▼
    Sub-agente   Sub-agente   Sub-agente
        A            B            C
        ↓            ↓            ↓
    MCP Server   MCP Server   MCP Server
```

**Cuándo usar:**
- Tareas que requieren múltiples capacidades especializadas
- Necesidad de paralelización dentro del mismo contexto
- Mejor experiencia de usuario (trazas en FactorIA)

**Ventajas:**
- Menor complejidad operacional
- Trazabilidad completa en una sola interfaz
- Gestión de estado simplificada

#### 2.3.2 Patrón: Agent-to-Agent (A2A)
```
Usuario → API → Agente A
                     ↓ (Protocolo A2A)
                 Agente B (compartido)
                     ↓
                 MCP Server
```

**Cuándo usar:**
- Agentes compartidos entre múltiples casos de uso
- Necesidad de reutilización de capacidades
- Separación de responsabilidades clara

#### 2.3.3 Patrón: Event-Driven Agent
```
Evento → EventBridge → Agent Controller → Agent Core → Acción
                                              ↓
                                         DynamoDB (trazas)
                                              ↓
                                    Notificación (email/webhook)
```

**Cuándo usar:**
- Procesamiento asíncrono
- Agentes batch (procesos programados)
- Reacción a eventos del sistema
- Desacoplamiento de componentes

**Nota**: Distinguir entre agentes conversacionales (expuestos a usuarios) y agentes batch (procesos programados) para rate limiting

#### 2.3.4 Patrón: Circuit Breaker (en Agent Controller)
```
Request → Agent Controller → Circuit Breaker → Bedrock/MCP
                                  ↓
                         [Closed, Open, Half-Open]
```

**Cuándo usar:**
- Protección contra fallos en cascada
- Servicios externos no confiables (Bedrock throttling)
- Mejora de resiliencia

**Estados del Circuit Breaker:**
- **Closed:** Estado normal, las peticiones fluyen normalmente
- **Open:** Servicio marcado como fallido, peticiones fallan inmediatamente
- **Half-Open:** Estado de prueba, permite peticiones limitadas para verificar recuperación

**Implementación:**
- No incluido por defecto en Agent Core
- Debe implementarse en el Agent Controller
- Definir umbrales apropiados (ej: 50% error rate → Open)
- Configurar tiempo de espera antes de pasar a Half-Open
- Implementar fallbacks o respuestas por defecto

### 2.4 Flujos de Datos

#### 2.4.1 Flujo de Solicitud Síncrona
```
1. Usuario envía solicitud → API
2. API valida JWT → Cognito User Pool (FactorIA)
3. Agent Controller gestiona invocación → Agent Core
4. Agent procesa solicitud → MCP Servers (con token JWT)
5. Agent genera respuesta → Usuario
6. Telemetría registrada → DynamoDB (vía HIS) + CloudWatch
7. Visualización → FactorIA Dashboard
```

#### 2.4.2 Flujo de Solicitud Asíncrona
```
1. Usuario envía solicitud → API
2. API publica evento → EventBridge
3. EventBridge enruta → Agent Controller
4. Agent Controller invoca → Agent Core
5. Agent procesa → MCP Servers
6. Agent publica resultado → DynamoDB
7. Usuario notificado → Email/Webhook/UI (según caso de uso)
```

#### 2.4.3 Flujo de Autenticación Completo
```
1. Usuario accede → FactorIA Web
2. Redirige a → Entra ID (SSO corporativo)
3. Usuario se autentica → Entra ID emite SAML token
4. SAML token → Cognito User Pool (FactorIA)
5. Cognito valida SAML → Lambda Pre-Token Generation
6. Lambda consulta → RDS (tabla cognito_group_scope)
7. Lambda enriquece JWT con scopes del grupo
8. JWT retornado → Usuario
9. Usuario invoca agente con JWT → API
10. Agent Core valida JWT → Cognito User Pool
11. Agente ejecuta con permisos → MCP Servers (token propagado)
```

### 2.5 Arquitectura Multi-Cuenta y Disaster Recovery

#### 2.5.1 Estrategia de Cuentas AWS

**Arquitectura Confirmada:**

```
┌──────────────────────────────────────────────────────────┐
│ Cuenta FactorIA (core-factgen) - PLATAFORMA              │
│  ┌────────────────────────────────────────────────────┐  │
│  │ • Web de gestión (frontend + backend)              │  │
│  │ • Cognito User Pool (centralizado)                 │  │
│  │ • RDS (permisos y configuración)                   │  │
│  │   - platform_user                                  │  │
│  │   - user_permissions                               │  │
│  │   - platform_role                                  │  │
│  │   - cognito_group_scope                            │  │
│  │ • Gestión de prompts (versionado, playground)      │  │
│  │ • Catálogo de agentes (imágenes Docker)            │  │
│  │ • Inference Profiles (etiquetado para costes)      │  │
│  │ • Lambda Pre-Token Generation                      │  │
│  │ • Permisos cross-account para despliegue           │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
                        ↓ (despliega agentes)
┌────────────────────────────────────────────────────────────┐
│ Cuenta Comercializadora (comer-factgen) - CASOS DE USO     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ • Agentes AgentCore (Strands + HIS)                  │  │
│  │ • MCP Servers                                        │  │
│  │ • VPC, subnets, security groups                      │  │
│  │ • Bedrock (modelos con Application Inference         │  │
│  │   Profiles)                                          │  │
│  │ • DynamoDB (trazas de HIS)                           │  │
│  │ • S3 (artefactos)                                    │  │
│  │ • CloudWatch (logs)                                  │  │
│  │ • Entornos: desarrollo + producción (separados)      │  │
│  └──────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ Cuenta Nedgia (nedgia-factgen) - CASOS DE USO Nedgia        │
│  • Misma estructura que Comercializadora                    │
│  • Agentes específicos de Nedgia                            │
│  • Separación por negocio                                   │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ Cuentas Adicionales (futuro)                                │
│  • Corporación                                              │
│  • UFD                                                      │
│  • Cuentas de caso de uso específicas (SOLO cuando hay      │
│    requisitos especiales):                                  │
│    - VPC completamente separada                             │
│    - Datos altamente sensibles                              │
│    - Requisitos de compliance específicos                   │
└─────────────────────────────────────────────────────────────┘
```

**Principios de Separación:**

1. **FactorIA (Plataforma - core-factgen)**:
   - Gestión centralizada de autenticación (Cognito User Pool único)
   - Interfaz web para gestión de agentes
   - Repositorio de prompts y configuraciones
   - Catálogo de imágenes Docker de agentes
   - Despliegue cross-account a otras cuentas
   - **NO ejecuta agentes**, solo los gestiona

2. **Comercializadora/Nedgia (Ejecución)**:
   - Ejecución de agentes y casos de uso
   - Recursos de infraestructura (VPC, compute)
   - Separación por negocio
   - Entornos desarrollo y producción separados por cuenta
   - Bedrock con modelos y Application Inference Profiles

3. **Cuentas Específicas (Excepcional)**:
   - Solo cuando hay requisitos especiales
   - VPC completamente separada
   - Datos altamente sensibles
   - Requisitos de compliance específicos

**Región Principal:**
- **Frankfurt (eu-central-1)**: Región principal
- **Cumplimiento GDPR**: Datos no salen de Europa


**Permisos Cross-Account:**
- FactorIA producción tiene permisos para desplegar en otras cuentas
- Roles IAM con políticas específicas por cuenta destino
- Auditoría completa de despliegues cross-account
- Despliegue desde web de FactorIA seleccionando cuenta destino

**Aprobaciones Pendientes:**
- Aprobación de corporación para desplegar en FactorIA producción (CRÍTICO)
- Si no se aprueba, habría que replicar FactorIA en cuenta de Comercializadora

### 2.6 Decisiones de Diseño

#### 2.6.1 Runtime: Agent Core vs. Contenedores

| Criterio | Agent Core (Serverless) | Fargate/ECS (Contenedores) |
|----------|------------------------|----------------------------|
| **Uso recomendado** | Por defecto para todos los agentes | Solo casos muy específicos |
| **Latencia** | Gestionada por AWS | Warm start  100ms |
| **Costo** | Pay-per-use | Costo fijo + variable |
| **Escalabilidad** | Automática (transparencia pendiente AWS) | Manual/Auto-scaling |
| **Complejidad** | Baja | Media-Alta |
| **Gestión** | AWS gestionado | Requiere gestión |
| **Integración** | Nativa con Bedrock, A2A, memoria | Requiere configuración |

**Decisión:** Agent Core por defecto, contenedores solo para casos que no puedan ejecutarse en Agent Core

#### 2.6.2 Stateless vs. Stateful

| Aspecto | Stateless (Recomendado) | Stateful |
|---------|------------------------|----------|
| **Escalabilidad** | Excelente | Limitada |
| **Complejidad** | Baja | Alta |
| **Persistencia** | Externa (DynamoDB, S3) | Interna (memoria) |
| **Memoria de sesiones** | Agent Core nativo | Gestión manual |
| **Recomendado para** | Mayoría de agentes | Sesiones largas específicas |

**Decisión:** Stateless con estado externalizado + memoria de sesiones nativa de Agent Core

---

## 3. Autenticación y Autorización

### 3.1 Arquitectura de Identidad Híbrida

#### 3.1.1 Integración Entra ID ↔ AWS Cognito

**Flujo de Autenticación Completo:**

```
┌──────────────┐
│   Usuario    │
│  (Usuario    │
│    900)      │
└──────┬───────┘
       │ 1. Accede a FactorIA Web
       ▼
┌──────────────────┐
│  FactorIA Web    │
└──────┬───────────┘
       │ 2. Redirige a Entra ID para SSO
       ▼
┌──────────────────┐
│    Entra ID      │
│   (Azure AD)     │
└──────┬───────────┘
       │ 3. Usuario se autentica (SSO corporativo)
       │ 4. Entra ID emite SAML token
       ▼
┌──────────────────┐
│  AWS Cognito     │
│  User Pool       │
│  (FactorIA)      │
└──────┬───────────┘
       │ 5. Cognito valida SAML
       │ 6. Trigger: Lambda Pre-Token Generation
       ▼
┌──────────────────┐
│  Lambda Function │
│  (Pre-Token Gen) │
└──────┬───────────┘
       │ 7. Consulta grupo del usuario
       ▼
┌──────────────────┐
│   RDS Database   │
│ cognito_group_   │
│     scope        │
└──────┬───────────┘
       │ 8. Retorna scopes del grupo
       ▼
┌──────────────────┐
│  Lambda Function │
│  (Pre-Token Gen) │
└──────┬───────────┘
       │ 9. Enriquece JWT con scopes
       ▼
┌──────────────────┐
│   JWT Token      │
│  (con scopes)    │
└──────┬───────────┘
       │ 10. Usuario recibe token
       ▼
┌──────────────────┐
│   API / Agente   │
└──────┬───────────┘
       │ 11. Agent Core valida JWT
       │ 12. Autoriza según scopes
       ▼
┌──────────────────┐
│  MCP Servers     │
│ (token propagado)│
└──────────────────┘
```

**Nota Crítica**: 
- Es el runtime de Agent Core el que valida y autoriza las peticiones
- El API realiza validación inicial del JWT
- La autorización basada en scopes ocurre en el código del agente
- El token JWT se propaga a las invocaciones de MCP servers

#### 3.1.2 Configuración de Cognito User Pool

**User Pool de FactorIA (Centralizado):**

**Características:**
- **Un único User Pool** para toda la plataforma
- Integrado con Entra ID como Identity Provider (SAML 2.0)
- Usuarios corporativos (usuario 900) autenticados vía SSO
- Lambda Pre-Token Generation para enriquecimiento de tokens

**Configuración:**
- User Pool ya existente en cuenta de FactorIA
- SAML 2.0 Identity Provider configurado con Entra ID
- Attribute mapping entre claims SAML y atributos Cognito
- Grupos de Cognito para gestión de permisos

**Grupos de Cognito:**
- Los usuarios se asignan a grupos
- Cada grupo tiene scopes asociados (almacenados en RDS)
- Lambda Pre-Token Generation lee scopes del grupo y los añade al JWT

### 3.2 Base de Datos de Usuarios en RDS

Event bridge eventos de cognito, para gestion de los usuarios

#### 3.2.1 Modelo de Datos

**Tablas Principales:**

```sql
-- Tabla de usuarios de la plataforma
CREATE TABLE platform.platform_user (
	id varchar(36) NOT NULL,
	full_name text NOT NULL,
	email text NOT NULL,
	company text NOT NULL,
	naturgy_user_code text NOT NULL,
	role_id varchar(36) NOT NULL,
	register_datetime timestamp NULL,
	last_login_datetime timestamp NULL,
	CONSTRAINT platform_user_pkey PRIMARY KEY (id),
	CONSTRAINT platform_user_platform_role_id_fk FOREIGN KEY (role_id) REFERENCES platform.platform_role(id)
);

-- Tabla de permisos por usuario
CREATE TABLE platform.platform_user_permission (
	id varchar(36) NOT NULL,
	user_id text NOT NULL,
	wiki_read bool DEFAULT false NULL,
	cloud_formation bool DEFAULT false NULL,
	playground bool NULL,
	inference_profile_id varchar(36) NULL,
	projects json NULL,
	use_cases json NULL,
	CONSTRAINT platform_user_permission_pkey PRIMARY KEY (id),
	CONSTRAINT platform_user_permission_user_id_fk FOREIGN KEY (user_id) REFERENCES platform.platform_user(id)
);

-- Tabla de roles predefinidos
CREATE TABLE platform.platform_role (
	id varchar(36) NOT NULL,
	platform_role text NOT NULL,
	nicename varchar(1000) NOT NULL,
	description text NOT NULL,
	CONSTRAINT platform_role_pkey PRIMARY KEY (id)
);

-- Tabla de mapeo grupo-scope (CRÍTICA)
CREATE TABLE platform.platform_role (
	id varchar(36) NOT NULL,
	platform_role text NOT NULL,
	nicename varchar(1000) NOT NULL,
	description text NOT NULL,
	CONSTRAINT platform_role_pkey PRIMARY KEY (id)
);
```

**Roles Genéricos Definidos (Pendiente de completar):**
- `admin`: Administrador completo
- `developer`: Desarrollador de agentes
- `reader`: Solo lectura
- `playground`: Acceso a playground para pruebas
- `advanced`: Usuario avanzado con permisos especiales

#### 3.2.2 Sincronización Cognito ↔ RDS

**DECISIÓN IMPORTANTE: NO HAY SINCRONIZACIÓN**

- **La interfaz web lee directamente de Cognito** (grupos, usuarios)
- **Solo se almacena en RDS** la relación grupo → scopes (tabla `cognito_group_scope`)
- **Evita redundancia** y problemas de consistencia
- **Acceso al RDS desde casos de uso**: NO necesario, toda la información viene en el token JWT

**Flujo de Gestión:**
1. Administrador gestiona grupos en Cognito (vía interfaz FactorIA)
2. Administrador define scopes para cada grupo en RDS (vía interfaz FactorIA)
3. Lambda Pre-Token Generation lee de RDS al generar token
4. JWT contiene toda la información necesaria

#### 3.2.3 Enriquecimiento de Token JWT

**Lambda Pre-Token Generation:**

**Función:**
- Trigger de Cognito que se ejecuta antes de generar el token
- Consulta la tabla `cognito_group_scope` en RDS
- Obtiene los scopes del grupo al que pertenece el usuario
- Añade los scopes al token JWT

**Código de Referencia (Pendiente de que nos envie la version definitiva Lorenzo):**
```python
# Pseudocódigo
def lambda_handler(event, context):
    user_id = event['request']['userAttributes']['sub']
    groups = event['request']['groupConfiguration']['groupsToOverride']
    
    # Consultar RDS para obtener scopes
    scopes = get_scopes_for_groups(groups)
    
    # Añadir scopes al token
    event['response']['claimsOverrideDetails'] = {
        'claimsToAddOrOverride': {
            'scope': ' '.join(scopes)
        }
    }
    
    return event
```

**Scopes en JWT:**
- Formato: Lista separada por espacios (estándar OAuth2)
- Ejemplo: `"agent:invoke agent:read project:comercializadora"`
- Validados por Agent Core automáticamente

#### 3.2.4 Validación de Token JWT

**Validación Automática por Agent Core:**
- Agent Core valida el token contra el Cognito User Pool configurado
- Verifica firma, expiración, issuer
- Extrae scopes del token
- **Out-of-the-box**, no requiere implementación adicional

**Autorización en el Agente:**
- El código del agente verifica los scopes necesarios
- Ejemplo: `if 'agent:invoke' in token_scopes:`
- Permite autorización granular por funcionalidad

### 3.3 Modelo de Autorización

#### 3.3.1 Autorización Basada en Scopes

**Por qué Scopes:**
- Es la opción por defecto en Cognito
- Más simple de implementar que claims adicionales
- Compatible con estándares OAuth2
- Integración nativa con Agent Core

**Estructura de Scopes:**
```
{recurso}:{acción}:{contexto}

Ejemplos:
- agent:invoke                    # Invocar cualquier agente
- agent:invoke:lcs-com-gen-001   # Invocar agente específico
- agent:read                      # Leer información de agentes
- agent:create                    # Crear nuevos agentes
- agent:delete                    # Eliminar agentes
- project:comercializadora        # Acceso a proyectos de comercializadora
- mcp:invoke:sap-server          # Invocar MCP server específico
```

**Limitación Actual:**
- Un usuario puede invocar un agente pero no necesariamente todos sus MCP servers
- Requiere diseño cuidadoso de scopes
- Consulta con AWS para mejoras futuras

#### 3.3.2 Roles y Permisos (Pendiente de definir completamente)

**Roles Propuestos:**

**agent_admin - Administrador de Agentes:**
- Permisos completos sobre todos los agentes
- Puede crear, modificar y eliminar agentes
- Acceso a configuración avanzada y deployment
- Puede asignar permisos a otros usuarios
- Scopes: `agent:* project:* mcp:* user:manage`

**agent_developer - Desarrollador de Agentes:**
- Puede desarrollar y modificar agentes existentes
- Puede ejecutar tests y ver logs
- No puede deployar a producción directamente
- Scopes: `agent:read agent:update agent:test agent:logs`

**agent_user - Usuario Estándar:**
- Puede invocar agentes asignados
- Puede ver información básica de agentes
- No puede modificar configuración
- Scopes: `agent:invoke agent:read`

**agent_viewer - Visualizador:**
- Solo lectura de agentes y documentación
- Acceso a logs para análisis
- Scopes: `agent:read agent:logs`

**Usuarios Genéricos para Agentes Batch:**
- Crear usuarios no asociados a personas para agentes batch
- Permisos específicos para ejecución programada
- Auditoría de acciones realizadas

#### 3.3.3 Principio de Menor Privilegio

**Implementación:**

1. **Permisos de Usuario:**
   - Asignar roles específicos, no usar admin por defecto
   - Revisar permisos regularmente (trimestral)
   - Remover accesos de empleados que cambian de rol
   - Permisos temporales con auto-expiración para casos especiales

2. **Permisos de Agentes (IAM Roles):**
   - Cada agente tiene su propio IAM role específico
   - Rol de servicio por defecto con políticas base:
     * CloudWatch Logs (escritura)
     * S3 (lectura/escritura en bucket específico)
     * Bedrock (invocación de modelos)
     * DynamoDB (lectura/escritura en tablas específicas)
   - Posibilidad de añadir políticas adicionales desde FactorIA (fusión de políticas)
   - Evitar wildcards (*) en resources cuando sea posible

### 3.4 Rotación de Credenciales

**Estrategia de Rotación:**

**Usuarios Corporativos:**
- Gestionado por Entra ID
- Rotación automática según políticas corporativas
- No requiere gestión adicional

**Credenciales Técnicas:**
- **Secret Manager** con rotación automática (90 días)
- Credenciales M2M con terceros: pendiente de implementar cuando sea necesario
- Gestión vía Terraform, no vía FactorIA

**Rotación de Secrets: Ejemplo**
```yaml
RotationSchedule:
  - Credential: API Keys (terceros)
    Frequency: 90 days
    Method: Automated via Secrets Manager
    
  - Credential: Database Passwords
    Frequency: 90 days
    Method: Automated via RDS rotation
    
  - Credential: JWT Signing Keys
    Frequency: 180 days
    Method: Managed by Cognito
    
  - Credential: Service Account Keys
    Frequency: 365 days
    Method: Automated via IAM
```

---

## 4. Seguridad

### 4.1 Zero Trust Architecture

**Principios:**
1. **Nunca confiar, siempre verificar**
2. **Asumir que la red está comprometida**
3. **Verificar explícitamente cada solicitud**
4. **Usar acceso con menor privilegio**
5. **Asumir que habrá brechas**

**Implementación en AgentCore:**

```
┌─────────────────────────────────────────────────────────┐
│                    Zero Trust Layers                    │
├─────────────────────────────────────────────────────────┤
│  1. Identity Verification (Entra ID + Cognito)          │
│  2. Device Posture Check (Conditional Access)           │
│  3. Network Segmentation (VPC, Security Groups)         │
│  4. Application Security (WAF Kyndryl)                  │
│  5. Data Encryption (TLS 1.3, KMS)                      │
│  6. Continuous Monitoring (CloudWatch, GuardDuty)       │
└─────────────────────────────────────────────────────────┘
```

### 4.2 Cifrado

#### 4.2.1 Cifrado en Tránsito

**TLS 1.3 Obligatorio:**
- **Out-of-the-box** en AgentCore
- Security Policy: TLS 1.3 únicamente
- Perfect Forward Secrecy (PFS) habilitado
- Cipher suites seguros: TLS_AES_128_GCM_SHA256, TLS_AES_256_GCM_SHA384
- Certificados de AWS Certificate Manager con renovación automática

**Mutual TLS (mTLS) para MCP Servers:**
- **No aplica** si MCPs están dentro de AgentCore (protegidos por permisos)
- Si MCPs están fuera, se usa el mismo token JWT del usuario
- Autenticación bidireccional no requerida en arquitectura actual

#### 4.2.2 Cifrado en Reposo

**AWS KMS Configuration:**

**Key Management Strategy:**
- Una CMK (Customer Managed Key) por entorno y tipo de dato
- Rotación automática anual habilitada
- Key policies restrictivas con principio de menor privilegio
- Multi-region keys para disaster recovery si se requiere

**Recursos Cifrados:**

1. **RDS Database:**
   - Cifrado habilitado a nivel de instancia
   - KMS CMK dedicada para RDS
   - Cifrado de snapshots automático

2. **DynamoDB:**
   - Encryption at rest habilitada
   - AWS owned CMK o Customer managed CMK según sensibilidad
   - Point-in-time recovery con cifrado

3. **S3 Buckets:**
   - Default encryption habilitado (SSE-KMS)
   - Bucket policy que rechaza objetos no cifrados
   - Versionado habilitado

4. **Secrets Manager:**
   - Cifrado automático con KMS
   - Rotación automática habilitada
   - Versionado de secrets

5. **CloudWatch Logs:**
   - Log groups cifrados con KMS
   - Retención configurada según política
   - Exportación a S3 cifrado para archivado

### 4.3 Protección contra Ataques

#### 4.3.1 Rate Limiting (PENDIENTE DE DEFINIR)

**Estrategia Propuesta:**

**Dos Niveles de Rate Limiting:**

1. **Por Usuario:**
   - Límite de peticiones/día
   - Límite de tokens/día (consumo de Bedrock)
   - Diferenciación por tipo de usuario (free, standard, premium)
   - **Pendiente**: Definir límites específicos según casos de uso

2. **Por Agente:**
   - Throttling de Bedrock (límite de invocaciones a modelos)
   - Gestión en Agent Controller
   - Detección y manejo de errores de throttling

**Distinción Importante:**
- **Agentes conversacionales**: Expuestos a usuarios, requieren rate limiting estricto
- **Agentes batch**: Procesos programados, rate limiting menos restrictivo

**Implementación:**
- En el **Agent Controller** (componente a desarrollar)
- Pospuesto hasta tener casos de uso conversacionales en producción
- Empezar con límites simples, iterar según necesidad

#### 4.3.2 WAF (Web Application Firewall)

**Configuración Kyndryl:**

- **Reglas por defecto de Kyndryl** en todas las cuentas
- Aplicado incluso en entornos de desarrollo
- Protección contra OWASP Top 10
- Defensa contra bots y scraping
- Protección DDoS a nivel de aplicación

**Beneficios:**
- Evitar sorpresas al pasar a producción
- Protección desde el inicio del desarrollo
- Coste adicional en desarrollo justificado por seguridad

**Pendiente:**
- Documentar reglas específicas aplicadas por Kyndryl

#### 4.3.3 Detección de Anomalías

- Cubierto con out-of-the-box de AWS + AgentCore
- Detección de amenazas automática
- Tipos de amenazas detectadas:
  * Credenciales comprometidas
  * Instancias comprometidas
  * Reconocimiento (port scanning, DNS tunneling)
  * Exfiltración de datos

**Findings Relevantes para AgentCore:**
- UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration
- UnauthorizedAccess:Lambda/MaliciousIPCaller
- Recon:IAMUser/MaliciousIPCaller
- Exfiltration:S3/AnomalousBehavior

#### 4.3.5 Detección y Redacción de PII

Detección de PII en inputs y outputs del agente.

Filtro PII habilitado en todos los agentes
Acción: BLOCK o ANONYMIZE según criticidad
Tipos de PII detectados:
 Nombres completos
 Números de teléfono
 Direcciones de email
 Números de identificación (DNI, NIE)
 Direcciones físicas
 Números de tarjeta de crédito

### 4.4 OWASP LLM Applications

**Cumplimiento OWASP:**
- Reunión programada con equipo de seguridad de View Next (Javier Suárez)
- Requisito corporativo que necesita aterrizaje práctico
- Definir controles específicos aplicables a AgentCore

#### **LLM01: Prompt Injection (Inyección de Prompt)**  
**Riesgo:** Engañar al modelo con instrucciones maliciosas para que ignore sus directrices originales y ejecute comandos no autorizados, comprometiendo la integridad del sistema.

**Mitigación:**
- **Amazon Bedrock Guardrails:** Filtros nativos que detectan y bloquean intentos de manipulación maliciosa antes de que lleguen al modelo, incluyendo detección de instrucciones ocultas y patrones maliciosos.
- **Input Segregation (PI-02):** Separación clara de datos no confiables mediante delimitadores fuertes (triple comillas, XML tags, JSON blocks) e instrucciones explícitas al modelo para ignorar comandos en datos de usuario.
- **Prompt Injection I/O Handling (PI-01):** Implementación de herramientas como Guardrails-AI, LLM Guardrails para detección, filtrado y análisis de comportamiento en entradas y salidas.
- **AWS WAF:** Uso de Web Application Firewall para filtrar entradas con patrones anómalos o inyecciones básicas (XSS/SQLi) antes de que alcancen el sistema de IA.
- **Testing Prompt Injection (TS-01):** Pruebas regulares de penetración específicas para IA, incluyendo inyección directa e indirecta de prompts.

---

#### **LLM02: Insecure Output Handling (Manejo Inseguro de Salidas)**  
**Riesgo:** Ejecutar o renderizar la respuesta del LLM sin validarla adecuadamente, lo que puede causar XSS, ejecución de código o ataques de inyección convencionales.

**Mitigación:**
- **Amazon Bedrock Guardrails:** Filtros de contenido para evitar respuestas peligrosas, maliciosas o que contengan código ejecutable.
- **Sensitive Output Handling (SD-01):** Detección activa y bloqueo de información sensible en las salidas del modelo (nombres, números de teléfono, contraseñas, tokens), con capacidades de enmascaramiento y registro.
- **Encode Model Output (RT-05):** Aplicación de codificación de salida apropiada según el contexto (HTML, JavaScript, SQL) para prevenir ataques de inyección convencionales como XSS.
- **AWS Lambda (Validación de Esquemas):** Uso de librerías como Pydantic para validar que las respuestas cumplan con estructuras esperadas y parametrización de la salida.
- **Oversight (GC-UB-01):** Supervisión del comportamiento del modelo mediante mecanismos automatizados con reglas de detección para lenguaje ofensivo, toxicidad, información peligrosa y datos sensibles.

---

#### **LLM03: Training Data Poisoning (Envenenamiento de Datos)**  
**Riesgo:** Manipular los datos de entrenamiento o datos de aumento (RAG) para introducir sesgos, vulnerabilidades o comportamientos maliciosos en el modelo.

**Mitigación:**
- **Development Security (DT-01):** Protección del entorno de desarrollo con cifrado de datos en reposo, control de acceso técnico centralizado, verificación de integridad y monitoreo continuo.
- **Augmentation Data Integrity (RT-08):** Protección de la integridad de datos de aumento en bases de datos vectoriales mediante control de acceso, cifrado, checksums y monitoreo de manipulaciones.
- **Augmentation Data Confidentiality (RT-07):** Control de acceso y cifrado en reposo de bases de datos vectoriales para prevenir acceso no autorizado.
- **Aislamiento de Datos en S3:** Uso de políticas IAM estrictas y cifrado con AWS KMS para proteger los datos de entrenamiento y validación.
- **Auditoría y Trazabilidad:** Registro de modificaciones en datos mediante AWS CloudTrail y Amazon Macie para escaneo de datos manipulados o sensibles.

---

#### **LLM04: Model Denial of Service (Denegación de Servicio del Modelo)**  
**Riesgo:** Consumir recursos del modelo con peticiones pesadas, complejas o en gran volumen, causando interrupciones del servicio o costos elevados.

**Mitigación:**
- **Rate Limit (IT-GEN-02):** Limitación de tasa por actor (usuario, clave API o sesión) para ralentizar experimentación maliciosa y prevenir agotamiento de recursos.
- **Throttling en Amazon:** Configuración de límites de tasa y cuotas por usuario para controlar el flujo de peticiones.
- **Limit Resources (AR-02):** Establecimiento de límites en tiempo de procesamiento por inferencia, memoria utilizada, llamadas de API/función y recursos de computación, con timeouts y mecanismos de terminación.
- **DOS Input Validation (AR-01):** Validación y sanitización de entrada para rechazar o corregir contenido malicioso diseñado para aumentar el tiempo de computación (ej. entradas muy grandes).
- **AWS WAF (Bot Control):** Reglas para limitar peticiones automatizadas y proteger contra bots maliciosos.
- **Monitor Use (IT-GEN-01):** Observabilidad completa del uso del modelo, entradas, salidas y comportamiento del sistema para identificar patrones de abuso.

---

#### **LLM05: Supply Chain Vulnerabilities (Vulnerabilidades en la Cadena de Suministro)**  
**Riesgo:** Usar modelos, librerías, datasets o dependencias de terceros que ya vienen comprometidos o contienen vulnerabilidades conocidas.

**Mitigación:**
- **Modelos Gestionados por AWS Bedrock:** Uso de modelos pre-entrenados seguros y mantenidos por AWS (Anthropic Claude, Meta Llama, Amazon Titan, etc.) con garantías de seguridad.
- **Supply Chain Management (DT-05):** Gestión completa de la cadena de suministro con procedencia, trazabilidad, verificación de integridad y evaluación de proveedores.
- **Amazon Inspector & AWS CodeArtifact:** Escaneo automático de vulnerabilidades en dependencias (SCA) y uso de repositorios confiables para librerías.
- **Escaneo de Imágenes de Contenedores:** Verificación de seguridad de imágenes Docker antes del despliegue.
- **Runtime Model Integrity (RT-01):** Protección de la integridad del modelo mediante checksums, firmas digitales y controles de acceso (gestionado por Bedrock).

---

#### **LLM06: Sensitive Information Disclosure (Divulgación de Información Sensible)**  
**Riesgo:** El modelo revela contraseñas, PII (información personal identificable), secretos de la empresa o datos de entrenamiento en sus respuestas.

**Mitigación:**
- **Bedrock Guardrails (Data Redaction):** Enmascaramiento automático de PII en las respuestas del modelo mediante detección y filtrado de información sensible.
- **Sensitive Output Handling (SD-01):** Escaneo activo de salidas para información restringida (nombres, números de teléfono, identificadores, contraseñas, tokens) con filtrado, enmascaramiento o detención.
- **Model Input Confidentiality (RT-06):** Protección de la confidencialidad del transporte y almacenamiento de entrada del modelo mediante control de acceso, cifrado y minimización de retención.
- **Testing Sensitive Data Disclosure (TS-05):** Pruebas regulares de ataques de inversión de modelo, inferencia de membresía y extracción directa de datos sensibles.
- **RAG con Control de Acceso (RBAC):** Filtros para garantizar que el LLM solo acceda a información autorizada según el rol del usuario.
- **Data Minimization:** Minimizar la cantidad, granularidad y duración de almacenamiento de información personal en conjuntos de datos.

---

#### **LLM07: Insecure Plugin Design (Diseño Inseguro de Plugins/Herramientas)**  
**Riesgo:** Herramientas o plugins que confían ciegamente en el LLM, carecen de control de acceso adecuado o no validan las acciones solicitadas por el modelo.

**Mitigación:**
- **Agentes de Bedrock (Action Groups):** Definición de herramientas mediante esquemas OpenAPI estrictos que especifican claramente parámetros, tipos de datos y restricciones.
- **Model Access Control (IT-GEN-03):** Autenticación y autorización robusta mediante AWS IAM, restringiendo el acceso a funciones de inferencia solo a usuarios aprobados e identificables.
- **Autenticación con Cognito:** Uso de autorizadores (Amazon Cognito/JWT) para validar la identidad del usuario antes de permitir acceso a herramientas.
- **Least Model Privilege (GC-UB-02):** Minimización de lo que un modelo puede hacer (acciones y datos) mediante políticas de mínimos privilegios, ejecutando acciones con los derechos del usuario que se está atendiendo.
- **AWS Lambda con Roles Restrictivos:** Roles de ejecución con permisos mínimos necesarios para cada función específica.

---

#### **LLM08: Excessive Agency (Agencia Excesiva)**  
**Riesgo:** Permitir que el agente realice acciones destructivas, irreversibles o de alto impacto sin supervisión humana adecuada.

**Mitigación:**
- **Least Model Privilege (GC-UB-02):** Implementación del principio de mínimo privilegio mediante roles IAM de solo lectura por defecto y permisos limitados para operaciones críticas.
- **Human-in-the-Loop (HITL):** Requerimiento de confirmación explícita de un usuario humano para acciones sensibles mediante AWS Step Functions con puntos de aprobación.
- **Oversight (GC-UB-01):** Supervisión del comportamiento del modelo con mecanismos de rollback y presentación de acciones para aprobación humana cuando sea necesario.
- **Model Alignment (GC-UB-03):** Alineación del modelo mediante prompts de sistema/instrucción y guardrails incorporados para limitar comportamientos no deseados.
- **Monitor Use (IT-GEN-01):** Registro completo de acciones del modelo, entradas, salidas y comportamiento del sistema para auditoría y detección de anomalías.

---

#### **LLM09: Overreliance (Exceso de Confianza)**  
**Riesgo:** Confiar ciegamente en las respuestas del LLM sin verificar posibles "alucinaciones", información incorrecta o sesgos en las salidas.

**Mitigación:**
- **Citas y Referencias Nativas (RAG):** Uso de Knowledge Bases for Amazon Bedrock para devolver citas verificables y referencias a fuentes originales en las respuestas.
- **AI Transparency (GC-UB-04):** Información clara a los usuarios sobre las propiedades del sistema de IA, incluyendo precisión esperada, robustez de la salida y cualquier riesgo residual.
- **Bedrock Model Evaluation:** Evaluaciones continuas de precisión, exactitud y calidad de respuestas antes de desplegar nuevas versiones o configuraciones.
- **Oversight (GC-UB-01):** Supervisión humana o automatizada del comportamiento del modelo con verificaciones de fundamentación (grounding checks).
- **Security Education (GC-GOV-06):** Educación de usuarios y desarrolladores sobre limitaciones de los LLMs, incluyendo el fenómeno de alucinaciones y la necesidad de verificación.

---

#### **LLM10: Model Theft (Robo del Modelo)**  
**Riesgo:** Copiar, extraer o clonar el modelo mediante consultas masivas, acceso no autorizado al almacenamiento o técnicas de ingeniería inversa.

**Mitigación:**
- **Protección Nativa de Amazon Bedrock:** Garantía contractual de que los modelos no pueden ser descargados ni extraídos, con aislamiento completo en la infraestructura de AWS.
- **Runtime Model Confidentiality (RT-03):** Protección de la confidencialidad del modelo mediante control de acceso, cifrado y Entornos de Ejecución Confiables (TEE) gestionados por AWS.
- **Model Obfuscation (RT-04):** Técnicas de ofuscación para almacenar el modelo de manera compleja, dificultando la extracción y comprensión (responsabilidad de AWS).
- **Rate Limit (IT-GEN-02):** Limitación de tasa por actor para prevenir consultas masivas diseñadas para extraer comportamiento del modelo.
- **Protección contra Clonación de Comportamiento:** Alarmas en Amazon CloudWatch para detectar y bloquear patrones de extracción masiva mediante análisis de series de entrada (IT-GEN-05).
- **Monitor Use (IT-GEN-01):** Observabilidad y registro de uso del modelo para identificar patrones sospechosos de consultas que puedan indicar intentos de robo.
- **Discrete (GC-DL-05):** Minimización del acceso a detalles técnicos del modelo que podrían ayudar a los atacantes en sus esfuerzos de extracción.

---

## **Controles Transversales de Seguridad**

### **Gobernanza y Gestión**
- **Security Program (GC-GOV-02):** Sistema de gestión de seguridad de la información que incluye todo el ciclo de vida de IA con inventario de casos de uso.
- **Secure Development Program (GC-GOV-03):** Integración de seguridad en el desarrollo mediante skills, security-sdk, revisión de código y modelado de amenazas específicas de IA.
- **Compliance (GC-GOV-05):** Cumplimiento con GDPR y otras regulaciones relevantes para IA.
- **Security Education (GC-GOV-06):** Programa de educación continua sobre amenazas de IA y controles de seguridad.

### **Detección y Respuesta**
- **Anomalous Input Handling (IT-GEN-04):** Sanitización de entradas y detección de anomalías mediante análisis Out-of-Distribution (OOD) y detección de valores atípicos.
- **Evasion Input Handling (EV-01):** Implementación de capa previa con PCA sobre embeddings, clasificador adversarial y normalización para detectar entradas adversariales.
- **Unwanted Input Series Handling (IT-GEN-05):** Análisis estadístico de series de entrada para detectar patrones de optimización o reconocimiento.

### **Seguridad en Runtime**
- **Runtime Model I/O Integrity (RT-02):** Comunicaciones cifradas para proteger contra ataques man-in-the-middle.
- **Model Access Control (IT-GEN-03):** LLM Gateway (Bedrock) con autenticación IAM y control de acceso granular.

---

## **Controles Adicionales de Privacidad y Cumplimiento**

### **Principios de Privacidad (Privacy Principles)**

**Use Limitation (PR-01):**
- **Riesgo:** Usar datos recopilados para un propósito (ej. seguridad) como conjunto de entrenamiento para otros propósitos incompatibles (ej. perfilado, marketing).
- **Mitigación:** Documentar propósito/base legal antes de recopilar datos, crear copias anonimizadas para propósitos incompatibles, implementar enclaves de datos y aprendizaje federado. Verificar base legal según GDPR Art. 6 y 9.
- **Estado:** Not Yet Answered (NYA)

**Fairness (PR-02):**
- **Riesgo:** Comportamiento discriminatorio del algoritmo que conduce a efectos adversos injustificados para ciertos grupos.
- **Mitigación:** Evaluar equidad usando métricas de equidad de grupo, tasa de error de falsos positivos, desconocimiento y equidad contrafactual. Realizar análisis antes del desarrollo para identificar sesgos estructurales. Balancear precisión con no discriminación.
- **Estado:** Not Yet Answered (NYA)

**Data Minimization (PR-03):**
- **Riesgo:** Recopilación excesiva de información personal que aumenta el riesgo de exposición.
- **Mitigación:** No recopilar atributos innecesarios, anonimizar datos, reducir granularidad (redondear coordenadas, truncar IPs), eliminar vínculos (ofuscar IDs de usuario), usar técnicas de análisis distribuido y computación segura multi-parte.
- **Estado:** Not Yet Answered (NYA)

**Storage Limitation (PR-04):**
- **Riesgo:** Retención indefinida de datos personales más allá de su propósito original.
- **Mitigación:** Establecer períodos de retención basados en propósito y requisitos legales, implementar políticas automáticas de eliminación, aplicar tanto a datos de entrenamiento como a logs de inferencia.
- **Estado:** Not Yet Answered (NYA)

**Transparency (PR-05):**
- **Riesgo:** Falta de claridad sobre cómo se procesan los datos personales y cómo funciona el modelo.
- **Mitigación:** Documentación del modelo (tipo, intención, características, daño potencial, sesgo), transparencia del dataset (fuente, base legal, Data Cards), trazabilidad de decisiones, explicabilidad mediante LIME, SHAP, explicaciones contrafactuales. GDPR Art. 22 requiere información significativa sobre la lógica involucrada.
- **Estado:** Not Yet Answered (NYA)

**Privacy Rights (PR-06):**
- **Riesgo:** Incapacidad de los individuos para ejercer sus derechos sobre sus datos personales.
- **Mitigación:** Implementar derecho de acceso/portabilidad (proporcionar copia en formato legible por máquina), derecho de eliminación (borrar datos y re-entrenar modelo), derecho de corrección (corregir datos incorrectos), derecho de objeción (permitir objetar uso para entrenamiento).
- **Estado:** Not Yet Answered (NYA)

**Data Accuracy (PR-07):**
- **Riesgo:** Decisiones algorítmicas basadas en datos incorrectos pueden tener consecuencias severas.
- **Mitigación:** Procesos para corregir problemas de precisión rápidamente, obtener datos de fuentes confiables, validar reclamos de validez, evaluar periódicamente calidad y precisión de datos.
- **Estado:** Not Yet Answered (NYA)

**Consent (PR-08):**
- **Riesgo:** Uso de datos personales sin consentimiento apropiado cuando es legalmente requerido.
- **Mitigación:** Obtener consentimiento antes de recopilar/usar/compartir datos, debe ser registrado y auditable, granular por propósito, no empaquetado con T&C, protegido contra manipulación. Retiro debe ser tan fácil como dar consentimiento. Si se retira, eliminar datos y re-entrenar modelo.
- **Estado:** Not Yet Answered (NYA)

### **Cumplimiento Normativo**

**EU AI Act Compliance (AR-02):**
- **Riesgo:** Incumplimiento con el EU AI Act que puede resultar en sanciones y prohibiciones.
- **Mitigación:** 
  - **Riesgo Inaceptable:** Evitar sistemas prohibidos (manipulación de personas, puntuación social, identificación biométrica remota en tiempo real)
  - **Alto Riesgo:** Cumplir evaluación de riesgo de seguridad y estándares armonizados para infraestructura crítica y aplicación de la ley
  - **Riesgo Limitado:** Cumplir requisitos mínimos de transparencia
  - **IA Generativa:** Divulgar fuentes con derechos de autor usadas y prevenir contenido ilegal
- **Estado:** Not Yet Answered (NYA)

---

## **Resumen Completo de Controles por Estado**

### **Controles Completamente Implementados (FC) - 3 controles:**
1. **RT-01** - Runtime Model Integrity (Bedrock)
2. **RT-03** - Runtime Model Confidentiality (Bedrock)
3. **RT-04** - Model Obfuscation (Bedrock)

### **Controles En Progreso (IP) - 35 controles:**

**Gobernanza (4):**
- GC-GOV-02 (SEC PROGRAM)
- GC-GOV-03 (SEC DEV PROGRAM)
- GC-GOV-05 (CHECK COMPLIANCE)
- GC-GOV-06 (SEC EDUCATE)

**Limitación de Comportamiento No Deseado (4):**
- GC-UB-01 (OVERSIGHT)
- GC-UB-02 (LEAST MODEL PRIVILEGE)
- GC-UB-03 (MODEL ALIGNMENT)
- GC-UB-04 (AI TRANSPARENCY)

**Controles Generales de Entrada (5):**
- IT-GEN-01 (MONITOR USE)
- IT-GEN-02 (RATE LIMIT)
- IT-GEN-03 (MODEL ACCESS CONTROL)
- IT-GEN-04 (ANOMALOUS INPUT HANDLING)
- EV-01 (EVASION INPUT HANDLING)

**Prompt Injection (2):**
- PI-01 (PROMPT INJECTION I/O HANDLING)
- PI-02 (INPUT SEGREGATION)

**Divulgación de Datos (1):**
- SD-01 (SENSITIVE OUTPUT HANDLING)

**Agotamiento de Recursos (2):**
- AR-01 (DOS INPUT VALIDATION)
- AR-02 (LIMIT RESOURCES)

**Desarrollo (2):**
- DT-01 (DEV SECURITY)
- DT-05 (SUPPLY CHAIN MANAGE)

**Runtime (5):**
- RT-02 (RUNTIME MODEL IO INTEGRITY)
- RT-05 (ENCODE MODEL OUTPUT)
- RT-06 (MODEL INPUT CONFIDENTIALITY)
- RT-07 (AUGMENTATION DATA CONFIDENTIALITY)
- RT-08 (AUGMENTATION DATA INTEGRITY)

**Testing (2):**
- TS-01 (TESTING PROMPT INJECTION)
- TS-05 (TESTING SENSITIVE DATA DISCLOSURE)

### **Controles Pendientes (NYA) - 11 controles:**

**Limitación de Datos (1):**
- GC-DL-05 (DISCRETE)

**Series de Entrada (1):**
- IT-GEN-05 (UNWANTED INPUT SERIES HANDLING)

**Privacidad (8):**
- PR-01 (USE LIMITATION)
- PR-02 (FAIRNESS)
- PR-03 (DATA MINIMIZATION)
- PR-04 (STORAGE LIMITATION)
- PR-05 (TRANSPARENCY)
- PR-06 (PRIVACY RIGHTS)
- PR-07 (DATA ACCURACY)
- PR-08 (CONSENT)

**Cumplimiento Normativo (1):**
- AR-02 (AI ACT COMPLIANCE)

### 4.5 Cumplimiento Normativo

#### 4.5.1 GDPR Compliance

**Región Frankfurt (eu-central-1):**
- Datos no salen de Europa
- Cumplimiento GDPR garantizado

**Principios Fundamentales:**

1. **Lawfulness, Fairness and Transparency:**
   - Base legal clara para procesamiento de datos personales
   - Privacy Notice clara en aplicación
   - Consentimiento explícito para datos sensibles

2. **Purpose Limitation:**
   - Datos solo usados para propósito específico declarado
   - No reutilización sin nuevo consentimiento

3. **Data Minimization:**
   - Solo recopilar datos necesarios
   - Retención limitada según política

4. **Accuracy:**
   - Datos actualizados y correctos
   - Derecho de rectificación

5. **Storage Limitation:**
   - Retención configurada en CloudWatch, DynamoDB, S3
   - Eliminación automática según política

6. **Integrity and Confidentiality:**
   - Cifrado TLS 1.3 + KMS
   - Controles de acceso estrictos

7. **Accountability:**
   - Auditoría completa vía CloudTrail
   - Documentación de procesamiento

**Derechos de los Interesados:**
- Derecho de acceso
- Derecho de rectificación
- Derecho al olvido (implementación pendiente)
- Derecho a la portabilidad
- Derecho de oposición

#### 4.5.2 ISO 27001 (Pendiente de Detallar)

**A.5 - Políticas de Seguridad de la Información**
- **Policies** + **IAM** para mínimo privilegio.
- **AWS Config** + **Security Hub** para auditoría continua.
- **CloudTrail** para trazabilidad y evidencia.
- **AWS Artifact** para certificaciones y reportes.

---

**A.6 - Organización de la Seguridad de la Información**
- Roles y funciones separados con **IAM Roles** + **IAM Identity Center (SSO)**.
- Cuentas segregadas.
- **CloudWatch** para detección y respuesta.
- Infraestructura como código para cambios controlados.

---

**A.8 - Gestión de Activos**
- Inventario automático con **AWS Config** y **Systems Manager Inventory**.
- **Tagging obligatorio** (propietario, clasificación, criticidad).
- Cifrado con **KMS** y políticas de backup con **AWS Backup**.
- Controles de exposición (S3 Block Public Access).

---

**Nota:** Estos controles fortalecen el SGSI conforme a ISO 27001, integrando herramientas nativas de AWS.

---

### 4.6 Seguridad de Servidores MCP

#### 4.6.1 Escaneo de Seguridad MCP

**Herramientas de Análisis:**

Los servidores MCP deben ser escaneados regularmente para detectar:
- Prompt injection vulnerabilities
- Tool poisoning
- Flujos tóxicos
- Problemas de autenticación
- Vulnerabilidades en tool descriptions

**Herramientas Recomendadas:**
- **mcp-scan**: Scanner específico para MCP (Invariant Labs)
- **Bandit**: Análisis de seguridad Python
- **Semgrep**: SAST (Static Application Security Testing)
- **Checkov**: Escaneo de IaC

#### 4.6.2 Arquitectura de MCP Security Scanner en AWS

**Patrón Recomendado:**

AWS AgentCore Agent │ │ MCP Protocol ▼ MCP Security Scanner Server (ECS/Fargate) │ ├── mcp-scan (MCP-specific) >├── Semgrep (SAST) ├── Bandit (Python security) └── Checkov (IaC scanning) │ ▼ Target MCP Servers / Code >Repos / IaC

**Componentes:**

1. **MCP Scanner Server**: Servicio que expone herramientas de escaneo como tools MCP
2. **ECS/Fargate**: Runtime para el scanner
3. **CloudWatch**: Logs y métricas de escaneos


#### 4.6.3 Implementación del MCP Scanner

**Ejemplo de MCP Server con Herramientas de Seguridad:**

```python
from mcp.server.fastmcp import FastMCP
import subprocess
import json

mcp = FastMCP("security-scanner")

@mcp.tool()
def scan_mcp_server(url: str) -> dict:
    """Escanea un servidor MCP en busca de vulnerabilidades"""
    result = subprocess.run(
        ["mcp-scan", "scan", "--target", url, "--format", "json"],
        capture_output=True,
        text=True
    )
    return json.loads(result.stdout)

@mcp.tool()
def scan_repository(path: str, config: str = "auto") -> dict:
    """Escanea un repositorio con Semgrep"""
    result = subprocess.run(
        ["semgrep", "--config", config, path, "--json"],
        capture_output=True,
        text=True
    )
    return json.loads(result.stdout)

@mcp.tool()
def scan_python_security(path: str) -> dict:
    """Escanea código Python con Bandit"""
    result = subprocess.run(
        ["bandit", "-r", path, "-f", "json"],
        capture_output=True,
        text=True
    )
    return json.loads(result.stdout)

@mcp.tool()
def scan_infrastructure(path: str) -> dict:
    """Escanea IaC con Checkov"""
    result = subprocess.run(
        ["checkov", "-d", path, "-o", "json"],
        capture_output=True,
        text=True
    )
    return json.loads(result.stdout)

if __name__ == "__main__":
    mcp.run()
```

#### 4.6.4 Registro del Scanner en AgentCore

**Configuración del Agente:**

```json
{
  "mcpServers": {
    "security-scanner": {
      "transport": "http",
      "url": "https://scanner.company.com/mcp",
      "authentication": {
        "type": "bearer",
        "token": "${AWS_SECRET_TOKEN}"
      }
    }
  }
}
```

**El agente podrá invocar automáticamente:**

- `scan_mcp_server(url)`
- `scan_repository(path)`
- `scan_python_security(path)`
- `scan_infrastructure(path)`

#### 4.6.5 Monitorización en Runtime (Proxy MCP)

**Patrón Avanzado:**

```javascript
Client → mcp-scan proxy → MCP Server
```

**Capacidades:**

- Monitorización de llamadas en tiempo real
- Detección de ataques en runtime
- Bloqueo de herramientas maliciosas
- Logging de todas las invocaciones

**Implementación:**

```bash
# Ejecutar mcp-scan como proxy
mcp-scan proxy \
  --target https://backend-mcp-server.com/mcp \
  --listen 0.0.0.0:8080 \
  --log-level info
```

#### 4.6.6 Integración con CI/CD

**GitHub Actions Example:**

```yaml
name: MCP Security Scan

on: [push, pull_request]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install mcp-scan
        run: |
          pip install git+https://github.com/invariantlabs-ai/mcp-scan.git
      
      - name: Scan MCP Configuration
        run: |
          mcp-scan scan --config .mcp/config.json
      
      - name: Scan Python Code
        run: |
          bandit -r src/ -f json -o bandit-report.json
      
      - name: Upload Results
        uses: actions/upload-artifact@v3
        with:
          name: security-reports
          path: |
            bandit-report.json
            mcp-scan-report.json
```

#### 4.6.7 Despliegue en AWS

**Terraform Module:**

```hcl
module "mcp_scanner" {
  source = "./modules/mcp-scanner"
  
  cluster_name     = "agentcore-cluster"
  service_name     = "mcp-security-scanner"
  container_image  = "company/mcp-scanner:latest"
  
  vpc_id           = var.vpc_id
  subnet_ids       = var.private_subnet_ids
  
  environment = {
    LOG_LEVEL = "info"
    SCAN_TIMEOUT = "300"
  }
  
  tags = {
    UseCase     = "security"
    Project     = "factoria"
    Environment = "pro"
  }
}
```

#### 4.6.8 Métricas y Alertas

**CloudWatch Metrics:**

- `mcp_scans_total`: Total de escaneos ejecutados
- `mcp_vulnerabilities_found`: Vulnerabilidades detectadas
- `mcp_scan_duration`: Duración de escaneos
- `mcp_scan_failures`: Fallos en escaneos

**Alertas Recomendadas:**

- Vulnerabilidades críticas detectadas
- Fallos repetidos en escaneos
- Tiempo de escaneo excesivo
- Servidores MCP no escaneados en >7 días

#### 4.6.9 Mejores Prácticas

**Frecuencia de Escaneo:**

- **Desarrollo**: En cada commit (CI/CD)
- **Producción**: Semanal + on-demand

**Priorización de Vulnerabilidades:**

1. **Critical**: Prompt injection, credential exposure
2. **High**: Tool poisoning, authentication bypass
3. **Medium**: Information disclosure, weak validation
4. **Low**: Warnings, best practices

**Remediación:**

- Critical/High: Fix inmediato (< 24h)
- Medium: Fix en sprint actual
- Low: Backlog

#### 4.6.10 Limitaciones y Consideraciones

**⚠️ Importante:**

1. **AgentCore no ejecuta CLI tools directamente**

   - ❌ No funciona: `agent -> mcp-scan CLI`
   - ✅ Debe ser: `agent -> MCP server -> mcp-scan`

2. **Autenticación requerida**

   - Todos los MCP servers deben autenticar con JWT
   - El scanner debe propagar el token del usuario

3. **Performance**

   - Escaneos pueden ser lentos (minutos)
   - Considerar ejecución asíncrona
   - Implementar caching de resultados

4. **Falsos positivos**

   - Revisar manualmente vulnerabilidades críticas
   - Ajustar reglas según contexto
   - Mantener whitelist de excepciones


## 5. Observabilidad

### 5.1 Filosofía de Observabilidad

**Observabilidad Nativa:**
- Aprovechar capacidades out-of-the-box de AWS
- Minimizar instrumentación manual
- Usar estándares (OpenTelemetry, CloudWatch)

**Componentes Clave:**
- **CloudWatch Logs**: Logs estructurados
- **CloudWatch Metrics**: Métricas técnicas
- **HIS (Here-I-Strands)**: Trazabilidad de agentes
- **Factoría Dashboard**: Visualización centralizada
- **OpenTelemetry**: Telemetría estándar
- **DynamoDB**: Bus de eventos para trazas + procesos (no solo agentes)
- **FactorIA Dashboard**: Visualización centralizada
- **OpenTelemetry**: Telemetría estándar

### 5.2 HIS (Here-I-Strands) - Wrapper de Observabilidad

**Características:**
- **Wrapper sobre Strands** desarrollado por Bluetab
- **Publicado en PyPI** (licencia MIT)
- **Registro automático** de eventos en DynamoDB
- **Integración con Factoría** para visualización

**Eventos Registrados Automáticamente:**
- Inicio de sesión del agente
- Event loop (ciclo de eventos)
- Invocaciones a tools (MCP servers)
- Invocaciones a modelos (Bedrock)
- Errores y excepciones
- Finalización de sesión

**Flujo de Trazabilidad:**
```
Agente (con HIS wrapper)
    ↓
DynamoDB (eventos)
    ↓
Lambda (stream de DynamoDB)
    ↓
BD FactorIA (procesamiento)
    ↓
UI FactorIA (visualización)
```

**Beneficios:**
- **Sin modificar código del agente**: Wrapper transparente
- **Trazabilidad completa**: Todos los eventos registrados
- **Visualización en FactorIA**: Mejor experiencia que consola AWS
- **Estandarización**: Todos los agentes usan el mismo wrapper


### 5.3 Arquitectura de Observabilidad

**Decisión Arquitectónica:**
- **DynamoDB como bus de eventos** para trazas + procesos (no solo trazas de agentes)
- Los agentes no son el único componente: Step Functions y Lambdas también necesitan trazabilidad
- **Dashboard centralizado en FactorIA** para todos los agentes (evitar proliferación de dashboards)

**Flujo de Telemetría:**
```
Agente/Lambda/Step Function 
  → OpenTelemetry (instrumentación)
  → CloudWatch Logs
  → DynamoDB (trazas + procesos) via Stream
  → Lambda de procesamiento
  → FactorIA (dashboard)
  → Alertas SNS (si aplica)
```

**Beneficios:**
- **Trazabilidad completa**: Todos los eventos registrados automáticamente
- **Visualización centralizada**: Dashboard único en FactorIA vs. consola AWS
- **Diseño flexible**: Cubre todo el ecosistema de procesos, no solo agentes
- **Estandarización**: Todos los componentes usan el mismo flujo

### 5.4 Monitorización Técnica

#### 5.4.1 Métricas Técnicas (Estandarizadas)

- **Métricas técnicas estandarizadas** para todos los agentes
- **Dashboard técnico centralizado** en FactorIA
- **Instrumentación automática** con Strands + OpenTelemetry

**Métricas Técnicas Estándar:**

**Por Agente:**
- **Invocaciones**: Número total de invocaciones
- **Errores**: Número y tasa de errores (%)
- **Duración**: Tiempo de ejecución (p50, p95, p99)
- **Latencia de modelo**: Tiempo de llamadas a Bedrock
- **Tokens**: Input, output y total consumidos
- **Throttling**: Eventos de throttling de Bedrock
- **Concurrencia**: Ejecuciones concurrentes

**Métricas Nativas de AWS (Out-of-the-box):**

**Agent Core / Lambda:**
- Invocations, Errors, Duration, Throttles, ConcurrentExecutions

**Amazon Bedrock:**
- Invocations, ModelInvocationLatency, TokensUsed, InputTokens, OutputTokens, ThrottledRequests

**DynamoDB:**
- UserErrors, SystemErrors, ConsumedReadCapacityUnits, ConsumedWriteCapacityUnits, SuccessfulRequestLatency

**S3:**
- NumberOfObjects, BucketSizeBytes, AllRequests, 4xxErrors, 5xxErrors

#### 5.4.2 Dashboard Técnico Centralizado en FactorIA

- **Un único dashboard técnico** en FactorIA para todos los agentes
- Evitar proliferación de dashboards individuales
- Mejor experiencia de usuario vs. consola AWS

**Vistas del Dashboard Técnico:**

**1. Vista General:**
- Total de invocaciones en período
- Tasa de error agregada
- Latencia promedio (p50, p95, p99)
- Consumo de tokens total
- Costos estimados

**2. Vista por Agente:**
- Invocaciones por agente
- Error rate por agente
- Duración promedio
- Throttling events
- Consumo de tokens

**3. Vista de Bedrock:**
- Invocaciones al modelo
- Token usage (input/output)
- Costos por modelo
- Latencia del modelo
- Throttling de Bedrock

**4. Vista de Infraestructura:**
- DynamoDB: throttles, latencia, capacidad
- S3: requests, errores
- CloudWatch: logs, métricas

**5. Alertas Activas:**
- Lista de alarmas en estado ALARM
- Historial de alertas
- Acciones recomendadas

#### 5.4.3 Alertas Técnicas

- **Alertas vía SNS** (emails) como solución inicial
- Posible evolución a **SES** para emails más elaborados
- **NO se usará PagerDuty** (descartado)

**Tipos de Alertas Configuradas:**

1. **Tasa de Errores:**
   - Threshold: >5% en 5 minutos
   - Acción: Notificación SNS (email)
   - Severidad: Alta

2. **Latencia Excesiva:**
   - Threshold: p95 >3s en 10 minutos
   - Acción: Notificación SNS (email)
   - Severidad: Media

3. **Throttling de Bedrock:**
   - Threshold: >10 throttles/min
   - Acción: Notificación SNS (email)
   - Severidad: Alta

4. **Costos Anómalos:**
   - Threshold: Configurable por caso de uso
   - Acción: Notificación SNS (email)
   - Severidad: Media

**Configuración de SNS:**
- Topics por tipo de alerta
- Suscripciones por equipo/caso de uso
- Formato de notificación estandarizado

### 5.5 Monitorización Funcional  

#### 5.5.1 Métricas de Negocio

- **Métricas de negocio NO estandarizadas** - específicas por caso de uso
- Cada equipo define sus propias métricas funcionales
- Solo se proporcionan ejemplos como referencia

**Consideraciones Importantes:**
- **Métricas de calidad (confidence score, accuracy)**: Consideradas **testimoniales**
- **Expectativa de negocio**: 100% de acierto (gestión de expectativas crítica)
- **Enfoque**: Gestión de expectativas vs. métricas técnicas

**Ejemplos de Métricas por Tipo de Agente:**

**Agente de Soporte (Ejemplo):**
- ResolutionRate: % de tickets resueltos sin escalamiento
- TimeToResolution: Tiempo promedio de resolución
- UserSatisfaction: CSAT score promedio (si aplica)
- FirstContactResolution: % resuelto en primera interacción
- EscalationRate: % escalados a humano

**Agente de Desarrollo (Ejemplo):**
- CodeSuggestionsAccepted: % de sugerencias aceptadas
- BugsFound: Número de bugs identificados
- TimeToSolution: Tiempo promedio para resolver problema

**Agente de Documentación (Ejemplo):**
- DocumentsGenerated: Número de documentos generados
- DocumentQuality: Score de calidad evaluado por usuarios
- TimeToGenerate: Tiempo promedio de generación

**Nota**: Estos son solo ejemplos. Cada caso de uso debe definir sus propias métricas según sus objetivos de negocio.


**⚠️ Importante:**
Pendiente de revisar y cerrar estandarización de las metricas funcionales (DataLake) Repositorio Comun, conjunto de indicadores comunes "NAPAI" Posibilidad del uso de AWS Quicksights

#### 5.5.2 Dashboards Funcionales

- Dashboards funcionales específicos por caso de uso
- No hay plantilla única debido a la variabilidad de métricas
- Acordado con AWS se deberian gestionar dentro del dashboard de FactorIA.

**Componentes Sugeridos para Dashboards Funcionales:**

**1. Executive Summary (Adaptable):**
- Total de interacciones en período
- Tasa de éxito general
- Satisfacción de usuario promedio (si aplica - solo para casos con human-in-the-loop)
- Ahorro de tiempo/costos estimado

**2. Tendencias:**
- Adopción de agentes por semana/mes
- Evolución de métricas clave
- Mejora en tiempo de resolución

**3. Por Agente:**
- Uso relativo de cada agente
- Performance comparativo
- Casos de uso más frecuentes

**4. Impacto en el Negocio:**
- Horas ahorradas al equipo humano
- Tickets deflected (si aplica)
- ROI estimado

**Nota Importante:**
- **Satisfacción de usuario**: Solo para casos con human-in-the-loop (no todos los agentes tienen interacción directa)
- **Feedback de usuario**: Para mejora continua, NO para reentrenamiento automático
- Los usuarios son reticentes a escribir feedback detallado, se usa para ajustar prompts manualmente

### 5.6 Telemetría con OpenTelemetry

- **Strands** como framework de observabilidad con OpenTelemetry
- **Grapher** para procesar invocaciones a agentes y MCP servers
- **DynamoDB** como almacenamiento de trazas con stream para procesamiento

**Integración OpenTelemetry:**
- Integrado en Strands (framework de AWS)
- Estándar abierto para telemetría
- Exportación a CloudWatch
- Trazas distribuidas end-to-end

**Componentes:**
- **Strands**: Framework con OpenTelemetry integrado
- **DynamoDB Stream**: Trigger para Lambda de procesamiento
- **Lambda Processor**: Transforma y envía datos a FactorIA

**Eventos Capturados Automáticamente:**
- Inicio de sesión del agente
- Event loop (ciclo de eventos)
- Invocaciones a tools (MCP servers)
- Invocaciones a modelos (Bedrock)
- Errores y excepciones
- Finalización de sesión
- Procesos de Step Functions y Lambdas

### 5.7 Arquitectura de Observabilidad

- DynamoDB como bus de eventos para trazas + procesos (no solo agentes)
- Dashboard centralizado en FactorIA para todos los agentes
- Strands para observabilidad con OpenTelemetry

```
┌─────────────────────────────────────────────────────────────────┐
│                    USUARIO / APLICACIÓN                         │
└────────────────────┬────────────────────────────────────────────┘
                     │ Invoca agente con JWT
                     ▼
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  - Validación JWT inicial                                       │
│  - WAF (Kyndryl)                                                │
└────────────────────┬────────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────────┐
│                   AGENT CONTROLLER                              │
│  - Gestión de timeouts                                          │
│  - Rate limiting (pendiente definir)                            │
│  - Circuit breaker                                              │
│  - Gestión de concurrencia                                      │
└────────────────────┬────────────────────────────────────────────┘
                     │
                     ▼
┌────────────────────────────────────────────────────────────────┐
│              AWS AGENT CORE RUNTIME                            │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  AGENTE (Strands Framework)                              │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  1. Procesar solicitud                             │  │  │
│  │  │  2. Invocar sub-agentes (si aplica)                │  │  │
│  │  │  3. Invocar MCP Servers                            │  │  │
│  │  │  4. Invocar modelo LLM (Bedrock)                   │  │  │
│  │  │  5. Generar respuesta                              │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │                                                          │  │
│  │  Strands + OpenTelemetry registra automáticamente:       │  │
│  │  • Inicio de sesión                                      │  │
│  │  • Event loop                                            │  │
│  │  • Invocaciones a tools                                  │  │
│  │  • Invocaciones a modelos                                │  │
│  │  • Errores y excepciones                                 │  │
│  │  • Finalización de sesión                                │  │
│  │  • Procesos de Step Functions y Lambdas                  │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────┬───────────────────────────────────────────┘
                     │
        ┌────────────┴────────────┐
        │                         │
        ▼                         ▼
┌──────────────────┐    ┌──────────────────┐
│   DynamoDB       │    │  CloudWatch      │
│ (Bus de Eventos) │    │  Logs            │
│                  │    │                  │
│  • Trazas        │    │  • Logs          │
│  • Procesos      │    │  • Métricas      │
│  • Agentes       │    │  • Alarmas       │
│  • Step Functions│    │                  │
│  • Lambdas       │    │                  │
└────────┬─────────┘    └──────────────────┘
         │
         │ DynamoDB Stream
         ▼
┌──────────────────┐
│  Lambda Stream   │
│  Processor       │
│                  │
│  • Procesa       │
│  • Transforma    │
│  • Enriquece     │
│  • Agrega        │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  BD FactorIA     │
│  (Procesamiento) │
│                  │
│  • Agregación    │
│  • Análisis      │
│  • Métricas      │
└────────┬─────────┘
         │
         ▼
┌───────────────────────────────────────────────────────────────┐
│              FactorIA DASHBOARD CENTRALIZADO                  │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  DASHBOARD TÉCNICO (ÚNICO PARA TODOS LOS AGENTES)       │  │
│  │  ┌────────────────┐  ┌────────────────┐                 │  │
│  │  │  Invocaciones  │  │  Consumo       │                 │  │
│  │  │  por Agente    │  │  Tokens/Costes │                 │  │
│  │  └────────────────┘  └────────────────┘                 │  │
│  │  ┌────────────────┐  ┌────────────────┐                 │  │
│  │  │  Trazas de     │  │  Latencia      │                 │  │
│  │  │  Ejecución     │  │  (p50,p95,p99) │                 │  │
│  │  └────────────────┘  └────────────────┘                 │  │
│  │  ┌────────────────┐  ┌────────────────┐                 │  │
│  │  │  Errores y     │  │  Throttling    │                 │  │
│  │  │  Tasa de Error │  │  Bedrock       │                 │  │
│  │  └────────────────┘  └────────────────┘                 │  │
│  └─────────────────────────────────────────────────────────┘  │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  DASHBOARDS FUNCIONALES (ESPECÍFICOS POR CASO DE USO)   │  │
│  │  • Métricas de negocio personalizadas                   │  │
│  │  • KPIs específicos del caso de uso                     │  │
│  └─────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────┘
```

## Componentes Clave

### 1. HIS (Here-I-Strands) - Capa de Instrumentación

**Características:**
- **Wrapper transparente** sobre Strands (framework de AWS)
- **Registro automático** sin modificar código del agente
- **Publicado en PyPI** con licencia MIT
- **Desarrollado por Bluetab**

**Eventos Capturados Automáticamente:**
- Inicio y fin de sesión del agente
- Event loop (ciclo de eventos del agente)
- Invocaciones a tools (MCP servers)
- Invocaciones a modelos (Bedrock)
- Errores y excepciones
- Metadata de contexto (usuario, timestamp, etc.)

**Ventajas:**
- Sin impacto en el código del agente
- Estandarización automática
- Trazabilidad completa
- Integración nativa con FactorIA

---

### 2. DynamoDB - Almacenamiento de Trazas

**Configuración:**
- **Tabla de eventos** optimizada para queries
- **DynamoDB Streams** habilitado para procesamiento en tiempo real
- **Retención configurable** según política de datos

---

### 3. Lambda Stream Processor - Procesamiento en Tiempo Real

**Trigger:** DynamoDB Stream

**Funciones:**
1. **Transformación de eventos:**
   - Normalización de formato
   - Enriquecimiento con metadata adicional
   - Cálculo de métricas derivadas

2. **Agregación de métricas:**
   - Contadores por agente
   - Latencias promedio
   - Tasas de error
   - Consumo de tokens

3. **Detección de patrones anómalos:**
   - Errores repetidos
   - Latencias inusuales
   - Consumo excesivo de tokens

4. **Escritura a BD FactorIA:**
   - Eventos procesados
   - Métricas agregadas
   - Alertas generadas

**Código de Referencia:**

---

### 4. BD FactorIA - Almacenamiento Analítico

**Propósito:**
- Base de datos optimizada para análisis y reporting
- Agregaciones pre-calculadas para dashboards
- Métricas de negocio derivadas de eventos técnicos
- Histórico para análisis de tendencias


### 5. FactorIA Dashboard - Visualización Centralizada, Adaptar a no un unico Dashboard

**Características:**
- **Interfaz web única** para todos los agentes
- **Mejor experiencia** que consola AWS (decisión arquitectónica)
- **Tiempo real** con actualización automática
- **Búsqueda y filtrado** avanzados

**Vistas Disponibles:**

#### A. Sesiones Activas
- Lista de sesiones en curso
- Usuario, agente, duración
- Eventos en tiempo real
- Posibilidad de "seguir" una sesión

#### B. Trazas de Ejecución
- Timeline visual de eventos
- Detalles de cada invocación
- Inputs/outputs de tools y modelos
- Tiempos de ejecución

#### C. Consumo de Tokens y Costes
- Tokens por agente/usuario/período
- Costes estimados
- Tendencias de consumo
- Alertas de presupuesto

#### D. Métricas de Performance
- Latencia (p50, p95, p99)
- Tasa de error
- Throughput
- Comparativa entre agentes

#### E. Errores y Alertas
- Lista de errores recientes
- Alertas activas
- Análisis de causa raíz
- Acciones recomendadas

#### F. Análisis de Tendencias
- Adopción de agentes
- Evolución de métricas
- Patrones de uso
- Predicciones

---

### 6. CloudWatch - Observabilidad Nativa AWS

**Logs:**
- **CloudWatch Logs** para todos los componentes
- **Logs estructurados** en formato JSON
- **Retención configurable** (7, 30, 90 días)
- **CloudWatch Logs Insights** para queries ad-hoc

**Métricas:**
- **Métricas nativas** de Agent Core, Bedrock, DynamoDB
- **Métricas custom** enviadas desde agentes
- **Dashboards** en CloudWatch

**Alarmas:**
- **CloudWatch Alarms** para umbrales críticos
- **Composite Alarms** para reducir ruido
- **Integración con SNS** para notificaciones

---

### 7. OpenTelemetry - Telemetría Estándar

**Integración:**
- **Integrado en Agent Core** (out-of-the-box)
- **Trazas distribuidas** para seguimiento end-to-end
- **Propagación de contexto** entre componentes
- **Exportación a CloudWatch**
---

## Flujo de Datos Completo

### Flujo Paso a Paso

1. **Usuario invoca agente** con token JWT
2. **API valida** token y enruta a Agent Core
3. **Agent Core ejecuta** agente con wrapper GIS
4. **HIS captura eventos** automáticamente:
   - Inicio de sesión
   - Cada invocación a tool
   - Cada invocación a modelo
   - Errores si ocurren
   - Fin de sesión
5. **Eventos se escriben** a DynamoDB
6. **DynamoDB Stream dispara** Lambda
7. **Lambda procesa** eventos:
   - Transforma y enriquece
   - Calcula métricas
   - Detecta anomalías
8. **Lambda escribe** a BD FactorIA
9. **FactorIA Dashboard** visualiza en tiempo real
10. **Alertas se disparan** si hay anomalías
11. **CloudWatch** mantiene logs y métricas nativas

---

## Métricas Disponibles

### Métricas Técnicas (Automáticas)

**Por Agente:**
- Invocaciones totales
- Tasa de error (%)
- Latencia (p50, p95, p99)
- Tokens consumidos (input/output)
- Coste estimado
- Throttling de Bedrock

**Por Usuario:**
- Sesiones iniciadas
- Agentes utilizados
- Tokens consumidos
- Coste generado

**Por MCP Server:**
- Invocaciones
- Latencia
- Tasa de error

### Métricas Funcionales (Custom)

**Agente de Soporte:**
- Tickets analizados
- Resoluciones propuestas
- Tasa de aceptación
- Tiempo de resolución

**Agente de Desarrollo:**
- Sugerencias de código
- Bugs encontrados
- Calidad de código generado

---

## Alertas Configuradas

### Alertas Técnicas Revisar dependencias con Terceros. 

1. **Alta Tasa de Errores**
   - Threshold: >5% en 5 minutos
   - Acción: SNS + PagerDuty
   - Severidad: Alta

2. **Alta Latencia**
   - Threshold: p95 >3s en 10 minutos
   - Acción: SNS
   - Severidad: Media

3. **Throttling Excesivo**
   - Threshold: >10 throttles/min
   - Acción: SNS + Auto-scaling
   - Severidad: Alta

4. **Alto Coste**
   - Threshold: >$X/día
   - Acción: SNS + Revisión
   - Severidad: Media

### Alertas Funcionales

1. **CSAT Bajo**
   - Threshold: < 4.0/5.0
   - Acción: Notificación equipo
   - Severidad: Media

2. **Baja Tasa de Resolución**
   - Threshold: < 60%
   - Acción: Revisión de agente
   - Severidad: Media

---

> Pendiente cerrar Notificación de problemas de interfaz Gestion por caso de uso.

## Ventajas de esta Arquitectura

### 1. Trazabilidad Completa
- Todos los eventos registrados automáticamente
- Visibilidad end-to-end de cada invocación
- Correlación entre eventos relacionados
- Auditoría completa

### 2. Mejor Experiencia de Usuario
- Visualización en FactorIA vs. consola AWS
- Interfaz unificada para todos los agentes
- Búsqueda y filtrado intuitivos
- Tiempo real

### 3. Procesamiento en Tiempo Real
- Detección inmediata de problemas
- Alertas proactivas
- Métricas actualizadas en tiempo real
- Análisis de tendencias

### 4. Estandarización
- Todos los agentes usan el mismo wrapper (HIS)
- Formato consistente de eventos
- Facilita comparación entre agentes
- Reduce complejidad

### 5. Escalabilidad
- DynamoDB escala automáticamente
- Lambda procesa eventos en paralelo
- Sin impacto en performance del agente
- Arquitectura serverless

### 6. Separación de Responsabilidades
- Agente se enfoca en lógica de negocio
- HIS maneja observabilidad
- FactorIA maneja visualización
- CloudWatch maneja infraestructura

---

### 5.8 Monitorización de Performance

#### 5.8.1 Posibles Métricas Clave

**Métricas Técnicas:**
- **Latency**: p50, p95, p99 de tiempo de respuesta
- **Token Usage**: Input, output y total tokens por request
- **Cost**: Costo por request y costo total
- **Error Rate**: Porcentaje de errores
- **Throughput**: Requests por segundo
- **Vienen Out-of-the-box**

**Métricas de Calidad:** (En revision, de forma testimonial)
- **Confidence Score**: Score de confianza del modelo
- **Feedback Score**: Rating del usuario (1-5)
- **Accuracy**: Precisión de clasificación
- **Semantic Similarity**: Similitud entre output esperado y real
- **Requerirán que se programen Custom Spans (OTEL) en el flujo de código para enviarlas a CloudWatch.**

**Métricas de Negocio:**
- **Resolution Rate**: Tasa de resolución de tickets
- **Time to Resolution**: Tiempo promedio de resolución
- **User Satisfaction**: CSAT score
- **Deflection Rate**: Tasa de deflexión de tickets


#### 5.8.2 Dashboards 

**1. Executive Dashboard**
```
┌───────────────────────────────────────────────────────┐
│              Executive Dashboard - AgentCore          │
├───────────────────────────────────────────────────────┤
│                                                       │
│  Total Requests (30d)        User Satisfaction        │
│  ┌──────────────┐           ┌──────────────┐          │
│  │   125,450    │           │   4.3 / 5.0  │          │
│  │   ↑ 15%      │           │   ↑ 0.2      │          │
│  └──────────────┘           └──────────────┘          │
│                                                       │
│  Avg Cost per Request        Resolution Rate          │
│  ┌──────────────┐           ┌──────────────┐          │
│  │   $0.0045    │           │     68%      │          │
│  │   ↓ 8%       │           │   ↑ 5%       │          │
│  └──────────────┘           └──────────────┘          │
│                                                       │
│  Requests by Agent (Last 7 days)                      │
│  ┌─────────────────────────────────────────────────┐  │
│  │ lcs-com-gen-001 ████████████████ 45%            │  │
│  │ lcs-com-gen-002 ██████████ 28%                  │  │
│  │ lcs-com-gen-005 ██████ 18%                      │  │
│  │ lcs-com-gen-004 ███ 9%                          │  │
│  └─────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────┘
```

**2. Technical Performance Dashboard**
```
┌───────────────────────────────────────────────────────┐
│         Technical Performance - lcs-com-gen-001       │
├───────────────────────────────────────────────────────┤
│                                                       │
│  Latency Distribution                                 │
│  ┌─────────────────────────────────────────────────┐  │
│  │ p50: 850ms  p95: 2.1s  p99: 3.5s                │  │
│  │                                                 │  │
│  │     ▁▃▅█▅▃▁                                    │  │
│  │  ▁▃▅███████▅▃▁                                 │  │
│  │▁▃▅█████████████▅▃▁                             │  │
│  └─────────────────────────────────────────────────┘  │
│                                                       │
│  Token Usage (Last 24h)                               │
│  ┌─────────────────────────────────────────────────┐  │
│  │ Input:  2.5M tokens  ($12.50)                   │  │
│  │ Output: 1.8M tokens  ($54.00)                   │  │
│  │ Total:  4.3M tokens  ($66.50)                   │  │
│  └─────────────────────────────────────────────────┘  │
│                                                       │
│  Error Rate Trend                                     │
│  ┌─────────────────────────────────────────────────┐  │
│  │ 5% ┤                                            │  │
│  │ 4% ┤                                            │  │
│  │ 3% ┤     ╭─╮                                    │  │
│  │ 2% ┤   ╭─╯ ╰─╮                                  │  │
│  │ 1% ┤ ╭─╯     ╰─────────────                     │  │
│  │ 0% ┴─────────────────────────────────────────   │  │
│  └─────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────┘
```

Se debe construir (Executive y Technical) usando CloudWatch Custom Dashboards, ya que la información deberia estar disponible en CloudWatch Logs y Metrics.

#### 5.8.3 Alertas Configuradas

**Alertas Técnicas**:

Alta tasa de error (>5%)
Alta latencia (p95 > 3s)
Throttling excesivo
Costos anómalos de Bedrock

**Alertas de Negocio**:

CSAT drops por debajo de 4.0
Resolution rate cae por debajo de 60%
Tiempo de resolución aumenta >50%

### 5.10 Troubleshooting

#### 5.10.1 Root Cause Analysis

**proporciona:**
1. **Slice Analysis**: Identificar segmentos problemáticos
2. **Feature Impact**: Qué features causan problemas
3. **Cohort Analysis**: Comparar grupos de predicciones
4. **Time Series Analysis**: Identificar cuándo empezó el problema

**Ejemplo de Análisis:**

```
Problema Detectado: Error rate aumentó de 2% a 8%

Root Cause Analysis:
┌────────────────────────────────────────────────────────┐
│ Segmento Problemático Identificado:                    │
│                                                        │
│ • user_id contains "new_users"                         │
│ • input_text length > 1000 characters                  │
│ • ticket_priority = "low"                              │
│                                                        │
│ Error Rate en este segmento: 45%                       │
│ Error Rate en otros segmentos: 1.5%                    │
│                                                        │
│ Recomendación:                                         │
│ - Añadir validación de longitud de input               │
│ - Mejorar handling de tickets de baja prioridad        │
│ - Revisar onboarding de nuevos usuarios                │
└────────────────────────────────────────────────────────┘
```


## 6. Servidores MCP

### 6.1 Definición y Rol

**MCP (Model Context Protocol)** es un protocolo estándar que permite a los agentes interactuar con herramientas externas, recursos y servicios de manera uniforme.

**Componentes MCP:**

```
┌─────────────────────────────────────────────────────┐
│                   MCP Server                        │
├─────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌───────── ─┐  │
│  │    Tools     │  │  Resources   │  │ Prompts   │  │
│  │  (Actions)   │  │    (Data)    │  │(Templates)│  │
│  └──────────────┘  └──────────────┘  └──────── ──┘  │
└─────────────────────────────────────────────────────┘
                        ▲
                        │ MCP Protocol
                        ▼
┌─────────────────────────────────────────────────────┐
│                    Agent                            │
│  - Descubre herramientas disponibles                │
│  - Invoca tools según necesidad                     │
│  - Accede a resources                               │
│  - Usa prompt templates                             │
└─────────────────────────────────────────────────────┘
```

### 6.2 Tipos de Servidores MCP (Añadir MCPs de terceros)

#### 6.2.1 Tool Providers

Tool Providers exponen herramientas ejecutables que los agentes pueden invocar para realizar acciones o obtener información.
**Ver si dentro de FactorIA se puede incluir un catalogo de tools y MCP Servers*

#### 6.2.2 Resource Providers

**Tipos de Resources**:

- Knowledge Bases: Documentación, FAQs, procedimientos
- Databases: Acceso de solo lectura a datos estructurados
- File Repositories: Documentos, manuales, especificaciones
- APIs de Consulta: Endpoints read-only de sistemas externos

**Ver si dentro del FactorIA se puede incluir un catalogo de Resources*

**IMPORTANTE**
- Tools: Ejecutan acciones, pueden modificar estado
- Resources: Proporcionan información, solo lectura

### 6.3 Configuración y Despliegue

#### 6.3.1 Despliegue en la plataforma

Infraestructura base (bbdd, repos, etc..)
Aprovisamiento prompts se deberá realizar dentro de factorIA.


### 6.4 Seguridad y Validación de MCP Servers

#### 6.4.1 Checklist de Seguridad MCP

Antes de desplegar un MCP server en producción:

- [ ] Escaneo con mcp-scan completado
- [ ] Sin vulnerabilidades críticas
- [ ] Autenticación JWT implementada
- [ ] Validación de parámetros en todas las tools
- [ ] Rate limiting configurado
- [ ] Logging de todas las invocaciones
- [ ] Documentación de seguridad actualizada

#### 6.4.2 Catálogo de MCP Servers Seguros

**MCP Servers Aprobados:**

| Servidor | Versión | Última Auditoría | Estado |
|----------|---------|------------------|--------|
| sap-query-mcp | v2.1.0 | 2026-03-01 | ✅ Aprobado |
| documentation-mcp | v1.5.0 | 2026-02-28 | ✅ Aprobado |
| kb-search-mcp | v3.0.0 | 2026-03-05 | ✅ Aprobado |

**Proceso de Aprobación:**
1. Desarrollo y testing
2. Escaneo de seguridad (mcp-scan + Bandit + Semgrep)
3. Revisión de código
4. Aprobación de seguridad
5. Despliegue a producción
6. Monitorización continua

## 7. Organización y Nomenclatura

### 7.1 Convenciones de Nombres

**Decisiones de Nomenclatura**

#### 7.1.1 Estructura General de Recursos AWS

**Formato Estándar:**
```
{cuenta}-{caso_uso}-{recurso}
```

**Principios:**
- Balance entre claridad y brevedad
- Evitar limitaciones de caracteres de AWS
- NO incluir región (todo en Frankfurt)
- Códigos de caso de uso de 6 letras


**Ejemplos:**
```
comfact-gen-gesexp-lambda-processor
comfact-gen-bonsoc-ddb-trazas
nedgia-factgen-pintur-s3-artifacts
```

**Nota Importante:** Los logs de AWS tienen formato propio y no siguen esta nomenclatura (ej: `/aws/lambda/{nombre}`)

#### 7.1.2 Nomenclatura de Agentes

- **Formato simplificado**: `{caso_uso}-{nombre_agente}`
- **SIN prefijos** de cuenta/entorno para mayor legibilidad
- Códigos de caso de uso de 6 letras

**Formato de ID de Agente:**
```
{caso_uso}-{nombre_agente}
```

**Ejemplos:**
```
gesexp-chatbot
bonsoc-processor
pintur-analyzer
```

**Ventajas:**
- Nombres más cortos y comprensibles
- Más fácil de recordar y comunicar
- Ejemplo: `gesexp-chatbot` es más claro que `comfact-gen-dev-gesexp-chatbot`

#### 7.1.3 Códigos de Casos de Uso

- Códigos de **6 letras** fácilmente identificables
- Mantener catálogo centralizado

**Ejemplos de Códigos:**
- **GESEXP**: Gestión de Expedientes
- **BONSOC**: Bono Social
- **PINTUR**: Pintura (caso de uso de Nedgia)

**Acción Pendiente:** Crear y mantener catálogo completo de códigos de casos de uso

#### 7.1.4 Nomenclatura Detallada por Tipo de Recurso

**Lambda Functions:**
```
{cuenta}-{caso_uso}-{recurso}
Ejemplo: comfact-gen-gesexp-lambda-processor
```

**DynamoDB Tables:**
```
{cuenta}-{caso_uso}-{recurso}
Ejemplo: comfact-gen-gesexp-ddb-trazas
```

**S3 Buckets:**
```
{cuenta}-{caso_uso}-{recurso}
Ejemplo: comfact-gen-bonsoc-s3-artifacts
```

**RDS Instances:**
```
{cuenta}-{caso_uso}-{recurso}
Ejemplo: comfact-gen-factoria-rds-users
```

**IAM Roles:**
```
{cuenta}-{caso_uso}-{recurso}-role
Ejemplo: comfact-gen-gesexp-lambda-role
```

**IAM Policies:**
```
{cuenta}-{entorno}-{recurso}-policy
Ejemplo: comfact-gen-gesexp-lambda-policy
```

**CloudWatch Log Groups:**
```
/aws/{service}/{nombre}
Ejemplo: /aws/lambda/comfact-gen-dev-gesexp-lambda-processor
Nota: Formato estándar de AWS, no personalizable
```

**Decisión Importante:** NO incluir región en nomenclatura (todo en Frankfurt, incluir región añade ruido innecesario)

#### 7.1.5 Nomenclatura de Variables y Parámetros

**Variables de Entorno:**
```python
# Formato: SCREAMING_SNAKE_CASE
AGENT_ID = "lcs-com-gen-001"
AGENT_VERSION = "v2"
AWS_REGION = "eu-west-1"
DB_HOST = "prod-eu-west-1-agentcore-rds-users.xxx.rds.amazonaws.com"
MCP_SERVER_URL = "https://mcp.agentcore.internal"
LOG_LEVEL = "INFO"
```

**Parámetros de Configuración:**
```yaml
# Formato: kebab-case
agent-config:
  agent-id: lcs-com-gen-001
  max-tokens: 4096
  temperature: 0.7
  timeout-seconds: 300
  retry-attempts: 3
```
**Nombres de Funciones y Métodos:**
```python
# Formato: snake_case
def process_agent_request(event, context):
    pass

def validate_user_permissions(user_id, agent_id):
    pass

def call_mcp_server(server_url, tool_name, arguments):
    pass
```

**Nombres de Clases:**
```python
# Formato: PascalCase
class AgentOrchestrator:
    pass

class UserAuthenticationService:
    pass

class MCPServerClient:
    pass
```

### 7.2 Estructura de Repositorios

- **Monorepo por caso de uso** con estructura estándar
- Cohesión de código relacionado
- Facilita gestión de dependencias

#### 7.2.1 Estrategia Monorepo por Caso de Uso <--> Incluir piezas de logado y documentacion. <-->

**Estructura Estándar:**

```
{caso-de-uso}-repo/
├── .github/
│   └── workflows/
│       ├── ci-agents.yml
│       ├── cd-production.yml
│       └── security-scan.yml
├── agents/
│   ├── chatbot/
│   │   ├── src/
│   │   │   ├── handler.py
│   │   │   ├── agent.py
│   │   │   └── utils.py
│   │   ├── tests/
│   │   │   ├── test_handler.py
│   │   │   └── test_agent.py
│   │   ├── config/
│   │   │   ├── dev.yaml
│   │   │   └── prod.yaml
│   │   ├── requirements.txt
│   │   └── README.md
│   └── processor/
├── infrastructure/
│   ├── terraform/
│   │   ├── modules/
│   │   │   ├── agent/
│   │   │   ├── dynamodb/
│   │   │   └── s3/
│   │   ├── environments/
│   │   │   ├── dev/
│   │   │   └── prod/
│   │   └── main.tf
│   └── boto3-scripts/
│       ├── deploy_agent.py
│       ├── deploy_prompt.py
│       └── deploy_mcp.py
├── mcp-servers o tools/
│   ├── custom-tool-1/
│   │   ├── src/
│   │   ├── tests/
│   │   └── README.md
│   └── custom-tool-2/
├── shared/
│   ├── utils/
│   ├── config/
│   └── constants.py
├── docs/
│   ├── architecture/
│   ├── api/
│   ├── runbooks/
│   └── adr/
├── scripts/
│   ├── deploy.sh
│   ├── rollback.sh
│   └── test.sh
├── .gitignore
├── README.md
└── CONTRIBUTING.md
```

**Nota Importante:** CloudFormation se va a deprecar para gestión de prompts/agentes/MCPs, usar Boto3 para realizarlo de manera mas agil

#### 7.2.2 Branching Strategy

- **Git Flow** como estrategia de ramas
- Estándar de industria, bien conocido por los equipos

**GitFlow:**

```
main (production)
  ├── develop (integration)
  │   ├── feature/{descripcion}
  │   ├── fix/{descripcion}
  │   └── hotfix/{descripcion}
  └── release/v{major}.{minor}.{patch}
```

**Reglas de Branches:**

1. **main**: Solo código en producción
   - Protegido, requiere PR review
   - CI/CD automático a producción
   - Tags para cada release

2. **develop**: Integración continua
   - Código estable para siguiente release
   - Deploy automático a Dev

3. **feature/**: Nuevas funcionalidades
   - Formato: `feature/{descripcion}`
   - Merge a develop via PR

4. **fix/**: Corrección de bugs
   - Formato: `fix/{descripcion}`
   - Merge a develop via PR

5. **hotfix/**: Correcciones urgentes en producción
   - Formato: `hotfix/{descripcion}`
   - Merge a main y develop

6. **release/**: Preparación de releases
   - Formato: `release/v{major}.{minor}.{patch}`
   - Testing final antes de producción

**Entornos:**
- **Local**: Desarrollo local
- **Dev**: Entorno de desarrollo (desde develop)
- **Pro**: Entorno de producción (desde main)
- **NO hay staging/integración** (decisión de simplificación)

### 7.3 Versionado

- **Versionado semántico (X.Y.Z)** para agentes
- Estándar de industria para versionado

#### 7.3.1 Versionado Semántico

**Formato:** `v{MAJOR}.{MINOR}.{PATCH}`

**Reglas:**
- **MAJOR (X)**: Cambios incompatibles en API (breaking changes)
- **MINOR (Y)**: Nueva funcionalidad compatible hacia atrás
- **PATCH (Z)**: Correcciones de bugs

**Ejemplos:**
```
v1.0.0 - Release inicial
v1.1.0 - Nueva funcionalidad: soporte multi-idioma
v1.1.1 - Fix: corrección de timeout
v2.0.0 - Breaking change: nueva estructura de API
```

**Gestión de Versiones:**
- Gestión clara de cambios breaking/features/fixes
- Tags en Git para cada versión
- Changelog documentado

#### 7.3.2 Versionado de Agentes

**Metadata de Versión:**

**EJEMPLO:**

```python
AGENT_METADATA = {
    "agent_id": "lcs-com-gen-001",
    "version": "v2.1.0",
    "name": "Asistente de Resolución de Tickets SAP",
    "description": "Analiza y propone resoluciones para tickets SAP",
    "created_at": "2024-01-15T10:00:00Z",
    "updated_at": "2024-03-20T14:30:00Z",
    "author": "AgentCore Team",
    "status": "active",
    "capabilities": [
        "ticket_analysis",
        "solution_recommendation",
        "knowledge_base_search"
    ],
    "dependencies": {
        "mcp_servers": ["sap-mcp-server", "documentation-mcp-server"],
        "models": ["claude-3-sonnet-20240229"],
        "min_python_version": "3.11"
    },
    "changelog": {
        "v2.1.0": "Added multi-language support",
        "v2.0.0": "Migrated to Claude 3 Sonnet",
        "v1.0.0": "Initial release"
    }
}
```

### 7.4 Tagging Strategy en AWS

- **Tags obligatorios**: UseCase, Project, Environment, Owner, CostCenter
- Estándares de Kyndryl + necesidades de FactorIA
- Todos los recursos deben tener estos tags para trazabilidad y costos

#### 7.4.1 Tags Obligatorios

```yaml
Tags:
  # Tags Obligatorios (Kyndryl + FactorIA)
  - Key: UseCase
    Value: {codigo_6_letras}  # GESEXP, BONSOC, PINTUR, etc.
  
  - Key: Project
    Value: factoria
  
  - Key: Environment
    Value: dev|pro
  
  - Key: Owner
    Value: {email_responsable}
  
  - Key: CostCenter
    Value: {centro_costos}
  
  # Tags Opcionales pero Recomendados
  - Key: Business
    Value: comercializadora|nedgia|corporacion
  
  - Key: Version
    Value: v{X}.{Y}.{Z}
  
  - Key: ManagedBy
    Value: terraform|boto3|manual
  
  - Key: DataClassification
    Value: confidential|internal|public
  
  - Key: BackupPolicy
    Value: daily|weekly|none
  
  - Key: DisasterRecovery
    Value: critical|high|medium|low
```

**Ejemplo Completo:**
```yaml
UseCase: gesexp
Project: factoria
Environment: pro
Owner: equipo-gesexp@naturgy.com
CostCenter: IT-AI-001
Business: comercializadora
Version: v2.1.0
ManagedBy: terraform
```


#### 7.4.2 Gestión de Tags

- **Migración de CloudFormation a Boto3** para gestión de prompts/agentes/MCPs
- CloudFormation es lento para actualizaciones frecuentes
- Boto3 es más ágil para cambios iterativos

**Gestión de Recursos:**
- **Infraestructura base**: Terraform (roles, security groups, DBs, ECR)
- **Agentes/Prompts/MCPs**: FactorIA (web UI) con Boto3
- **Tags aplicados automáticamente** desde FactorIA al desplegar

**Ejemplo de Despliegue con Boto3:**
```python
import boto3

bedrock = boto3.client('bedrock-agent')

# Crear agente con tags
response = bedrock.create_agent(
    agentName='gesexp-chatbot',
    foundationModel='anthropic.claude-3-sonnet-20240229-v1:0',
    instruction='...',
    tags={
        'UseCase': 'gesexp',
        'Project': 'factoria',
        'Environment': 'pro',
        'Owner': 'equipo-gesexp@naturgy.com',
        'CostCenter': 'IT-AI-001'
    }
)
```

### 7.5 Documentación

#### 7.5.1 Architecture Decision

**Plantilla:**

```markdown
# AXX-001: Uso de Serverless para Agentes

## Estado
Aceptado

## Contexto
La arquitectura de ejecución para los agentes.
Opciones: Serverless AgentCore, Containerizado (ECS/EKS) dependiente de caso de uso

## Decisión
Usar AWS Agent Core (serverless) como plataforma principal para agentes.

## Consecuencias

### Positivas
- Escalabilidad automática
- Pago por uso
- Menor complejidad operacional
- Integración nativa con AWS

### Negativas
- Cold start latency (1-3s)
- Límite de 15 minutos de ejecución
- Límite de 10GB de memoria

## Alternativas Consideradas
1. ECS Fargate: Mayor control, mayor costo
2. EKS: Máxima flexibilidad, alta complejidad

## Fecha
2024-01-15

## Autores
- Pepito Perez (Arquitecto)
- Ana Rosa (Tech Lead)
```

#### 7.5.2 README Template para Agentes

```markdown
# lcs-com-gen-001: Asistente de Resolución de Tickets SAP

## Descripción
Agente que analiza tickets SAP y propone soluciones basadas en 
patrones históricos y base de conocimiento.

## Versión
v2.1.0

## Capacidades
- Análisis de tickets SAP
- Búsqueda en base de conocimiento
- Recomendación de soluciones
- Generación de pasos de resolución

## Requisitos
- Python 3.11+
- AWS Lambda Runtime
- Acceso a MCP Servers: sap-mcp-server, documentation-mcp-server

## Configuración

### Ejemplo Variables de Entorno
```bash
AGENT_ID=lcs-com-gen-001
AGENT_VERSION=v2.1.0
MODEL_ID=anthropic.claude-3-sonnet-20240229-v1:0
MAX_TOKENS=4096
TEMPERATURE=0.7
MCP_SERVER_URL=https://mcp.agentcore.internal
```

### Permisos IAM Requeridos
- bedrock:InvokeModel
- dynamodb:GetItem, PutItem
- s3:GetObject, PutObject
- secretsmanager:GetSecretValue

## Uso

### Invocación Directa
```python
import boto3

lambda_client = boto3.client('lambda')

response = lambda_client.invoke(
    FunctionName='prod-eu-west-1-agentcore-lambda-lcs-com-gen-001-v2',
    InvocationType='RequestResponse',
    Payload=json.dumps({
        'ticket_id': 'INC0012345',
        'description': 'Error en transacción MM01',
        'user_id': 'user@company.com'
    })
)
```

### Via API
```bash
curl -X POST https://api.agentcore.company.com/v1/agents/lcs-com-gen-001/invoke \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "ticket_id": "INC0012345",
    "description": "Error en transacción MM01"
  }'
```

## Desarrollo

### Setup Local
```bash
# Crear entorno virtual
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# Instalar dependencias
pip install -r requirements.txt
pip install -r requirements-dev.txt

# Ejecutar tests
pytest tests/

# Ejecutar localmente
python -m src.handler
```

### Testing
```bash
# Unit tests
pytest tests/unit/

# Integration tests
pytest tests/integration/

# Coverage
pytest --cov=src tests/
```

## Deployment

### Manual
```bash
./scripts/deploy.sh lcs-com-gen-001 prod
```

### CI/CD
El deployment automático se ejecuta al hacer merge a main.

## Monitorización

### Métricas Clave
- Invocations: Número de invocaciones
- SuccessRate: Tasa de éxito
- AvgResolutionTime: Tiempo promedio de resolución
- UserSatisfaction: CSAT score

### Dashboards
- [Technical Dashboard](https://console.aws.amazon.com/cloudwatch/dashboards/lcs-com-gen-001-tech)
- [Business Dashboard](https://console.aws.amazon.com/cloudwatch/dashboards/lcs-com-gen-001-business)

### Alertas
- High Error Rate (> 5%)
- High Latency (p95 > 3s)
- Low Success Rate (< 90%)

## Troubleshooting

### Error: Timeout
**Causa**: Procesamiento excede 5 minutos
**Solución**: Aumentar timeout en configuración Lambda

### Error: Rate Limit Exceeded
**Causa**: Demasiadas invocaciones concurrentes
**Solución**: Aumentar límite de concurrencia

## Changelog

### v2.1.0 (2024-03-20)
- Added: Soporte multi-idioma (ES, EN, DE)
- Improved: Precisión de recomendaciones (+15%)
- Fixed: Timeout en tickets complejos

### v2.0.0 (2024-02-15)
- Breaking: Migración a Claude 3 Sonnet
- Added: Integración con nueva base de conocimiento
- Removed: Soporte para Claude 2

### v1.0.0 (2024-01-15)
- Initial release

## Contacto
- Team: agentcore-team@company.com
- Slack: #agentcore-support
- Wiki: https://wiki.company.com/agentcore
```

## 8. Ciclo de Vida de un Agente

### 8.1 Fases del Ciclo de Vida

- **Entornos**: Local, Dev, Pro (sin staging/integración)
- Simplificar pipeline, dos entornos son suficientes

```
┌─────────────────────────────────────────────────────────┐
│                 CICLO DE VIDA DEL AGENTE                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. LOCAL (Desarrollo Local)                            │
│     ├── Diseño                                          │
│     ├── Implementación                                  │
│     ├── Testing Local                                   │
│     └── Code Review                                     │
│                    ↓                                    │
│  2. DESARROLLO (Entorno Dev)                            │
│     ├── Merge a develop                                 │
│     ├── CI Pipeline                                     │
│     ├── Integration Tests                               │
│     │       ├── Pruebas de estrés                       │
│     │       ├── Test Data Sets                          │
│     │       └── High Volume Testing                     │
│     ├── Security Scan                                   │
│     └── Despliegue automático a Dev                     │
│                    ↓                                    │
│  3. PRODUCCIÓN (Entorno Pro)                            │
│     ├── Merge a main                                    │
│     ├── Deploy a Producción                             │
│     ├── Smoke Tests                                     │
│     ├── Monitoring                                      │
│     └── Gradual Rollout (si aplica)                     │
│                    ↓                                    │
│  4. OPERACIÓN                                           │
│     ├── Monitorización Continua                         │
│     ├── Incident Response                               │
│     ├── Performance Tuning                              │
│     └── User Feedback                                   │
│                    ↓                                    │
│  5. MANTENIMIENTO                                       │
│     ├── Bug Fixes                                       │
│     ├── Security Patches                                │
│     ├── Dependency Updates                              │
│     └── Model Updates                                   │
│                    ↓                                    │
│  6. EVOLUCIÓN                                           │
│     ├── New Features                                    │
│     ├── Optimization                                    │
│     ├── Prompt Tuning                                   │
│     └── Version Upgrade                                 │
│                    ↓                                    │
│  7. RETIRO (Eventual)                                   │
│     ├── Deprecation Notice                              │
│     ├── Migration Plan                                  │
│     ├── Data Archival                                   │
│     └── Decommission                                    │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Flujo Simplificado:**
- Desarrollo local → Dev → Pro
- Sin paso intermedio de staging/integración
- Dos entornos son suficientes para el alcance actual

### 8.2 Desarrollo

#### 8.2.1 Setup de Entorno Local

**Herramientas Requeridas:**
```bash
# Python
python 3.11+
pip
virtualenv

# AWS
aws-cli
sam-cli (para testing local de Lambda)
terraform o cloudformation

# Containers
docker
docker-compose

# Testing
pytest
localstack (para emular servicios AWS)

# Code Quality
black (formatter)
pylint (linter)
mypy (type checker)
bandit (security)
```

**Configuración Inicial:**
```bash
# Clonar repositorio
git clone https://github.com/company/agentcore.git
cd agentcore

# Crear entorno virtual
python -m venv venv
source venv/bin/activate

# Instalar dependencias
pip install -r requirements.txt
pip install -r requirements-dev.txt

# Configurar pre-commit hooks
pre-commit install

# Configurar AWS credentials
aws configure --profile agentcore-dev

# Iniciar servicios locales
docker-compose up -d
```

#### 8.2.2 Testing Local

**Unit Tests:**
- Pruebas diseñadas para validar funcionalidades específicas de componentes individuales.
- Enfoque: Garantizar que cada módulo funcione de forma aislada y cumpla con los requisitos establecidos.

**Integration Tests:**
- Validación de la interacción entre múltiples componentes o servicios.
- Enfoque: Asegurar que los sistemas integrados funcionen correctamente y que los datos fluyan como se espera.


### 8.3 CI/CD Pipeline

#### 8.3.1 GitHub Actions Workflow

**Repositorio específico para cada caso de uso:**
- Cada proyecto o caso de uso debe contar con su propio repositorio en GitHub.
- Configuración de workflows en **GitHub Actions**:
  - **Build**: Compilación y validación de código.
  - **Test**: Ejecución de pruebas unitarias e integraciones.
  - **Deploy**: Automatización del despliegue hacia entornos específicos (ej.:  producción).

#### 8.3.1.1 Security Scanning en CI/CD Pipeline

**Herramientas de Seguridad Integradas:**

1. **Bandit** - Análisis de seguridad para Python
2. **SonarQube/SonarCloud** - Análisis de calidad y seguridad de código
3. **Safety** - Verificación de vulnerabilidades en dependencias
4. **Trivy** - Escaneo de imágenes Docker (si aplica)
5. **mcp-scan** - Escaneo específico de servidores MCP


┌─────────────────────────────────────────────────────────────┐
│                    DEVELOPER PUSH CODE                      │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│              PRE-COMMIT HOOKS (Local)                       │
│  • Black (formatting)                                       │
│  • Pylint (linting)                                         │
│  • Bandit (security - quick scan)                           │
└────────────────────┬────────────────────────────────────────┘
                     │ ✅ Pass
                     ▼
┌────────────────────────────────────────────────────────────┐
│              GITHUB ACTIONS - CI PIPELINE                  │
│                                                            │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ STAGE 1: BUILD & TEST                               │   │
│  │  • Install dependencies                             │   │
│  │  • Run unit tests                                   │   │
│  │  • Generate coverage report                         │   │
│  └─────────────────────────────────────────────────────┘   │
│                     ↓ ✅ Pass                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ STAGE 2: SECURITY SCANS (PARALLEL)                  │   │
│  │                                                     │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────┐   │   │
│  │  │   BANDIT     │  │  SONARQUBE   │  │  SAFETY  │   │   │
│  │  │ (Python sec) │  │ (Code qual.) │  │  (Deps)  │   │   │
│  │  └──────────────┘  └──────────────┘  └──────────┘   │   │
│  │                                                     │   │
│  │  ❌ FAIL si:                                       │   │
│  │  • Vulnerabilidades HIGH/CRITICAL (Bandit)          │   │
│  │  • Quality Gate FAILED (SonarQube)                  │   │
│  │  • Vulnerabilidades en dependencias (Safety)        │   │
│  └─────────────────────────────────────────────────────┘   │
│                     ↓ ✅ All Pass                          │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ STAGE 3: INTEGRATION TESTS                          │   │
│  │  • Integration tests                                │   │
│  │  • E2E tests                                        │   │
│  └─────────────────────────────────────────────────────┘   │
│                     ↓ ✅ Pass                              │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ STAGE 4: CONTAINER SCAN (if applicable)             │   │
│  │  • Build Docker image                               │   │
│  │  • Trivy scan                                       │   │
│  └─────────────────────────────────────────────────────┘   │
└────────────────────┬────────────────────────────────────────┘
                     │ ✅ All Stages Pass
                     ▼
┌─────────────────────────────────────────────────────────────┐
│              READY FOR MERGE / DEPLOYMENT                   │
└─────────────────────────────────────────────────────────────┘


---

#### 8.3.2 Deployment Strategy

- **GitHub Actions** como herramienta principal para automatizar despliegues.
- Estrategias recomendadas:
  - **Rolling Deployment**: Actualización gradual de instancias para minimizar interrupciones.
  - **Canary Deployment**: Liberación de cambios a un subconjunto de usuarios antes de un despliegue completo.

**Consideraciones:**
- Asegurar que los despliegues sean reversibles en caso de fallos.
- Integrar herramientas de validación post-despliegue para verificar la estabilidad del entorno.

---

### 8.3.3 Monitorización Post-Deployment

- **FactorIA**: Solución para monitoreo y análisis de rendimiento.
- Herramientas recomendadas según el caso:
  - **AWS CloudWatch**: Monitoreo de recursos en entornos AWS.

**Enfoque:**
- Supervisar el rendimiento, disponibilidad y errores en los sistemas desplegados.
- Configurar alertas proactivas para detectar problemas antes de que impacten a los usuarios.
- Realizar revisiones periódicas para optimizar los recursos y garantizar la estabilidad del sistema.

---

## 9. Casos de Uso 

### 9.1 Ejemplo Caso de uso: Asistente de Resolución de Tickets SAP

#### 9.1.1 Descripción

Agente especializado en analizar tickets de soporte SAP y proponer soluciones basadas en patrones históricos y base de conocimiento.

#### 9.1.2 Arquitectura

```
┌─────────────────────────────────────────────────────────┐
│                    Usuario/Técnico                      │
└────────────────────┬────────────────────────────────────┘
                     │ Ticket: "Error MM01"
                     ▼
┌─────────────────────────────────────────────────────────┐
│              Load Balancer + Authorizer                 │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌────────────────────────────────────────────────────────┐
│         Lambda: lcs-com-gen-001 (Ticket Agent)         │
│  ┌──────────────────────────────────────────────────┐  │
│  │  1. Clasificación de Ticket                      │  │
│  │     - Módulo SAP (MM, SD, FI, etc.)              │  │
│  │     - Severidad (Critical, High, Medium, Low)    │  │
│  │     - Tipo (Error, Question, Request)            │  │
│  └──────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────┐  │
│  │  2. Búsqueda en Knowledge Base                   │  │
│  │     - Vector search en embeddings                │  │
│  │     - Tickets similares resueltos                │  │
│  │     - Documentación SAP relevante                │  │
│  └──────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────┐  │
│  │  3. Análisis con LLM                             │  │
│  │     - Contexto del ticket                        │  │
│  │     - Soluciones históricas                      │  │
│  │     - Generación de recomendaciones              │  │
│  └──────────────────────────────────────────────────┘  │
└────────────────────┬───────────────────────────────────┘
                     │
        ┌────────────┴────────────┐
        │                         │
        ▼                         ▼
┌──────────────┐         ┌──────────────┐
│ SAP MCP      │         │ Vector DB    │
│ Server       │         │ (OpenSearch) │
│ - Query SAP  │         │ - Embeddings │
│ - Get logs   │         │ - Similarity │
└──────────────┘         └──────────────┘
```

#### 9.1.3 Implementación

Implementar una ingesta en el gestor documental de factorIA, documentacion formato Markdown. Incluyendo imagenes que estan almacenados en S3.


**Objetivo:** implementar la **ingesta de documentación en el gestor documental de factorIA** en formato **Markdown**, incluyendo **imágenes almacenadas en S3**.

---

**Alcance**
- Documentación técnica/funcional en **Markdown** (`.md`).
- Imágenes (`.png/.jpg/.svg`) alojadas en **Amazon S3**.
- Publicación/actualización automática en el gestor documental de **factorIA**.

---


#### 9.1.4 Métricas de Éxito

| Métrica | Objetivo | Actual |
|---------|----------|--------|
| Tasa de Resolución Automática | >40% | 45% |
| Tiempo Medio de Resolución | < 2 horas | 1.5 horas |
| Satisfacción del Usuario (CSAT) | >4.0/5.0 | 4.3/5.0 |
| Precisión de Recomendaciones | >80% | 85% |
| Reducción de Escalamiento | >30% | 35% |

---

### 9.2 Plantillas de Configuración

Este apartado define los **estándares de plantillas** para desplegar y configurar componentes del caso de uso, priorizando reutilización, trazabilidad y consistencia entre entornos.

---

#### 9.2.1 CloudFormation Templates

**Estado:** *Uso limitado* ⚠️  
- El uso de CloudFormation queda limitado a componentes legacy o dependencias existentes.
- **Nota:** en el modelo actual, **Agents, MCPs y prompts** se gestionan fuera de CloudFormation (pendiente de estandar final).

**Criterio:** solo se aceptará CloudFormation si existe necesidad técnica/organizativa (p. ej. integraciones existentes o restricciones del entorno).

---

#### 9.2.2 Terraform Module – Agente (estándar Naturgy) ✅

**Terraform será el estándar** de despliegue y configuración para Naturgy, mediante **módulos reutilizables** por caso de uso.

**Contenido típico del módulo**
- Recursos de cómputo (según arquitectura): ECS/EKS/Lambda/EC2 (según corresponda).
- IAM (roles/policies) con mínimo privilegio.
- Networking (VPC/subnets/SG) o integración con red existente.
- Observabilidad: logs/métricas/alertas (CloudWatch u otras herramientas corporativas).
- Parámetros/secretos: SSM Parameter Store / Secrets Manager.
- Tags corporativos obligatorios (owner, entorno, coste, criticidad).

**Estructura recomendada**
- Repositorio IaC por caso de uso o monorepo con módulos:
  - `modules/agent/`
  - `envs/dev/`, `envs/pre/`, `envs/pro/`

**Buenas prácticas**
- Variables por entorno y control de cambios vía PR.
- `terraform plan` + aprobaciones antes de aplicar.
- Estado remoto con control de acceso y versionado (según estándar del equipo).


### 9.3 Checklists

#### 9.3.1 Pre-Deployment Checklist

```markdown
# Pre-Deployment Checklist

## Código
- [ ] Todos los tests unitarios pasan
- [ ] Tests de integración ejecutados exitosamente
- [ ] Cobertura de código > 80%
- [ ] Code review completado y aprobado (o Sonar) -> Pendiente de definicion.
- [ ] Sin vulnerabilidades críticas (Bandit, Safety)
- [ ] Linting sin errores (Pylint, Black)
- [ ] Type checking sin errores (Mypy)

## Configuración
- [ ] Variables de entorno configuradas
- [ ] Secrets en Secrets Manager
- [ ] Permisos IAM revisados y mínimos
- [ ] Tags AWS aplicados correctamente
- [ ] Límites de concurrencia configurados
- [ ] Timeouts apropiados

## Documentación
- [ ] README actualizado
- [ ] CHANGELOG actualizado
- [ ] API documentation generada
- [ ] Runbooks actualizados
- [ ] ADRs documentados

## Monitorización
- [ ] Dashboards creados
- [ ] Alertas configuradas
- [ ] Logs estructurados implementados
- [ ] Tracing habilitado
- [ ] Métricas custom definidas

## Seguridad
- [ ] Scan de seguridad ejecutado
- [ ] Dependencias actualizadas
- [ ] Secrets rotados
- [ ] Cifrado habilitado
- [ ] WAF configurado

## Testing
- [ ] Smoke tests preparados
- [ ] Performance tests ejecutados
- [ ] Load tests completados
- [ ] Security tests realizados
- [ ] Rollback plan probado

## Comunicación
- [ ] Stakeholders notificados
- [ ] Ventana de mantenimiento comunicada
- [ ] Equipo de soporte informado
- [ ] Documentación de usuario actualizada
```

#### 9.3.2 Security Review Checklist

```markdown
# Security Review Checklist

>## Controles Bloqueantes (DEBEN estar completos)
>- [ ] Agent Controller implementado y operativo
>- [ ] Bedrock Guardrails configurado (PROMPT_ATTACK, PII, TOXICITY)
>- [ ] Tool Authorization Layer implementado
>- [ ] Parameter Validation en todos los MCP tools
>- [ ] Rate Limiting configurado (usuario y agente)
>- [ ] Circuit Breaker implementado
>- [ ] Kill Switch global funcional
>- [ ] Human-in-the-Loop para acciones críticas
>- [ ] PII Detection habilitado
>- [ ] Suite de tests de seguridad ejecutada

## Autenticación y Autorización
- [ ] Autenticación delegada en Entra ID + Cognito
- [ ] Tokens JWT con expiración apropiada (< 1 hora)
- [ ] Scopes validados en Agent Controller
- [ ] Principio de menor privilegio aplicado
- [ ] Rotación de credenciales configurada

## Cifrado
- [ ] TLS 1.3 en todas las comunicaciones
- [ ] Datos en reposo cifrados (KMS)
- [ ] Secrets en Secrets Manager
- [ ] Certificados válidos y renovables

## Input Validation
- [ ] Validación de entrada implementada
- [ ] Sanitización de datos
- [ ] Protección contra injection
- [ ] Rate limiting configurado
- [ ] Tamaño máximo de payload definido

## OWASP Top 10 LLM
- [ ] Protección contra prompt injection
- [ ] Validación de output
- [ ] PII detection y redacción
- [ ] Control de acciones críticas
- [ ] Auditoría completa

## Compliance
- [ ] GDPR compliance verificado
- [ ] Retención de datos configurada
- [ ] Derecho al olvido implementado
- [ ] Audit logs habilitados
- [ ] Data classification aplicada

## Monitorización de Seguridad
- [ ] GuardDuty habilitado
- [ ] Security Hub configurado
- [ ] CloudTrail logging activo
- [ ] Alertas de seguridad definidas
- [ ] Incident response plan documentado
```

#### 9.3.3 Go-Live Checklist

```markdown
# Go-Live Checklist

## Pre-Go-Live (T-24h) Implementar documentos acorde al estandar E280.
- [ ] Backup de configuración actual
- [ ] Rollback plan validado
- [ ] Equipo de soporte en standby
- [ ] Comunicación a usuarios enviada
- [ ] Ventana de mantenimiento confirmada

## Go-Live (T-0)
- [ ] Deployment iniciado
- [ ] Métricas baseline capturadas
- [ ] Logs monitoreados
- [ ] Primeras transacciones verificadas

## Post-Go-Live (T+1h)
- [ ] Error rate < 1%
- [ ] Latencia dentro de SLA
- [ ] No alertas críticas
- [ ] Feedback de usuarios positivo
- [ ] Capacidad suficiente

## Post-Go-Live (T+24h)
- [ ] Métricas de negocio normales
- [ ] Sin incidentes reportados
- [ ] Performance estable
- [ ] Costos dentro de presupuesto
- [ ] Documentación actualizada

## Cierre
- [ ] Retrospectiva completada
- [ ] Lecciones aprendidas documentadas
- [ ] Métricas de éxito reportadas
- [ ] Stakeholders informados
```

## 10. Evaluación de Calidad de Modelos del caso de uso

### 10.1 Framework de Evaluación

**Objetivo:** evaluar el comportamiento del caso de uso de extremo a extremo y **cerrar las diferencias** entre el resultado esperado y el observado mediante **métricas acordes al caso de uso**.

#### Alcance de la evaluación
- **Calidad funcional**: el sistema cumple los requisitos y reglas de negocio.
- **Calidad del resultado** (si aplica IA/analítica): precisión, cobertura, consistencia.
- **Rendimiento**: latencia, throughput, tiempos de procesamiento.
- **Fiabilidad**: errores, reintentos, estabilidad bajo carga.
- **Coste**: consumo de recursos y coste por ejecución/transacción.


#### Métricas (definidas por caso de uso)
- **KPIs de negocio**: métricas que validan el valor (p. ej., tasa de éxito del proceso, reducción de tiempos, % automatización).
- **KPIs técnicos**:
  - **Disponibilidad** (SLA/SLO)
  - **Tasa de error** (4xx/5xx, fallos de jobs)
  - **Latencia** (p50/p95/p99)
  - **Tiempo de ciclo** (end-to-end)
- **KPIs de datos / calidad**:
  - completitud, duplicados, consistencia, frescura (data freshness)
- **KPIs de seguridad/compliance** (si aplica):
  - eventos críticos, accesos no autorizados, cumplimiento de cifrado/logging

#### Metodología de “cierre de diferencias” (gap closing)
1. **Definir baseline**: valor inicial de métricas antes del cambio o primera versión.
2. **Definir objetivo**: umbrales acordados (targets) y criterio de aceptación.
3. **Medir y comparar**: ejecución controlada + registro de resultados.
4. **Analizar desviaciones**: identificar causa (código, datos, configuración, rendimiento).
5. **Plan de acciones**: remediaciones priorizadas con responsable y fecha.
6. **Re-test**: repetir medición hasta cumplir umbrales o documentar aceptación del riesgo.
7. **Evidencia**: informe breve con resultados, decisiones y versión evaluada.


### 10.2 Human Evaluation

**Objetivo:** complementar las métricas automáticas con una evaluación humana estructurada para validar **calidad, utilidad y alineamiento con negocio** del caso de uso.

#### Cuándo aplica
- Casos de uso con **salida no determinista** (IA/LLMs, NLP, clasificación, extracción).
- Procesos con impacto operativo donde se requiere validación de **criterios cualitativos**.
- Validación de **UX/contenido** (claridad, completitud, acción recomendada).

#### Roles involucrados
- **Subject Matter Experts (SME)**: validación funcional y de negocio.
- **Owner del caso de uso**: aprueba criterios y umbrales.
- **Equipo técnico**: prepara datasets, guía y consolida resultados.



## 11. Backup (Parte delegada en Kyndryl – AWS Backup)

El servicio de backup se implementa y opera mediante **AWS Backup**, con ejecución y operación delegada en **Kyndryl**.

### 11.1 Objetivo
Garantizar la **recuperación de datos y configuraciones** ante borrados accidentales, corrupción lógica o fallos operativos, con trazabilidad y evidencias para auditoría.

### 11.2 Responsabilidades (modelo operativo)
- **Kyndryl**:
  - Configuración y operación de **AWS Backup Plans/Vaults**, scheduling, retenciones, copias cross-region (si aplica).
  - Monitorización de jobs, alertas, remediación y reporting.
  - Ejecución de restauraciones bajo solicitud/cambio controlado.


#### 11.2.1 Procedimientos de Restauración (pendiente de petición a Kyndryl)

**Estado:** en definición. Se abrirá petición a **Kyndryl** para confirmar el procedimiento operativo (SOP), tiempos de ejecución y responsables.  
**Nota:** el equipo del caso de uso mantendrá **scripts propios** para ejecutar/validar restauraciones y reducir tiempos de recuperación.

---

**Dependencias con Kyndryl**
- Confirmación del **método de solicitud** (ticket/ITSM), SLAs y ventana operativa.
- Acceso y permisos para ejecutar **Restore Jobs** en **AWS Backup** (o ejecución delegada).
- Procedimiento estándar para:
  - restauración en **entorno alternativo** (staging/DR)
  - restauración **in-place** (si aplica)
  - evidencias y validación post-restore

---

#### Ejemplos de restauración

**Restauración de RDS:**
```bash
# 1. Identificar snapshot a restaurar
aws rds describe-db-snapshots \
  --db-instance-identifier prod-agentcore-rds-users

# 2. Restaurar desde snapshot
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier prod-agentcore-rds-users-restored \
  --db-snapshot-identifier rds:prod-agentcore-rds-users-2026-03-04-03-00

# 3. Verificar estado
aws rds describe-db-instances \
  --db-instance-identifier prod-agentcore-rds-users-restored

# 4. Actualizar DNS/endpoints si es necesario
```

**Restauración de DynamoDB:**
```bash
# Restauración point-in-time
aws dynamodb restore-table-to-point-in-time \
  --source-table-name prod-agentcore-ddb-sessions \
  --target-table-name prod-agentcore-ddb-sessions-restored \
  --restore-date-time 2026-03-04T10:00:00Z
```

**Restauración de S3:**
```bash
# Restaurar versión específica de objeto
aws s3api get-object \
  --bucket prod-agentcore-artifacts \
  --key agent-config/lcs-com-gen-001.yaml \
  --version-id <version-id> \
  agent-config-restored.yaml
```

#### 11.3.1 Ventanas de Mantenimiento 

**Programación:**
Depende del equipo del caso de uso.

**Procedimiento: Tentativa**
1. Notificación a usuarios
2. Backup completo pre-mantenimiento
3. Despliegue en staging y validación
4. Despliegue en producción con blue/green
5. Smoke tests post-despliegue
6. Monitorización intensiva 24h
7. Comunicación de finalización

#### 11.3.2 Actualizaciones Sin Downtime

**Estrategias:**
- **Blue/Green Deployment**: Para actualizaciones de agentes
- **Rolling Updates**: Para actualizaciones de infraestructura
- **Canary Releases**: Para cambios de alto riesgo
- **Feature Flags**: Para activación gradual de funcionalidades

**Rollback Automático:**
- Detección de error rate > 5%
- Latencia p95 > 2x baseline
- Rollback automático en < 5 minutos


## 12. Apéndices

### 12.1 Glosario de Términos

**A**
- **ABAC**: Attribute-Based Access Control - Control de acceso basado en atributos
- **ADR**: Architecture Decision Record - Registro de decisiones arquitectónicas
- **Agent**: Entidad autónoma que ejecuta tareas específicas usando IA
- **API Gateway**: Servicio AWS que gestiona APIs RESTful y WebSocket

**B**
- **Bedrock**: Servicio AWS para modelos de lenguaje (LLM)
- **Blue/Green Deployment**: Estrategia de deployment con dos entornos paralelos

**C**
- **CI/CD**: Continuous Integration/Continuous Deployment
- **CloudWatch**: Servicio de monitorización de AWS
- **Cognito**: Servicio de autenticación y autorización de AWS
- **Correlation ID**: Identificador único para rastrear solicitudes

**D**
- **DLQ**: Dead Letter Queue - Cola para mensajes fallidos
- **DynamoDB**: Base de datos NoSQL de AWS

**E**
- **Entra ID**: Servicio de identidad de Microsoft (anteriormente Azure AD)
- **EventBridge**: Servicio de bus de eventos de AWS

**G**
- **GDPR**: General Data Protection Regulation
- **GuardDuty**: Servicio de detección de amenazas de AWS

**I**
- **IAM**: Identity and Access Management
- **ISO 27001**: Estándar internacional de seguridad de la información

**K**
- **KMS**: Key Management Service - Servicio de gestión de claves de AWS

**L**
- **Lambda**: Servicio serverless de AWS para ejecutar código
- **LLM**: Large Language Model - Modelo de lenguaje grande

**M**
- **MCP**: Model Context Protocol - Protocolo para integración de herramientas
- **mTLS**: Mutual TLS - Autenticación TLS bidireccional

**O**
- **OpenTelemetry**: Framework de observabilidad open source
- **OWASP**: Open Web Application Security Project

**R**
- **RBAC**: Role-Based Access Control
- **RDS**: Relational Database Service de AWS

**S**
- **SAML**: Security Assertion Markup Language
- **Serverless**: Arquitectura sin gestión de servidores
- **SQS**: Simple Queue Service de AWS

**T**
- **TLS**: Transport Layer Security

**W**
- **WAF**: Web Application Firewall

**Z**
- **Zero Trust**: Arquitectura de seguridad que no confía en ninguna entidad por defecto

---

### Recursos Adicionales

- **Repositorio**: 
- **Documentación**: 
- **Email**: 

---


*Este documento es un trabajo vivo y será actualizado regularmente con nuevos aprendizajes, patrones y mejores prácticas.*