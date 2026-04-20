<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

El **polimorfismo** es un principio de la programación orientada a objetos que permite tratar a objetos de distintas clases de forma uniforme, siempre que todas ellas compartan una misma superclase. En lugar de trabajar directamente con el tipo concreto de un objeto, se trabaja con el tipo de la clase padre, y es el propio objeto el que decide qué comportamiento ejecutar. Esto permite escribir código más genérico, reutilizable y flexible, reduciendo dependencias entre clases.

En Java, el polimorfismo se apoya especialmente en la **herencia**. Una referencia de la clase base puede apuntar a un objeto de cualquiera de sus subclases, y cuando se invoca un método sobre esa referencia, se ejecuta la versión del método correspondiente a la clase real del objeto. Este mecanismo recuerda, a un nivel conceptual, al uso de punteros a funciones en C, pero gestionado automáticamente por el lenguaje y de forma segura.

La **sobreescritura de métodos** consiste en que una subclase proporcione su propia implementación de un método heredado de la clase padre, manteniendo exactamente la misma firma (nombre, parámetros y tipo de retorno). Gracias a la sobreescritura, las distintas subclases pueden modificar o especializar el comportamiento definido en la superclase, lo que hace posible el polimorfismo en tiempo de ejecución.

```java
class Animal {
    void hacerSonido() {
        System.out.println("El animal hace un sonido");
    }
}

class Perro extends Animal {
    @Override
    void hacerSonido() {
        System.out.println("El perro ladra");
    }
}

// Uso polimórfico
Animal a = new Perro();
a.hacerSonido(); // Ejecuta el método de Perro
```


## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

La **ligadura dinámica** (o **enlace tardío**) consiste en que la decisión de qué método concreto se ejecuta **no se toma en tiempo de compilación**, sino **en tiempo de ejecución**, cuando ya se conoce el tipo real del objeto al que apunta la referencia. Es decir, aunque una variable tenga declarado un tipo base, el método que se ejecuta depende de la clase concreta del objeto asignado. Este mecanismo es fundamental para que el polimorfismo funcione correctamente en lenguajes orientados a objetos.

La relación con el **polimorfismo** es directa: el polimorfismo permite usar una referencia de la superclase para manejar objetos de distintas subclases, y la ligadura dinámica permite que cada objeto responda de forma distinta a la misma llamada de método. Sin ligadura dinámica, siempre se ejecutaría el método asociado al tipo de la referencia, lo que impediría el comportamiento polimórfico. Por tanto, puede afirmarse que la ligadura dinámica es el mecanismo que hace efectivo el polimorfismo en tiempo de ejecución.

En **C++**, la ligadura dinámica **no es el comportamiento por defecto**. Para que un método se enlace dinámicamente, debe declararse explícitamente como `virtual` en la clase base. Si no se hace así, el enlace será estático y la decisión se tomará en compilación, usando el tipo de la referencia. En cambio, en **Java**, **todos los métodos no estáticos son virtuales por defecto**, por lo que la ligadura dinámica se aplica automáticamente sin necesidad de indicarlo explícitamente. Esto simplifica el uso del polimorfismo y reduce errores comunes.

En **Python**, el enlace es incluso más flexible, ya que el lenguaje es de **tipado dinámico**. La ligadura de métodos es siempre dinámica y no depende de una jerarquía explícita de herencia, sino de la presencia del método en el objeto (lo que se conoce como *duck typing*). No es necesario declarar interfaces ni métodos virtuales: si un objeto tiene el método esperado, se puede utilizar. Esto hace que la ligadura dinámica y el polimorfismo estén completamente integrados en el funcionamiento normal del lenguaje.


## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

Un ejemplo sencillo de **polimorfismo en Java** puede construirse a partir de una clase base `Soldado` que defina un método `saludar`. Este método representa un comportamiento común a todos los soldados, y será heredado por las subclases. Mediante la herencia, se permite que cada tipo concreto de soldado pueda especializar o modificar dicho comportamiento según sea necesario.

