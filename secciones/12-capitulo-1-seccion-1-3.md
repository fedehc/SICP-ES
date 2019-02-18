## 1.3 Formulación de Abstracciones con Procedimientos de Orden Superior

Hemos visto que los procedimientos son, en efecto, abstracciones que describen operaciones compuestas sobre números, independientemente de los números particulares. Por ejemplo, cuando nosotros

```scheme
(define (al-cubo x) (* x x x))
```

no estamos hablando del cubo de un número en particular, sino de un método para obtener el cubo de cualquier número. Por supuesto que podríamos avanzar sin definir este procedimiento, escribiendo siempre las expresiones como

```scheme
(* 3 3 3)
(* x x x)
(* y y y) 
```

y nunca mencionar `al-cubo` explícitamente. Esto nos pondría en una seria desventaja, obligándonos a trabajar siempre al nivel de las operaciones particulares que resultan ser primitivas en el lenguaje (multiplicación, en este caso), más que en términos de operaciones de nivel superior. Nuestros programas podrían calcular cubos, pero nuestro lenguaje carecería de la capacidad de expresar el concepto de cubo. Una de las cosas que debemos exigir de un potente lenguaje de programación es la capacidad de construir abstracciones asignando nombres a patrones comunes y luego trabajar en términos de abstracciones directamente. Los procedimientos proporcionan esta capacidad. Por eso, todos los lenguajes de programación, excepto los más primitivos, incluyen mecanismos para definir procedimientos.

Sin embargo, incluso en el procesamiento numérico, estaremos severamente limitados en nuestra capacidad de crear abstracciones si nos limitamos a procedimientos cuyos parámetros deban ser números. Con frecuencia el mismo patrón de programación se utiliza con varios procedimientos diferentes. Para expresar patrones como conceptos, necesitaremos construir procedimientos que puedan aceptar los procedimientos como argumentos o los procedimientos de retorno como valores. Los procedimientos que manipulan procedimientos se denominan *procedimientos de orden superior*. Esta sección describe cómo los procedimientos de orden superior pueden servir como poderosos mecanismos de abstracción, aumentando enormemente el poder expresivo de nuestro lenguaje.


### 1.3.1 Procedimientos como Argumentos


Considere los siguientes tres procedimientos. El primero calcula la suma de los enteros de `a` hasta `b`:

```scheme
(define (suma-enteros a b)
  (if (> a b)
      0
      (+ a (suma-enteros (+ a 1) b))))
```

El segundo calcula la suma de los cubos de los enteros en el rango dado:

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

Nótese que `suma` toma como argumentos los límites inferior y superior `a` y `b` junto con los procedimientos `term` y `sig`. Podemos usar la suma como lo haríamos con cualquier procedimiento. Por ejemplo, podemos usarlo (junto con un procedimiento `inc` que incrementa su argumento en 1) para definir `suma-cubos`:

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

Con la ayuda de un procedimiento `identidad` para calcular el término, podemos definir `suma-enteros` en términos de suma:

```scheme
(define (identidad x) x)

(define (suma-enteros a b)
  (sum identidad a inc b))
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
ᵃ     
∫ f =  [ f(a + dx/2) + f(a + dx + dx/2) + f(a + 2dx + dx/2) + ... ] dx
ᵇ    
```

para valores pequeños de `dx`. Podemos expresarlo directamente como un procedimiento:

```scheme
(define (integral f a b dx)
  (define (agregar-dx x) (+ x dx))

  (* (sum f (+ a (/ dx 2.0)) agregar-dx b)
     dx))

(integral al-cubo 0 1 0.01)
.24998750000000042

(integral al-cubo 0 1 0.001)
.249999875000001
```

(el valor exacto de la integral de `al-cubo` entre 0 y 1 es `1/4`).


# ---Traducción pendiente---

___


[^49]:


[^50]:
