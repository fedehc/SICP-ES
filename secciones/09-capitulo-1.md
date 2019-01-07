# Capítulo 1

## Construyendo Abstracciones con Procedimientos

> Los actos de la mente, en la cual ejerce su poder sobre las ideas simples, son principalmente estos tres: 1. Combinando varias ideas simples en una compuesta, y de este modo todas las ideas complejas son hechas. 2. La segunda es juntando dos ideas, sean simples o complejas, entre sí, y poniéndolas una a la otra para poder verlas a la vez, sin unirlas en una sola, por lo cual se obtienen todas sus ideas de relaciones. 3. La tercera es separarlas de todas las demás ideas que las acompañan en su existencia real: esto se llama abstracción, y de este modo todas sus ideas generales son elaboradas.
>
> **John Locke**, *Un ensayo sobre la comprensión humana* (1690)

Estamos a punto de estudiar la idea de un proceso computacional. Los procesos computacionales son seres abstractos que habitan en las computadoras. A medida que evolucionan, los procesos manipulan otras cosas abstractas llamadas datos. La evolución de un proceso está dirigida por un patrón de reglas llamado programa. La gente crea programas para dirigir procesos. En efecto, conjuramos los espíritus de la computadora con nuestros hechizos.

Estamos a punto de estudiar la idea de un proceso computacional. Los procesos computacionales son seres abstractos que habitan en las computadoras. A medida que evolucionan, los procesos manipulan otras cosas abstractas llamadas datos. La evolución de un proceso está dirigida por un patrón de reglas llamado programa. La gente crea programas para dirigir procesos. En efecto, conjuramos los espíritus de la computadora con nuestros hechizos.

Un proceso computacional es ciertamente muy parecido al concepto que los hechiceros tienen de un espíritu. No puede ser visto ni tocado. No está compuesto de materia en absoluto. Sin embargo, es muy real. Puede realizar trabajo intelectual. Puede responder a preguntas. Puede influir en el mundo desembolsando dinero en un banco o controlando el brazo de un robot en una fábrica. Los programas que usamos para conjurar procesos son como los conjuros de un hechicero. Están cuidadosamente compuestos de expresiones simbólicas en lenguajes de programación arcanos y esotéricos que determinan las tareas que queremos que nuestros procesos realicen.

Un proceso computacional, en una computadora que funciona correctamente, ejecuta programas con precisión y exactitud. Así, como el aprendiz del hechicero, los programadores principiantes deben aprender a entender y anticipar las consecuencias de sus conjuros. Incluso pequeños errores (usualmente llamados bugs o fallos) en los programas pueden tener consecuencias complejas e imprevistas.

Afortunadamente, aprender a programar es considerablemente menos peligroso que aprender hechicería, porque los espíritus con los que tratamos están convenientemente contenidos de una manera segura. La programación en el mundo real, sin embargo, requiere cuidado, experiencia y sabiduría. Un pequeño error en un programa de diseño asistido por computadora, por ejemplo, puede llevar al colapso catastrófico de un avión o una presa o a la autodestrucción de un robot industrial.

Los expertos en ingeniería de software tienen la capacidad de organizar programas de manera que modo que puedan estar razonablemente seguros de que los procesos resultantes realizarán las tareas previstas. Pueden visualizar el comportamiento de sus sistemas por adelantado. Saben cómo estructurar los programas para que los problemas imprevistos no tengan consecuencias catastróficas, y cuando surgen problemas, pueden depurar sus programas. Los sistemas computacionales bien diseñados, como los automóviles o los reactores nucleares bien diseñados, se diseñan de manera modular, de modo que las partes puedan ser construidas, reemplazadas y depuradas por separado.


### Programando en Lisp

