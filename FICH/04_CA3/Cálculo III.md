
## 1. Cónicas y superficies cuádricas

### 1.1 Cónicas (resumen de fórmulas)

| |Parábola|Elipse|Hipérbola|
|---|---|---|---|
|**Constantes**|$p$ = distancia del vértice al foco = distancia del vértice a la directriz|$2a$ = long. eje mayor = dist. entre vértices · $2b$ = long. eje menor · $2c$ = dist. entre focos · $c^2=a^2-b^2$|$2a$ = dist. entre vértices · $2c$ = dist. entre focos · $c^2=a^2+b^2$|
|**Ecuación (eje focal horizontal)**|$(y-k)^2=4p(x-h)$|$\dfrac{(x-h)^2}{a^2}+\dfrac{(y-k)^2}{b^2}=1$|$\dfrac{(x-h)^2}{a^2}-\dfrac{(y-k)^2}{b^2}=1$|
|**Ecuación (eje focal vertical)**|$(x-h)^2=4p(y-k)$|$\dfrac{(x-h)^2}{b^2}+\dfrac{(y-k)^2}{a^2}=1$|$\dfrac{(y-k)^2}{a^2}-\dfrac{(x-h)^2}{b^2}=1$|
|**Excentricidad**|$e=1$|$e=\dfrac{c}{a}<1$ (circunferencia: $e=0$)|$e=\dfrac{c}{a}>1$|
|**Cónica general sin término $xy$**: $Ax^2+Cy^2+Dx+Ey+F=0$|$AC=0$|$AC>0$ (circunferencia: $A=C$)|$AC<0$|

> [!note]- Casos degenerados
> 
> - **Parábola**: si $p=0$, degenera en una recta perpendicular a la directriz que pasa por el foco.
> - **Elipse**: si $c=0$ ($a=b$) degenera en circunferencia de radio $a$; si $c=a$ degenera en un segmento sobre el eje focal entre los vértices.
> - **Hipérbola**: si la distancia a los dos focos es igual, degenera en una recta perpendicular al eje focal que pasa por el centro.

> [!note]- Deducción de la parábola (forma canónica) Partiendo de $\text{Dist}(P,F)=\text{Dist}(P,L)$: $$\sqrt{(x-h)^2+(y-(k+p))^2}=\sqrt{(y-(k-p))^2}$$ Elevando al cuadrado y simplificando se llega a $(x-h)^2=4p(y-k)$.

### 1.2 Superficies cuádricas

Es la gráfica en el espacio de una ecuación de segundo grado en $x,y,z$: $$Ax^2+By^2+Cz^2+Dz+Fx+Gy=E$$

**Cilindros**: $F(x,y)=C$ → paralelo al eje $z$ · $G(x,z)=C$ → paralelo al eje $y$ · $H(y,z)=C$ → paralelo al eje $x$.

|Superficie|Ecuación|Trazas|
|---|---|---|
|Elipsoide|$\dfrac{x^2}{a^2}+\dfrac{y^2}{b^2}+\dfrac{z^2}{c^2}=1$|elipses en los 3 planos coordenados|
|Hiperboloide de una hoja|$\dfrac{x^2}{a^2}+\dfrac{y^2}{b^2}-\dfrac{z^2}{c^2}=1$|hipérbola en $xz$ e $yz$; elipse en $z=c$|
|Hiperboloide de dos hojas|$\dfrac{z^2}{c^2}-\dfrac{x^2}{a^2}-\dfrac{y^2}{b^2}=1$|—|
|Cono elíptico|$\dfrac{x^2}{a^2}+\dfrac{y^2}{b^2}=\dfrac{z^2}{c^2}$|—|
|Paraboloide elíptico|$z=\dfrac{x^2}{a^2}+\dfrac{y^2}{b^2}$|(multiplicado por $c$)|
|Paraboloide hiperbólico|$z=\dfrac{y^2}{b^2}-\dfrac{x^2}{a^2}$|(multiplicado por $c$) — silla de montar|

> [!question] Clase _(anotar acá el énfasis del profesor: identificación rápida de trazas, ejercicios típicos, etc.)_

---

## 2. Curvas en el espacio (funciones vectoriales)

### 2.1 Definiciones básicas

> [!abstract] Función vectorial $\mathbf{r}(t)=f(t)\mathbf{i}+g(t)\mathbf{j}+h(t)\mathbf{k}$, regla que asigna un vector a cada elemento de un dominio $D$.

> [!abstract] Límite $\lim_{t\to t_0}\mathbf{r}(t)=L$ si $\forall \varepsilon>0\ \exists\delta>0$ tal que $\forall t\in D$, $|\mathbf{r}(t)-L|<\varepsilon$ siempre que $0<|t-t_0|<\delta$.

