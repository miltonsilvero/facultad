
# Resumen Exhaustivo: Inferencia Estadística

> Versión con agregados propios (💡) sobre cómo encarar los ejercicios prácticos de cada unidad, para usar junto con la guía de código.

---

## Unidad 0: Fundamentos y Estadística Descriptiva

La **estadística inferencial** busca obtener conclusiones sobre una **población** a partir de una **muestra** representativa.

- **Parámetro:** medida numérica que describe una característica de la población.
- **Estadístico:** medida calculada sobre los datos de la muestra.
- **Media Muestral** ($\bar{x}$): promedio aritmético de las observaciones. $$ \bar{x} = \frac{\sum_{i=1}^n x_i}{n} $$

> 💡 **Cómo encararlo:** en la práctica, la Unidad 0 casi nunca pide "calcular" en el sentido de un test — pide **explorar** un dataset. La pregunta clave que hay que hacerse frente a cualquier variable nueva es "¿qué tipo de variable es?" (continua, categórica, binaria) porque de eso depende el gráfico (`histplot` vs. `value_counts` / pie) y qué probabilidad tiene sentido calcular (condicional, conjunta, correlación). Si el enunciado dice "¿es representativo de la población?", casi siempre la respuesta es **no** (la muestra de Kaggle no es aleatoria) — es una pregunta conceptual, no de cálculo.

---

## Unidad 2: Estimación de Parámetros

Un estimador $\hat{\theta}$ es una función de la muestra. Para ser considerado el "mejor", debe cumplir:

1. **Insesgadez:** el valor esperado debe ser igual al parámetro real: $E(\hat{\theta}) = \theta$.
2. **Varianza mínima:** la varianza del estimador debe tender a 0 cuando $n \to \infty$.
3. **Eficiencia:** ante dos estimadores insesgados, se prefiere el de menor desvío estándar.

### Intervalos de Confianza (IC)

Representan un rango donde se espera encontrar al parámetro con una probabilidad $(1-\alpha)$.

- **Para la media** ($\mu$):
    - $\sigma$ conocida: $\bar{x} \pm Z_{\alpha/2} \frac{\sigma}{\sqrt{n}}$
    - $\sigma$ desconocida ($n < 30$, población normal): $\bar{x} \pm t_{\alpha/2, \nu} \frac{s}{\sqrt{n}}$, con $\nu = n-1$ grados de libertad.
- **Para la proporción** ($p$): $\hat{p} \pm Z_{\alpha/2} \sqrt{\frac{\hat{p}(1-\hat{p})}{n}}$
- **Muestras pareadas:** $\bar{d} \pm t_{\alpha/2, \nu} \frac{s_d}{\sqrt{n}}$

> 💡 **Cómo encararlo:** antes de tocar una fórmula, respondé tres preguntas en orden: **(1)** ¿el parámetro es una media, una proporción o una diferencia? **(2)** si es media, ¿conozco $\sigma$? (casi nunca en los ejercicios reales → $t$, no $Z$) **(3)** si son dos muestras, ¿están pareadas (mismo sujeto/bloque medido dos veces) o son independientes? Ese árbol de tres preguntas define automáticamente qué fórmula de la lista de arriba usar. El error más común es usar $Z$ "porque es más simple" cuando en realidad no se conoce $\sigma$ poblacional.
> 
> Otro tip: si el enunciado pregunta "qué pasa con el ancho del intervalo al aumentar la confianza", pensalo siempre como un trade-off: **más confianza → intervalo más ancho** (necesitás "abarcar más" para estar más seguro), y la única forma de achicarlo sin perder confianza es **aumentar $n$**.

---

## Unidad 3: Tests de Hipótesis

Procedimiento para tomar decisiones sobre una hipótesis nula ($H_0$) frente a una alternativa ($H_1$).

