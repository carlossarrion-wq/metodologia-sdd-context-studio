---
id: "meta-constitution"
title: "Constitución de uso del contexto curado"
category: "metadata"
document_type: "governance"
authority: "operational"
curation_status: "curated"
version: "1.0"
curated_on: "2026-10-10"
usage: "grounding"
---

# Constitución de uso del contexto curado

## Propósito y alcance

Este documento establece las reglas mínimas para recuperar, interpretar, actualizar y usar los activos de `Curated_ctx` en Context Studio y en asistentes conectados. Se aplica a personas, agentes y flujos automatizados que consuman este corpus.

## Principios vinculantes

1. **Trazabilidad.** Toda respuesta material debe poder remitir al `id`, título y `source_path` del activo recuperado.
2. **Autoridad antes que similitud.** La recuperación semántica no prevalece sobre `authority`, vigencia ni normativa aplicable.
3. **No invención.** Si el corpus no confirma un dato, el sistema declarará la incertidumbre y solicitará o propondrá una fuente de validación.
4. **Normativa prevalente.** Un documento `NORM` aplicable prevalece sobre `BUS`, `SDD`, `TOOL` y ejemplos. Las políticas en borrador requieren confirmación de vigencia por su propietario.
5. **Separación de hechos e hipótesis.** El contenido `needs_review`, ilustrativo o genérico se presentará como tal; nunca como hecho corporativo, compromiso regulatorio o configuración existente.
6. **Minimización.** Recuperar sólo los fragmentos necesarios para la tarea y no incorporar secretos, datos personales reales ni información restringida sin autorización.
7. **Supervisión humana.** Las decisiones con impacto legal, regulatorio, financiero, de seguridad o de producción requieren revisión humana responsable.

## Reglas de consumo

- Filtrar por `category`, `authority` y `curation_status` antes de generar una respuesta.
- Citar o enlazar internamente los activos que sustenten recomendaciones, requisitos o decisiones.
- Interpretar ejemplos `SDD` como patrones didácticos, no como requisitos de producción.
- Verificar con documentación oficial actual cualquier capacidad, precio, versión o disponibilidad de herramientas `TOOL`.
- Cuando dos fuentes discrepen, aplicar el orden de resolución de `META_README.md`; si persiste la duda, bloquear la conclusión y registrar el conflicto.

## Reglas de publicación y mantenimiento

- Los archivos deben llamarse con un prefijo de categoría y tener nombre único global.
- Cada activo debe incluir el esquema de metadatos definido en `META_README.md`.
- No eliminar procedencia ni transformar una afirmación fuente en una afirmación validada durante la curación.
- Marcar como `needs_review` los borradores, campos con `[ADAPTAR]`/`[VALIDAR]`, fuentes genéricas o afirmaciones sin evidencia suficiente.
- Toda modificación debe actualizar `version`, `curated_on`, el inventario y, si corresponde, el informe de auditoría.

## Criterio de salida de una respuesta grounding

Antes de entregar una salida, el asistente debe comprobar: (a) que usó fuentes adecuadas a la pregunta, (b) que respetó las normas prevalentes, (c) que etiquetó las incertidumbres, (d) que no expuso información sensible y (e) que una persona puede localizar las fuentes que sustentan la respuesta.


## Regla de recuperación

Los agentes DEBEN recuperar el conjunto mínimo pertinente, citar el `id` de los activos usados y declarar si una conclusión depende de contenido `provisional` o `needs_review`. Los ejemplos, las guías de herramienta y los borradores no pueden prevalecer sobre una norma aplicable ni sobre evidencia primaria fechada.
