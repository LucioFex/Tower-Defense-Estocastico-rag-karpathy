---
alias: fuente-variables-aleatorias-u2
tags: [fuente, unidad-2, distribuciones]
source_file: "Simulación Unidad II - Variables aleatorias discretas y continuas.pdf"
type: apunte
ingested: 2026-06-27
status: ingesta-parcial
---

# Fuente — Simulación Unidad II: Variables aleatorias discretas y continuas

Apunte de variables aleatorias. Ingesta parcial (alto nivel); ver lint para profundizar.

## Takeaways clave
- Distinción **discretas vs. continuas** y sus funciones de probabilidad/densidad.
- Generación por **transformada inversa** (caso continuo) y por tablas/acumulada (caso discreto).
- La **exponencial** (continua) es la distribución de los tiempos del modelo de colas markoviano;
  su detalle correctivo está en [[fuente-variable-exponencial]].

## Notas de integración
- Justifica que los **tiempos** del modelo (arribos, servicio) sean continuos exponenciales y que el
  **tipo de enemigo** (extensión) sea una variable aleatoria **discreta** que escala μ.

## Alimenta a
- [[variables]] (clasificación V.A. discretas/continuas; extensión tipo de enemigo)
- [[generadores-aleatorios]] (transformada inversa)

> Lint: ingesta parcial. Profundizar generación de discretas (Bernoulli/categórica) si se activa la
> extensión de tipos de enemigo en el backend.
