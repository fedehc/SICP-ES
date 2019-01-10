# Capítulo 1

## Construyendo Abstracciones con Procedimientos

> Los actos de la mente, en los que ejerce su poder sobre ideas simples, son principalmente estos tres: 1. Combinando varias ideas simples en una compuesta, y así todas las ideas complejas son hechas. 2. La segunda es reunir dos ideas, sean simples o complejas, y ponerlas una al lado de la otra para poder verlas a la vez, sin unirlas en una sola, con lo que obtiene todas sus ideas de relaciones. 3. La tercera es separarlas de todas las demás ideas que las acompañan en su existencia real: esto se llama abstracción, y así todas sus ideas generales son hechas.
>
> **John Locke**, *Un ensayo sobre la comprensión humana* (1690)

Estamos a punto de estudiar la idea de un *proceso computacional*. Los procesos computacionales son seres abstractos que habitan en las computadoras. A medida que evolucionan, los procesos manipulan otras cosas abstractas llamadas *datos*. La evolución de un proceso está dirigida por un patrón de reglas llamado *programa*. La gente crea programas para dirigir procesos. En efecto, invocamos los espíritus de la computadora con nuestros hechizos.

Un proceso computacional es ciertamente muy parecido al concepto que los hechiceros tienen de un espíritu. No puede ser visto ni tocado. No está compuesto de materia en absoluto. Sin embargo, es muy real. Puede realizar trabajo intelectual. Puede responder a preguntas. Puede influir en el mundo desembolsando dinero en un banco o controlando el brazo de un robot en una fábrica. Los programas que usamos para conjurar procesos son como los conjuros de un hechicero. Están cuidadosamente compuestos de expresiones simbólicas en *lenguajes de programación* arcanos y esotéricos que determinan las tareas que queremos que nuestros procesos realicen.

Un proceso computacional, en una computadora que funciona correctamente, ejecuta programas con precisión y exactitud. Así, como el aprendiz del hechicero, los programadores principiantes deben aprender a entender y anticipar las consecuencias de sus hechizos. Incluso pequeños errores (usualmente llamados *bugs* o *fallos*) en los programas pueden tener consecuencias complejas e imprevistas.

Afortunadamente, aprender a programar es considerablemente menos peligroso que aprender a hacer magia, porque los espíritus con los que tratamos están convenientemente contenidos de una manera segura. La programación en el mundo real, sin embargo, requiere cuidado, experiencia y sabiduría. Un pequeño error en un programa de diseño asistido por computadora, por ejemplo, puede llevar al colapso catastrófico de un avión o una presa o a la autodestrucción de un robot industrial.

Los expertos en ingeniería de software tienen la capacidad de organizar programas de manera que modo que puedan estar razonablemente seguros de que los procesos resultantes realizarán las tareas previstas. Pueden visualizar el comportamiento de sus sistemas por adelantado. Saben cómo estructurar los programas para que los problemas imprevistos no tengan consecuencias catastróficas, y cuando surgen problemas, pueden *depurar* (NdT: *debug* en inglés) sus programas. Los sistemas computacionales bien diseñados, como los automóviles o los reactores nucleares bien diseñados, se diseñan de manera modular, de modo que las partes puedan ser construidas, reemplazadas y depuradas por separado.


### Programando en Lisp

Necesitamos un lenguaje apropiado para describir los procesos, y para ello utilizaremos el lenguaje de programación Lisp. Así como nuestros pensamientos cotidianos se expresan normalmente en nuestro lenguaje natural (como el español, el inglés, el francés o el japonés) y las descripciones de los fenómenos cuantitativos se expresan con notaciones matemáticas, nuestros pensamientos procedurales los expresaremos en Lisp. Lisp fue inventado a fines de la década de 1950 como un formalismo para razonar sobre el uso de ciertos tipos de expresiones lógicas, llamadas *ecuaciones de recursión*, como modelo para la computación. El lenguaje fue concebido por John McCarthy y se basa en su trabajo *"Recursive Functions of Symbolic Expressions and Their Computation by Machine"* (en español: *Funciones Recursivas de Expresiones Simbólicas y su Cálculo por Máquina*, McCarthy 1960).

