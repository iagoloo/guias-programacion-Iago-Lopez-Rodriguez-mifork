<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Clases y Objetos". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: ninguno.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 1. Clases y objetos

## 1. ¿Cuáles son las cuatro características básicas de la programación orientada a objetos? Describe brevemente cada una

La programación orientada a objetos se fundamenta en cuatro características esenciales: **encapsulamiento**, **abstracción**, **herencia** y **polimorfismo**. El **encapsulamiento** consiste en agrupar los datos y las funciones que operan sobre ellos dentro de una misma unidad llamada clase. Este mecanismo permite controlar qué partes del estado interno son accesibles desde fuera, algo que en Java se gestiona mediante modificadores como `private`, `public` o `protected`. Desde la perspectiva de C, funciona como una forma más estricta y automática de proteger los datos frente a accesos indebidos.

La **abstracción** permite centrarse únicamente en las características esenciales de un objeto, ocultando los detalles internos que no son relevantes para quien lo utiliza. En Java, esto se consigue definiendo clases que solo exponen los métodos necesarios, mientras que la implementación queda oculta. Es comparable a cómo en C se usan funciones sin necesidad de conocer el código exacto de su interior, pero llevado a un nivel más sistemático.

La **herencia** posibilita crear nuevas clases basadas en otras ya existentes, reutilizando atributos y métodos y permitiendo extender o modificar su comportamiento. En Java, este mecanismo se implementa mediante la palabra clave `extends`. Aunque en C no existe herencia como tal, podría aproximarse a reutilizar estructuras y funciones, pero con un mecanismo mucho más integrado que favorece la organización y la reutilización del código.

Por último, el **polimorfismo** permite que un mismo método pueda comportarse de forma diferente según el tipo del objeto que lo invoque. Esto da lugar a programas más flexibles, capaces de trabajar con objetos distintos que comparten una estructura común. En Java, el polimorfismo se apoya en la herencia y en la sobrescritura de métodos, lo que facilita la ampliación del programa sin necesidad de modificar el código existente.


## 2. Cita cuatro lenguajes populares que permitan la programación orientada a objetos

 Java es un lenguaje muy utilizado que obliga a trabajar con clases, lo que facilita aprender orientación a objetos desde el inicio. C++ también soporta este paradigma, combinándolo con la programación estructurada y ofreciendo gran control sobre el sistema. Python adopta la orientación a objetos de manera flexible, haciendo que prácticamente todo sea un objeto y facilitando su uso en múltiples áreas. Por último, C# implementa un modelo muy similar al de Java y se utiliza en aplicaciones de escritorio, servicios en la nube y videojuegos gracias a su claridad y robustez.



## 3. Los paradigmas anteriores a la POO, ¿Qué es la **programación estructurada**? y, todavía mejor, ¿Qué es la **programación modular**?

La **programación estructurada** se basa en dividir un programa en bloques lógicos que siguen un flujo claro y predecible, utilizando únicamente estructuras de control como secuencias, condicionales y bucles. Este enfoque surge como respuesta al uso excesivo de instrucciones `goto` en lenguajes antiguos, que provocaban código difícil de seguir y mantener. Al imponer una estructura coherente, se facilita la lectura del programa y se reduce la posibilidad de errores lógicos derivados de saltos incontrolados.

La **programación modular** representa un paso más allá, ya que propone dividir el programa en módulos independientes, cada uno encargado de una tarea concreta. Estos módulos pueden verse como piezas autocontenidas que reciben datos, los procesan y devuelven resultados, lo que favorece tanto la reutilización del código como su mantenimiento. En esencia, se trata de separar responsabilidades, evitando que todo el programa esté concentrado en un único bloque, como suele ocurrir en desarrollos totalmente estructurados.

Este enfoque modular también permite trabajar en equipos con mayor eficiencia, ya que cada persona puede encargarse de uno de los módulos sin interferir en los demás. Además, facilita la detección de errores, pues cada componente puede probarse de forma aislada antes de integrarlo en el conjunto del programa. Aunque no utiliza conceptos como clases u objetos, la modularidad es una base importante que conecta con las ideas de encapsulamiento y organización que se verán en la programación orientada a objetos.

