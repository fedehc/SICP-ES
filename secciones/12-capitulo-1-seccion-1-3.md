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




# ---Traducción pendiente---

___

[^49]: Esta serie, usualmente escrita en la forma equivalente `(π/4) = 1 - (1/3) + (1/5) - (1/7) + ...`, se debe a Leibniz. Veremos cómo usar esto como base para algunos trucos numéricos en la [sección 3.5.3](./24-capitulo-3-seccion-3-5.md#353-).

[^50]: Note que hemos usado la estructura de bloques ([sección 1.1.8](./10-capitulo-1-seccion-1-1.md#118-Procedimientos-como-Abstracciones-de-Caja-Negra)) para incrustar las definiciones de `pi-sig` y `pi-term` dentro de `pi-suma`, ya que es poco probable que estos procedimientos sean útiles para cualquier otro propósito. Veremos cómo deshacernos de ellos en la [sección 1.3.2](./12-capitulo-1-seccion-1-3.md#132-).

[^51]: La intención de los ejercicios 1.31-1.33 es demostrar el poder expresivo que se logra usando una abstracción apropiada para consolidar muchas operaciones aparentemente dispares. Sin embargo, aunque la acumulación y el filtrado son ideas elegantes, nuestras manos están un poco atadas en su uso en este momento, ya que aún no disponemos de estructuras de datos para proporcionar los medios adecuados de combinación para estas abstracciones. Volveremos a estas ideas en la [sección 2.2.3](./15-capitulo-2-seccion-2-2.md#223-) cuando mostremos cómo usar secuencias como interfaces para combinar filtros y acumuladores para construir abstracciones aún más poderosas. Veremos allí cómo estos métodos realmente se imponen como un enfoque poderoso y elegante para el diseño de programas.

[^52]: Esta fórmula fue descubierta por el matemático inglés del siglo XVII John Wallis.

[^53]: Sería más claro y menos intimidante para la gente que esta aprendiendo Lisp si se usara un nombre más obvio que `lambda`, como `hacer-procedimiento`. Pero la convención está firmemente arraigada. La notación se adopta del cálculo λ, un formalismo matemático introducido por el lógico matemático Alonzo Church (1941). Church desarrolló el cálculo λ para proporcionar una base rigurosa para el estudio de las nociones de función y aplicación de la función. El cálculo λ se ha convertido en una herramienta básica para la investigación matemática de la semántica de los lenguajes de programación.

[^54]: Entender las definiciones internas lo suficientemente bien como para asegurarnos de que un programa significa lo que pretendemos que signifique requiere un modelo más elaborado del proceso de evaluación que el que hemos presentado en este capítulo. Sin embargo, las sutilezas no surgen con las definiciones internas de los procedimientos. Volveremos sobre este tema en la [sección 4.1.6](./26-capitulo-4-seccion-4.1.md#416-), después de aprender más sobre la evaluación.