A pesar de su concepción como un formalismo matemático, Lisp es un lenguaje de programación práctico. Un *intérprete* de Lisp es una máquina que lleva a cabo los procesos descritos en el lenguaje Lisp. El primer intérprete de Lisp fue implementado por McCarthy con la ayuda de colegas y estudiantes del Artificial Intelligence Group del Laboratorio de Investigación de Electrónica del MIT y del Centro de Cálculo del MIT.[^1] Lisp, cuyo nombre es un acrónimo de **LIS**t **P**rocessing, fue diseñado para proporcionar capacidades de manipulación simbólica para atacar problemas de programación como la diferenciación simbólica y la integración de expresiones algebraicas. Para este propósito se incluyeron nuevos objetos de datos conocidos como átomos y listas, que lo diferenciaba notablemente de todos los demás lenguajes de la época.

Lisp no fue el producto de un esfuerzo de diseño concertado. Al contrario, evolucionó de manera informal y experimental en respuesta a las necesidades de sus usuarios y por cuestiones pragmáticas de implementación. La evolución informal de Lisp ha continuado a través de los años, y la comunidad de usuarios de Lisp ha resistido tradicionalmente los intentos de promulgar cualquier definición "oficial" del lenguaje. Esta evolución, junto con la flexibilidad y elegancia de su concepción inicial, ha permitido que Lisp, que es el segundo lenguaje más antiguo en uso hoy en día (sólo Fortran es más antiguo), se adapte continuamente para abarcar las ideas más modernas sobre el diseño de programas. Por lo tanto, Lisp es ya una familia de dialectos que, aunque comparten la mayoría de las características originales, pueden diferir uno del otro de modo significativo. El dialecto de Lisp utilizado en este libro se llama Scheme.[^2]

Debido a su carácter experimental y su énfasis en manipulación simbóloca, Lisp fue al principio muy ineficiente para los cálculos numéricos, al menos en comparación con Fortran. A lo largo de los años, sin embargo, se han desarrollado compiladores Lisp que traducen programas en código máquina que pueden realizar cálculos numéricos con una eficiencia razonable. Y para aplicaciones especiales, Lisp ha sido usado con gran efectividad.[^3] Aunque Lisp aún no ha superado su antigua reputación de ser ineficiente, Lisp se usa ahora en muchas aplicaciones donde la eficiencia no es la preocupación central. Por ejemplo, Lisp se ha convertido en el lenguaje preferido para los lenguajes de shell del sistemas operativos y para los lenguajes de extensión de los editores y para los sistemas de diseño asistido por computadora.

Si Lisp no es un lenguaje convencional, ¿por qué lo usamos como marco para nuestra discusión de la *programación*? Porque el lenguaje posee características únicas que lo convierten en un excelente medio para estudiar importantes constructos de programación y estructuras de datos y para relacionarlos con las características lingüísticas que los soportan. El más significativo de estos rasgos es el hecho de que las descripciones de los procesos de Lisp, llamados *procedimientos*, pueden ser representados y manipulados como datos de Lisp. La importancia de esto es que existen poderosas técnicas de diseño de programas que se basan en la habilidad de desdibujar la distinción tradicional entre datos "pasivos" y procesos "activos". Como descubriremos, la flexibilidad de Lisp en el manejo de procedimientos como datos lo convierte en uno de los lenguajes más convenientes en existencia para explorar estas técnicas. La capacidad de representar procedimientos como datos también hace de Lisp un excelente lenguaje para escribir programas que deben manipular otros programas como datos, como los intérpretes y compiladores que soportan lenguajes de programación. Más allá de estas consideraciones, programar en Lisp es muy divertido.


### 1.1 Los Elementos de la Programación

Un lenguaje de programación potente es más que un medio para instruir a una computadora para que realice tareas. El lenguaje también sirve como marco dentro del cual organizamos nuestras ideas acerca de los procesos. Por lo tanto, cuando describimos un lenguaje, debemos prestar especial atención a los medios que el lenguaje proporciona para combinar ideas simples para formar ideas más complejas. Todo lenguaje potente tiene tres mecanismos para lograr esto:

* **expresiones primitivas**, que representan las entidades más simples que conciernen al lenguaje,

* **modos de combinación**, mediante los cuales los elementos compuestos se construyen a partir de los más sencillos, y

* **medios de abstracción**, por el cual los elementos compuestos pueden ser nombrados y manipulados como unidades.

