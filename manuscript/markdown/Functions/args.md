## Eh. Me Gustaría Tener un Argumento, Por favor.[^mp] {#fargs}

[^mp]: [El Boceto del Argumento](http://www.mindspring.com/~mfpatton/sketch.htm) de "Monty Python's Previous Record" y "Monty Python's Instant Record Collection"

Hasta ahora, hemos visto funciones sin argumentos. No hemos dicho que es un argumento, simplemente nuestras funciones no tienen ninguno.

A> La mayoría de los programadores están perfectamente familiarizados con los argumentos (a menudo llamados "parámetros"). En secundaria se discute esto. Así que sabes que son, y sabes que tú sabes que son, pero por favor ¡sé paciente con la explicación!

Vamos a hacer una función con un argumento:

    function (habitacion) {}

Esta función tiene un argumento, `habitación`, y no tiene cuerpo. Aquí una función con dos argumentos y sin cuerpo:

    function (habitacion, pizarra) {}

Estoy seguro de que te sientes perfectamente confortable con la idea de que esta función tiene dos argumentos: `habitacion`, y `pizarra`. ¿Qué hace uno con los argumentos? Usarlos en el cuerpo, por supuesto. ¿Que piensas que es esto?

    function (diametro) { return diametro * 3.14159265 }

Es una función para calcular la circunferencia de un círculo dado el diámetro. Lo leeré en voz alta "Cuando aplica a un valor que representa el diámetro, esta función *retorna* el diámetro 3.14159265 veces."

Recuerda que para aplicar una función sin argumentos, escribimos `(function (){})()`. Para aplicar una función con un argumento (o argumentos) pondremos el argumento (o argumentos) entre los paréntesis, de esta manera:

    (function (diametro) { return diametro * 3.14159265 })(2)
      //=> 6.2831853

No te sorprenderá como se escribe una función con dos argumentos:

    (function (habitacion, tablero) { return habitacion + tablero })(800, 150)
      //=> 950

T> ### un resumen de funciones y cuerpos
T>
T> Cómo se usan los argumentos en la expresión del cuerpo es probablemente obvio siguiendo los ejemplos, especialmente si sueles usar algún lenguaje (excepto el dialecto de BASIC--el cual recuerdo desde secundaria--que no permitía parámetros cuando llamabas a un procedimiento).
T>
T> Las expresiones consisten tanto en representaciones de valores (como `3.14159262`, `true`, y `undefined`), operadores que combinan expresiones (como `3 + 2`), algunas formas especiales como `[1, 2, 3]` para crear arrays de expresiones, o `function (`*argumentos*`) [`*sentencias-del-cuerpo*}` para la creación de funciones.
T>
T> Posiblemente, una de las sentencias más importantes es la sentencia return. Una sentencia return acepta cualquier expresión válida JavaScript.
T>
T> Esta definición débil es recursiva, como podemos intuir (o usar nuestra experiencia con otros lenguajes) que desde el momento en que una función puede contener una sentencia return con una expresión, podemos escribir una función que retorne una función, o una array que contenga otra array. O una función que retorne una array, una array de funciones, una función que retorne una array de funciones, y así sucesivamente:
T> <<(code/f1.js)

### llamar por valor {#call-by-value}

Como la mayoría de los lenguajes de programación contemporáneos, JavaScript utiliza la "llamada por valor" [estrategia de evaluación]. Lo que significa que cuando tu escribes algún código y parece que aplica a una función, una expresión o expresiones, JavaScript evalua todas las expresiones que aplican a funciones al valor(es) resultante(s).

[estrategia de evaluación]: http://en.wikipedia.org/wiki/Evaluation_strategy

Entonces cuando escribes:

    (function (diametro) { return diametro * 3.14159265 })(1 + 1)
      //=> 6.2831853

Lo que ocurre internamente es que la expresión `1 + 1` fue evaluada primero, resultando en `2`. Entonces nuestra función de circunferencia fue aplicada a `2`.[^f2f]

[^f2f]: Hemos dicho que no puedes aplicar una función a una expresión. *Puedes* aplicar una función a una o más funciones. ¡Las funciones son valores! Esto tiene aplicaciones interesantes, y seran exploradas en profundidad en [Funciones Que Son Aplicadas a Funciones](#consumers).

### variables y enlaces

Ahora que parece fácil y sencillo, vamos a hablar con más detalle sobre los argumentos. Vamos a ir trabajando hasta llegar a funciones como `function (diametro) …:
    function (x) { return (function (y) { return x }) }

