> **Planificación:** proceso de articulación de una secuencia de acciones que permiten alcanzar un objetivo.

Se caracteriza por representar explícitamente el objetivo, descomponer problemas en subproblemas, suposición de independencia de objetivos, lenguaje expresivo y restrictivo (para usar STRIPS), y los entornos son: accesibles, determinísticos, finitos, estáticos y discretos.

El planificador puede incorporar acciones al plan en cualquier lugar en que sean necesarias. Por ejemplo, el agente puede decidir que va a tener que Tomar(b1), antes de decidir como va a hacer para llegar a donde se encuentra b1.

El tomar decisiones obvias o importantes en forma temprana permite reducir el factor de ramificación, disminuyendo la necesidad de backtraking.

---
##### Limitaciones del enfoque de Búsqueda
En la búsqueda clásica:
- *Representación de acciones:* funciones que generan descripciones de estados sucesores.
- *Representación de estados:* representaciones completas de los estados.
- *Representación de objetivos:* solo un test para verificar el objetivo y una función heurística.
Plantear el problema como búsqueda exige demasiadas acciones y demasiados estados para analizar.

---
##### Lenguaje Formal — STRIPS
***Representación de Estados***
- Conjunto de literales positivas (átomos).
- Deben ser ground (instanciadas, sin variables libres) y libres de funciones.
- Presunción de Mundo Cerrado: todo lo que no se afirma es falso.
- Espacio de estados finito.

***Representación de Metas (Objetivos)***
- Representación parcial de un estado.
- Conjunción de literales ground positivas.
- Un estado `s` satisface una meta `g` si `s` contiene todos los literales de `g` (y posiblemente otros).

***Representación de Acciones — Operadores STRIPS***
Constan de tres partes:

| Componente     | Símbolo | Descripción                                                           |
| -------------- | ------- | --------------------------------------------------------------------- |
| Precondiciones | `PC`    | Conjunto de literales que deben ser verdaderos para aplicar la acción |
| Lista borrar   | `B`     | Literales que se eliminan del estado                                  |
| Lista añadir   | `A`     | Literales que se agregan al estado                                    |

Formato general:

```
NombreAcción(Variables)
PC: literal1 ∧ literal2 ∧ ...
B:  literal1, literal2, ...
A:  literal1, literal2, ...
```

**EJ:** PutOn(X,Y,Z)

```
PutOn(X,Y,Z)
PC: clear(Y) ∧ clear(X) ∧ on(X,Z)
B:  clear(Y), on(X,Z)
A:  clear(Z), on(X,Y)
```

***Fórmula de Transición de Estado***
Dado un operador `a` aplicado al estado `s`, el nuevo estado `s'` es:

$$s' = s + p - q$$

donde:
- `p ⊆ Aₐ` (literales positivas de la lista añadir)
- `q ⊆ Bₐ` (literales positivas de la lista borrar)

*Presunción de STRIPS:* Cualquier literal que no esté mencionado en el efecto de una acción permanece sin cambio.

---
##### Reglas STRIPS y Operadores
- Un esquema STRIPS (regla) usa variables libres (no instanciadas).
- Un operador es una instancia de una regla: se obtiene aplicando un unificador `θ` a los conjuntos `PC`, `A` y `B`.
- La instancia se puede aplicar al estado `s` si existe una instancia base de `PC` satisfecha por `s`.
- Restricción: no pueden aparecer variables libres en `PC`, `A` ni `B` del operador.

---
##### Búsqueda hacia Delante y hacia Atrás

|Dirección|Descripción|
|---|---|
|**Forward**|Se parte del estado inicial y se aplican acciones hasta alcanzar el objetivo.|
|**Backward**|Se parte del objetivo y se retrocede mediante los operadores (preferible cuando el objetivo tiene pocos elementos → pocos operadores posibles).|

*Ventaja del backward:* la descripción del objetivo suele tener pocos literales, por lo que hay pocos operadores que pueden producirlos, reduciendo el factor de ramificación.
Ambas búsquedas (hacia delante y hacia atrás) son totalmente ordenadas y no sacan provecho de la descomposición del problema.

---
##### Planificación de Orden Parcial (POP)
- El planificador trabaja sobre submetas en forma independiente.
- No impone un orden total durante la búsqueda.
- *Estrategia de mínimo compromiso:* demorar decisiones durante la búsqueda para evitar backtracking innecesario.
- Pueden existir dos acciones en el plan sin orden definido entre ellas.

***Acciones Especiales:*** Todo plan de orden parcial define dos acciones ficticias.
- *Inicio (S₀):* `PC: true`, `A:` literales del estado inicial.
- *Fin (S₁):* `PC:` literales del estado/meta final, `B: ∅`, `A: ∅`.

***Representación de un Plan***

```
Plan(
  pasos:   {S1: op(acción: ...), S2: op(acción: ...), ...}
  orden:   {Si ≺ Sj, ...}          ← restricciones de precedencia
  asoc:    {X/val, ...}            ← asociaciones variable-constante
  links:   {Si --c--> Sj, ...}     ← links causales
)
```

***Links Causales***
Un link causal `Sᵢ --c--> Sⱼ` significa que la acción `Sᵢ` produce el literal `c`, que es precondición de `Sⱼ`.
Imponen un orden en la ejecución: `Sᵢ` debe ejecutarse antes que `Sⱼ`.

***Propiedades de un Plan***

|Propiedad|Definición|
|---|---|
|**Plan completo**|Se han alcanzado **todas** las precondiciones de todos los pasos.|
|**Precondición alcanzada**|Es efecto de un paso anterior y no puede ser removida por otro paso.|
|**Plan consistente**|No hay ciclos en los links de ordenamiento y no hay conflictos con los vínculos causales.|
|**Solución**|Plan **consistente y completo**.|

> Toda linealización de un plan de orden parcial es una solución de orden total que, al ejecutarse desde el estado inicial, llegará al estado final.

---
##### Amenazas y Protección de Links Causales
Una amenaza ocurre cuando una acción `Sk` podría eliminar un literal `c` que es necesario en un link causal `Sᵢ --c--> Sⱼ`. Formas de resolver una amenaza:

|Estrategia|Descripción|
|---|---|
|**Retrasar (Demotion)**|Ordenar `Sk` **antes** de `Sᵢ`|
|**Promover (Promotion)**|Ordenar `Sk` **después** de `Sⱼ`|

---
##### Planificación con Mínimo Compromiso
- Al unificar un objetivo con una regla, si alguna variable `Z` no queda ligada (no hay suficiente información aún), se deja sin instanciar.
- Esta decisión se difiere hasta tener más información.
- Esperar a tener más información es más eficiente que probar con todos los posibles valores, evitando backtracking.

---
##### Ventajas de la Planificación sobre la Búsqueda Clásica

|Aspecto|Búsqueda|Planificación|
|---|---|---|
|Acceso a estructura del objetivo|No|Sí (explícita)|
|Descomposición en submetas|No|Sí|
|Orden de construcción vs. ejecución|Deben coincidir|Pueden diferir|
|Reducción de backtracking|Limitada|Mayor (mínimo compromiso)|
|Heurísticas automáticas|Difícil de derivar|Más fácil (estructura explícita)|
