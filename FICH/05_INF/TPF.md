# Guía completa de explicación — TP Heart Disease

---

## CONTEXTO GENERAL

**Dataset:** 303 pacientes del Cleveland Clinic Foundation. 13 variables clínicas + variable objetivo `target` (0 = sin enfermedad, 1 = con enfermedad). ~54% tiene enfermedad.

**Eje del trabajo:** no diagnosticar ni afirmar causalidad. El objetivo es comparar los dos grupos y ver qué patrones aparecen en los datos.

**Flujo:**
1. Exploración descriptiva
2. Revisión de supuestos
3. Tests no paramétricos (Mann-Whitney)
4. Naive Bayes
5. Metropolis-Hastings

---

## 1. VARIABLES

| Variable | Qué mide | Tipo |
|---|---|---|
| age | Edad | Continua |
| sex | Sexo (1=M, 0=F) | Categórica |
| cp | Tipo de dolor de pecho (0=angina típica … 3=asintomático) | Ordinal |
| trestbps | Presión arterial en reposo (mmHg) | Continua |
| chol | Colesterol sérico (mg/dl) | Continua |
| fbs | Glucemia en ayuno > 120 mg/dl | Binaria |
| restecg | ECG en reposo | Categórica |
| **thalach** | **FC máxima alcanzada (bpm)** | Continua |
| exang | Angina inducida por ejercicio | Binaria |
| **oldpeak** | **Depresión ST inducida por ejercicio** | Continua |
| slope | Pendiente del segmento ST | Ordinal |
| ca | Vasos coloreados (0–3) | Discreta |
| thal | Resultado prueba talio (1=normal, 2=fijo, 3=reversible) | Categórica |

`thalach` y `oldpeak` son las variables principales del análisis inferencial porque miden respuesta al esfuerzo físico — que es el mecanismo más directamente relacionado con la función cardíaca.

---

## 2. EXPLORACIÓN Y DESCRIPTIVA

**Qué se hizo:** calcular media, mediana, desvío estándar, Q1, Q3, IQR, mínimo, máximo y asimetría para cada variable continua, separado por grupo. Para variables categóricas, tabla de frecuencias relativas por grupo.

**Por qué mediana además de media:** varias variables son asimétricas o tienen outliers. En esos casos la media se "arrastra" hacia los extremos y la mediana es más representativa del centro de la distribución.

**Hallazgos clave:**
- `thalach`: mediana 142 bpm (sin enfermedad) vs 161 bpm (con enfermedad) → los enfermos tienen FC máxima *más alta* en este dataset, lo cual puede parecer contraintuitivo pero se explica porque FC alta bajo estrés puede ser señal de respuesta anormal del sistema cardiovascular.
- `oldpeak`: mediana 1.4 (sin enfermedad) vs 0.2 (con enfermedad) → los sin enfermedad tienen *más* depresión ST, también refleja diferencias en la respuesta al ejercicio.
- `chol`: diferencias menores entre grupos → por eso no se eligió como variable principal de los tests.

**Correlación de Spearman:** se calculó como exploración para ver qué variables se asocian más con `target`. Las de mayor correlación absoluta fueron `cp`, `ca`, `exang`, `thalach`, `oldpeak`. Esto respaldó la elección de las dos variables para los tests.

---

## 3. OUTLIERS Y SUPUESTOS

### Outliers — método IQR

Un outlier se define como un valor que cae fuera del rango `[Q1 − 1.5×IQR, Q3 + 1.5×IQR]`. Este criterio es estándar y no depende de supuestos distribucionales.

**Decisión:** no se eliminaron automáticamente en esta etapa. Los outliers se detectaron para describir la base, no para limpiarla. La eliminación se hace después, como parte del análisis de sensibilidad del modelo Naive Bayes.

### Test de Shapiro-Wilk — normalidad

**Qué es:** contrasta H0 = "la muestra proviene de una distribución normal". Si p ≤ 0.05, se rechaza la normalidad.

**Por qué importa:** la mayoría de los tests paramétricos (t-test, ANOVA) asumen que los datos tienen distribución normal o que el n es suficientemente grande para el TCL. Con muestras chicas y distribuciones asimétricas, esos tests pueden dar resultados incorrectos.