- **Continuidad en $t_0$**: existe $\lim_{t\to t_0}\mathbf r(t)=\mathbf r(t_0)$.
- **Derivabilidad en $t_0$**: existe $\lim_{\Delta t\to 0}\dfrac{\mathbf r(t_0+\Delta t)-\mathbf r(t_0)}{\Delta t}$. Geométricamente, $\mathbf r'(t)$ es tangente a la trayectoria.
- **Parametrización suave**: $\mathbf r'(t)$ continua y $\neq 0$. Una curva es **suave** si admite al menos una parametrización suave, y **suave a trozos** si es unión de curvas suaves.

> [!success]- Propiedad: función vectorial de longitud constante Si $|\mathbf r(t)|=C\ \forall t$, entonces $\mathbf r\cdot \dfrac{d\mathbf r}{dt}=0$ (son ortogonales). **Demostración:** $|\mathbf r(t)|^2=C^2 \Rightarrow \dfrac{d}{dt}[\mathbf r(t)\cdot\mathbf r(t)]=0 \Rightarrow 2,\mathbf r'(t)\cdot\mathbf r(t)=0$.

### 2.2 Longitud de arco y rapidez

$$L=\int_a^b\sqrt{\left(\frac{dx}{dt}\right)^2+\left(\frac{dy}{dt}\right)^2+\left(\frac{dz}{dt}\right)^2},dt=\int_a^b|\mathbf v|,dt$$

- **Rapidez**: $\dfrac{ds}{dt}=|\mathbf v(t)|$.
- **Vector tangente unitario**: $\mathbf T=\dfrac{\mathbf v}{|\mathbf v|}$ (dirección de movimiento).

### 2.3 Curvatura y vectores normales

- **Curvatura**: $k=\left|\dfrac{d\mathbf T}{ds}\right|=\dfrac{1}{|\mathbf v|}\left|\dfrac{d\mathbf T}{dt}\right|$.
- **Vector normal unitario**: $\mathbf N=\dfrac{1}{k}\dfrac{d\mathbf T}{ds}=\dfrac{d\mathbf T/dt}{|d\mathbf T/dt|}$.
- **Círculo de curvatura** en $P$ (con $k\neq0$): tangente a la curva en $P$, misma curvatura que la curva en $P$, del lado cóncavo.

### 2.4 Componentes T y N de la aceleración

- **Vector binormal unitario**: $\mathbf B=\mathbf T\times\mathbf N$ (ortogonal a ambos, $|\mathbf B|=1$).
- Si $\mathbf a=a_T\mathbf T+a_N\mathbf N$: $$a_T=\frac{d^2s}{dt^2}=\frac{d|v|}{dt}\qquad a_N=k\left(\frac{ds}{dt}\right)^2=k|v|^2$$
    - $a_T$ mide el cambio de **rapidez**.
    - $a_N$ mide el cambio de **dirección** de $\mathbf v$.

> [!note]- Planos asociados al triedro T-N-B
> 
> - **Plano normal**: determinado por $\mathbf B,\mathbf N$ (ortogonal a $\mathbf T$).
> - **Plano rectificador**: determinado por $\mathbf T,\mathbf B$ (ortogonal a $\mathbf N$).
> - **Plano osculador**: determinado por $\mathbf T,\mathbf N$ (ortogonal a $\mathbf B$).

### 2.5 Torsión

> [!abstract] Definición Tasa de cambio de $\mathbf B$ respecto a la longitud de arco; mide qué tan rápido gira el plano osculador alrededor de $\mathbf T$. $$\tau=-\frac{d\mathbf B}{ds}\cdot\mathbf N$$

> [!success]- Deducción $\dfrac{d\mathbf B}{ds}=\dfrac{d(\mathbf T\times\mathbf N)}{ds}=\left(\dfrac{d\mathbf T}{ds}\times\mathbf N\right)+\left(\mathbf T\times\dfrac{d\mathbf N}{ds}\right)$. Como $\mathbf N\parallel d\mathbf T/ds$, el primer término es $0$. Entonces $d\mathbf B/ds=\mathbf T\times d\mathbf N/ds$, que resulta ortogonal a $\mathbf T$ y a $\mathbf B$ → paralelo a $\mathbf N$. Por eso $d\mathbf B/ds=-\tau\mathbf N$, y multiplicando por $\mathbf N$: $\tau=-\dfrac{d\mathbf B}{ds}\cdot\mathbf N$.

---

## 3. Funciones de varias variables

### 3.1 Definiciones de dominio

- $p$ es **punto interior** de $\Omega$ si $\exists\delta>0$ / $D(p,\delta)\subset\Omega$.
- $p$ es **punto frontera** de $\Omega$ si todo disco centrado en $p$ tiene puntos dentro y fuera de $\Omega$.
- $\Omega$ es **abierta** si $\Omega=\dot\Omega$ (coincide con su interior); **cerrada** si $Fr(\Omega)\subset\Omega$.
- $\Omega$ es **acotada** si cabe en un disco de radio finito.
- **Curva/superficie de nivel**: $f(x,y)=c$ o $f(x,y,z)=c$. **Curva de contorno**: intersección del plano $z=c$ con $z=f(x,y)$.