Necesitamos un lenguaje apropiado para describir los procesos, y para ello utilizaremos el lenguaje de programación Lisp. Así como nuestros pensamientos cotidianos se expresan normalmente en nuestro lenguaje natural (como el español, el inglés, el francés o el japonés), y las descripciones de los fenómenos cuantitativos se expresan con notaciones matemáticas, nuestros pensamientos procedurales los expresaremos en Lisp. Lisp fue inventado a fines de la década de 1950 como un formalismo para razonar sobre el uso de ciertos tipos de expresiones lógicas, llamadas ecuaciones de recursión, como modelo para la computación. El lenguaje fue concebido por John McCarthy y se basa en su trabajo *"Recursive Functions of Symbolic Expressions and Their Computation by Machine"* (en español: *Funciones Recursivas de las Expresiones Simbólicas y su Cálculo por Máquina*, McCarthy 1960).

A pesar de su concepción como un formalismo matemático, Lisp es un lenguaje de programación práctico. Un intérprete de Lisp es una máquina que lleva a cabo los procesos descritos en el lenguaje Lisp. El primer intérprete de Lisp fue implementado por McCarthy con la ayuda de colegas y estudiantes del Artificial Intelligence Group del Laboratorio de Investigación de Electrónica del MIT y del Centro de Cálculo del MIT.[^1] Lisp, cuyo nombre es un acrónimo de LISt Processing, fue diseñado para proporcionar capacidades de manipulación simbólica para atacar problemas de programación como la diferenciación simbólica y la integración de expresiones algebraicas. Para este propósito se incluyeron nuevos objetos de datos conocidos como átomos y listas, que lo diferenciaba notablemente de todos los demás lenguajes de la época.

Lisp no fue el producto de un esfuerzo de diseño concertado. Al contrario, evolucionó de manera informal y experimental en respuesta a las necesidades de sus usuarios y por cuestiones pragmáticas de implementación. La evolución informal de Lisp ha continuado a través de los años, y la comunidad de usuarios de Lisp ha resistido tradicionalmente los intentos de promulgar cualquier definición "oficial" del lenguaje. Esta evolución, junto con la flexibilidad y elegancia de su concepción inicial, ha permitido que Lisp, que es el segundo lenguaje más antiguo en uso hoy en día (sólo Fortran es más antiguo), se adapte continuamente para abarcar las ideas más modernas sobre el diseño de programas. Por lo tanto, Lisp es ya una familia de dialectos que, aunque comparten la mayoría de las características originales, pueden diferir uno del otro de modo significativo. El dialecto de Lisp utilizado en este libro se llama Scheme.[^2]

Debido a su carácter experimental y su énfasis en la manipulación de simbóloca, Lisp fue al principio muy ineficiente para los cálculos numéricos, al menos en comparación con Fortran. A lo largo de los años, sin embargo, los compiladores Lisp se han desarrollado para traducir programas a código de máquina que pueden realizar cálculos numéricos de forma razonablemente eficiente. Y para aplicaciones especiales, Lisp ha sido usado con gran efectividad.[^3] Aunque Lisp aún no ha superado su antigua reputación de ser ineficiente, Lisp se usa ahora en muchas aplicaciones donde la eficiencia no es la preocupación central. Por ejemplo, Lisp se ha convertido en el lenguaje preferido para los lenguajes de shell del sistemas operativos y para los lenguajes de extensión de los editores y para los sistemas de diseño asistido por computadora.

Si Lisp no es un lenguaje convencional, ¿por qué lo usamos como marco para nuestra discusión de la programación? Porque el lenguaje posee características únicas que lo convierten en un excelente medio para estudiar importantes constructos de programación y estructuras de datos y para relacionarlos con las características lingüísticas que los soportan. El más significativo de estos rasgos es el hecho de que las descripciones de los procesos de Lisp, llamados procedimientos, pueden ser representadas y manipuladas como datos de Lisp. La importancia de esto es que existen poderosas técnicas de diseño de programas que se basan en la habilidad de desdibujar la distinción tradicional entre datos "pasivos" y procesos "activos". Como descubriremos, la flexibilidad de Lisp en el manejo de procedimientos como datos lo convierte en uno de los lenguajes más convenientes en existencia para explorar estas técnicas. La capacidad de representar procedimientos como datos también hace de Lisp un excelente lenguaje para escribir programas que deben manipular otros programas como datos, como los intérpretes y compiladores que soportan lenguajes de programación. Más allá de estas consideraciones, programar en Lisp es muy divertido.