**Resultado:** varias variables no pasan Shapiro → no se usan tests paramétricos.

### Test de Levene — homogeneidad de varianzas

**Qué es:** contrasta H0 = "las varianzas de los grupos son iguales". Se usa `center='median'` porque es más robusto ante no-normalidad que usar la media.

**Por qué importa:** el t-test estándar asume varianzas iguales. Si no se cumple, hay versiones corregidas (Welch), pero como ya descartamos normalidad, esto es información adicional que refuerza la decisión de usar tests no paramétricos.

---

## 4. TESTS DE HIPÓTESIS — MANN-WHITNEY U

### Qué es

Mann-Whitney U es el equivalente no paramétrico del t-test para dos grupos independientes. En lugar de comparar medias asumiendo normalidad, compara la distribución completa de rangos entre los dos grupos.

**Idea intuitiva:** toma todos los valores de los dos grupos juntos, los ordena de menor a mayor, y analiza si los valores de un grupo tienden a estar sistemáticamente por encima o por debajo de los del otro. Si los grupos son iguales, los rangos deberían mezclarse al azar.

### Hipótesis

- H0: la distribución de la variable es similar en ambos grupos (no hay diferencia sistemática).
- H1: las distribuciones difieren entre grupos.

### El p-valor — qué es y cómo se lee

El p-valor es la probabilidad de observar un resultado tan extremo como el obtenido (o más extremo) **asumiendo que H0 es verdadera**.

- No es la probabilidad de que H0 sea falsa.
- No dice nada sobre el tamaño de la diferencia.
- Un p-valor muy pequeño solo dice que la diferencia observada es muy improbable si los grupos fueran iguales.

**Criterio usado:** α = 0.05. Si p ≤ 0.05, se rechaza H0.

**Resultados:**
- `thalach`: p = 9.80×10⁻¹⁴ → diferencia altamente significativa
- `oldpeak`: p = 2.41×10⁻¹³ → diferencia altamente significativa

Estos p-valores son tan pequeños que la probabilidad de que la diferencia se deba al azar es prácticamente nula.

### Tamaño de efecto — rank-biserial

El p-valor solo dice que hay diferencia, no qué tan grande es. Para eso se usa el **rank-biserial**, que va de −1 a +1:
- Cercano a ±1: efecto fuerte (una distribución está casi siempre por encima de la otra).
- Cercano a 0: efecto nulo.

**Resultados:**
- `thalach`: −0.497 (efecto moderado-alto; el signo indica que el grupo sin enfermedad tiende a tener valores más altos)
- `oldpeak`: 0.480 (efecto moderado-alto; el grupo con enfermedad tiende a tener valores más altos)

---

## 5. NAIVE BAYES

### Qué es

Naive Bayes es un clasificador probabilístico basado en el teorema de Bayes. Estima la probabilidad de que un paciente tenga enfermedad dado el conjunto de sus variables clínicas.

**Teorema de Bayes resumido:**
```
P(enfermedad | datos) ∝ P(datos | enfermedad) × P(enfermedad)
```
- `P(enfermedad)` = probabilidad a priori (qué tan común es la enfermedad en la muestra).
- `P(datos | enfermedad)` = verosimilitud (qué tan probable es observar esos valores si el paciente tiene enfermedad).
- `P(enfermedad | datos)` = probabilidad a posteriori (lo que nos interesa).

**El supuesto "naive":** se asume que todas las variables son independientes entre sí dado `target`. Eso no es realista (cp y exang claramente correlacionan), pero simplifica mucho el cálculo y en la práctica el modelo funciona razonablemente bien.

### Variables y encoding

Variables usadas: `cp`, `exang`, `ca`, `thal`, `slope`, `sex` (categóricas) + `thalach`, `oldpeak` (continuas discretizadas).

`CategoricalNB` requiere variables discretas. Para las continuas se usó `KBinsDiscretizer` con 4 bins por cuantiles, lo que divide cada variable en 4 rangos de igual cantidad de observaciones. No se estandarizó porque el modelo trabaja con frecuencias, no con distancias.

