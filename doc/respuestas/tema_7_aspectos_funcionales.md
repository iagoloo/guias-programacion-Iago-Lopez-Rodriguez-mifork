<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

Un puntero a función es una variable cuyo valor es la **dirección de memoria de una función**, permitiendo invocar dicha función de forma indirecta. En C, las funciones no son valores de primer nivel como en los lenguajes funcionales modernos, pero pueden referenciarse mediante punteros, lo que habilita patrones como callbacks, tablas de funciones o selección dinámica del comportamiento en tiempo de ejecución. Conceptualmente, un puntero a función encapsula “qué operación ejecutar”, separando la llamada de la implementación concreta.

Desde el punto de vista sintáctico, un puntero a función debe declarar explícitamente el **tipo de retorno y los tipos de los parámetros** de la función a la que apuntará. Esto resulta coherente con el modelo de tipos de C, donde el compilador necesita conocer exactamente la firma de la función para generar una llamada válida. A diferencia de Java, donde el polimorfismo se basa en métodos virtuales y referencias a objetos, en C este mecanismo se implementa de forma explícita y manual mediante punteros.

En el ejemplo siguiente se define una función que recibe una cadena de caracteres y devuelve la misma cadena transformada a mayúsculas. A continuación, se declara un puntero local llamado `aMayusculas` que apunta a dicha función y se invoca a través del puntero, mostrando cómo la llamada indirecta es equivalente a una llamada directa desde el punto de vista del resultado.

```c
#include <stdio.h>
#include <ctype.h>

char* convertirAMayusculas(char* cadena) {
    for (int i = 0; cadena[i] != '\0'; i++) {
        cadena[i] = toupper((unsigned char)cadena[i]);
    }
    return cadena;
}

int main() {
    char texto[] = "Programacion en C";

    /* Declaración del puntero a función */
    char* (*aMayusculas)(char*);
    
    /* Asignación de la función al puntero */
    aMayusculas = convertirAMayusculas;

    /* Invocación de la función mediante el puntero */
    char* resultado = aMayusculas(texto);

    printf("%s\n", resultado);
    return 0;
}
```

Este mecanismo se considera un precursor de ideas propias de la programación funcional, como el paso de comportamientos como argumentos, aunque en C se realiza de forma más verbosa y con menor seguridad de tipos que en lenguajes diseñados explícitamente para ese paradigma.



## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

Una **función lambda** es una función **anónima**, es decir, una función que no tiene nombre propio y que se define de forma inline para ser asignada a una variable, pasada como argumento o devuelta como resultado. Estas funciones tratan el comportamiento como un valor, idea central de la programación funcional. A diferencia de C, donde se necesitan punteros a función y una sintaxis explícita, en lenguajes como JavaScript o Java moderno las lambdas permiten expresar operaciones de forma más concisa y legible.

En **JavaScript**, las funciones son ciudadanos de primer nivel, por lo que una función lambda puede asignarse directamente a una variable local. El siguiente ejemplo define una función lambda que recibe una cadena y devuelve su versión en mayúsculas, asignándola a la variable `aMayusculas` y ejecutándola posteriormente. La conversión se apoya en funciones estándar del propio lenguaje, sin necesidad de gestión manual de memoria.

```javascript
let aMayusculas = (cadena) => {
    return cadena.toUpperCase();
};

let texto = "Programación en JavaScript";
let resultado = aMayusculas(texto);
console.log(resultado);
```

En **Java**, las funciones lambda no existen de forma independiente, sino que se expresan a través de **interfaces funcionales**, es decir, interfaces con un único método abstracto. Para simplificar, puede utilizarse la interfaz genérica `Function<String, String>` del paquete `java.util.function`, que representa una función que recibe un `String` y devuelve otro `String`. La siguiente lambda se asigna a la variable local `aMayusculas` y se invoca mediante el método `apply`, manteniendo un paralelismo conceptual con los punteros a función de C, pero con mayor seguridad de tipos y mejor integración con la orientación a objetos.

```java
import java.util.function.Function;

public class EjemploLambda {
    public static void main(String[] args) {
        Function<String, String> aMayusculas = 
            cadena -> cadena.toUpperCase();

        String texto = "Programación en Java";
        String resultado = aMayusculas.apply(texto);

        System.out.println(resultado);
    }
}
```

Este enfoque permite expresar operaciones de forma más declarativa y reusable, y sirve como base para técnicas funcionales más avanzadas como el uso de streams, composición de funciones y programación orientada a operaciones en lugar de estructuras.



## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