### 3.2 Límite y continuidad

> [!abstract] Límite $\lim_{(x,y)\to(x_0,y_0)}f(x,y)=L$ si $\forall\varepsilon>0\ \exists\delta>0$ tal que $|f(x,y)-L|<\varepsilon$ siempre que $0<\sqrt{(x-x_0)^2+(y-y_0)^2}<\delta$.

> [!abstract] Continuidad en $(x_0,y_0)$
> 
> 1. $f$ está definida en $(x_0,y_0)$
> 2. Existe $\lim_{(x,y)\to(x_0,y_0)}f(x,y)$
> 3. El límite coincide con $f(x_0,y_0)$

### 3.3 Derivadas parciales

$$f_x(x_0,y_0)=\lim_{h\to0}\frac{f(x_0+h,y_0)-f(x_0,y_0)}{h}\qquad f_y(x_0,y_0)=\lim_{h\to0}\frac{f(x_0,y_0+h)-f(x_0,y_0)}{h}$$

Interpretación geométrica: pendiente de la recta tangente a la curva intersección de $z=f(x,y)$ con $y=y_0$ (para $f_x$) o $x=x_0$ (para $f_y$).

> [!success] Teorema de Clairaut (derivada mixta) Si $f,f_x,f_y,f_{xy},f_{yx}$ están definidas en una región abierta que contiene a $(a,b)$ y son continuas allí, entonces $f_{xy}(a,b)=f_{yx}(a,b)$.

### 3.4 Diferenciabilidad

> [!abstract] Teorema del incremento Si $f_x,f_y$ están definidas en una región abierta $R\ni(x_0,y_0)$ y son continuas en $(x_0,y_0)$, entonces: $$\Delta z=f_x(x_0,y_0)\Delta x+f_y(x_0,y_0)\Delta y+\varepsilon_1\Delta x+\varepsilon_2\Delta y,\quad \varepsilon_1,\varepsilon_2\to0$$

> [!abstract] Diferenciabilidad $f$ es diferenciable en $(x_0,y_0)$ si $f_x,f_y$ existen y $\Delta z$ satisface la fórmula anterior. Es diferenciable si lo es en todo su dominio.
> 
> **Condición suficiente**: si $f_x,f_y$ son continuas en una región abierta $R$, entonces $f$ es diferenciable en $R$. **Consecuencia**: diferenciable $\Rightarrow$ continua (en ese punto).

> [!warning]- Contraejemplos clásicos (anexo) **C1**: $f(x,y)=|x|+|y|$. Es continua en $(0,0)$ pero $f_x(0,0)$ no existe (límites laterales distintos: $1$ y $-1$) → no diferenciable.
> 
> **C2**: $f(x,y)=1$ si $xy=0$, $0$ si $xy\neq0$. Existen $f_x(0,0)=f_y(0,0)=0$, pero $f$ **no es continua** en $(0,0)$ → no diferenciable (las derivadas parciales solo permiten calcular por definición, no garantizan nada).
> 
> **C3**: $f(x,y)=\dfrac{xy^2}{x^2+y^4}$ si $(x,y)\neq(0,0)$, $0$ si no. No es continua en $(0,0)$ (el límite depende del camino: por $y=0$ da $0$, por $x=0$... da valores distintos) → no diferenciable.
> 
> **C4**: $f(x,y)=(x^2+y^2)\sin!\left(\frac{1}{x^2+y^2}\right)$. **Es diferenciable** en $(0,0)$ (se verifica por definición con $\varepsilon_1,\varepsilon_2\to0$), **pero sus derivadas parciales no son continuas** en $(0,0)$ — muestra que la condición suficiente de diferenciabilidad no es necesaria.

### 3.5 Regla de la cadena y derivación implícita

> [!abstract] Derivación implícita Si $f(x,y)$ es derivable y $f(x,y)=0$ define a $y$ como función derivable de $x$, entonces donde $f_y\neq0$: $$\frac{dy}{dx}=-\frac{f_x}{f_y}$$

### 3.6 Derivadas direccionales y gradiente

> [!abstract] Derivada direccional $$\left(\frac{df}{ds}\right)_{u,p_0}=\lim_{s\to0}\frac{f(x_0+su_1,y_0+su_2)-f(x_0,y_0)}{s}$$ con $u$ vector unitario. Interpretación: pendiente de la tangente en la dirección de $u$; físicamente, tasa de cambio instantánea en esa dirección.

> [!abstract] Vector gradiente $$\nabla f=\frac{\partial f}{\partial x}\mathbf i+\frac{\partial f}{\partial y}\mathbf j$$ Es perpendicular a la curva de nivel que pasa por ese punto. La derivada direccional es un producto punto: $D_uf=\nabla f\cdot u=|\nabla f|\cos\theta$.

