---
id: "sdd-sdd-06"
title: "Ejemplo completo de cambio OpenSpec: alerta de plazos SDC en UFD"
category: methodology
document_type: "reference"
source_path: "Context/ns_sdd/sdd_06_ejemplo_cambio_ufd.md"
source_identifier: "sdd_06; referencias de origen corregidas en la curación"
authority: methodological
curation_status: needs_review
version: "1.1"
curated_on: "2026-10-10"
usage: "grounding"
---

> **Estado de curación:** Este contenido conserva el texto de origen y ha sido normalizado para su ingestión. Contiene afirmaciones o campos pendientes de validación; no debe usarse como fuente definitiva sin contrastar la procedencia indicada.

# Ejemplo completo: `add-alerta-plazo-sdc`

Ejemplo de referencia de un caso de uso sencillo. Los agentes pueden usarlo como modelo de calidad. Las cifras y el diseño son ilustrativos.

## 1. proposal.md

```markdown
## Why
En el proceso SDC-01 (Nuevo suministro BT baremo), el RD 1183/2020 fija 5 días hábiles
para emitir la propuesta previa. Hoy los tramitadores (ADS) revisan los plazos a mano
y algunos expedientes vencen sin detectarse, con riesgo de penalización regulatoria y
empeorando el KPI "Tasa de resolución en plazo SDC" (objetivo interno > 95 %).

## What Changes
- Cálculo automático de la fecha de vencimiento de la propuesta previa en días hábiles.
- Alerta diaria al tramitador asignado para expedientes a 2 días hábiles o menos del vencimiento.
- Aviso al supervisor para expedientes vencidos.
- Registro de cada alerta en el historial del expediente.

## Out of Scope
- Otros tipos de expediente (SDC-02 en adelante): cambios posteriores.
- Comunicaciones al solicitante.
- Cambios en el canal digital de clientes (PDS).

## Impact
- Nueva capacidad: `alertas-plazos`.
- Integración de lectura con el sistema de expedientes SDC.
- Usuarios: ADS y supervisores del backoffice SDC.

## Value
- Tasa de resolución en plazo SDC-01: de [ADAPTAR: valor actual] a > 95 %.
- Ahorro estimado: [ADAPTAR] horas/semana de revisión manual.
```

## 2. specs/alertas-plazos/spec.md (delta)

```markdown
## ADDED Requirements

### Requirement: Cálculo del vencimiento de la propuesta previa
El sistema SHALL calcular la fecha de vencimiento de la propuesta previa de un expediente
SDC-01 sumando 5 días hábiles a la fecha de solicitud completa, excluyendo sábados,
domingos y festivos nacionales y autonómicos del territorio del suministro.

#### Scenario: Solicitud completa en lunes sin festivos
- **GIVEN** un expediente SDC-01 con solicitud completa el lunes 19 de octubre de 2026
- **WHEN** el sistema calcula el vencimiento
- **THEN** la fecha de vencimiento es el lunes 26 de octubre de 2026

#### Scenario: Festivo nacional en el periodo
- **GIVEN** un expediente SDC-01 con solicitud completa el lunes 5 de octubre de 2026
- **WHEN** el sistema calcula el vencimiento
- **THEN** la fecha de vencimiento es el martes 13 de octubre de 2026 (el 12 de octubre es festivo nacional)

#### Scenario: Festivo autonómico en el periodo
- **GIVEN** un expediente con un festivo autonómico de su territorio dentro de los 5 días
- **WHEN** el sistema calcula el vencimiento
- **THEN** el festivo no se cuenta como día hábil

#### Scenario: Solicitud incompleta
- **GIVEN** un expediente sin fecha de solicitud completa
- **WHEN** el sistema calcula el vencimiento
- **THEN** no se calcula vencimiento y el expediente se marca como "pendiente de completar"

### Requirement: Alerta de vencimiento próximo
El sistema SHALL enviar cada día hábil a las 08:00 una alerta al tramitador asignado
por cada expediente SDC-01 abierto a 2 días hábiles o menos de su vencimiento.

#### Scenario: Expediente a 2 días hábiles del vencimiento
- **GIVEN** un expediente abierto sin propuesta previa emitida y a 2 días hábiles del vencimiento
- **WHEN** se ejecuta el proceso diario de alertas
- **THEN** el tramitador asignado recibe una alerta con el número de expediente y la fecha de vencimiento
- **AND** la alerta queda registrada en el historial del expediente

#### Scenario: Propuesta previa ya emitida
- **GIVEN** un expediente con la propuesta previa emitida
- **WHEN** se ejecuta el proceso diario de alertas
- **THEN** no se genera alerta

#### Scenario: Expediente sin tramitador asignado
- **GIVEN** un expediente a 1 día hábil del vencimiento sin tramitador asignado
- **WHEN** se ejecuta el proceso diario de alertas
- **THEN** la alerta se envía al supervisor del equipo SDC

### Requirement: Aviso de expediente vencido
El sistema MUST notificar al supervisor del equipo SDC cada expediente SDC-01 abierto
cuya fecha de vencimiento ya haya pasado sin propuesta previa emitida.

#### Scenario: Expediente vencido
- **GIVEN** un expediente con vencimiento ayer y sin propuesta previa emitida
- **WHEN** se ejecuta el proceso diario de alertas
- **THEN** el supervisor recibe un aviso de expediente vencido

### Requirement: Control de acceso a las alertas
El sistema SHALL mostrar las alertas solo a los usuarios con rol ADS o Supervisor SDC
y solo para los expedientes de su ámbito.

#### Scenario: Usuario sin rol autorizado
- **GIVEN** un usuario autenticado con rol Agente SAC
- **WHEN** intenta consultar el listado de alertas SDC
- **THEN** el sistema devuelve un error de acceso denegado (403)
- **AND** registra el intento en el log de auditoría
```