En programación, nos ocupamos de dos tipos de elementos: procedimientos y datos (más adelante descubriremos que realmente no son tan distintos). Informalmente, los datos son "cosas" que queremos manipular, y los procedimientos son descripciones de las reglas para manipular los datos. Por lo tanto, cualquier lenguaje de programación potente debería ser capaz de describir datos y procedimientos primitivos y debería tener métodos para combinar y abstraer procedimientos y datos.

En este capítulo sólo trataremos con datos numéricos simples para que podamos concentrarnos en las reglas para la construcción de procedimientos.[^4] En capítulos posteriores veremos que estas mismas reglas nos permitirán construir procedimientos para manipular también datos compuestos.


#### 1.1.1 Expresiones

Una manera fácil de empezar a programar es examinar algunas interacciones típicas con un intérprete para el dialecto Scheme de Lisp. Imagínese que se encuentra sentado en una terminal de computadora. Uno escribe una *expresión*, y el intérprete responde mostrando el resultado de su *evaluación* de esa expresión.

Un tipo de expresión primitiva que uno podría escribir es un número (para ser más precisos, la expresión que uno escriba consiste en las cifras que representan el número en base 10). Si le presentas a Lisp un número

```scheme
486
```

el intérprete responderá imprimiendo[^5]

```scheme
486
```

Las expresiones que representan números pueden combinarse con una expresión que represente un procedimiento primitivo (como `+` o `*`) para formar una expresión compuesta que represente la aplicación del procedimiento a esos números. Por ejemplo:

```scheme
(+ 137 349)
486
(- 1000 334)
666
(* 5 99)
495
(/ 10 5)
2
(+ 2.7 10)
12.7
```

Expresiones como éstas, formadas por la delimitación de una lista de expresiones entre paréntesis con el fin de indicar la aplicación del procedimiento, son llamadas *combinaciones*. El elemento más a la izquierda de la lista se llama el *operador*, y los otros elementos se llaman *operandos*. El valor de una combinación se obtiene aplicando el procedimiento especificado por el operador a los argumentos que son los valores de los operandos.

La convención de colocar el operador a la izquierda de los operandos se conoce como *notación de prefijo* (NdT: o también conocido como *notación polaca*), y puede ser algo confuso al principio porque se aparta significativamente de la convención matemática habitual. Sin embargo, la notación de prefijo tiene varias ventajas. Una de ellas es que puede acomodar procedimientos que pueden tomar un número arbitrario de argumentos, como en los siguientes ejemplos:

```scheme
(+ 21 35 12 7)
75

(* 25 4 12)
1200
```

No puede surgir ninguna ambigüedad, ya que el operador es siempre el elemento más a la izquierda y toda la combinación está delimitada por los paréntesis.

Una segunda ventaja de la notación de prefijo es que se extiende de una manera directa para permitir que las combinaciones sean *anidadas*, es decir, que tengan combinaciones cuyos elementos son en sí mismos combinaciones:

```scheme
(+ (* 3 5) (- 10 6))
19
```

No hay límite (en principio) a la profundidad de este tipo de anidamiento y a la complejidad general de las expresiones que el intérprete de Lisp puede evaluar. Somos nosotros los humanos los que nos confundimos por expresiones relativamente simples como

```scheme
(+ (* 3 (+ (* 2 4) (+ 3 5))) (+ (- 10 7) 6))
```

que el intérprete evaluaría fácilmente como 57. Podemos ayudarnos escribiendo tal expresión en la forma

```scheme
(+ (* 3
      (+ (* 2 4)
         (+ 3 5)))
   (+ (- 10 7)
      6))
```

siguiendo una convención de formato conocida como *pretty-printing* (NdT: traducido sería *impresión-bonita*), en la que cada combinación larga se escribe de manera que los operandos estén alineados verticalmente. Las indentaciones resultantes muestran claramente la estructura de la expresión.[^6]

Incluso con expresiones complejas, el intérprete siempre opera en el mismo ciclo básico: lee una expresión de la terminal, evalúa la expresión e imprime el resultado. Este modo de funcionamiento se expresa a menudo diciendo que el intérprete funciona en un bucle de lectura-evaluación-impresión (NdT: en inglés *read-eval-print loop*, o más conocido como *REPL*). Observe en particular que no es necesario indicar explícitamente al intérprete que imprima el valor de la expresión.[^7]


#### 1.1.2 Los Nombres y el Entorno

Un aspecto crítico de un lenguaje de programación es la manera en que proporciona el uso de nombres para referirse a objetos computacionales. Decimos que el nombre identifica a una *variable* cuyo *valor* es el objeto.

