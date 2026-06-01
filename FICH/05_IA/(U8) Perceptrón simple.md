#### Inspiración biológica
La neurona biológica posee:
- Soma (cuerpo celular), dendritas (entradas) y axón (salida).
- Comunicación vía sinapsis y neurotransmisores.
- Comportamiento todo-o-nada: si el potencial supera un umbral, dispara.
- Las sinapsis se refuerzan con el uso → base del aprendizaje.

---
#### Modelo de neurona artificial
Cada entrada $x_i$ se pondera con un peso $w_i$. La neurona computa la suma ponderada y la compara con un umbral $u$:
$$y = \varphi!\left(\sum_{i=1}^{N} w_i x_i - u\right)$$
**Entrada extendida (bias incorporado)**
Se agrega una entrada fija $x_0 = -1$ con peso $w_0 = u$:
$$y = \varphi!\left(\sum_{i=0}^{N} w_i x_i\right) = \varphi!\left(\langle \mathbf{w}, \mathbf{x} \rangle\right)$$
Esto simplifica la notación y elimina el umbral explícito.

---
#### Funciones de activación $\varphi(z)$

|Nombre|Fórmula|
|---|---|
|**Signo (escalón bipolar)**|$\text{sgn}(z) = \begin{cases} -1 & z < 0 \ +1 & z \geq 0 \end{cases}$|
|**Lineal a tramos**|$\text{sln}(z) = \begin{cases} -1 & z < -a \ \alpha z & -a < z < a \ +1 & z \geq a \end{cases}$|
|**Sigmoide bipolar**|$\text{sig}(z) = \dfrac{1 - e^{-az}}{1 + e^{-az}}$|
|Gaussiana / sinusoidal|Otras formas no lineales|

---
#### Perceptrón simple con 2 entradas — frontera de decisión
Para $N=2$ sin bias, la frontera es una recta que pasa por el origen:
$$w_1 x_1 + w_2 x_2 = 0 \implies x_2 = -\frac{w_1}{w_2},x_1$$
Con bias ($w_0$), la recta puede desplazarse:
$$w_1 x_1 + w_2 x_2 - w_0 = 0 \implies x_2 = \frac{w_0}{w_2} - \frac{w_1}{w_2},x_1$$
> El perceptrón simple sólo puede resolver problemas linealmente separables.

---
#### Algoritmo de aprendizaje (corrección de error)
**Regla de actualización general**
$$\boxed{w(n+1) = w(n) + \frac{\eta}{2},[y_d(n) - y(n)],x(n)}$$
donde $\eta$ es la tasa de aprendizaje y $y_d(n)$ es la salida deseada.

**Casos explícitos (con $x_i > 0$)**
- Si $y(n) = y_d(n)$ → no hay cambio (principio de mínima perturbación).
- Si $y(n) = +1$ y $y_d(n) = -1$ → $w(n+1) = w(n) - \eta,x(n)$
- Si $y(n) = -1$ y $y_d(n) = +1$ → $w(n+1) = w(n) + \eta,x(n)$

#### Algoritmo completo
1. **Inicializar** $w(1) \in [-0.5,\ 0.5]$ al azar.
2. Para cada ejemplo de entrenamiento ${x(n),, y_d(n)}$:
    - Calcular salida: $y(n) = \varphi(\langle \mathbf{w}(n), \mathbf{x}(n)\rangle)$
    - Actualizar pesos: $w(n+1) = w(n) + \dfrac{\eta}{2}[y_d(n) - y(n)],x(n)$
3. Repetir hasta satisfacer el criterio de parada.

---
#### Método de gradiente (LMS — Least Mean Squares)
**Criterio de error instantáneo**
$$\xi(n) = e^2(n) = [d(n) - y(n)]^2 = [d(n) - \langle \mathbf{w}(n), \mathbf{x}(n)\rangle]^2$$
**Gradiente respecto a los pesos**
$$\nabla_w, e^2(n) = -2,e(n),x(n)$$
**Ecuación de actualización (descenso por gradiente)**

$$w(n+1) = w(n) - \mu,\nabla_w,\xi(\mathbf{w}(n))$$

$$\boxed{w(n+1) = w(n) + 2\mu,e(n),x(n)}$$

donde $\mu$ es el paso de aprendizaje (learning rate).

> Esta regla es exacta en el caso lineal (neurona sin función de activación no lineal) y aproximada en el caso general → conduce al algoritmo de back-propagation para redes multicapa.

---
#### Limitación fundamental
El perceptrón simple no puede resolver el XOR ni ningún problema que no sea linealmente separable en el espacio de entrada. Esto motivó el desarrollo del perceptrón multicapa.