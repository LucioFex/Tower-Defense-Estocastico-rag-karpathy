---
alias: fuente-variable-exponencial
tags: [fuente, unidad-2, distribuciones, exponencial]
source_file: "Adicional variable continua exponencial.pdf"
type: apunte-correccion
ingested: 2026-06-27
---

# Fuente — Adicional: variable continua exponencial

Nota correctiva sobre la generación de la exponencial negativa por transformada inversa.

## Takeaways clave
- **Media** de la exponencial negativa = `1/λ`.
- **Transformada inversa** (corrige un error de otro apunte): de `F(x)=1−e^{−λx}=R` se despeja
  `x = −(1/λ)·ln(1−R)`, equivalente a `x = −(1/λ)·ln(R)`.
- Expresada en función de la media: `x = −media·ln(R)`.

## Notas de integración
- Implementado en `prng.py::exponencial_inversa`. Es el método con el que se generan **tiempo entre
  arribos** Exp(λ) y **tiempo de servicio** Exp(μ) del modelo.

## Alimenta a
- [[generadores-aleatorios]] (transformada inversa exponencial)
- [[variables]] (V.A.1 arribos, V.A.2 servicio)
