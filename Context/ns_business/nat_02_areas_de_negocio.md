---
id: nat_02
namespace: ns_corporate
title: "Áreas de Negocio de Naturgy"
type: business_context
tags: [áreas-de-negocio, redes, renovables, gestión-energía, comercialización, GNL, UFD, Nedgia, Latinoamérica]
entities: [Naturgy, UFD, Nedgia, Naturgy Renovables, Naturgy Clientes, Sonatrach, Medgaz]
source: "Información corporativa Naturgy + resultados 2025 (estructura a validar con el informe anual)"
status: available
priority: tier1_critical
created: "2026-10-10"
---

# Áreas de Negocio de Naturgy

Naturgy organiza su actividad en grandes áreas que cubren toda la cadena de valor del gas y la electricidad. Unas son **reguladas** (redes, con ingresos fijados por el regulador) y otras **liberalizadas** (generación, gestión de la energía y comercialización, sujetas a competencia y a precios de mercado).

```
Aprovisionamiento ─► Generación ─► Redes (transporte/distribución) ─► Comercialización ─► Cliente
  (gas, GNL)        (renovable,      (UFD, Nedgia,                    (Naturgy Clientes,
                     térmica)         Latinoamérica)                   soluciones energéticas)
```

---

## 1. Redes (negocio regulado)

| Atributo | Detalle |
|---|---|
| **Qué hace** | Construye, opera y mantiene las redes que llevan el gas y la electricidad hasta el cliente. No vende energía. |
| **Sociedades principales en España** | **UFD** (distribución eléctrica) y **Nedgia** (distribución de gas) |
| **Internacional** | Redes de distribución de gas y/o electricidad en Latinoamérica (p. ej., Chile y Argentina) [VALIDAR perímetro actual] |
| **Ingresos** | Retribución regulada fijada por el regulador de cada país (en España, la CNMC) |
| **Peso en el grupo** | Principal contribuidor al EBITDA (~2.700 M€ en 2025) y destino de ~50 % de la inversión del plan 2025-2027 |
| **Retos** | Electrificación, integración de renovables y autoconsumo, calidad de suministro, gases renovables (biometano, hidrógeno) en la red de gas |

## 2. Renovables y nuevos negocios (liberalizado)

| Atributo | Detalle |
|---|---|
| **Qué hace** | Desarrolla, construye y opera plantas de generación renovable |
| **Tecnologías** | Eólica, solar fotovoltaica, hidráulica, almacenamiento (baterías, bombeo) |
| **Nuevos negocios** | Biometano, hidrógeno renovable, movilidad eléctrica, soluciones de autoconsumo |
| **Mercados** | España (principal), Australia, Estados Unidos, Latinoamérica [VALIDAR] |
| **Peso en el grupo** | ~30 % de la inversión del plan 2025-2027 |
| **Retos** | Tramitación de permisos, acceso a red, canibalización de precios solares, almacenamiento |

## 3. Gestión de la energía (liberalizado)

| Atributo | Detalle |
|---|---|
| **Qué hace** | Aprovisiona y gestiona el gas y la electricidad del grupo; optimiza la cartera en los mercados mayoristas |
| **Aprovisionamiento de gas** | Contratos a largo plazo por gasoducto (Argelia, gasoducto **Medgaz**, socio Sonatrach) y GNL (gas natural licuado) de distintos orígenes |
| **GNL y flota** | Compraventa internacional de GNL y transporte marítimo con buques metaneros |
| **Generación convencional** | Ciclos combinados de gas, participaciones en centrales nucleares y cogeneración [VALIDAR] |
| **Trading y coberturas** | Gestión de riesgos de precio de gas, electricidad y CO₂ |
| **Retos** | Volatilidad de precios, seguridad de suministro, papel del gas como respaldo del sistema eléctrico |

## 4. Comercialización y clientes (liberalizado)

| Atributo | Detalle |
|---|---|
| **Qué hace** | Vende gas, electricidad y servicios a clientes finales |
| **Sociedades** | **Naturgy Clientes** (mercado libre) y comercializadora de referencia para el mercado regulado (PVPC / TUR) [VALIDAR denominación] |
| **Segmentos** | Residencial, pymes, grandes clientes e industria, administraciones públicas |
| **Productos y servicios** | Suministro de luz y gas, mantenimiento de instalaciones, autoconsumo solar, recarga de vehículo eléctrico, eficiencia energética |
| **Canales** | Web y app, atención telefónica, tiendas y puntos de servicio, red comercial, gestores de cuenta para empresas |
| **Retos** | Competencia y captación, experiencia de cliente, protección del cliente vulnerable, digitalización, cumplimiento de la Ley de Servicios de Atención a la Clientela |

---

## 5. Mapa resumen

| Área | Tipo | Regulador / Mercado | Cliente principal | Ejemplos de mejora de procesos con IA |
|---|---|---|---|---|
| Redes | Regulado | CNMC y reguladores locales | Clientes, instaladores, promotores, comercializadoras | Validación documental de solicitudes de conexión, clasificación de comunicaciones, alertas de plazos |
| Renovables | Liberalizado | Mercado eléctrico | Mercado mayorista, PPAs | Seguimiento de permisos, análisis de producción, mantenimiento predictivo |
| Gestión de la energía | Liberalizado | Mercados mayoristas de gas y electricidad | Mercado, comercializadoras del grupo | Resúmenes de contratos, informes de mercado, conciliaciones |
| Comercialización | Liberalizado | Competencia; supervisión de consumo | Clientes finales | Atención al cliente, gestión de reclamaciones, back office de contratación |

> Las sociedades de redes (UFD, Nedgia) y las comercializadoras del grupo deben mantener separación funcional e informativa (ver nat_04). Un caso de uso no puede compartir datos entre ambas sin cumplir esa separación.
