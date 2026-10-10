# sdd-methodology-designer

Skill para agentes que diseñan o revisan una metodología SDD asistida por IA. Su definición operativa está en `SKILL.md`.

## Activación sugerida

> Usa el skill `sdd-methodology-designer` para diseñar una metodología o un caso de uso SDD, aplicando los contextos y gates definidos en el corpus `curated_context`.

## Dependencias

Antes de utilizarlo en Context Studio, configurar los contextos `context-governance` y `sdd-methodology-core`. Añadir `business-boundaries` y `tooling-reference` sólo cuando el caso lo requiera.


## Integración MCP Context Broker

El skill presupone el uso preferente del MCP **Context Broker** para descubrir contextos, verificar metadatos y esquema ontológico, recuperar evidencia vectorial/híbrida/grafo y notificar conocimiento aprobado mediante eventos de ingesta. La secuencia, límites de consulta y reglas de actualización están definidos en `SKILL.md`.

Antes de un diseño material, el agente debe descubrir contextos con `context-broker-get-contexts`, verificar su metadata y schema, y recuperar sólo evidencia pertinente. Para exploración de grafo debe aplicar `max_depth=1` y `limit=5`.