El **paradigma funcional** es un estilo de programación que trata la computación como la evaluación de funciones matemáticas, evitando en la medida de lo posible el uso de estado mutable y efectos secundarios. En este paradigma, el énfasis no está en “cómo” se ejecutan los pasos, sino en “qué” transformación se aplica a los datos. Las funciones suelen ser puras, lo que significa que, para los mismos argumentos, siempre producen el mismo resultado y no modifican variables externas, facilitando el razonamiento, la prueba y el paralelismo del código frente a enfoques más imperativos como los habituales en C.

Se denomina a lenguajes como **Java 8** multi‑paradigma porque permiten combinar de forma integrada distintos estilos de programación. Java sigue siendo fundamentalmente orientado a objetos, basado en clases, objetos, herencia y polimorfismo, pero desde Java 8 incorpora construcciones propias del paradigma funcional, como funciones lambda, referencias a métodos e interfaces funcionales. De este modo, es posible resolver un mismo problema usando un enfoque imperativo clásico o uno funcional más declarativo, eligiendo el que resulte más expresivo o adecuado en cada contexto.

Decir que las funciones son **“ciudadanos de primera clase”** implica que pueden tratarse igual que cualquier otro valor del lenguaje. Esto significa que pueden almacenarse en variables, pasarse como argumentos a otras funciones, devolverse como resultado y componerse entre sí. En C esto se consigue de forma limitada mediante punteros a función, mientras que en Java moderno se logra con lambdas y tipos funcionales. Esta característica es clave del paradigma funcional, ya que permite abstraer el comportamiento y construir programas más flexibles y expresivos.


## 4. Explica la sintaxis básica de una función lambda en Java.

En **Java**, la sintaxis básica de una función lambda se introdujo a partir de **Java 8** y permite implementar de forma concisa una **interfaz funcional**, es decir, una interfaz que define un único método abstracto. Una expresión lambda no se declara como método independiente, sino como una implementación inline del método de dicha interfaz. Su forma general es `(parámetros) -> expresión` o `(parámetros) -> { bloque de instrucciones }`, donde el operador `->` separa los parámetros de la lógica que se ejecutará.

Los **parámetros** se escriben de manera similar a los de un método, aunque el compilador puede inferir sus tipos en la mayoría de los casos. Si la lambda tiene un único parámetro, los paréntesis pueden omitirse, y si el cuerpo consiste en una sola expresión, las llaves y la palabra clave `return` también pueden eliminarse. Esta sintaxis compacta permite expresar comportamientos simples de forma clara, evitando la creación explícita de clases anónimas, que era la alternativa tradicional en Java.

```java
Function<String, String> aMayusculas = 
    cadena -> cadena.toUpperCase();
```

Cuando el cuerpo de la lambda requiere más de una instrucción, se utilizan llaves y un `return` explícito si la función devuelve un valor. Aunque la lambda no tiene nombre propio, queda asociada a una referencia tipada, lo que garantiza la verificación estática de tipos. De esta forma, Java combina seguridad de tipos propia de la orientación a objetos con una sintaxis funcional más expresiva y cercana a la idea de “funciones como valores”.



## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

Recibir una función como parámetro es una aplicación directa de la idea de **funciones como ciudadanos de primera clase**, característica esencial del paradigma funcional. En este enfoque, un método no solo recibe datos, sino también comportamiento, lo que permite desacoplar la lógica de transformación de la lógica de control. El método `transformar` se limita a coordinar la operación, delegando el trabajo concreto a la función recibida, lo que aumenta la reutilización y flexibilidad del código.

En **JavaScript**, esto se implementa de forma natural, ya que las funciones pueden pasarse directamente como argumentos. El método `transformar` recibe una cadena y una función transformadora, y la invoca desde su interior. La lambda `aMayusculas` se mantiene como una variable local, del mismo modo que en los ejemplos anteriores, y se pasa como parámetro sin necesidad de tipos explícitos.

```javascript
function transformar(cadena, funcionTransformadora) {
    return funcionTransformadora(cadena);
}

let aMayusculas = (cadena) => cadena.toUpperCase();

let texto = "Programación funcional en JavaScript";
let resultado = transformar(texto, aMayusculas);
console.log(resultado);
```

En **Java**, el mismo patrón se expresa utilizando interfaces funcionales. El método `transformar` recibe un `String` y una referencia de tipo `Function<String, String>`, que representa la operación transformadora. Desde el interior del método, la función se invoca mediante `apply`, manteniendo una separación clara entre la definición del comportamiento y su uso, pero con verificación estática de tipos propia del lenguaje.

