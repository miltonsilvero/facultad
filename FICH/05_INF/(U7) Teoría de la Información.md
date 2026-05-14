###### Entropía H(X)
Mide la incertidumbre de una variable aleatoria X ~ p(x).  $$H(X) = -\sum p(x)\,log_2\,p(x)$$
-Máxima: estados equiprobables.
-Mínima (cero): si la posibilidad de un estado es 1.

---
###### Información Mutua I(X;Y)
Mide la información compartida entre dos variables aleatorias.
			$I(X;Y)=-\sum p(x)\,log_2\,\frac{p(x,y)}{p(x)p(y)}$
			$I(X;Y)=H(X)-H(X|Y)=H(Y)-H(Y|X)$
Es simétrica: I(X;Y) = I(Y;X). Y si es 0, las variables son estadísticamente independientes.

---
###### Divergencia Kullback-Leibler 
Mide la similitud entre dos distribuciones de probabilidad P y Q.
		x discreta: $D_{KL}(P||Q)=\sum_{x\in X}\, P(x)\,log\,\frac{P(x)}{Q(x)}$ 
		x continua: $D_{KL}(P||Q)=\int_{-\infty}^{\infty}\, P(x)\,log\,\frac{P(x)}{Q(x)}\,dx$
-No es simétrica: DKL(P||Q)$\,\neq$ DKL(Q||P).
-DKL(P||Q) $>=$ 0.
-DKL(P||Q) = 0 $\Leftrightarrow$  P = Q.
![[Pasted image 20260511185644.png]]

---
###### ¿Cómo representar distribuciones de probabilidad?
-Probabilidades (o sus logaritmos). 
-Parámetros: media, covarianza, etc.
-Muestras.
Los dos primeros requieren muchos valores y el tercero, tiempo.

---
###### Inferencia Probabilística Aproximada
En inferencia bayesiana el objetivo es obtener la distribución posterior de las variables no observadas dado los datos. En general esto es intractable (la marginal likelihood P(x) = ∫ P(x|y) P(y) dy no se puede evaluar en la práctica). Hay dos métodos para aproximar la posterior:
*-Métodos variacionales*
*-Métodos por muestreo* 

---
###### Inferencia Variacional
La posterior de las variables latentes p(z|x) se aproxima mediante una distribución más sencilla q(z), llamada distribución variacional (pertenece a una familia paramétrica, ejemplo, Gaussianas, descrita por media y varianza).

El objetivo es encontrar q*(z), la distribución de la familia que más se parezca a p(z|x), minimizando la divergencia KL:
$$D_{KL}(p \| q) = \int q(z) \log\frac{q(z)}{p(z|x)}\, dz$$
Esto implica una optimización iterativa sobre los parámetros φ de q.
![[Pasted image 20260512202344.png]]

---
###### Evidence Lower Bound (ELBO)
![[Pasted image 20260512202553.png]]
$$log(p(x))= KL(q(z;φ)||p(z|x))\,+\,ELBO(q(z;φ))$$
Las q que maximizan el ELBO, minimizan la KL. Otra forma del ELBO es:
$$ELBO(q(z;φ))=E_{q(z;φ)}(log\,p(x,z))\,-\,E_{q(z;φ)}(log\,q(z;φ))$$
Son dos valores esperados con respecto a la distribución aproximada q. Si se pueden generar muestras de esa q, se lo puede estimar.

---
###### Aproximación de campo medio
Una suposición habitual es que las variables latentes son estadísticamente independientes: $q(z)=\prod_{i}\,q_i(z_i)$
Si se usa la familia de Gaussianas multivariadas, esta aproximación resulta en una matriz de covarianza diagonal:
$$\Sigma = \begin{pmatrix} \sigma_1^2 & & 0 \\ & \sigma_2^2 & \\ 0 & & \ddots & \sigma_n^2 \end{pmatrix}$$
*En diap. 42/117 hay un ejemplo de inferir la media a partir de n muestras.*

