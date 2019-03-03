## 1.2 Procedimientos y los Procesos que Generan

Ya hemos considerado los elementos de la programación: hemos usado operaciones aritméticas primitivas, hemos combinado estas operaciones, y las hemos abstraído definiéndolas como procedimientos compuestos. Pero eso no es suficiente como para poder decir que sabemos programar. Nuestra situación es análoga a la de alguien que aprendió las reglas de cómo se mueven las piezas en el ajedrez pero que no sabe nada de las aperturas típicas, tácticas, o estrategias. Al igual que el jugador de ajedrez novato, todavía no conocemos los patrones comunes de uso en este campo. Nos falta el conocimiento acerca de qué movimientos valen la pena hacer (qué procedimientos vale la pena definir). Nos falta la experiencia para predecir las consecuencias de hacer una jugada (ejecutar un procedimiento).

La capacidad de visualizar las consecuencias de las acciones consideradas es crucial para convertirse en un programador experto, como lo es en cualquier actividad sintética y creativa. Para ser un fotógrafo experto, por ejemplo, uno debe aprender a mirar una escena y saber cuán oscura aparecerá cada región en una impresión para cada posible elección de condiciones de exposición y revelado. Sólo entonces uno puede razonar hacia atrás, planificando el encuadre, la iluminación, la exposición y el desarrollo para obtener los efectos deseados. Lo mismo ocurre con la programación, cuando planeamos el curso de acción que debe seguir un proceso y cuando controlamos el proceso mediante un programa. Para llegar a ser expertos, debemos aprender a visualizar los procesos generados por varios tipos de procedimientos. Sólo después de haber desarrollado tal habilidad podemos aprender a construir de manera confiable programas que muestren el comportamiento deseado.

Un procedimiento es un patrón para la *evolución local* de un proceso computacional. En él se especifica cómo se construye cada etapa del proceso a partir de la etapa anterior. Nos gustaría poder hacer afirmaciones sobre el comportamiento general, o *global*, de un proceso cuya evolución local ha sido especificado por un procedimiento. Esto es muy difícil de hacer en general, pero al menos podemos intentar describir algunos patrones típicos en la evolución de los procesos.

En esta sección examinaremos algunas "formas" comunes para los procesos generados a partir de procedimientos simples. También investigaremos el ritmo con el que estos procesos consumen valiosos recursos computacionales de tiempo y espacio. Los procedimientos que consideraremos son muy sencillos. Su papel es como el que desempeñan las pruebas de patrones en fotografía: como patrones prototípicos sobresimplificados, más que como ejemplos prácticos de por sí.


### 1.2.1 Recursión e Iteración Lineales

![Figura 1.3](./imagenes/capitulo-1/figura-1-3.png)

**Figura 1.3:** Un proceso recursivo lineal para calcular `6!`.

Empezamos considerando a la función factorial, definida por

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

