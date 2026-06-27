---
alias: fuente-teoria-de-colas-u3
tags: [fuente, unidad-3, colas]
source_file: "Simulación Unidad III Teoría de Colas.pdf"
type: apunte
ingested: 2026-06-27
---

# Fuente — Simulación Unidad III: Teoría de Colas

Apunte base de la materia sobre líneas de espera. Fuente de verdad para la notación y el modelo.

## Takeaways clave
- **Origen** (Erlang, 1909, tráfico telefónico de Copenhague). La teoría **no resuelve** el
  problema: da información para la **toma de decisiones**.
- **Objetivos**: nivel óptimo de capacidad que minimiza el costo global; balance costo/calidad de
  servicio; atención al tiempo de permanencia en sistema/cola.
- **Características de un sistema de colas**: patrón de llegadas, patrón de servicio, disciplina
  (FIFO/LIFO), capacidad del sistema, n° de servidores/canales, etapas.
- **Notación Kendall-Lee** `(a/b/c)(d/e/f)`: distribución de llegadas / de servicio / n° servidores
  / disciplina / capacidad / población. Símbolos: M=exponencial, D=determinística, Ek=Erlang,
  G=general, etc.
- **M/M/1** `(M/M/1)(FIFO/10/50)`: 1 servidor, llegadas y servicio exponenciales. Modelo base.
- **Parámetros**: λ=tasa media de llegadas (1/λ = tiempo entre llegadas); μ=tasa media de servicio
  (1/μ = tiempo de servicio).
- **Ejemplo del peaje** (validación numérica): λ=0.05, μ=1/12 → Lq≈0.91, W≈30.3 s, P0≈0.40.
- **Algoritmo de simulación por eventos** (lista de eventos): variables TM, AT, DT, TE, TS, SS, WL,
  MX. Solo 2 eventos: llegada o salida. Es la base conceptual del motor SimPy.
- Caso 1: **una cola con varios servidores** (= M/M/c, una cola única) → el que usamos.

## Alimenta a
- [[caso-de-estudio]] (mapeo Tower Defense → colas, objetivos)
- [[modelo-de-colas]] (Kendall-Lee, M/M/1, algoritmo de eventos)
- [[variables]] (variables del reloj de simulación: TM, AT, DT, WL...)
- [[recursos-y-temperatura]] (lista de eventos → procesos SimPy)
