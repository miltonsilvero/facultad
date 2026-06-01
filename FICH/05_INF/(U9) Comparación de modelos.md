#### La Navaja de Ockham
> _"En igualdad de condiciones, la explicación más simple suele ser la más probable."_ Guillermo de Ockham.

Principio: cuando hay varias explicaciones de un fenómeno, preferir la más simple (no multiplicar entidades sin necesidad). La inferencia Bayesiana incorpora esto de forma matemáticamente natural.

---
#### Inferencia Bayesiana en el Modelado de Datos
El proceso general es:
1. Recolectar datos
2. Proponer modelos alternativos
3. Ajustar cada modelo a los datos ← entra la inferencia Bayesiana
4. Asignar preferencias a los modelos ← entra la inferencia Bayesiana
5. Decidir acciones / recolectar más datos / crear nuevos modelos

---
#### Dos Niveles de Inferencia
**Nivel 1 — Inferencia sobre los parámetros de un modelo $M$**

$$P(\theta \mid D, M) = \frac{P(D \mid \theta, M), P(\theta \mid M)}{P(D \mid M)}$$

|Término|Nombre|
|---|---|
|$P(\theta \mid D, M)$|Posterior sobre parámetros|
|$P(D \mid \theta, M)$|Likelihood|
|$P(\theta \mid M)$|Prior sobre parámetros|
|$P(D \mid M)$|Evidencia del modelo|

La evidencia se obtiene marginalizando sobre $\theta$:

$$P(D \mid M) = \int P(D \mid \theta, M), P(\theta \mid M), d\theta$$

**Nivel 2 — Inferencia sobre los modelos**

$$P(M \mid D) = \frac{P(D \mid M), P(M)}{P(D)}$$

La cantidad $P(D \mid M)$ es la evidencia del modelo y es el puente entre los dos niveles.

---
#### Comparación de Modelos SIN Parámetros Libres

Dado $M$ (ej. "enfermo") y $M'$ (ej. "sano") con datos $D$:

$$P(M \mid D) = \frac{1}{1 + \frac{1}{R}}$$

donde $R$ es el posterior odds ratio:

$$R = \frac{P(D \mid M), P(M)}{P(D \mid M'), P(M')}$$

El Factor de Bayes es:

$$BF = \frac{P(D \mid M)}{P(D \mid M')}$$

Si ambos modelos son equiprobables a priori, $R = BF$.
Para más de dos modelos (conociendo todos los posibles):

$$P(M_1 \mid D) = \frac{P(D \mid M_1), P(M_1)}{\sum_i P(D \mid M_i), P(M_i)}$$

> Si faltan modelos no se puede calcular posteriors, pero sí odd ratios entre pares.

**Ejemplo: Test de Cáncer de Mama**

| Dato                           | Valor   |
| ------------------------------ | ------- |
| $P(+\mid M)$ — sensibilidad    | $0.9$   |
| $P(+\mid M')$ — falso positivo | $0.07$  |
| $P(M)$ — prevalencia           | $0.008$ |

$$R = \frac{0.9 \times 0.008}{0.07 \times 0.992} \approx 0.104$$

$$P(M \mid +) = \frac{1}{1 + 1/R} \approx 0.09$$

Conclusión: aunque el test tiene 90% de sensibilidad, la probabilidad de tener cáncer dado positivo es solo ~9%, por la baja prevalencia.

---
#### Comparación de Modelos CON Parámetros Libres
La diferencia está en que la evidencia requiere integrar sobre $\theta$:

$$P(D \mid M) = \int P(D \mid \theta, M), P(\theta \mid M), d\theta$$

Esta integral puede ser intratable → se usa la **Aproximación de Laplace**.

---
#### Aproximación de Laplace
Aproximar la posterior por una Gaussiana alrededor del MAP:
**Paso 1** — Encontrar el MAP: $$\frac{d}{d\theta} \log P(\theta) \Big|_{\theta = \hat{\theta}} = 0$$
**Paso 2** — Calcular el Hessiano (curvatura): $$H = -\frac{d^2}{d\theta^2} \log P(\theta) \Big|_{\theta = \hat{\theta}}$$**Paso 3** — Posterior aproximada: $$P(\theta) \approx P(\hat{\theta}), \exp!\left(-\tfrac{1}{2}(\theta - \hat{\theta})^2 H\right) = P(\hat{\theta}), \mathcal{N}(\theta \mid \hat{\theta},, H^{-1})$$
##### Aplicado a la Evidencia

$$P(D \mid M) \approx \sqrt{\frac{2\pi}{H}}, P(D \mid \hat{\theta}, M), P(\hat{\theta} \mid M)$$

##### Descomposición del Ratio de Posteriors

$$\frac{P(M_1 \mid D)}{P(M_2 \mid D)} \approx \underbrace{\frac{P(D \mid \hat{\theta}_1, M_1)}{P(D \mid \hat{\theta}_2, M_2)}}_{\text{Likelihood Ratio}} \cdot \underbrace{\frac{P(M_1)}{P(M_2)}}_{\text{Prior Odds}} \cdot \underbrace{\sqrt{\frac{H_2}{H_1}}, \frac{P(\hat{\theta}_1 \mid M_1)}{P(\hat{\theta}_2 \mid M_2)}}_{\text{Factor de Occam}}$$

El Factor de Occam penaliza modelos complejos que "desparraman" probabilidad en muchas configuraciones. Incorpora la Navaja de Ockham automáticamente.

> Intuición: un modelo más simple (prior más concentrado) que igual explica los datos será favorecido sobre uno más general.

---
#### Comparación vs. Selección de Modelos
- **Selección** (_model selection_): Elegir UN modelo que mejor describa los datos.
- **Comparación** (_model comparison_): Determinar la probabilidad a posteriori de cada modelo candidato.

**Estimador Óptimo Bayesiano (promedio de modelos)**
Si no es necesario elegir un solo modelo, el estimador óptimo de una cantidad $c$ es:
$$\hat{c} = E[c \mid D] = \sum_{j=1}^{M} E[c \mid m_j, D] \cdot P(m_j \mid D)$$
Es decir: promedio ponderado por la posterior de cada modelo.

---
#### Ensambles Pesados en Machine Learning
Un ensamble combina predicciones de múltiples modelos:
- **Heterogéneo:** distintos tipos de modelos
- **Homogéneo:** mismo tipo, entrenado en variaciones de los datos (ej. bagging)

Un ensamble pesado asigna pesos según el desempeño de cada modelo:
$$\hat{y}_{\text{ensamble}} = \sum_{j=1}^{M} w_j \cdot \hat{y}_j \qquad \text{con} \qquad w_j = \frac{\text{Métrica}(m_j)}{\sum_k \text{Métrica}(m_k)}$$
> Un ensamble pesado es una aproximación práctica a la solución óptima Bayesiana de comparación de modelos.
---
#### Tabla de Notación

|Símbolo|Significado|
|---|---|
|$D$|Datos observados|
|$M, M'$|Modelos o hipótesis|
|$\theta$|Parámetros del modelo|
|$P(D \mid M)$|Evidencia del modelo (marginal likelihood)|
|$BF$|Factor de Bayes|
|$R$|Posterior odds ratio|
|$\hat{\theta}$|Estimador MAP (máximo a posteriori)|
|$H$|Hessiano de la log-posterior en el MAP|
