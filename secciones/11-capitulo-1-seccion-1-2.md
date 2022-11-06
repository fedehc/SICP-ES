## 1.2 Procedimientos y los Procesos que Generan

Ya hemos considerado los elementos de la programación: hemos usado operaciones aritméticas primitivas, hemos combinado estas operaciones, y las hemos abstraído definiéndolas como procedimientos compuestos. Pero esto no es suficiente como para poder decir que ya sabemos programar. Nuestra situación es análoga a la de alguien que aprendió las reglas de cómo se mueven las piezas en el ajedrez pero que no sabe nada de aperturas típicas, tácticas, o estrategias. Al igual que el jugador de ajedrez novato, todavía no conocemos los patrones comunes de uso en este campo. Nos falta el conocimiento acerca de qué movimientos valen la pena hacer (qué procedimientos vale la pena definir). Nos falta la experiencia para predecir las consecuencias de hacer una jugada (ejecutar un procedimiento).

La capacidad de visualizar las consecuencias de las acciones en cuestión es crucial para convertirse en un programador experto, como lo es en cualquier actividad sintética y creativa. Para ser un fotógrafo experto, por ejemplo, uno debe aprender a mirar una escena y saber cuán oscura aparecerá cada región en una impresión para cada posible elección de condiciones de exposición y revelado. Sólo entonces uno puede razonar hacia atrás, planificando el encuadre, la iluminación, la exposición y el desarrollo para obtener los efectos deseados. Así es también con la programación, donde planificamos el curso de acción a tomar por un proceso y donde controlamos el proceso por medio de un programa. Para convertirnos en expertos, debemos aprender a visualizar los procesos generados por varios tipos de procedimientos. Sólo después de que hayamos desarrollado tal habilidad podemos aprender a construir de forma fiable programas que exhiban el comportamiento deseado.

Un procedimiento es un patrón para la *evolución local* de un proceso computacional. En él se especifica cómo se construye cada etapa del proceso a partir de la etapa previa. Nos gustaría poder hacer afirmaciones sobre el comportamiento general, o  *global*, de un proceso cuya evolución local ha sido especificada por un procedimiento. Esto es muy difícil de hacer en general, pero al menos podemos intentar describir algunos patrones típicos de la evolución de los procesos.

En esta sección examinaremos algunas "formas" comunes de procesos generados por procedimientos simples. También investigaremos el ritmo con el que estos procesos consumen valiosos recursos computacionales de tiempo y espacio. Los procedimientos que consideraremos son muy sencillos. Su papel es como el que desempeñan las pruebas de patrones en fotografía: como patrones prototípicos sobresimplificados, más que como ejemplos prácticos en sí mismos.


### 1.2.1 Recursión e Iteración Lineales

![Figura 1.3](./imagenes/capitulo-1/figura-1-3.png)

**Figura 1.3:** Un proceso recursivo lineal para el cálculo de `6!`.

Empezamos considerando a la función factorial, definida como

```
n! = n . (n-1) . (n-2) ... 3 - 2 - 1
```

Hay varias maneras de calcular factoriales. Una forma es hacer uso de la observación de que `n!` es igual a `n` veces `(n - 1)!` para cualquier entero positivo `n`:

```
n! = n . [(n-1) . (n-2) ... 3 - 2 - 1]  =  n . (n-1)!
```

Así, podemos calcular `n!` calculando `(n - 1)!` y multiplicando el resultado por `n`. Si agregamos la condición de que `1!` es igual a 1, esta observación se traduce directamente a un procedimiento:

```scheme
(define (factorial n)
  (if (= n 1)
      1
      (* n (factorial (- n 1)))))
```