- **Error Tipo I** ($\alpha$): rechazar $H_0$ cuando es verdadera (nivel de significancia).
- **Error Tipo II** ($\beta$): no rechazar $H_0$ cuando es falsa. Su complemento ($1-\beta$) es la **Potencia**.
- **P-valor:** probabilidad de observar un estadístico tan extremo como el obtenido si $H_0$ fuera cierta. Si $p\text{-valor} < \alpha$, se rechaza $H_0$.

**Estadísticos de prueba para la media:**

- $Z = \frac{\bar{X} - \mu_0}{\sigma/\sqrt{n}}$ (varianza conocida)
- $T = \frac{\bar{X} - \mu_0}{s/\sqrt{n}}$ (varianza desconocida)

> 💡 **Cómo encararlo:** un test de hipótesis es, en el fondo, **el mismo IC de la Unidad 2 mirado al revés**. Si ya sabés calcular el IC de un parámetro, el test se resuelve viendo si el valor de $H_0$ cae dentro o fuera de ese intervalo (para tests de dos colas). Esto es un atajo útil para verificar a mano lo que da `scipy.stats.ttest_ind` o similar. Frente a cualquier enunciado, escribí primero $H_0$ y $H_1$ en símbolos ANTES de calcular nada — la mitad del error en los parciales es plantear mal la hipótesis (por ejemplo, confundir "quiero probar que A > B" con un test de dos colas en vez de una cola). Y siempre cerrá con una frase en palabras: el número solo no alcanza, hay que traducir la decisión estadística a la pregunta original del problema.

---

## Unidad 4: ANOVA (Análisis de la Varianza)

Técnica para comparar medias de $a$ niveles de un factor.

- **Modelo de efectos fijos:** los niveles son seleccionados específicamente por el investigador.
- **Modelo de efectos aleatorios:** los niveles son una muestra aleatoria de una población mayor de niveles.
- **Identidad de la suma de cuadrados:** $SST = SSTrat + SSE$
- **Estadístico F:** $F_{calc} = \frac{MSTrat}{MSE}$. Se distribuye como $F$ de Snedecor con $(a-1)$ y $(N-a)$ grados de libertad.
- **Supuestos:** normalidad de residuos, independencia y homogeneidad de varianzas (Levene).

> 💡 **Cómo encararlo:** ANOVA responde una sola pregunta ("¿al menos una media es distinta?") — **no** te dice cuál. Por eso todo ejercicio de ANOVA con $p < \alpha$ tiene que terminar con un post-hoc (Tukey) si el enunciado pide identificar _cuáles_ grupos difieren. Antes de reportar el $F$ y el p-valor, siempre hay que chequear los tres supuestos (normalidad, independencia, homocedasticidad) — si el enunciado da gráficos de residuos o pide "verificar los supuestos", es una señal de que quieren que corras Shapiro y Levene antes de confiar en la tabla ANOVA. Si hay dos factores, pensá primero si tiene sentido una **interacción** (¿el efecto de un factor depende del nivel del otro?) antes de interpretar los efectos principales por separado.

---

## Unidad 5: Estadística No Paramétrica

Se utiliza cuando no se cumplen los supuestos de normalidad o en tamaños de muestra pequeños.

- **Prueba de Signo:** basada en la cantidad de signos positivos respecto a la mediana.
- **Prueba de Wilcoxon (rango con signo):** alternativa a la prueba $t$ para muestras pareadas, considerando la magnitud del rango de las diferencias.
- **Prueba de Mann-Whitney:** alternativa para comparar dos grupos independientes basándose en la suma de rangos.
- **Prueba de Kruskal-Wallis:** alternativa no paramétrica al ANOVA de un factor.

> 💡 **Cómo encararlo:** esta unidad es literalmente un "mapa de reemplazos" — a cada test paramétrico de las unidades 2-4 le corresponde un equivalente no paramétrico: t-pareada→Wilcoxon, t-independiente→Mann-Whitney, ANOVA→Kruskal-Wallis. La pregunta que define si hay que usar esta unidad es siempre la misma: **¿se verifica normalidad?** (Shapiro-Wilk, o el enunciado directamente dice "no se puede asumir normalidad" o da una escala ordinal). Si la respuesta es no, se cae automáticamente en el test no paramétrico equivalente al que hubieras usado en el caso paramétrico.