Podemos usar el modelo de sustitución de la [sección 1.1.5](./10-capitulo-1-seccion-1-1.md#115-El-Modelo-de-Sustitución-para-la-Aplicación-de-Procedimientos) para observar este procedimiento en acción calculando `6!`, como se muestra en la figura 1.3.

Ahora tomemos una perspectiva diferente en el cálculo de factoriales. Podríamos describir una regla para calcular `n!` especificando primero que multiplicamos 1 por 2, luego multiplicamos el resultado por 3, luego por 4, y así sucesivamente hasta alcanzar `n`. Más formalmente, mantendremos una productoria en ejecución, junto con un contador que contará de 1 a `n`. Podemos describir este cálculo diciendo que el contador y el producto cambian simultáneamente de un paso al siguiente según la regla

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

Comparemos los dos procesos. Desde un punto de vista, apenas parecen diferentes entre sí. Ambos calculan la misma función matemática en el mismo dominio, y cada uno requiere un número de pasos proporcional a `n` para calcular `n!`. De hecho, ambos procesos incluso llevan a cabo la misma secuencia de multiplicaciones, obteniendo la misma secuencia de productos parciales. Pero cuando consideramos las "formas" de los dos procesos, nos damos cuenta de que evolucionan de una manera muy diferente.

Consideremos el primer proceso. El modelo de sustitución revela una forma de expansión seguida de una contracción, indicada por la flecha de la figura 1.3. La expansión ocurre a medida de que el proceso construye una cadena de *operaciones diferidas* (en este caso, una cadena de multiplicaciones). La contracción se produce a medida que se realizan las operaciones. Este tipo de proceso, caracterizado por una cadena de operaciones diferidas, se denomina *proceso recursivo*. Para llevar a cabo este proceso es necesario que el intérprete lleve el registro de las operaciones que se van a realizar posteriormente. En el cálculo de `n!`, la longitud de la cadena de multiplicaciones diferidas, y por lo tanto la cantidad de información necesaria para seguirla, crece linealmente con `n` (o sea, es proporcional a `n`), así como el número de pasos. Este proceso se denomina *proceso recursivo lineal*.

En contraste, el segundo proceso no crece ni se contrae. En cada paso, todo lo que necesitamos para hacer el seguimiento, para cualquier `n`, son los valores actuales de las variables `producto`, `contador`, y `max-cont`. Llamamos a esto un *proceso iterativo*. En general, un proceso iterativo es aquel cuyo estado puede ser resumido por un número fijo de *variables de estado*, junto con una regla fija que describe cómo deben actualizarse las variables de estado a medida que el proceso se desplaza de un estado a otro y una prueba final (opcional) que especifica las condiciones bajo las cuales el proceso debe terminar. En el cálculo de `n!`, el número de pasos requeridos crece linealmente con `n`. Este proceso se llama *proceso iterativo lineal*.

El contraste entre los dos procesos puede verse de otra manera. En el caso iterativo, las variables del programa proporcionan una descripción completa del estado del proceso en cualquier punto. Si detuviésemos el cálculo entre los pasos, todo lo que tendríamos que hacer para reanudar el cálculo es proporcionar al intérprete los valores de las tres variables del programa. No sucede así con el proceso recursivo. En este último caso existe una información adicional "oculta", mantenida por el intérprete y no contenida en las variables del programa, que indica "en dónde se encuentra el proceso" en la gestión de la cadena de operaciones diferidas. Cuanto más larga sea la cadena, más información debe mantenerse.[^30]

Al contrastar la iteración y la recursividad, debemos tener cuidado de no confundir la noción de un *proceso* recursivo con la noción de un *procedimiento* recursivo. Cuando describimos un procedimiento como recursivo, nos referimos al hecho sintáctico de que la definición del procedimiento se refiere (sea directa o indirectamente) al procedimiento mismo. Pero cuando describimos un proceso como si siguiera un patrón que es linealmente recursivo, por ejemplo, estamos hablando de cómo evoluciona el proceso, no de la sintaxis de cómo se escribe un procedimiento. Puede parecer desconcertante que nos refiramos a un procedimiento recursivo tal como `fact-iter` como generador de un proceso iterativo. Sin embargo, el proceso es realmente iterativo: Su estado es capturado completamente por sus tres variables de estado, y un intérprete sólo necesita hacer el seguimiento de tres variables para poder ejecutar el proceso.

Una razón por la que la distinción entre proceso y procedimiento puede resultar confusa es que la mayoría de las implementaciones de los lenguajes habituales (incluyendo Ada, Pascal y C) están diseñadas de manera tal que la interpretación de cualquier procedimiento recursivo consume una cantidad de memoria que crece con el número de llamadas al procedimiento, incluso cuando el proceso descripto es, en principio, iterativo. Como consecuencia, estos lenguajes pueden describir procesos iterativos sólo recurriendo a "constructos de bucles" de propósito especial tales como `do`, `repeat`, `until`, `for` y `while`. La implementación de Scheme que consideraremos en el [capítulo 5](./30-capitulo-5-intro.md) no comparte este defecto. Este ejecutará un proceso iterativo en espacio constante, aún cuando el proceso iterativo esté descripto por un procedimiento recursivo. Una implementación con esta propiedad se la llama *tail-recursive* (NdT: traducido libremente al español sería algo así como *cola-recursivo*). Con una implementación tail-recursive, la iteración puede ser expresada usando el mecanismo de llamada de procedimiento normal, de modo que los constructos de iteración especiales son útiles sólo como azúcar sintáctico[^31].

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

¿Cuáles serán los valores para las siguientes expresiones?

```scheme
(A 1 10)

(A 2 4)

(A 3 3)
```

Considere los siguientes procedimientos, donde `A` es el procedimiento definido anteriormente:

```scheme
(define (f n) (A 0 n))

(define (g n) (A 1 n))

(define (h n) (A 2 n))

(define (k n) (* 5 n n))
```

Defina de forma concisa las funciones calculadas mediante los procedimientos `f`, `g`, y `h` para valores enteros positivos de `n`. Por ejemplo, `(k n)` calcula `5n2`.


### 1.2.2 Árbol de Recursión

Otro patrón común de cálculo se llama *Árbol de Recursión* (NdT: *Tree Recursion* en inglés). A modo de ejemplo, consideremos el cálculo de la secuencia de números de Fibonacci, en la que cada número es la suma de los dos anteriores:

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

**Figura 1.5:** El proceso árbol-recursivo generado en el cálculo de `(fib 5)`.

Considere el patrón de este cálculo. Para calcular `(fib 5)`, calculamos `(fib 4)` y `(fib 3)`. Para calcular `(fib 4)`, calculamos `(fib 3)` y `(fib 2)`. En general, el proceso desarrollado se parece a un árbol, como se muestra en la figura 1.5. Note que las ramas se dividen en dos en cada nivel (excepto en la parte inferior); esto refleja el hecho de que el procedimiento `fib` se llama a sí mismo dos veces cada vez que es invocado.

Este procedimiento es instructivo como un caso prototípico de árbol de recursión, aunque resulte una forma pésima de calcular los números de Fibonacci, ya que realiza una gran cantidad de cálculos redundantes. Note en la figura 1.5 que todo el cálculo de `(fib 3)` -casi la mitad del trabajo- está duplicado. De hecho, no es difícil mostrar que el número de veces que el procedimiento computará `(fib 1)` o `(fib 0)` (el número de hojas en el árbol anterior, en general) es precisamente `Fib(n + 1)`. Para tener una idea de lo malo que es esto, uno puede mostrar que el valor de `Fib(n)` crece exponencialmente con `n`. Más precisamente (ver ejercicio 1.13), `Fib(n)` es el entero más cercano a `Φⁿ/√5`, donde

```
Φ = (1 + √5)/2 = 1,6810...
```

es el número áureo, que satisface la ecuación

```
Φ² = Φ + 1
```

Por lo tanto, el proceso utiliza una serie de pasos que crecen exponencialmente con la entrada. Por otra parte, el espacio requerido crece sólo linealmente con la entrada, ya que sólo tenemos que hacer un seguimiento de los nodos que están por encima de nosotros en el árbol en cualquier punto del cálculo. En general, el número de pasos requeridos por un proceso árbol-recursivo será proporcional al número de nodos en el árbol, mientras que el espacio requerido será proporcional a la profundidad máxima del árbol.

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

De esto no se debe concluir de que los procesos árbol-recursivos son inútiles. Cuando consideramos procesos que operan sobre datos estructurados jerárquicamente en lugar de números, encontraremos que el árbol de recursión es una herramienta natural y poderosa.[^32] Incluso en operaciones numéricas, los procesos árbol-recursivos pueden ser útiles para ayudarnos a entender y diseñar programas. Por ejemplo, aunque el primer procedimiento `fib` es mucho menos eficiente que el segundo, este es más sencillo, siendo poco más que una traducción a Lisp de la definición de la secuencia de Fibonacci. Para formular el algoritmo iterativo fue necesario tener en cuenta que el cálculo podía ser reformulado como una iteración con tres variables de estado.


#### Ejemplo: Contando el cambio

Sólo se necesita un poco de ingenio para idear el algoritmo iterativo de Fibonacci. En contraste, considere el siguiente problema: ¿De cuántas maneras diferentes podemos hacer el cambio de $1.00, teniendo solamente monedas de 50, 25, 10, 5 y 1 centavos? De manera más general, ¿podemos escribir un procedimiento para calcular la cantidad de formas de cambiar una determinada cantidad de dinero?

Este problema tiene una solución simple como un procedimiento recursivo. Supongamos que pensamos en los tipos de monedas disponibles dispuestos en algún orden. Entonces la siguiente relación es válida:

La cantidad de maneras de cambiar la cantidad `a` usando `n` tipos de monedas es igual a

* la cantidad de maneras de cambiar la cantidad `a` usando todas menos la primera clase de monedas, más

* el número de maneras de cambiar la cantidad `a - d` usando todos los tipos de monedas `n`, donde `d` es la denominación de la primera clase de monedas.

Para comprender por qué esto es cierto, observe que las formas de hacer el cambio se pueden dividir en dos grupos: los que no usan ninguna de la primera clase de monedas, y los que sí lo hacen. Por lo tanto, la cantidad total de maneras de hacer cambios por un monto es igual a la cantidad de maneras de hacer cambios por ese monto sin usar ninguna de las primeras clases de monedas, más la cantidad de maneras de hacer cambios asumiendo que usamos la primera clase de monedas. Pero este último número es igual a la cantidad de maneras de hacer cambios por el monto que queda después de usar una moneda de la primera clase.

Así, podemos reducir recursivamente el problema de cambiar una cantidad dada al problema de cambiar cantidades más pequeñas usando menos tipos de monedas. Considere esta regla de reducción cuidadosamente, y comprenda a fondo de que podemos usarla para describir un algoritmo si especificamos los siguientes casos degradados:[^33].

* Si `a` es exactamente 0, debemos contarlo como 1 manera de hacer cambio.

* Si `a` es menos de 0, debemos contarlo como 0 maneras de hacer cambio.

* Si `n` es 0, debemos contarlo como 0 maneras de hacer cambio. 

Podemos traducir fácilmente esta descripción en un procedimiento recursivo:

```scheme
(define (contar-cambio monto)
  (cc monto 5))

(define (cc monto clases-de-monedas)
  (cond ((= monto 0) 1)
        ((or (< monto 0) (= clases-de-monedas 0)) 0)
        (else (+ (cc monto
                     (- clases-de-monedas 1))
                 (cc (- monto
                        (primera-denominacion clases-de-monedas))
                     clases-de-monedas)))))

(define (primera-denominacion clases-de-monedas)
  (cond ((= clases-de-monedas 1) 1)
        ((= clases-de-monedas 2) 5)
        ((= clases-de-monedas 3) 10)
        ((= clases-de-monedas 4) 25)
        ((= clases-de-monedas 5) 50)))
```

(El procedimiento `primera-denominacion` toma como entrada el número de clases de monedas disponibles y devuelve la primera denominación de monedas. En este caso estamos pensando en las monedas en orden desde el más grande hasta el más pequeño, pero con cualquier orden también funcionaría). Ahora podemos responder a nuestra pregunta inicial sobre el cambio de $1.00 (NdT: tener en cuenta que $1.00 = 100 centavos):

```scheme
(contar-cambio 100)
> 292
```

`contar-cambio` genera un proceso árbol-recursivo con redundancias similares a las de nuestra primera implementación de `fib` (tomará un tiempo para que ese 292 sea calculado). Por otro lado, no es tan obvio saber diseñar un mejor algoritmo para calcular el resultado, y dejaremos este problema como un reto.  La observación de que un proceso árbol-recursivo puede ser altamente ineficiente pero a la vez fácil de especificar y de entender ha llevado a la gente a proponer que uno podría obtener lo mejor de ambos mundos mediante el diseño de un "compilador inteligente" que podría transformar los procedimientos árbol-recursivos en procedimientos más eficientes que calculen el mismo resultado.[^34]

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

**Ejercicio 1.13.** Demuestre que `Fib(n)` es el entero más cercano a `Φⁿ/√5`, donde `Φ = (1 + √5)/2`. Pista: Usar `⨚ = (1 - √5)/2`. Utilice la inducción y la definición de los números de Fibonacci (ver [sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Árbol-de-Recursión)) para probar que `Fib(n) = (Φⁿ - ⨚ⁿ)/5`. 


### 1.2.3 Órdenes de crecimiento

Los ejemplos anteriores ilustran que los procesos pueden diferir considerablemente en la velocidad con la que consumen recursos computacionales. Una forma conveniente de describir esta diferencia es utilizar la noción de *orden de crecimiento* para obtener una medida en bruto de los recursos requeridos por un proceso a medida de que las entradas se hacen cada vez más grandes.

Supongamos que `n` es un parámetro que mide el tamaño del problema y que `R(n)` es la cantidad de recursos que el proceso requiere para un problema de tamaño `n`. En nuestros ejemplos anteriores tomábamos a `n` como el número con el que debe calcularse una función dada, pero hay otras posibilidades. Por ejemplo, si nuestra meta es calcular una aproximación a la raíz cuadrada de un número, podríamos tomar a `n` como el número de dígitos de precisión que requerimos. Para la multiplicación de matrices podríamos tomar a `n` como el número de filas en las matrices. En general, hay una serie de propiedades del problema con respecto a las cuales será deseable analizar un proceso determinado. De manera similar, `R(n)` podría medir el número de registros internos de almacenamiento usados, el número de operaciones elementales de máquina realizadas, y así sucesivamente. En computadoras donde sólo se realizan un número fijo de operaciones a la vez, el tiempo requerido será proporcional al número de operaciones elementales efectuadas por la máquina.

Decimos que `R(n)` tiene orden de crecimiento `Θ(f(n))`, escrito `R(n) = Θ(f(n))` (pronunciado "theta de f(n)")**\***, si hay constantes positivas `k₁` y `k₂` independientes de `n` tales que 

```
k₁ f(n) < R(n) < k₂ f(n)
```

para cualquier valor suficientemente grande de `n` (en otras palabras, para un gran `n`, el valor `R(n)` está entre `k₁ f(n)` y `k₂ f(n)`).

**\*** NdT: este concepto se lo conoce más actualmente como *Notación O*, o en inglés *Big O Notation* (o simplemente *Big O*).

Por ejemplo, con el proceso recursivo lineal para calcular el factorial detallado en la [sección 1.2.1](#121-Recursión-e-Iteración-Lineales) el número de pasos crece proporcionalmente con la entrada `n`. Así, los pasos requeridos para este proceso crecen como `Θ(n)`. También vimos que el espacio requerido crece como `Θ(n)`. Para el factorial iterativo, el número de pasos sigue siendo `Θ(n)` pero el espacio es `Θ(1)`, es decir, constante.[^36] El cálculo árbol-recursivo de Fibonacci requiere `Θ(n)` pasos y espacio `Θ(Φⁿ)`, donde `Φ` es la relación de oro descrita en la [sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Árbol-de-Recursión).

Los órdenes de crecimiento sólo proporcionan una descripción aproximada del comportamiento de un proceso. Por ejemplo, un proceso que requiere `n²` pasos, otro proceso que requiere `1000n²` pasos y otro proceso que requiere `3n² + 10n + 17` pasos, todos estos tienen un orden de crecimiento `Θ(n²)`. Por otro lado, el orden de crecimiento nos proporciona una indicio útil de cómo podemos esperar a que el comportamiento del proceso cambie a medida de que cambiemos el tamaño del problema. Para un proceso `Θ(n)` (lineal), duplicar el tamaño duplicará aproximadamente la cantidad de recursos utilizados. Para un proceso exponencial, cada incremento en el tamaño del problema multiplicará la utilización de recursos por un factor constante. En el resto de la [sección 1.2](./11-capitulo-1-seccion-1-2.md) examinaremos dos algoritmos cuyos órdenes de crecimiento son logarítmicos, de modo que duplicar el tamaño del problema aumentará la necesidad de recursos en una cantidad constante.


**Ejercicio 1.14.** Dibuje el árbol que ilustre el proceso generado por el procedimiento de `contar-cambio` de la [sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Árbol-de-Recursión) al hacer el cambio por 11 centavos. ¿Cómo será el orden de crecimiento en el espacio y en el número de pasos utilizados por este proceso a medida que la cantidad a cambiar se incremente? 

**Ejercicio 1.15.** El seno de un ángulo (especificado en radianes) puede ser calculado haciendo uso de la aproximación `sen(x) ≈ x` si `x` es suficientemente pequeño, y la identidad trigonométrica

```
sen(x) = 3 sen(x/3) - 4 sen³(x/3)
```

para reducir el tamaño del argumento de `sen` (para los propósitos de este ejercicio, un ángulo se considerará "suficientemente pequeño" si su magnitud no es mayor a 0.1 radianes). Estas ideas son incorporadas en los siguientes procedimientos:

```scheme
(define (cubo x) (* x x x))

(define (p x) (- (* 3 x) (* 4 (cubo x))))

(define (sen angulo)
   (if (not (> (abs angulo) 0.1))
       angulo
       (p (sen (/ angulo 3.0)))))
```

a. ¿Cuántas veces se aplicará el procedimiento `p` cuando `sen(12.15)` es evaluado?

b. ¿Cuál es el orden de crecimiento en espacio y en número de pasos (en función de `a`) utilizados por el proceso generado por el procedimiento `sen` cuando `(sen a)` es evaluado?


### 1.2.4 Exponenciación

Considere el problema de calcular el exponencial de un número dado. Queremos un procedimiento que tome como argumentos una base `b` y un exponente entero positivo `n` y que calcule `bⁿ`. Una forma de hacer esto sería mediante la definición recursiva

```
bⁿ = b . bⁿ⁻¹
b⁰ = 1
```

que se traduce fácilmente al procedimiento

```scheme
(define (exp b n)
  (if (= n 0)
      1
      (* b (exp b (- n 1)))))
```

Este es un proceso recursivo lineal, que requiere de `Θ(n)` pasos y `Θ(n)` espacio. Del mismo modo que con el factorial, podemos formular fácilmente una iteración lineal equivalente:

```scheme
(define (exp b n)
  (exp-iter b n 1))

(define (exp-iter b contador producto)
  (if (= contador 0)
      producto
      (exp-iter b
                (- contador 1)
                (* b producto)))) 
```

Esta versión requiere `Θ(n)` pasos y de `Θ(1)` espacio.

Podemos calcular exponenciales en menos pasos usando cuadráticas sucesivas. Por ejemplo, en lugar de computar `b⁸` como

```
b . (b . (b . (b . (b . (b . (b . b))))))
```

podemos calcularlo usando tres multiplicaciones: 

```
b² = b . b
b⁴  = b² . b²
b⁸  = b⁴ . b⁴
```

Este método funciona bien para exponentes que son potencias de 2. También podemos aprovechar en general las cuadráticas sucesivas en el cálculo de exponenciales si usamos la regla

```
bⁿ = (b⁽ⁿ/²⁾)²     si n es par
bⁿ = b . bⁿ⁻¹      si n es impar
```

Podemos expresar este método como un procedimiento:

```scheme
(define (exp-rapido b n)
  (cond ((= n 0) 1)
        ((par? n) (cuadrado (exp-rapido b (/ n 2))))
        (else (* b (exp-rapido b (- n 1))))))
```

donde el predicado para determinar si un número entero es par se define en términos del procedimiento primitivo `remainder` (NdT: *remanente* o *resto* en español) de la siguiente manera

```scheme
(define (par? n)
  (= (remainder n 2) 0))
```

El proceso desarrollado por `exp-rapido` crece logarítmicamente con `n` tanto en espacio como en número de pasos. Para ver esto, observe que computar `b²ⁿ` usando `exp-rapido` sólo requiere de una multiplicación más en comparación con `bⁿ`. El tamaño del exponente que podemos calcular se duplica (aproximadamente) con cada nueva multiplicación permitida. De este modo, el número de multiplicaciones requeridas para un exponente de `n` crece tan rápido como el logaritmo de `n` de base 2. El proceso tiene un crecimiento de `Θ(log n)`.[^37]

La diferencia entre el crecimiento de `Θ(log n)` y el crecimiento de `Θ(n)` se vuelve sorprendente a medida que `n` se hace más grande. Por ejemplo, `exp-rapido` para `n = 1000` solo requiere de sólo 14 multiplicaciones.[^38] También es posible usar la idea de cuadráticas sucesivas para idear un algoritmo iterativo que calcule exponenciales con un número logarítmico de pasos (ver ejercicio 1.16), aunque, como suele suceder con los algoritmos iterativos, esto no se escribe de manera tan directa como los algoritmos recursivos.[^39]

**Ejercicio 1.16.** Diseñar un procedimiento que desarrolle un proceso de exponenciación iterativo, que utilice cuadráticas sucesivas y un número logarítmico de pasos, igual como en  `exp-rápido` (sugerencia: partiendo de la observación de que `(bⁿ/²)² = (b²)ⁿ/²`, mantener junto con el exponente `n` y la base `b` una variable de estado adicional `a`, y definir la transformación del estado de tal forma que el producto `bⁿ` no sufra ningún cambio de un estado a otro. Al principio del proceso `a` se toma como 1, y la respuesta viene dada por el valor de `a` al final del proceso. En general, la técnica de definir una *cantidad invariable* que permanece sin cambiar de un estado a otro es una forma poderosa de pensar el diseño de los algoritmos iterativos). 

**Ejercicio 1.17.** Los algoritmos de exponenciación de esta sección se basan en la realización de la exponenciación misma mediante la multiplicación repetida. De manera similar, se puede realizar la multiplicación de números enteros por medio de la suma repetida. El siguiente procedimiento de multiplicación (en el cual se asume que nuestro lenguaje sólo puede sumar, no multiplicar) es análogo al procedimiento `exp`:

```scheme
(define (* a b)
  (if (= b 0)
      0
      (+ a (* a (- b 1)))))
```

Este algoritmo realiza una serie de pasos que son lineales en `b`. Ahora supongamos que incluimos, junto con la suma, las operaciones `doble`, que duplica un entero, y `mitad`, que divide un entero (par) por 2. Usando estos, diseñar un procedimiento de multiplicación análogo a `exp-rapido` que utilice un número logarítmico de pasos. 

**Ejercicio 1.18.** Usando los resultados de los ejercicios 1.16 y 1.17, idear un procedimiento que genere un proceso iterativo para multiplicar dos números enteros en términos de sumar, duplicar y dividir a la mitad, y que use un número logarítmico de pasos.[^40].

**Ejercicio 1.19.** Existe un algoritmo ingenioso para calcular los números de Fibonacci en una cantidad logarítmica de pasos. Recordemos la transformación de las variables de estado `a` y `b` en el proceso `fib-iter` de la [sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Árbol-de-Recursión): `a ← a + b` y `b ← a`. Llamemos a esta transformación `T`, y observemos que la aplicación de `T` una y otra vez `n` veces, comenzando con 1 y 0, produce el par `Fib(n + 1)` y `Fib(n)`. En otras palabras, los números de Fibonacci se producen aplicando `Tⁿ`, la enésima potencia de la transformación `T`, comenzando con la pareja `(1,0)`. Ahora consideremos el caso especial de `p = 0` y `q = 1` en una familia de transformaciones `Tₚᵩ`, donde `Tₚᵩ` transforma la pareja `(a,b)` según `a ← bq + aq + ap` y `b ← bp + aq`. Mostrar que si aplicamos tal transformación `Tₚᵩ` dos veces, el efecto es el mismo que si usáramos una sola transformación `Tᵖ'ᵠ'` de la misma forma, y calcular `p'` y `q'` en términos de `p` y `q`. Esto nos da una forma explícita de elevar al cuadrado estas transformaciones, y así podemos calcular `Tⁿ` usando cuadráticas sucesivas, como en el procedimiento `exp-rapido`. Reúna todo esto para completar el siguiente procedimiento, que corre en un número logarítmico de pasos:[^41]

```scheme
(define (fib n)
  (fib-iter 1 0 0 1 n))

(define (fib-iter a b p q contador)
  (cond ((= contador 0) b)
        ((par? contador)
         (fib-iter a
                   b
                   <??>      ; compute p'
                   <??>      ; compute q'
                   (/ contador 2)))
        (else (fib-iter (+ (* b q) (* a q) (* a p))
                        (+ (* b p) (* a q))
                        p
                        q
                        (- contador 1)))))
```


### 1.2.5 Máximos Comunes Divisores

El máximo común divisor (MCD) de dos enteros `a` y `b` se define como el mayor entero que divide tanto `a` como `b` sin dejar resto. Por ejemplo, el MCD de 16 y 28 es 4. En el [capítulo 2](./13-capitulo-2-intro.md), cuando investiguemos cómo implementar la aritmética de números racionales, necesitaremos ser capaces de calcular los MCD's para reducir los números racionales a sus términos más bajos (para reducir un número racional a los términos más bajos, debemos dividir tanto el numerador como el denominador por su MCD. Por ejemplo, `16/28` se reduce a `4/7`). Una manera de encontrar el MCD de dos enteros es factorizarlos y buscar factores comunes, pero hay un algoritmo famoso que es mucho más eficiente.

La idea de este algoritmo se basa en la observación de que si `r` es el resto cuando `a` se divide por `b`, entonces los divisores comunes de `a` y `b` son precisamente los mismos que los divisores comunes de `b` y `r`. Por lo tanto, podemos usar la ecuación

```
MCD(a,b) = MCD(b,r)
```

para reducir sucesivamente el problema de calcular un MCD al problema de calcular el MCD de pares cada vez más pequeños de enteros. Por ejemplo

```
MCD(206,40) = MCD(40,6)
            = MCD(6,4)
            = MCD(4,2)
            = MCD(2,0)
            = 2
```

reduce MCD(206,40) a MCD(2,0), que es 2. Es posible mostrar que comenzando con dos números enteros positivos cualesquiera y realizando reducciones repetidas siempre se producirá eventualmente un par donde el segundo número es 0. Entonces el MCD es el otro número del par. Este método para calcular el MCD se conoce como el *Algoritmo de Euclides*.[^42]

Es fácil expresar el Algoritmo de Euclides como un procedimiento:

```scheme
(define (mcd a b)
  (if (= b 0)
      a
      (mcd b (remainder a b))))
```

Esto genera un proceso iterativo, cuyo número de pasos crece como el logaritmo de los números implicados.

El hecho de que el número de pasos requeridos por el Algoritmo de Euclides tenga crecimiento logarítmico conlleva una relación interesante con los números de Fibonacci:

***Teorema de Lamé:** Si el Algoritmo de Euclides requiere `k` pasos para calcular el MCD de algún par, entonces el número más pequeño en el par debe ser mayor o igual al k-ésimo número de Fibonacci.*[^43]

Podemos usar este teorema para obtener una estimación del orden de crecimiento del Algoritmo de Euclides. Sea `n` la más pequeña de las dos entradas del procedimiento. Si el proceso toma `k` pasos, entonces debemos tener `n >= Fib(k) ≈ Φᵏ/√5`. Por lo tanto, el número de pasos `k` crece como el logaritmo (a la base `Φ`) de `n`. Por lo tanto, el orden de crecimiento es `Θ(log n)`.

**Ejercicio 1.20.** El proceso que un procedimiento genera es, por supuesto, dependiente de las reglas utilizadas por el intérprete. Como ejemplo, considere el procedimiento iterativo de `mcd` dado arriba. Supongamos que interpretamos este procedimiento usando la evaluación de orden normal, como se discutió en la [sección 1.1.5]((./10-capitulo-1-seccion-1-1.md#115-El-Modelo-de-Sustitución-para-la-Aplicación-de-Procedimientos) (la regla de evaluación de orden normal para `if` se describe en el ejercicio 1.5.). Utilizando el método de sustitución (para el orden normal), ilustre el proceso generado en la evaluación `(mcd 206 40)` e indique las operaciones `remainder` que se efectúan realmente. ¿Cuántas operaciones `remainder` se realizan realmente en la evaluación de orden normal de `(mcd 206 40)`? ¿Y en la evaluación del orden aplicativo? 


### 1.2.6 Ejemplo: Evaluando por Primalidad

Esta sección describe dos métodos para comprobar la primalidad de un entero `n`, uno con orden de crecimiento `Θ(√n)`, y un algoritmo "probabilístico" con orden de crecimiento `Θ(log n)`. Los ejercicios al final de esta sección sugieren proyectos de programación basados en estos algoritmos.


#### Búsqueda de divisores

Desde la antigüedad, los matemáticos han estado fascinados por los problemas relacionados con los números primos, y muchas personas han trabajado en el problema de determinar maneras de comprobar si los números son tales. Una manera de chequear que un número es primo es encontrar los divisores del mismo. El siguiente programa busca el divisor integral más pequeño (mayor que 1) de un número `n` dado. Esto se hace de forma directa, al probar la divisibilidad de `n` mediante números enteros sucesivos que empiecen a partir de 2.

```scheme
(define (menor-divisor n)
  (encontrar-divisor n 2))

(define (encontrar-divisor n test-divisor)
  (cond ((> (cuadrado test-divisor) n) n)
        ((divide? test-divisor n) test-divisor)
        (else (encontrar-divisor n (+ test-divisor 1)))))

(define (divide? a b)
  (= (remainder b a) 0))
```

Podemos probar si un número es primo de la siguiente manera: `n` es primo si y sólo si `n` es su propio divisor más pequeño.

```scheme
(define (primo? n)
  (= n (menor-divisor n)))
```

La prueba final de `encontrar-divisor` se basa en el hecho de que si `n` no es primo debe tener un divisor menor o igual a `√n`.[^44] Esto significa que el algoritmo sólo necesita probar divisores entre 1 y `√n`. Consecuentemente, el número de pasos necesarios para identificar a `n` como primo tendrá un orden de crecimiento `Θ(√n)`.


#### El test de Fermat

El test de primalidad de `Θ(log n)` se basa en un resultado de la teoría de números conocida como el Pequeño Teorema de Fermat.[^45]

***El pequeño teorema de Fermat:** Si `n` es un número primo y `a` es un número entero positivo menor que `n`, entonces `a` elevado a la enésima potencia es congruente con `a` módulo `n`.*

(se dice que dos números son de *congruencia modulo* `n` -NdT: traducción del inglés de *congruent modulo*- si ambos tienen el mismo resto cuando son divididos por `n`. El resto de un número `a` cuando es dividido por `n` también es referido como el *remanente de `a` modulo `n`*, o simplemente como `a` modulo `n`).

Si `n` no es primo, entonces en general la mayoría de los números `a < n` no cumplirán la relación mencionada arriba. Esto nos lleva al siguiente algoritmo para probar la primalidad: Dado un número `n`, se elige un número aleatorio `a < n` y se calcula el resto de un módulo `n`. Si el resultado no es igual a `a`, entonces `n` no es ciertamente el primo. Si es `a`, entonces hay buenas chances de que `n` sea primo. Ahora elija otro número aleatorio `a` y pruebe con el mismo método. Si también satisface la ecuación, entonces podemos estar aún más seguros de que `n` es el primo. Probando más y más valores de `a`, podemos aumentar nuestra confianza en el resultado. Este algoritmo se conoce como el test de Fermat.

Para implementar el test de Fermat, necesitamos un procedimiento que calcule el exponencial de un número módulo otro número:

```scheme
(define (exp-mod base exp m)
  (cond ((= exp 0) 1)
        ((par? exp)
         (remainder (cuadrado (exp-mod base (/ exp 2) m))
                    m))
        (else
         (remainder (* base (exp-mod base (- exp 1) m))
                    m))))    
```

Esto es muy similar al procedimiento de `exp-rapido` de la [sección 1.2.4]((./11-capitulo-1-seccion-1-2.md#124-Exponenciación)). Utiliza cuadráticas sucesivas, de modo que el número de pasos crece logarítmicamente con el exponente.[^46]

El test de Fermat se realiza eligiendo al azar un número `a` entre 1 y `n - 1` inclusive, y comprobando si el resto del módulo `n` de la enésima potencia de `a` es igual a `a`. El número aleatorio `a` se elige usando el procedimiento `random`, el cual asumimos que está incluido como procedimiento primitivo en Scheme. `random` devuelve un número entero no negativo menor que el valor de su número entero de entrada. Por lo tanto, para obtener un número aleatorio entre 1 y `n - 1`, llamamos a `random` con una entrada de `n - 1` y sumamos 1 al resultado:

```scheme
(define (test-fermat n)
  (define (probar a)
    (= (exp-mod a n n) a))

  (probar (+ 1 (random (- n 1)))))
```

El siguiente procedimiento ejecuta el test un número determinado de veces, según lo especificado por un parámetro. Su valor será verdadero si la prueba tiene éxito siempre, y falso en caso contrario.

```scheme
(define (primo-rapido? n veces)
  (cond ((= veces 0) true)
        ((test-fermat n) (primo-rapido? n (- veces 1)))
        (else false)))
```

#### Métodos probabilísticos

El test de Fermat difiere en carácter de la mayoría de los algoritmos conocidos, en los que se calcula una respuesta que garantiza que es correcta. En este caso, la respuesta que obtenemos es probablemente correcta. Más precisamente, si `n` alguna vez falla la prueba de Fermat, podemos estar seguros de que `n` no es primo. Pero el hecho de que `n` pase la prueba, aunque resulte ser una indicación extremadamente fuerte, no es garantía de que `n` sea primo. Lo que nos gustaría decir es que para cualquier número `n`, si realizamos la prueba suficientes veces y encontramos que `n` siempre pasa la prueba, entonces la probabilidad de error en nuestra prueba de primalidad puede ser tan pequeña como queramos.

Desafortunadamente, esta afirmación no es del todo correcta. Existen números que engañan al test de Fermat: números `n` que no son primos y sin embargo tienen la propiedad de que `aⁿ` es congruente con `a` módulo `n` para todos los enteros `a < n`. Tales números son extremadamente raros, así que el test de Fermat resulta bastante fiable en la práctica.[^47] Hay algunas variantes del test de Fermat que no pueden ser engañadas. En estos tests, al igual que con el método Fermat, se prueba la primalidad de un entero `n` eligiendo un entero al azar `a < n` y comprobando alguna condición que dependa de `n` y de `a` (ver ejercicio 1.28 para un ejemplo de tal test). Por otro lado, en contraste con el test de Fermat, se puede probar que, para cualquier `n`, la condición no es válida para la mayoría de los enteros `a < n` a menos que `n` sea primo. Por lo tanto, si `n` pasa la prueba para alguna elección aleatoria de `a`, las probabilidades son mejores que si incluso esa `n` sea primo. Si `n` pasa la prueba para dos opciones aleatorias de `a`, las probabilidades son mejores en 3 de cada 4 de que `n` sea primo. Al ejecutar la prueba con más y más valores elegidos al azar de `a` podemos hacer que la probabilidad de error sea tan pequeña como queramos.

La existencia de tests para los que se puede probar que la posibilidad de error puede reducirse arbitrariamente ha despertado el interés en los algoritmos de este tipo, que se han llegado a conocer como *algoritmos probabilísticos*. Hay mucha actividad de investigación en esta área, y los algoritmos probabilísticos se han aplicado fructíferamente a muchos campos[^48].


**Ejercicio 1.21.** Utilice el procedimiento `menor-divisor` para encontrar el divisor más pequeño de cada uno de los siguientes números: 199, 1999, 19999.

**Ejercicio 1.22.** La mayoría de las implementaciones de Lisp incluyen un primitivo llamado `runtime` que devuelve un entero que especifica la cantidad de tiempo que el sistema ha estado funcionando (medido, por ejemplo, en microsegundos). El siguiente procedimiento de `test-primo-cronometrado`, cuando se llama con un número entero `n`, imprime `n` y comprueba si `n` es primo. Si `n` es primo, el procedimiento imprime tres asteriscos seguidos de la cantidad de tiempo utilizado para realizar el test.

```scheme
(define (test-primo-cronometrado n)
  (newline)
  (display n)
  (iniciar-test-primo n (runtime)))

(define (iniciar-test-primo n tiempo-inicio)
  (if (primo? n)
      (reporte-primo (- (runtime) tiempo-inicio))))

(define (reporte-primo tiempo-transcurrido)
  (display " *** ")
  (display tiempo-transcurrido))
```

(NdT 1: `newline` y `display` son también primitivos de Scheme: el primero permite hacer un salto de linea, y el segundo muestra el valor pasado como argumento en pantalla).

(NdT 2: `runtime` podría no estar en otras implementaciones de Scheme, como por ejemplo Racket. Pero para este último caso, `current-milliseconds` cumpliría exactamente la misma función que `runtime`).

Usando este procedimiento, escriba un procedimiento `buqueda-de-primos` que compruebe la primalidad de números enteros impares consecutivos dentro de un rango especificado. Use este procedimiento para encontrar los tres primos más pequeños mayores de 1000; mayores de 10,000; mayores de 100,000; mayores de 1,000,000. Anote el tiempo necesario para probar cada primo. Dado que el algoritmo de prueba tiene un orden de crecimiento de `Θ(√n)`, debe esperar que las pruebas para primos alrededor de 10,000 tarden alrededor de √10 veces más tiempo que las pruebas para primos de alrededor de 1000. ¿Sus datos de cronometraje lo confirman? ¿Qué tan bien los datos para 100.000 y 1.000.000 soportarían la predicción de `√n`? ¿Su resultado es compatible con la noción de que los programas de su computadora corren en un tiempo proporcional al número de pasos requeridos para dicho cálculo?

**Ejercicio 1.23.** El procedimiento del `menor-divisor` que se muestra al principio de esta sección hace muchas pruebas innecesarias: después de comprobar si el número es divisible por 2, no tiene sentido comprobar si es divisible por números pares más grandes. Esto sugiere que los valores usados para el `test-divisor` no deberían ser `2, 3, 4, 5, 6, ...`, sino `2, 3, 5, 7, 9, ...` Para implementar este cambio, defina un procedimiento que devuelva 3 si su entrada es igual a 2 y de lo contrario que devuelva su entrada más 2. Modifique el procedimiento `menor-divisor` para usar `(siguiente-test-divisor)` en lugar de `(+ test-divisor 1)`. Con `test-primo-cronometrado` incorporando esta versión modificada de `menor-divisor`, ejecute la prueba para cada uno de los 12 primos encontrados en el ejercicio 1.22. Dado que esta modificación reduce a la mitad el número de pasos de prueba, es de esperar que se ejecute el doble de rápido. ¿Se confirma esta expectativa? Si no es así, ¿cuál es la relación observada de las velocidades de los dos algoritmos, y cómo explica el hecho de que sea diferente de 2?

**Ejercicio 1.24.** Modifique el procedimiento de `test-primo-cronometrado` del ejercicio 1.22 para usar el método de `primo-rapido?` (el método de Fermat), y pruebe cada uno de los 12 primos que usted encontró en ese ejercicio. Ya que el test de Fermat tiene un crecimiento de `Θ(log n)`, ¿cómo esperaría que el tiempo de testar primos de casi 1.000.000 se compare con el tiempo que se necesita para testar primos de alrededor de 1000? ¿Sus datos lo confirman? ¿Puede explicar cualquier discrepancia que encuentre?

**Ejercicio 1.25.** Alyssa P. Hacker se queja de que hicimos demasiado trabajo extra al escribir `exp-mod`. Después de todo, dice, dado que ya sabemos cómo calcular los exponenciales podríamos simplemente haber escrito

```scheme
(define (exp-mod base exp m)
  (remainder (exp-rapido base exp) m))
```

¿Es correcto lo que ella dice? ¿Serviría este procedimiento también para nuestro test rápido de números primos? Explicar.

**Ejercicio 1.26.** Louis Reasoner está teniendo grandes dificultades para hacer el ejercicio 1.24. Su prueba de `primo-rapido?` parece ser más lenta que su prueba de `primo?`. Louis llama a su amiga Eva Lu Ator para que le ayude. Cuando examinan el código de Louis, descubren que él ha reescrito el procedimiento `exp-mod` para usar una multiplicación explícita, en lugar de llamar a `cuadrado`:

```scheme
(define (exp-mod base exp m)
  (cond ((= exp 0) 1)
        ((par? exp)
         (remainder (* (exp-mod base (/ exp 2) m)
                       (exp-mod base (/ exp 2) m))
                    m))
        (else
         (remainder (* base (exp-mod base (- exp 1) m))
                    m))))
```

"No veo qué diferencia puede hacer", dice Louis. "Yo sí", dice Eva. "Al escribir el procedimiento de esta manera, transformamos el proceso de `Θ(log n)` en un proceso de `Θ(n)`". Explicar.

**Ejercicio 1.27.** Demostrar que los números de Carmichael listados en la nota[^47] realmente engañan al test de Fermat. Es decir, escriba un procedimiento que tome un número entero `n` y compruebe si un es congruente con un `a` módulo `n` para cada `a < n`, y pruebe su procedimiento con los números de Carmichael dados.

**Ejercicio 1.28.** Una variante del test Fermat que no se puede engañar se llama el test Miller-Rabin (Miller 1976; Rabin 1980). Este parte de una forma alternativa del Pequeño Teorema de Fermat, que establece que si `n` es un número primo y `a` es un número entero positivo menor que `n`, entonces `a` elevada a `(n - 1)` potencia es congruente con `1` módulo `n`. Para probar la primalidad de un número `n` por la prueba de Miller-Rabin, elegimos un número aleatorio `a < n` y elevamos `a` a la  `(n - 1)` potencia modulo `n` usando el procedimiento `exp-mod`. Sin embargo, siempre que realicemos el paso de elevar al cuadrado en `exp-mod`, comprobaremos si hemos descubierto una "raíz cuadrada no trivial de `1` módulo `n`", es decir, un número no igual a 1 o `n - 1` cuyo cuadrado es igual a `1` módulo `n`. Es posible probar que si existe tal raíz cuadrada no trivial de 1, entonces `n` no es primo. También es posible probar que si `n` es un número impar que no es primo, entonces, para al menos la mitad de los números `a < n`, el cálculo de `aⁿ⁻¹` de esta manera revelará una raíz cuadrada no trivial de `1` módulo `n` (esta es la razón por la que la prueba de Miller-Rabin no puede ser engañada). Modifique el procedimiento `exp-mod` para señalar si este descubre una raíz cuadrada no trivial de 1, y utilícelo para implementar la prueba de Miller-Rabin con un procedimiento análogo a `test-fermat`. Revise su procedimiento probando varios primos y no primos conocidos. Sugerencia: Una forma conveniente de señalar con `exp-mod` es que este devuelva 0.

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

[^32]: Un ejemplo de esto fue mencionado en la [sección 1.1.3](./10-capitulo-1-seccion-1-1.md#113-Evaluando-Combinaciones): El propio intérprete evalúa las expresiones usando un proceso árbol-recursivo.

[^33]: Por ejemplo, analice detalladamente cómo se aplica la regla de reducción al problema de hacer cambios de 10 centavos utilizando monedas de un centavo y de cinco centavos.

[^34]: Un enfoque para hacer frente a los cálculos redundantes es organizar las cosas de manera tal que automáticamente construyamos una tabla de valores a medida que se calculan. Cada vez que se nos pide que apliquemos el procedimiento a algún argumento, primero buscamos si el valor ya está almacenado en la tabla, en cuyo caso evitamos realizar el cálculo redundante. Esta estrategia, conocida como *tabulación* o *memoization*, puede ser implementada de manera sencilla. La tabulación se puede utilizar a veces para transformar procesos que requieren de un número exponencial de pasos (tal como sucede en `contar-cambio`) en procesos cuyos requisitos de espacio y tiempo crecen linealmente con la entrada de datos. Ver ejercicio 3.27.

[^35]: Los elementos del triángulo de Pascal se llaman *coeficientes binomiales*, porque la enésima fila consiste en los coeficientes de los términos en la expansión de `(x + y)ⁿ`. Este patrón para calcular los coeficientes apareció en el trabajo seminal de Blaise Pascal en 1653 sobre la teoría de la probabilidad, *Traité du triangle arithmétique*. Según Knuth (1973), el mismo patrón aparece en el *Szu-yuen Yü-chien* ("El precioso espejo de los cuatro elementos"), publicado por el matemático chino Chu Shih-chieh en 1303, en las obras del matemático hindú del siglo XII Bháscara Áchárya.

[^36]: Estas declaraciones enmascaran una gran cantidad de simplificación. Por ejemplo, si contamos los pasos del proceso como "operaciones de máquina", estamos haciendo la suposición de que el número de operaciones de máquina necesarias para realizar, por ejemplo, una multiplicación es independiente del tamaño de los números a multiplicar, lo que es falso si los números son lo suficientemente grandes. Observaciones similares son válidas también para las estimaciones de espacio. Al igual que el diseño y la descripción de un proceso, el análisis de un proceso puede llevarse a cabo en varios niveles de abstracción.

[^37]: Más precisamente, el número de multiplicaciones requeridas es igual a 1 menos que la base logarítmica 2 de `n`, más el número de multiplicaciones en la representación binaria de `n`. Este total es siempre menos del doble de la base logarítmica 2 de `n`. Las constantes arbitrarias `k1` y `k2` en la definición de notación de orden implican que, para un proceso logarítmico, la base a la que se llevan los logaritmos no importa, así que todos esos procesos se describen como `Θ(log n)`.

[^38]: Se preguntará por qué a alguien le importaría elevar los números a la 1000va potencia. Vea la [sección 1.2.6](./11-capitulo-1-seccion-1-2.md#126-Ejemplo-Evaluando-por-Primalidad).

[^39]: Este algoritmo iterativo es antiguo.  Aparece en el *Chandah-sutra* de Áchárya Pingala, escrito antes del año 200 a.C. Ver Knuth 1981, sección 4.6.3, para una discusión completa y análisis de este y otros métodos de exponenciación.

[^40]: Este algoritmo, que a veces se conoce como el "método ruso" (NdT: en inglés *Russian peasant method*) de multiplicación, es antiguo. Ejemplos de su uso se encuentran en el Papiro de Rhind, uno de los dos documentos matemáticos más antiguos que existen, escrito alrededor del año 1700 a.C. (y copiado a partir de un documento aún más antiguo) por un escriba egipcio llamado A'h-mose.

[^41]: Este ejercicio nos fue sugerido por Joe Stoy, basado en un ejemplo de Kaldewaij 1990. 

[^42]: El Algoritmo de Euclides se llama así porque aparece en los Elementos de Euclides (Libro 7, ca. 300 A.C.). Según Knuth (1973), puede considerarse el algoritmo no trivial más antiguo conocido. El antiguo método egipcio de multiplicación (ejercicio 1.18) es seguramente más antiguo, pero, como explica Knuth, el algoritmo de Euclides es el más antiguo que se sabe que se ha presentado como un algoritmo general, más que como un conjunto de ejemplos ilustrativos.

[^43]: Este teorema fue probado en 1845 por Gabriel Lamé, un matemático e ingeniero francés conocido principalmente por sus contribuciones a la física matemática. Para probar el teorema, consideramos los pares `(aₖ ,bₖ)`, donde `aₖ > bₖ`, para los cuales el Algoritmo de Euclides termina en `k` pasos. La prueba se basa en la afirmación de que, si `(aₖ₊₁, bₖ₊₁) → (aₖ, bₖ) → (aₖ₋₁, bₖ₋₁)` son tres pares sucesivos en el proceso de reducción, entonces debemos tener `bₖ₊₁ >= bₖ + bₖ₋₁`. Para verificar el reclamo, considere que un paso de reducción se define aplicando la transformación `aₖ₋₁ = bₖ, bₖ₋₁ =` resto de `aₖ` dividido por `bₖ`. La segunda ecuación significa que `aₖ = qbₖ + bₖ₋₁` para algún número entero positivo `q`. Y como `q` debe ser al menos 1, tenemos `aₖ = qbₖ + bₖ₋₁ >= bₖ + bₖ₋₁`. Pero en el paso de reducción anterior tenemos `bₖ₊₁ = aₖ`. Por lo tanto, `bₖ₊₁ = aₖ >= bₖ + bₖ₋₁`. Esto verifica la afirmación. Ahora podemos probar el teorema por inducción en `k`, el número de pasos que el algoritmo requiere para terminar. El resultado es verdadero para `k = 1`, ya que esto simplemente requiere que `b` sea al menos tan grande como `Fib(1) = 1`. Ahora, asuma que el resultado es verdadero para todos los enteros menores o iguales a `k` y establezca el resultado para `k + 1`. Que `(aₖ₊₁, bₖ₊₁) → (aₖ, bₖ) (aₖ₋₁, bₖ₋₁)` sean pares sucesivos en el proceso de reducción. Por nuestras hipótesis de inducción, tenemos `bₖ₋₁ >= Fib(k - 1)` y `bₖ> Fib(k)`. Así, aplicando la afirmación que acabamos de probar junto con la definición de los números de Fibonacci se obtiene que `bₖ₊₁ > bₖ + bₖ₋₁> Fib(k) + Fib(k - 1) = Fib(k + 1)`, lo que completa la prueba del Teorema de Lamé.

[^45]: Pierre de Fermat (1601-1665) es considerado como el fundador de la teoría moderna de números. Obtuvo muchos resultados teóricos importantes, pero él generalmente anunciaba sólo los resultados, sin proporcionar sus pruebas. El Pequeño Teorema de Fermat fue declarado en una carta que escribió en 1640. La primera prueba publicada fue dada por Euler en 1736 (y una prueba anterior, idéntica, fue descubierta en los manuscritos inéditos de Leibniz). El más famoso de los resultados de Fermat -conocido como el Último Teorema de Fermat- fue escrito en 1637 en su copia del libro *Aritmética* (por el matemático griego del siglo III Diophantus) con la observación "He descubierto una prueba verdaderamente notable, pero este margen es demasiado pequeño para incluirla". Encontrar una prueba del último teorema de Fermat se convirtió en uno de los desafíos más famosos de la teoría de números. Una solución completa fue finalmente dada en 1995 por Andrew Wiles, de la Universidad de Princeton.

[^46]: Los pasos de reducción en los casos en los que el exponente `e` es mayor que 1 se basan en el hecho de que, para cualquier conjunto de números enteros `x`, `y`, y `m`, podemos encontrar el resto de `x` veces y modulo `m` calculando separadamente los restos de `x` modulo `m` y `y` modulo `m`, multiplicándolos, y luego tomando el resto del resultado modulo `m`. Por ejemplo, en el caso de que `e` sea par, calculamos el resto del módulo `m`, lo elevamos al cuadrado, y tomamos el resto del módulo `m`. Esta técnica es de gran utilidad porque significa que podemos realizar nuestro cálculo sin tener que tratar nunca con números mucho mayores que `m` (compare con el ejercicio 1.25).

[^47]: Los números que engañan al test de Fermat se llaman números de Carmichael, y poco se sabe de ellos aparte de que son extremadamente raros.  Hay 255 números de Carmichael por debajo de 100.000.000. Los más pequeños son 561, 1105, 1729, 2465, 2821, y 6601. Al probar la primalidad de números muy grandes elegidos al azar, la posibilidad de tropezar con un valor que engaña a la prueba de Fermat es menor que la posibilidad de que la radiación cósmica haga que la computadora cometa un error al llevar a cabo un algoritmo "correcto". Considerar que un algoritmo como inadecuado por la primera razón, pero no por la segunda, ilustra la diferencia entre las matemáticas y las ingenierías.

[^48]: Una de las aplicaciones más sorprendentes de las pruebas probabilísticas ha sido en el campo de la criptografía. Aunque no sea ahora factible calcular el factor de un número arbitrario de 200 dígitos, la primalidad de tal número puede ser comprobada en pocos segundos con el test de Fermat. Este hecho constituye la base de una técnica para construir "códigos irrompibles" sugerida por Rivest, Shamir y Adleman (1977). El *algoritmo RSA* resultante se ha convertido en una técnica ampliamente utilizada para mejorar la seguridad de las comunicaciones electrónicas. Debido a esto y a los desarrollos relacionados, el estudio de los números primos, que antes se consideraba el epítome de un tema de las matemáticas "puras" para ser estudiado sólo por sí mismo, ahora resulta tener importantes aplicaciones prácticas para la criptografía, la transferencia electrónica de fondos y la recuperación de información. 