```java
import java.util.function.Function;

public class EjemploTransformar {

    public static String transformar(String texto, Function<String, String> funcionTransformadora) {
        return funcionTransformadora.apply(texto);
    }

    public static void main(String[] args) {
        Function<String, String> aMayusculas =
            cadena -> cadena.toUpperCase();

        String texto = "Programación funcional en Java";
        String resultado = transformar(texto, aMayusculas);

        System.out.println(resultado);
    }
}
```

Este esquema es conceptualmente similar al uso de punteros a función en C, pero resulta más expresivo y seguro. Además, sienta las bases para técnicas funcionales más avanzadas, como el encadenamiento de transformaciones o el uso de APIs basadas en operaciones, muy habituales en Java moderno.



## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

Invocar una función directamente como **lambda inline** al llamar a otro método refuerza la idea de que el comportamiento puede definirse en el propio punto de uso. En este caso, no se asigna la función a una variable intermedia, sino que se crea justo cuando se pasa como argumento a `transformar`. Esto resulta habitual en programación funcional cuando la operación es específica de una llamada concreta y no se reutiliza en otros lugares del programa.

En **JavaScript**, esta forma de trabajar es especialmente natural. El método `transformar` recibe la cadena y una función, y en la llamada se define una lambda que invierte el texto. La inversión puede realizarse dividiendo la cadena en un array de caracteres, invirtiendo su orden y volviendo a unirlos. La lambda queda definida únicamente en el contexto de esa llamada.

```javascript
function transformar(cadena, funcionTransformadora) {
    return funcionTransformadora(cadena);
}

let texto = "JavaScript funcional";

let resultado = transformar(texto, (cadena) => {
    return cadena.split("").reverse().join("");
});

console.log(resultado);
```

En **Java**, el mismo concepto se aplica utilizando una interfaz funcional. El método `transformar` recibe un `Function<String, String>`, y en la llamada se pasa directamente una expresión lambda que invierte la cadena usando la clase `StringBuilder`. La función no tiene nombre explícito y solo existe durante la evaluación de la llamada, lo que enfatiza su carácter de comportamiento puntual.

```java
import java.util.function.Function;

public class EjemploLambdaInline {

    public static String transformar(String texto, Function<String, String> funcionTransformadora) {
        return funcionTransformadora.apply(texto);
    }

    public static void main(String[] args) {
        String texto = "Java funcional";

        String resultado = transformar(texto,
            cadena -> new StringBuilder(cadena).reverse().toString()
        );

        System.out.println(resultado);
    }
}
```

Este estilo favorece un código más declarativo y compacto, ya que la lógica se expresa exactamente donde se necesita. Además, muestra con claridad la transición desde esquemas imperativos basados en llamadas fijas hacia un enfoque donde las operaciones se combinan dinámicamente mediante funciones pasadas como parámetros.



## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

En el contexto de las funciones lambda, un **cierre** o *closure* se produce cuando una función es capaz de **acceder y capturar variables definidas en el entorno donde fue creada**, incluso cuando se ejecuta en un momento posterior. Estas variables forman parte del contexto léxico de la lambda y se mantienen disponibles mientras la función exista. El concepto es fundamental en programación funcional, ya que permite combinar datos y comportamiento sin necesidad de encapsularlos explícitamente en un objeto, como sería habitual en un enfoque puramente orientado a objetos.

En **Java**, las lambdas pueden acceder a variables locales del método que las contiene, siempre que dichas variables sean **finales o efectivamente finales**. Esto significa que la variable no puede modificarse después de su inicialización. Esta restricción garantiza seguridad y coherencia en un modelo de ejecución que puede implicar concurrencia o ejecución diferida. A diferencia de C, donde la gestión de contexto depende de la pila y la memoria, Java gestiona estos cierres de forma segura mediante el sistema de tipos y el recolector de basura.

Partiendo del ejemplo anterior, puede definirse una variable local fuera de la lambda y crear una nueva función transformadora que concatene dicho valor a la cadena de entrada. La lambda “captura” la variable externa y la utiliza dentro de su cuerpo, ilustrando claramente el comportamiento de un cierre en Java.

```java
import java.util.function.Function;

public class EjemploClosure {

    public static String transformar(String texto, Function<String, String> funcionTransformadora) {
        return funcionTransformadora.apply(texto);
    }

    public static void main(String[] args) {
        String sufijo = " - programación funcional";

        String resultado = transformar("Java",
            cadena -> cadena + sufijo
        );

        System.out.println(resultado);
    }
}
```

