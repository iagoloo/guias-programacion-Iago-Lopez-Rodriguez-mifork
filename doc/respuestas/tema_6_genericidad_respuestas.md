<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

Un modo clásico de simular “genericidad” en C consiste en utilizar un array primitivo de punteros (`void*`), de forma que cada posición pueda referenciar datos de cualquier tipo. El tipo `void*` permite almacenar direcciones de memoria sin información sobre el tipo concreto, lo que concede una gran flexibilidad similar a la de los genéricos, aunque sin comprobación en tiempo de compilación. Este enfoque es habitual en estructuras como listas o vectores genéricos en bibliotecas en C.

En este caso, el array subyacente sigue siendo primitivo (un array de punteros), pero la responsabilidad de saber qué tipo real se ha almacenado y de realizar los *casts* adecuados recae completamente en el programador. Esto implica una mayor probabilidad de errores en tiempo de ejecución, algo que no ocurre en lenguajes con soporte nativo de genericidad. Aun así, este mecanismo resulta útil para entender el problema que los genéricos pretenden resolver.

```c
typedef struct {
    void** data;
    int size;
    int capacity;
} VectorGenerico;

void inicializar(VectorGenerico* v, int cap) {
    v->data = malloc(sizeof(void*) * cap);
    v->size = 0;
    v->capacity = cap;
}

void añadir(VectorGenerico* v, void* elemento) {
    v->data[v->size++] = elemento;
}
```

Un enfoque equivalente en Java, anterior a la introducción de genéricos, consiste en usar un array de `Object`. Dado que cualquier clase en Java hereda de `Object`, este tipo de array puede almacenar instancias de cualquier clase. De nuevo, la estructura subyacente es un array, y la flexibilidad se logra mediante el uso de un tipo común a todos los objetos.

Este método presenta problemas similares a los del uso de `void*` en C: al recuperar los elementos, es necesario realizar *casts* explícitos al tipo concreto, y los errores solo se detectan en tiempo de ejecución. Precisamente estas limitaciones motivaron la introducción de la genericidad en Java, para permitir estructuras de datos reutilizables con seguridad de tipos.

```java
class VectorGenerico {
    private Object[] data;
    private int size;

    public VectorGenerico(int capacidad) {
        data = new Object[capacidad];
        size = 0;
    }

    public void add(Object o) {
        data[size++] = o;
    }

    public Object get(int i) {
        return data[i];
    }
}
```

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

La **programación genérica** es un paradigma cuyo objetivo es definir algoritmos y estructuras de datos de forma independiente del tipo concreto de los datos que manipulan, manteniendo al mismo tiempo la seguridad de tipos. La idea central es poder escribir una única implementación reutilizable que funcione con distintos tipos, delegando en el compilador la comprobación de que esos tipos se usan de forma correcta. En lenguajes como Java, esto se materializa mediante los *genéricos*, que permiten parametrizar clases y métodos con tipos.

Este enfoque se apoya en conceptos ya conocidos como la reutilización de código y el polimorfismo, pero va un paso más allá, ya que evita la necesidad de realizar conversiones de tipo explícitas (*casts*) al recuperar los datos. De este modo, muchos errores que antes solo se detectaban en tiempo de ejecución pasan a detectarse en tiempo de compilación, aumentando la robustez del programa sin perder flexibilidad.

El ejemplo anterior basado en `void*` en C o en `Object` en Java **no es propiamente un ejemplo de programación genérica**, aunque suele considerarse un antecedente conceptual. En esos casos se logra almacenar datos de cualquier tipo, pero se pierde completamente la información de tipo, obligando al programador a realizar conversiones manuales y asumiendo el riesgo de errores en tiempo de ejecución. Por tanto, se trata de una simulación rudimentaria de genericidad, pero no de programación genérica en sentido estricto, ya que no existe comprobación de tipos ni soporte específico del lenguaje para ello.


## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