### 1.1 Los Elementos de la Programación

Un poderoso lenguaje de programación es más que un medio para instruir a una computadora para que realice tareas. El lenguaje también sirve como marco dentro del cual organizamos nuestras ideas acerca de los procesos. Por lo tanto, cuando describimos un lenguaje, debemos prestar especial atención a los medios que el lenguaje proporciona para combinar ideas simples para formar ideas más complejas. Cada lenguaje poderoso tiene tres mecanismos para lograr esto:

* **expresiones primitivas**, que representan las entidades más simples que conciernen al lenguaje,

* **modos de combinación**, mediante los cuales los elementos compuestos se construyen a partir de los más sencillos, y

* **medios de abstracción**, por el cual los elementos compuestos pueden ser nombrados y manipulados como unidades.

En programación, nosotros lidiamos con dos tipos de elementos: procedimientos y datos. (más adelante descubriremos que realmente no son tan distintos.) Informalmente, los datos son "cosas" que queremos manipular, y los procedimientos son descripciones de las reglas para manipular los datos. Por lo tanto, cualquier lenguaje de programación poderoso debe ser capaz de describir datos primitivos y procedimientos primitivos y debe tener métodos para combinar y abstraer procedimientos y datos.

En este capítulo sólo trataremos con datos numéricos simples para que podamos concentrarnos en las reglas para la construcción de procedimientos.[^4] En capítulos posteriores veremos que estas mismas reglas nos permiten construir procedimientos para manipular datos compuestos también.


#### 1.1.1 Expresiones

Una manera fácil de empezar a programar es examinar algunas interacciones típicas con un intérprete para el dialecto Scheme de Lisp. Imagínese que se encuentra sentado en una terminal de computadora. Usted escribe una expresión, y el intérprete responde mostrando el resultado de su evaluación de esa expresión.

Un tipo de expresión primitiva que uno podría escribir es un número. (más precisamente, la expresión que uno escriba consiste en los numerales que representan el número en base 10). Si usted presenta a Lisp con un número

    486

el intérprete responderá imprimiendo[^5]

    486

Las expresiones que representan números pueden combinarse con una expresión que represente un procedimiento primitivo (como + o \*) para formar una expresión compuesta que represente la aplicación del procedimiento a esos números. Por ejemplo:

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

Expresiones como éstas, formadas por la delimitación de una lista de expresiones entre paréntesis con el fin de indicar la aplicación del procedimiento, son llamadas combinaciones. El elemento más a la izquierda de la lista se llama el operador, y los otros elementos se llaman operandos. El valor de una combinación se obtiene aplicando el procedimiento especificado por el operador a los argumentos que son los valores de los operandos.

La convención de colocar el operador a la izquierda de los operandos se conoce como notación de prefijo (NdT: o también conocido como *notación polaca*), y puede ser algo confuso al principio porque se aparta significativamente de la convención matemática habitual. Sin embargo, la notación de prefijo tiene varias ventajas. Una de ellas es que puede acomodar procedimientos que pueden tomar un número arbitrario de argumentos, como en los siguientes ejemplos:

    (+ 21 35 12 7)
    75

    (* 25 4 12)
    1200

No puede surgir ninguna ambigüedad, ya que el operador es siempre el elemento más a la izquierda y toda la combinación está delimitada por los paréntesis.

Una segunda ventaja de la notación de prefijo es que se extiende de una manera directa para permitir que las combinaciones sean anidadas, es decir, que tengan combinaciones cuyos elementos son en sí mismos combinaciones:

    (+ (* 3 5) (- 10 6))
    19