Este ejemplo muestra que la lambda puede acceder a `sufijo` aunque no sea un parámetro explícito. De este modo, el cierre permite construir funciones más expresivas y contextuales, acercando Java a técnicas habituales en lenguajes funcionales, pero manteniendo las garantías de seguridad y claridad propias del lenguaje.



## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

Una **función lambda** y un **puntero a función en C** comparten la idea básica de permitir tratar una función como un valor y llamar a una operación de forma indirecta. Sin embargo, la diferencia fundamental radica en el **nivel de abstracción** y en el **modelo de programación** que las rodea. Un puntero a función en C es esencialmente una dirección de memoria que apunta a código ejecutable, y su uso está fuertemente ligado a una visión de bajo nivel, cercana a cómo el programa se representa en memoria.

Las funciones lambda, en cambio, son una construcción de **alto nivel**, integrada en el sistema de tipos del lenguaje. En Java o JavaScript no se trabaja con direcciones de memoria, sino con referencias seguras que representan comportamiento. Además, una lambda siempre está asociada a un tipo funcional bien definido (como `Function<T,R>` en Java), lo que permite comprobaciones estáticas de tipos, algo que en C es más frágil y depende estrictamente de que la firma del puntero coincida manualmente con la de la función apuntada.

Otra diferencia clave es la existencia de **cierres (closures)**. Una lambda puede capturar variables del entorno donde fue definida y utilizarlas más adelante, manteniendo ese contexto vivo. En C, un puntero a función no captura ningún contexto por sí mismo; únicamente apunta a código. Si se desea comportamiento dependiente de datos externos, es necesario pasar explícitamente esos datos como parámetros o recurrir a estructuras adicionales, lo que aumenta la complejidad y el acoplamiento.

Por último, desde el punto de vista del diseño del software, las lambdas fomentan un estilo más **declarativo y expresivo**, donde el énfasis está en describir transformaciones y comportamientos. Los punteros a función, aunque potentes, se emplean de forma más mecánica y están orientados a resolver problemas concretos como callbacks o tablas de funciones. Así, puede decirse que las lambdas generalizan y amplían la idea de los punteros a función, integrándola plenamente en paradigmas modernos como el funcional y el orientado a objetos.



## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

Devolver funciones es una consecuencia directa de tratar las funciones como **ciudadanos de primera clase**. En este caso, se desea una función `crearDescuento` que no aplique directamente un descuento, sino que **genere y devuelva otra función** capaz de aplicar dicho descuento más adelante. Este patrón es habitual en programación funcional y permite construir comportamientos reutilizables y configurables a partir de parámetros iniciales, en lugar de valores concretos.

En **Java**, esto se expresa devolviendo una interfaz funcional, por ejemplo `Function<Double, Double>`. La función `crearDescuento` recibe únicamente el porcentaje y devuelve una lambda que, cuando se invoque, aplicará ese porcentaje a la cantidad recibida. El porcentaje no se pasa como parámetro a la función descuento, sino que queda capturado desde el contexto donde se creó, lo que constituye un cierre (*closure*).

```java
import java.util.function.Function;

public class EjemploDescuentos {

    public static Function<Double, Double> crearDescuento(double porcentaje) {
        return cantidad -> cantidad - (cantidad * porcentaje / 100);
    }

    public static void main(String[] args) {
        Function<Double, Double> descuento10 = crearDescuento(10);
        Function<Double, Double> descuento25 = crearDescuento(25);

        double precio = 200.0;

        System.out.println(descuento10.apply(precio)); // 180.0
        System.out.println(descuento25.apply(precio)); // 150.0
    }
}
```

En este ejemplo, cada función descuento es un **closure**, ya que la lambda captura la variable `porcentaje` definida en el método `crearDescuento`. Aunque dicho método ya ha terminado su ejecución cuando se aplica el descuento, el valor de `porcentaje` permanece asociado a la lambda. Así, `descuento10` y `descuento25` comparten la misma estructura de código, pero encapsulan contextos distintos, demostrando cómo los cierres permiten combinar datos y comportamiento de forma segura y expresiva en Java.



## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

En **Java**, una **interfaz funcional** es una interfaz que representa un **tipo de función**, es decir, define un único contrato de comportamiento que puede ser implementado mediante una expresión lambda o una referencia a método. Dado que Java es un lenguaje con comprobación estática de tipos, toda función lambda debe tener un tipo bien definido en tiempo de compilación, y ese tipo es precisamente una interfaz funcional. Por este motivo, las lambdas no existen de forma “libre” como en otros lenguajes, sino siempre asociadas a una interfaz concreta.