La clase `Zapador` sobrescribe completamente el método `saludar`, proporcionando una implementación propia que sustituye a la original. En cambio, la clase `Artillero` hereda el método tal como está, sin modificarlo. Esto permite observar cómo, a través de una referencia del tipo común `Soldado`, se ejecutan métodos distintos dependiendo del tipo real del objeto asociado a dicha referencia.

El polimorfismo se ilustra creando un array de `Soldado` que contiene objetos de distintas subclases. Al recorrer este array y llamar al método `saludar` usando referencias de tipo `Soldado`, Java decide en tiempo de ejecución qué versión del método debe ejecutarse. Este comportamiento demuestra la ligadura dinámica y evita la necesidad de comprobar manualmente el tipo de cada objeto.

```java
class Soldado {
    void saludar() {
        System.out.println("El soldado saluda respetuosamente.");
    }
}

class Zapador extends Soldado {
    @Override
    void saludar() {
        System.out.println("El zapador saluda mientras prepara explosivos.");
    }
}

class Artillero extends Soldado {
    // No sobrescribe saludar
}

public class Main {
    public static void main(String[] args) {
        Soldado[] soldados = new Soldado[2];
        soldados[0] = new Zapador();
        soldados[1] = new Artillero();

        for (Soldado s : soldados) {
            s.saludar(); // Polimorfismo en acción
        }
    }
}
```


## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

Sí, al **sobreescribir un método** es posible invocar la versión definida en la clase base y trabajar a partir de su comportamiento. Esto permite **reutilizar código** ya existente en la superclase y añadir solo la parte específica necesaria en la subclase, evitando duplicaciones y facilitando el mantenimiento. Este enfoque es habitual cuando el comportamiento general sigue siendo válido, pero se desea ampliarlo o complementarlo.

En Java, para invocar explícitamente un método de la **clase base** desde una subclase se utiliza la palabra clave **`super`**. Esta referencia permite acceder a los métodos y atributos heredados, incluso cuando han sido sobreescritos. De este modo, la subclase puede ejecutar primero la lógica común definida en la superclase y, a continuación, añadir su propio código.

En el caso del `Zapador`, el método `saludar` puede llamar primero al método `saludar` de `Soldado` y después añadir el mensaje adicional. Aunque el método está sobreescrito, el uso de `super.saludar()` garantiza que se ejecute el comportamiento original antes de la extensión específica del zapador. Esto mantiene la coherencia del diseño y respeta el principio de reutilización.

```java
class Soldado {
    void saludar() {
        System.out.println("El soldado saluda respetuosamente.");
    }
}

class Zapador extends Soldado {
    @Override
    void saludar() {
        super.saludar(); // Llamada al método de la clase base
        System.out.println("ZAPADOR A SUS ORDENES");
    }
}
```

La palabra clave utilizada para invocar el método de la clase base es **`super`**, y su uso es esencial cuando se desea combinar el comportamiento heredado con uno nuevo en un contexto de sobreescritura.


## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

Al **sobreescribir un método en Java**, existen restricciones estrictas sobre su firma. Los **parámetros deben coincidir exactamente** en número, orden y tipo con los del método de la clase base; no está permitido modificarlos, ya que en ese caso el método pasaría a considerarse distinto. En cuanto al **tipo de retorno**, debe ser el mismo que el del método original o bien un **subtipo compatible** (lo que se conoce como *retorno covariante*). Además, no se puede **reducir la visibilidad** del método (por ejemplo, pasar de `public` a `protected`), aunque sí se puede hacer más accesible.

La **sobreescritura (*overriding*)** y la **sobrecarga (*overloading*)** son conceptos distintos aunque a menudo se confunden. La sobreescritura implica redefinir en una subclase un método heredado para cambiar su comportamiento, y está directamente ligada al polimorfismo y a la ligadura dinámica. La sobrecarga, en cambio, consiste en definir varios métodos con el mismo nombre pero con **distintos parámetros** dentro de una misma clase (o jerarquía), y la elección del método se realiza en **tiempo de compilación**, no en ejecución.

