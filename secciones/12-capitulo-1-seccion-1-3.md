## 1.3 Formulación de Abstracciones con Procedimientos de Orden Superior

Hemos visto que los procedimientos son, en efecto, abstracciones que describen operaciones compuestas sobre números, independientemente de los números dados. Por ejemplo, cuando nosotros

```scheme
(define (al-cubo x) (* x x x))
```

no estamos hablando del cubo de un número en particular, sino de un método para obtener el cubo de cualquier número. Por supuesto que podríamos avanzar sin definir este procedimiento, escribiendo siempre las expresiones como

```scheme
(* 3 3 3)
(* x x x)
(* y y y) 
```

y nunca mencionar `al-cubo` explícitamente. Esto nos pondría en una seria desventaja, obligándonos a trabajar siempre al nivel de las operaciones particulares, que resultan ser primitivas en el lenguaje (multiplicación, en este caso), más que en términos de operaciones de nivel superior. Nuestros programas podrían calcular números al cubo, pero nuestro lenguaje carecería de la capacidad de expresar el concepto de elevar al cubo. Una de las cosas que debemos exigir de un potente lenguaje de programación es la capacidad de construir abstracciones asignando nombres a patrones comunes y luego trabajar en términos de abstracciones directamente. Los procedimientos proporcionan esta capacidad. Es por esto que todos los lenguajes de programación, excepto los más primitivos, incluyen mecanismos para definir procedimientos.

Aún incluso en el procesamiento numérico estaremos severamente limitados en nuestra capacidad de crear abstracciones si nos limitamos a procedimientos cuyos parámetros deban ser números. Con frecuencia el mismo patrón de programación se utiliza con varios procedimientos diferentes. Para expresar patrones como conceptos, necesitaremos construir procedimientos que puedan aceptar los procedimientos como argumentos o los procedimientos de retorno como valores. Los procedimientos que manipulan procedimientos se denominan *procedimientos de orden superior*. Esta sección describe cómo los procedimientos de orden superior pueden servir como poderosos mecanismos de abstracción, aumentando enormemente el poder expresivo de nuestro lenguaje.


### 1.3.1 Procedimientos como Argumentos


Considere los siguientes tres procedimientos. El primero calcula la suma de los enteros de `a` hasta `b`:

```scheme
(define (suma-enteros a b)
  (if (> a b)
      0
      (+ a (suma-enteros (+ a 1) b))))
```

El segundo calcula la suma de los enteros al cubo en el rango dado:

```scheme
(define (suma-cubos a b)
  (if (> a b)
      0
      (+ (al-cubo a) (suma-cubos(+ a 1) b))))
```

El tercero calcula la suma de una secuencia de términos de la serie 

```
  1       1        1
――――― + ――――― + ――――――― + ...
1 . 3   5 . 7   9  . 11
```

que converge en `π/8` (muy lentamente): [^49]

```scheme
(define (pi-suma a b)
  (if (> a b)
      0
      (+ (/ 1.0 (* a (+ a 2))) (pi-suma (+ a 4) b))))
```

Estos tres procedimientos comparten claramente un patrón subyacente común. Son en su mayor parte idénticos, difiriendo sólo en el nombre del procedimiento, la función `a` utilizada para calcular el término a añadir, y la función que proporciona el siguiente valor de `a`. Podríamos generar cada uno de los procedimientos rellenando espacios en la misma plantilla:

```scheme
(define (<nombre> a b)
  (if (> a b)
      0
      (+ (<termino> a)
         (<nombre> (<siguiente> a) b))))
```

La presencia de un patrón tan común es una fuerte evidencia de que existe una abstracción útil que espera ser traída a la superficie. De hecho, los matemáticos identificaron hace mucho tiempo la abstracción de la *suma de series* e inventaron la "notación sigma", por ejemplo

```
 ᵇ
 ∑ f(n) = f(a) + ... + f(b)
ⁿ⁼ᵃ
```

para expresar este concepto. El poder de la notación sigma reside en que permite a los matemáticos tratar el concepto de la suma en sí mismo y no sólo con sumas particulares; por ejemplo, formular resultados generales sobre sumas que son independientes de la serie particular que se está sumando.

De la misma manera, como diseñadores de programas, nos gustaría que nuestro lenguaje fuera lo suficientemente potente para que podamos escribir un procedimiento que exprese el concepto de suma en sí mismo en lugar de sólo procedimientos que calculen sumas particulares. Podemos hacerlo fácilmente en nuestro lenguaje procedural tomando la plantilla común mostrado más arriba y convertir los "casilleros" en parámetros formales:

```scheme
(define (suma term a sig b)
  (if (> a b)
      0
      (+ (term a)
         (suma term (sig a) sig b))))
```

Nótese que `suma` toma como argumentos los límites inferior y superior `a` y `b` junto con los procedimientos `term` (término) y `sig` (siguiente). Podemos usar la suma como lo haríamos con cualquier procedimiento. Por ejemplo, podemos usarlo (junto con un procedimiento `inc` que incrementa su argumento en 1) para definir `suma-cubos`:

```scheme
(define (inc n) (+ n 1))

(define (suma-cubos a b)
  (suma al-cubo a inc b))
```

Usando esto, podemos calcular la suma de los cubos de los enteros de 1 a 10:

```scheme
(suma-cubos 1 10)
3025
```

