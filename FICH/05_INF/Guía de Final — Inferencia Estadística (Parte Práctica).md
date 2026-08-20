
Resumen de todo el contenido con código visto en la materia, organizado por tema, con las librerías estándar y la forma "canónica" de resolver cada tipo de ejercicio en Python.

---

## 0. Stack de librerías — Cheatsheet de imports

```python
import numpy as np                      # arrays, medias, std, percentiles
import pandas as pd                     # dataframes, crosstab, value_counts
import matplotlib.pyplot as plt         # gráficos base
import seaborn as sns                   # histogramas, heatmaps, boxplots

from scipy import stats                 # distribuciones, tests, ppf/cdf
from scipy.stats import t, norm, gamma, comb, zscore, levene, shapiro

import statsmodels.api as sm
import statsmodels.formula.api as sm    # ols() con fórmulas tipo R
import statsmodels.stats.api as sms     # DescrStatsW -> IC directo
from statsmodels.stats.anova import anova_lm
from statsmodels.stats.multicomp import pairwise_tukeyhsd

from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression, LogisticRegression
from sklearn.naive_bayes import GaussianNB, BernoulliNB
from sklearn.metrics import accuracy_score, confusion_matrix
```

**Regla general de la materia:** casi todo ejercicio de intervalo de confianza o test de hipótesis se resuelve en dos pasos: (1) "a mano" con las fórmulas explícitas usando `scipy.stats` para los valores críticos, y (2) con la función de librería equivalente (`stats.t.interval`, `stats.ttest_ind`, `sms.DescrStatsW`, etc.) para verificar. Conviene saber hacer ambas.

---

## 1. Unidad 0 — Repaso de probabilidad y estadística descriptiva

Trabaja sobre un dataset real (diabetes de Kaggle) para practicar:

- **Limpieza:** `df.dropna()`
- **Exploración:** `df.head()`, `df.columns`, `df.shape`
- **Visualización de distribuciones:** `sns.histplot(df['col'], bins=20, kde=True)`
- **Gráfico de torta de proporciones:** `df['Outcome'].value_counts().plot(kind='pie', autopct='%1.1f%%')`
- **Crear variable binaria por cuartil:**
    
    ```python
	df['High_Glucose'] = np.where(df['Glucose'] > df['Glucose'].quantile(0.75), 1, 0)
    ```
    
- **Tabla de contingencia + heatmap:** `sns.heatmap(pd.crosstab(df['A'], df['B']), annot=True, fmt='d')`
- **Probabilidad condicional a mano:**
    
    ```python
p_A = df[df['Outcome']==1].shape[0] / df.shape[0]p_B = df[df['High_Glucose']==1].shape[0] / df.shape[0]p_AB = df[(df['Outcome']==1)&(df['High_Glucose']==1)].shape[0] / df.shape[0]p_A_dado_B = p_AB / p_B
    ```
    
- **Correlación:** `df['x'].corr(df['y'])` + `sns.scatterplot(data=df, x='x', y='y')`

---

## 2. Estimación de parámetros e Intervalos de Confianza

### 2.1 IC para la media (σ desconocida, distribución t)

**A mano:**

```python
media = np.mean(datos)
s = np.std(datos, ddof=1)          # ddof=1 -> desvío MUESTRAL
n = len(datos)
t_crit = t.ppf((1 + confianza) / 2, n - 1)
se = s / np.sqrt(n)
lim_inf, lim_sup = media - t_crit*se, media + t_crit*se
```

**Con librería:**

```python
ic = t.interval(0.95, n - 1, loc=media, scale=s/np.sqrt(n))
# o equivalente:
import statsmodels.stats.api as sms
ic = sms.DescrStatsW(df["columna"]).tconfint_mean(alpha=0.05)
```

### 2.2 Tamaño de muestra para reducir el error a la mitad

```python
error_deseado = error_original / 2
z = norm.ppf((1+confianza)/2)
n_necesario = int(np.ceil((z * s / error_deseado)**2))
```

Idea clave: **al aumentar la confianza, el intervalo se agranda** (z o t crecen); para achicar el error manteniendo confianza, hay que **aumentar n** (relación cuadrática inversa).

### 2.3 IC para una proporción (binomial)

```python
p_hat = x / n
z = norm.ppf(1 - alpha/2)
se = z * np.sqrt(p_hat*(1-p_hat)/n)
lower, upper = p_hat - se, p_hat + se
```