La anotación **`@Override`** se utiliza para indicar explícitamente que un método pretende sobreescribir uno heredado de la superclase. Su función principal no es funcional, sino de **verificación**: el compilador comprueba que realmente existe un método compatible en la clase base. Si hay un error en la firma (por ejemplo, un parámetro incorrecto o un fallo tipográfico en el nombre), el compilador avisará inmediatamente.

Es altamente recomendable usar **`@Override` siempre** que se sobrescriba un método, ya que evita errores difíciles de detectar y mejora la claridad del código. Además, facilita la lectura y el mantenimiento, ya que deja explícito que ese método redefine un comportamiento heredado y forma parte del diseño polimórfico de la jerarquía de clases.


## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

Sí, al estudiar Java se empieza a **usar polimorfismo muy pronto**, aunque no siempre se identifique explícitamente como tal. Desde el momento en que se trabaja con herencia y se sobrescriben métodos heredados de una superclase, ya se está aplicando polimorfismo. Esto ocurre incluso antes de estudiar el concepto de forma teórica, porque Java lo incorpora de manera natural en su diseño y en sus clases básicas.

Un ejemplo claro es la sobrescritura de métodos como **`toString`** o **`equals`**, que pertenecen a la clase `Object`, la superclase de todas las clases en Java. Al redefinir estos métodos en una clase propia, se está proporcionando un comportamiento específico que se ejecutará cuando el objeto sea tratado como un `Object`. Cuando una referencia de tipo `Object` apunta a un objeto de una clase concreta y se llama a `toString` o `equals`, Java elige en tiempo de ejecución la versión adecuada del método, lo que constituye un uso directo del polimorfismo.

Es importante destacar que el polimorfismo **no depende de que se use explícitamente una referencia de la superclase en el código del programador**. Basta con que el método sobrescrito sea invocado a través de una referencia que no conozca el tipo concreto del objeto en compilación. Esto ocurre de forma habitual, por ejemplo, al imprimir objetos, al compararlos o al almacenarlos en colecciones genéricas.

Por tanto, puede afirmarse que **el polimorfismo está presente desde los primeros pasos en Java**, aunque se entienda formalmente más adelante. Sobrescribir `toString` o `equals` no solo mejora la funcionalidad de las clases, sino que también supone una introducción práctica y temprana al polimorfismo y a la ligadura dinámica, dos pilares fundamentales de la programación orientada a objetos.


## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

Una **clase abstracta** es una clase que está pensada para servir como **base de otras clases**, pero que no representa por sí misma un objeto completo. Su objetivo es definir una estructura común y un conjunto de comportamientos que las subclases deberán compartir o completar. Por este motivo, **no se pueden crear instancias** de una clase abstracta: solo se pueden instanciar sus subclases concretas. Conceptualmente, una clase abstracta representa una idea general, no un objeto específico.

Un **método abstracto** es un método que se declara **sin implementación**, es decir, sin cuerpo. Indica que todas las subclases concretas están obligadas a proporcionar su propia versión de dicho método. Los métodos abstractos solo pueden existir dentro de clases abstractas, y su función principal es **forzar un comportamiento común** en la jerarquía de herencia, dejando la implementación concreta a cada subclase.

En el ejemplo del `Soldado`, se puede redefinir la clase para que incluya un método `atacar` abstracto. Esto refleja que todo soldado debe poder atacar, pero **cada tipo de soldado lo hará de manera diferente**. La palabra clave `abstract` debe colocarse tanto en la **declaración de la clase** como en la **declaración del método abstracto**. Las subclases(`Zapador`, `Artillero`) estarán obligadas a implementar `atacar`; de lo contrario, también deberán ser abstractas.

```java
abstract class Soldado {
    void saludar() {
        System.out.println("El soldado saluda respetuosamente.");
    }

    abstract void atacar(); // Método abstracto
}

class Zapador extends Soldado {
    @Override
    void atacar() {
        System.out.println("El zapador coloca y detona explosivos.");
    }
}

class Artillero extends Soldado {
    @Override
    void atacar() {
        System.out.println("El artillero dispara con artillería pesada.");
    }
}
```

