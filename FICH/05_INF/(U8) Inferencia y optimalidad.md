
El resultado central de la inferencia bayesiana es la **distribución posterior**:

$$P(y \mid x) = \frac{P(x \mid y), P(y)}{P(x)}$$

- $P(y \mid x)$: **posterior** — lo que queremos conocer dado lo observado.
- $P(x \mid y)$: **likelihood** (verosimilitud) — modelo generativo de los datos.
- $P(y)$: **prior** — conocimiento previo sobre $y$.
- $P(x)$: constante de normalización (evidencia).

Muchas veces la posterior completa no es necesaria; basta con un **estimador puntual** $\hat{y}$ que minimice alguna función de costo.

> **Definición:** Un _estimador bayesiano_ es el estimador puntual que minimiza una función de costo (loss).

---

#### Estimadores Bayesianos en Regresión (variables continuas)

|Función de costo|Estimador óptimo|
|---|---|
|MSE: $\mathbb{E}[(\hat{y}(x) - y)^2]$|**Media** de la posterior|
|Norma L1: $\mathbb{E}[\lvert\hat{y}(x) - y\rvert]$|**Mediana** de la posterior|
|_Hit-or-miss_ (0-1)|**Moda** de la posterior = **MAP**|

**Estimador MSE → Media de la posterior**

$$\hat{y}(x) = \mathbb{E}[y \mid x] = \int y, P(y \mid x), dy$$

Si la posterior es aproximadamente normal, entonces media, mediana y moda coinciden, por lo que todos los estimadores dan el mismo resultado.

---

#### Ejemplo: Regresión Lineal vs. MAP
**Modelo generativo**

$$x = k(y + c + \epsilon), \quad \epsilon \sim \mathcal{N}(0, 1)$$
Con prior uniforme:

$$p(y) = \begin{cases} \frac{1}{10} & \text{si } 0 \leq y \leq 10 \ 0 & \text{en otro caso} \end{cases}$$

Objetivo: inferir $y$ a partir de observaciones de $x$.

**Solución por Mínimos Cuadrados (MC)**
Se busca $\hat{y}(x) = a_{MC},x + b_{MC}$, con:

$$a_{MC} = \frac{\text{Cov}(x,y)}{\text{Var}(x)} = \frac{1}{k}$$

$$b_{MC} = \bar{y} - \frac{\text{Cov}(x,y)}{\text{Var}(x)},\bar{x} = -c$$

**Solución Bayesiana (MAP)**
La posterior es proporcional a:

$$P(y \mid x) \propto P(x \mid y),P(y)$$
Donde:

$$P(x \mid y) = \frac{1}{\sqrt{2\pi k^2}} \exp!\left(-\frac{(x - k(y+c))^2}{2k^2}\right)$$

La posterior resultante (dentro del soporte de la prior) es:

$$P(y \mid x) = \begin{cases} C(x),\exp!\left(-\dfrac{(x - k(y+c))^2}{2k^2}\right) & \text{si } 0 \leq y \leq 10 \ 0 & \text{en otro caso} \end{cases}$$

Derivando e igualando a 0 para encontrar el MAP:

$$y_{MAP} = \begin{cases} 0 & \text{si } y^* < 0 \ y^* = \dfrac{x}{k} - c & \text{si } 0 \leq y^* \leq 10 \ 10 & \text{si } y^* > 10 \end{cases}$$

Dentro del dominio $[0, 10]$:

$$\hat{y}_{MAP}(x) = \frac{1}{k},x - c$$

> **Conclusión:** dentro del intervalo de la prior, el estimador MAP coincide exactamente con la solución de mínimos cuadrados ($a_{MAP} = \frac{1}{k}$, $b_{MAP} = -c$).

---

#### Inferencia Bayesiana en Clasificación (variables discretas)

Cuando $Y$ es una variable aleatoria discreta o categórica, se obtiene un **clasificador bayesiano**: calcula la posterior para cada clase y elige la que la maximiza.

$$\hat{y}(x) = \arg\max_y, P(y \mid x)$$

> **Propiedad clave:** Un clasificador bayesiano es óptimo porque minimiza la probabilidad media de clasificación errónea (riesgo o _risk_).

#### Relación con la accuracy
Para una función de pérdida 0-1:

$$\text{riesgo} = 1 - \mathbb{E}[\text{accuracy}]$$

Minimizar el riesgo equivale a maximizar el valor esperado de la accuracy.

---
#### Inferencia Bayesiana y Deep Learning

Tanto la inferencia bayesiana como el entrenamiento con Deep Learning son formas de aproximar soluciones óptimas en clasificación y regresión.
- Las funciones de costo habituales en DL buscan predecir un valor/clase de forma óptima → la solución óptima coincide con un estimador puntual de la posterior (ej. MAP).
- **Limitación del DL:** no hay garantía de que se represente correctamente _toda_ la posterior. En particular, los métodos de DL suelen tener dificultades para representar fielmente la **incerteza** de sus predicciones.

---
#### Inferencia Bayesiana en el Cerebro

>"Existe evidencia creciente de que el cerebro opera con distribuciones de probabilidad para realizar inferencias probabilísticas."

**Marco conceptual**
Dado un modelo generativo de la información sensorial:

$$P(\text{estímulo} \mid \text{latentes})$$

El cerebro parece capaz de invertir esta relación (vía regla de Bayes) para obtener:

$$P(\text{latentes} \mid \text{estímulo}) = \frac{P(\text{estímulo} \mid \text{latentes}), P(\text{latentes})}{P(\text{estímulo})}$$

|Término|Rol|
|---|---|
|$P(\text{latentes} \mid \text{estímulo})$|Lo que **percibimos**|
|$P(\text{estímulo} \mid \text{latentes})$|**Información sensorial**|
|$P(\text{latentes})$|**Conocimiento previo**|

#### Evidencia experimental
Experimento de integración visual-háptica: los sujetos combinan información de dos modalidades sensoriales con ruidos distintos. La estimación óptima de maximum likelihood predice que el peso de cada señal es inversamente proporcional a su varianza:

$$\hat{S} = \sum_i w_i \hat{S}_i, \quad w_i = \frac{1/\sigma_i^2}{\sum_j 1/\sigma_j^2}$$

La varianza combinada resulta:

$$\sigma_{VH}^2 = \frac{\sigma_V^2,\sigma_H^2}{\sigma_V^2 + \sigma_H^2}$$

Los datos experimentales confirman que el comportamiento humano sigue este modelo óptimo.