---
id: "norm-norm-sec-001"
title: "Política de seguridad en el desarrollo de aplicaciones"
category: governance
document_type: "policy"
source_path: "Context/ns_marco_normativo/politica-seguridad-desarrollo.md"
source_identifier: "NORM-SEC-001"
authority: governing
curation_status: provisional
policy_status: provisional
policy_owner: "CISO"
effective_date: "2026-10-10"
review_cycle: "annual"
next_review_date: "2027-10-10"
version: "1.1"
curated_on: "2026-10-10"
usage: "grounding"
---

> **Estado de gobierno:** Política operativa provisional, con vigencia desde el 2026-10-10. Propietario designado: **CISO**. Requiere ratificación formal antes de tratarse como política corporativa definitiva.

# Política de seguridad en el desarrollo de aplicaciones

## 1. Objeto y alcance

Esta política fija los requisitos de seguridad obligatorios para todas las aplicaciones desarrolladas por los equipos, incluidas las construidas con asistentes de codificación por IA (Kiro, Cline) y las que incorporan modelos de IA generativa.

Se aplica a todo el ciclo de vida: especificación, diseño, implementación, verificación, despliegue y operación.

**Convención:** "DEBE" = obligatorio; "NO DEBE" = prohibido; "DEBERÍA" = recomendado, desviación justificada por escrito.
Cada requisito tiene un identificador (SEC-XX) para que agentes y revisores puedan citarlo.

## 2. Principios

- **Seguridad por diseño y por defecto**: la seguridad se especifica en la spec, no se añade al final.
- **Mínimo privilegio**: personas, servicios y agentes de IA tienen solo los permisos imprescindibles.
- **Defensa en profundidad**: ningún control único es suficiente.
- **Zero trust**: toda petición se autentica y autoriza, venga de donde venga.
- **Responsabilidad humana**: el código generado por IA tiene un autor humano responsable de su seguridad.

## 3. Clasificación de la información

| Nivel | Ejemplos | Requisitos mínimos |
|---|---|---|
| Público | Documentación publicada | Integridad |
| Interno | Procedimientos, código fuente | Acceso autenticado |
| Confidencial | Datos de clientes, datos financieros, datos personales | Cifrado, acceso por rol, registro de accesos |
| Restringido | Categorías especiales RGPD, credenciales, datos de salud | Cifrado con claves gestionadas, acceso nominal aprobado, monitorización |

- **SEC-01** La spec de cada caso de uso DEBE identificar los datos que trata y su nivel de clasificación.
- **SEC-02** Los datos de nivel Confidencial o Restringido NO DEBEN usarse en entornos de desarrollo o pruebas sin anonimizar o seudonimizar.

## 4. Seguridad en las fases SDD

### 4.1 Especificación (requirements)
- **SEC-03** La spec DEBE incluir requisitos de seguridad explícitos: autenticación, autorización por rol, datos tratados, retención y auditoría.
- **SEC-04** Los requisitos de seguridad DEBEN tener criterios de aceptación verificables (p. ej., "CUANDO un usuario sin rol X acceda a /informes, el sistema DEBE devolver 403").

### 4.2 Diseño (design)
- **SEC-05** El diseño DEBE incluir un modelado de amenazas ligero (STRIDE o equivalente) proporcional al caso de uso.
- **SEC-06** El diseño DEBE usar exclusivamente servicios y patrones del catálogo aprobado de arquitectura y del hiperescalar de referencia.
- **SEC-07** Los flujos de datos que salgan del perímetro corporativo (APIs externas, SaaS, modelos de IA) DEBEN documentarse y aprobarse.

### 4.3 Implementación
- **SEC-08** El código DEBE seguir OWASP Top 10 y, si expone APIs, OWASP API Security Top 10.
- **SEC-09** Toda entrada externa DEBE validarse en servidor; toda salida a navegador DEBE codificarse según contexto.
- **SEC-10** Las consultas a bases de datos DEBEN usar consultas parametrizadas u ORM; NO DEBE concatenarse SQL con datos de entrada.
- **SEC-11** NO DEBEN implementarse algoritmos criptográficos propios; se usarán librerías y servicios estándar (AWS KMS, bibliotecas mantenidas).

### 4.4 Verificación
- **SEC-12** El pipeline CI DEBE ejecutar: SAST, análisis de dependencias (SCA), detección de secretos y análisis de IaC. Si hay contenedores, también análisis de imágenes.
- **SEC-13** Las aplicaciones expuestas a Internet DEBEN pasar un DAST antes de producción.

### 4.5 Despliegue y operación
- **SEC-14** El despliegue DEBE realizarse por pipeline; NO DEBEN hacerse cambios manuales en producción.
- **SEC-15** La infraestructura DEBE definirse como código (IaC) y desplegarse dentro de la landing zone corporativa.

## 5. Identidad, acceso y secretos

- **SEC-16** La autenticación de usuarios DEBE delegarse en el proveedor de identidad corporativo (SSO, OIDC/SAML) con MFA.
- **SEC-17** Los servicios en AWS DEBEN usar roles IAM; NO DEBEN usarse claves de acceso de larga duración.
- **SEC-18** Los secretos DEBEN almacenarse en AWS Secrets Manager o Parameter Store (cifrado). NO DEBEN aparecer en código, specs, ficheros de configuración versionados, prompts, logs ni tickets.
- **SEC-19** Las políticas IAM DEBEN revisarse para eliminar comodines (`*`) injustificados.