El principal problema respecto al **chequeo de tipos** al emplear `void*` en C o `Object` en Java es que **la información de tipo se pierde** al almacenar los elementos en la estructura de datos. El compilador ya no sabe qué tipo concreto hay en cada posición del array, por lo que no puede verificar si las operaciones realizadas sobre esos datos son correctas. Como consecuencia, se renuncia a una de las funciones fundamentales del sistema de tipos: detectar errores antes de ejecutar el programa.

Al recuperar un elemento de la estructura, es obligatorio realizar un *cast* explícito al tipo esperado. Este *cast* no se comprueba en tiempo de compilación, sino en tiempo de ejecución, lo que implica que un error de tipo solo se manifestará cuando el programa esté funcionando. En C, este problema es aún más grave, ya que un *cast* incorrecto desde `void*` puede provocar comportamientos indefinidos, accesos inválidos a memoria o fallos difíciles de diagnosticar.

Otro inconveniente importante es que **no existe ninguna relación formal entre la estructura de datos y el tipo que se espera almacenar en ella**. La estructura puede contener mezclados distintos tipos sin restricción alguna, incluso aunque conceptualmente se pretenda que sea homogénea. El compilador no puede impedir que se añada un tipo incorrecto ni advertir sobre usos inconsistentes, lo que incrementa la probabilidad de errores lógicos.

En resumen, el uso de `void*` o `Object` permite flexibilidad, pero a costa de **perder seguridad de tipos**, trasladando la responsabilidad del chequeo al programador. Este enfoque contradice el principio de detección temprana de errores y es precisamente uno de los problemas que la programación genérica moderna trata de resolver, proporcionando comprobación de tipos estática sin sacrificar reutilización ni abstracción.


## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

Los **parámetros de tipo** son un mecanismo propio de la programación genérica que permite **parametrizar clases, interfaces o métodos con uno o varios tipos**, sin especificar de antemano cuáles serán esos tipos concretos. En lugar de fijar el tipo de los datos, se utiliza un identificador simbólico (por convenio, letras como `T`, `E`, `K`, `V`) que actúa como un *placeholder* de tipo. Este identificador se sustituye por un tipo real cuando la clase o el método se utiliza, manteniendo la coherencia de tipos durante toda la compilación.

Gracias a los parámetros de tipo, el compilador conserva la información de tipo que se perdía al usar `Object` o `void*`. Esto permite que las operaciones sobre los datos sean comprobadas estáticamente, evitando *casts* explícitos y detectando incompatibilidades de tipo antes de ejecutar el programa. De este modo, se logra la misma flexibilidad que en los ejemplos previos, pero sin renunciar a la seguridad del sistema de tipos del lenguaje.

Desde el punto de vista conceptual, un parámetro de tipo puede entenderse como una **generalización del tipo de una variable**, aplicada a estructuras completas. Así como una variable puede representar un valor, un parámetro de tipo representa un tipo que aún no se conoce. Este enfoque encaja de forma natural con conocimientos previos como clases, herencia y polimorfismo, ya que los genéricos operan a nivel de tipos, no de objetos.

Por ejemplo, en Java una estructura genérica basada en parámetros de tipo se definiría así:

```java
class Vector<T> {
    private T[] data;
    private int size;

    public Vector(int capacidad) {
        data = (T[]) new Object[capacidad];
        size = 0;
    }

    public void add(T elemento) {
        data[size++] = elemento;
    }

    public T get(int i) {
        return data[i];
    }
}
```

En este caso, `T` es el parámetro de tipo que garantiza que todos los elementos del vector sean del mismo tipo y que dicho tipo sea conocido y verificado por el compilador.


## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

En **Java**, la programación genérica se implementa mediante *generics*, que permiten parametrizar clases con un tipo concreto y hacer que el compilador garantice la coherencia de dicho tipo en todos los usos. Al instanciar una estructura genérica indicando `String` como parámetro de tipo, se asegura que solo se puedan introducir objetos `String` y que, al recuperarlos, no sean necesarios *casts*. Esto elimina errores típicos asociados al uso de `Object` y refuerza la seguridad de tipos en tiempo de compilación.