A> `function (x) { return (function (y) { return x }) }` parece una locura, como si estuvieramos aprendiendo inglés como segunda lengua y el profesor nos prometiera que pronto estaremos utilizando palabras como *antidisestablishmentarianism*
A> Pero hay otra razón para aprender la palabra *antidisestablishmentarianism*: Podríamos aprender cómo funcionan los prefijos y los sufijos en la gramática inglesa. Es lo mismo que con function (x) { return (function (y) { return x }) }`. Esto tiene cierto significado importante en sí mismo, Y también es una excusa excelente para aprender sobre funciones que hacen funciones, entornos, variables y más.

A fin de hablar acerca d cómo funciona, deberíamos estar de acuerdo en unos pocos términos (ya los debes conocer, pero vamos a repasarlos juntos y así "sincronizar nuestros diccionarios"). La primera `x`, la que hay en `{ return x }`, no es un argumento, *es una expresión que se refiere a una variable*. Los argumentos y las variables trabajan de la misma manera si nos referimos a `function (x) { return (function (y) { return x }) }` o simplemente `function (x) { return x }`.

Cada vez que una función es invocada ("invocada" significa aplica a cero o más argumentos), se crea un nuevo entorno. Un entorno es un diccionario (posiblemente vacío) que mapea variables a valores por nombre. La `x`en la expresión que llamamos "variable" es en si misma una expresión que es evaluada mirando su valor en el entorno.

¿Cómo se pone el valor en el entorno? Bien para los argumentos, es muy sencillo. Cuando tú aplicas la función a los argumentos, se añade una entrada al diccionario por cada argumento. Entonces es cuando podemos escribir:
    (function (x) { return x })(2)
      //=> 2

Lo que ocurre es esto:

1. Javascript analiza toda esta cosa como una expresión hecha de varias sub-expresiones.
1. Entonces empieza a evaluar la expresión, incluyendo las sub-expresiones
1. La sub-expresión, `function (x) { return x }` evalua a una función.
1. Otra sub-expresión, '2' evalua al número 2.
1. JavaScript ahora evalua aplicando la función al argumento `2`. Aquí empieza a ponerse interesante...
1. Se crea un entorno.
1. El valor '2' se enlaza al nombre 'x' en el entorno.
1. La expresión 'x' (a la derecha de la función) se evalua en el entorno que se acaba de crear.
1. El valor de la variable cuando se evalúa en un entorno es el valor enlazado al nombre de la variable en ese entorno, el cual es '2'
1. Y ese es nuestro resultado.


Cuando hablamos de entornos, utilizaremos una [sintaxis poco sorprendente] [json] para mostrar sus enlaces: `{x: 2, ...}`. indicando, que el entorno es un diccionario, y que el valor `2` está enlazado al nombre `x`, y que podría haber otras cosas en el diccoinario que no entrarán en discusión ahora mismo.

[json]: http://json.org/

### llamada por compartición

Antes, hemos distinguido los *tipos de valor* de los *tipos de referencia* en Javascript. En ese momento, hemos visto como JavaScript distingue objetos que son idénticos de los que no lo son. Ahora es el momento de echar otro vistazo entre los tipos de valor y de referencia.

Hay una propiedad que JavaScript mantiene de forma estricta: Cuando un valor--cualquier valor--se pasa como argumento a una función, el valor enlazado a la función del entorno debe ser idéntico al original.

Dijimos que JavaScript enlaza nombres a valores, pero no dijimos qué significaba enlazar un nombre a un valor. Ahora podemos elaborarlo: Cuando JavaScript enlaza un tipo-de-valor a un nombre, este hace una copia del valor y pone la copia en el entorno. Como recordarás, los tipos de valor como cadenas o números unos a otros si tienen el mismo contenido. Por eso JavaScript puede hacer tantas copias de cadenas, números o booleanos como se desee.

¿Qué ocurre con los tipos de referencia? JavaScript no pone copias de valores por referencia en ningún entorno. JavaScript pone *referencias* a los tipos de referenca en los entornos, y cuando es necesario utilizar el valor, JavaScript usa la referencia para obtener el original.

Ya que varias referencias puede compartir el mismo valor, y porque JavaScript pasa referencias como argumentos, se puede decir que JavaScript pone en práctiva las semánticas de "llamada por compartición". En general se entiende como llamada por compartición como una especialización de la llamada por valor, lo que explica porqué algunos valores son conocidos como tipos de valor y otros tipos de referencia.

Y con esto, ya estamos preparados para revisar las *clausuras*. Cuando combinamos nuestro conocimiento acerca de tipos de valor, tipos de referencia, argumentos y clausuras, entenderemos porqué esta función siempre evalúa a `cierto` sin importar qué argumentos apliques a la misma:

    function (valor) {
      return (function (copia) {
        return copia === valor
      })(valor)
    }