---
###### Autocodificadores Variacionales
***Autocodificadores clásicos (AE):*** Los autocodificadores buscan una representación compacta de la información, haciéndola pasar por un cuello de botella. Son clásicamente deterministas (no probabilísticos). Usan arquitectura Encoder–Decoder:
- *Encoder:* comprime la entrada en un espacio latente (cada entrada es un punto en el espacio latente).
- *Decoder:* reconstruye la entrada desde la representación latente.

***Autocodificadores Variacionales (VAE):*** En los VAEs se aprende simultáneamente el modelo de inferencia y el modelo generativo:

| Componente  | Rol                  | Distribución         |
| ----------- | -------------------- | -------------------- |
| **Encoder** | Modelo de inferencia | $q_\phi(z \mid x)$   |
| **Decoder** | Modelo generativo    | $p_\theta(x \mid z)$ |

En el VAE cada entrada es mapeada a una distribución en el espacio latente (no a un punto), típicamente una Gaussiana $\mathcal{N}(\mu, \sigma^2)$.
El encoder mapea x a los parámetros (μ, σ) de esa Gaussiana. Luego se muestrea z de esa distribución para pasarlo al decoder que intenta reconstruir la entrada original.

La función de pérdida es el ELBO del VAE:

$$\boxed{ELBO = \mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)] - D_{KL}(q_\phi(z|x)\|p(z))}$$

$$\underbrace{\mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)]}_{\text{Reconstrucción}} \quad - \quad \underbrace{D_{KL}(q_\phi(z|x)\|p(z))}_{\text{Regularización}}$$
- El *término de reconstrucción* equivale al error cuadrático entre x y su reconstrucción x̂.
- El *término de regularización* tiene forma analítica simple en función de μ y σ de las Gaussianas.
Con Gaussianas, la forma explícita es:

$$ELBO = \mathbb{E}_{q_\phi(z|x)}\!\left[-\tfrac{1}{2}(x-\hat{x})^2\right] - \frac{1}{2}\sum_{i=1}^{D}\left[1 + \log(\sigma^2_{\phi,i}(x)) - \mu^2_{\phi,i}(x) - \sigma^2_{\phi,i}(x)\right] - \tfrac{1}{2}\log(2\pi)$$
Se puede optimizar esta función con técnicas de gradiente descendiente.
El beneficio es que los VAEs generan representaciones suaves del espacio latente (a diferencia de los AE clásicos), lo que permite generar muestras nuevas interpolando en el espacio latente.

***β-VAEs:*** Se modifica la función de costo para fomentar el desentrelazamiento de las variables latentes:
![[Pasted image 20260512210425.png]]
*x*: imagen a construir.
*z:* latentes.
*p(z):* prior sobre las latentes que incentiva independencia lineal.

---
###### Inferencia por muestreo
Se trabaja directamente con muestras de la distribución posterior P(y|x), en lugar de encontrar una distribución analítica. Ventajas:
- *Marginalización trivial:* P(y₁) = ∫ P(y₁, y₂) dy₂ → se ignora una variable en las muestras.
- *Integrales como costos medios (Monte Carlo):*
Levantar muestras:

$$E[F]_{P(y|x)} = \int F(y)\,P(y|x)\,dy \approx \frac{1}{N}\sum_{i=1}^N F(y_i), \quad y_i \sim P(y|x)$$
Estimar la media a posteriori:

$$\bar{y}|x = E[y]_{P(y|x)} = \int y\,P(y|x)\,dy \approx \frac{1}{N}\sum_{i=1}^N y_i, \quad y_i \sim P(y|x)$$
Conocer varianza a posteriori:
$$s_y²|x=E((y-\bar{y})²)_{p(x|y)}=\int (y-\bar{y})²\,P(y|x)\,dy=\sum_{i}^N\frac{(y-\bar{y})²}{N},\,y_i\sim P(y|x)$$
Con N muestras independientes, la incerteza de la estimación decae como 1/√N.