Además, durante el recorrido de la estructura, cada elemento recuperado es tratado directamente como un `String`. El compilador verifica que los métodos invocados sobre los elementos existen realmente en esa clase, lo que demuestra que el tipo concreto se conserva a nivel lógico, aunque internamente exista borrado de tipos (*type erasure*). Desde el punto de vista del programador, el uso es sencillo y seguro.

```java
import java.util.ArrayList;
import java.util.List;

List<String> lista = new ArrayList<>();

lista.add("Hola");
lista.add("Genéricos");
lista.add("Java");

for (String s : lista) {
    System.out.println(s.toUpperCase()); // s es String con total seguridad
}
```

En **C++**, la programación genérica se basa en *templates*, que funcionan de manera diferente a los generics de Java. En este caso, el compilador genera código específico para cada tipo con el que se instancia el template, lo que preserva completamente la información de tipo y no requiere conversiones ni borrado de tipos. Al crear un `std::vector<std::string>`, se obtiene una estructura que solo admite cadenas y cuyo tipo concreto se conoce en todo momento.

Durante el recorrido del vector, cada elemento es inequívocamente un `std::string`, y cualquier intento de introducir o tratar los datos como otro tipo provocaría un error de compilación. Este comportamiento muestra una forma de programación genérica fuertemente tipada y resuelta en tiempo de compilación, alineada con los principios clásicos de C++.

```cpp
#include <vector>
#include <string>
#include <iostream>

std::vector<std::string> v;

v.push_back("Hola");
v.push_back("Templates");
v.push_back("C++");

for (const std::string& s : v) {
    std::cout << s.length() << std::endl; // s es std::string con seguridad total
}
```

Ambos ejemplos ilustran cómo la programación genérica permite reutilizar estructuras de datos sin perder seguridad de tipos. Aunque Java y C++ emplean mecanismos distintos, en los dos casos el compilador impide usos incorrectos y garantiza que cada elemento es del tipo concreto especificado, evitando los problemas asociados a `void*` o `Object`.


## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

Cuando se **instancia una clase genérica con parámetros de tipo**, el compilador utiliza la información del tipo concreto proporcionado para **verificar la corrección del uso de la clase**, especialmente en accesos, asignaciones y llamadas a métodos. Es decir, el compilador comprueba que solo se introducen y se recuperan valores del tipo indicado, y que las operaciones realizadas sobre ellos son válidas. Esta comprobación ocurre **en tiempo de compilación**, lo que permite detectar muchos errores antes de la ejecución del programa.

Sin embargo, **Java y C++ no realizan el mismo trabajo interno** al compilar código genérico. En Java, los genéricos se implementan mediante un mecanismo denominado **type erasure** (borrado de tipos). Tras realizar las comprobaciones de tipo en compilación, el compilador **elimina la información del parámetro de tipo**, sustituyéndolo generalmente por `Object` (o por el límite superior si existe uno). Como consecuencia, en el bytecode no existen clases distintas para `List<String>` o `List<Integer>`; ambas comparten la misma representación en tiempo de ejecución, y los *casts* necesarios se insertan automáticamente.

El **type erasure** implica que la genericidad en Java es principalmente una característica de compilación y no de ejecución. No es posible, por ejemplo, conocer el tipo genérico concreto en tiempo de ejecución ni crear directamente arrays de tipos genéricos. A cambio, se mantiene compatibilidad con código antiguo previo a los genéricos, lo que fue una decisión clave en el diseño del lenguaje.

En **C++**, los *templates* funcionan de manera diferente mediante la llamada **instanciación de plantillas**. Cada vez que se usa un template con un tipo concreto, el compilador **genera una versión específica del código** para ese tipo. Así, `vector<string>` y `vector<int>` se convierten en clases distintas en el binario final. Esto preserva completamente la información de tipo y permite optimizaciones más agresivas, aunque puede aumentar el tamaño del código compilado.