En resumen, mientras la programación estructurada organiza el flujo del programa, la programación modular organiza su arquitectura interna. Ambos paradigmas proporcionan una base sólida para comprender la transición hacia la POO, donde estas ideas se integran y se amplían mediante clases, objetos y mecanismos adicionales que permiten construir programas más robustos y extensibles.

## 4. ¿Qué tres elementos definen a un objeto en programación orientada a objetos?

Un objeto en programación orientada a objetos se define por **tres elementos fundamentales: atributos, métodos y estado**. Los **atributos** representan las características o datos que describen al objeto; pueden compararse con las variables dentro de una estructura en C, aunque en Java están encapsulados dentro de una clase. Estos atributos permiten que cada objeto tenga información propia, como por ejemplo la velocidad o el color en un objeto que represente un coche.

Los **métodos** son las operaciones o comportamientos que el objeto puede realizar. Funcionan como funciones asociadas directamente a la clase y permiten modificar o consultar los atributos que definen al objeto. Este enfoque ofrece una forma más organizada de agrupar datos y acciones, en contraste con C, donde las funciones y los datos suelen estar separados.

El **estado** de un objeto se refiere a los valores concretos que tienen sus atributos en un momento determinado. Aunque la clase define qué atributos existen, cada objeto mantiene su propio estado independiente del resto. Este concepto es clave para entender cómo múltiples objetos pueden comportarse de la misma manera pero contener información distinta, algo que resulta difícil de representar de forma tan clara en la programación estructurada clásica.

## 5. ¿Qué es una clase? ¿Es lo mismo que un objeto? ¿Qué es una instancia? ¿Todos los lenguajes orientados a objetos manejan el concepto de clase?

Una clase puede entenderse como una plantilla que define qué atributos y métodos tendrán los objetos creados a partir de ella. Actúa como un molde que describe la estructura y el comportamiento común de un conjunto de entidades. En lenguajes como Java, prácticamente todo el código debe estar dentro de una clase, lo que obliga a organizar el programa siguiendo este modelo. Esta estructura permite reunir en un mismo lugar tanto los datos como las operaciones que se realizan sobre ellos, facilitando la programación modular y el encapsulamiento.

Un objeto, en cambio, es una **instancia** concreta de una clase. Mientras que la clase es solo la definición, la instancia representa un ejemplar real que ocupa memoria y tiene valores propios en sus atributos. Así, múltiples objetos pueden derivar de una misma clase, compartiendo su forma general pero manteniendo estados independientes. El proceso de crear un objeto a partir de su clase se denomina **instanciación**, y es lo que permite trabajar con entidades individuales dentro del programa.

Aunque muchos lenguajes orientados a objetos utilizan clases como base de su sistema, no todos funcionan estrictamente con este concepto. Java o C# dependen completamente de las clases, pero existen lenguajes como JavaScript o Python que permiten orientación a objetos sin requerir clases tradicionales. JavaScript, por ejemplo, usa un modelo basado en prototipos, aunque versiones modernas introducen la sintaxis `class` como una abstracción. Esto demuestra que la orientación a objetos no depende exclusivamente del concepto de clase, sino de la capacidad de trabajar con objetos que combinan estado y comportamiento.


## 6. ¿Dónde se almacenan en memoria los objetos? ¿Es igual en todos los lenguajes? ¿Qué es la **recolección de basura**? 

Los objetos se almacenan normalmente en una zona de memoria denominada **heap**, que es el área destinada a la creación dinámica de datos. En Java, cada vez que se usa la palabra clave `new`, el objeto resultante se aloja en el heap, mientras que las variables que hacen referencia a ese objeto suelen almacenarse en la pila de llamadas (stack). Esta separación permite que varios lugares del programa puedan referirse al mismo objeto en memoria y que su ciclo de vida no dependa de un único ámbito local, a diferencia de lo que ocurre con variables automáticas en C.

No todos los lenguajes orientados a objetos gestionan la memoria de la misma manera. En C++, por ejemplo, los objetos pueden almacenarse en la pila, en el espacio estático o en el heap, dependiendo de cómo se declaren. Esto otorga flexibilidad, pero también responsabilidad al programador, ya que debe encargarse de liberar la memoria cuando deja de ser necesaria. En contraposición, lenguajes como Java o Python asignan siempre los objetos en el heap y confían en un sistema automático de gestión que evita errores típicos como fugas de memoria o usos después de liberar.