En este diseño, `Soldado` no puede instanciarse directamente, pero sí puede usarse como **tipo de referencia**, lo que permite combinar clases abstractas y polimorfismo. De este modo, se garantiza que todos los soldados tengan un comportamiento `atacar`, mientras se mantiene la flexibilidad para que cada subclase lo implemente de forma distinta.


## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

La palabra clave **`final`** en Java se utiliza para **restringir la herencia o la modificación del comportamiento**. Cuando se aplica a un **método**, indica que dicho método **no puede ser sobreescrito** por las subclases. Esto garantiza que la implementación proporcionada por la clase base será siempre la que se ejecute, independientemente de la jerarquía de herencia. Cuando se aplica a una **clase**, indica que esa clase **no puede ser heredada**, es decir, no puede tener subclases.

La relación de `final` con el **polimorfismo** es directa: el polimorfismo se basa en la sobrescritura de métodos y en la ligadura dinámica. Si un método es `final`, **se rompe la posibilidad de polimorfismo para ese método**, ya que no puede existir una versión distinta en las subclases. Del mismo modo, una clase `final` impide cualquier forma de polimorfismo basada en herencia, puesto que no se pueden crear tipos derivados que especialicen su comportamiento.

El uso de `final` es común cuando se quiere **asegurar un comportamiento fijo**, ya sea por razones de seguridad, de diseño o de eficiencia. En algunos casos, el compilador puede optimizar llamadas a métodos `final` porque sabe que no serán sobreescritos, resolviéndose el enlace de forma estática. Esto refuerza la idea de que `final` limita deliberadamente la flexibilidad del polimorfismo a cambio de control y previsibilidad.

Un ejemplo conocido dentro de la **API estándar de Java** es la clase **`String`**, que es `final`. Esto impide que se creen subclases de `String` que modifiquen su comportamiento, lo cual es crucial para la seguridad, la inmutabilidad y el correcto funcionamiento del lenguaje. Otros ejemplos son clases como `Integer`, `Double` o `Math`, que también son `final` para evitar alteraciones de comportamientos fundamentales del sistema.


## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

En Java, una **interfaz** es un tipo que define un **conjunto de métodos que una clase se compromete a implementar**, pero sin proporcionar, en su forma más básica, la implementación de dichos métodos. Una interfaz expresa **qué puede hacerse**, no **cómo se hace**, y se utiliza para definir un **contrato común** entre clases no necesariamente relacionadas por herencia directa. Desde el punto de vista del polimorfismo, una interfaz permite trabajar con objetos distintos de manera uniforme siempre que implementen la misma interfaz.

Las interfaces **se parecen a las clases abstractas**, ya que ambas pueden declarar métodos sin implementación y no pueden instanciarse directamente. Sin embargo, existen diferencias importantes: una clase abstracta puede contener atributos de instancia y métodos con implementación, mientras que una interfaz (en su concepción clásica) solo declara métodos públicos y constantes. Además, mientras que una clase solo puede heredar de **una única clase abstracta**, una clase puede implementar **varias interfaces simultáneamente**.

El hecho de que una clase pueda implementar más de una interfaz resuelve una limitación clave de Java: la **ausencia de herencia múltiple de clases**. Mediante interfaces, una clase puede adquirir múltiples comportamientos distintos sin heredar implementación, evitando los problemas clásicos asociados a la herencia múltiple. Esto hace que las interfaces sean especialmente útiles para diseñar sistemas flexibles y desacoplados.

En la práctica, una interfaz se utiliza como **tipo de referencia**, de forma similar a una clase abstracta. Cuando un objeto se maneja a través de una referencia de tipo interfaz, la llamada a los métodos se resuelve en tiempo de ejecución según la clase concreta que implementa la interfaz, lo que constituye otro uso habitual del polimorfismo en Java.


## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

Se puede plantear este ejemplo definiendo una **clase abstracta `Punto`** que represente la idea general de un punto, sin fijar sus dimensiones. El método `calcularDistanciaA` se declara como **abstracto**, indicando que el cálculo de la distancia depende del tipo concreto de punto. De este modo, se obliga a que cada subtipo (`Punto2D` y `Punto3D`) implemente su propia lógica de distancia, manteniendo un diseño polimórfico.

