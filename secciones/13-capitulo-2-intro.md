## Capítulo 2

# Construyendo Abstracciones con Datos

> Ahora llegamos al paso decisivo de la abstracción matemática: nos olvidaremos de lo que significan los símbolos. ...[El matemático] no necesita estar inactivo; hay muchas operaciones que él puede llevar a cabo con estos símbolos, sin tener que fijarse nunca por las cosas que representan.
>
> **Hermann Weyl**, *The Mathematical Way of Thinking*

Nos concentramos en el Capítulo 1 en procesos computacionales y
sobre el papel de los procedimientos en el diseño del programa. Vimos cómo usar datos primitivos (números) y operaciones primitivas (operaciones aritméticas), cómo combinar procedimientos para formar procedimientos compuestos a través de la composición, condicionales y el uso de parámetros, y cómo abstraer procedimientos mediante el uso de `define`. Vimos que un procedimiento puede considerarse como un antecedente para la evolución local de un proceso, y clasificamos, razonamos y realizamos análisis algorítmicos simples de algunos patrones comunes para procesos tal como están incorporados en los procedimientos.

También vimos que los procedimientos de orden superior mejoran el poder de nuestro lenguaje al permitirnos manipular y, por lo tanto, razonar en términos de métodos generales de computación. Esta es gran parte de la esencia de la programación.
En este capítulo vamos a ver datos más complejos. Todos los procedimientos del capítulo 1 operan con datos numéricos simples y simples
los datos no son suficientes para muchos de los problemas que queremos abordar usando la computación. Los programas generalmente están diseñados para modelar complejos
fenómenos, y más de las veces uno debe construir computacional objetos que tienen varias partes para modelar fenómenos del mundo real que tienen varios aspectos. Así, mientras que nuestro enfoque en el capítulo 1 era
en la construcción de abstracciones combinando procedimientos para formar compuestos procedimientos, pasamos en este capítulo a otro aspecto clave de cualquier lenguaje de programación: los medios que proporciona para construir abstracciones mediante
combinando objetos de datos para formar *datos compuestos*.

¿Por qué queremos datos compuestos en un lenguaje de programación? por
las mismas razones por las que queremos procedimientos compuestos: elevar el nivel conceptual en el que podemos diseñar nuestros programas, para aumentar el modularidad de nuestros diseños, y para mejorar el poder expresivo de nuestros
idioma. Así como la capacidad de definir procedimientos nos permite tratar con procesos en un nivel conceptual más alto que el de las operaciones primitivas del lenguaje, la capacidad de construir objetos de datos compuestos nos permite tratar datos a un nivel conceptual más alto que el de Objetos de datos primitivos del lenguaje.

# ---Traducción pendiente---