En el dialecto Scheme de Lisp, nombramos las cosas con `define` (NtD: `define` está expresado en inglés, aunque en español se escriba igual). Escribiendo

```scheme
(define tamaño 2)
```

hace que el intérprete asocie el valor 2 con el nombre `tamaño`.[^8] Una vez que el nombre `tamaño` ha sido asociado con el número 2, podemos referirnos al valor 2 por nombre:

```scheme
(tamaño)
2
(* 5 tamaño)
10
```
Aquí hay más ejemplos del uso de `define`:

```scheme
(define pi 3.14159)
(define radio 10)
(* pi (* radio radio))
314.159
(define circunferencia (* 2 pi radio))
circunferencia
62.8318
```

`define` es el medio de abstracción más simple de nuestro lenguaje, ya que nos permite utilizar nombres simples para referirnos a los resultados de operaciones compuestas, como la `circunferencia` calculada anteriormente. En general, los objetos computacionales pueden tener estructuras muy complejas, y sería extremadamente incómodo tener que recordar y repetir sus detalles cada vez que queremos usarlos. En efecto, los programas complejos se elaboran construyendo, paso a paso, objetos computacionales de complejidad creciente. El intérprete hace que esta construcción paso a paso del programa sea muy conveniente porque las asociaciones nombre-objeto pueden ser creadas gradualmente en interacciones sucesivas. Esta característica fomenta el desarrollo incremental y el testeo de programas y es en gran medida responsable del hecho de que un programa Lisp normalmente se componga de un gran número de procedimientos relativamente sencillos.

Debe quedar claro que la posibilidad de asociar valores con símbolos y luego recuperarlos significa que el intérprete debe mantener algún tipo de memoria que mantenga un registro de los pares nombre-objeto. Esta memoria se llama el *entorno* (más precisamente el *entorno global*, ya que veremos más adelante que un cálculo puede implicar varios entornos diferentes).[^9]


#### 1.1.3 Evaluando Combinaciones

Uno de nuestros objetivos en este capítulo es aislar las cuestiones pensando en términos de procesos. Como caso concreto, consideremos que, al evaluar las combinaciones, el propio intérprete está siguiendo un procedimiento.

* Para evaluar una combinación, haga lo siguiente:

1) Evaluar las subexpresiones de la combinación.

2) Aplicar el procedimiento que es el valor de la subexpresión más a la izquierda (el operador) a los argumentos que son los valores de las otras subexpresiones (los operandos). 

Incluso esta simple regla ilustra algunos puntos importantes sobre los procesos en general. Primero, observe que el primer paso dicta que para llevar a cabo el proceso de evaluación de una combinación, primero debemos realizar el proceso de evaluación de cada elemento de la combinación. Por lo tanto, la regla de evaluación es de naturaleza *recursiva*; esto es, incluye, como uno de sus pasos, la necesidad de invocar la regla misma.[^10]

Observe cuán sucintamente se puede utilizar la idea de recursión para expresar lo que, en el caso de una combinación profundamente anidada, se vería de otro modo como un proceso bastante complicado. Por ejemplo, evaluar

```scheme
(* (+ 2 (* 4 6))
   (+ 3 5 7))
```

requiere que la regla de evaluación se aplique a cuatro combinaciones diferentes. Podemos obtener una imagen de este proceso representando la combinación en forma de árbol, como se muestra en la figura 1.1. Cada combinación está representada por un nodo con ramas que corresponden al operador y a los operandos de la combinación que se derivan de él. Los nodos terminales (es decir, nodos sin ramas que deriven en ellos) representan operadores o números. Viendo la evaluación en términos del árbol, podemos imaginar que los valores de los operandos se filtran hacia arriba, empezando por los nodos terminales y luego combinándose a niveles cada vez más altos y altos. En general, veremos que la recursividad es una técnica muy poderosa para tratar con objetos jerárquicos en forma de árbol. De hecho, la forma "filtrar valores hacia arriba" de la regla de evaluación es un ejemplo de un tipo general de proceso conocido como *acumulación de árbol* (NtD: *tree accumulation* en inglés).

![Figura 1.1](/secciones/imagenes/capitulo-1/figura-1-1.png)

**Figura 1.1:**  Representación de un árbol, mostrando el valor de cada subcombinación.