Tamaño de muestra necesario para un error máximo dado:

```python
n_req = (z**2 * p_hat*(1-p_hat)) / error_deseado**2       # con info previa de p
n_req_sin_info = (z**2 * 0.5*0.5) / error_deseado**2      # sin info previa -> p=0.5 (peor caso)
```

### 2.4 IC para diferencia de medias — muestras independientes (varianzas iguales)

```python
sp2 = ((n1-1)*s1**2 + (n2-1)*s2**2) / (n1+n2-2)   # varianza combinada (pooled)
sp = np.sqrt(sp2)
se = sp * np.sqrt(1/n1 + 1/n2)
df = n1 + n2 - 2
t_crit = stats.t.ppf(1-alpha/2, df)
ic = (x1_bar - x2_bar) - t_crit*se, (x1_bar - x2_bar) + t_crit*se

# con librería:
ic = stats.t.interval(0.95, df=n1+n2-2, loc=x1_bar-x2_bar, scale=se)
```

### 2.5 IC para diferencia de medias — muestras pareadas

Se trabaja sobre las **diferencias** entre pares (útil cuando el mismo sujeto/unidad se mide dos veces, o hay un factor de bloqueo que conviene neutralizar):

```python
diferencias = muestra1 - muestra2
ic = stats.t.interval(0.99, len(diferencias)-1,
                       loc=np.mean(diferencias), scale=stats.sem(diferencias))
```

---

## 3. Tests de Hipótesis

### 3.1 Test de una media (t de una muestra)

```python
t_stat = (media_muestral - mu0) / (s / np.sqrt(n))
p_valor = 2 * stats.t.cdf(-abs(t_stat), df=n-1)   # dos colas
# Decisión: si p_valor > alfa -> NO rechazo Ho
```

### 3.2 Test de diferencia de medias — independientes

```python
t_stat, p_valor = stats.ttest_ind(muestra1, muestra2, equal_var=False)  # Welch
# equal_var=True si se asume varianzas iguales
```

### 3.3 Test de proporción

Análogo al IC de proporción pero comparando el estadístico z contra el valor crítico, o usando el p-valor con `norm.cdf`.

**Checklist típico de un ejercicio de test de hipótesis:**

1. Plantear H0 / H1
2. Elegir estadístico (t, z) según si se conoce σ y el tamaño de muestra
3. Calcular estadístico y p-valor
4. Comparar contra α → Decisión
5. Conclusión en palabras del problema

---

## 4. ANOVA (uno y dos factores)

### 4.1 ANOVA de un factor

```python
model = sm.ols('Resistencia ~ C(Concentracion)', data=df).fit()
anova_table = anova_lm(model, typ=2)
print(anova_table)   # mirar columna PR(>F): si < alfa, rechazo Ho (las medias difieren)
```

**Supuestos a verificar siempre antes de confiar en el ANOVA:**

- **Normalidad de residuos:** `shapiro(residuos)`
- **Homogeneidad de varianzas:** `levene(grupo1, grupo2, grupo3)`
- Gráfico de residuos estandarizados vs. grupo/predicho (detectar patrones)

### 4.2 Post-hoc (Tukey HSD)

Si el ANOVA da significativo, hay que ver **cuáles** grupos difieren entre sí:

```python
tukey = pairwise_tukeyhsd(endog=df['Y'], groups=df['Grupo'], alpha=0.05)
print(tukey)
tukey.plot_simultaneous()
```

### 4.3 ANOVA de dos factores (factorial)

```python
model = ols('Duracion ~ C(Material) * C(Temperatura)', data=df).fit()
anova_table = sm.stats.anova_lm(model, typ=2)
```

El término `C(A)*C(B)` incluye automáticamente la **interacción** A×B. Si la interacción es significativa, hay que interpretar los efectos principales con cuidado (el efecto de un factor depende del nivel del otro).

---

## 5. Clasificación Bayesiana / Naive Bayes

### 5.1 Naive Bayes Gaussiano (features continuas)

```python
from sklearn.naive_bayes import GaussianNB
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.5, shuffle=True)

nb = GaussianNB()
nb.fit(X_train.reshape(-1,1), y_train)
pred = nb.predict(X_test.reshape(-1,1))
proba = nb.predict_proba(X_test.reshape(-1,1))[:, 1]
```

