"El aprendizaje por refuerzo consiste en aprender qué hacer (cómo mapear situaciones a acciones) para maximizar una recompensa".

-*Búsqueda por prueba y error:* Al agente no se le dice qué acciones tomar, sino que debe descubrir qué acciones producen la mayor recompensa al ejecutarlas.
-*Recompensa retrasada:*  Las acciones pueden afectar no sólo a la recompensa inmediata sino también a la situación siguiente y a todas las recompensas posteriores.

***AxR vs IA tradicional:*** El primero supone que todo el espacio de estados se puede enumerar y almacenar en la memoria y no es necesario ningún modelo, mientras que la tradicional requiere de un modelo predefinido con transiciones y que asume determinismo.

-*Recompensa retrasada:* El agente debe aprender del estado actual s la acción óptima a para maximizar la función objetivo $a=\pi(s)$.
-*Exploración vs. Explotación:* el agente influye en la distribución de los ej de entrenamiento, eligiendo entre: explorar lo desconocido (nueva info) o explotar estados y acciones ya aprendidas (maximizar recompensa acumulada).
-*Estados parcialmente observables:* Los sensores solo pueden proporcionar info parcial.
-*Aprendizaje de largo plazo:* El agente debe aprender varias tareas relacionadas, usando la experiencia.

---
###### Elementos clave de aprendizaje por refuerzo
- **política$(\pi)$:** define la forma de comportarse del agente en un momento dado. Es un mapeo desde los estados percibidos del ambiente hacia las acciones que se deben tomar en esos estados.
- **Recompensa$(r)$:** define el objetivo del problema de aprendizaje. Asigna a cada estado o (a,s) percibido del entorno una recompensa que indica la deseabilidad intrínseca de ese estado.
- **Función de valor$(V)$:** especifica lo que es bueno a largo plazo. El valor de un estado es la cantidad total de recompensa que un agente puede esperar acumular en el futuro, a partir de ese estado.
- **modelo del ambiente:** es algo que imita el comportamiento del ambiente.
![[Pasted image 20260513025004.png]]
-*Supuestos habituales:* estados discretos, acciones finitas, tiempo discreto, transiciones estocásticas, observaciones perfectas, racionalidad.

---
###### Tarea de aprendizaje del agente
Ejecutar acciones en el ambiente, observar resultados y aprender la política de acciones $\pi\,\colon\,S\rightarrow A$ que maximiza la recompensa acumulada en el tiempo.
$E(r_t+\,\gamma_{t+1}+\,\gamma²r_{t+2}+\,...)$ y desde cualquier estado inicial S:
- $\gamma\,=\, 0$ : solamente se considera la r inmediata.
- $0\, =<\,\gamma\,<\,0$ : factor de descuento para r futuras.
- $\gamma\,=\,1$ : a la r futura se la da mayor importancia que a la inmediata.
Los ejemplos de entrenamiento son <(s,a),r> en vez de (s,a).

---
###### Política de acciones
- **Modelo de horizonte finito:** en un momento dado, el agente debe optimizar su recompensa esperada para los siguientes h pasos. $$E(\sum_{t=0}^h\,r_t)$$
- **Modelo de horizonte infinito descontado:** Tiene en cuenta la recompensa a largo plazo del agente, pero las recompensas futuras se descuentan según un factor de descuento γ.
$$E(\sum_{t=0}^\infty\,\gamma^tr_t)$$
- **Modelo de premio promedio:** El agente debe tomar acciones que optimicen su recompensa promedio a largo plazo.
$$\lim_{h\rightarrow\infty}\,E(\frac{1}{h}\sum_{t=0}^hr_t)$$

---
###### Función de valor
En un mundo determinístico, para cada política posible, se puede definir una función de evaluación sobre estados: $V^\pi\equiv\sum\gamma^ir_{t+i}$
La tarea del agente es aprender la política optima $\pi^*$ : $\pi^*\equiv argmax_\pi\,V^\pi(s)$ siendo $V^*(s)=V^{\pi^*}(s)$ la recompensa máximo obtenida a partir de s inicial.

---
El agente debe elegir el estado $s_1$ sobre el estado $s_2$ siempre que $V^*(s_1)>V^*(s_2)$ porque la r futura será mayor a partir de $s_1$. Pero el problema es que debe elegir entre acciones, no estados. La acción óptima en s es la acción que maximiza la suma de la recompensa inmediata r(s,a) más el valor V* del estado sucesor inmediato, descontado por γ:
$$r^*(s)= argmax_a[r(s,a)\,+\,\gamma V^*(\delta(s,a))]$$
-*Función Q:* si el agente la aprende, puede elegir la acción óptima sin saber $\delta$.
$Q(s,a)\equiv r(s,a)\,+\,\gamma V^*(\delta(s,a))$
Es la función de evaluación que el agente debe aprender. 
$\pi^*(s)=argmax_a\,Q(s,a)$