En resumen, Java utiliza genericidad con **borrado de tipos**, centrada en la seguridad sintáctica y la compatibilidad, mientras que C++ emplea genericidad basada en **generación de código especializado**, manteniendo el tipo en todo el proceso. Ambos enfoques cumplen el mismo objetivo conceptual, pero con diferencias importantes en su funcionamiento interno y en sus consecuencias prácticas.


## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

Una clase con **parámetros de tipo** permite definir una estructura que trabaja con varios tipos sin fijarlos de antemano, manteniendo seguridad de tipos en tiempo de compilación. En este caso, la clase `Par` se parametriza con dos tipos distintos, lo que permite alojar dos valores heterogéneos relacionados conceptualmente, sin necesidad de recurrir a `Object` ni a conversiones explícitas. El compilador garantiza que cada valor se usa siempre con su tipo correcto.

Este diseño resulta especialmente útil para modelar retornos múltiples de funciones, algo habitual en lenguajes como C pero no directamente soportado en Java. En lugar de usar arrays o clases específicas para cada caso, una clase genérica como `Par<A, B>` permite expresar de forma clara e intuitiva que una función devuelve dos resultados de tipos concretos, documentando además la intención del código mediante el sistema de tipos.

La definición de la clase se limita a declarar los parámetros de tipo, almacenar los valores y proporcionar un constructor y métodos de acceso. No es necesario ningún *cast* al recuperar los datos, ya que el tipo concreto queda fijado cuando se instancia la clase.

```java
class Par<T, U> {
    private final T primero;
    private final U segundo;

    public Par(T primero, U segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public T getPrimero() {
        return primero;
    }

    public U getSegundo() {
        return segundo;
    }
}
```

Un ejemplo de uso consiste en devolver la **media** y la **desviación típica** de un array de `double`. La función devuelve un `Par<Double, Double>`, dejando claro qué tipos contiene el resultado y permitiendo usar cada valor con total seguridad de tipos durante su posterior tratamiento.

```java
public static Par<Double, Double> estadisticas(double[] datos) {
    double suma = 0.0;
    for (double d : datos) {
        suma += d;
    }
    double media = suma / datos.length;

    double varianza = 0.0;
    for (double d : datos) {
        varianza += Math.pow(d - media, 2);
    }
    varianza /= datos.length;
    double desviacion = Math.sqrt(varianza);

    return new Par<>(media, desviacion);
}

// Uso
double[] valores = {2.0, 4.0, 4.0, 4.0, 5.0, 5.0, 7.0, 9.0};
Par<Double, Double> res = estadisticas(valores);

double media = res.getPrimero();
double desviacion = res.getSegundo();
```

En este ejemplo, se observa cómo la programación genérica permite expresar resultados compuestos de forma clara, reutilizable y con comprobación estática de tipos, evitando errores habituales asociados a estructuras no genéricas.


## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

En Java, un **método genérico** es aquel que declara sus propios parámetros de tipo, independientemente de que la clase sea genérica o no. Esto permite definir operaciones reutilizables que trabajan con distintos tipos manteniendo la seguridad de tipos. Un método genérico se reconoce porque introduce el parámetro de tipo antes del tipo de retorno, y el compilador exige coherencia entre los argumentos y el valor devuelto.

Si se define el método `seleccionaUno` usando `Object`, se consigue flexibilidad, pero se pierden dos propiedades importantes: es necesario realizar *downcasting* al recuperar el valor, y no se puede garantizar que ambos objetos sean del mismo tipo. El compilador permite pasar objetos heterogéneos, lo que puede provocar errores en tiempo de ejecución.

