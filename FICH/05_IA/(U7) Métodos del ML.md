#### Aprendizaje No Supervisado — Clustering
**Clustering**: agrupar objetos similares basándose en alguna noción de similaridad. El resultado es una partición de los datos.
**Clúster**: conjunto de objetos similares entre sí, que difieren de los objetos en otros clústeres.

**Objetivo dual:**
- Minimizar la distancia **intra-clúster** (cohesión interna)
- Maximizar la distancia **inter-clúster** (separación entre grupos)

---
#### Métricas de Distancia
**Distancia Euclídea** entre dos puntos $p = (x_1, y_1)$ y $q = (x_2, y_2)$:
$$d_2(p, q) = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}$$
Generalización a $n$ dimensiones:
$$d_2(p, q) = \sqrt{\sum_{i=1}^{n}(q_i - p_i)^2}$$
**Distancia Manhattan:**

$$d_1(p, q) = \sum_{i=1}^{n} |q_i - p_i|$$
**Similitud Coseno** (ángulo $\alpha$ entre vectores):
$$\cos(\alpha) = \frac{p \cdot q}{|p| \cdot |q|}$$
---
#### Agrupamiento Jerárquico
Produce una jerarquía anidada de clústeres representada mediante un **dendrograma**.

| Enfoque                      | Descripción                                                                  |
| ---------------------------- | ---------------------------------------------------------------------------- |
| **Aglomerativo** (bottom-up) | Cada punto es su propio cluster; se fusionan iterativamente los más cercanos |
| **Divisivo** (top-down)      | Todos los puntos forman un único cluster; se divide iterativamente           |
#### Criterios de Enlace (Linkage)
**Single Linkage** — distancia mínima entre elementos de dos clústeres: $$D(C_1, C_2) = \min_{x \in C_1,, y \in C_2} d(x, y)$$**Complete Linkage** — distancia máxima: $$D(C_1, C_2) = \max_{x \in C_1,, y \in C_2} d(x, y)$$**Average Linkage** — promedio de todas las distancias entre pares: $$D(C_1, C_2) = \frac{1}{|C_1| \cdot |C_2|} \sum_{x \in C_1} \sum_{y \in C_2} d(x, y)$$**Centroid Method** — distancia entre centroides de los clústeres.
**Ward's Method** — fusiona los clústeres que minimizan el **incremento** en la varianza intra-clúster total.

---
#### K-Medias (K-Means)
Algoritmo de clustering no jerárquico y hard (cada punto pertenece exactamente a un clúster).
- Muy popular y simple
- Particiona los datos en $k$ clústeres
- Utiliza **centroides** como prototipos de cada clúster
- Minimiza la suma de distancias euclídeas al centroide

**Función objetivo (WCSS — Within Clúster Sum of Squares):**
$$J = \sum_{i=1}^{k} \sum_{x \in C_i} |x - \mu_i|^2$$
#### Algoritmo
Dado un conjunto de entrada $(\mathbf{x}_1, \mathbf{x}_2, \ldots, \mathbf{x}_n)$ y la cantidad de clusters $k$.
1. **Inicialización:** elegir $k$ centroides $\mu_i,\ i = 1, \ldots, k$ aleatoriamente.
2. **Asignación:** cada punto se asigna al centroide más cercano según $d(\mathbf{x}_j, \mu_i)$: $$C_i = { \mathbf{x}_j : d(\mathbf{x}_j, \mu_i) \leq d(\mathbf{x}_j, \mu_l),\ \forall l \neq i }$$
3. **Actualización:** recalcular cada centroide como la media de los puntos asignados: $$\mu_i = \frac{1}{|C_i|} \sum_{j \in C_i} \mathbf{x}_j, \quad \forall i$$
4. **Repetir** pasos 2 y 3 hasta que los centroides no se desplacen (convergencia).

| Ventajas                     | Limitaciones                                      |
| ---------------------------- | ------------------------------------------------- |
| Simple y rápido              | Requiere definir $k$ a priori                     |
| Escalable a grandes datasets | Sensible a la inicialización de centroides        |
| —                            | Converge a mínimos locales, no globales           |
| —                            | Asume clusters de forma esférica y tamaño similar |

---
#### Métricas de Validación de Clustering
Los algoritmos de clustering siempre producen un resultado, incluso sin estructura real en los datos → es necesario validar la calidad de la partición obtenida.

#### El Problema del $k$ Óptimo — Método del Codo (_Elbow Method_)
Se calcula una métrica de clustering (ej. WCSS) para un rango de valores de $k$ y se grafica la curva resultante. El $k$ óptimo se identifica en el punto de inflexión ("codo") de la curva, donde agregar más clusters deja de reducir significativamente el error.

#### Métricas Internas
Evalúan la calidad del clustering usando solo los datos y la partición obtenida (sin etiquetas externas).

|Métrica|Descripción|
|---|---|
|**Cohesión**|Compacidad interna de cada cluster (distancia intra-cluster)|
|**Separación**|Qué tan distintos son los clusters entre sí (distancia inter-cluster)|
|**Davies-Bouldin Index**|Promedio de la relación cohesión/separación para cada par de clusters. Menor es mejor.|
|**Silhouette Score**|Combina cohesión y separación por punto. Rango $[-1, 1]$; mayor es mejor.|

_Silhouette_ para un punto $i$:
$$s(i) = \frac{b(i) - a(i)}{\max{a(i),, b(i)}}$$
donde:
- $a(i)$ = distancia media de $i$ a los demás puntos de su clúster (cohesión)
- $b(i)$ = distancia media mínima de $i$ a los puntos de otro clúster (separación)

---
#### K Vecinos Más Cercanos (K-NN)
Algoritmo de aprendizaje supervisado para clasificación (y regresión). A diferencia de K-Medias, requiere datos etiquetados. Para clasificar un nuevo punto, se buscan los $k$ puntos más cercanos en el conjunto de entrenamiento y se asigna la clase mayoritaria entre ellos (votación).

**Algoritmo**
Dado un conjunto de entrenamiento etiquetado y un nuevo punto $\mathbf{x}^*$.
1. Calcular $d(\mathbf{x}^*, \mathbf{x}_i)$ para todos los puntos de entrenamiento.
2. Seleccionar los $k$ puntos con menor distancia.
3. Asignar la clase más frecuente entre los $k$ vecinos: $$\hat{y} = \arg\max_{c} \sum_{i \in \mathcal{N}_k(\mathbf{x}^*)} \mathbf{1}[y_i = c]$$

|$k$ pequeño|$k$ grande|
|---|---|
|Mayor sensibilidad al ruido (overfitting)|Fronteras de decisión más suaves|
|Alta varianza|Mayor sesgo|
|Riesgo de clasificar mal por outliers|Puede ignorar estructura local|

> Conviene usar valores impares de $k$ para evitar empates en clasificación binaria.

---
#### Comparación K-Medias vs K-NN

|Aspecto|K-Medias|K-NN|
|---|---|---|
|Tipo de aprendizaje|No supervisado|Supervisado|
|Requiere etiquetas|No|Sí|
|Objetivo|Descubrir grupos|Clasificar nuevos puntos|
|El parámetro $k$ significa|Número de clusters|Número de vecinos a consultar|
|Fase de entrenamiento|Iterativa (actualiza centroides)|Lazy (almacena todos los datos)|