Con la ayuda de un procedimiento `identidad` para calcular el término, podemos definir `suma-enteros` en términos de `suma`:

```scheme
(define (identidad x) x)

(define (suma-enteros a b)
  (suma identidad a inc b))
```

Entonces podemos sumar los números enteros de 1 a 10:

```scheme
(suma-enteros 1 10)
55
```

También podemos definir `pi-suma` de la misma manera: [^50]

```scheme
(define (pi-suma a b)
  (define (pi-term x)
    (/ 1.0 (* x (+ x 2))))

  (define (pi-sig x)
    (+ x 4))

  (suma pi-term a pi-sig b))
```

Usando estos procedimientos, podemos calcular una aproximación a `π`:

```scheme
(* 8 (pi-suma 1 1000))
3.139592655589783
```

Una vez que tenemos `suma`, podemos usarla como un bloque de construcción en la formulación de otros conceptos. Por ejemplo, la integral definida de una función `f` entre los límites `a` y `b` puede ser aproximada numéricamente usando la fórmula

```
ₐ  
∫ f = [ f(a + dx/2) + f(a + dx + dx/2) + f(a + 2dx + dx/2) + ... ] dx
ᵇ
```

para valores pequeños de `dx`. Podemos expresarlo directamente como un procedimiento:

```scheme
(define (integral f a b dx)
  (define (agregar-dx x) (+ x dx))

  (* (suma f (+ a (/ dx 2.0)) agregar-dx b)
     dx))

(integral al-cubo 0 1 0.01)
.24998750000000042

(integral al-cubo 0 1 0.001)
.249999875000001
```

(el valor exacto de la integral de `al-cubo` entre 0 y 1 es `1/4`).

**Ejercicio 1.29.** La Regla de Simpson es un método más preciso de integración numérica que el método ilustrado anteriormente. Usando la Regla de Simpson, la integral de una función `f` entre `a` y `b` es aproximada como

```
h/3 = [y₀ + 4y₁ + 2y₂ + 4y₃ + 2y₄ + ... + 2yₙ₋₂ + 4yₙ₋₁ + yₙ ]
```

donde `h = (b - a)/n`, para algunos incluso enteros `n`, y `yₖ = f(a + kh)` (aumentando `n` aumenta la precisión de la aproximación). Defina un procedimiento que tome como argumentos `f`, `a`, `b`, y `n`, y devuelva el valor de la integral, calculado mediante la Regla de Simpson. Use su procedimiento para integrar el cubo entre 0 y 1 (con n = 100 y n = 1000), y compare los resultados con los del procedimiento `integral` mostrado arriba.

**Ejercicio 1.30.** El procedimiento `suma` anterior genera una recursión lineal. El procedimiento puede ser reescrito para que la suma se realice de forma iterativa. Muestre cómo hacerlo rellenando las expresiones que faltan en la siguiente definición:

```scheme
(define (suma term a sig b)
  (define (iter a result)
    (if <??>
        <??>
        (iter <??> <??>)))
  (iter <??> <??>))
```

**Ejercicio 1.31.**
a.  El procedimiento `suma` es sólo el más simple de un vasto número de abstracciones similares que pueden ser tomadas como procedimientos de orden superior. [^51] Escriba un procedimiento análogo llamado `producto` que devuelva el producto de los valores de una función en puntos sobre un rango dado. Mostrar cómo definir `factorial` en términos de `producto`. También use `producto` para calcular aproximaciones al uso de la fórmula [^52].

```
π   2 . 4 . 4 . 6 . 6 . 8 ...
― = ―――――――――――――――――――――――――
4   3 . 3 . 5 . 5 . 7 . 7 ...
```

b.  Si su procedimiento `producto` genera un proceso recursivo, escriba uno que genere un proceso iterativo. Si genera un proceso iterativo, escriba uno que genere un proceso recursivo.

**Ejercicio 1.32.** a. Mostrar que `suma` y `producto` (ejercicio 1.31) son ambos ejemplos especiales de una noción aún más general llamada `acumular` que combina una colección de términos, usando una determinada función de acumulación general:

```scheme
(acumular combinador valor-nulo term a sig b)
```

`acumular` toma como argumentos las mismas especificaciones de términos y rangos que `suma` y `producto` junto con un procedimiento de `combinador` (de dos argumentos) que especifica cómo debe combinarse el término actual con la acumulación de los términos precedentes, así como un `valor nulo` que especifica qué valor de base se debe usar cuando se acaban los términos. Escriba `acumular` y muestre cómo la `suma` y el `producto` pueden definirse como simples llamadas a `acumular`.

b. Si su procedimiento `acumular` genera un proceso recursivo, escriba uno que genere un proceso iterativo. Si genera un proceso iterativo, escriba uno que genere un proceso recursivo.

**Ejercicio 1.33.** Puede obtener una versión aún más general de "acumular" (ejercicio 1.32) introduciendo la noción de *filtro* en los términos a combinar. Es decir, combinar sólo aquellos términos derivados de valores en el rango que cumplan una condición especificada. La abstracción resultante `filtrado-acumulador` toma los mismos argumentos que el acumulado, junto con un predicado adicional de un argumento que especifica el filtro. Escribir `filtrado-acumulador` como procedimiento. Muestre cómo expresar lo siguiente usando `filtrado-acumulador`:

a. la suma de los cuadrados de los números primos en el intervalo `a` a `b` (asumiendo que se tiene un predicado `primo?` ya escrito).

b. El producto de todos los enteros positivos menores que `n` que son relativamente primos a `n` (es decir, todos los enteros positivos `i < n` tales que `GCD(i,n) = 1`).


### 1.3.2 Construcción de procedimientos mediante `lambda`.

Al usar la suma como en la [sección 1.3.1](./12-capitulo-1-seccion-1-3.md#131-Procedimientos-como-Argumentos), parece terriblemente incómodo tener que definir procedimientos triviales como `pi-term` y `pi-sig` sólo para poder usarlos como argumentos para nuestro procedimiento de orden superior. En lugar de definir `pi-sig` y `pi-term`, sería más conveniente tener una forma de especificar directamente "el procedimiento que devuelve su entrada incrementado en 4" y "el procedimiento que devuelve el recíproco de su entrada multiplicado por su entrada más 2". Podemos hacer esto introduciendo la forma especial `lambda`, que genera procedimientos. Usando `lambda` podemos describir lo que queremos como

```scheme
(lambda (x) (+ x 4))
```

y

```scheme
(lambda (x) (/ 1.0 (* x (+ x 2))))
```

Entonces nuestro procedimiento `pi-suma` puede ser expresado sin definir ningún procedimiento auxiliar como

```scheme
(define (pi-suma a b)
  (suma (lambda (x) (/ 1.0 (* x (+ x 2))))
       a
       (lambda (x) (+ x 4))
       b))
```

De nuevo, usando `lambda`, podemos escribir el procedimiento `integral` sin tener que definir el procedimiento auxiliar `agregar-dx`:

```scheme
(define (integral f a b dx)
  (* (suma f
          (+ a (/ dx 2.0))
          (lambda (x) (+ x dx))
          b)
     dx))
```

En general, `lambda` es usado para crear procedimientos de la misma manera que `define`, con la diferencia de que no se especifica ningún nombre para el procedimiento:

```scheme
(lambda (<parametros-formales>) <cuerpo>)
```

El procedimiento resultante es exactamente igual a un procedimiento creado usando `define`. La única diferencia es que no se ha asociado a ningún nombre en el entorno. De hecho,

```scheme
(define (sumar-4 x) (+ x 4))
```

es equivalente a

```scheme
(define sumar-4 (lambda (x) (+ x 4)))
```

Podemos leer una expresión `lambda` como sigue:

```
(lambda            (x)                 (+          x     4))
 ↑                  ↑                   ↑          ↑     ↑
 El procedimiento   de un argumento x   que suma   x  y  4
```

Como cualquier expresión que tenga un procedimiento como su valor, una expresión `lambda` puede ser usada como operador en una combinación tal como

```scheme
((lambda (x y z) (+ x y (al-cuadrado z))) 1 2 3)
12
```

o, más generalmente, en cualquier contexto en el que normalmente utilizaríamos un nombre de procedimiento. [^53]


#### Usando `let` para crear variables locales

Otro uso del `lambda` es en la creación de variables locales. A menudo necesitamos variables locales en nuestros procedimientos que no sean las que han sido vinculadas como parámetros formales. Por ejemplo, supongamos que deseamos calcular la función

```
f(x,y) = x(1 + xy)² + y(1 - y) + (1 + xy) (1 - y)
```

que también podríamos expresar como 

```
     a = 1 + xy
     b = 1 - y
f(x,y) = xa² + yb + ab
```

Al escribir un procedimiento para calcular `f`, nos gustaría incluir como variables locales no sólo `x` y `y` sino también los nombres de las cantidades intermedias como `a` y `b`. Una manera de lograr esto es usando un procedimiento auxiliar para enlazar las variables locales:

```scheme
(define (f x y)
  (define (f-auxiliar a b)
    (+ (* x (al-cuadrado a))
       (* y b)
       (* a b)))
  (f-auxiliar (+ 1 (* x y)) 
            (- 1 y)))
```

Por supuesto, podríamos usar una expresión `lambda` para especificar un procedimiento anónimo para vincular nuestras variables locales. El cuerpo de `f` se convierte entonces en una sola llamada a ese procedimiento:

```scheme
(define (f x y)
  ((lambda (a b)
     (+ (* x (al-cuadrado a))
        (* y b)
        (* a b)))
   (+ 1 (* x y))
   (- 1 y)))
```

Esta construcción es tan útil que hay una forma especial llamada `let` para hacer su uso más conveniente. Usando `let`, el procedimiento `f` podría escribirse como

```scheme
(define (f x y)
  (let ((a (+ 1 (* x y)))
        (b (- 1 y)))
    (+ (* x (al-cuadrado a))
       (* y b)
       (* a b))))
```

La forma general de una expresión `let` es

```scheme
(let ((<var₁> <exp₁>)
      (<var₂> <exp₂>)
      ⋮
      (<varₙ> <expₙ>))
   <body>)
```

que se puede pensar como si dijera

```
let <var₁> tiene el valor de <exp₁> y
    <var₂> tiene el valor de <exp₂> y
    ⋮
    <varₙ> tiene el valor de <expₙ>
en  <body> 
```

La primera parte de la expresión `let` es una lista de pares de nombre-expresión. Cuando el `let` es evaluado, cada nombre se asocia con el valor de la expresión correspondiente. El cuerpo del `let` se evalúa con estos nombres vinculados como variables locales. Ocurre así porque la expresión `let` se interpreta como una sintaxis alternativa para

```scheme
((lambda (<var₁> ...<varₙ>)
    <cuerpo>)
 <exp₁>
 
 <expₙ>)
```

No se requiere ningún mecanismo nuevo en el intérprete para proporcionar variables locales. Una expresión `let` es simplemente un azúcar sintáctico para la aplicación subyacente de `lambda`.

Podemos deducir de esta equivalencia que el alcance de una variable especificada por una expresión `let` es el cuerpo del `let`. Esto implica que:

* `let` permite vincular variables tan localmente como sea posible en el lugar donde se van a utilizar. Por ejemplo, si el valor de `x` es 5, el valor de la expresión

  ```scheme
  (+ (let ((x 3))
       (+ x (* x 10)))
     x)
  ```

  es 38. Aquí, la "x" en el cuerpo del `let` es 3, así que el valor de la expresión `let` es 33. Por otro lado, el `x`, que es el segundo argumento para el `+` más externo, sigue siendo 5.

* Los valores de la variable se calculan fuera del `let`. Esto importa cuando las expresiones que proporcionan los valores para las variables locales dependen de que las variables tengan los mismos nombres que las variables locales mismas. Por ejemplo, si el valor de `x` es 2, la expresión

  ```scheme
  (let ((x 3)
        (y (+ x 2)))
    (* x y))
  ```

  tendrá el valor 12 porque, dentro del cuerpo del `let`, `x` será 3 e `y` será 4 (que es el `x` exterior más 2).

A veces podemos usar definiciones internas para obtener el mismo efecto que con `let`. Por ejemplo, podríamos haber definido el procedimiento `f` arriba como

```scheme
(define (f x y)
  (define a (+ 1 (* x y)))
  (define b (- 1 y))
  (+ (* x (al.cuadrado a))
     (* y b)
     (* a b)))
```

Sin embargo, preferimos usar `let` en situaciones como ésta y usar `define` internos sólo para procedimientos internos. [^54]


**Ejercicio 1.34.** Supongamos que definimos el procedimiento

```scheme
(define (f g)
  (g 2))
```

Entonces tenemos

```scheme
(f al-cuadrado)
4

(f (lambda (z) (* z (+ z 1))))
6
```

¿Qué sucede si le pedimos (perversamente) al intérprete que evalúe la combinación `(f f)`? Explicar.


### 1.3.3 Procedimientos como Métodos Generales

Introducimos procedimientos compuestos en la [sección 1.1.4](./10-capitulo-1-seccion-1-1.md#114-Procedimientos-Compuestos) como mecanismo para abstraer patrones de operaciones numéricas con el fin de hacerlas independientes de los números particulares involucrados. Con los procedimientos de orden superior, como el procedimiento `integral` de la [sección 1.3.1](./12-capitulo-1-seccion-1-3.md#131-Procedimientos-como-Argumentos), comenzamos a ver un tipo de abstracción más potente: los procedimientos utilizados para expresar métodos generales de cálculo, independientemente de las funciones particulares involucradas. En esta sección discutimos dos ejemplos más elaborados -los métodos generales para encontrar ceros y puntos fijos de funciones- y mostramos cómo estos métodos pueden expresarse directamente como procedimientos.


#### Encontrar las raíces de las ecuaciones por el método de intervalo medio

El método *intervalo medio* es una técnica simple pero poderosa para encontrar las raíces de una ecuación `f(x) = 0`, donde `f` es una función continua. La idea es que, si se nos dan puntos `a` y `b` de tal manera que `f(a) < 0 < f(b)`, entonces `f` debe tener al menos un cero entre `a` y `b`. Para localizar un cero, permitamos que `x` sea el promedio de `a` y `b` y computemos `f(x)`. Si `f(x) > 0`, entonces `f` debe tener un cero entre `a` y `x`. Si `f(x) < 0`, entonces `f` debe tener un cero entre `x` y `b`. Continuando de esta manera, podemos identificar intervalos cada vez más pequeños en los que `f` debe tener un cero. Cuando llegamos al punto donde el intervalo es lo suficientemente pequeño, el proceso se detiene. Dado que el intervalo de incertidumbre se reduce a la mitad en cada paso del proceso, el número de pasos requeridos crece como `Θ(log( L/T))`, donde `L` es la longitud del intervalo original y `T` es la tolerancia de error (es decir, el tamaño del intervalo que consideraremos lo "suficientemente pequeño"). A continuación se presenta un procedimiento que implementa esta estrategia:


```scheme
(define (buscar f punto-neg punto-pos)
  (let ((mitad (promedio punto-neg punto-pos)))
    (if (suficientemente-bueno? punto-neg punto-pos)
        mitad
        (let ((test-valor (f mitad)))
          (cond ((positivo? test-valor)
                 (buscar f punto-neg mitad))
                ((negativo? test-valor)
                 (buscar f mitad punto-pos))
                (else mitad))))))
```

Asumimos que inicialmente se nos da la función `f` junto con puntos en los que sus valores son negativos y positivos. Primero calculamos el punto medio de los dos puntos dados. A continuación comprobamos si el intervalo dado es lo suficientemente pequeño, y si es así, simplemente devolvemos el punto medio como respuesta. De lo contrario, calculamos como valor de prueba el valor de `f` en el punto medio. Si el valor de la prueba es positivo, entonces continuamos el proceso con un nuevo intervalo que va desde el punto negativo original hasta el punto medio. Si el valor de la prueba es negativo, continuamos con el intervalo desde el punto medio hasta el punto positivo. Finalmente, existe la posibilidad de que el valor de la prueba sea 0, en cuyo caso el punto medio es en sí mismo la raíz que estamos buscando.

```scheme
(define (suficientemente-bueno? x y)
  (< (abs (- x y)) 0.001))
```

`buscar` es difícil de usar directamente, porque podemos accidentalmente darles puntos en los que los valores de `f` no tienen el signo requerido, en cuyo caso obtendríamos una respuesta errónea. En su lugar usaremos `buscar` a través del siguiente procedimiento, que comprueba cuál de los puntos finales tiene un valor de función negativo y cuál tiene un valor positivo, y llamará al procedimiento `buscar` de forma acorde. Si la función tiene el mismo signo en los dos puntos dados, el método de intervalo medio no se puede utilizar, en cuyo caso el procedimiento señala un error. [^56]

```scheme
(define (metodo-intervalo-medio f a b)
  (let ((valor-a (f a))
        (valor-b (f b)))
    (cond ((and (negativo? valor-a) (positivo? valor-b))
           (buscar f a b))
          ((and (negativo? valor-b) (positivo? valor-a))
           (buscar f b a))
          (else
           (error "Los valores no son de signo opuesto" a b)))))
```

El siguiente ejemplo utiliza el método de intervalo medio para aproximarse como la raíz entre 2 y 4 de `sin x = 0`:

```scheme
(metodo-intervalo-medio sin 2.0 4.0)
3.14111328125
```

Aquí hay otro ejemplo, usando el método de intervalo medio para buscar una raíz de la ecuación `x³ - 2x - 3 = 0` entre 1 y 2:

```scheme
(metodo-intervalo-medio (lambda (x) (- (* x x x) (* 2 x) 3))
                      1.0
                      2.0)
1.89306640625
```

#### Encontrar puntos fijos de funciones

Un número `x` se denomina punto fijo de una función `f` si `x` satisface la ecuación `f(x) = x`. Para algunas funciones `f` podemos localizar un punto fijo comenzando con una suposición inicial y aplicando `f` repetidamente,

```
f(x), f(f(x)), f(f(f(x))), ...
```

hasta que el valor no cambie demasiado. Usando esta idea, podemos idear un procedimiento `punto-fijo` que tome como entradas una función y una estimación inicial y produzca una aproximación a un punto fijo de la función. Aplicaremos la función repetidamente hasta que encontremos dos valores sucesivos cuya diferencia sea inferior a alguna tolerancia preestablecida:

```scheme
(define tolerancia 0.00001)

(define (punto-fijo f primera-estimacion)
  (define (close-enough? v1 v2)
    (< (abs (- v1 v2)) tolerancia))

  (define (probar estimacion)
    (let ((siguiente (f estimacion)))
      (if (suficientemente-bueno? estimacion siguiente)
          siguiente
          (probar siguiente))))

  (probar primera-estimacion))
```

Por ejemplo, podemos usar este método para aproximar el punto fijo de la función coseno, comenzando con 1 como una aproximación inicial: [^57]

```scheme
(punto-fijo cos 1.0)
.7390822985224023
```

Similarmente, podemos encontrar una solución a la ecuación `y = sin y + cos y`:

```scheme
(punto-fijo (lambda (y) (+ (sin y) (cos y)))
             1.0)
1.2587315962971173
```

El proceso de punto fijo nos recuerda al proceso que usamos para encontrar raíces cuadradas en la [sección 1.1.7](./10-capitulo-1-seccion-1-1.md#117-Ejemplo-Raíces-Cuadradas-por-el-Método-de-Newton)). Ambos se basan en la idea de mejorar repetidamente una estimación hasta que el resultado satisfaga algún criterio. De hecho, podemos formular fácilmente el cálculo de `raíz-cuadrada` como una búsqueda de punto fijo. Calcular la raíz cuadrada de un número `x` requiere encontrar un `y` tal que `y² = x`. Poniendo esta ecuación en la forma equivalente `y = x/y`, reconocemos que estamos buscando un punto fijo de la función [^58] `y → x/y`, y por lo tanto podemos intentar calcular raíces cuadradas como

```scheme
(define (raiz-cuadrada x)
  (punto-fijo (lambda (y) (/ x y))
               1.0))
```

Desafortunadamente, esta búsqueda de punto fijo no converge. Considere una estimación inicial `y₁`. La siguiente estimación es `y₂ = x/y₁` y la siguiente es `y₃ = x/y₂ = x/(x/y₁) = y₁`. Esto resulta en un bucle infinito en el que las dos conjeturas `y₁` y `y₂` se repiten una y otra vez, oscilando sobre la respuesta.

Una manera de controlar tales oscilaciones es evitar que las estimaciones cambien tanto. Puesto que la respuesta siempre estará entre `y` y `x/y`, podemos hacer una nueva estimación que no esté tan lejos de `y` como `x/y` promediando `y` con `x/y`, de modo que la siguiente estimación después de `y` sea `(1/2)(y + x/y)` en lugar de `x/y`. El proceso de hacer tal secuencia de estimaciones es simplemente el proceso de buscar un punto fijo de `y → (1/2)(y + x/y)`:

```scheme
(define (raiz-cuadrada x)
  (punto-fijo (lambda (y) (promedio y (/ x y)))
               1.0))
```

(note que `y = (1/2)(y + x/y)` es una simple transformación de la ecuación `y = x/y`; para derivarla, sume `y` a ambos lados de la ecuación y divida por 2).

Con esta modificación, el procedimiento de `raiz-cuadrada` funciona. De hecho, si desentrañamos las definiciones, podemos ver que la secuencia de aproximaciones a la raíz cuadrada generada en este caso es precisamente la misma que la generada por nuestro procedimiento original de raíz cuadrada de la [sección 1.1.7](./10-capitulo-1-seccion-1-1.md#117-Ejemplo-Raíces-Cuadradas-por-el-Método-de-Newton)). Este enfoque de promediar aproximaciones sucesivas a una solución, una técnica que llamamos *amortiguación promedio* (NdT: en inglés *average damping*), a menudo ayuda a la convergencia de las búsquedas de punto fijo.

**Ejercicio 1.35.** Mostrar que la proporción áurea `Φ` ([sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Árbol-de-Recursión)) es un punto fijo de la transformación `x → 1 + 1/x`, y usar este hecho para calcular `Φ` mediante el procedimiento de `punto-fijo`.

**Ejercicio 1.36.** Modificar `punto-fijo` para que imprima la secuencia de aproximaciones que genera, usando la nueva línea y mostrar las primitivas mostradas en el ejercicio 1.22. A continuación, encuentre una solución para `xˣ = 1000` buscando un punto fijo de `x → log(1000)/log(x)` (utilice el procedimiento de logaritmo primitivo de Scheme, que calcula los logaritmos naturales). Compare el número de pasos que se dan con y sin amortiguación promedio (tenga en cuenta que no puede empezar `punto fijo` con una estimación de 1, ya que esto causaría división por `log(1) = 0`).

**Ejercicio 1.37.** a. Una *fracción continuada* (NdT: en inglés *continued fraction*) infinita es una expresión de la forma

```
            N₁
f = ―――――――――――――――――――
     D₁ +      N₂
          ―――――――――――――
          D₂ +    N₃
               ――――――――
               D₃ + ...
```

Como ejemplo, se puede mostrar que la expansión de la fracción continuada infinita con la `Nᵢ` y la `Dᵢ` todas iguales a 1 produce `1/Φ`, donde `Φ` es la razón de oro (descrita en la [sección 1.2.2](./11-capitulo-1-seccion-1-2.md#122-Árbol-de-Recursión)). Una forma de aproximarse a una fracción continua infinita es truncar la expansión después de un número dado de términos. Tal truncamiento -el llamado *k-término de fracción continua finita*- tiene la forma

```
     N₁
―――――――――――――
  D₁ +   N₂
       ――――――
           Nₖ
     ... + ――
           Dₖ
```

Supongamos que `n` y `d` son procedimientos de un argumento (el índice del término `i`) que devuelven los términos de la fracción continua de `Nᵢ` y `Dᵢ`. Defina un procedimiento `frac-cont` de tal manera que al evaluar `(frac-cont n d k)` se calcule el valor de la fracción continua finita del k-término. Compruebe su procedimiento aproximándose a `1/Φ` utilizando

```scheme
(frac-cont (lambda (i) 1.0)
           (lambda (i) 1.0)
           k)
```

para valores sucesivos de `k`. ¿Qué tan grande debe hacer `k` para obtener una aproximación que sea exacta hasta 4 decimales?

b. Si su procedimiento `frac-cont` genera un proceso recursivo, escriba uno que genere un proceso iterativo. Si genera un proceso iterativo, escriba uno que genere un proceso recursivo.

**Ejercicio 1.38.** En 1737, el matemático suizo Leonhard Euler publicó una autobiografía *De Fractionibus Continuis*, que incluía una expansión continua de la fracción para `e - 2`, donde `e` es la base de los logaritmos naturales. En esta fracción, los `Nᵢ` son todos 1, y los `Dᵢ` son sucesivamente `1, 2, 1, 1, 1, 4, 1, 1, 1, 6, 1, 1, 8, ...`. Escriba un programa que utilice su procedimiento de `frac-cont` desde el ejercicio 1.37 hasta aproximarse a `e`, basado en la expansión de Euler.

**Ejercicio 1.39.** El matemático alemán J.H. Lambert publicó una representación continua de la función tangente en 1770: 

```
              x
tan x = ―――――――――――――――
        1 -     x²
            ―――――――――――
            3 -    x²
                ―――――――
                5 - ...
```

donde `x` está en radianes. Defina un procedimiento `(tan-fc x k)` que calcule una aproximación a la función tangente basada en la fórmula de Lambert. `k` especifica el número de términos a calcular, como en el ejercicio 1.37.


### 1.3.4 Procedimientos como Valores Retornados

Los ejemplos anteriores demuestran cómo la habilidad de pasar procedimientos como argumentos mejora significativamente el poder expresivo de nuestro lenguaje de programación. Podemos lograr un poder aún más expresivo creando procedimientos cuyos valores devueltos son en sí mismos procedimientos.

Podemos ilustrar esta idea mirando de nuevo el ejemplo de punto fijo descrito al final de la [sección 1.3.3](). Formulamos una nueva versión del procedimiento de raíz cuadrada como una búsqueda de punto fijo, comenzando con la observación de que `√x` es un punto fijo de la función `y → x/y`. Luego usamos amortiguación promedio (NdT: *average damping* en inglés) para hacer converger las aproximaciones. La amortiguación promedio es una técnica general muy útil en sí misma. Es decir, dada una función `f`, consideramos la función cuyo valor en `x` es igual al promedio de `x` y `f(x)`.

Podemos expresar la idea de amortiguación media mediante el siguiente procedimiento:

```scheme
(define (amort-prom f)
  (lambda (x) (promedio x (f x))))
```

La `amort-prom` es un procedimiento que toma como argumento un procedimiento `f` y devuelve como su valor un procedimiento (producido por el lambda) que, cuando se aplica a un número `x`, produce el promedio de `x` y `(f x)`. Por ejemplo, la aplicación de `amort-prom` al procedimiento `al-cuadrado` produce un procedimiento cuyo valor con un número `x` es el promedio de `x` y `x²`. Aplicando este procedimiento a 10 devuelve el promedio de 10 y 100, o 55: [^59]

```scheme
((amort-prom al-cuadrado) 10)
55
```

Usando `amort-prom`, podemos reformular el procedimiento de raíz cuadrada de la siguiente manera:

```scheme
(define (raiz-cuadrada x)
  (punto-fijo (amort-prom (lambda (y) (/ x y)))
               1.0))
```

Observe cómo esta formulación hace explícitas las tres ideas en el método: la búsqueda de punto fijo, la amortiguación promedio y la función `y → x/y`. Es instructivo comparar esta formulación del método de raíz cuadrada con la versión original dada en [sección 1.1.7](./10-capitulo-1-seccion-1-1.md#117-Ejemplo-Raíces-Cuadradas-por-el-Método-de-Newton). Tenga en cuenta que estos procedimientos expresan el mismo proceso, y observe cuán clara se vuelve la idea cuando expresamos el proceso en términos de estas abstracciones.  En general, hay muchas maneras de formular un proceso como un procedimiento. Los programadores experimentados saben cómo elegir formulaciones de procedimiento que son particularmente perspicaces, y en las que los elementos útiles del proceso se exponen como entidades separadas que pueden ser reutilizadas en otras aplicaciones. Como ejemplo simple de reutilización, note que la raíz cúbica de `x` es un punto fijo de la función `y → x/y²`, así que podemos generalizar inmediatamente nuestro procedimiento de raíz cuadrada a uno que extrae raíces cúbicas: [^60]

```scheme
(define (raiz-cubica x)
  (punto-fijo (amort-prom (lambda (y) (/ x (al-cuadrado y))))
               1.0))
```

#### El método de Newton

Cuando se introdujo por primera vez el procedimiento de raíz cuadrada, en la [sección 1.1.7](), mencionamos que se trataba de un caso especial del *método de Newton*. Si `x → g(x)` es una función diferenciable, entonces una solución de la ecuación `g(x) = 0` es un punto fijo de la función `x → f(x)` donde

```
            g(x)
f(x) = x - ―――――――
            Dg(x)    
```

y `Dg(x)` es la derivada de `g` evaluada en `x`. El método de Newton es el uso del método de punto fijo que vimos arriba para aproximar una solución de la ecuación encontrando un punto fijo de la función `f`. [^61] Para muchas funciones `g` y para suposiciones iniciales lo suficientemente buenas para `x`, el método de Newton converge muy rápidamente a una solución de `g(x) = 0`. [^62] 

Para implementar el método de Newton como un procedimiento, primero debemos expresar la idea de la derivada. Nótese que la "derivada", al igual que la amortiguación promedio, es algo que transforma una función en otra función. Por ejemplo, el derivado de la función `x → x³` es la función `x → 3x²`. En general, si `g` es una función y `dx` es un número pequeño, entonces la derivada `Dg` de `g` es la función cuyo valor en cualquier número `x` es dado (en el límite de `dx`) por 

```
        g(x + dx) - g(x)
Dg(x) = ――――――――――――――――
              d(x)    
```

De este modo, podemos expresar la idea de la derivada (tomando `dx` para que sea, digamos, 0.00001) como el procedimiento

```scheme
(define (derivada g)
  (lambda (x)
    (/ (- (g (+ x dx)) (g x))
       dx))
```

junto con la definición

```scheme
(define dx 0.00001)
```

Al igual que `amort-prom`, `derivada` es un procedimiento que toma un procedimiento como argumento y devuelve otro procedimiento como valor. Por ejemplo, para aproximar la derivada de `x → x³` a 5 (cuyo valor exacto es 75) podemos evaluar

```scheme
(define (al-cubo x) (* x x x))
((derivada al-cubo) 5)
75.00014999664018
```

Con la ayuda de `derivada`, podemos expresar el método de Newton como un proceso de punto fijo:

```scheme
(define (transf-newton g)
  (lambda (x)
    (- x (/ (g x) ((derivada g) x)))))
(define (metodo-newton g estimacion)
  (punto-fijo (transf-newton  g) estimacion))
```

El procedimiento `newton-transform` expresa la fórmula al principio de esta sección, y `newtons-método` es rápidamente definido en términos de esto. Toma como argumento un procedimiento que calcula la función para la que queremos encontrar un cero, junto con una conjetura inicial. Por ejemplo, para encontrar la raíz cuadrada de `x`, podemos usar el método de Newton para encontrar un cero de la función `y → y² - x` comenzando con una suposición inicial de 1.[^63] Esto proporciona otra forma del procedimiento de raíz cuadrada:

```scheme
(define (raiz-cuadrada x)
  (newtons-method (lambda (y) (- (square y) x))
                  1.0))
```




# ---Traducción pendiente---

___

[^49]: Esta serie, usualmente escrita en la forma equivalente `(π/4) = 1 - (1/3) + (1/5) - (1/7) + ...`, se debe a Leibniz. Veremos cómo usar esto como base para algunos trucos numéricos en la [sección 3.5.3](./24-capitulo-3-seccion-3-5.md#353-).

[^50]: Note que hemos usado la estructura de bloques ([sección 1.1.8](./10-capitulo-1-seccion-1-1.md#118-Procedimientos-como-Abstracciones-de-Caja-Negra)) para incrustar las definiciones de `pi-sig` y `pi-term` dentro de `pi-suma`, ya que es poco probable que estos procedimientos sean útiles para cualquier otro propósito. Veremos cómo deshacernos de ellos en la [sección 1.3.2](./12-capitulo-1-seccion-1-3.md#132-).

[^51]: La intención de los ejercicios 1.31 - 1.33 es demostrar el poder expresivo que se logra usando una abstracción apropiada para consolidar muchas operaciones aparentemente dispares. Sin embargo, aunque la acumulación y el filtrado son ideas elegantes, nuestras manos están un poco atadas en su uso en este momento, ya que aún no disponemos de estructuras de datos para proporcionar los medios adecuados de combinación para estas abstracciones. Volveremos a estas ideas en la [sección 2.2.3](./15-capitulo-2-seccion-2-2.md#223-) cuando mostremos cómo usar secuencias como interfaces para combinar filtros y acumuladores para construir abstracciones aún más poderosas. Veremos allí cómo estos métodos realmente se imponen como un enfoque poderoso y elegante para el diseño de programas.

[^52]: Esta fórmula fue descubierta por el matemático inglés del siglo XVII John Wallis.

[^53]: Sería más claro y menos intimidante para la gente que esta aprendiendo Lisp si se usara un nombre más obvio que `lambda`, como `hacer-procedimiento`. Pero la convención está firmemente arraigada. La notación se adopta del cálculo λ, un formalismo matemático introducido por el lógico matemático Alonzo Church (1941). Church desarrolló el cálculo λ para proporcionar una base rigurosa para el estudio de las nociones de función y aplicación de la función. El cálculo λ se ha convertido en una herramienta básica para la investigación matemática de la semántica de los lenguajes de programación.

[^54]: Entender las definiciones internas lo suficientemente bien como para asegurarnos de que un programa significa lo que pretendemos que signifique requiere un modelo más elaborado del proceso de evaluación que el que hemos presentado en este capítulo. Sin embargo, las sutilezas no surgen con las definiciones internas de los procedimientos. Volveremos sobre este tema en la [sección 4.1.6](./26-capitulo-4-seccion-4.1.md#416-), después de aprender más sobre la evaluación.

[^55]: Hemos utilizado 0,001 como un número "pequeño" representativo para indicar una tolerancia de error aceptable en un cálculo. La tolerancia adecuada para un cálculo real depende del problema a resolver y de las limitaciones de la computadora y del algoritmo. Esta es a menudo una consideración muy sutil, que requiere de la ayuda de un analista numérico o de algún otro tipo de mago.

[^56]: Esto se puede lograr usando `error,` que toma como argumentos un número de ítems que se imprimen como mensajes de error.

[^57]: Pruebe esto durante una clase aburrida: Ponga su calculadora en modo radianes y luego presione repetidamente el botón `cos` hasta que obtenga el punto fijo.

[^58]: `→` (se pronuncia "mapear a") es la forma en que los matemáticos escriben `lambda`. `y → x/y` significa `(lambda(y) (/ x y))`, es decir, la función cuyo valor en `y` es `x/y`.

[^59]: Observe que se trata de una combinación cuyo operador es a su vez una combinación. En el Ejercicio 1.4 ya se demostró la capacidad de formar tales combinaciones, pero eso sólo fue un ejemplo de juguete. Aquí empezamos a ver la necesidad real de tales combinaciones: al aplicar un procedimiento que se obtiene como el valor devuelto por un procedimiento de orden superior.

[^60]: Véase el ejercicio 1.45 para una mayor generalización.

[^61]: Los libros de cálculo elemental generalmente describen el método de Newton en términos de una secuencia de aproximaciones `xₙ₊₁ = xₙ - g(xₙ)/Dg(xₙ)`. Disponer de un lenguaje para hablar de procesos y utilizar la idea de puntos fijos simplifica la descripción del método.

[^62]: El método de Newton no siempre converge en una respuesta, pero se puede demostrar que, en los casos favorables, cada iteración duplica la precisión del número de dígitos de la aproximación a la solución. En tales casos, el método de Newton convergerá mucho más rápidamente que el método de intervalo medio.

[^63]: Para encontrar raíces cuadradas, el método de Newton converge rápidamente a la solución correcta desde cualquier punto de partida. 