```java
import java.util.Random;

public static Object seleccionaUno(Object a, Object b) {
    Random r = new Random();
    return r.nextBoolean() ? a : b;
}

// Uso
Object o = seleccionaUno("Hola", 42);
String s = (String) o; // Riesgo de ClassCastException
```

La versión genérica del método evita ambos problemas. Al declarar un parámetro de tipo `<T>`, el compilador fuerza a que los dos argumentos sean del mismo tipo y garantiza que el valor devuelto también lo sea. De este modo, no es necesario hacer *casts* explícitos y los errores de tipo se detectan en compilación, no en ejecución.

```java
import java.util.Random;

public static <T> T seleccionaUno(T a, T b) {
    Random r = new Random();
    return r.nextBoolean() ? a : b;
}

// Uso
String s = seleccionaUno("Hola", "Adiós"); // Correcto
// seleccionaUno("Hola", 42); // Error de compilación
```

En términos comparativos, el método genérico (i) **elimina el downcasting**, ya que el tipo concreto del retorno es conocido por el compilador, y (ii) **fuerza la homogeneidad de tipos**, impidiendo que se mezclen objetos incompatibles. Esto ejemplifica claramente cómo los parámetros de tipo mejoran la seguridad y expresividad del código frente al uso de `Object`.


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

Sí, **es posible establecer restricciones en los parámetros de tipo** en Java mediante los llamados **tipos acotados** (*bounded type parameters*). En lugar de permitir que un parámetro de tipo represente cualquier clase, se puede imponer que ese tipo herede de una clase concreta o implemente una interfaz determinada, usando la palabra clave `extends`. De este modo, el compilador garantiza que el tipo genérico dispone, al menos, de los métodos definidos en ese límite, permitiendo tratarlo de forma más específica.

Una **primera solución sin genéricos** consiste en definir las coordenadas de un punto directamente como `Number`. Dado que `Number` es la superclase de todos los tipos numéricos envoltorio en Java (`Integer`, `Double`, `Float`, etc.), se puede tratar cualquier número de forma uniforme. Sin embargo, esta solución pierde precisión de tipos, ya que no queda reflejado si el punto trabaja con `Integer`, `Double` u otro tipo concreto, y obliga a convertir a `double` para realizar cálculos.

```java
class Punto {
    private Number x;
    private Number y;

    public Punto(Number x, Number y) {
        this.x = x;
        this.y = y;
    }

    public Number getX() {
        return x;
    }

    public Number getY() {
        return y;
    }

    public double calcularDistanciaA(Punto otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

Una **segunda solución más robusta** utiliza **parámetros de tipo acotados**, declarando que el tipo genérico `T` debe extender `Number`. Esto permite reforzar el chequeo de tipos, garantizando que ambas coordenadas y los puntos implicados trabajan con el mismo tipo de número. El compilador impide mezclar, por ejemplo, un `Punto<Integer>` con un `Punto<Double>`, mejorando la consistencia del diseño.

```java
class Punto<T extends Number> {
    private T x;
    private T y;

    public Punto(T x, T y) {
        this.x = x;
        this.y = y;
    }

    public T getX() {
        return x;
    }

    public T getY() {
        return y;
    }

