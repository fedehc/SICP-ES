### 1.3 Formulación de Abstracciones con Procedimientos de Orden Superior

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


# ---Traducción pendiente---