---

## Unidad 6: Inferencia Bayesiana

Actualiza el conocimiento sobre una variable $y$ (parámetros) tras observar datos $x$.

### El Teorema de Bayes

$$ P(y \mid x) = \frac{P(x \mid y) \cdot P(y)}{P(x)} $$

- **Posterior** ($P(y \mid x)$): distribución de probabilidad actualizada de $y$ dados los datos $x$.
- **Likelihood/Verosimilitud** ($P(x \mid y)$): probabilidad de observar los datos $x$ dado un valor de $y$.
- **Prior/Previa** ($P(y)$): conocimiento previo sobre $y$ antes de observar los datos.
- **Evidencia** ($P(x)$): constante de normalización calculada como $\int P(x \mid y)P(y),dy$.

**Caso Gaussiano:** si el prior es $\mathcal{N}(\mu_{pri}, \sigma_{pri}^2)$ y el likelihood $\mathcal{N}(\mu_{lik}, \sigma_{lik}^2)$, la posterior es otra Gaussiana cuya precisión (inversa de la varianza) es la suma de las precisiones individuales.

> 💡 **Cómo encararlo:** en cualquier ejercicio de Bayes, lo primero es **etiquetar** cada pieza del enunciado con su rol: ¿qué es el prior, qué es la verosimilitud, qué es la evidencia? El error típico es tratar de calcular la evidencia $P(x)$ integrando cuando en realidad, si el ejercicio compara dos modelos/hipótesis discretas (como el test diagnóstico), la evidencia es simplemente una **suma ponderada** de los casos (enfermo/sano), no una integral. Si el prior y el likelihood son ambos Gaussianos, **no hace falta integrar nada**: la posterior sale directo de sumar precisiones (es la fórmula "gratis" que conviene memorizar). Preguntate siempre: ¿el parámetro es discreto (moneda justa/sesgada, enfermo/sano) o continuo ($\theta \in [0,1]$)? Esa distinción define si termina en una razón de probabilidades simple o en una integral/ aproximación (que lleva a la Unidad 9).

---

## Unidad 7: Teoría de la Información e Inferencia Aproximada

Cuando la evidencia $P(x)$ es intratable, se usan métodos aproximados.

### Inferencia Variacional y ELBO

Se busca una distribución sencilla $q(z)$ que aproxime a la posterior $p(z|x)$ minimizando la **Divergencia KL**.

$$ \log(p(x)) = KL(q(z) | p(z|x)) + ELBO(q(z)) $$

Maximizar el **ELBO (Evidence Lower Bound)** equivale a minimizar la KL. En un VAE, el ELBO se descompone en:

$$ \underbrace{\mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)]}_{\text{Término de Reconstrucción}} - \underbrace{D_{KL}(q_\phi(z|x)|p(z))}_{\text{Término de Regularización}} $$

### Inferencia por Muestreo (MCMC)

Genera muestras de la posterior para estimar integrales (Monte Carlo).

- **Balance detallado:** condición para que una cadena de Markov converja: $P(y_1)K(y_1 \to y_2) = P(y_2)K(y_2 \to y_1)$
- **Metropolis-Hastings:** algoritmo que acepta o rechaza propuestas de salto basado en el ratio de probabilidades.
- **Dinámica de Langevin:** usa el gradiente de la log-posterior para dirigir las muestras hacia zonas de alta probabilidad. $$ y_{t+1} = y_t - \frac{dt}{2}\nabla_y U(y_t) + \sqrt{dt},z_t $$

