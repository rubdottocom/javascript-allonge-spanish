## Construyendo Bloques {#buildingblocks}

Cuando ves funciones dentro de funciones en JavaScript, parece un poco de "código espagueti". La potencia de JavaScript reside en que puedes hacer cualquier cosa. La debilidad es que puedes hacerlo. Hay ifs, fors, returns, todo mezclado sin ningún tipo de orden. Aunque no es necesario que te limites a un número pequeño de patrones simples, entender estos patrones te puede ayudar para estructurar tu código en bloques básicos

### composición

Uno de los bloques más básicos es la *composición*:

    function cocinarYComer (comida) {
      return comer(cocinar(comida))
    }

En realidad es muy simple: Siempre que encadenes dos o más funciones juntas, estás haciendo una composición de las mismas. Puedes componerlas de forma explícita con código JavaScript como acabamos de hacer. También puedes generalizar una composición con el Combinador B o "componer" como vimos en [Combinadores y Decoradores](#combinators):

    function componer (a, b) {
      return function (c) {
        return a(b(c))
      }
    }

    var cocinarYComer = componer(comer, cocinar);

Si sólo fuera esto, la composición no importaría demasiado. Pero como muchos patrones, usarlos cuando corresponde es sólo el 20% del beneficio. El otro 80% viene por organizar tu código de manera que puedas usarlo: Escribiendo funciones que se pueden componer de varias maneras.

En las recetas, le echaremos un vistazo a un decorador llamado [once](#once): Se asegura de que una función sólo se ejecuta una vez. Después de eso, no hace nada. Once es útil para asegurarse de que ciertos efectos no se repiten. También echaremos un vistazo a [maybe](#maybe): Que se asegura de que una función no haga nada si no se le pasa nada (como `null` o `undefined`) como argumento.

Por supuesto, no necesitas usar combinadores para implementar cualquiera de estas ideas, puedes utilizar sentencias if. Pero `once` y `maybe` componen, por lo qe puedes encadenarlos juntos si lo necesitas.

    function transferenciaEnRealidad(desde, a, cantidad) {
      // hacer algo
    }

    var invocarTransferencia = once(maybe(transferenciaEnRealidad(...)));

### aplicación parcial

Otro bloque básico de construcción es la *aplicación parcial*. Cuando una función obtiene múltiples parámetros, "aplicamos" la función a los parámetros evaluándola con cada uno de los parámetros, produciendo un valor. Pero ¿qué pasa si sólo suministramos algunos de los parámetros? En ese caso, no podemos llegar al valor final, pero podemos obtener una función que represente "parte" de nuestra aplicación.

El código es más fácil que las palabras para esto. La librería [Underscore] provee una función de alto orden llamada *map*.[^headache] Aplica otra función a cada uno de los elementos de una array, como esto:

    _.map([1, 2, 3], function (n) { return n * n })
      //=> [1, 4, 9]

Este código implementa una aplicación parcial de la función map aplicando la función `function (n) { return n * n }` como su segundo argumento:

    function calculaElCuadradoDeTodo (array) {
      return _.map(array, function (n) { return n * n })
    }

La función resultante--`calculaElCuadradoDeTodo`--sigue siendo la función map, es sólo que ya hemos aplicado uno de sus parámetros. `calculaElCuadradoDeTodo` es bonita, pero ¿por qué escribir una función cada vez que queremos aplicar parcialmente una función a map? Podemos abstraer esto a un nivel más alto. `mapWith` toma cualquier función como parámetro y devuelve una aplicación parcial de la función map.

    function mapWith (fn) {
      return function (array) {
        return _.map(array, fn)
      }
    }

    var calculaElCuadradoDeTodo = mapWith(function (n) { return n * n });

    calculaElCuadradoDeTodo([1, 2, 3])
      //=> [1, 4, 9]

Volveremos a hablar de mapWith en [las recetas](#mapWith). Lo importante aquí es ver que la aplicación parcial es ortogonal a la composición, y que ambos trabajan juntos perfectamente:

    var calculaElCuadradoDeTodoDeFormaSegura = mapWith(maybe(function (n) { return n * n }));

    calculaElCuadradoDeTodoDeFormaSegura([1, null, 2, 3])
      //=> [1, null, 4, 9]

Hemos generalizado la composición con el combinador `componer`. La aplicación parcial también tiene un combinador, el que veremos en la receta [partial](#partial).

[^bind]: En JavaScript Moderno se provee de una forma limitada de aplicación parcial a través del método `Function.prototype.bind`. Esto se discutirá ampliamente cuando revisemos los contextos de una función.

[^headache]: En JavaScript Moderno se provee de un método map para arrays, pero la implementación de Underscore también trabaja con navegadores más antiguos si te estás encontrando con este dolor de cabeza

[Underscore]: http://underscorejs.org