### Análisis de sensibilidad frente a outliers

Se entrenó el mismo modelo con tres versiones de la base:

| Versión | Accuracy | Sensibilidad | Especificidad |
|---|---|---|---|
| Original | 0.852 | 0.939 | 0.750 |
| Sin outliers IQR | 0.860 | 0.903 | 0.808 |
| Outliers → mediana | 0.852 | 0.909 | 0.786 |

**Decisión:** se eligió la versión sin outliers IQR como modelo principal porque mejora especificidad sin perder demasiada sensibilidad. Es el balance más razonable.

### Métricas — cómo leerlas

- **Accuracy:** proporción de clasificaciones correctas sobre el total. Puede ser engañosa si los grupos son desbalanceados.
- **Sensibilidad (recall positivo):** de todos los enfermos reales, cuántos detectó el modelo. Alta sensibilidad = pocos falsos negativos. En medicina es el error más costoso (no detectar a alguien enfermo).
- **Especificidad (recall negativo):** de todos los sanos reales, cuántos clasificó correctamente. Alta especificidad = pocos falsos positivos.

### Matriz de confusión

```
                  Pred: sin enf.  Pred: con enf.
Real: sin enf.        VN              FP
Real: con enf.        FN              VP
```

- **VN (verdaderos negativos):** sanos bien clasificados.
- **VP (verdaderos positivos):** enfermos bien clasificados.
- **FP (falsos positivos):** sanos clasificados como enfermos → error de sobrediagnóstico.
- **FN (falsos negativos):** enfermos clasificados como sanos → el error más grave en clínica.

---

## 6. METROPOLIS-HASTINGS

### Contexto bayesiano

En estadística bayesiana, en lugar de estimar un único valor para un parámetro (como hace la estadística frecuentista), se estima una **distribución de probabilidad** para ese parámetro. Esa distribución se llama **posterior** y refleja lo que sabemos sobre el parámetro después de ver los datos.

La fórmula es:
```
posterior ∝ prior × verosimilitud
```
- **Prior:** lo que creemos sobre el parámetro antes de ver los datos.
- **Verosimilitud:** qué tan probable es observar los datos si el parámetro toma cierto valor.
- **Posterior:** la combinación de ambas cosas.

El problema es que en muchos modelos la posterior no tiene forma analítica simple. No se puede calcular directamente. Para eso existen los métodos de Monte Carlo por cadenas de Markov (MCMC), entre los cuales está Metropolis-Hastings.

### Qué hace Metropolis-Hastings

Es un algoritmo que **muestrea** la distribución posterior sin necesidad de conocerla explícitamente. Funciona así:

1. Empieza en un punto inicial (las medias observadas de `thalach` en cada grupo).
2. En cada iteración, propone un nuevo punto sumando un ruido aleatorio pequeño (la "propuesta").
3. Calcula qué tan probable es ese nuevo punto bajo la posterior.
4. Si es más probable que el actual → lo acepta siempre.
5. Si es menos probable → lo acepta con probabilidad proporcional a la diferencia (no lo rechaza siempre, para explorar la distribución).
6. Después de muchas iteraciones, la secuencia de puntos aceptados es una muestra de la posterior.

**Burn-in:** las primeras iteraciones (15.000 de 60.000) se descartan porque la cadena todavía está "buscando" la zona de alta probabilidad. El burn-in da tiempo para que converja antes de tomar muestras.

**Tasa de aceptación:** ~0.6 en este caso. Una tasa muy alta (cercana a 1) indica que las propuestas son demasiado pequeñas y la cadena no explora bien. Una tasa muy baja indica propuestas demasiado grandes. El rango razonable es 0.2–0.7.

### Qué se estimó

Se estimó δ = μ₁ − μ₀ (diferencia de medias de `thalach` entre el grupo con enfermedad y el grupo sin enfermedad).

**Priors usados:** N(150, 30) para cada media — centrado en 150 bpm que es un valor razonable de FC máxima, con varianza amplia para no ser demasiado informativo.