Podemos usar el modelo de sustitución de la [sección 1.1.5](./10-capitulo-1-seccion-1-1.md#115-El-Modelo-de-Sustitución-para-la-Aplicación-de-Procedimientos) para observar este procedimiento en acción calculando `6!`, como se muestra en la figura 1.3.

Tomemos ahora una perspectiva diferente en el cálculo de factoriales. Podríamos describir una regla para calcular `n!` especificando primero que multiplicamos 1 por 2, luego multiplicamos el resultado por 3, luego por 4, y así sucesivamente hasta alcanzar `n`. Más formalmente, tenemos un producto en ejecución, junto con un contador que contará de 1 a `n`. Podemos describir este cálculo diciendo que el contador y el producto cambian simultáneamente de un paso al siguiente según la regla

```
producto ← contador · producto

contador ← contador + 1
```

y estipular que `n!` es el valor del producto cuando el contador excede `n`.

![Figura 1.4](./imagenes/capitulo-1/figura-1-4.png)

**Figura 1.4:** Un proceso iterativo lineal para el cálculo de `6!`.

Una vez más, podemos reformular nuestra descripción como un procedimiento para calcular factoriales:<sup>[**29**](#nota-29)</sup>

```scheme
(define (factorial n)
  (fact-iter 1 1 n))

(define (fact-iter producto contador maximo)
  (if (> contador maximo)
      producto
      (fact-iter (* contador producto)
                 (+ contador 1)
                 maximo)))
```

Como en el caso anterior, podemos usar el modelo de sustitución para visualizar el proceso de calcular `6!`, como se muestra en la figura 1.4.

Comparemos los dos procesos. Desde un punto de vista, apenas parecen diferentes. Ambos calculan la misma función matemática en el mismo dominio, y cada uno requiere un número de pasos proporcional a `n` para calcular `n!`. De hecho, ambos procesos llevan a cabo la misma secuencia de multiplicaciones, obteniendo la misma secuencia de productos parciales. Por otro lado, cuando consideramos las "formas" de los dos procesos, descubrimos que evolucionan de manera muy diferente.

Analicemos ahora el primer proceso. El modelo de sustitución revela una forma de expansión seguida de una contracción, indicada por la flecha de la figura 1.3. La expansión ocurre a medida de que el proceso construye una cadena de *operaciones diferidas* (en este caso, una cadena de multiplicaciones). La contracción se produce a medida que se realizan las operaciones. Este tipo de proceso, caracterizado por una cadena de operaciones diferidas, se denomina *proceso recursivo*. Para llevar a cabo este proceso es necesario que el intérprete lleve el registro de las operaciones que se van a realizar posteriormente. En el cálculo de `n!`, la longitud de la cadena de multiplicaciones diferidas, y por lo tanto la cantidad de información necesaria para seguirla, crece linealmente con `n` (o sea, es proporcional a `n`), así como el número de pasos. Este proceso se denomina *proceso recursivo lineal*.

En contraste, el segundo proceso no crece ni se contrae. En cada paso, todo lo que necesitamos para hacer el seguimiento, para cualquier `n`, son los valores actuales de las variables `producto`, `contador`, y `maximo`. Llamamos a esto un *proceso iterativo*. En general, un proceso iterativo es aquel cuyo estado puede ser resumido por un número fijo de *variables de estado*, junto con una regla fija que describe cómo deben actualizarse estas variables a medida que el proceso se desplaza de un estado a otro y una prueba final (opcional) que especifica las condiciones bajo las cuales el proceso debe terminar. En el cálculo de `n!`, el número de pasos requeridos crece linealmente con `n`. Este proceso se llama *proceso iterativo lineal*.

El contraste entre los dos procesos puede verse de otra manera. En el caso iterativo, las variables del programa proveen una descripción completa del estado del proceso en cualquier punto. Si detuviésemos el cálculo entre los pasos, todo lo que tendríamos que hacer para reanudar al mismo es proporcionar al intérprete con los valores de las tres variables del programa. No sucede así con el proceso recursivo. En este último caso existe una información adicional "oculta", mantenida por el intérprete y no contenida en las variables del programa, que indica "en dónde se encuentra el proceso" en la gestión de la cadena de operaciones diferidas. Cuanto más larga sea la cadena, más información debe ser mantenida.<sup>[**30**](#nota-30)</sup>

Al contrastar la iteración y la recursividad, debemos tener cuidado de no confundir la noción de un *proceso* recursivo con la noción de un *procedimiento* recursivo. Cuando describimos un procedimiento como recursivo, nos referimos al hecho sintáctico de que la definición del procedimiento se refiere (sea directa o indirectamente) al procedimiento mismo. Pero cuando describimos un proceso que sigue un patrón que es, digamos, linealmente recursivo, estamos hablando de cómo evoluciona el proceso, no acerca de la sintaxis de cómo un procedimiento es escrito. Puede parecer desconcertante que nos refiramos a un procedimiento recursivo como `fact-iter` como generador de un proceso iterativo. Sin embargo, el proceso es realmente iterativo: su estado es capturado completamente por sus tres variables de estado, y un intérprete sólo tiene que llevar la cuenta de las tres variables para ejecutar el proceso.

Una razón por la que la distinción entre proceso y procedimiento puede resultar confusa es que la mayoría de las implementaciones de los lenguajes habituales (incluyendo Ada, Pascal y C) están diseñadas de manera tal que la interpretación de cualquier procedimiento recursivo consume una cantidad de memoria que crece con el número de llamadas de procedimiento, incluso cuando el proceso descripto es, en principio, iterativo. Como consecuencia, estos lenguajes pueden describir procesos iterativos sólo recurriendo a "constructos de bucles" de propósito especial tales como `do`, `repeat`, `until`, `for` y `while`. La implementación de Scheme que examinaremos en el [capítulo 5](./30-capitulo-5-intro.md) no comparte este defecto. Este ejecutará un proceso iterativo en espacio constante, aún cuando el proceso iterativo sea descripto por un procedimiento recursivo.  Una implementación con esta propiedad se la llama *recursión de cola* `(NdT: traducción libre de "tail-recursive")`. Con una implementación de recursión de cola, la iteración puede expresarse utilizando el mecanismo habitual de llamada de procedimiento, de manera que las construcciones de iteración especiales son sólo útiles como azúcar sintáctico.<sup>[**31**](#nota-31)</sup>

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

Considere los siguientes procedimientos, donde `A` es el procedimiento definido anteriormente:

```scheme
(define (f n) (A 0 n))

(define (g n) (A 1 n))

(define (h n) (A 2 n))

(define (k n) (* 5 n n))
```

Dé definiciones matemáticas concisas para las funciones calculadas por los procedimientos `f`, `g`, y `h` para valores enteros positivos de `n`. Por ejemplo, `(k n)` calcula `5n²`.


### 1.2.2 Recursión de Árbol

Otro patrón común de cálculo es la *Recursión de Árbol* `(NdT: "Tree Recursion" en inglés)`. A modo de ejemplo, consideremos el cálculo de la secuencia de números de Fibonacci, en la que cada número es la suma de los dos anteriores:

```
0, 1, 1, 2, 3, 5, 8, 13, 21, ...
```

En general, los números de Fibonacci pueden ser definidos por la regla

```
         ⎧  0                    si n = 0
Fib(n) = ⎨  1                    si n = 1
         ⎩  Fib(n-1) + Fib(n-2)  en cualquier otro caso
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

**Figura 1.5:** El proceso árbol-recursivo generado durante el cálculo de `(fib 5)`.

Considere el patrón de este cálculo. Para calcular `(fib 5)`, calculamos `(fib 4)` y `(fib 3)`. Para calcular `(fib 4)`, calculamos `(fib 3)` y `(fib 2)`. En general, el proceso desarrollado se parece a un árbol, como se muestra en la figura 1.5. Note que las ramas se dividen en dos en cada nivel (excepto en la parte inferior); esto refleja el hecho de que el procedimiento `fib` se llama a sí mismo dos veces cada vez que es invocado.

Este procedimiento es instructivo como caso prototípico de una recursión de árbol, pero es una forma pésima de calcular los números de Fibonacci debido a que realiza demasiados cálculos redundantes. Note en la figura 1.5 que todo el cálculo de `(fib 3)` -casi la mitad del trabajo- está duplicado. De hecho, no es difícil mostrar que el número de veces que el procedimiento computará `(fib 1)` o `(fib 0)` (el número de hojas del árbol anterior, en general) es precisamente `Fib(n + 1)`. Para tener una idea de lo malo que es esto, uno puede demostrar que el valor de `Fib(n)` crece exponencialmente con `n`. Más precisamente (ver ejercicio 1.13), `Fib(n)` es el entero más cercano a `Φⁿ/√5`, donde

```
Φ = (1 + √5)/2 = 1,6810...
```

es el número áureo, que satisface la ecuación

```
Φ² = Φ + 1
```

Por lo tanto, el proceso utiliza una serie de pasos que crecen exponencialmente con la entrada. Por otra parte, el espacio requerido solo crece linealmente con la entrada, ya que sólo necesitamos llevar la cuenta de aquellos nodos que están por encima de nosotros dentro del árbol en cualquier punto del cálculo. En general, el número de pasos requeridos por un proceso de recursión de árbol será proporcional al número de nodos en el árbol, mientras que el espacio requerido será proporcional a la profundidad máxima del árbol.

También podemos formular un proceso iterativo para calcular los números de Fibonacci. La idea es usar un par de enteros `a` y `b`, inicializados a `Fib(1) = 1` y `Fib(0) = 0`, y aplicar repetidamente las transformaciones simultáneas 

```
a ← a + b

b ← a
```

No es difícil demostrar que, después de aplicar esta transformación `n` veces, `a` y `b` serán iguales respectivamente a `Fib(n + 1)` y `Fib(n)`. Por lo tanto, podemos calcular los números de Fibonacci de forma iterativa utilizando el procedimiento

```scheme
(define (fib n)
  (fib-iter 1 0 n))

(define (fib-iter a b contador)
  (if (= contador 0)
      b
      (fib-iter (+ a b) a (- contador 1))))
```

Este segundo método para calcular `Fib(n)` es una iteración lineal. La diferencia en el número de pasos requeridos por los dos métodos -uno lineal en `n`, el otro creciendo tan rápido como `Fib(n)` mismo- es enorme, incluso para entradas pequeñas.

Uno no debería concluir de esto que los procesos de recursión de árbol son inútiles. Cuando consideramos procesos que operan sobre datos estructurados jerárquicamente en lugar de números, encontraremos que estos son una herramienta natural y poderosa.<sup>[**32**](#nota-32)</sup> Incluso en operaciones numéricas, los procesos de recursión de árbol pueden ser útiles para ayudarnos a entender y diseñar programas. Por ejemplo, aunque el primer procedimiento `fib` es mucho menos eficiente que el segundo, es más sencillo, siendo poco más que una traducción a Lisp de la definición de la serie de Fibonacci. Para formular el algoritmo iterativo era necesario observar que el cálculo podía ser reformulado como una iteración con tres variables de estado.


#### Ejemplo: Contando el cambio

Sólo se necesita un poco de ingenio para concebir el algoritmo iterativo de Fibonacci. En contraste, considere el siguiente problema: ¿De cuántas maneras diferentes podemos hacer el cambio de $1.00, teniendo solamente monedas de 50, 25, 10, 5 y 1 centavos? De manera más general, ¿podemos escribir un procedimiento para calcular la cantidad de formas de cambiar una determinada cantidad de dinero?

Este problema tiene una solución simple como procedimiento recursivo. Supongamos que pensamos en los tipos de monedas disponibles dispuestos en algún orden. Entonces la siguiente relación es válida:

La cantidad de maneras de cambiar la cantidad `a` usando `n` tipos de monedas es igual a

* la cantidad de maneras de cambiar la cantidad `a` usando todas menos la primera clase de monedas, más

* el número de maneras de cambiar la cantidad `a - d` usando todos los tipos de monedas `n`, donde `d` es la denominación de la primera clase de monedas.

Para comprender por qué esto es cierto, observe que las formas de hacer el cambio se pueden dividir en dos grupos: los que no usan ninguna de la primera clase de monedas, y los que sí lo hacen. Por lo tanto, la cantidad total de maneras de hacer cambios por un monto es igual a la cantidad de maneras de hacer cambios por ese monto sin usar ninguna de la primera clase de monedas, más la cantidad de maneras de hacer cambios asumiendo que usamos la primera clase de monedas. Pero este último número es igual a la cantidad de maneras de hacer cambios por el monto que queda después de usar una moneda de la primera clase.

Así, podemos reducir recursivamente el problema de cambiar una cantidad dada al problema de cambiar cantidades más pequeñas usando menos tipos de monedas. Considere esta regla de reducción cuidadosamente, y comprenda a fondo de que podemos usarla para describir un algoritmo si especificamos los siguientes casos degradados:<sup>[**33**](#nota-33)</sup>

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

(El procedimiento `primera-denominacion` toma como entrada el número de clases de monedas disponibles y devuelve la primera denominación de monedas. En este caso estamos pensando en las monedas en orden desde el más grande hasta el más pequeño, pero con cualquier orden también funcionaría). Ahora podemos responder a nuestra pregunta inicial sobre el cambio de $1.00 `(NdT: $1.00 = 100 centavos)`:

```scheme
(contar-cambio 100)
> 292
```

`contar-cambio` genera un proceso de recursión de árbol con redundancias similares a las de nuestra primera implementación de `fib` (tomará un tiempo para que ese 292 sea calculado). Por otro lado, no es obvio que se pueda diseñar un mejor algoritmo para calcular el resultado, por lo que dejamos este problema como un desafío. La observación de que un proceso de recursión de árbol puede ser muy ineficiente pero a menudo fácil de especificar y de comprender ha llevado a algunos a proponer que se podría obtener lo mejor de ambos mundos diseñando un "compilador inteligente" que pudiera transformar los procedimientos de recursión de árbol en procedimientos más eficientes que calculen el mismo resultado.<sup>[**34**](#nota-34)</sup>

**Ejercicio 1.11.** Una función `f` se define por la regla que `f(n) = n` si `n < 3` y `f(n) = f(n - 1) + 2f(n - 2) + 3f(n - 3)` si `n > 3`. Escribir un procedimiento que calcule `f` mediante un proceso recursivo. Escriba un procedimiento que calcule `f` mediante un proceso iterativo.

**Ejercicio 1.12.** El siguiente patrón de números se llama el Triángulo de Pascal.

```
    1
   1 1
  1 2 1
 1 3 3 1
1 4 6 4 1
```

Los números en los bordes del triángulo son todos 1, y cada número dentro del triángulo es la suma de los dos números que están por encima de él.<sup>[**35**](#nota-35)</sup> Escriba un procedimiento que calcule los elementos del triángulo de Pascal mediante un proceso recursivo.

**Ejercicio 1.13.** Demuestre que `Fib(n)` es el entero más cercano a `Φⁿ/√5`, donde `Φ = (1 + √5)/2`. Pista: Usar `⨚ = (1 - √5)/2`. Utilice la inducción y la definición de los números de Fibonacci (ver [sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Recursión-de-Árbol)) para probar que `Fib(n) = (Φⁿ - ⨚ⁿ)/5`.


### 1.2.3 Órdenes de Crecimiento

Los ejemplos anteriores ilustran que los procesos pueden diferir considerablemente en los ritmos con los que consumen recursos computacionales. Una forma conveniente de describir esta diferencia es usar la noción de *orden de crecimiento* **\*** para obtener una medida en bruto de los recursos requeridos por un proceso a medida de que las entradas se hacen cada vez más grandes.

**\*** `NdT: Este concepto se lo conoce mejor como "Notación O"; del inglés "Big O Notation" o simplemente "Big O".`

Supongamos que `n` es un parámetro que mide el tamaño del problema y que `R(n)` es la cantidad de recursos que el proceso requiere para un problema de tamaño `n`. En nuestros ejemplos anteriores tomamos `n` como el número para el cual una función dada debe ser calculada, pero hay otras posibilidades. Por ejemplo, si nuestra meta es calcular una aproximación a la raíz cuadrada de un número, podríamos tomar a `n` como el número de dígitos de precisión que requerimos. Para la multiplicación de matrices podríamos tomar a `n` como el número de filas en las matrices. En general, hay una serie de propiedades del problema con respecto a las cuales será deseable analizar un proceso determinado. De manera similar, `R(n)` podría medir el número de registros internos de almacenamiento usados, el número de operaciones elementales de máquina realizadas, y así sucesivamente. En computadoras donde sólo se realizan un número fijo de operaciones a la vez, el tiempo requerido será proporcional al número de operaciones elementales efectuadas por la máquina.

Decimos que `R(n)` tiene orden de crecimiento `Θ(f(n))`, escrito `R(n) = Θ(f(n))` (pronunciado "theta de f(n)"), si hay constantes positivas `k₁` y `k₂` independientes de `n` tales que 

```
k₁ f(n) < R(n) < k₂ f(n)
```

para cualquier valor suficientemente grande de `n` (en otras palabras, para un gran `n`, el valor `R(n)` está entre `k₁ f(n)` y `k₂ f(n)`).

Por ejemplo, con el proceso recursivo lineal para calcular el factorial detallado en la [sección 1.2.1](#121-Recursión-e-Iteración-Lineales) el número de pasos crece proporcionalmente a la entrada `n`. Así, los pasos requeridos para este proceso crecen como `Θ(n)`. También vimos que el espacio requerido crece como `Θ(n)`. Para el factorial iterativo, el número de pasos sigue siendo `Θ(n)` pero el espacio es `Θ(1)`, es decir, constante.<sup>[**36**](#nota-36)</sup> El cálculo árbol-recursivo de Fibonacci requiere `Θ(n)` pasos y espacio `Θ(Φⁿ)`, donde `Φ` es la proporción áurea descrita en la [sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Árbol-de-Recursión).

Los órdenes de crecimiento sólo proporcionan una descripción aproximada del comportamiento de un proceso. Por ejemplo, un proceso que requiere `n²` pasos, otro proceso que requiere `1000n²` pasos y otro proceso que requiere `3n² + 10n + 17` pasos, todos estos tienen un orden de crecimiento `Θ(n²)`. Por otro lado, el orden de crecimiento nos proporciona una indicación útil de cómo podemos esperar a que el comportamiento del proceso cambie a medida de que cambiemos el tamaño del problema. Para un proceso `Θ(n)` (lineal), duplicar el tamaño duplicará aproximadamente la cantidad de recursos utilizados. Para un proceso exponencial, cada incremento en el tamaño del problema multiplicará la utilización de recursos por un factor constante. En el resto de la [sección 1.2](./11-capitulo-1-seccion-1-2.md) examinaremos dos algoritmos cuyos órdenes de crecimiento son logarítmicos, de modo que duplicar el tamaño del problema aumentará la necesidad de recursos en una cantidad constante.


**Ejercicio 1.14.** Dibuje el árbol que ilustre el proceso generado por el procedimiento de `contar-cambio` de la [sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Árbol-de-Recursión) al hacer el cambio por 11 centavos. ¿Cuáles son los órdenes de crecimiento en espacio y del número de pasos utilizados por este proceso a medida que aumenta la cantidad a cambiar?  

**Ejercicio 1.15.** El seno de un ángulo (especificado en radianes) puede calcularse haciendo uso de la aproximación `sen(x) ≈ x` si `x` es suficientemente pequeño, y la identidad trigonométrica

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

b. ¿Cuál es el orden de crecimiento en espacio y el número de pasos (en función de `a`) utilizados por el proceso generado por el procedimiento `sen` cuando `(sen a)` es evaluado?


### 1.2.4 Exponenciación

Considere el problema de calcular el exponencial de un número dado. Queremos un procedimiento que tome como argumentos una base `b` y un exponente entero positivo `n` y que calcule `bⁿ`. Una forma de hacerlo es mediante la definición recursiva

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

Este es un proceso recursivo lineal, que requiere de `Θ(n)` pasos y `Θ(n)` espacio. Al igual que en el factorial, podemos formular fácilmente una iteración lineal equivalente:

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

Esta versión requiere `Θ(n)` pasos y `Θ(1)` espacio.

Podemos calcular exponenciales en menos pasos usando sucesivas elevaciones al cuadrado. Por ejemplo, en lugar de computar `b⁸` como

```
b . (b . (b . (b . (b . (b . (b . b))))))
```

podemos calcularlo usando tres multiplicaciones: 

```
b² = b . b
b⁴  = b² . b²
b⁸  = b⁴ . b⁴
```

Este método funciona bien para exponentes que son potencias de 2. También podemos aprovechar el cálculo de sucesivas elevaciones al cuadrado para calcular exponenciales en general si utilizamos la regla

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

donde el predicado para comprobar si un entero es par se define en términos del procedimiento `remainder` `(NdT: *remanente* o *resto* en español)` por

```scheme
(define (par? n)
  (= (remainder n 2) 0))
```

El proceso desarrollado por `exp-rapido` crece logarítmicamente con `n` tanto en espacio como en número de pasos. Para ver esto, observe que computar `b²ⁿ` usando `exp-rapido` sólo requiere de una multiplicación más en comparación con `bⁿ`. El tamaño del exponente que podemos calcular se duplica (aproximadamente) con cada nueva multiplicación que se nos permite. De este modo, el número de multiplicaciones necesarias para un exponente de `n` crece tan rápido como el logaritmo de `n` de base 2. El proceso tiene un crecimiento de `Θ(log n)`.<sup>[**37**](#nota-37)</sup>

La diferencia entre el crecimiento de `Θ(log n)` y el crecimiento de `Θ(n)` se vuelve sorprendente a medida que `n` se hace más grande. Por ejemplo, `exp-rapido` para `n = 1000` requiere sólo 14 multiplicaciones.<sup>[**38**](#nota-38)</sup> También es posible usar la idea de cuadráticas sucesivas para idear un algoritmo iterativo que calcule exponenciales con un número logarítmico de pasos (ver ejercicio 1.16), aunque, como suele suceder con los algoritmos iterativos, esto no se escribe tan directamente como el algoritmo recursivo.<sup>[**39**](#nota-39)</sup>

**Ejercicio 1.16.** Diseñar un procedimiento que desarrolle un proceso de exponenciación iterativo, que utilice cuadráticas sucesivas y un número logarítmico de pasos, como lo hace `exp-rápido` (sugerencia: partiendo de la observación de que `(bⁿ/²)² = (b²)ⁿ/²`, mantener junto con el exponente `n` y la base `b` una variable de estado adicional `a`, y definir la transformación del estado de tal forma que el producto `bⁿ` no cambie de estado en estado. Al principio del proceso `a` se toma como 1, y la respuesta viene dada por el valor de `a` al final del proceso. En general, la técnica de definir una *cantidad invariable* que permanece inalterada de un estado a otro es una forma poderosa de pensar el diseño de los algoritmos iterativos). 

**Ejercicio 1.17.** Los algoritmos de exponenciación de esta sección se basan en realizar la exponenciación mediante la multiplicación repetida. De forma similar, se puede realizar la multiplicación de enteros mediante la suma repetida. El siguiente procedimiento de multiplicación (en el cual se asume que nuestro lenguaje sólo puede sumar, no multiplicar) es análogo al procedimiento `exp`:

```scheme
(define (* a b)
  (if (= b 0)
      0
      (+ a (* a (- b 1)))))
```

Este algoritmo realiza una serie de pasos que es lineal en `b`. Ahora supongamos que incluimos, junto con la suma, las operaciones `doble`, que duplica un entero, y `mitad`, que divide un entero (par) por 2. Usando estos, diseñar un procedimiento de multiplicación análogo a `exp-rapido` que utilice un número logarítmico de pasos. 

**Ejercicio 1.18.** Usando los resultados de los ejercicios 1.16 y 1.17, idear un procedimiento que genere un proceso iterativo para multiplicar dos números enteros en términos de sumar, duplicar y dividir a la mitad, y que use un número logarítmico de pasos.<sup>[**40**](#nota-40)</sup>.

**Ejercicio 1.19.** Existe un algoritmo ingenioso para calcular los números de Fibonacci en un número logarítmico de pasos. Recordemos la transformación de las variables de estado `a` y `b` en el proceso `fib-iter` de la [sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Árbol-de-Recursión): `a ← a + b` y `b ← a`. Llamemos a esta transformación `T`, y observemos que la aplicación de `T` una y otra vez `n` veces, comenzando con 1 y 0, produce el par `Fib(n + 1)` y `Fib(n)`. En otras palabras, los números de Fibonacci se producen aplicando `Tⁿ`, la enésima potencia de la transformación `T`, comenzando con el par `(1,0)`. Ahora consideremos el caso especial de `p = 0` y `q = 1` en una familia de transformaciones `Tₚᵩ`, donde `Tₚᵩ` transforma el par `(a,b)` según `a ← bq + aq + ap` y `b ← bp + aq`. Demostrar que si aplicamos tal transformación `Tₚᵩ` dos veces, el efecto es el mismo que si usáramos una sola transformación `Tᵖ'ᵠ'` de la misma forma, y calcular `p'` y `q'` en términos de `p` y `q`. Esto nos da una forma explícita de elevar al cuadrado estas transformaciones, y así podemos calcular `Tⁿ` usando cuadráticas sucesivas, como en el procedimiento `exp-rapido`. Reúna todo esto para completar el siguiente procedimiento, que corre en un número logarítmico de pasos:<sup>[**41**](#nota-41)</sup>

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

El máximo común divisor (MCD) de dos enteros `a` y `b` se define como el mayor entero que divide tanto `a` como `b` sin que quede ningún resto. Por ejemplo, el MCD de 16 y 28 es 4. En el [capítulo 2](./13-capitulo-2-intro.md), cuando investiguemos cómo implementar la aritmética de números racionales, necesitaremos ser capaces de calcular los MCD's para reducir los números racionales a sus términos más bajos (para reducir un número racional a los términos más bajos, debemos dividir tanto el numerador como el denominador por su MCD. Por ejemplo, `16/28` se reduce a `4/7`). Una forma de encontrar el MCD de dos enteros es factorizarlos y buscar los factores comunes, pero hay un famoso algoritmo que es mucho más eficiente.

La idea de este algoritmo se basa en la observación de que si `r` es el resto cuando `a` se divide por `b`, entonces los divisores comunes de `a` y `b` son precisamente los mismos que los divisores comunes de `b` y `r`. Por lo tanto, podemos usar la ecuación

```
MCD(a,b) = MCD(b,r)
```

para reducir sucesivamente el problema de calcular un MCD al problema de calcular el MCD de pares de enteros cada vez más pequeños. Por ejemplo

```
MCD(206,40) = MCD(40,6)
            = MCD(6,4)
            = MCD(4,2)
            = MCD(2,0)
            = 2
```

reduce MCD(206,40) a MCD(2,0), que es 2. Es posible demostrar que comenzando con dos números enteros positivos cualesquiera y realizando reducciones repetidas, siempre se obtendrá un par en el que el segundo número es 0. Entonces el MCD es el otro número del par. Este método para calcular el MCD se conoce como el *Algoritmo de Euclides*.<sup>[**42**](#nota-42)</sup>

Es fácil expresar el Algoritmo de Euclides como un procedimiento:

```scheme
(define (mcd a b)
  (if (= b 0)
      a
      (mcd b (remainder a b))))
```

Esto genera un proceso iterativo, cuyo número de pasos crece como el logaritmo de los números involucrados.

El hecho de que el número de pasos requeridos por el Algoritmo de Euclides tenga crecimiento logarítmico conlleva una relación interesante con los números de Fibonacci:

***Teorema de Lamé:** Si el Algoritmo de Euclides requiere `k` pasos para calcular el MCD de algún par, entonces el número más pequeño del par debe ser mayor o igual al k-ésimo número de Fibonacci.*<sup>[**43**](#nota-43)</sup>

Podemos usar este teorema para obtener una estimación del orden de crecimiento del Algoritmo de Euclides. Sea `n` la menor de las dos entradas del procedimiento. Si el proceso requiere de `k` pasos, entonces debemos tener `n >= Fib(k) ≈ Φᵏ/√5`. Por lo tanto, el número de pasos `k` crece como el logaritmo (a la base `Φ`) de `n`. Por lo tanto, el orden de crecimiento es `Θ(log n)`.

**Ejercicio 1.20.** El proceso que genera un procedimiento depende, por supuesto, de las reglas utilizadas por el intérprete. Como ejemplo, considere el procedimiento iterativo `mcd` dado anteriormente. Supongamos que interpretamos este procedimiento usando la evaluación de orden normal, como discutimos en la [sección 1.1.5](./10-capitulo-1-seccion-1-1.md#115-El-Modelo-de-Sustitución-para-la-Aplicación-de-Procedimientos) (la regla de evaluación de orden normal para `if` se describe en el ejercicio 1.5.). Utilizando el método de sustitución (para el orden normal), ilustre el proceso generado en la evaluación `(mcd 206 40)` e indique las operaciones `remainder` que se efectúan realmente. ¿Cuántas operaciones `remainder` se realizan realmente en la evaluación de orden normal de `(mcd 206 40)`? ¿Y en la evaluación del orden aplicativo? 


### 1.2.6 Ejemplo: Evaluando por Primalidad

En esta sección se describen dos métodos para comprobar la primalidad de un entero `n`, uno con orden de crecimiento `Θ(√n)`, y un algoritmo "probabilístico" con orden de crecimiento `Θ(log n)`. Los ejercicios al final de esta sección sugieren proyectos de programación basados en estos algoritmos.


#### Búsqueda de divisores

Desde la antigüedad, los matemáticos han estado fascinados por los problemas relacionados con los números primos, y muchas personas han trabajado en el problema de determinar maneras de comprobar si los números son tales. Una manera de chequear que un número es primo es encontrar los divisores del mismo. El siguiente programa busca el divisor integral más pequeño (mayor que 1) de un número `n` dado. Esto se hace de forma directa, al probar la divisibilidad de `n` mediante números enteros sucesivos empezando por 2.

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

La prueba final de `encontrar-divisor` se basa en el hecho de que si `n` no es primo debe tener un divisor menor o igual a `√n`.<sup>[**44**](#nota-44)</sup> Esto significa que el algoritmo sólo necesita probar divisores entre 1 y `√n`. En consecuencia, el número de pasos necesarios para identificar a `n` como primo tendrá un orden de crecimiento `Θ(√n)`.


#### El test de Fermat

El test de primalidad de `Θ(log n)` se basa en un resultado de la teoría de números conocido como el Pequeño Teorema de Fermat.<sup>[**45**](#nota-45)</sup>

***El pequeño teorema de Fermat:** Si `n` es un número primo y `a` es cualquier número entero positivo menor que `n`, entonces `a` elevado a la enésima potencia es congruente con `a` módulo `n`.*

(se dice que dos números son de *congruentes modulo\** `n` si ambos tienen el mismo resto cuando se dividen por `n`. El resto de un número `a` cuando es dividido por `n` también es referido como el *resto del modulo `n`*, o simplemente como `a` modulo `n`).

**\*** `NdT: "congruent modulo" en inglés.`


Si `n` no es primo, entonces en general la mayoría de los números `a < n` no satisfacen la relación anteriormente mencionada. Esto nos lleva al siguiente algoritmo para probar la primalidad: Dado un número `n`, se elige un número al azar `a < n` y se calcula el resto de `aⁿ`módulo `n`. Si el resultado no es igual a `a`, entonces ciertamente `n` no es primo. Si es `a`, entonces es muy probable que `n` sea primo. Ahora elija otro número al azar `a` y pruebe con el mismo método. Si también satisface la ecuación, entonces podemos estar aún más seguros de que `n` es el primo. Probando más y más valores de `a`, podemos aumentar nuestra confianza en el resultado. Este algoritmo se conoce como el test de primalidad de Fermat.

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

Esto es muy similar al procedimiento de `exp-rapido` de la [sección 1.2.4]((./11-capitulo-1-seccion-1-2.md#124-Exponenciación)). Utiliza cuadráticas sucesivas, de modo que el número de pasos crece logarítmicamente con el exponente.<sup>[**46**](#nota-46)</sup>

El test de Fermat se realiza eligiendo al azar un número `a` entre 1 y `n - 1` inclusive, y comprobando si el resto del módulo `n` de la enésima potencia de `a` es igual a `a`. El número aleatorio `a` se elige usando el procedimiento `random`, el cual asumimos que está incluido como procedimiento primitivo en Scheme. `random` devuelve un número entero no negativo menor que el valor de su entero de entrada. Por lo tanto, para obtener un número aleatorio entre 1 y `n - 1`, llamamos a `random` con una entrada de `n - 1` y sumamos 1 al resultado:

```scheme
(define (test-fermat n)
  (define (probar a)
    (= (exp-mod a n n) a))

  (probar (+ 1 (random (- n 1)))))
```

El siguiente procedimiento ejecuta el test un número determinado de veces, según lo especificado por un parámetro. Su valor será verdadero si la prueba tiene éxito en cada ocasión, y falso en caso contrario.

```scheme
(define (primo-rapido? n veces)
  (cond ((= veces 0) true)
        ((test-fermat n) (primo-rapido? n (- veces 1)))
        (else false)))
```

#### Métodos probabilísticos

El test de Fermat difiere en carácter de la mayoría de los algoritmos conocidos, en los que se calcula una respuesta que garantiza que es correcta. En este caso, la respuesta que obtenemos es probablemente correcta. Más precisamente, si `n` alguna vez falla la prueba de Fermat, podemos estar seguros de que `n` no es primo. Pero el hecho de que `n` pase la prueba, aunque resulte ser una indicación extremadamente fuerte, no es garantía de que `n` sea primo. Lo que nos gustaría decir es que para cualquier número `n`, si realizamos la prueba suficientes veces y encontramos que `n` siempre pasa la prueba, entonces la probabilidad de error en nuestra prueba de primalidad puede ser tan pequeña como queramos.

Desafortunadamente, esta afirmación no es del todo correcta. Existen números que engañan al test de Fermat: números `n` que no son primos y sin embargo tienen la propiedad de que `aⁿ` es congruente con `a` módulo `n` para todos los enteros `a < n`. Tales números son extremadamente raros, así que el test de Fermat resulta bastante fiable en la práctica.<sup>[**47**](#nota-47)</sup> Hay algunas variantes del test de Fermat que no pueden ser engañadas. En estos tests, al igual que con el método Fermat, se prueba la primalidad de un entero `n` eligiendo un entero al azar `a < n` y comprobando alguna condición que dependa de `n` y de `a` (ver ejercicio 1.28 para un ejemplo de tal test). Por otro lado, en contraste con el test de Fermat, se puede probar que, para cualquier `n`, la condición no es válida para la mayoría de los enteros `a < n` a menos que `n` sea primo. Por lo tanto, si `n` pasa la prueba para alguna elección aleatoria de `a`, las probabilidades son mejores que si incluso esa `n` sea primo. Si `n` pasa la prueba para dos opciones aleatorias de `a`, las probabilidades son mejores en 3 de cada 4 de que `n` sea primo. Al ejecutar la prueba con más y más valores elegidos al azar de `a` podemos hacer que la probabilidad de error sea tan pequeña como queramos.

La existencia de tests para los que se puede probar que la posibilidad de error puede reducirse arbitrariamente ha despertado el interés en los algoritmos de este tipo, que se han llegado a conocer como *algoritmos probabilísticos*. Hay mucha actividad de investigación en esta área, y los algoritmos probabilísticos se han aplicado fructíferamente a muchos campos<sup>[**48**](#nota-48)</sup>.


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

Usando este procedimiento, escriba un procedimiento `busqueda-de-primos` que compruebe la primalidad de números enteros impares consecutivos dentro de un rango especificado. Use este procedimiento para encontrar los tres primos más pequeños mayores de 1000; mayores de 10,000; mayores de 100,000; mayores de 1,000,000. Anote el tiempo necesario para probar cada primo. Dado que el algoritmo de prueba tiene un orden de crecimiento de `Θ(√n)`, debe esperar que las pruebas para primos alrededor de 10,000 tarden alrededor de √10 veces más tiempo que las pruebas para primos de alrededor de 1000. ¿Sus datos de cronometraje lo confirman? ¿Qué tan bien los datos para 100.000 y 1.000.000 soportarían la predicción de `√n`? ¿Su resultado es compatible con la noción de que los programas de su computadora corren en un tiempo proporcional al número de pasos requeridos para dicho cálculo?

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


<a name="nota-29">**29**</a>: En un programa real probablemente usaríamos la estructura de bloques introducida en la última sección para ocultar la definición de `fact-iter`:

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

<a name="nota-30">**30**</a>: Cuando discutamos la implementación de procedimientos en máquinas de registro en el [capítulo 5](./30-capitulo-5-intro.md), veremos que cualquier proceso iterativo puede ser realizado "en hardware" como una máquina que tiene un conjunto de registros fijos y sin memoria auxiliar. Por el contrario, la realización de un proceso recursivo requiere una máquina que utilice una estructura de datos auxiliar conocida como *stack*.

<a name="nota-31">**31**</a>: La recursividad de la cola se conoce desde hace tiempo como un truco de optimización del compilador. Una base semántica coherente para la recursión de la cola fue proporcionada por Carl Hewitt (1977), quien la explicó en términos del modelo de computación de "pasar mensajes" que discutiremos en el [capítulo 3](./19-capitulo-3-intro.md). Inspirados por esto, Gerald Jay Sussman y Guy Lewis Steele Jr (ver Steele 1975) construyeron un intérprete de cola recurrente para Scheme. Más tarde Steele mostró cómo la recursividad de la cola es una consecuencia de la forma natural de compilar las llamadas de procedimiento (Steele 1977). El estándar IEEE para Scheme requiere que las implementaciones de Scheme sean recursivas. 

<a name="nota-32">**32**</a>: Un ejemplo de esto fue mencionado en la [sección 1.1.3](./10-capitulo-1-seccion-1-1.md#113-Evaluando-Combinaciones): El propio intérprete evalúa las expresiones usando un proceso árbol-recursivo.

<a name="nota-33">**33**</a>: Por ejemplo, analice detalladamente cómo se aplica la regla de reducción al problema de hacer cambios de 10 centavos utilizando monedas de un centavo y de cinco centavos.

<a name="nota-34">**34**</a>: Un enfoque para hacer frente a los cálculos redundantes es organizar las cosas de manera tal que automáticamente construyamos una tabla de valores a medida que se calculan. Cada vez que se nos pide que apliquemos el procedimiento a algún argumento, primero buscamos si el valor ya está almacenado en la tabla, en cuyo caso evitamos realizar el cálculo redundante. Esta estrategia, conocida como *tabulación* o *memoization*, puede ser implementada de manera sencilla. La tabulación se puede utilizar a veces para transformar procesos que requieren de un número exponencial de pasos (tal como sucede en `contar-cambio`) en procesos cuyos requisitos de espacio y tiempo crecen linealmente con la entrada de datos. Ver ejercicio 3.27.

<a name="nota-35">**35**</a>: Los elementos del triángulo de Pascal se llaman *coeficientes binomiales*, porque la enésima fila consiste en los coeficientes de los términos en la expansión de `(x + y)ⁿ`. Este patrón para calcular los coeficientes apareció en el trabajo seminal de Blaise Pascal en 1653 sobre la teoría de la probabilidad, *Traité du triangle arithmétique*. Según Knuth (1973), el mismo patrón aparece en el *Szu-yuen Yü-chien* ("El precioso espejo de los cuatro elementos"), publicado por el matemático chino Chu Shih-chieh en 1303, en las obras del matemático hindú del siglo XII Bháscara Áchárya.

<a name="nota-36">**36**</a>: Estas declaraciones enmascaran una gran cantidad de simplificación. Por ejemplo, si contamos los pasos del proceso como "operaciones de máquina", estamos haciendo la suposición de que el número de operaciones de máquina necesarias para realizar, por ejemplo, una multiplicación es independiente del tamaño de los números a multiplicar, lo que es falso si los números son lo suficientemente grandes. Observaciones similares son válidas también para las estimaciones de espacio. Al igual que el diseño y la descripción de un proceso, el análisis de un proceso puede llevarse a cabo en varios niveles de abstracción.

<a name="nota-37">**37**</a>: Más precisamente, el número de multiplicaciones requeridas es igual a 1 menos que la base logarítmica 2 de `n`, más el número de multiplicaciones en la representación binaria de `n`. Este total es siempre menos del doble de la base logarítmica 2 de `n`. Las constantes arbitrarias `k1` y `k2` en la definición de notación de orden implican que, para un proceso logarítmico, la base a la que se llevan los logaritmos no importa, así que todos esos procesos se describen como `Θ(log n)`.

<a name="nota-38">**38**</a>: Se preguntará por qué a alguien le importaría elevar los números a la 1000va potencia. Vea la [sección 1.2.6](./11-capitulo-1-seccion-1-2.md#126-Ejemplo-Evaluando-por-Primalidad).

<a name="nota-39">**39**</a>: Este algoritmo iterativo es antiguo.  Aparece en el *Chandah-sutra* de Áchárya Pingala, escrito antes del año 200 a.C. Ver Knuth 1981, sección 4.6.3, para una discusión completa y análisis de este y otros métodos de exponenciación.

<a name="nota-40">**40**</a>: Este algoritmo, que a veces se conoce como el "método ruso" `(NdT: en inglés "Russian peasant method")` de multiplicación, es antiguo. Ejemplos de su uso se encuentran en el Papiro de Rhind, uno de los dos documentos matemáticos más antiguos que existen, escrito alrededor del año 1700 a.C. (y copiado a partir de un documento aún más antiguo) por un escriba egipcio llamado A'h-mose.

<a name="nota-41">**41**</a>: Este ejercicio nos fue sugerido por Joe Stoy, basado en un ejemplo de Kaldewaij 1990. 

<a name="nota-42">**42**</a>: El Algoritmo de Euclides se llama así porque aparece en los Elementos de Euclides (Libro 7, ca. 300 A.C.). Según Knuth (1973), puede considerarse el algoritmo no trivial más antiguo conocido. El antiguo método egipcio de multiplicación (ejercicio 1.18) es seguramente más antiguo, pero, como explica Knuth, el algoritmo de Euclides es el más antiguo que se sabe que se ha presentado como un algoritmo general, más que como un conjunto de ejemplos ilustrativos.

<a name="nota-43">**43**</a>: Este teorema fue probado en 1845 por Gabriel Lamé, un matemático e ingeniero francés conocido principalmente por sus contribuciones a la física matemática. Para probar el teorema, consideramos los pares `(aₖ ,bₖ)`, donde `aₖ > bₖ`, para los cuales el Algoritmo de Euclides termina en `k` pasos. La prueba se basa en la afirmación de que, si `(aₖ₊₁, bₖ₊₁) → (aₖ, bₖ) → (aₖ₋₁, bₖ₋₁)` son tres pares sucesivos en el proceso de reducción, entonces debemos tener `bₖ₊₁ >= bₖ + bₖ₋₁`. Para verificar el reclamo, considere que un paso de reducción se define aplicando la transformación `aₖ₋₁ = bₖ, bₖ₋₁ =` resto de `aₖ` dividido por `bₖ`. La segunda ecuación significa que `aₖ = qbₖ + bₖ₋₁` para algún número entero positivo `q`. Y como `q` debe ser al menos 1, tenemos `aₖ = qbₖ + bₖ₋₁ >= bₖ + bₖ₋₁`. Pero en el paso de reducción anterior tenemos `bₖ₊₁ = aₖ`. Por lo tanto, `bₖ₊₁ = aₖ >= bₖ + bₖ₋₁`. Esto verifica la afirmación. Ahora podemos probar el teorema por inducción en `k`, el número de pasos que el algoritmo requiere para terminar. El resultado es verdadero para `k = 1`, ya que esto simplemente requiere que `b` sea al menos tan grande como `Fib(1) = 1`. Ahora, asuma que el resultado es verdadero para todos los enteros menores o iguales a `k` y establezca el resultado para `k + 1`. Que `(aₖ₊₁, bₖ₊₁) → (aₖ, bₖ) (aₖ₋₁, bₖ₋₁)` sean pares sucesivos en el proceso de reducción. Por nuestras hipótesis de inducción, tenemos `bₖ₋₁ >= Fib(k - 1)` y `bₖ> Fib(k)`. Así, aplicando la afirmación que acabamos de probar junto con la definición de los números de Fibonacci se obtiene que `bₖ₊₁ > bₖ + bₖ₋₁> Fib(k) + Fib(k - 1) = Fib(k + 1)`, lo que completa la prueba del Teorema de Lamé.

<a name="nota-44">**44**</a>: Si `d` es un divisor de `n`, entonces también lo es `n/d`. Pero `d` y `n/d` no pueden ser ambos mayores que `n`. 

<a name="nota-45">**45**</a>: Pierre de Fermat (1601-1665) es considerado como el fundador de la teoría moderna de números. Obtuvo muchos resultados teóricos importantes, pero él generalmente anunciaba sólo los resultados, sin proporcionar sus pruebas. El Pequeño Teorema de Fermat fue declarado en una carta que escribió en 1640. La primera prueba publicada fue dada por Euler en 1736 (y una prueba anterior, idéntica, fue descubierta en los manuscritos inéditos de Leibniz). El más famoso de los resultados de Fermat -conocido como el Último Teorema de Fermat- fue escrito en 1637 en su copia del libro *Aritmética* (por el matemático griego del siglo III Diophantus) con la observación "He descubierto una prueba verdaderamente notable, pero este margen es demasiado pequeño para incluirla". Encontrar una prueba del último teorema de Fermat se convirtió en uno de los desafíos más famosos de la teoría de números. Una solución completa fue finalmente dada en 1995 por Andrew Wiles, de la Universidad de Princeton.

<a name="nota-46">**46**</a>: Los pasos de reducción en los casos en los que el exponente `e` es mayor que 1 se basan en el hecho de que, para cualquier conjunto de números enteros `x`, `y`, y `m`, podemos encontrar el resto de `x` veces y modulo `m` calculando separadamente los restos de `x` modulo `m` y `y` modulo `m`, multiplicándolos, y luego tomando el resto del resultado modulo `m`. Por ejemplo, en el caso de que `e` sea par, calculamos el resto del módulo `m`, lo elevamos al cuadrado, y tomamos el resto del módulo `m`. Esta técnica es de gran utilidad porque significa que podemos realizar nuestro cálculo sin tener que tratar nunca con números mucho mayores que `m` (compare con el ejercicio 1.25).

<a name="nota-47">**47**</a>: Los números que engañan al test de Fermat se llaman números de Carmichael, y poco se sabe de ellos aparte de que son extremadamente raros.  Hay 255 números de Carmichael por debajo de 100.000.000. Los más pequeños son 561, 1105, 1729, 2465, 2821, y 6601. Al probar la primalidad de números muy grandes elegidos al azar, la posibilidad de tropezar con un valor que engaña a la prueba de Fermat es menor que la posibilidad de que la radiación cósmica haga que la computadora cometa un error al llevar a cabo un algoritmo "correcto". Considerar que un algoritmo como inadecuado por la primera razón, pero no por la segunda, ilustra la diferencia entre las matemáticas y las ingenierías.

<a name="nota-48">**48**</a>: Una de las aplicaciones más sorprendentes de las pruebas probabilísticas ha sido en el campo de la criptografía. Aunque no sea ahora factible calcular el factor de un número arbitrario de 200 dígitos, la primalidad de tal número puede ser comprobada en pocos segundos con el test de Fermat. Este hecho constituye la base de una técnica para construir "códigos irrompibles" sugerida por Rivest, Shamir y Adleman (1977). El *algoritmo RSA* resultante se ha convertido en una técnica ampliamente utilizada para mejorar la seguridad de las comunicaciones electrónicas. Debido a esto y a los desarrollos relacionados, el estudio de los números primos, que antes se consideraba el epítome de un tema de las matemáticas "puras" para ser estudiado sólo por sí mismo, ahora resulta tener importantes aplicaciones prácticas para la criptografía, la transferencia electrónica de fondos y la recuperación de información. 