**Propiedades:**

1. $f$ crece más rápido en la dirección de $\nabla f$: $D_uf=|\nabla f|$.
2. $f$ decrece más rápido en la dirección de $-\nabla f$: $D_uf=-|\nabla f|$.
3. Toda dirección ortogonal a $\nabla f\neq0$ es de cambio nulo: $D_uf=0$.

### 3.7 Planos tangentes y diferenciales

> [!abstract] Plano tangente a la superficie de nivel $f(x,y,z)=C$ en $p_0$ Plano que pasa por $p_0$, normal a $\nabla f|_{p_0}$: $$f_x(p_0)(x-x_0)+f_y(p_0)(y-y_0)+f_z(p_0)(z-z_0)=0$$

> [!abstract] Recta normal en $p_0$ $$x=x_0+f_xt,\quad y=y_0+f_yt,\quad z=z_0+f_zt$$

> [!abstract] Caso $z=f(x,y)$ Plano tangente en $p_0=(x_0,y_0,f(x_0,y_0))$: $$z=f(p_0)+f_x(p_0)(x-x_0)+f_y(p_0)(y-y_0)$$

**Diferencial y aproximación lineal:**

- Estimación de cambio en dirección $u$: $df=(\nabla f_{p_0}\cdot u),ds$.
- Linealización: $L(x,y)=f(x_0,y_0)+f_x(x_0,y_0)(x-x_0)+f_y(x_0,y_0)(y-y_0)$; $f(x,y)\approx L(x,y)$.
- Error de la aproximación: si $M$ acota $|f_{xx}|,|f_{yy}|,|f_{xy}|$ en un rectángulo $R$: $E(x,y)\le \frac12 M(|x-x_0|+|y-y_0|)^2$.
- Diferencial total: $df=f_x(x_0,y_0),dx+f_y(x_0,y_0),dy$.

### 3.8 Extremos locales y puntos de silla

> [!abstract] Definiciones $f(a,b)$ es **máximo local** si $f(a,b)\ge f(x,y)$ en un disco abierto centrado en $(a,b)$; **mínimo local** si $\le$.

> [!success] Criterio de la primera derivada (Fermat) Si $f$ tiene extremo local en un punto **interior** $(a,b)$ y las primeras parciales existen, entonces $f_x(a,b)=f_y(a,b)=0$. Geométricamente: existe plano tangente horizontal. **Recíproco falso** (ej: $f=y^2-x^2$ en el origen).

- **Punto crítico**: punto interior donde $f_x=f_y=0$ o donde alguna no existe.
- **Punto de silla**: en todo disco alrededor de $(a,b)$ hay puntos con $f>f(a,b)$ y puntos con $f<f(a,b)$.

> [!success] Criterio de la segunda derivada (Hessiano) Con $f,f_x,f_y,f_{xx},f_{yy},f_{xy}$ continuas cerca de $(a,b)$ y $f_x(a,b)=f_y(a,b)=0$:
> 
> - $f_{xx}f_{yy}-f_{xy}^2>0$: **máximo** si $f_{xx}<0$; **mínimo** si $f_{xx}>0$.
> - $f_{xx}f_{yy}-f_{xy}^2<0$: **punto de silla**.
> - $=0$: criterio **no concluyente**.

### 3.9 Multiplicadores de Lagrange

> [!success] Teorema del gradiente ortogonal Si $f$ es derivable en una región cuyo interior contiene una curva suave $C:\mathbf r(t)$, y $p_0\in C$ es un extremo relativo de $f$ sobre $C$, entonces $\nabla f$ es ortogonal a $C$ en $p_0$.

> [!abstract] Método de Lagrange Sea $g$ continuamente diferenciable con $\nabla g\neq0$ sobre $g(x,y,z)=0$. Si $p_0$ maximiza/minimiza $f$ sujeta a $g=0$, entonces $\nabla f(p_0)\parallel\nabla g(p_0)$: existe $\lambda$ tal que $$\nabla f(p_0)=\lambda,\nabla g(p_0)$$ Interpretación: se buscan los puntos donde los gradientes de $f$ y $g$ son paralelos.

---

## 4. Integrales dobles

### 4.1 Definición y Teorema de Fubini

> [!abstract] Integral doble $f$ es integrable sobre $R$ si existe $\lim_{|p|\to0}\sum f(x_k,y_k)\Delta A_k=\iint_R f,dA$. Condición suficiente: $f$ continua. Si $f\ge0$, la integral es el **volumen** bajo $z=f(x,y)$ sobre $R$.

> [!success] Teorema de Fubini (rectángulos) Si $f$ es continua en $a\le x\le b,\ c\le y\le d$: $$\iint_R f,dA=\int_c^d\int_a^b f,dx,dy=\int_a^b\int_c^d f,dy,dx$$