**Resultados:**
- Media posterior de δ: **19.26 bpm**
- Intervalo creíble 95%: **[14.53, 24.08]**
- P(δ > 0 | datos): **≈ 1.0000**

### Intervalo creíble vs intervalo de confianza

Son conceptos diferentes aunque visualmente similares:

- **Intervalo de confianza (frecuentista):** "si repitiéramos el experimento muchas veces, el 95% de los intervalos construidos contendría el verdadero parámetro". No dice nada sobre la probabilidad de que el parámetro esté en ese intervalo específico.
- **Intervalo creíble (bayesiano):** "dado lo que vimos en los datos, hay 95% de probabilidad de que el parámetro esté en este rango". Es más intuitivo y es exactamente lo que dice.

**Lectura del resultado:** como todo el intervalo [14.53, 24.08] está por encima de 0, hay evidencia bayesiana muy fuerte de que el grupo con enfermedad tiene mayor `thalach` que el grupo sin enfermedad. La probabilidad de que la diferencia sea negativa o nula es prácticamente cero.

### Trace plot — cómo leerlo

El trace plot muestra la secuencia de valores propuestos por la cadena a lo largo de las iteraciones. Lo que se busca es que **después del burn-in** la cadena oscile estabilmente alrededor de una zona, sin tendencias ni saltos grandes. Eso indica que la cadena convergió y está muestreando correctamente la posterior.

---

## 7. DECISIONES CLAVE Y POR QUÉ

| Decisión | Justificación |
|---|---|
| Usar `thalach` y `oldpeak` para los tests | Mayor correlación con `target`, diferencias descriptivas claras, relevancia clínica (respuesta al ejercicio) |
| No usar t-test | Shapiro-Wilk rechaza normalidad en varias variables |
| Mann-Whitney con `alternative='two-sided'` | No teníamos hipótesis direccional a priori sobre qué grupo tendría valores más altos |
| Modelo principal = sin outliers IQR | Mejora especificidad sin perder demasiada sensibilidad vs. base original |
| Mediana en lugar de media para reemplazo de outliers | Las distribuciones son asimétricas; la mediana es más robusta |
| Prior N(150, 30) en MH | Valor razonable de FC máxima humana, varianza amplia para no sesgar el resultado |
| Burn-in = 15.000 de 60.000 iteraciones | Proporción estándar (~25%) para dar tiempo a la convergencia |
| Sigma fijo en MH | Simplificación para concentrarse en la estimación de las medias; modelo didáctico, no de producción |

---

## 8. LO QUE NO SE HIZO Y POR QUÉ

- **No se eliminaron outliers en la exploración:** modificar la base sin justificación clínica sesgaría el análisis descriptivo.
- **No se usó colesterol como variable principal:** la diferencia entre grupos es menor y la correlación con `target` es más débil.
- **No se afirma causalidad:** el dataset es observacional. Que `thalach` sea diferente entre grupos no implica que cause (ni que no cause) la enfermedad.
- **No se interpreta como herramienta diagnóstica:** el modelo fue entrenado en 303 pacientes de un solo centro, con variables seleccionadas. No es generalizable.

---

## 9. RESUMEN DE RESULTADOS

| Análisis | Variable | Resultado |
|---|---|---|
| Mann-Whitney | thalach | p = 9.80×10⁻¹⁴, rank-biserial = −0.497 |
| Mann-Whitney | oldpeak | p = 2.41×10⁻¹³, rank-biserial = 0.480 |
| Naive Bayes (sin outliers) | múltiples | Acc=0.860, Sens=0.903, Esp=0.808 |
| Metropolis-Hastings | δ thalach | Media=19.26 bpm, ICr95%=[14.53, 24.08] |

**Conclusión general:** hay asociación clara entre las variables de respuesta al ejercicio (`thalach`, `oldpeak`) y la presencia de enfermedad cardíaca en esta muestra. La asociación es consistente tanto desde el enfoque frecuentista (Mann-Whitney) como bayesiano (MH). El modelo Naive Bayes combina múltiples variables y logra una clasificación razonable con alta sensibilidad.