El **requisito fundamental** de una interfaz funcional es que contenga **exactamente un método abstracto**. Este método es el que define la firma de la función (parámetros y valor de retorno) que la lambda debe implementar. No se consideran métodos abstractos aquellos heredados de `Object`, como `toString`, `equals` o `hashCode`, por lo que pueden declararse sin romper la condición de interfaz funcional. Esta restricción garantiza que el compilador pueda determinar de forma inequívoca qué método está implementando la lambda.

Además, una interfaz funcional **puede contener métodos `default` y métodos `static`**, siempre que no se añadan más métodos abstractos. Estos métodos permiten incluir comportamiento común o utilidades asociadas al tipo funcional sin afectar a su uso con lambdas. De forma opcional, puede anotarse la interfaz con `@FunctionalInterface`, lo que no es obligatorio, pero sí recomendable, ya que permite al compilador verificar que la interfaz cumple realmente los requisitos y protege frente a errores de diseño al añadir nuevos métodos abstractos accidentalmente.

En conjunto, las interfaces funcionales actúan como el puente entre el sistema de tipos estático de Java y el paradigma funcional, permitiendo que las funciones sean tratadas como valores sin abandonar las garantías de seguridad y claridad propias del lenguaje.



## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

Una **interfaz funcional definida a mano** permite describir explícitamente el tipo de una función que se desea utilizar en el programa. En este caso, se busca representar una operación que **recibe una cadena de texto y devuelve otra**, exactamente el mismo rol que anteriormente cumplía `Function<String, String>`, pero con un nombre semánticamente más significativo. Esto mejora la legibilidad del código y comunica mejor la intención del diseño.

Para que una interfaz pueda considerarse funcional en Java, debe cumplir el requisito de **definir un único método abstracto**. Dicho método establece la firma que cualquier lambda asociada a esta interfaz debe implementar. De manera opcional, puede utilizarse la anotación `@FunctionalInterface`, que no cambia el comportamiento en tiempo de ejecución, pero sí permite al compilador verificar que la interfaz mantiene la propiedad funcional.

A continuación se muestra la definición de la interfaz funcional `Transformador`, que declara un método encargado de convertir una cadena de texto en otra. Este tipo puede utilizarse posteriormente como tipo de referencia para funciones lambda, del mismo modo que se hizo con `Function<String, String>`, pero con una interfaz personalizada al dominio del problema.

```java
@FunctionalInterface
public interface Transformador {
    String transformar(String texto);
}
```

Con esta definición, cualquier función lambda que reciba un `String` y devuelva un `String` será compatible con `Transformador`. Esto refuerza la integración del paradigma funcional dentro de Java, manteniendo un control explícito de los tipos y alineando el diseño con principios de claridad y abstracción propios de la programación orientada a objetos.



## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

Puede definirse una **interfaz funcional genérica** utilizando *generics* para desacoplar el tipo de entrada del tipo de salida. De este modo, el mismo concepto de transformador puede reutilizarse para convertir valores entre distintos tipos, no solo cadenas de texto. Este enfoque es coherente con los conocimientos previos de genericidad en Java y permite expresar de forma clara operaciones de transformación entre dominios diferentes.

Una versión genérica de la interfaz `Transformador` debe declarar dos parámetros de tipo: uno para el tipo de entrada y otro para el tipo de salida. El requisito de interfaz funcional se mantiene, por lo que solo debe existir un método abstracto. La anotación `@FunctionalInterface` sigue siendo válida y recomendable, ya que ayuda a preservar la intención funcional de la interfaz incluso al evolucionar el código.

```java
@FunctionalInterface
public interface Transformador<T, R> {
    R transformar(T valor);
}
```

A partir de esta definición, puede crearse un transformador concreto que convierta un `Double` en un `Integer`. En el siguiente ejemplo, se define una función lambda que redondea un número decimal y devuelve su valor entero, mostrando cómo la interfaz genérica permite expresar claramente el cambio de tipo sin necesidad de casting explícito.

```java
public class EjemploGenerics {

    public static void main(String[] args) {
        Transformador<Double, Integer> redondear =
            valor -> (int) Math.round(valor);

        Double numero = 3.6;
        Integer resultado = redondear.transformar(numero);

        System.out.println(resultado); // 4
    }
}
```

Este diseño refuerza la flexibilidad del paradigma funcional en Java, ya que permite definir transformaciones reutilizables y tipadas de forma segura. Al mismo tiempo, se integra sin fricciones con la orientación a objetos y la genericidad, facilitando la creación de APIs más expresivas y alineadas con el dominio del problema.



## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

En Java existen **interfaces funcionales predefinidas**, principalmente en el paquete `java.util.function`, cuyo objetivo es cubrir los patrones más comunes de uso de funciones sin necesidad de definir interfaces propias como `Transformador`. Estas interfaces representan operaciones habituales como transformaciones, predicados, consumo de valores o producción de resultados, y están pensadas para trabajar de forma natural con expresiones lambda y referencias a métodos. El hecho de que `Transformador<T, R>` coincida conceptualmente con `Function<T, R>` demuestra precisamente el propósito de este paquete.

La interfaz más general es **`Function<T, R>`**, que representa una función que recibe un valor de tipo `T` y devuelve otro de tipo `R`. Junto a ella aparecen especializaciones muy habituales:

*   **`Predicate<T>`**, que recibe un `T` y devuelve un `boolean`, típica para condiciones o filtrados.
*   **`Consumer<T>`**, que recibe un `T` pero no devuelve resultado, usada para operaciones con efectos secundarios (por ejemplo, imprimir).
*   **`Supplier<T>`**, que no recibe parámetros y devuelve un `T`, utilizada para generación de valores.

Además, Java proporciona **variantes especializadas para tipos primitivos** con el fin de evitar el *autoboxing* y mejorar el rendimiento. Algunos ejemplos representativos son `IntFunction<R>`, `ToIntFunction<T>`, `IntConsumer`, `DoublePredicate` o `LongSupplier`. Estas interfaces cumplen el mismo rol conceptual que sus equivalentes genéricos, pero trabajan directamente con tipos primitivos como `int`, `double` o `long`.

En conjunto, estas interfaces funcionales predefinidas permiten expresar la gran mayoría de necesidades funcionales sin definir nuevos tipos, fomentando un código más estándar, interoperable y reconocible. No obstante, definir interfaces funcionales propias sigue siendo útil cuando se desea **expresar semántica de dominio**, como ocurre con `Transformador`, aunque técnicamente sea equivalente a `Function<T, R>`.



## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

El método **`forEach`** de la interfaz `List` representa una forma funcional de recorrer colecciones en Java, introducida a partir de Java 8. En lugar de controlar explícitamente el índice y la iteración, como ocurre con el bucle `for` tradicional, se delega el recorrido al propio objeto colección y se indica **qué operación debe ejecutarse para cada elemento**. Este enfoque es más declarativo y está alineado con el paradigma funcional, ya que se expresa el comportamiento a aplicar y no los pasos del algoritmo.

Desde el punto de vista del tipado, `forEach` recibe un **`Consumer<T>`**, que es una interfaz funcional que acepta un parámetro y no devuelve ningún resultado. Esto encaja bien con tareas como mostrar mensajes, modificar estados externos o realizar operaciones de salida. En comparación con C o con Java imperativo clásico, se elimina la gestión explícita del contador y de los límites de la colección, reduciendo el código repetitivo y el riesgo de errores.

En el siguiente ejemplo se recorre una lista de `Integer` y se muestra un mensaje únicamente cuando el valor es positivo. La condición se evalúa dentro de la función lambda pasada a `forEach`, lo que concentra la lógica de decisión en un único punto y hace el código más expresivo.

```java
import java.util.List;
import java.util.Arrays;

public class EjemploForEach {

    public static void main(String[] args) {
        List<Integer> numeros = Arrays.asList(-3, 5, 0, 12, -7);

        numeros.forEach(n -> {
            if (n > 0) {
                System.out.println("Número positivo: " + n);
            }
        });
    }
}
```

Este uso de `forEach` no solo simplifica la sintaxis, sino que prepara el código para un estilo más funcional y composable, similar al que se emplea posteriormente con *streams*. Así, se evoluciona desde bucles imperativos hacia operaciones centradas en el “qué hacer” con los datos, en lugar de en el “cómo recorrerlos”.


## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

En la firma de `List.forEach`, se utiliza **`Consumer<? super T>`** en lugar de `Consumer<T>` por una cuestión de **flexibilidad de tipos** en el uso de genéricos. Un `Consumer` es un tipo que *consume* valores, es decir, **recibe** objetos de tipo `T` pero no devuelve ninguno. Permitir `? super T` significa que se puede pasar no solo un consumidor de `T`, sino también un consumidor de cualquier **supertipo** de `T`, como `Object`. Esto encaja con la idea de que, si un consumidor sabe manejar un tipo más general, también es capaz de manejar instancias de un subtipo sin problemas.