> [!success] Teorema de Fubini (forma fuerte, regiones generales)
> 
> - Región tipo I ($a\le x\le b,\ g_1(x)\le y\le g_2(x)$): $\iint_R f,dA=\int_a^b\int_{g_1(x)}^{g_2(x)}f,dy,dx$
> - Región tipo II ($c\le y\le d,\ h_1(y)\le x\le h_2(y)$): $\iint_R f,dA=\int_c^d\int_{h_1(y)}^{h_2(y)}f,dx,dy$

### 4.2 Propiedades (válidas también para integrales triples)

> [!success]- 1. Linealidad / múltiplo constante $$\iint_R cf,dA=c\iint_R f,dA$$ Se prueba pasando la constante fuera del límite de la suma de Riemann.

> [!success]- 2. Suma y resta $$\iint_R[f\pm g],dA=\iint_R f,dA\pm\iint_R g,dA$$

> [!success]- 3. Dominación **a)** Si $f\ge0$ en $R$, $\iint_R f,dA\ge0$. **b)** Si $f\ge g$ en $R$, $\iint_R f,dA\ge\iint_R g,dA$ (se prueba con $h=f-g\ge0$).

> [!success]- 4. Aditividad Si $R=R_1\cup R_2$ sin solaparse: $\iint_R f,dA=\iint_{R_1}f,dA+\iint_{R_2}f,dA$.

### 4.3 Área y valor promedio

- **Área de $R$**: $A=\iint_R dA$.
- **Valor promedio**: $\dfrac{1}{\text{área }R}\iint_R f,dA$.

> [!success] Teorema del valor medio para integrales dobles Si $f$ es continua en $R$ cerrada, acotada y conexa, existe $(x_k,y_k)\in R$ tal que $f(x_k,y_k)\cdot\text{área}(R)=\iint_R f,dA$. _(Región conexa: dos puntos cualquiera se unen con una curva suave contenida en la región.)_

### 4.4 Integrales dobles en polares

$$x=r\cos\theta,\quad y=r\sin\theta,\quad dA=r,dr,d\theta$$ $$\text{Área}(R)=\iint_R r,dr,d\theta$$

Conversión: $r=\sqrt{x^2+y^2}$, $\theta=\arctan(y/x)$.

---

## 5. Integrales triples

$$V(D)=\iiint_D dV\qquad f_{\text{prom}}=\frac{1}{V(D)}\iiint_D f,dV$$

> [!success] Teorema de Fubini (triples) $$\iiint_D f,dV=\int_a^b\int_{g_1(x)}^{g_2(x)}\int_{z_1(x,y)}^{z_2(x,y)} f,dz,dy,dx$$

**Condiciones**: $f$ continua; $D$ cerrado, acotado y con frontera suave a trozos.

### 5.1 Coordenadas cilíndricas

$$x=r\cos\theta,\ y=r\sin\theta,\ z=z \qquad dV=r,dz,dr,d\theta$$

- $r=k$: cilindro de radio $k$ (eje $z$ si $r=0$).
- $\theta=\theta_0$: semiplano que arranca en el eje $z$.
- $z=z_0$: plano horizontal.

### 5.2 Coordenadas esféricas

$$x=\rho\sin\phi\cos\theta,\ y=\rho\sin\phi\sin\theta,\ z=\rho\cos\phi \qquad dV=\rho^2\sin\phi,d\rho,d\phi,d\theta$$

Rango usual: $\rho\ge0,\ 0\le\phi\le\pi,\ 0\le\theta\le2\pi$.

- $\rho=a$: esfera de radio $a$.
- $\phi=\phi_0$: cono con eje $z$ (abre hacia arriba si $0\le\phi\le\pi/2$, hacia abajo si $\pi/2\le\phi\le\pi$; $\phi=\pi/2$ da el plano $xy$).
- $\theta=\theta_0$: semiplano vertical.

> [!tip] Conversión rápida esféricas↔cilíndricas $r=\rho\sin\phi,\quad z=\rho\cos\phi,\quad \theta=\theta$

---

## 6. Integrales de línea

### 6.1 Integral de línea escalar

Curva suave $C: \mathbf r(t)=\langle x(t),y(t),z(t)\rangle,\ a\le t\le b$. Si $f$ es continua sobre $C$: $$\int_C f,ds=\int_a^b f(\mathbf r(t)),|\mathbf r'(t)|,dt$$

Interpretación: si $f\ge0$, es el área de la "cortina" vertical sobre $C$ con altura $f$.

**Propiedades:**

- Si $C=C_1\cup C_2$ (suave a trozos): $\int_C f,ds=\int_{C_1}f,ds+\int_{C_2}f,ds$.
- Recorrer en sentido creciente de $t$ da signo positivo; invertir el sentido cambia el signo: $\int_{-C}f,ds=-\int_C f,ds$.
- Es independiente de la parametrización elegida.

### 6.2 Integral de línea de un campo vectorial

