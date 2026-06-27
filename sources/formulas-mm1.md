---
alias: fuente-formulas-mm1
tags: [fuente, unidad-3, colas, formulas]
source_file: "Fórmulas Sistemas de Colas tipo MM1.pdf"
type: formulario
ingested: 2026-06-27
---

# Fuente — Fórmulas Sistemas de Colas tipo MM1

Formulario cerrado de M/M/1. Referencia exacta para `analytical.py` del backend.

## Takeaways clave (fórmulas)
- **Utilización** `ρ = λ/μ` (= 1 − P0).
- **Servidor ocioso** `P0 = 1 − ρ = 1 − λ/μ`.
- **N° medio en sistema** `L = λ/(μ−λ)`.
- **N° medio en cola** `Lq = λ²/(μ(μ−λ))`.
- **Tiempo en sistema** `W = 1/(μ−λ)`.
- **Tiempo de espera en cola** `Wq = λ/(μ(μ−λ))`.
- **Prob. de n clientes** `Pn = (1−ρ)·ρⁿ`.
- Condición: `μ > λ` (estabilidad).

## Notas de integración
- Estas fórmulas son el **caso c=1** del modelo M/M/c (Erlang C). El backend testea que
  M/M/c(c=1) las reproduce exactamente (`test_mmc_reduce_a_mm1`).

## Alimenta a
- [[modelo-de-colas]] (sección M/M/1)
- [[sintesis]] (validación analítico vs. simulado)