La **recolección de basura** es un mecanismo que libera automáticamente la memoria de los objetos que ya no están siendo utilizados por el programa. El sistema detecta cuando un objeto no tiene más referencias activas y lo marca para su eliminación, recuperando así el espacio que ocupaba en el heap. Este proceso permite que el programador no tenga que preocuparse por liberar la memoria manualmente, reduciendo la posibilidad de errores de gestión.

Este enfoque aporta seguridad y sencillez al desarrollo en lenguajes como Java, ya que elimina la necesidad de emplear funciones manuales de liberación como `free()` o `delete` en C y C++. Sin embargo, también implica que el programador no tiene control directo sobre el momento exacto en el que la memoria será liberada, ya que depende del funcionamiento interno del recolector. Aun así, la recolección de basura es un componente fundamental para garantizar la estabilidad y evitar errores relacionados con el manejo incorrecto de la memoria.


## 7. ¿Qué es un método? ¿Qué es la **sobrecarga de métodos**? 

Un **método** es una función definida dentro de una clase que describe una acción o comportamiento asociado a los objetos creados a partir de ella. Su función es operar sobre los atributos del objeto o realizar tareas relacionadas con él, actuando como la forma principal de interactuar con su estado interno. En lenguajes como Java, los métodos permiten establecer una clara organización entre datos y operaciones, algo que en C suele aparecer separado entre estructuras y funciones. Además, los métodos pueden ser llamados por distintas instancias, lo que facilita reutilizar el comportamiento definido en la clase.

La **sobrecarga de métodos** consiste en definir varios métodos con el mismo nombre dentro de una clase, pero con listas de parámetros distintas. Esto permite adaptar una misma acción a diferentes tipos o cantidades de datos, ofreciendo una interfaz unificada al usuario del código. Java selecciona automáticamente qué versión del método debe ejecutarse según los argumentos que se pasen en la llamada, lo que permite escribir código más claro y flexible. Este mecanismo no existe de forma directa en C, donde funciones con el mismo nombre no pueden coexistir, lo que resalta la utilidad organizativa que aporta la orientación a objetos.

La sobrecarga resulta especialmente útil para simplificar operaciones que conceptualmente representan la misma acción, pero requieren variaciones según el contexto. Por ejemplo, un método que calcule un área podría sobrecargarse para aceptar diferentes combinaciones de parámetros según la figura geométrica. De este modo, el programa mantiene coherencia semántica sin obligar a usar nombres distintos para cada variante, mejorando tanto la legibilidad como la mantenibilidad del código.


## 8. Ejemplo mínimo de clase en Java, que se llame Punto, con dos atributos, x e y, con un método que se llame `calculaDistanciaAOrigen`, que calcule la distancia a la posición 0,0. Por sencillez, los atributos deben tener visibilidad por defecto. Crea además un ejemplo de uso con una instancia y uso del método

// Archivo: Punto.java
class Punto {
    double x; // visibilidad por defecto (package-private)
    double y; // visibilidad por defecto (package-private)

    double calculaDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
}
// Archivo: Main.java
public class Main {
    public static void main(String[] args) {
        Punto p = new Punto();
        p.x = 3;
        p.y = 4;

        double d = p.calculaDistanciaAOrigen();
        System.out.println("Distancia al origen: " + d); // Imprime: 5.0
    }
}

## 9. ¿Cuál es el punto de entrada en un programa en Java? ¿Qué es `static` y para qué vale? ¿Sólo se emplea para ese método `main`? ¿Para qué se combina con `final`?

El punto de entrada de un programa en Java es el método **`main`**, cuya firma debe ser exactamente `public static void main(String[] args)`. Es el método que la máquina virtual de Java invoca automáticamente al iniciar la ejecución, actuando como la puerta de entrada del programa. Aunque la aplicación pueda contener muchas clases y métodos, la ejecución siempre comienza por esta función concreta, lo que establece un orden claro y definido para iniciar el flujo del programa.