## 6. Protección de datos

- **SEC-20** Los datos DEBEN cifrarse en tránsito (TLS 1.2 o superior) y en reposo (KMS).
- **SEC-21** Los datos personales DEBEN tratarse en regiones de la UE salvo excepción aprobada por el DPO.
- **SEC-22** La spec DEBE aplicar minimización de datos: solo se recogen los datos necesarios para el caso de uso.
- **SEC-23** Si el caso de uso trata datos personales a gran escala o de categorías especiales, DEBE consultarse al DPO para valorar una evaluación de impacto (EIPD).
- **SEC-24** Los logs NO DEBEN contener datos personales en claro, secretos ni tokens.

## 7. Infraestructura cloud (AWS)

- **SEC-25** NO DEBEN crearse buckets S3 públicos; el bloqueo de acceso público DEBE estar activo.
- **SEC-26** Las aplicaciones web expuestas DEBEN protegerse con AWS WAF o equivalente aprobado.
- **SEC-27** DEBEN estar activos CloudTrail, registro de acceso y alertas de seguridad (GuardDuty / Security Hub) en las cuentas de la aplicación.
- **SEC-28** Los recursos DEBEN etiquetarse según la política de etiquetado (propietario, aplicación, entorno, clasificación).

## 8. Seguridad de aplicaciones que integran IA generativa

Basado en OWASP Top 10 for LLM Applications.

- **SEC-29** La entrada del usuario y el contenido recuperado (documentos, webs, correos) DEBEN tratarse como no confiables; el diseño DEBE mitigar la inyección de prompts (directa e indirecta).
- **SEC-30** La salida de un modelo NO DEBE ejecutarse ni interpretarse directamente (SQL, shell, HTML, llamadas a APIs) sin validación.
- **SEC-31** Los agentes con acceso a herramientas DEBEN tener el mínimo de herramientas y permisos (evitar "agencia excesiva"); las acciones irreversibles DEBEN requerir confirmación humana.
- **SEC-32** DEBEN configurarse Amazon Bedrock Guardrails (o equivalente) para filtrar contenido no permitido y datos personales cuando aplique.
- **SEC-33** Las bases de conocimiento (RAG) DEBEN respetar los permisos de acceso del usuario a los documentos de origen.

## 9. Seguridad en el uso de asistentes de codificación (Kiro, Cline)

- **SEC-34** Solo DEBEN usarse asistentes, versiones y extensiones aprobados, configurados contra modelos autorizados (ver NORM-IA-001).
- **SEC-35** Los servidores MCP DEBEN estar en la lista aprobada. NO DEBEN instalarse servidores MCP de origen desconocido.
- **SEC-36** NO DEBE activarse la autoaprobación de comandos de terminal ni de escritura fuera del repositorio. La ejecución de comandos DEBE requerir revisión humana, salvo una lista blanca de comandos seguros (tests, linters, build).
- **SEC-37** Los ficheros de reglas del agente (steering files, `.clinerules`) DEBEN versionarse y revisarse como código, ya que pueden ser vector de inyección.
- **SEC-38** NO DEBEN incluirse secretos ni datos Confidenciales o Restringidos en el contexto enviado al asistente.
- **SEC-39** Las dependencias sugeridas por la IA DEBEN verificarse (existencia real del paquete, mantenimiento, licencia) para evitar paquetes inexistentes o maliciosos ("slopsquatting").

## 10. Gestión de vulnerabilidades

| Severidad (CVSS) | Plazo máximo de corrección | ¿Bloquea paso a producción? |
|---|---|---|
| Crítica (9.0–10) | 7 días | Sí |
| Alta (7.0–8.9) | 30 días | Sí |
| Media (4.0–6.9) | 90 días | No, con ticket registrado |
| Baja (<4.0) | Siguiente ciclo | No |

[ADAPTAR plazos a la política corporativa vigente]

## 11. Incidentes y excepciones

- **SEC-40** Cualquier sospecha de incidente (fuga de secretos, datos expuestos, comportamiento anómalo de un agente) DEBE comunicarse de inmediato a [ADAPTAR – canal del SOC].
- **SEC-41** Las excepciones a esta política DEBEN aprobarse por escrito por el CISO, con fecha de caducidad y plan de remediación.

## 12. Checklist de quality gate de seguridad

- [ ] Datos y clasificación identificados en la spec (SEC-01)
- [ ] Requisitos de seguridad con criterios de aceptación (SEC-03, SEC-04)
- [ ] Modelado de amenazas en el diseño (SEC-05)
- [ ] SAST, SCA, secretos e IaC sin hallazgos críticos ni altos (SEC-12)
- [ ] Sin secretos en código ni en prompts (SEC-18, SEC-38)
- [ ] Cifrado en tránsito y reposo (SEC-20)
- [ ] Si integra IA: mitigación de inyección de prompts y Guardrails (SEC-29 a SEC-33)
- [ ] DAST superado si está expuesta a Internet (SEC-13)
