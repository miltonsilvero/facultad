
### Representación del conocimiento
La sentencia almacenada en la BC debe incluir la percepción y el tiempo en que esta fue obtenida. El agente necesita saber cuándo vio qué cosa.

---
***Sentencias diacrónicas:*** permiten razonar a través del tiempo.
%% En(agente, celda(1,1),3) + accion(avanzar,3) -> En(agente, celda(2,1),4) %%

***Cálculo situacional:*** método de representación que permite razonar sobre los resultados de las acciones de un agente basado en conocimiento.

***Situación:*** denota los estados resultantes de ejecutar acciones.
%%s=Resultado(a,s-1) %%

---
##### Definiciones Cálculo situacional
- *Acciones:* términos lógicos (girar, tomar).
- *Situación:* términos lógicos con situación inicial y todas las situaciones generadas a partir de aplicar una acción a una situación.
- *Flujos:* predicados que varían de una situación a otra.
- *Predicados atemporales:* no varían entre las situaciones.
###### EJ: Wumpus
Esquema de percepciones flexibles, desacoplando percepciones de acciones.
*$\forall$ O,B,U,C,T Percepción((O,B,resplandor,U,C), T) -> Oro(T)*
*$\forall$  T Oro(T) -> Acción(tomar, T)* 

El mundo es considerado una secuencia de acciones.

---
###### Cambios en el mundo
Se pueden optimizar los agentes al permitir reglas que hagan inferencias a percepciones pasadas así como las actuales. Las situaciones son generadas a partir de situaciones previas mediante la aplicación de acciones.

Cada relación que puede cambiar con el tiempo se maneja con un argumento situacional extra en el predicado. *En(Agente, Ubicación, Situación)* este último es el parámetro situacional.

Para definir el como cambia el mundo, se utiliza: *Resultado(Acción, Situación)*
**EJ:** *Resultado(avanzar, s0) = s1.*

***Axiomas de efecto:*** describen los cambios que ocurren en el estado del mundo como consecuencia de una acción, pero no indican qué cosas no cambias y permanecen entre dos situaciones. Se expresan como reglas que conectan una acción realiza en una situación con el resultado obtenido.
$\forall$ S,X,Y,O sostiene(O,S) $\land$ en(O,X,S) $\land$ Adyacente(X,Y) -> en(O,Y,Resultado(avanzar,S))

***Axiomas de marco:*** describen qué cosas no cambian en el mundo al realizarse una acción. Por ejemplo, en el mundo del Wumpus, si el agente está sosteniendo el oro y se realiza una acción distinta a soltar, es necesario representar que el agente lo sigue sosteniendo.

***Axiomas de estado sucesor:*** describe un predicado P que puede variar de situación en situación. P es cierto luego de la aplicación de una acción. 
Se debe establecer uno de estos axiomas por cada uno de los predicados que cambian en el tiempo y cada uno de estos debe listas todas las formas que P puede ser cierto y puede ser falso. 

---
***Reglas causales:*** expresan que ciertas propiedades ocultas del dominio producen determinadas percepciones. 
***EJ:***  $\forall$ *I1 Wumpus(I1) -> ($\forall$ I2 Adyacente(I1,I2) -> Olor(I2))* 

***Reglas de diagnóstico:*** infieren ciertas propiedades del dominio a partir de info. obtenida de determinadas percepciones. 
***EJ:*** $\forall$ *I, s En(Agente,I,s) $\land$ Olor(s) -> apesta(I)* 

---
###### Ranking y categorización 
Es conveniente separar los hechos acerca de las acciones de los hechos acerca de los objetivos, implica que el agente puede ser reprogramado al pedirle que consiga algo diferente. Hay un sistema de acción-valor para ver cuan deseable es:
![[Pasted image 20260511113249.png]]





### Introducción a los Sistemas Expertos
![[Pasted image 20260511113542.png]]
- ***MI (Máquina de inferencia)***
- ***BC (Base de conocimiento)*** 
- ***MP (Memoria de producciones):*** constituida por un conjunto de reglas lógicas con la estructura IF(antecedentes) THEN (consecuentes).
- ***MT (Memoria de trabajo):*** contiene un conjunto de literales positivas que no contienen variables. *EJ:* (perro tiene pelo).

Se comienza desde hechos (sentencias atómicas) de la MT y se infiere añadiendo los hechos nuevos hasta que no se pueda inferir más o que el objetivo haya sido agregado a la BC. Cada inferencia utiliza *Modus Ponens.* 

---
###### Fases
1. ***Match (fase de cotejo):*** Se compara cada elemento de la premisa (antecedentes) de las reglas almacenadas en la MP con el contenido actual de la MT.  
   Se buscan hechos en la MT que hagan verdadera la premisa de una regla, unifica variables de los antecedentes con los hechos presentes. Como resultado, se genera un conjunto de conflicto que es el grupo de todas las reglas cuyas condiciones se satisfacen con la info. actual de la memoria.
   ***EJ:*** Dada la regla *pelos(x) -> mamífero(x)* y la MT con *pelos(Matilde)*, la fase de cotejo unifica *?x* con *Matilde*, incorporando esa regla al conjunto de conflicto.
   
2. ***Resolución de conflictos:*** Se decide cuál de las reglas contenidas en el conjunto de conflicto se va a ejecutar. Hay varios criterios:
   -Especificidad: prioriza la regla que sea más específica del caso actual.
   -Novedad: selecciona las que se satisfacen con los hechos más nuevos en la BC.
   -No duplicación: se evita volver a una regla que ya fue ejecutada en un paso anterior.
   -Prioridad de operación: se sigue un orden de jerarquía preestablecido.
   ***EJ:*** Si tanto la regla *(pelos(x) -> mamífero(x))* como la regla *(plumas(x) -> ave(x))* están activas, se elije la primera por criterio de prioridad.
   
3. ***Aplicación:*** Luego de haber seleccionado la regla, se la ejecuta. Se agregan los consecuentes a la MT o ejecutar las acciones indicadas en la consecuente.
   ***EJ:*** Se dispara la regla seleccionada y se agrega a la MT el nuevo hecho: *mamífero(Matilde)*. La MT queda actualizada y el ciclo vuelve al Match.

---
***Ventajas:*** económico y útil para inferir a partir de nueva info.
***Desventajas:*** cláusulas positivas de primer orden, deducción de hechos irrelevantes y se pueden generar infinitos hechos nuevos.