### 5.2 Naive Bayes Bernoulli (features binarias, ej. dígitos)

```python
from sklearn.naive_bayes import BernoulliNB
bnb = BernoulliNB()
bnb.fit(X_train, y_train)
```

### 5.3 Solución analítica Bayesiana (comparar con Naive Bayes / Regresión logística)

Con clases Gaussianas 1D conocidas (μ, σ, priors):

```python
def posteriors(X, mu1, mu2, s1, s2, p1, p2):
    p_C1 = (1/(s1*np.sqrt(2*np.pi))) * np.exp(-0.5*((X-mu1)/s1)**2) * p1
    p_C2 = (1/(s2*np.sqrt(2*np.pi))) * np.exp(-0.5*((X-mu2)/s2)**2) * p2
    p_X  = p_C1 + p_C2                      # evidencia (normalización)
    return p_C1/p_X, p_C2/p_X               # posteriors normalizados

def classify(p_C1, p_C2):
    return (p_C2 >= p_C1).astype(int)
```

### 5.4 Regresión Logística (comparación)

```python
from sklearn.linear_model import LogisticRegression
lr = LogisticRegression()
lr.fit(X_train.reshape(-1,1), y_train)
```

### 5.5 Evaluación de clasificadores

```python
from sklearn.metrics import accuracy_score, confusion_matrix
accuracy_score(y_test, pred)
confusion_matrix(y_test, pred)   # comparar targets vs modelo, y modelo vs modelo
```

---

## 6. Regresión: MC (mínimos cuadrados) vs. Bayesiana (MAP)

Idea central de la materia: **para modelos lineales con ruido Gaussiano, la solución MAP (punto máximo de la posterior) coincide con la solución de mínimos cuadrados.**

```python
from sklearn.linear_model import LinearRegression
modelo = LinearRegression()
modelo.fit(x_train.reshape(-1,1), y_train)
a_MC, b_MC = modelo.coef_[0], modelo.intercept_
# Si el modelo generativo es x = k*(y + c + eps), la solución MAP teórica es:
a_MAP, b_MAP = 1/k, -c
```

Se comparan graficando ambas rectas sobre los datos de test.

---

## 7. Inferencia Bayesiana avanzada (Unidad 7)

### 7.1 Divergencia KL entre Gaussianas

Se resuelve primero **analíticamente en papel** (log del cociente de densidades, integrar) y luego se verifica numéricamente comparando con la fórmula cerrada. Para el caso 1D:

```python
D_KL = np.log(sigma_q/sigma_p) + (sigma_p**2 + (mu_p-mu_q)**2)/(2*sigma_q**2) - 0.5
```

Propiedad a verificar: si ambas Gaussianas son iguales, D_KL = 0.

### 7.2 Inferencia variacional (posterior Gaussiana aproximada)

Cuando la posterior es Gaussiana, la aproximación variacional **coincide** con la posterior analítica exacta (media y varianza posteriores clásicas de un modelo Normal-Normal). Se implementa comparando la solución por optimización variacional contra la fórmula cerrada.

### 7.3 MCMC — Algoritmo de Metropolis

Patrón estándar de implementación:

```python
def likelihood(theta, data): ...
def prior(theta): ...
def posterior_no_normalizada(theta, data):
    return likelihood(theta, data) * prior(theta)

theta_actual = theta_inicial
muestras = []
for _ in range(n_iter):
    theta_prop = theta_actual + np.random.normal(0, paso)     # propuesta
    ratio = posterior_no_normalizada(theta_prop, data) / posterior_no_normalizada(theta_actual, data)
    if np.random.rand() < min(1, ratio):
        theta_actual = theta_prop
    muestras.append(theta_actual)
```

Clave: no hace falta normalizar la posterior (la constante se cancela en el ratio).

---

## 8. Comparación de Modelos (Unidad 9)

### 8.1 Modelos sin parámetros libres — Razón de verosimilitud / Bayes

Ejemplo clásico: test diagnóstico (sensibilidad, falsos positivos, prevalencia) → Teorema de Bayes planteado como comparación M1 (enfermo) vs M2 (sano):

