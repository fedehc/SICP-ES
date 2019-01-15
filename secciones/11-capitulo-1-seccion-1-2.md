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

Compare los dos procesos. Desde un punto de vista, apenas parecen diferentes entre sí. Ambos computan la misma función matemática en el mismo dominio, y cada uno requiere un número de pasos proporcional a `n` para calcular `n!`. De hecho, ambos procesos incluso llevan a cabo la misma secuencia de multiplicaciones, obteniendo la misma secuencia de productos parciales. Pero cuando consideramos las "formas" de los dos procesos, nos damos cuenta de que evolucionan de una manera muy diferente.

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



## ---Traducción pendiente---