Esta regla se resume en el principio conocido como **PECS**: *Producer Extends, Consumer Super*. PECS indica que, cuando un tipo genérico **produce** valores (los devuelve), debe usarse `? extends T`, y cuando **consume** valores (los recibe como parámetros), debe usarse `? super T`. En el caso de `forEach`, la colección produce elementos de tipo `T`, pero la función pasada los consume; por tanto, el parámetro correcto es `Consumer<? super T>`. Esto mejora la reutilización del código sin comprometer la seguridad de tipos.

Este mismo razonamiento puede aplicarse al método `transformar`. Si se define como `transformar(T valor, Function<T, R> f)`, se está siendo más restrictivo de lo necesario. Siguiendo PECS, `Function` **consume** un `T` (entrada) y **produce** un `R` (salida), por lo que una firma más flexible sería `Function<? super T, ? extends R>`. De este modo, se permite pasar funciones que acepten un supertipo de `T` y devuelvan un subtipo de `R`, ampliando los casos válidos sin perder seguridad.

En conclusión, el uso de `Consumer<? super T>` en `forEach` no es accidental, sino una aplicación cuidadosa de PECS para maximizar la expresividad del sistema de tipos. Comprender este principio permite diseñar métodos genéricos, como `transformar`, que sean más reutilizables, robustos y alineados con las mejores prácticas de Java en programación funcional y genérica.


## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

Las **referencias a métodos** permiten tratar un método existente como un valor reutilizable, de forma similar a una función lambda, pero sin definir el cuerpo de la función explícitamente. En lugar de escribir una lambda que invoque a un método, se puede obtener directamente una referencia a ese método y almacenarla en una variable. Esta técnica mejora la legibilidad y refuerza la idea de reutilización de comportamiento, especialmente cuando el método ya existe y su intención es clara.

En **JavaScript**, los métodos de los objetos son funciones y pueden referenciarse directamente. Sin embargo, debe tenerse en cuenta el valor de `this`, ya que al extraer un método puede perderse el contexto del objeto. En el ejemplo siguiente, se obtiene una referencia al método `saludar` de una instancia de `Persona` y se invoca posteriormente, asegurando que el contexto se mantiene correctamente mediante `bind`.

```javascript
class Persona {
    constructor(nombre) {
        this.nombre = nombre;
    }

    saludar() {
        console.log(`Hola, soy ${this.nombre}`);
    }
}

// Código principal
const persona = new Persona("Iago");

// Referencia al método saludar, ligada al objeto
const saludoRef = persona.saludar.bind(persona);

// Invocación mediante la referencia
saludoRef();
```

En **Java**, las referencias a métodos están integradas en el lenguaje desde Java 8 y se expresan con el operador `::`. En el caso de métodos de instancia, la referencia se obtiene a partir de un objeto concreto. El método referenciado debe ser compatible con la interfaz funcional a la que se asigna, lo que permite invocarlo posteriormente de forma segura y tipada.

```java
import java.util.function.Consumer;

class Persona {
    private String nombre;

    public Persona(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}

public class EjemploReferenciaMetodo {
    public static void main(String[] args) {
        Persona persona = new Persona("Iago");

        // Referencia al método de instancia
        Consumer<Void> saludoRef = v -> persona.saludar();
        // Alternativa directa con referencia a método
        Runnable saludoRef2 = persona::saludar;

        // Invocación mediante la referencia
        saludoRef2.run();
    }
}
```

Estos ejemplos muestran cómo las referencias a métodos permiten separar la definición del comportamiento de su ejecución, de una forma más declarativa que las llamadas directas. Tanto en JavaScript como en Java, este enfoque encaja de forma natural con el paradigma funcional y complementa el uso de funciones lambda cuando el comportamiento ya está implementado en un método existente.



## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

En **Java** existen varios **tipos de referencias a método**, introducidas a partir de Java 8, que permiten reutilizar métodos existentes como implementación de una interfaz funcional sin necesidad de escribir una expresión lambda explícita. Todas las referencias a método comparten la sintaxis `::` y se utilizan cuando la firma del método referenciado coincide con la firma del método abstracto de la interfaz funcional destino. Esto refuerza la legibilidad y deja más clara la intención del código.

El primer tipo es la **referencia a un método estático**, que se obtiene a partir del nombre de la clase. En este caso, el método no depende de ninguna instancia concreta. Este tipo de referencia suele emplearse cuando la operación es puramente funcional y no necesita estado de objeto.