A continuación, observe que la aplicación repetida del primer paso nos lleva al punto en el que necesitamos evaluar, no combinaciones, sino expresiones primitivas como números, operadores incorporados, u otros nombres. Nos ocupamos de los casos primitivos estipulando que

* los valores de los números son los números que nombran,

* los valores de los operadores incorporados son las secuencias de instrucciones de máquina que realizan las operaciones correspondientes, y

* los valores de otros nombres son los objetos asociados a esos nombres en el entorno.

Podemos considerar la segunda regla como un caso especial de la tercera estipulando que símbolos como `+` y `*` también están incluidos en el entorno global, y están asociados a las secuencias de instrucciones de máquina que son sus "valores". El punto clave a tener en cuenta es el papel del entorno en determinar el significado de los símbolos en las expresiones. En un lenguaje interactivo como Lisp, no tiene sentido hablar del valor de una expresión como `(+ x 1)` sin especificar ninguna información sobre el entorno que proporcione un significado para el símbolo `x` (o incluso para el símbolo `+`). Como veremos en el capítulo 3, la noción general de que el entorno proporciona un contexto en el que tiene lugar la evaluación desempeñará un papel importante en nuestra comprensión de la ejecución de los programas.

Note que la regla de evaluación arriba mencionada no maneja definiciones. Por ejemplo, evaluar `(define x 3)` no se aplica a dos argumentos, uno de los cuales es el valor del símbolo `x` y el otro es `3`, ya que el propósito de la definición es precisamente asociar `x` con un valor (es decir, `(define x 3)` no es una combinación).

Estas excepciones a la regla general de evaluación son llamadas *formas especiales*. `define` es el único ejemplo de una forma especial que hemos visto hasta ahora, pero nos encontraremos con otros en breve. Cada forma especial tiene su propia regla de evaluación. Los distintos tipos de expresiones (cada uno con su regla de evaluación asociada) constituyen la sintaxis del lenguaje de programación. En comparación con la mayoría de los otros lenguajes de programación, Lisp tiene una sintaxis muy simple; es decir, la regla de evaluación de expresiones puede ser descrita mediante una simple regla general junto con reglas especializadas para un pequeño número de formas especiales.[^11].


#### 1.1.4 Procedimientos Compuestos

Hemos identificado en Lisp algunos de los elementos que deben aparecer en cualquier lenguaje de programación potente:

* Los números y las operaciones aritméticas son datos y procedimientos primitivos.

* El anidamiento de combinaciones proporciona un medio para combinar operaciones.

* Las definiciones que asocian nombres con valores proporcionan un medio limitado de abstracción. 

Ahora aprenderemos sobre las *definiciones de procedimientos*, una técnica de abstracción mucho más poderosa mediante la cual se puede dar un nombre a una operación compuesta y luego referirse a ella como una unidad.

Comenzamos examinando cómo expresar la idea de "al cuadrado". Podríamos decir: "Para elevar al cuadrado algo, multiplíquelo por sí mismo". Esto se expresa en nuestro lenguaje como 

```scheme
(define (cuadrado x) (* x x))
```

Podemos entender esto último de la siguiente manera:

```
(define (cuadrado           x)   (*             x   x))
 ↑       ↑                  ↑     ↑             ↑   ↑                             
 Para    elevar al cuadrado algo, multiplicarlo por si mismo.
```

Tenemos aquí un *procedimiento compuesto*, al que se le ha dado el nombre `cuadrado`. El procedimiento representa la operación de multiplicar algo por sí mismo. La cosa a multiplicar se le da un nombre local, `x`, que juega el mismo papel que un pronombre juega en el lenguaje natural. La evaluación de la definición crea este procedimiento compuesto y lo asocia con el cuadrado del nombre.[^12]

La forma general para la definición de un procedimiento es

```scheme
(define (<nombre> <parámetros formales>) <cuerpo>)
```

El *`<nombre>`* es un símbolo que se asocia con la definición del procedimiento en el entorno.[^13] Los *`<parámetros formales>`* son los nombres utilizados dentro del cuerpo del procedimiento para referirse a los argumentos correspondientes del procedimiento. El *`<cuerpo>`* es una expresión que generará el valor de la aplicación del procedimiento cuando los parámetros formales sean reemplazados por los argumentos reales a los que se aplica el procedimiento.[^14] El *`<nombre>`* y los *`<parámetros formales>`* se agrupan entre paréntesis, justo como lo estarían si se tratara de una llamada real al procedimiento que se está definiendo.