    public double calcularDistanciaA(Punto<T> otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

Respecto al **type erasure**, en ambos casos el tipo genérico `T` **desaparece tras la compilación**. En la versión genérica, el tipo final que queda en el bytecode es `Number`, ya que es el límite superior del parámetro de tipo. Por tanto, aunque en el código fuente se distinga entre `Punto<Integer>` o `Punto<Double>`, en tiempo de ejecución ambos se tratan como `Punto<Number>`, siendo el compilador el responsable de asegurar la corrección de tipos antes de ejecutar el programa.


## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

Ambas soluciones permiten reutilizar la clase `Punto` para distintos tipos numéricos, pero **no ofrecen el mismo nivel de refuerzo del chequeo de tipos**. En la solución sin genéricos, donde las coordenadas son de tipo `Number`, **sí es posible crear un punto con una coordenada entera y la otra real** (por ejemplo, un `Integer` y un `Double`). Esto es así porque `Number` actúa como un cajón común que acepta cualquier subtipo, y el compilador no impone ninguna restricción adicional sobre la homogeneidad de las coordenadas.

En cambio, en la solución con genéricos (`Punto<T extends Number>`), **no se permite mezclar tipos distintos de coordenadas dentro de un mismo punto**. El compilador obliga a que ambas coordenadas sean del mismo tipo concreto `T`, por ejemplo `Integer` o `Double`. Un intento de crear un `Punto<Integer>` pasando un `Double` en una de las coordenadas produciría un error de compilación. Esto refuerza el diseño del programa, haciendo explícito que un punto trabaja coherentemente con un único tipo numérico.

Respecto al tipo devuelto por los métodos de acceso, en la solución sin genéricos el método `getX` devuelve un `Number`. Esto implica que, para usar el valor como un tipo concreto (por ejemplo, `Integer`), sería necesario realizar un *cast*, con el consiguiente riesgo de error en tiempo de ejecución. El compilador no puede inferir más información que la clase base común.

En la solución con genéricos, el método `getX` devuelve el tipo **concreto `T`**, que queda fijado al instanciar el objeto (`Integer`, `Double`, etc.). Esto elimina la necesidad de *downcasting* y permite que el compilador conozca exactamente el tipo con el que se está trabajando. Por tanto, el uso de generics no solo restringe combinaciones incorrectas, sino que también **mejora la precisión del tipo devuelto y la seguridad del código**.


## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

Para reforzar el **chequeo de tipos** y evitar el uso de `instanceof` y *downcasting*, se puede aplicar programación genérica a la propia interfaz `Punto`. La idea consiste en expresar, a nivel de tipos, que un punto solo puede calcular la distancia a **otro punto del mismo tipo concreto**. Este patrón es una forma controlada de *polimorfismo recursivo* y permite que el compilador garantice la corrección sin comprobaciones en tiempo de ejecución.

La interfaz se define con un **parámetro de tipo acotado a sí mismo**, indicando que el método `distanciaA` recibe exactamente el mismo tipo que la clase que lo implementa. De este modo, cuando una clase concreta implementa la interfaz, fija ese parámetro de tipo con su propio tipo, y el método queda automáticamente especializado.

```java
public interface Punto<T extends Punto<T>> {
    double distanciaA(T p);
}
```

Al implementar la interfaz, cada clase concreta se asocia consigo misma como parámetro de tipo. Esto fuerza que `Punto2D` solo pueda calcular distancias con otros `Punto2D`, y lo mismo para `Punto3D`, todo ello sin necesidad de comprobaciones dinámicas ni conversiones de tipo.

```java
public class Punto2D implements Punto<Punto2D> {
    private final double x, y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double distanciaA(Punto2D p) {
        return Math.sqrt(
            Math.pow(x - p.x, 2) +
            Math.pow(y - p.y, 2)
        );
    }
}

public class Punto3D implements Punto<Punto3D> {
    private final double x, y, z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double distanciaA(Punto3D p) {
        return Math.sqrt(
            Math.pow(x - p.x, 2) +
            Math.pow(y - p.y, 2) +
            Math.pow(z - p.z, 2)
        );
    }
}
```

Con esta solución, el compilador impide expresamente llamadas como `punto2D.distanciaA(punto3D)`, ya que los tipos no coinciden. Se logra así un **polimorfismo seguro y estático**, donde el diseño correcto se impone desde el sistema de tipos, eliminando errores en tiempo de ejecución y haciendo innecesario el uso de `instanceof`. Este ejemplo muestra un uso avanzado pero muy potente de los genéricos en Java para modelar relaciones invariantes entre tipos.


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

Que `String` sea subtipo de `Object` **no implica** que `List<String>` sea subtipo de `List<Object>` en Java. Aunque intuitivamente pueda parecerlo, los **tipos genéricos en Java son invariantes** respecto a sus parámetros de tipo. Esto significa que, aunque exista una relación de herencia entre `String` y `Object`, **no existe ninguna relación de subtipo** entre `List<String>` y `List<Object>`. Permitirlo rompería la seguridad de tipos, ya que se podrían insertar objetos que no fuesen `String` en una lista que conceptualmente debería contener solo cadenas.

En cambio, los **arrays en Java sí son covariantes**, por lo que `String[]` **sí es subtipo** de `Object[]`. Esta decisión se tomó por compatibilidad histórica con versiones antiguas del lenguaje. Sin embargo, esta covarianza introduce un problema potencial en tiempo de ejecución: aunque el compilador permita asignar un `String[]` a una referencia `Object[]`, en tiempo de ejecución se puede intentar almacenar un objeto que no sea `String`, provocando una `ArrayStoreException`.

```java
Object[] arr = new String[10];
arr[0] = 42; // Compila, pero lanza ArrayStoreException en ejecución
```

Este ejemplo muestra que los arrays trasladan ciertos errores de tipo al tiempo de ejecución, mientras que los genéricos los detectan en compilación. Precisamente para evitar este tipo de problemas, los diseñadores de Java optaron por hacer los genéricos invariantes. En otras palabras, es preferible un error de compilación que una excepción en ejecución.

A partir de estos ejemplos, se pueden definir tres conceptos clave. Un tipo genérico es **covariante** si al cumplirse `A <: B` también se cumple `G<A> <: G<B>` (como ocurre con los arrays). Es **contravariante** si la relación se invierte (`G<B> <: G<A>`), y es **invariante** si no existe ninguna relación de subtipo entre `G<A>` y `G<B>` aunque `A <: B` (caso de `List<T>` en Java). Los genéricos en Java son invariantes por defecto, aunque pueden expresarse variantes controladas mediante comodines (`? extends`, `? super`) para recuperar flexibilidad sin perder seguridad de tipos.


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

Un **wildcard** (`?`) en Java es un comodín de tipo que se usa en genéricos para **expresar variancia de forma controlada**, es decir, para permitir relaciones de subtipo que los genéricos invariantes no admiten por defecto. El wildcard indica que no se conoce el tipo exacto del parámetro genérico, pero sí se pueden imponer **límites** sobre él. De esta forma, se gana flexibilidad sin perder seguridad de tipos en compilación.

La forma `List<? extends T>` introduce **covarianza** acotada superiormente: la lista contiene elementos de algún subtipo de `T`. En este caso, la lista es segura para **leer** valores como `T`, pero **no permite insertar** nuevos elementos (salvo `null`), ya que el compilador no sabe el subtipo concreto. Este patrón se resume en la regla *“Producer Extends”*: si la estructura produce valores, se usa `extends`.

```java
public static double suma(List<? extends Number> numeros) {
    double total = 0.0;
    for (Number n : numeros) {
        total += n.doubleValue();
    }
    return total;
}
```

Por el contrario, `List<? super T>` introduce **contravarianza** acotada inferiormente: la lista acepta elementos de tipo `T` o de cualquiera de sus supertipos. En este caso, la lista es segura para **escribir** valores de tipo `T`, pero al leer solo se obtiene `Object`, ya que el tipo concreto es desconocido. Este patrón se resume como *“Consumer Super”*: si la estructura consume valores, se usa `super`.

```java
public static void añadeEnteros(List<? super Integer> lista) {
    lista.add(1);
    lista.add(2);
    lista.add(3);
}
```

En resumen, los wildcards permiten recuperar covarianza y contravarianza de forma explícita: `? extends T` se utiliza cuando interesa **leer** valores como `T`, y `? super T` cuando interesa **añadir** valores de tipo `T`. Esta distinción evita errores en tiempo de ejecución y combina flexibilidad con un chequeo de tipos sólido en compilación.