No hay límite (en principio) a la profundidad de este tipo de anidamiento y a la complejidad general de las expresiones que el intérprete de Lisp puede evaluar. Somos nosotros los humanos los que nos confundimos por expresiones relativamente simples como

    (+ (* 3 (+ (* 2 4) (+ 3 5))) (+ (- 10 7) 6))

que el intérprete evaluaría fácilmente como 57. Podemos ayudarnos a nosotros mismos escribiendo esta expresión en la forma

    (+ (* 3
          (+ (* 2 4)
             (+ 3 5)))
       (+ (- 10 7)
          6))

siguiendo una convención de formato conocida como impresión-bonita (NdT: *pretty-printing* en inglés), en la que cada combinación larga se escribe de manera que los operandos estén alineados verticalmente. Las indentaciones resultantes muestran claramente la estructura de la expresión.[^6]

Incluso con expresiones complejas, el intérprete siempre opera en el mismo ciclo básico: lee una expresión de la terminal, evalúa la expresión e imprime el resultado. Este modo de funcionamiento se expresa a menudo diciendo que el intérprete funciona en un bucle de lectura-evaluación-impresión (NdT: en inglés *read-eval-print loop*, o *REPL*). Observe en particular que no es necesario indicar explícitamente al intérprete que imprima el valor

#### 1.1.2 Denominación y el entorno


### 1.2 Procedimientos y los Procesos que Generan

### 1.3 Formulación de Abstracciones con Procedimientos de Orden Superior


## ---Traducción pendiente---


[^1]: El Manual del programador de Lisp 1 apareció en 1960, y el Manual del programador de Lisp 1.5 (McCarthy 1965) se publicó en 1962. La primera etapa de Lisp es descrito en McCarthy 1978.

[^2]: Los dos dialectos en los que se escribieron la mayor parte de los programas Lisp en la década de 1970 son MacLisp (Moon 1978; Pitman 1983), desarrollado en el Proyecto MAC del MIT, e Interlisp (Teitelman 1974), desarrollado en Bolt Beranek y Newman Inc. y en el Centro de Investigación Xerox Palo Alto. Portable Standard Lisp (Hearn 1969; Griss 1981) fue un dialecto de Lisp diseñado para ser fácilmente portable entre diferentes máquinas. MacLisp engendro una serie de subdialectos, como Franz Lisp, que fue desarrollado en la Universidad de California en Berkeley, y Zetalisp (Moon 1981), que estaba basado en un procesador de propósito especial diseñado en el Laboratorio de Inteligencia Artificial del MIT para que funcionara de manera muy eficiente. El dialecto Lisp utilizado en este libro, llamado Scheme (Steele 1975), fue inventado en 1975 por Guy Lewis Steele Jr. y Gerald Jay Sussman del Laboratorio de Inteligencia Artificial del MIT y posteriormente reimplementado para uso educativo en el MIT. Scheme se convirtió en un estándar IEEE en 1990 (IEEE 1990). El dialecto Common Lisp (Steele 1982, Steele 1990) fue desarrollado por la comunidad Lisp para combinar características de los primeros dialectos Lisp para crear un estándar industrial para Lisp. Common Lisp se convirtió en un estándar ANSI en 1994 (ANSI 1994).

[^3]: Una de esas aplicaciones especiales fue un revolucionario cálculo de importancia científica, una integración del movimiento del Sistema Solar que extendió los resultados anteriores en casi dos órdenes de magnitud, y demostró que las dinámicas del Sistema Solar son caóticas. Este cálculo fue posible gracias a nuevos algoritmos de integración, un compilador de propósito especial y una computadora de propósito especial, todos implementados con la ayuda de herramientas de software escritas en Lisp (Abelson et al. 1992; Sussman y Wisdom 1992). 
