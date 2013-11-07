## Me Gustaría tener Tener Algunos Argumentos. Otra vez

Como hemos hablado, cuando se aplican argumentos a una función (o "se la llama con argumentos"), JavaScript enlaza los valores de los argumentos de la función a sus nombres en el entorno creado para la ejecución de la función. De lo que no hemos hablado, es que también enlaza algunos nombres "mágicos" a cada uno de los argumentos de la lista.

Nunca deberías intentar definir tus propios enlaces hacia estos nombres. Considéralos siempre de sólo lectura. El primero se llama `this` y se enlaza a algo llamado el [context](#context) de la función. Lo veremos cuando vayamos a hablar de los objetos y las clases. El segundo es muy interesante, se llama `arguments`, y lo más interesante es que contiene una lista de los argumentos que se le pasan a la función:

    function suma (a, b) {
      return arguments[0] + arguments[1]
    }

    suma(2,3)
      //=> 5

Aunque `arguments` parece un array, no es una array:[^pojo] Se parece más un objeto:[^pojo] lo que ocurre es que enlaza los valores a propiedades cuyos nombres parecen enteros y empezando desde cero:

    function obtenerArgumentos (a, b) {
      return arguments
    }

    obtenerArgumentos(2,3)
      //=> { '0': 2, '1': 3 }


`arguments` siempre contiene todos los argumentos pasados a la función, independientemente de cuantos se declararon. Por lo tanto, podemos escribir `suma` de esta manera:

    function suma () {
      return arguments[0] + arguments[1]
    }

    suma(2,3)
      //=> 5

Cuando hablemos sobre los objetos, hablaremos de propiedades en más detalle. Esta es una propiedad interesante de `arguments`:

    function cuantosArgumentosHay () {
      return arguments['length']
    }

    cuantosArgumentosHay()
      //=> 0

    cuantosArgumentosHay('hola')
      //=> 1

    cuantosArgumentosHay('los', 'tiburones', 'son', 'superpredadores')
      //=> 4

El uso más común de `arguments` es construir funciones que puedan coger un número variable de argumentos. Veremos como se usan en varias recetas, partiendo de [partial application](#simple-partial) y [ellipses](#ellipses).
[^pojo]: We'll look at [arrays](#arrays) and [plain old javascript objects](#objects) in depth later.