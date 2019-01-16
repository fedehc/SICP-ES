### 1.2 Procedimientos y los Procesos que Generan

Ya hemos considerado los elementos de la programación: Hemos usado operaciones aritméticas primitivas, hemos combinado estas operaciones, y las hemos abstraído definiéndolas como procedimientos compuestos. Pero eso no es suficiente como para poder decir que sabemos programar. Nuestra situación es análoga a la de alguien que aprendió las reglas de cómo se mueven las piezas en el ajedrez pero que no sabe nada de las aperturas típicas, tácticas, o estrategias. Al igual que el jugador de ajedrez novato, todavía no conocemos los patrones comunes de uso en este campo. Nos falta el conocimiento sobre qué movimientos convienen hacer (qué procedimientos conviene definir). Nos falta la experiencia para predecir las consecuencias de hacer una jugada (ejecutar un procedimiento).

La capacidad de visualizar las consecuencias de las acciones bajo consideración es crucial para convertirse en un programador experto, como lo es en cualquier actividad sintética y creativa. Al convertirse en un fotógrafo experto, por ejemplo, uno debe aprender a mirar una escena y saber cuán oscura aparecerá cada región en una impresión para cada posible elección de condiciones de exposición y revelado. Sólo entonces uno puede razonar hacia atrás, planificando el encuadre, la iluminación, la exposición y el desarrollo para obtener los efectos deseados. Lo mismo ocurre con la programación, en la que planificamos el curso de acción a seguir por un proceso y en la que controlamos el proceso por medio de un programa. Para llegar a ser expertos, debemos aprender a visualizar los procesos generados por varios tipos de procedimientos. Sólo después de haber desarrollado tal habilidad podemos aprender a construir de manera confiable programas que muestren el comportamiento deseado.

Un procedimiento es un patrón para la *evolución local* de un proceso computacional. En él se especifica cómo se construye cada etapa del proceso a partir de la etapa anterior. Nos gustaría poder hacer afirmaciones sobre el comportamiento general, o *global*, de un proceso cuya evolución local ha sido especificado por un procedimiento. Esto por lo general es muy difícil de hacer, pero al menos podemos intentar describir algunos patrones típicos en la evolución de los procesos.

En esta sección examinaremos algunas "formas" comunes para aquellos procesos generados a partir de procedimientos simples. También investigaremos el ritmo con el que estos procesos consumen valiosos recursos computacionales de tiempo y espacio. Los procedimientos que consideraremos son muy simples. Su papel es como el que desempeñan las pruebas de patrones en fotografía: como patrones prototípicos sobresimplificados, más que como ejemplos prácticos por si mismos.


#### 1.2.1 Recursión e Iteración Lineales

![Figura 1.3](./imagenes/capitulo-1/figura-1-3.png)

**Figura 1.3:** Un proceso recursivo lineal para calcular `6!`.

Comenzamos por considerar la función factorial, definida por

```
n! = n . (n-1) . (n-2) ... 3 - 2 - 1
```

Hay varias maneras de calcular los factoriales. Una forma es hacer uso de la observación de que `n!` es igual a `n` veces `(n - 1)!` para cualquier entero positivo `n`:

```
n! = n . [(n-1) . (n-2) ... 3 - 2 - 1]  =  n . (n-1)!
```

Así, podemos calcular `n!` computando `(n - 1)!` y multiplicando el resultado por `n`. Si agregamos la condición de que `1!` es igual a 1, esta observación se traduce directamente al procedimiento:

```scheme
(define (factorial n)
  (if (= n 1)
      1
      (* n (factorial (- n 1)))))
```