---
###### Monte Carlo con Cadenas de Markov (MCMC)
MCMC es una clase de algoritmos para muestrear distribuciones de probabilidad. Se utilizan para aproximar la distribución a posteriori en inferencia bayesiana.
Se basan en construir una cadena de Markov cuya distribución estacionaria es la distribución deseada (la posterior).

**Balance detallado:** Un algoritmo de muestreo cumple balance detallado si el flujo de probabilidad entre dos puntos cualesquiera de la distribución es igual en ambas direcciones:

$$\boxed{P(y_1)\,K(y_1 \to y_2) = P(y_2)\,K(y_2 \to y_1)}$$

donde K(y₁ → y₂) es la probabilidad de transición entre y₁ e y₂.
Para posteriors, la condición se convierte en:

$$\frac{K(y_1 \to y_2)}{K(y_2 \to y_1)} = \frac{P(y_2|x)}{P(y_1|x)}$$

***Algoritmo de Metropolis-Hastings:*** Se define una distribución de propuesta Q(yᵗ, yᵗ⁺¹), de la que es fácil muestrear (ej, Gaussiana multivariada). No importa si P(y) es Gaussiana o no. Algoritmo (partiendo de un estado y₀):
1. Parados en yᵗ, muestreamos de Q para proponer un estado ỹ.
2. Calculamos la razón de aceptación:

$$\varrho = \frac{Q(\tilde{y},y^t)\,P(\tilde{y})}{Q(y^t,\tilde{y})\,P(y^t)}$$

   Si Q es simétrica (Q(ỹ, yᵗ) = Q(yᵗ, ỹ)):

$$\varrho = \frac{P(\tilde{y})}{P(y^t)}$$

3. Aceptamos con probabilidad p = min(1, ϱ):
   - Si aceptamos: yᵗ⁺¹ = ỹ
   - Si rechazamos: yᵗ⁺¹ = yᵗ
Con N muestras independientes, la incerteza decae como 1/√N.
El problema en MCMC las muestras están correlacionadas (muestras consecutivas están cerca), lo que reduce el número de muestras efectivas a:

$$N_{\text{efectivas}} = \frac{N}{\lambda}$$

donde λ es la longitud de autocorrelación: cuántos pasos hay que esperar para tener una muestra considerada independiente.
Trade-off: usar una Q con saltos más grandes baja λ pero aumenta el rechazo (se aleja de zonas de alta probabilidad).

---
###### El método de Langevin
Se define el potencial:

$$U(y) = -\log P(y|x) = -\log P(x|y) - \log P(y)$$

La dinámica de Langevin combina un gradiente del potencial (que empuja hacia zonas de alta probabilidad) con un ruido externo (que genera exploración aleatoria):

$$\boxed{y_{t+1} = y_t - \frac{dt}{2}\,\nabla_y U(y_t) + \sqrt{dt}\,z_t}$$
donde:
- *dt*: intervalo temporal entre muestras.
- *zₜ*: ruido muestreado de una distribución sencilla (ej, Gaussiana multivariada unitaria).
Sustituyendo la definición de U:

$$y_{t+1} = y_t + \frac{dt}{2}\nabla_y \log P(x|y_t) + \frac{dt}{2}\nabla_y \log P(y_t) + \sqrt{dt}\,z_t$$

-*Burn-in:* Al inicio, las muestras dependen de la posición inicial y no de la distribución. El tiempo que tarda en llegar al equilibrio se llama tiempo de burn-in. Las muestras del burn-in se descartan.
-*Mixing lento:* La cadena puede quedarse mucho tiempo muestreando solo una porción de la distribución. Cuando la distribución tiene varios modos muy separados, esto es especialmente problemático.
-*Solución – Inercia (Momentum):* Los métodos con momentum incorporan la noción física de inercia para que la partícula pueda escapar más fácilmente de un modo y recorrer mejor toda la distribución.


