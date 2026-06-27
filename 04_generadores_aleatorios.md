# 04 — Generadores de Números Pseudoaleatorios y Variables Aleatorias

> Unidad II. La simulación necesita un flujo de números aleatorios **reproducible**. Documentamos
> el generador y la transformación a las distribuciones del modelo (exponencial).

## 4.1 Números pseudoaleatorios: propiedades exigidas

Un buen generador `R ∈ [0,1)` debe ser:

1. **Uniforme** en `[0,1)`.
2. **Estadísticamente independiente** (sin correlación predecible entre valores sucesivos).
3. **Reproducible** desde una **semilla** (mismas condiciones iniciales → misma secuencia).
4. De **período amplio** (sin repetirse demasiado pronto).
5. **Rápido** y de **bajo costo de memoria**.

"Pseudo" = falso: provienen de un **algoritmo determinístico**, no del azar real.

## 4.2 Generador Congruencial Lineal (GCL)

Método estándar de la industria (Unidad II). Recurrencia:

```
X_{i+1} = (a · X_i + C) mod m
R_i     = X_i / m            # normalización a [0,1)
```

Parámetros: semilla `X0`, multiplicador `a > 0`, incremento `C > 0`, módulo `m > a, m > C`.

### Variante multiplicativa (C = 0)
```
X_{i+1} = (a · X_i) mod m
```
Más rápida (una operación menos). Para período máximo: `m = 2^g`, `a = 3+8k` ó `a = 5+8k`,
`X0` impar, período `N = m/4 = 2^(g−2)`.

> **Decisión de diseño:** el backend usa el **Mersenne Twister** de la librería estándar de Python
> (`random`, sembrado con `seed`), que cumple holgadamente las 5 propiedades y tiene período
> 2^19937−1. El **GCL queda documentado e implementado** en `prng.py` como cumplimiento académico
> explícito de la Unidad II y para mostrar que entendemos el mecanismo; ambos pasan las mismas
> pruebas de uniformidad. Lo importante para la reproducibilidad del TP es **fijar la semilla**.

## 4.3 Otros métodos (documentados, Unidad II)

- **Cuadrados Centrales (Von Neumann):** elevar la semilla al cuadrado y tomar los dígitos
  centrales. Período corto y degenera a cero → solo histórico.
- **Aditivo de congruencias:** `X_i = (X_{i−1} + X_{i−n}) mod m`, requiere secuencia previa.
- **Fibonacci:** `V_{n+1} = V_n + V_{n−1} + K·A`, con `K = 0` si `(V_n+V_{n−1}) ≤ A`, `K = −1` si no.

## 4.4 De uniforme a Exponencial: método de la Transformada Inversa

Las variables aleatorias del modelo (tiempo entre arribos `TE` y de servicio `TS`) son
**exponenciales**. Se generan a partir de `R ~ U[0,1)` invirtiendo la CDF.

CDF de la exponencial: `F(x) = 1 − e^(−λx)`. Igualamos a `R` y despejamos `x`:

```
R = 1 − e^(−λx)
1 − R = e^(−λx)
ln(1 − R) = −λx
        ┌─────────────────────────┐
        │  x = −(1/λ) · ln(1 − R)  │   forma canónica
        └─────────────────────────┘
```

Como `R` y `1−R` son ambas `U[0,1)`, se usa la forma equivalente y más común:

```
x = −(1/λ) · ln(R)          (R ∈ (0,1],  evitar R = 0)
```

Expresada en función de la **media** `T_media = 1/λ`:

```
x = −T_media · ln(R)
```

> Corrección del apunte ("Adicional variable continua exponencial"): la ecuación de generación
> correcta es la de arriba; la media de la exponencial es `1/λ`. El backend implementa esta
> transformada en `prng.py` (`exponencial_inversa`) y la valida contra `random.expovariate`.

## 4.5 Reproducibilidad en el proyecto

- Toda corrida fija `seed` (campo en `output.json → meta.seed`).
- Misma `seed` + mismos parámetros ⇒ **mismo `output.json`** ⇒ misma reproducción en Godot.
- El sweep de `c` usa **la misma semilla por valor de `c`** (técnica de **números aleatorios
  comunes**) para reducir la varianza de la comparación entre escenarios y aislar el efecto de `c`.