Habiendo definido `cuadrado`, ahora podemos usarlo:

```scheme
(cuadrado 21)
441

(cuadrado (+ 2 5))
49

(cuadrado (cuadrado 3))
81
```
También podemos usar `cuadrado` como un bloque de construcción en la definición de otros procedimientos. Por ejemplo, x² + y² puede expresarse como

```scheme
(+ (cuadrado x) (cuadrado y))
```
Podemos definir fácilmente un procedimiento `suma-de-cuadrados` que, dados dos números cualesquiera como argumentos, produce la suma de sus cuadrados:

```scheme
(define (suma-de-cuadrados x y)
  (+ (cuadrado x) (cuadrado y)))

(suma-de-cuadrados 3 4)
25
```

Ahora podemos usar `suma-de-cuadrados` como un bloque de construcción en la construcción de otros procedimientos:

```scheme
(define (f a)
  (suma-de-cuadrados (+ a 1) (* a 2)))

(f 5)
136
```

Los procedimientos compuestos son usados exactamente de la misma manera que los procedimientos primitivos. De hecho, uno no podría decir al mirar la definición de `suma-de-cuadrados` dada arriba si `cuadrado` fue construido dentro del intérprete, como **+** y **\***, o definido como un procedimiento compuesto.


#### 1.1.5 El Modelo de Sustitución para la Aplicación de Procedimientos

Para evaluar una combinación cuyo operador nombra a un procedimiento compuesto, el intérprete sigue el mismo proceso que para las combinaciones cuyos operadores nombran procedimientos primitivos, que hemos descripto en la sección 1.1.3. Es decir, el intérprete evalúa los elementos de la combinación y aplica el procedimiento (que es el valor del operador de la combinación) a los argumentos (que son los valores de los operandos de la combinación).

Podemos asumir que el mecanismo para aplicar los procedimientos primitivos a los argumentos está incorporado en el intérprete. Para los procedimientos compuestos, el proceso de aplicación es el siguiente:

* Para aplicar un procedimiento compuesto a los argumentos, evalúe el cuerpo del procedimiento con cada parámetro formal sustituido por el argumento correspondiente.

Para ilustrar este proceso, evaluemos la combinación

```scheme
(f 5)
```