Podemos usar el modelo de sustitución de la [sección 1.1.5]((./10-capitulo-1-seccion-1-1.md#115-El-Modelo-de-Sustitución-para-la-Aplicación-de-Procedimientos) para observar este procedimiento en el cálculo de `6!`, como se muestra en la figura 1.3.

Ahora tomemos una perspectiva diferente sobre el cálculo de factoriales. Podríamos describir una regla para calcular `n!` especificando que primero multiplicamos 1 por 2, luego multiplicamos el resultado por 3, luego por 4, y así sucesivamente hasta alcanzar `n`. Más formalmente, nosotros mantendremos un producto en ejecución, junto con un contador que contará de 1 a `n`. Podemos describir este cómputo diciendo que el contador y el producto cambian simultáneamente de un paso al siguiente según la regla

```
producto ← contador · producto

contador ← contador + 1
```

y estipular que `n!` es el valor del producto cuando el contador excede `n`.

![Figura 1.4](./imagenes/capitulo-1/figura-1-4.png)

**Figura 1.4:** Un proceso iterativo lineal para calcular `6!`.

Una vez más, podemos reformular nuestra descripción como un procedimiento para calcular factoriales:[^29]

```scheme
(define (factorial n)
  (fact-iter 1 1 n))

(define (fact-iter producto contador max-cont)
  (if (> contador max-cont)
      producto
      (fact-iter (* contador producto)
                 (+ contador 1)
                 max-cont)))
```

Como en el caso anterior, podemos usar el modelo de sustitución para visualizar el proceso de calcular `6!`, como se muestra en la figura 1.4.

Comparemos los dos procesos. Desde un punto de vista, apenas parecen diferentes entre sí. Ambos computan la misma función matemática en el mismo dominio, y cada uno requiere un número de pasos proporcional a `n` para calcular `n!`. De hecho, ambos procesos incluso llevan a cabo la misma secuencia de multiplicaciones, obteniendo la misma secuencia de productos parciales. Pero cuando consideramos las "formas" de los dos procesos, nos damos cuenta de que evolucionan de una manera muy diferente.

Consideremos el primer proceso. El modelo de sustitución revela una forma de expansión seguida de una contracción, indicada por la flecha de la figura 1.3. La expansión ocurre a medida de que el proceso construye una cadena de *operaciones diferidas* (en este caso, una cadena de multiplicaciones). La contracción se produce a medida que se realizan las operaciones. Este tipo de proceso, caracterizado por una cadena de operaciones diferidas, se denomina *proceso recursivo*. Para llevar a cabo este proceso es necesario que el intérprete lleve el registro de las operaciones que se van a realizar posteriormente. En el cálculo de `n!`, la longitud de la cadena de multiplicaciones diferidas, y por lo tanto la cantidad de información necesaria para seguirla, crece linealmente con `n` (o sea, es proporcional a `n`), así como el número de pasos. Este proceso se denomina *proceso recursivo lineal*.

En contraste, el segundo proceso no crece ni se contrae. En cada paso, todo lo que necesitamos para hacer el seguimiento, para cualquier n, son los valores actuales de las variables `producto`, `contador`, y `max-cont`. Llamamos a esto un *proceso iterativo*. En general, un proceso iterativo es aquel cuyo estado puede ser resumido por un número fijo de *variables de estado*, junto con una regla fija que describe cómo deben actualizarse las variables de estado a medida que el proceso se desplaza de un estado a otro y una prueba final (opcional) que especifica las condiciones bajo las cuales el proceso debe terminar. En el cálculo de `n!`, el número de pasos requeridos crece linealmente con `n`. Este proceso se llama *proceso iterativo lineal*.

El contraste entre los dos procesos puede verse de otra manera. En el caso iterativo, las variables del programa proporcionan una descripción completa del estado del proceso en cualquier punto. Si detuviésemos el cálculo entre los pasos, todo lo que tendríamos que hacer para reanudar el cálculo es proporcionar al intérprete los valores de las tres variables del programa. No sucede así con el proceso recursivo. En este último caso existe una información adicional "oculta", mantenida por el intérprete y no contenida en las variables del programa, que indica "en dónde se encuentra el proceso" en la gestión de la cadena de operaciones diferidas. Cuanto más larga sea la cadena, más información debe mantenerse.[^30]

Al contrastar la iteración y la recursividad, debemos tener cuidado de no confundir la noción de un *proceso* recursivo con la noción de un *procedimiento* recursivo. Cuando describimos un procedimiento como recursivo, nos referimos al hecho sintáctico de que la definición del procedimiento se refiere (sea directa o indirectamente) al procedimiento mismo. Pero cuando describimos un proceso como si siguiera un patrón que es linealmente recursivo, por ejemplo, estamos hablando de cómo evoluciona el proceso, no de la sintaxis de cómo se escribe un procedimiento. Puede parecer desconcertante que nos refiramos a un procedimiento recursivo tal como `fact-iter` como generador de un proceso iterativo. Sin embargo, el proceso es realmente iterativo: Su estado es capturado completamente por sus tres variables de estado, y un intérprete sólo necesita hacer el seguimiento de tres variables para poder ejecutar el proceso.

Una razón por la que la distinción entre proceso y procedimiento puede resultar confusa es que la mayoría de las implementaciones de los lenguajes habituales (incluyendo Ada, Pascal y C) están diseñadas de manera tal que la interpretación de cualquier procedimiento recursivo consume una cantidad de memoria que crece con el número de llamadas al procedimiento, incluso cuando el proceso descrito es, en principio, iterativo. Como consecuencia, estos lenguajes pueden describir procesos iterativos sólo recurriendo a "constructos de bucles" de propósito especial tales como `do`, `repeat`, `until`, `for` y `while`. La implementación de Scheme que consideraremos en el [capítulo 5](./30-capitulo-5-intro.md) no comparte este defecto. Este ejecutará un proceso iterativo en espacio constante, aún cuando el proceso iterativo esté descrito por un procedimiento recursivo. Una implementación con esta propiedad se llama *tail-recursive* (NdT: traducido libremente al español sería *recursiva de cola*). Con una implementación tail-recursive, la iteración puede ser expresada usando el mecanismo de llamada de procedimiento normal, de modo que los constructos de iteración especiales son útiles sólo como azúcar sintáctico[^31].

**Ejercicio 1.9.** Cada uno de los dos procedimientos siguientes define un método para añadir dos números enteros positivos en términos de los procedimientos `inc`, que incrementa su argumento en 1, y `dec`, que disminuye su argumento en 1.

```scheme
(define (+ a b)
  (if (= a 0)
      b
      (inc (+ (dec a) b))))

(define (+ a b)
  (if (= a 0)
      b
      (+ (dec a) (inc b))))
```

Usando el modelo de sustitución, muestre el proceso generado por cada procedimiento al evaluar `(+ 4 5)`. ¿Son estos procesos iterativos o recursivos? 

**Ejercicio 1.10.** El siguiente procedimiento calcula una función matemática llamada función de Ackermann.

```scheme
(define (A x y)
  (cond ((= y 0) 0)
        ((= x 0) (* 2 y))
        ((= y 1) 2)
        (else (A (- x 1)
                 (A x (- y 1))))))
```

¿Cuáles son los valores de las siguientes expresiones?

```scheme
(A 1 10)

(A 2 4)

(A 3 3)
```

Consider the following procedures, where `A` is the procedure defined above:

```scheme
(define (f n) (A 0 n))

(define (g n) (A 1 n))

(define (h n) (A 2 n))

(define (k n) (* 5 n n))
```

Defina de forma concisa las funciones calculadas mediante los procedimientos `f`, `g`, y `h` para valores enteros positivos de `n`. Por ejemplo, `(k n)` calcula `5n2`.


#### 1.2.2 Recursión de Árbol

Otro patrón común de cálculo se llama *Recursión de Árbol* (NdT: *Tree Recursion* en inglés). A modo de ejemplo, consideremos el cálculo de la secuencia de números de Fibonacci, en la que cada número es la suma de los dos anteriores:

```
0, 1, 1, 2, 3, 5, 8, 13, 21, ...
```

En general, los números de Fibonacci pueden ser definidos por la regla

```
         ⎧  0                    si n = 0
Fib(n) = ⎨  1                    si n = 1
         ⎩  Fib(n-1) + Fib(n-2)  en caso contrario
```

Podemos traducir inmediatamente esta definición en un procedimiento recursivo para calcular los números de Fibonacci:

```scheme
(define (fib n)
  (cond ((= n 0) 0)
        ((= n 1) 1)
        (else (+ (fib (- n 1))
                 (fib (- n 2))))))
```

![Figura 1.5](./imagenes/capitulo-1/figura-1-5.png)

**Figura 1.5:** El proceso árbol-recursivo generado en el cálculo de `(fib 5)`..

Consideremos el patrón de este cálculo. Para calcular `(fib 5)`, calculamos `(fib 4)` y `(fib 3)`. Para calcular `(fib 4)`, calculamos `(fib 3)` y `(fib 2)`. En general, el proceso desarrollado se parece a un árbol, como se muestra en la figura 1.5. Note que las ramas se dividen en dos en cada nivel (excepto en la parte inferior); esto refleja el hecho de que el procedimiento `fib` se llama a sí mismo dos veces cada vez que es invocado.

Este procedimiento es ilustrativo a modo de árbol de recursión prototípico, aunque resulte una forma pésima de calcular los números de Fibonacci, ya que realiza una gran cantidad de cálculos redundantes. Note en la figura 1.5 que todo el cálculo de `(fib 3)` -casi la mitad del trabajo- está duplicado. De hecho, no es difícil mostrar que el número de veces que el procedimiento computará `(fib 1)` o `(fib 0)` (el número de hojas en el árbol anterior, en general) es precisamente `Fib(n + 1)`. Para tener una idea de lo malo que es esto, uno puede mostrar que el valor de `Fib(n)` crece exponencialmente con `n`. Más precisamente (ver ejercicio 1.13), `Fib(n)` es el entero más cercano a `φⁿ/√5`, donde

```
φ = (1 + √5)/2 = 1,6810...
```

es la número áureo, que satisface la ecuación

```
φ² = φ + 1
```

Por lo tanto, el proceso utiliza una serie de pasos que crecen exponencialmente con la entrada. Por otro lado, el espacio requerido crece sólo linealmente con la entrada, ya que sólo tenemos que hacer un seguimiento de los nodos que están por encima de nosotros en el árbol en cualquier punto del cálculo. En general, el número de pasos requeridos por un proceso recursivo de árbol será proporcional al número de nodos en el árbol, mientras que el espacio requerido será proporcional a la profundidad máxima del árbol.

También podemos formular un proceso iterativo para calcular los números de Fibonacci. La idea es usar un par de enteros `a` y `b`, inicializados a `Fib(1) = 1` y `Fib(0) = 0`, y aplicar repetidamente las transformaciones simultáneas 

```
a ← a + b

b ← a
```

No es difícil demostrar que, después de aplicar esta transformación `n` veces, `a` y `b` serán iguales, respectivamente, a `Fib(n + 1)` y `Fib(n)`. Así, podemos calcular los números de Fibonacci de forma iterativa utilizando el procedimiento

```scheme
(define (fib n)
  (fib-iter 1 0 n))

(define (fib-iter a b contador)
  (if (= contador 0)
      b
      (fib-iter (+ a b) a (- contador 1))))
```

Este segundo método para calcular `Fib(n)` es una iteración lineal. La diferencia en el número de pasos requeridos por los dos métodos -uno lineal en `n`, el otro creciendo tan rápido como `Fib(n)` mismo- es enorme, incluso para entradas pequeñas.

No se debe concluir de esto que los procesos de recursión de árbol son inútiles. Cuando consideramos procesos que operan sobre datos estructurados jerárquicamente en lugar de números, encontraremos que la recursividad de árbol es una herramienta natural y poderosa.[^32] Incluso en operaciones numéricas, los procesos de recursividad de árbol pueden ser útiles para ayudarnos a entender y diseñar programas. Por ejemplo, aunque el primer procedimiento `fib` es mucho menos eficiente que el segundo, es más sencillo, siendo poco más que una traducción a Lisp de la definición de la secuencia de Fibonacci. Para formular el algoritmo iterativo es necesario debe tenerse en cuenta que el cálculo puede ser reformulado como una iteración con tres variables de estado.


##### Ejemplo: Contando cambios

Sólo se necesita un poco de inteligencia para crear el algoritmo iterativo de Fibonacci. En contraste, considere el siguiente problema: ¿De cuántas maneras diferentes podemos hacer el cambio de 1 dolar, teniendo solamente monedas de 50, 25, 10, 5 y 1 centavos? De manera más general, ¿podemos escribir un procedimiento para calcular la cantidad de formas de cambiar una determinada cantidad de dinero?

Este problema tiene una solución simple como procedimiento recursivo. Supongamos que estamos pensando en los tipos de monedas que tenemos ordenados de alguna manera. Entonces la siguiente relación es aplicable:

La cantidad de maneras de cambiar la cantidad `a` usando `n` tipos de monedas es igual a

* la cantidad de maneras de cambiar la cantidad `a` usando todas menos la primera clase de moneda, más

* el número de maneras de cambiar la cantidad `a - d` usando todos los tipos de monedas `n`, donde `d` es la denominación de la primera clase de moneda.

Para comprender por qué esto es cierto, observe que las formas de hacer el cambio se pueden dividir en dos grupos: los que no usan ninguna de las primera clase de monedas, y los que sí lo hacen. Por lo tanto, la cantidad total de maneras de hacer cambios por una cantidad es igual a la cantidad de maneras de hacer cambios por ese monto sin usar ninguno de las primeras clases de monedas, más la cantidad de maneras de hacer cambios asumiendo que usamos la primera clase de monedas. Pero este último número es igual a la cantidad de maneras de hacer cambios por el monto que queda después de usar una moneda de la primera clase.

Así, podemos reducir recursivamente el problema de cambiar una cantidad dada al problema de cambiar cantidades más pequeñas usando menos tipos de monedas. Considere esta regla de reducción cuidadosamente, y convénzase de que podemos usarla para describir un algoritmo si especificamos los siguientes casos degradados:[^33].

* Si `a` es exactamente 0, debemos contarlo como 1 manera de hacer cambio.

* Si `a` es menos de 0, debemos contarlo como 0 maneras de hacer cambios.

* Si `n` es 0, debemos contarlo como 0 maneras de hacer cambios. 

Podemos traducir fácilmente esta descripción en un procedimiento recursivo:

```scheme
(define (contar-cambio monto)
  (cc monto 5))

(define (cc monto tipos-de-monedas)
  (cond ((= monto 0) 1)
        ((or (< monto 0) (= tipos-de-monedas 0)) 0)
        (else (+ (cc monto
                     (- tipos-de-monedas 1))
                 (cc (- monto
                        (primera-clase-de-monedas tipos-de-monedas))
                     tipos-de-monedas)))))

(define (primera-clase-de-monedas tipos-de-monedas)
  (cond ((= tipos-de-monedas 1) 1)
        ((= tipos-de-monedas 2) 5)
        ((= tipos-de-monedas 3) 10)
        ((= tipos-de-monedas 4) 25)
        ((= tipos-de-monedas 5) 50)))
```

(El procedimiento `primera-clase-de-monedas` toma como entrada el número de tipos de monedas disponibles y devuelve la primera clase de monedas. En este caso estamos pensando en las monedas en orden desde el más grande hasta el más pequeño, pero con cualquier orden también funcionaría). Ahora podemos responder a nuestra pregunta inicial sobre el cambio de 1 dólar: 

```scheme
(contar-cambio 100)
292
```

`contar-cambio` genera un proceso recursivo de árbol con redundancias similares a las de nuestra primera implementación de `fib` (tomará un tiempo para que ese 292 sea calculado). Por otro lado, no es tan obvio saber diseñar un mejor algoritmo para calcular el resultado, y dejaremos este problema como un reto. El hecho de que un proceso recursivo de árbol puede ser altamente ineficiente pero a la vez fácil de especificar y de entender ha llevado a la gente a proponer que uno podría obtener lo mejor de ambos mundos mediante el diseño de un "compilador inteligente" que podría transformar los procedimientos recursivos de árbol en procedimientos más eficientes que calculen el mismo resultado.[^34]

**Ejercicio 1.11.** Una función `f` se define por la regla que `f(n) = n` si `n<3` y `f(n) = f(n - 1) + 2f(n - 2) + 3f(n - 3)` si `n> 3`. Escribir un procedimiento que calcule `f` mediante un proceso recursivo. Escriba un procedimiento que calcule `f` por medio de un proceso iterativo.

**Ejercicio 1.12.** El siguiente patrón de números se llama el triángulo de Pascal.

```
    1
   1 1
  1 2 1
 1 3 3 1
1 4 6 4 1
```

Los números en el borde del triángulo son todos 1, y cada número dentro del triángulo es la suma de los dos números que están por encima de él.[^35] Escriba un procedimiento que calcule los elementos del triángulo de Pascal por medio de un proceso recursivo.

**Ejercicio 1.13.** Demuestre que `Fib(n)` es el entero más cercano a `φⁿ/√5)/2`, donde `φ= (1 + √5)/2`. Pista: Usar `⨚ = (1 - √5)/2`. Utilice la inducción y la definición de los números de Fibonacci (ver [sección 1.2.2](#122-Recursión-de-Árbol)) para probar que `Fib(n) = (φⁿ - ⨚ⁿ)/5`. 


#### 1.2.3 Órdenes de crecimiento



## ---Traducción pendiente---

___

[^29]: En un programa real probablemente usaríamos la estructura de bloques introducida en la última sección para ocultar la definición de `fact-iter`:

```scheme
(define (factorial n)
  (define (iter producto contador)
    (if (> contador n)
        producto
        (iter (* contador producto)
              (+ contador 1))))
  (iter 1 1))
```

Evitamos hacer esto aquí para minimizar el número de cosas en las que pensar a la vez.

[^30]: Cuando discutamos la implementación de procedimientos en máquinas de registro en el [capítulo 5](./30-capitulo-5-intro.md), veremos que cualquier proceso iterativo puede ser realizado "en hardware" como una máquina que tiene un conjunto de registros fijos y sin memoria auxiliar. Por el contrario, la realización de un proceso recursivo requiere una máquina que utilice una estructura de datos auxiliar conocida como *stack*.

[^31]: La recursividad de la cola se conoce desde hace tiempo como un truco de optimización del compilador. Una base semántica coherente para la recursión de la cola fue proporcionada por Carl Hewitt (1977), quien la explicó en términos del modelo de computación de "pasar mensajes" que discutiremos en el [capítulo 3](./19-capitulo-3-intro.md). Inspirados por esto, Gerald Jay Sussman y Guy Lewis Steele Jr (ver Steele 1975) construyeron un intérprete de cola recurrente para Scheme. Más tarde Steele mostró cómo la recursividad de la cola es una consecuencia de la forma natural de compilar las llamadas de procedimiento (Steele 1977). El estándar IEEE para Scheme requiere que las implementaciones de Scheme sean recursivas. 

[^32]: Un ejemplo de esto fue mencionado en la sección 1.1.3: El propio intérprete evalúa las expresiones usando un proceso recursivo de árbol.

[^33]: Por ejemplo, analice detalladamente cómo se aplica la regla de reducción al problema de hacer cambios de 10 centavos utilizando monedas de un centavo y de cinco centavos.

[^34]: Un enfoque para hacer frente a los cálculos redundantes es organizar las cosas de manera tal que automáticamente construyamos una tabla de valores a medida que se calculan. Cada vez que se nos pide que apliquemos el procedimiento a algún argumento, primero buscamos si el valor ya está almacenado en la tabla, en cuyo caso evitamos realizar el cálculo redundante. Esta estrategia, conocida como *tabulación* o *memoization*, puede ser implementada de manera sencilla. La tabulación se puede utilizar a veces para transformar procesos que requieren de un número exponencial de pasos (tal como sucede en `contar-cambio`) en procesos cuyos requisitos de espacio y tiempo crecen linealmente con la entrada de datos. Ver ejercicio 3.27.

[^35]: Los elementos del triángulo de Pascal se llaman *coeficientes binomiales*, porque la n-ésima fila consiste en los coeficientes de los términos en la expansión de `(x + y)ⁿ`. Este patrón para calcular los coeficientes apareció en el trabajo seminal de Blaise Pascal en 1653 sobre la teoría de la probabilidad, *Traité du triangle arithmétique*. Según Knuth (1973), el mismo patrón aparece en el *Szu-yuen Yü-chien* ("El precioso espejo de los cuatro elementos"), publicado por el matemático chino Chu Shih-chieh en 1303, en las obras del matemático hindú del siglo XII Bháscara Áchárya.