La palabra clave **`static`** indica que un método o atributo pertenece a la clase en sí y no a una instancia particular. Esto permite llamar al método `main` sin necesidad de crear un objeto previamente, algo esencial dado que aún no existe ninguna instancia cuando se inicia el programa. Este concepto no aparece en lenguajes estructurados como C, pero se aproxima a la idea de funciones globales, aunque Java evita este modelo para mantener su orientación estricta a objetos. Además, `static` puede aplicarse a otros métodos o atributos cuando se desea que compartan un mismo valor o comportamiento para todas las instancias.

El uso de `static` no está limitado al método `main`; se emplea también para utilidades generales, constantes compartidas y métodos auxiliares que no dependen del estado particular de un objeto. Por ejemplo, la clase `Math` de Java contiene métodos estáticos como `Math.sqrt()`, facilitando su uso sin necesidad de crear instancias. Sin embargo, abusar de elementos estáticos puede ir en contra de algunos principios de la orientación a objetos, por lo que debe utilizarse con criterio.

Cuando `static` se combina con **`final`**, se obtiene una constante: un atributo que pertenece a la clase y cuyo valor no puede modificarse después de inicializarse. Esta combinación se utiliza para definir valores fijos que deben ser accesibles globalmente, como configuraciones, límites o parámetros universales. Un ejemplo típico es `public static final double PI = 3.14159;`, que permite definir una constante clara y segura, evitando cambios accidentales durante la ejecución del programa.

## 10. Intenta ejecutar un poco de Java de forma básica, con los comandos `javac` y `java`. ¿Cómo podemos compilar el programa y ejecutarlo desde linea de comandos? ¿Java es compilado? ¿Qué es la **máquina virtual**? ¿Qué es el *byte-code* y los ficheros `.class`?

Para compilar y ejecutar un programa en Java desde la línea de comandos, se utiliza primero el comando **`javac`**, que transforma el código fuente `.java` en ficheros `.class`, y luego **`java`**, que ejecuta esos ficheros. Si, por ejemplo, se tiene un archivo `Main.java` con un método `main`, puede compilarse con `javac Main.java`, lo cual generará un archivo `Main.class`. Posteriormente, la ejecución se realiza mediante `java Main`, indicando el nombre de la clase sin extensión. Este proceso crea una separación clara entre la fase de compilación y la fase de ejecución, algo característico del funcionamiento del lenguaje Java.

Java se considera un lenguaje **compilado e interpretado**, porque pasa por dos fases distintas. En primer lugar, el compilador `javac` convierte el código fuente en *byte‑code*, que es un formato intermedio independiente de la plataforma. Posteriormente, ese *byte‑code* es ejecutado por la máquina virtual de Java, que se encarga de traducirlo a instrucciones específicas del sistema donde se esté ejecutando. Este enfoque híbrido permite alcanzar un equilibrio entre rendimiento y portabilidad, ya que el código no depende del hardware concreto, a diferencia de lenguajes como C o C++ donde se genera directamente código máquina.

La **máquina virtual de Java** (JVM) es un programa encargado de ejecutar el *byte‑code*. Actúa como una capa intermedia entre el programa y el sistema operativo, proporcionando un entorno controlado que gestiona aspectos como la memoria, la seguridad y la recolección de basura. Gracias a esta máquina virtual, un mismo archivo `.class` puede ejecutarse en sistemas distintos sin necesidad de recompilar, siempre que estos dispongan de una JVM compatible. Esta abstracción es clave para el principio de “escribe una vez, ejecuta en cualquier lugar” que caracteriza a Java.

El **byte‑code** es el conjunto de instrucciones binarias generadas tras compilar un archivo `.java`. Estos ficheros, con extensión `.class`, contienen un código que no es directamente ejecutable por el procesador, pero que la JVM puede interpretar o compilar de forma dinámica mediante técnicas como la compilación JIT (Just‑In‑Time). Esta representación intermedia hace que el código sea portátil, ya que los `.class` no están ligados a una arquitectura específica. De esta forma, el ciclo completo de Java combina portabilidad, seguridad y flexibilidad en la ejecución, esenciales para su uso en entornos muy variados.