```python
p_pos_M1 = sensibilidad          # P(+|enfermo)
p_pos_M2 = falsos_positivos      # P(+|sano)
p_M1, p_M2 = prevalencia, 1 - prevalencia

p_positivo = p_pos_M1*p_M1 + p_pos_M2*p_M2                 # evidencia total
p_M1_post = (p_pos_M1 * p_M1) / p_positivo                  # Bayes
p_M2_post = (p_pos_M2 * p_M2) / p_positivo
R = p_M1_post / p_M2_post                                   # razón de posteriors
```

### 8.2 Modelos con parámetros libres — Aproximación de Laplace

Comparar "moneda justa" (θ=0.5 fijo) vs "moneda sesgada" (θ libre) usando los datos (k caras en n tiros):

```python
from scipy.special import comb

# M1: verosimilitud con theta fijo
L_M1 = comb(n, k) * (0.5**k) * (0.5**(n-k))

# M2: MLE de theta y aproximación de Laplace
theta_hat = k / n
L_M2_mle = comb(n, k) * (theta_hat**k) * ((1-theta_hat)**(n-k))
prior_theta = 1.0                       # prior uniforme [0,1]

# Hessiano de la log-verosimilitud binomial en el MLE:
H = n / (theta_hat * (1 - theta_hat))

# Aproximación de Laplace (evidencia aproximada de M2):
L_M2 = L_M2_mle * prior_theta * np.sqrt(2*np.pi / H)

R = L_M1 / L_M2      # razón de verosimilitud marginal (Bayes factor aprox.)
```

Interpretación: R < 1 favorece M2 (moneda sesgada); R > 1 favorece M1 (moneda justa). El término `sqrt(2π/H)` es el "castigo por complejidad" (Occam) que penaliza el parámetro libre.

### 8.3 Ensamble pesado por accuracy

```python
accuracies = {m: (df[f'{m}_Prediccion'] == df['Y_Real']).mean() for m in modelos}
pesos = {m: acc / sum(accuracies.values()) for m, acc in accuracies.items()}

# Predicción del ensamble = voto pesado (redondeado a 0/1)
pred_ensamble = sum(pesos[m] * df[f'{m}_Prediccion'] for m in modelos).round()
accuracy_ensamble = (pred_ensamble == df['Y_Real']).mean()
```

---

## 9. Errores/detalles que rinden puntos en el parcial

- **`ddof=1`** en `np.std()` siempre que sea desvío **muestral** (default de numpy es `ddof=0`, poblacional → error clásico).
- **t vs z:** usar `t` cuando σ es desconocida (caso casi siempre en la práctica) y n es chico; `z` cuando se conoce σ o n es grande. Para tamaño de muestra se suele usar z.
- **Intervalo vs. test:** un IC que no contiene el valor de H0 es equivalente a rechazar Ho a ese nivel de significancia (con test de dos colas).
- **Pareado vs. independiente:** pareado reduce la varianza cuando las observaciones están naturalmente vinculadas (mismo sujeto, mismo bloque) — siempre justificar por qué corresponde.
- **`equal_var`** en `ttest_ind`: `True` = Student clásico (pooled), `False` = Welch (no asume varianzas iguales, más seguro por defecto).
- **ANOVA:** verificar supuestos (Shapiro, Levene) **antes** de confiar en el p-valor; si no se cumplen, hay alternativas no paramétricas (Kruskal-Wallis) aunque no se vieron en el curso.
- **Naive Bayes "naive":** asume independencia condicional entre features dado la clase — aclarar esta limitación si el dataset muestra correlación entre variables.
- **Bayes factor / Laplace:** cuantos más parámetros libres, mayor "penalización" automática (Occam) — por eso a veces el modelo simple gana aunque el complejo ajuste mejor en MLE.

---

## 10. Plantilla mental para resolver cualquier ejercicio del final

1. **Identificar el tipo:** ¿es IC, test de hipótesis, ANOVA, clasificación, o comparación de modelos?
2. **Identificar el escenario:** ¿una muestra, dos independientes, pareadas, más de dos grupos?
3. **Elegir la distribución:** t (σ desconocida) vs z (σ conocida/proporciones/n grande).
4. **Calcular "a mano"** con `scipy.stats` para mostrar el razonamiento.
5. **Verificar con la función de librería** correspondiente.
6. **Concluir en palabras**, relacionando el resultado numérico con la pregunta original del enunciado (¡esto es lo que más se pide explícitamente en los enunciados del curso!).