donde `f` es el procedimiento definido en la [sección 1.1.4](#-1.1.4-Procedimientos-Compuestos). Comencemos por recuperar el cuerpo de `f`:

```scheme
(suma-de-cuadrados (+ a 1) (* a 2))
```

Luego reemplazamos el parámetro formal `a` por el argumento 5:

```scheme
(suma-de-cuadrados (+ 5 1) (* 5 2))
```

Por lo tanto, el problema se reduce a la evaluación de una combinación con dos operandos y un operador `suma-de-cuadrados`. Evaluar esta combinación implica tres subproblemas. Debemos evaluar al operador para conseguir que el procedimiento se aplique, y debemos evaluar a los operandos para conseguir los argumentos. Ahora `(+ 5 1)` produce 6 y `(* 5 2)` produce 10, por lo que debemos aplicar el procedimiento de `suma-de-cuadrados` a 6 y 10. Estos valores sustituyen a los parámetros formales `x` e `y` en el cuerpo de `suma-de-cuadrados`, reduciendo la expresión a


```scheme
(+ (cuadrado 6) (cuadrado 10))
```

Si usamos la definición de `cuadrado`, esto se reduce a

```scheme
(+ (* 6 6) (* 10 10))
```

que se reduce por multiplicación a

```scheme
(+ 36 100)
```

y finalmente a
```scheme
136
```

El proceso que acabamos de describir se denomina *modelo de sustitución* para la aplicación de procedimientos. Puede tomarse como un modelo que determina el "significado" de la aplicación del procedimiento, en la medida en que concierne a los procedimientos de este capítulo. Sin embargo, hay dos puntos que deben ser remarcados:

* El propósito de la sustitución es ayudarnos a pensar en la aplicación del procedimiento, no para proporcionar una descripción de cómo funciona realmente el intérprete. Los intérpretes típicos no evalúan las aplicaciones de procedimientos manipulando el texto de un procedimiento para sustituir los valores de los parámetros formales. En la práctica, la "sustitución" se realiza utilizando un entorno local para los parámetros formales. Discutiremos esto más detalladamente en los capítulos 3 y 4 cuando examinemos la implementación de un intérprete en detalle.

* A lo largo del curso de este libro, presentaremos una secuencia de modelos cada vez más elaborados de cómo trabajan los intérpretes, culminando con la implementación completa de un intérprete y compilador en el capítulo 5. El modelo de sustitución es sólo el primero de estos modelos, una forma de empezar a pensar formalmente sobre el proceso de evaluación. En general, al modelar fenómenos en ciencia e ingeniería, comenzamos con modelos simplificados e incompletos. A medida que examinamos las cosas en mayor detalle, estos modelos simples se vuelven inadecuados y deben ser reemplazados por modelos más refinados. El modelo de sustitución no es la excepción. En particular, cuando abordemos en el capítulo 3 el uso de procedimientos con "datos mutables", veremos que el modelo de sustitución se quiebra y debe ser reemplazado por un modelo más complicado de aplicación de procedimientos.[^15].

##### Orden Aplicativo versus Orden Normal





### 1.2 Procedimientos y los Procesos que Generan

### 1.3 Formulación de Abstracciones con Procedimientos de Orden Superior


## ---Traducción pendiente---


[^1]: El Manual del programador de Lisp 1 apareció en 1960, y el Manual del programador de Lisp 1.5 (McCarthy 1965) se publicó en 1962. La primera etapa de Lisp es descrito en McCarthy 1978.

[^2]: Los dos dialectos en los que se escribieron la mayor parte de los programas Lisp en la década de 1970 son MacLisp (Moon 1978; Pitman 1983), desarrollado en el Proyecto MAC del MIT, e Interlisp (Teitelman 1974), desarrollado en Bolt Beranek y Newman Inc. y en el Centro de Investigación Xerox Palo Alto. Portable Standard Lisp (Hearn 1969; Griss 1981) fue un dialecto de Lisp diseñado para ser fácilmente portable entre diferentes máquinas. MacLisp engendro una serie de subdialectos, como Franz Lisp, que fue desarrollado en la Universidad de California en Berkeley, y Zetalisp (Moon 1981), que estaba basado en un procesador de propósito especial diseñado en el Laboratorio de Inteligencia Artificial del MIT para que funcionara de manera muy eficiente. El dialecto Lisp utilizado en este libro, llamado Scheme (Steele 1975), fue inventado en 1975 por Guy Lewis Steele Jr. y Gerald Jay Sussman del Laboratorio de Inteligencia Artificial del MIT y posteriormente reimplementado para uso educativo en el MIT. Scheme se convirtió en un estándar IEEE en 1990 (IEEE 1990). El dialecto Common Lisp (Steele 1982, Steele 1990) fue desarrollado por la comunidad Lisp para combinar características de los primeros dialectos Lisp para crear un estándar industrial para Lisp. Common Lisp se convirtió en un estándar ANSI en 1994 (ANSI 1994).

[^3]: Una de esas aplicaciones especiales fue un revolucionario cálculo de importancia científica, una integración del movimiento del Sistema Solar que extendió los resultados anteriores en casi dos órdenes de magnitud, y demostró que las dinámicas del Sistema Solar son caóticas. Este cálculo fue posible gracias a nuevos algoritmos de integración, un compilador de propósito especial y una computadora de propósito especial, todos implementados con la ayuda de herramientas de software escritas en Lisp (Abelson et al. 1992; Sussman y Wisdom 1992).

[^4]: La caracterización de los números como "datos simples" es un engaño descarado. De hecho, el tratamiento de los números es uno de los aspectos más difíciles y confusos de cualquier lenguaje de programación. Algunas cuestiones típicas implicadas son estas: Algunos sistemas informáticos distinguen los números enteros, como el 2, de los números reales, como el 2.71. ¿Es el número real 2.00 diferente del número entero 2? ¿Son las operaciones aritméticas utilizadas para los números enteros las mismas que las operaciones utilizadas para los números reales? ¿6 dividido por 2 produce 3, o 3.0? ¿Qué tan grande es el número que podemos representar? ¿Cuántos decimales de precisión podemos representar? ¿Es el rango de números enteros el mismo que el rango de números reales? Más allá de estas preguntas, por supuesto, subyace un conjunto de cuestiones relativas a los errores de redondeo y truncamiento, es decir, toda la ciencia del análisis numérico. Ya que nuestro enfoque en este libro está en el diseño de programas a gran escala en lugar de en técnicas numéricas, vamos a ignorar estos problemas. Los ejemplos numéricos de este capítulo mostrarán el comportamiento habitual de redondeo que se observa cuando se utilizan operaciones aritméticas que conservan un número limitado de decimales de precisión en operaciones no enteras.

[^5]:  A lo largo de este libro, cuando queramos destacar la distinción entre la entrada introducida por el usuario y la respuesta mostrada por el intérprete, la mostraremos en letras cursivas.

[^6]: Los sistemas Lisp típicamente proveen características para ayudar al usuario en el formateo de expresiones. Dos funciones especialmente útiles son una que automáticamente indenta a la posición correcta de pretty-print cada vez que se inicia una nueva línea y otra que resalta el paréntesis izquierdo correspondiente cada vez que se escribe un paréntesis derecho.

[^7]: Lisp obedece a la convención de que toda expresión tiene un valor. Esta convención, junto con la antigua reputación de Lisp como un lenguaje ineficiente, es la fuente de la broma de Alan Perlis (parafraseando a Oscar Wilde) de que "los programadores de Lisp conocen el valor de todo menos el costo de nada".

[^8]: En este libro, no mostramos la respuesta del intérprete a la evaluación de las definiciones, ya que esto depende en gran medida de la implementación.

[^9]: El capítulo 3 mostrará que esta noción de entorno es crucial, tanto para comprender cómo trabaja el intérprete como también para implementar intérpretes.

[^10]: Puede parecer extraño que la regla de evaluación diga, como parte del primer paso, que debemos evaluar el elemento que esté más a la izquierda de una combinación, ya que en este punto sólo puede ser un operador como `+` o `*` que represente un procedimiento primitivo incorporado como la suma o la multiplicación. Más adelante veremos que es útil poder trabajar con combinaciones cuyos operadores son a su vez expresiones compuestas.

[^11]: Formas sintácticas especiales, que son una alternativa conveniente para estructuras superficiales de cosas que pueden ser escritas de manera más uniforme, son a veces llamadas *azúcar sintáctico* (NdT: *syntactic sugar* en inglés), para usar una frase acuñada por Peter Landin. En comparación con los usuarios de otros lenguajes, los programadores de Lisp, por regla general, están menos preocupados por las cuestiones de sintaxis (por contraste, examine cualquier manual de Pascal y observe cuánto de él está dedicado a las descripciones de la sintaxis). Este desdén por la sintaxis se debe en parte a la flexibilidad de Lisp, que hace que sea fácil cambiar la sintaxis superficial, y en parte a la observación de que muchas construcciones sintácticas "convenientes", que hacen que el lenguaje sea menos uniforme, terminan causando más problemas de los que valen la pena cuando los programas se vuelven grandes y complejos. En palabras de Alan Perlis, *"El azúcar sintáctico causa cáncer de punto y coma"*.

[^12]: Observe que hay dos operaciones diferentes siendo combinadas aquí: estamos creando el procedimiento, y le estamos dando el nombre cuadrado. Es posible, de hecho importante, poder separar estas dos nociones: crear procedimientos sin nombrarlos, y dar nombres a procedimientos que ya han sido creados. Veremos cómo hacerlo en la sección 1.3.2.

[^13]: A lo largo de este libro, describiremos la sintaxis general de las expresiones usando símbolos en cursiva delimitados por corchetes angulares -por ejemplo, *`<nombre>`*- para denotar las "posiciones" en la expresión a ser llenada cuando tal expresión es actualmente usada.

[^14]: En términos más generales, el cuerpo del procedimiento puede ser una secuencia de expresiones. En este caso, el intérprete evalúa cada expresión de la secuencia y devuelve el valor de la expresión final como el valor de la aplicación del procedimiento.

[^15]: A pesar de la simplicidad de la idea de sustitución, resulta sorprendentemente complicado dar una definición matemática rigurosa del proceso de sustitución.  El problema surge de la posibilidad de confusión entre los nombres utilizados para los parámetros formales de un procedimiento y los nombres (posiblemente idénticos) usados en las expresiones a los cuales el procedimiento puede ser aplicado. De hecho, hay una larga historia de definiciones erróneas de sustitución en la literatura de la lógica y la semántica de programación. Ver Stoy 1977 para una discusión cuidadosa de la sustitución.