Campo $\mathbf F=\langle P,Q,R\rangle$ continuo sobre $C:\mathbf r(t)$: $$\int_C \mathbf F\cdot d\mathbf r=\int_a^b \mathbf F(\mathbf r(t))\cdot \mathbf r'(t),dt=\int_C P,dx+Q,dy+R,dz$$

**Trabajo**: $w=\int_C \mathbf F\cdot d\mathbf r$ (fuerza que varía en magnitud y dirección a lo largo de $C$).

**Circulación** (si $\mathbf F$ es campo de velocidades de un fluido y $C$ es cerrada simple): $$\text{circulación}=\oint_C \mathbf F\cdot d\mathbf r=\oint_C \mathbf F\cdot \mathbf T,ds$$

- $\mathbf F\cdot\mathbf T=0$ en todo punto: no hay circulación.
- Componente tangencial neta positiva/negativa: circulación positiva/negativa.

**Campo gradiente / conservativo**: $\nabla F(x,y)=F_x\mathbf i+F_y\mathbf j$. $\mathbf F$ es **conservativo** en $D$ si $\exists, \phi$ (función potencial) tal que $\mathbf F=\nabla\phi$ en $D$.

> [!warning] Importante Siempre hay que indicar en qué región $D$ se afirma que un campo es conservativo.

### 6.3 Teorema Fundamental de las integrales de línea

> [!success] Enunciado Sea $\mathbf F=\nabla\phi$ conservativo en $D$, y $C$ suave (o suave a trozos) contenida en $D$ que va de $A$ a $B$: $$\int_C \mathbf F\cdot d\mathbf r=\phi(B)-\phi(A)$$ **Hipótesis**: $D$ abierta; $\phi$ diferenciable en $D$; $\mathbf F=\nabla\phi$; $C\subset D$ suave a trozos.

> [!success]- Demostración Con $\mathbf r(a)=A,\ \mathbf r(b)=B$: $$\int_C \mathbf F\cdot d\mathbf r=\int_a^b \nabla\phi(\mathbf r(t))\cdot \mathbf r'(t),dt=\int_a^b \frac{d}{dt}\phi(\mathbf r(t)),dt=\phi(\mathbf r(b))-\phi(\mathbf r(a))$$ (se usó la regla de la cadena).

### 6.4 Independencia de la trayectoria

> [!abstract] Definición $\int_C\mathbf F\cdot d\mathbf r$ es independiente de la trayectoria en $D$ si para cualesquiera $C_1,C_2\subset D$ con mismos extremos $A,B$: $\int_{C_1}=\int_{C_2}$.

> [!success] Conservativo $\Rightarrow$ independencia de trayectoria Si $D$ es abierta y conexa y $\mathbf F$ es conservativo en $D$, entonces $\int_C\mathbf F\cdot d\mathbf r$ es independiente de la trayectoria.

> [!success]- Independencia de trayectoria $\Leftrightarrow$ integral nula en curvas cerradas Sea $\mathbf F$ continua en $D$ abierta y conexa. $\int_C\mathbf F\cdot d\mathbf r$ es independiente de la trayectoria $\iff \oint_C \mathbf F\cdot d\mathbf r=0$ para toda curva cerrada $C\subset D$. **Ida**: si es independiente de la trayectoria, en una curva cerrada el punto inicial=final, entonces la integral vale $0$. **Vuelta**: con $C=C_1\cup(-C_2)$ cerrada, $0=\oint_C=\int_{C_1}-\int_{C_2}\Rightarrow\int_{C_1}=\int_{C_2}$.

> [!success]- Conservativo $\iff$ independencia de trayectoria (equivalencia completa) **Hipótesis**: $D$ abierta y conexa; $\mathbf F$ continua en $D$. **($\Rightarrow$)** Usa el Teorema Fundamental. **($\Leftarrow$)** Se fija $A\in D$ y se define $\phi(P)=\int_A^P \mathbf F\cdot d\mathbf r$ (bien definida por independencia del camino). Se prueba $\nabla\phi=\mathbf F$, calculando $\partial\phi/\partial x$ y $\partial\phi/\partial y$ por separado usando el 2do Teorema Fundamental del Cálculo sobre segmentos de recta auxiliares.

### 6.5 Criterios de conservatividad

> [!success] Criterio en $\mathbb R^2$ Sea $\mathbf F=P\mathbf i+Q\mathbf j$ con $P,Q\in C^1(D)$, $D$ abierta y **simplemente conexa**: $$\mathbf F \text{ conservativo en } D \iff P_y=Q_x$$

> [!success]- Demostración (ambos sentidos) **Ida**: si $\mathbf F=\nabla\phi$, entonces $P=\phi_x,\ Q=\phi_y$. Por Clairaut, $\phi_{xy}=\phi_{yx}\Rightarrow P_y=Q_x$. **Vuelta**: si $P_y=Q_x$, entonces $Q_x-P_y=0$. Para $C$ cerrada simple en $D$ (simplemente conexa), por Green: $\oint_C Pdx+Qdy=\iint_R(Q_x-P_y)dA=0$. Como vale para toda curva cerrada, la integral es independiente de la trayectoria y $\mathbf F$ es conservativo.

> [!success] Criterio en $\mathbb R^3$ Sea $\mathbf F=P\mathbf i+Q\mathbf j+R\mathbf k$ con $P,Q,R\in C^1(D)$: $$\mathbf F\text{ conservativo en }D\ \Rightarrow\ \text{rot},\mathbf F=\nabla\times\mathbf F=\mathbf 0$$ El **recíproco** vale si $D\subset\mathbb R^3$ es abierta y **simplemente conexa**.

> [!success]- Demostración Si $\mathbf F=\nabla\phi$: $P=\phi_x,\ Q=\phi_y,\ R=\phi_z$. El rotacional es $\langle R_y-Q_z,,P_z-R_x,,Q_x-P_y\rangle$; sustituyendo y usando Clairaut (igualdad de derivadas mixtas), cada componente da $0$.
> 
> **Condición necesaria y suficiente (versión Zill)**: si además $D$ es abierta y simplemente conexa, rot $\mathbf F=0 \iff \mathbf F$ es conservativo. Componente a componente esto equivale a $R_y=Q_z,\ R_x=P_z,\ Q_x=P_y$.
> 
> ⚠️ Si $D$ es **solo abierta** (no simplemente conexa), la vuelta puede fallar — existe contraejemplo (ver libro, ej. 2 pág. 846).

---

## 7. Teorema de Green

> [!success] Enunciado Sea $C$ curva cerrada, simple, suave a trozos, orientada positivamente, que encierra $D$ (región plana). Si $P,Q$ tienen derivadas parciales continuas en una región abierta que contiene a $D$: $$\oint_C P,dx+Q,dy=\iint_D (Q_x-P_y),dA$$

> [!note] Orientación positiva La región queda a la izquierda al recorrer la curva. Para región simple: sentido **antihorario**. Con agujeros: borde exterior antihorario, bordes interiores horario.

> [!success]- Demostración (para región tipo I y tipo II simultáneamente) **Región tipo I** ($a\le x\le b,\ g_1(x)\le y\le g_2(x)$): usando Fubini + 2do TFC: $$\oint_C P,dx=-\iint_D P_y,dA$$ **Región tipo II** ($c\le y\le d,\ h_1(y)\le x\le h_2(y)$): $$\oint_C Q,dy=\iint_D Q_x,dA$$ Sumando: $\oint_C P,dx+Q,dy=\iint_D(Q_x-P_y),dA$.

### 7.1 Green en regiones con agujeros / múltiplemente conexas

Si $D$ tiene borde exterior $C_2$ e interior $C_1$ (orientados positivamente respecto de $D$): $$\oint_{\partial D} P,dx+Q,dy=\iint_D(Q_x-P_y),dA,\qquad \partial D=C_2\cup(-C_1)$$

**Sustitución de trayectorias**: si $Q_x-P_y=0$ en la región anular entre $C_1$ y $C_2$ (ambas orientadas antihorario): $$\oint_{C_2}P,dx+Q,dy=\oint_{C_1}P,dx+Q,dy$$

### 7.2 Área mediante Green

$$A(D)=\frac12\oint_C x,dy-y,dx=\oint_C x,dy=-\oint_C y,dx$$

> [!success]- Demostración Con $P=-y/2,\ Q=x/2$: $Q_x-P_y=1$. Por Green, $\oint_C -\frac{y}{2}dx+\frac{x}{2}dy=\iint_D 1,dA=A(D)$.

---

## 8. Superficies paramétricas

$$\mathbf r(u,v)=\langle x(u,v),y(u,v),z(u,v)\rangle,\quad (u,v)\in R$$

- **Vectores tangentes**: $\mathbf r_u=\partial\mathbf r/\partial u,\ \mathbf r_v=\partial\mathbf r/\partial v$.
- **Superficie suave** en $(u_0,v_0)$: $\mathbf r_u\times\mathbf r_v\neq0$ ahí (suave sobre $R$ si vale $\forall(u,v)\in R$).
- **Plano tangente en $P_0$**: pasa por $P_0$, normal $\mathbf r_u(u_0,v_0)\times\mathbf r_v(u_0,v_0)$.

### 8.1 Área de superficies

> [!abstract] Superficie paramétrica general $$A(S)=\iint_R |\mathbf r_u\times\mathbf r_v|,du,dv$$

> [!success]- Justificación Un rectángulo $du,dv$ del plano de parámetros se transforma en un paralelogramo generado por $\mathbf r_u,du,\ \mathbf r_v,dv$, cuya área es $|\mathbf r_u\times\mathbf r_v|,du,dv$. Sumando los aportes se obtiene la fórmula.