> 💡 **Cómo encararlo:** esta es la unidad más "abstracta" pero se vuelve mecánica si tenés en claro **por qué existe**: la evidencia $P(x)$ casi nunca se puede calcular en forma cerrada (integral sin solución analítica), así que hay dos caminos: (a) **optimizar** (variacional → ELBO) o (b) **muestrear** (MCMC/Metropolis). Frente a un ejercicio de KL, lo primero es identificar cuáles son las dos distribuciones que se comparan y si son Gaussianas (ahí hay fórmula cerrada, no hace falta integrar numéricamente). Frente a Metropolis, el punto clave que resuelve la mitad de la implementación es que **el ratio de aceptación cancela la constante de normalización** — por eso podés trabajar siempre con la posterior _no normalizada_ (likelihood × prior) sin preocuparte por $P(x)$.

---

## Unidad 8: Inferencia y Optimalidad

Un estimador bayesiano puntual $\hat{y}$ minimiza el riesgo esperado (función de costo).

- **Error Cuadrático Medio (MSE):** el estimador óptimo es la **Media a posteriori**.
- **Costo Absoluto (L1):** el estimador óptimo es la **Mediana a posteriori**.
- **Costo Hit-or-miss:** el estimador óptimo es la moda, conocida como **MAP (Máximo a Posteriori)**.

> 💡 **Cómo encararlo:** la clave de esta unidad es asociar automáticamente **función de costo ↔ estadístico posterior**: si el enunciado menciona "error cuadrático" pensá media; si menciona "error absoluto" pensá mediana; si menciona "todo o nada" / "acierto exacto" pensá moda/MAP. Es el mismo motivo por el que, en el ejercicio de regresión de la guía de código, la solución por mínimos cuadrados **coincide** con el MAP cuando el ruido es Gaussiano: MC minimiza error cuadrático, y para una posterior Gaussiana media = mediana = moda, así que los tres estimadores óptimos colapsan en uno solo. Ese es el "por qué" detrás de la equivalencia que se ve en el notebook de regresión.

---

## Unidad 9: Comparación de Modelos

- **Nivel 1:** inferencia sobre parámetros $\theta$ dentro de un modelo $M$.
- **Nivel 2:** inferencia sobre qué modelo $M$ es más probable dados los datos $D$.
- **Factor de Bayes** ($BF$): razón entre las evidencias de dos modelos. $$ BF = \frac{P(D \mid M)}{P(D \mid M')} $$
- **Navaja de Ockham:** la inferencia bayesiana penaliza modelos con demasiados parámetros (prior muy extendido) a favor de modelos más simples que expliquen igualmente bien los datos.
- **Aproximación de Laplace:** aproxima la posterior por una Gaussiana centrada en el MAP usando el **Hessiano** ($H$) de la log-posterior. $$ P(\theta) \approx P(\hat{\theta}) \exp\left(-\frac{1}{2}(\theta - \hat{\theta})^2 H\right) $$

> 💡 **Cómo encararlo:** distinguí siempre los **dos niveles** de inferencia antes de empezar a calcular: si el ejercicio pregunta "¿cuál es el mejor valor de $\theta$?" es Nivel 1 (unidades 2, 6-8); si pregunta "¿qué modelo es mejor?" es Nivel 2 (esta unidad). Frente a un Factor de Bayes con parámetros libres, el flujo mecánico es siempre el mismo: **(1)** hallar el MLE/MAP de $\theta$, **(2)** evaluar la verosimilitud ahí, **(3)** calcular el Hessiano de la log-verosimilitud en ese punto, **(4)** armar la aproximación de Laplace ($L_{MLE} \times \text{prior} \times \sqrt{2\pi/H}$). El término $\sqrt{2\pi/H}$ es literalmente el "factor de Occam" en números: entre más "puntudo" el máximo (Hessiano grande → posterior muy concentrada), menor el volumen ganado y menor la penalización — la intuición es que un modelo complejo solo gana si su ajuste extra compensa el volumen de hipótesis que "desperdició" en regiones que no explicaban los datos.