## 3. compliance.md

```markdown
## Data Classification
- Número de expediente, fechas, estado, tramitador: Interno.
- Datos del solicitante (nombre, CUPS, dirección): Confidencial. NO se incluyen en las alertas,
  solo el número de expediente (SEC-22, minimización).

## Personal Data (RGPD)
- Se tratan datos personales de los empleados (destinatarios de la alerta). Base jurídica: relación laboral.
- No requiere consulta al DPO.

## Regulatory Separation
- Aplicación interna de UFD. No se comparte información con comercializadoras (REG-01).
- Despliegue en cuenta AWS de UFD, separada de las de negocios liberalizados (REG-02).

## AI Act Classification
- No usa IA en la aplicación. No aplica.

## Security Requirements
- Autorización por rol: Requirement "Control de acceso a las alertas".
- Auditoría de accesos denegados: incluida como escenario.

## Approvals Needed
- Ninguna adicional.
```

## 4. design.md (extracto)

```markdown
## Decisions
- Proceso diario con Amazon EventBridge Scheduler + AWS Lambda (patrón de referencia "batch ligero").
  Alternativa descartada: consultas en tiempo real (sobredimensionado para una alerta diaria).
- Calendario de festivos como tabla de referencia mantenida por el negocio.
- Notificación por correo corporativo vía Amazon SES [ADAPTAR al canal corporativo].

## Architecture
EventBridge Scheduler (08:00, días hábiles) → Lambda "calcular-alertas"
  → lee expedientes SDC-01 abiertos (API del sistema de expedientes, solo lectura)
  → escribe historial de alertas (DynamoDB)
  → envía notificación (SES)

## Security & Threats
- Spoofing: la Lambda accede a la API con rol IAM dedicado y mínimo privilegio (SEC-17).
- Information disclosure: las alertas solo contienen el número de expediente.
- Secretos de la API en Secrets Manager (SEC-18).

## Testing Strategy
- Unitarios: cálculo de días hábiles (los 4 escenarios del requisito).
- Integración: proceso de alertas con dobles de la API de expedientes.
- Acceso: test del escenario 403.
```

## 5. tasks.md

```markdown
## 1. Cálculo de vencimiento
- [ ] 1.1 Implementar el calendario de festivos por territorio (Req: Cálculo del vencimiento)
- [ ] 1.2 Implementar el cálculo de días hábiles
- [ ] 1.3 Tests de los 4 escenarios del requisito "Cálculo del vencimiento"

## 2. Proceso de alertas
- [ ] 2.1 Lambda de selección de expedientes a ≤ 2 días hábiles (Req: Alerta de vencimiento próximo)
- [ ] 2.2 Envío al tramitador o al supervisor si no hay asignado
- [ ] 2.3 Registro de la alerta en el historial
- [ ] 2.4 Aviso de expedientes vencidos (Req: Aviso de expediente vencido)
- [ ] 2.5 Tests de integración de los escenarios de alertas

## 3. Acceso y seguridad
- [ ] 3.1 Autorización por rol en el listado de alertas (Req: Control de acceso)
- [ ] 3.2 Test del escenario 403 y del registro de auditoría

## 4. Infraestructura
- [ ] 4.1 IaC: EventBridge Scheduler, Lambda, DynamoDB, SES, roles IAM

## 5. Verificación
- [ ] 5.1 /opsx-verify sin discrepancias
- [ ] 5.2 openspec validate add-alerta-plazo-sdc --strict
- [ ] 5.3 Pipeline en verde (cobertura ≥ 80 %, SAST, SCA, secretos)
- [ ] 5.4 README y runbook actualizados
```
