---
id: nat_04
namespace: ns_corporate
title: "Marco Regulatorio y Separación de Actividades en Naturgy"
type: business_context
tags: [regulación, separación-actividades, unbundling, CNMC, CNMV, MITECO, regulado, liberalizado, datos]
entities: [Naturgy, UFD, Nedgia, Naturgy Clientes, CNMC, CNMV, MITECO, AEPD]
source: "Ley 24/2013 + Ley 34/1998 + normativa CNMC + conocimiento sectorial"
status: available
priority: tier1_critical
created: "2026-10-10"
---

# Marco Regulatorio y Separación de Actividades

## 1. Actividades reguladas y liberalizadas

| Tipo | Actividades del grupo | Cómo se fijan los ingresos |
|---|---|---|
| **Reguladas** | Distribución de electricidad (UFD) y de gas (Nedgia); redes en otros países | Retribución fijada por el regulador (en España, CNMC) |
| **Liberalizadas** | Generación, gestión de la energía, comercialización | Precios de mercado y competencia |

## 2. Normas marco en España

| Norma | Ámbito |
|---|---|
| **Ley 24/2013** del Sector Eléctrico | Electricidad: actividades, retribución, derechos del consumidor |
| **Ley 34/1998** del Sector de Hidrocarburos | Gas natural: transporte, distribución, comercialización |
| **Circulares de la CNMC** | Retribución de redes, peajes, calidad de servicio, intercambio de información |
| **Ley 10/2025** de Servicios de Atención a la Clientela | Plazos de respuesta, canales y protección del cliente |
| **RGPD y LOPDGDD** | Protección de datos personales |
| **ENS y normativa de infraestructuras críticas** | Seguridad de la información y de los sistemas |
| **Normativa del mercado de valores** | Información privilegiada y transparencia como cotizada (CNMV) |
| **Directivas europeas** | Mercado interior de electricidad y gas, eficiencia, renovables, CSRD |

## 3. Separación de actividades (unbundling)

Las distribuidoras del grupo deben operar de forma **independiente** de las actividades liberalizadas (comercialización y generación).

| Tipo de separación | Qué implica |
|---|---|
| **Jurídica** | UFD y Nedgia son sociedades distintas de Naturgy Clientes |
| **Funcional** | Decisiones de gestión independientes; directivos distintos |
| **Informativa** | Prohibido dar a la comercializadora del grupo información de la red o de clientes de forma preferente |
| **De marca** | Las distribuidoras usan su propia marca, no la de Naturgy, en su rol regulado |
| **Contable** | Cuentas separadas por actividad |

### Reglas prácticas para los proyectos

- **REG-01** Un caso de uso de una distribuidora NO DEBE usar ni exponer datos a la comercializadora del grupo fuera de los cauces regulados (mensajería ATR, accesos regulados).
- **REG-02** Las aplicaciones compartidas entre negocios regulados y liberalizados DEBEN separar datos, accesos y trazas por sociedad.
- **REG-03** Las comunicaciones de las distribuidoras a clientes DEBEN usar su propia marca.
- **REG-04** Los accesos a datos de clientes DEBEN ser trazables y auditables.

## 4. Organismos relevantes

| Organismo | Papel |
|---|---|
| **CNMC** | Regulador energético y de competencia: retribución, peajes, supervisión, reclamaciones |
| **MITECO** | Ministerio: política energética y normativa |
| **CNMV** | Supervisor del mercado de valores (Naturgy cotiza) |
| **AEPD** | Protección de datos |
| **Comunidades autónomas** | Autorizaciones de instalaciones, consumo, industria |
| **REE / Enagás** | Gestores técnicos de los sistemas eléctrico y gasista |

## 5. Implicaciones para la IA y los datos

- Los datos de clientes son **datos personales** (RGPD) y, en distribución, además **datos sensibles por separación de actividades**.
- Naturgy, como cotizada, debe proteger la **información privilegiada**: no debe introducirse en herramientas de IA no autorizadas.
- Ver también la política de seguridad (NORM-SEC-001) y la política de uso de IA (NORM-IA-001).
