### 1.2 Procedimientos y los Procesos que Generan

Ya hemos considerado los elementos de la programación: Hemos usado operaciones aritméticas primitivas, hemos combinado estas operaciones, y las hemos abstraído definiéndolas como procedimientos compuestos. Pero eso no es suficiente como para poder decir que sabemos programar. Nuestra situación es análoga a la de alguien que aprendió las reglas de cómo se mueven las piezas en el ajedrez pero que no sabe nada de las aperturas típicas, tácticas, o estrategias. Al igual que el jugador de ajedrez novato, todavía no conocemos los patrones comunes de uso en este campo. Nos falta el conocimiento sobre qué movimientos convienen hacer (qué procedimientos conviene definir). Nos falta la experiencia para predecir las consecuencias de hacer una jugada (ejecutar un procedimiento).

La capacidad de visualizar las consecuencias de las acciones bajo consideración es crucial para convertirse en un programador experto, como lo es en cualquier actividad sintética y creativa. Al convertirse en un fotógrafo experto, por ejemplo, uno debe aprender a mirar una escena y saber cuán oscura aparecerá cada región en una impresión para cada posible elección de condiciones de exposición y revelado. Sólo entonces uno puede razonar hacia atrás, planificando el encuadre, la iluminación, la exposición y el desarrollo para obtener los efectos deseados. Lo mismo ocurre con la programación, en la que planificamos el curso de acción a seguir por un proceso y en la que controlamos el proceso por medio de un programa. Para llegar a ser expertos, debemos aprender a visualizar los procesos generados por varios tipos de procedimientos. Sólo después de haber desarrollado tal habilidad podemos aprender a construir de manera confiable programas que muestren el comportamiento deseado.

Un procedimiento es un patrón para la *evolución local* de un proceso computacional. En él se especifica cómo se construye cada etapa del proceso a partir de la etapa anterior. Nos gustaría poder hacer declaraciones sobre el comportamiento general, o *global*, de un proceso cuya evolución local ha sido especificada por un procedimiento. Esto es muy difícil de hacer en general, pero al menos podemos intentar describir algunos patrones típicos de la evolución del proceso.

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

Podemos usar el modelo de sustitución de la [sección 1.1.5]((./10-capitulo-1-seccion-1-1.md#115-El-Modelo-de-Sustitución-para-la-Aplicación-de-Procedimientos)) para observar este procedimiento en el cálculo de `6!`, como se muestra en la figura 1.3.

Ahora tomemos una perspectiva diferente sobre el cálculo de factoriales. Podríamos describir una regla para calcular `n!` especificando que primero multiplicamos 1 por 2, luego multiplicamos el resultado por 3, luego por 4, y así sucesivamente hasta alcanzar `n`. Más formalmente, nosotros mantendremos un producto en ejecución, junto con un contador que contará de 1 a `n`. Podemos describir este cómputo diciendo que el contador y el producto cambian simultáneamente de un paso al siguiente según la regla

```
producto ← contador · producto

contador ← contador + 1
```

y estipular que `n!` es el valor del producto cuando el contador excede `n`.

![Figura 1.4](./imagenes/capitulo-1/figura-1-4.png)

**Figura 1.4:** Un proceso iterativo lineal para calcular `6!`.

Una vez más, podemos reformular nuestra descripción como un procedimiento para calcular factoriales: [^29] 

```scheme
(define (factorial n)
  (fact-iter 1 1 n))

(define (fact-iter producto contador max-count)
  (if (> contador max-count)
      producto
      (fact-iter (* contador producto)
                 (+ contador 1)
                 max-count)))
```

Como en el caso anterior, podemos usar el modelo de sustitución para visualizar el proceso de calcular `6!`, como se muestra en la figura 1.4.




## ---Traducción pendiente---