## 11. En el código anterior de la clase `Punto` ¿Qué es `new`? ¿Qué es un **constructor**? Pon un ejemplo de constructor en una clase `Empleado` que tenga DNI, nombre y apellidos

En Java, `new` es el operador que **crea** un objeto en memoria (en el *heap*) e **invoca** su constructor para inicializarlo. El resultado de `new` es una **referencia** al objeto recién creado, que se almacena en una variable de tipo de la clase correspondiente. Sin `new` (salvo en casos especiales como *strings* literales o arreglos con sintaxis específica), no se obtiene una instancia real y, por tanto, no es posible acceder a métodos o atributos de objeto.

Un **constructor** es un método especial de la clase, con el **mismo nombre** que la clase y **sin tipo de retorno**, cuya responsabilidad es dejar el objeto en un estado válido al momento de su creación. Si no se define ninguno, el compilador proporciona un **constructor por defecto** sin parámetros; si se define al menos uno, el por defecto deja de generarse automáticamente. Es habitual **sobrecargar** constructores para permitir distintas formas de inicialización (por ejemplo, con o sin parámetros).

A continuación, un ejemplo de clase `Empleado` con un constructor que inicializa **DNI**, **nombre** y **apellidos**:

```java
class Empleado {
    String dni;
    String nombre;
    String apellidos;

    // Constructor que inicializa todos los campos
    Empleado(String dni, String nombre, String apellidos) {
        this.dni = dni;
        this.nombre = nombre;
        this.apellidos = apellidos;
    }
}
```


## 12. ¿Qué es la referencia `this`? ¿Se llama igual en todos los lenguajes? Pon un ejemplo del uso de `this` en la clase `Punto`

La referencia **`this`** en Java alude al **objeto actual** sobre el que se está ejecutando el código. Sirve para acceder a sus atributos y métodos desde dentro de la clase, y resulta especialmente útil para **desambiguar** entre parámetros de método/constructor y campos con el mismo nombre. Además, permite **encadenar constructores** (con `this(...)`) y pasar la referencia del propio objeto a otros métodos o componentes cuando se necesita.

No se denomina igual en todos los lenguajes, aunque el concepto es similar. En **C++**, **C#** y **Java** se llama `this`; en **Python** se utiliza el identificador **`self`** por convención como primer parámetro de los métodos de instancia; en **Visual Basic** existe **`Me`**; y en **JavaScript** también se usa `this`, aunque su **vinculación** depende del contexto de llamada (lo que introduce matices particulares en ese lenguaje).

Ejemplo de uso de `this` en la clase `Punto`, mostrando desambiguación en el constructor y un método que modifica el estado del objeto:

```java
class Punto {
    double x; // visibilidad por defecto (package-private)
    double y; // visibilidad por defecto (package-private)

    // Constructor: desambiguación entre parámetros y campos
    Punto(double x, double y) {
        this.x = x; // 'this.x' es el campo; 'x' es el parámetro
        this.y = y;
    }

    // Método que usa 'this' para acceder a atributos y devolver la propia referencia
    Punto desplazar(double dx, double dy) {
        this.x += dx;
        this.y += dy;
        return this; // permite encadenar llamadas si se desea
    }

    double calculaDistanciaAOrigen() {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }
}
```


## 13. Añade ahora otro nuevo método que se llame `distanciaA`, que reciba un `Punto` como parámetro y calcule la distancia entre `this` y el punto proporcionado

class Punto {
    double x; // visibilidad por defecto (package-private)
    double y; // visibilidad por defecto (package-private)

    Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    double calculaDistanciaAOrigen() {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }

    // Nuevo método: distancia entre 'this' y el punto 'otro'
    double distanciaA(Punto otro) {
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

// Ejemplo de uso
class Main {
    public static void main(String[] args) {
        Punto a = new Punto(3, 4);
        Punto b = new Punto(0, 0);
        double d = a.distanciaA(b); // debería ser 5.0
        System.out.println("Distancia entre a y b: " + d);
    }
}

## 14. El paso del `Punto` como parámetro a un método, es **por copia** o **por referencia**, es decir, si se cambia el valor de algún atributo del punto pasado como parámetro, dichos cambios afectan al objeto fuera del método? ¿Qué ocurre si en vez de un `Punto`, se recibiese un entero (`int`) y dicho entero se modificase dentro de la función? 

En Java, **el paso de parámetros siempre se realiza por valor**, pero es importante distinguir qué es lo que se copia. Cuando se pasa un objeto como `Punto`, lo que se copia es **la referencia**, no el objeto en sí. Esto significa que tanto el método como el código que llamó al método apuntan al **mismo objeto en memoria**. Si dentro del método se modifica un atributo del `Punto`, ese cambio afecta al objeto original fuera del método, porque ambos usan la misma referencia que apunta al mismo lugar del *heap*. Por tanto, aunque la referencia se copie, el objeto no se duplica, y las modificaciones al estado son visibles desde fuera.

Sin embargo, si dentro del método se intenta reasignar la referencia para que apunte a un nuevo objeto, esa reasignación no afecta a la variable original fuera del método. La razón es que la copia de la referencia es local; cambiar la copia no cambia la referencia original. Este comportamiento elimina el riesgo de que una función cambie directamente qué objeto está usando otra parte del programa, aunque sí puede modificar su contenido interno si accede a él mediante la referencia copiada.

En contraste, cuando se pasa un **tipo primitivo** como `int`, lo que se copia es simplemente el valor numérico. Al modificar ese valor dentro del método, el cambio solo afecta a la copia local, no a la variable original. Esto se asemeja al comportamiento clásico de C en el paso por valor, donde los parámetros se copian y cualquier modificación queda limitada al ámbito de la función. Por tanto, los enteros, booleanos, caracteres y demás tipos primitivos son totalmente independientes dentro del método que los recibe.

En resumen, pasar un `Punto` implica copiar la referencia, lo que permite modificar el objeto original desde el método, mientras que pasar un `int` copia únicamente el valor, haciendo que cualquier cambio sea local al método. Este comportamiento es clave para comprender cómo se gestionan los objetos en Java y evita confusiones frecuentes sobre si el lenguaje utiliza paso por referencia real o no.


## 15. ¿Qué es el método `toString()` en Java? ¿Existe en otros lenguajes? Pon un ejemplo de `toString()` en la clase `Punto` en Java

El método `toString()` en Java es una función heredada de `Object` que devuelve una representación textual “humana” del estado de un objeto. Se emplea de forma implícita cuando se concatena un objeto con una cadena o cuando se imprime con `System.out.println(obj)`. Sobrescribirlo permite ofrecer información legible y útil para depuración, logging y trazas, en lugar de la representación por defecto (`NombreDeClase@hash`), que resulta poco descriptiva.

Este concepto existe en otros lenguajes con nombres y mecanismos equivalentes. En C# se sobrescribe `ToString()` para el mismo fin; en Python se usa `__str__` (y `__repr__` para una representación más técnica), mientras que en C++ se acostumbra a sobrecargar el operador de inserción `<<` para `std::ostream` o a definir un método propio que devuelva una cadena. En todos los casos, la idea es proporcionar una forma estándar de convertir objetos a texto de manera clara y coherente.

Ejemplo de `toString()` en la clase `Punto`:

```java
class Punto {
    double x; // visibilidad por defecto (package-private)
    double y; // visibilidad por defecto (package-private)

    Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    double calculaDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }

    double distanciaA(Punto otro) {
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }

    @Override
    public String toString() {
        return "Punto(x=" + x + ", y=" + y + ")";
    }
}
```

Uso típico:

```java
Punto p = new Punto(3, 4);
System.out.println(p);               // Imprime: Punto(x=3.0, y=4.0)
System.out.println(p.toString());    // Equivalente
```


## 16. Reflexiona: ¿una clase es como un `struct` en C? ¿Qué le falta al `struct` para ser como una clase y las variables de ese tipo ser instancias?


En C, un `struct` permite agrupar variables bajo un mismo tipo, lo que puede recordar superficialmente a una clase. Sin embargo, un `struct` carece de la mayor parte de los mecanismos que caracterizan a la programación orientada a objetos. Un `struct` simplemente define un contenedor de datos sin comportamiento asociado, ya que las funciones se definen por separado y no están vinculadas al tipo de forma natural. Esta separación obliga al programador a gestionar manualmente la relación entre datos y operaciones, lo que puede llevar a un diseño menos cohesionado en programas grandes.

Lo que realmente falta en un `struct` para comportarse como una clase es la capacidad de incorporar **métodos**, es decir, funciones que operen directamente sobre los datos contenidos. Además, un `struct` no soporta conceptos como **encapsulamiento**, ya que no ofrece mecanismos de visibilidad (`private`, `public`, etc.) que permitan proteger el estado interno del tipo. Cualquier parte del programa puede leer o modificar los campos libremente, lo que aumenta la exposición a errores y dificulta garantizar invariantes internas.

Otro aspecto ausente es la posibilidad de definir **constructores**, que en Java permiten inicializar un objeto de forma coherente al momento de su creación. En C, la inicialización debe hacerse manualmente campo a campo o mediante funciones auxiliares, sin un sistema que garantice que el `struct` queda en un estado válido inmediatamente tras su declaración. Asimismo, C no incluye **destructores automáticos**, ni gestión de memoria integrada ni recolección de basura, por lo que la responsabilidad recae totalmente en el programador.

Finalmente, un `struct` tampoco soporta características avanzadas como **herencia** o **polimorfismo**, fundamentales en la orientación a objetos. Aunque en C pueden simularse ciertos comportamientos mediante punteros a funciones y estructuras anidadas, el lenguaje no los proporciona de forma nativa ni segura. Por tanto, aunque un `struct` pueda verse como un precursor conceptual, le faltan la mayoría de los rasgos necesarios para que sus variables puedan considerarse verdaderas instancias en el sentido de la programación orientada a objetos.


## 17. Quitemos un poco de magia a todo esto: ¿Como se podría “emular”, con `struct` en C, la clase `Punto`, con su función para calcular la distancia al origen? ¿Qué ha pasado con `this`?

En C puede “emularse” una clase definiendo un `struct` para los datos y funciones separadas que operen sobre punteros a ese `struct`. De este modo, el `struct` representaría el estado (los campos `x` e `y`) y las funciones externas cumplirían el papel de “métodos”. No existe encapsulamiento ni espacio de nombres propio de la clase, por lo que suele adoptarse una convención de nombres tipo `Punto_*` para evitar colisiones. Tampoco hay constructores: la inicialización debe hacerse manualmente o con funciones auxiliares que devuelvan un `struct` inicializado.

El “`this`” de Java se convierte en C en un **parámetro explícito**, normalmente un puntero al `struct` que se pasa a cada función “método”. En otras palabras, donde en Java se usaría `this->x`, en C se usaría `p->x`, siendo `p` el puntero al `struct Punto` recibido como argumento. Esto hace evidente que las funciones no “pertenecen” al tipo, sino que simplemente operan sobre datos a los que se les pasa la dirección.

```c
/* Emulación de la clase Punto en C */
#include <math.h>
#include <stdio.h>

typedef struct {
    double x;
    double y;
} Punto;

/* “Método” que calcula distancia al origen: recibe el “this” explícito */
double Punto_calculaDistanciaAOrigen(const Punto* p) {
    /* p es como el this de Java */
    return sqrt(p->x * p->x + p->y * p->y);
}

/* Función auxiliar para crear/iniicializar (no es un constructor real) */
Punto Punto_crear(double x, double y) {
    Punto p = { x, y };
    return p;
}

int main(void) {
    Punto a = Punto_crear(3.0, 4.0);             /* “instanciación” manual */
    double d = Punto_calculaDistanciaAOrigen(&a); /* pasar &a como “this” */
    printf("Distancia al origen: %.1f\n", d);     /* 5.0 */
    return 0;
}
```

Esta aproximación ilustra bien las diferencias: no hay control de visibilidad ni constructores/destructores automáticos, ni herencia o polimorfismo nativo. Aun así, el patrón resulta útil para entender cómo los métodos de una clase no son más que funciones que operan sobre un bloque de memoria con cierta estructura; en C ese bloque se pasa explícitamente como parámetro, mientras que en Java el acceso se realiza mediante la referencia implícita `this`.