Para garantizar que la distancia solo se calcule entre puntos **del mismo subtipo**, en cada implementación se utiliza `instanceof` junto con *downcasting*. Esto permite comprobar en tiempo de ejecución si el objeto recibido es compatible y, si lo es, convertirlo al tipo adecuado para acceder a sus coordenadas. Aunque este enfoque no es el más elegante desde el punto de vista del diseño, resulta útil para ilustrar el control explícito del tipo real del objeto y el uso de herencia junto con polimorfismo.

Este diseño se aprovecha para crear la clase `Linea`, que almacena dos referencias de tipo `Punto`. La clase `Linea` **no necesita conocer** si los puntos son 2D o 3D: simplemente delega el cálculo de la longitud en el método polimórfico `calcularDistanciaA`. Gracias a ello, la longitud de la línea se obtiene correctamente sin depender de la dimensión concreta de los puntos, lo que demuestra el desacoplamiento que aporta el polimorfismo.

```java
abstract class Punto {
    abstract double calcularDistanciaA(Punto otro);
}

class Punto2D extends Punto {
    private double x, y;

    Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto2D)) {
            throw new IllegalArgumentException("Puntos incompatibles");
        }
        Punto2D p = (Punto2D) otro; // downcasting
        double dx = x - p.x;
        double dy = y - p.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

class Punto3D extends Punto {
    private double x, y, z;

    Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto3D)) {
            throw new IllegalArgumentException("Puntos incompatibles");
        }
        Punto3D p = (Punto3D) otro; // downcasting
        double dx = x - p.x;
        double dy = y - p.y;
        double dz = z - p.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}

class Linea {
    private Punto a, b;

    Linea(Punto a, Punto b) {
        this.a = a;
        this.b = b;
    }

    double longitud() {
        return a.calcularDistanciaA(b);
    }
}
```

Este ejemplo muestra cómo una clase puede trabajar con referencias abstractas (`Punto`) y obtener un comportamiento correcto y especializado en tiempo de ejecución, combinando polimorfismo, métodos abstractos y comprobaciones dinámicas de tipo.


## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

La **herencia de interfaces** en Java consiste en que una interfaz puede **extender a otra interfaz**, heredando todos sus métodos. A diferencia de las clases, una interfaz no hereda implementación (en el modelo clásico), sino **contratos**: es decir, obliga a que cualquier clase que implemente la interfaz hija también cumpla con los métodos declarados en la interfaz padre. Esto permite construir jerarquías de capacidades de forma clara y modular.

En Java **sí existe herencia múltiple de interfaces**. Una interfaz puede extender **una o varias interfaces al mismo tiempo**, separándolas por comas. Este mecanismo no presenta los problemas típicos de la herencia múltiple de clases, porque no hay estado ni implementación compartida que pueda causar ambigüedades. Gracias a ello, una clase puede implementar una interfaz que agrupe varios comportamientos distintos definidos en interfaces independientes.

Un ejemplo típico es separar la capacidad de **leer** un fichero de la capacidad de **modificarlo**. La interfaz `Fichero` puede definir el comportamiento básico de lectura, mientras que `FicheroEscribible` extiende esa interfaz para añadir operaciones de escritura y borrado. De este modo, se puede trabajar polimórficamente con ficheros solo de lectura o con ficheros modificables, según convenga.

```java
interface Fichero {
    String leerContenido();
}

interface FicheroEscribible extends Fichero {
    void escribirContenido(String contenido);
    void eliminar();
}
```

En este diseño, cualquier clase que implemente `FicheroEscribible` estará obligada a implementar **todos los métodos**, tanto los heredados de `Fichero` como los nuevos. Esto refuerza el uso de interfaces como contratos y favorece el polimorfismo, ya que una referencia de tipo `Fichero` puede apuntar indistintamente a objetos que sean solo legibles o completamente editables sin conocer su implementación concreta.
