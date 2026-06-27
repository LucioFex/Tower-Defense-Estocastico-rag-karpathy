---
alias: fuente-numeros-pseudoaleatorios-u2
tags: [fuente, unidad-2, prng]
source_file: "Simulación Unidad II - Números Pseudoaleatorios.pdf"
type: apunte
ingested: 2026-06-27
---

# Fuente — Simulación Unidad II: Números Pseudoaleatorios

Apunte de generación de números pseudoaleatorios. Base de `prng.py`.

## Takeaways clave
- **Pseudoaleatorio = falso**: salen de un algoritmo determinístico; con la misma **semilla** se
  obtiene la misma secuencia.
- **Propiedades exigidas**: uniformidad, independencia estadística, reproducibilidad, período
  amplio, rapidez, mínimo uso de memoria.
- **Métodos**:
  - **Cuadrados centrales (Von Neumann)**: cuadrado de la semilla, tomar dígitos centrales.
    Período corto, degenera → histórico.
  - **Congruencial lineal**: `X_{i+1} = (a·X_i + C) mod m`, `R = X/m`. Condiciones `m>a, m>C`.
  - **Congruencial multiplicativo** (C=0): más rápido. Período máximo con `m=2^g`, `a=3+8k` o
    `5+8k`, `X0` impar → `N = m/4`.
  - **Aditivo**: `X_i = (X_{i−1} + X_{i−n}) mod m`.
  - **Fibonacci**: `V_{n+1} = V_n + V_{n−1} + K·A`.

## Notas de integración
- El backend implementa el **congruencial lineal/multiplicativo** en `prng.py` (cumplimiento U2) y
  usa Mersenne Twister para la simulación, ambos validados por uniformidad. Lo crítico es **fijar la
  semilla** para reproducibilidad.

## Alimenta a
- [[generadores-aleatorios]] (métodos congruenciales)
- [[variables]] (cómo se generan las variables aleatorias del modelo)