```java
import java.util.function.Function;

class Utilidades {
    public static String aMayusculas(String texto) {
        return texto.toUpperCase();
    }
}

Function<String, String> refMetodoEstatico = Utilidades::aMayusculas;
System.out.println(refMetodoEstatico.apply("hola"));
```

El segundo tipo es la **referencia a un constructor**, que permite tratar la creación de objetos como una función. Es especialmente útil en combinación con interfaces como `Supplier` o `Function`, y encaja bien con un estilo declarativo de construcción de objetos.

```java
import java.util.function.Function;

class Persona {
    private String nombre;

    public Persona(String nombre) {
        this.nombre = nombre;
    }
}

Function<String, Persona> refConstructor = Persona::new;
Persona p = refConstructor.apply("Iago");
```

Existen además dos tipos relacionados con métodos de instancia. Por un lado, la **referencia a un método de una instancia concreta**, donde el método está ligado a un objeto específico. Por otro, la **referencia a un método de instancia sobre cualquier instancia de una clase**, donde el objeto sobre el que se ejecuta el método se recibe implícitamente como primer parámetro.

```java
import java.util.function.Consumer;
import java.util.function.Function;

Persona persona = new Persona("Iago");

/* Método de instancia de una instancia concreta */
Runnable refInstanciaConcreta = persona::saludar;
refInstanciaConcreta.run();

/* Método de instancia sobre cualquier instancia */
Function<String, Integer> refCualquierInstancia = String::length;
System.out.println(refCualquierInstancia.apply("Java"));
```

Estos cuatro tipos cubren todos los casos habituales de referencias a método en Java. Su uso permite escribir código más conciso y expresivo, eliminando lambdas innecesarias cuando ya existe un método adecuado, y reforzando la integración del paradigma funcional con la orientación a objetos del lenguaje.



## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

En Java, la ordenación de colecciones es un ejemplo muy representativo del uso del **paradigma funcional**, ya que el criterio de ordenación se expresa como una función que se pasa como parámetro. El método `Collections.sort` recibe una lista y un **comparador**, que conceptualmente es una función que compara dos elementos y devuelve un entero. Con la llegada de las expresiones lambda, este comparador puede definirse de forma inline, sin necesidad de crear clases auxiliares, haciendo el código más conciso y expresivo.

En una **primera versión**, la función de comparación se implementa manualmente dentro de una expresión lambda, evaluando primero la edad y, en caso de empate, el nombre en orden alfabético. Esta aproximación es directa y ayuda a entender claramente cómo funciona el criterio de ordenación, siendo especialmente útil para quien ya conoce comparaciones imperativas en C o Java clásico.

```java
import java.util.*;

class Persona {
    private String nombre;
    private int edad;

    public Persona(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }

    public String getNombre() { return nombre; }
    public int getEdad() { return edad; }

    @Override
    public String toString() {
        return nombre + " (" + edad + ")";
    }
}

public class EjemploOrdenacionManual {

    public static void main(String[] args) {
        List<Persona> personas = Arrays.asList(
            new Persona("Ana", 30),
            new Persona("Luis", 25),
            new Persona("Carlos", 30),
            new Persona("Bea", 25)
        );

        Collections.sort(personas, (p1, p2) -> {
            if (p1.getEdad() != p2.getEdad()) {
                return Integer.compare(p1.getEdad(), p2.getEdad());
            }
            return p1.getNombre().compareTo(p2.getNombre());
        });

        personas.forEach(System.out::println);
    }
}
```

En una **segunda versión**, se emplean las utilidades de la clase `Comparator`, que permiten construir comparadores de forma declarativa mediante métodos como `comparing` y `thenComparing`. Este enfoque reduce el código repetitivo y deja más clara la intención: primero ordenar por edad y luego por nombre. Además, favorece la reutilización y el encadenamiento de criterios, lo que encaja plenamente con el estilo funcional moderno de Java.

```java
import java.util.*;

public class EjemploOrdenacionComparator {

    public static void main(String[] args) {
        List<Persona> personas = Arrays.asList(
            new Persona("Ana", 30),
            new Persona("Luis", 25),
            new Persona("Carlos", 30),
            new Persona("Bea", 25)
        );

        Collections.sort(
            personas,
            Comparator.comparing(Persona::getEdad)
                      .thenComparing(Persona::getNombre)
        );

        personas.forEach(System.out::println);
    }
}
```

Ambas versiones producen el mismo resultado, pero la segunda muestra de forma más clara cómo Java combina orientación a objetos y programación funcional. El criterio de ordenación se expresa como una composición de funciones, lo que mejora la legibilidad y facilita la evolución del código frente a enfoques más imperativos.