> [!abstract] Superficie gráfica $z=g(x,y)$ $$A(S)=\iint_D \sqrt{1+g_x^2+g_y^2},dA$$

> [!success]- Justificación Parametrizando $\mathbf r(x,y)=\langle x,y,g(x,y)\rangle$: $\mathbf r_x=\langle1,0,g_x\rangle,\ \mathbf r_y=\langle0,1,g_y\rangle$, y $\mathbf r_x\times\mathbf r_y=\langle-g_x,-g_y,1\rangle$, cuya norma es $\sqrt{1+g_x^2+g_y^2}$.

---

## 9. Integrales de superficie

### 9.1 Integral escalar

$$\iint_S f,dS=\iint_R f(\mathbf r(u,v)),|\mathbf r_u\times\mathbf r_v|,du,dv$$

Si $S$ es gráfica $z=g(x,y)$: $$\iint_S f,dS=\iint_D f(x,y,g(x,y))\sqrt{1+g_x^2+g_y^2},dA$$

### 9.2 Flujo de un campo vectorial

$$\iint_S \mathbf F\cdot \mathbf n,dS=\iint_R \mathbf F(\mathbf r(u,v))\cdot(\mathbf r_u\times\mathbf r_v),du,dv$$

(si $\mathbf r_u\times\mathbf r_v$ apunta en la orientación pedida; si no, se cambia el signo.)

**Superficie gráfica** $z=g(x,y)$:

- Orientada **hacia arriba**: $\mathbf n,dS=\langle-g_x,-g_y,1\rangle,dA$
- Orientada **hacia abajo**: $\mathbf n,dS=\langle g_x,g_y,-1\rangle,dA$

**Relación divergencia-flujo**: el flujo de $\mathbf F$ a través de un área $\Delta S$ es aprox. $\text{comp}_n\mathbf F\cdot\Delta S=(\mathbf F\cdot\mathbf n),ds$.

---

## 10. Rotacional y divergencia

$$\text{rot},\mathbf F=\nabla\times\mathbf F=\left\langle R_y-Q_z,\ P_z-R_x,\ Q_x-P_y\right\rangle$$ $$\text{div},\mathbf F=\nabla\cdot\mathbf F=P_x+Q_y+R_z$$

> [!note] Interpretaciones físicas
> 
> - **rot F**: mide la tendencia local del fluido a girar (eje de rotación = dirección de rot F, sentido por regla de la mano derecha). Si rot $\mathbf F=0$: el fluido es **irrotacional**.
> - **div F**: flujo neto saliente por unidad de volumen.
>     - $\text{div},\mathbf F(P)>0$: $P$ es **fuente**.
>     - $\text{div},\mathbf F(P)<0$: $P$ es **sumidero**.
>     - $\text{div},\mathbf F(P)=0$: no hay fuentes ni sumideros locales.

> [!success] Propiedad: $\nabla\cdot(\nabla\times\mathbf F)=0$ Si $\mathbf F$ tiene derivadas parciales segundas continuas: $\text{div}(\text{rot},\mathbf F)=0$.

> [!success]- Demostración $\text{rot},\mathbf F=\langle R_y-Q_z,,P_z-R_x,,Q_x-P_y\rangle$. Entonces: $$\text{div}(\text{rot},\mathbf F)=(R_y-Q_z)_x+(P_z-R_x)_y+(Q_x-P_y)_z$$ $$=(R_{yx}-R_{xy})+(Q_{xz}-Q_{zx})+(P_{zy}-P_{yz})=0$$ por igualdad de derivadas mixtas (Clairaut).
> 
> **Uso típico**: si $\mathbf H=\text{rot},\mathbf G$ para algún campo $\mathbf G$, entonces necesariamente $\text{div},\mathbf H=0$. Por lo tanto, si $\text{div},\mathbf H\neq0$, $\mathbf H$ **no puede ser** el rotacional de otro campo — útil como test rápido de descarte.

---

## Checklist de repaso antes del examen

- [ ] Identificar cónicas y cuádricas a partir de la ecuación (sin graficar todo)
- [ ] Curvatura, torsión, T-N-B: saber deducir, no solo aplicar fórmula
- [ ] Diferenciabilidad vs. existencia de parciales vs. continuidad (repasar los 4 contraejemplos C1-C4)
- [ ] Extremos con Hessiano + Lagrange con restricciones
- [ ] Cambio de variable en integrales dobles/triples (polares, cilíndricas, esféricas) — practicar identificar límites de integración
- [ ] Condición de conservatividad: **ojo con simplemente conexa**, es la parte que más se olvida
- [ ] Green: aplicación directa + versión para regiones con agujeros + cálculo de área
- [ ] Superficies paramétricas: orientación de la normal (arriba/abajo) antes de calcular flujo
- [ ] rot/div: usar $\text{div(rot F)}=0$ como atajo de verificación en parciales