<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.2. Herencia

## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

En orientación a objetos, la **herencia** es un mecanismo mediante el cual una clase nueva se define a partir de otra ya existente, estableciendo una relación del tipo **“A es‑un B”**. Esto significa que una instancia de la subclase *es también* una instancia de la superclase. Por ejemplo, si *Artillero* hereda de *Soldado*, puede afirmarse que *un artillero es un soldado*. Esta relación no es solo conceptual, sino que tiene consecuencias directas en el comportamiento del programa y en cómo el compilador trata los tipos implicados.

La primera implicación importante es la **compatibilidad de tipos**. Debido a la relación “es‑un”, cualquier objeto de una subclase puede tratarse como si fuera un objeto de la superclase. Esto permite, por ejemplo, almacenar distintos tipos de soldados en una misma estructura de datos definida para *Soldado*. Gracias a esta compatibilidad, el código puede trabajar de forma más general sin conocer el subtipo concreto, lo que favorece la reutilización y simplifica el diseño.

La segunda implicación es la **herencia de estado y comportamiento**. Una subclase hereda los atributos y métodos de su superclase (respetando las reglas de visibilidad), sin necesidad de reescribirlos. De esta forma, *Artillero* y *Zapador* heredan el atributo privado `nombre` y el método `saludar()` de *Soldado*. Cada subtipo añade después su propio estado específico (cohetes o minas) y sus métodos asociados, manteniendo aquello que es común en la clase base.

```java
// Superclase
public class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}

// Subtipo Artillero
public class Artillero extends Soldado {
    private int cohetes;

    public Artillero(String nombre, int cohetes) {
        super(nombre);
        this.cohetes = cohetes;
    }

    public int getCohetes() {
        return cohetes;
    }
}

// Subtipo Zapador
public class Zapador extends Soldado {
    private int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public int getMinas() {
        return minas;
    }
}

// Uso de la compatibilidad de tipos
public class Principal {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[3];
        ejercito[0] = new Artillero("Carlos", 5);
        ejercito[1] = new Zapador("Luis", 3);
        ejercito[2] = new Artillero("Ana", 8);

        for (Soldado s : ejercito) {
            s.saludar();
        }
    }
}
```


## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

Al crear un soldado concreto, por ejemplo un `Artillero` o un `Zapador`, **siempre se ejecutan dos constructores**: primero el de la **clase base** (`Soldado`) y después el de la **subclase** concreta. El orden nunca se invierte, ya que Java garantiza que la parte del objeto correspondiente a la superclase quede correctamente inicializada antes de inicializar los atributos propios de la subclase. Por tanto, al construir un `Artillero`, se ejecuta primero el constructor de `Soldado` y, a continuación, el de `Artillero`.

La instrucción `super` dentro de un constructor sirve para **invocar explícitamente un constructor de la clase base**. De este modo, se pueden pasar los parámetros necesarios para inicializar los atributos heredados, como el `nombre` del soldado. Aunque no aparezca escrita, Java inserta implícitamente una llamada a `super()` como primera línea del constructor de la subclase, siempre que exista un constructor sin parámetros visible en la clase base. Esta llamada implícita explica por qué el constructor del padre se ejecuta incluso cuando no se menciona `super` de forma explícita.

Si la clase base **no tiene un constructor sin parámetros visible**, entonces **es obligatorio llamar a `super` explícitamente** desde la subclase, indicando los argumentos correctos. En caso contrario, el código no compila, ya que Java no sabe cómo inicializar la parte heredada del objeto. Esto obliga a que las subclases sean conscientes de cómo debe crearse correctamente la superclase, reforzando la coherencia del estado del objeto desde el momento de su construcción.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

Sí, **los atributos privados de la superclase forman parte de una instancia de la subclase en memoria**. Cuando se crea un objeto de una subclase, Java reserva memoria para **toda la cadena de herencia**, es decir, para los atributos definidos en la clase base y para los definidos en la subclase. Por tanto, un objeto `Artillero` contiene físicamente en memoria el atributo `nombre` declarado en `Soldado`, además de su propio atributo `cohetes`. No existen dos objetos separados, sino un único objeto compuesto por todas sus partes heredadas.

Sin embargo, que un atributo exista en memoria **no implica que sea accesible desde cualquier punto del código**. La visibilidad `private` restringe el acceso exclusivamente a la clase donde se declara. Por ello, aunque `nombre` está dentro del objeto `Artillero`, **no puede usarse directamente desde el código de la subclase**. Esta restricción es puramente de acceso en tiempo de compilación y no contradice el hecho de que el dato exista físicamente dentro del objeto.

En el ejemplo de `Soldado`, el atributo `nombre` es privado y pertenece a cada instancia concreta, incluida la de `Artillero`. El método `saludar()`, definido en la superclase, puede usar `nombre` porque está dentro de `Soldado`, y ese mismo método funciona correctamente cuando se invoca sobre un `Artillero`. En cambio, si desde `Artillero` se intentara acceder directamente a `nombre`, el compilador lo impediría. La forma correcta de interactuar con ese estado heredado es a través de métodos públicos o protegidos definidos en la superclase, respetando así la encapsulación.

```java
public class Artillero extends Soldado {
    private int cohetes;

    public Artillero(String nombre, int cohetes) {
        super(nombre);
        this.cohetes = cohetes;
        // System.out.println(nombre); // ERROR: nombre es private en Soldado
    }
}
```

Este comportamiento ilustra que **herencia implica reutilización de estructura y comportamiento**, pero **no rompe las reglas de encapsulación** establecidas por la clase base.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

La **compatibilidad de tipos** derivada de la herencia tiene una consecuencia directa muy importante en términos de **extensibilidad del código**. Al poder tratar cualquier subclase como si fuera un objeto de la superclase, el código que opera con referencias del tipo base no necesita modificarse cuando aparecen nuevos tipos concretos. Esto permite ampliar el sistema añadiendo nuevas clases sin tocar el código ya existente, lo cual reduce errores y facilita el mantenimiento.

Desde el punto de vista del diseño, esta propiedad hace posible programar contra **abstracciones generales** en lugar de implementaciones concretas. En el ejemplo, el código que pide a los soldados que saluden solo conoce la existencia de `Soldado` y de su método `saludar()`. No necesita saber si el objeto es un `Artillero`, un `Zapador` o cualquier otro subtipo. Esta independencia entre el código cliente y las clases concretas es una base esencial del diseño orientado a objetos y de sistemas extensibles.

Para ilustrarlo, se puede añadir un nuevo tipo de soldado, por ejemplo un `Medico`, que también hereda de `Soldado` y mantiene su propio estado específico. El código que recorre el array de `Soldado` y llama a `saludar()` no se modifica en absoluto, ya que el nuevo tipo es compatible a nivel de tipos con la superclase. La ampliación se limita únicamente a definir la nueva subclase.

```java
// Nuevo subtipo
public class Medico extends Soldado {
    private int botiquines;

    public Medico(String nombre, int botiquines) {
        super(nombre);
        this.botiquines = botiquines;
    }

    public int getBotiquines() {
        return botiquines;
    }
}

// Código existente que no cambia
Soldado[] ejercito = new Soldado[4];
ejercito[0] = new Artillero("Carlos", 5);
ejercito[1] = new Zapador("Luis", 3);
ejercito[2] = new Artillero("Ana", 8);
ejercito[3] = new Medico("Marta", 2);

for (Soldado s : ejercito) {
    s.saludar();
}
```

Este ejemplo demuestra que la compatibilidad de tipos permite **extender el sistema sin modificar el código que ya funciona**, cumpliendo uno de los objetivos fundamentales de la orientación a objetos: facilitar la evolución del software de forma segura y controlada.


## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

Sí, en Java **es perfectamente posible que una referencia del supertipo apunte a un objeto real de un subtipo**. Esto es una consecuencia directa de la relación “A es‑un B” propia de la herencia. Por ejemplo, una referencia de tipo `Soldado` puede apuntar a un objeto que en tiempo de ejecución es realmente un `Artillero` o un `Zapador`. Esta situación es habitual y es la base para trabajar con colecciones heterogéneas de objetos relacionados por herencia.

Sin embargo, **una referencia del supertipo solo permite invocar aquellos métodos que están declarados en el propio supertipo**, independientemente del tipo real del objeto al que apunte. Por tanto, aunque un `Soldado` apunte a un `Artillero`, no es posible llamar directamente a `getCohetes()` usando esa referencia, ya que dicho método no existe en la clase `Soldado`. Lo que sí ocurre es que, si un método está definido en `Soldado` y es sobrescrito en un subtipo, se ejecutará la versión correspondiente al objeto real en tiempo de ejecución.

El **upcasting** consiste en tratar un objeto de un subtipo como si fuera del supertipo. Este proceso es implícito y seguro, ya que todo objeto de la subclase es necesariamente también un objeto de la superclase. El **downcasting**, por el contrario, consiste en convertir una referencia del supertipo en una referencia del subtipo. Este proceso es explícito y potencialmente peligroso, ya que solo es válido si el objeto real pertenece realmente a ese subtipo; en caso contrario, se produce una excepción en tiempo de ejecución.

Para comprobar si un objeto es de un subtipo concreto antes de hacer un downcasting, se utiliza el operador **`instanceof`**. Este operador permite verificar el tipo real del objeto en tiempo de ejecución y evitar errores. En el siguiente ejemplo se recorre un array de `Soldado`; todos pueden saludar, pero solo si el objeto real es un `Artillero` se accede a su número de cohetes e imprime dicha información:

```java
Soldado[] ejercito = new Soldado[3];
ejercito[0] = new Artillero("Carlos", 5);
ejercito[1] = new Zapador("Luis", 3);
ejercito[2] = new Artillero("Ana", 8);

for (Soldado s : ejercito) {
    s.saludar(); // Uso válido del supertipo

    if (s instanceof Artillero) {
        Artillero a = (Artillero) s; // Downcasting seguro
        System.out.println("Cohetes disponibles: " + a.getCohetes());
    }
}
```

Este patrón ilustra cómo la herencia, junto con el sistema de tipos de Java, permite combinar código genérico con comportamientos específicos de forma controlada y segura.


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

El acceso **protegido** forma parte de los mecanismos de ocultación de información en la orientación a objetos y está estrechamente relacionado con la herencia. Un atributo o método protegido **no es completamente público ni completamente privado**: puede usarse desde la propia clase donde se declara y también desde sus subclases. La idea principal es permitir que las clases derivadas reutilicen cierto estado o comportamiento interno sin exponerlo a todo el mundo.

En Java, el acceso protegido se implementa mediante la palabra clave `protected`. A diferencia de `private`, que restringe el uso únicamente a la clase que declara el miembro, `protected` permite que las subclases accedan directamente a ese miembro, incluso cuando están definidas en otro paquete. De este modo, se mantiene la encapsulación frente al código externo, pero se flexibiliza el acceso para la herencia, que suele necesitar reutilizar parte de la implementación.

Aplicado al ejemplo, si el atributo `nombre` de `Soldado` se declara como `protected`, seguirá formando parte del estado interno del soldado y no será accesible desde cualquier clase, pero **sí podrá usarse directamente desde `Zapador` o `Artillero`**. Esto permite que una subclase utilice el nombre, por ejemplo, para mostrar mensajes más específicos al realizar una acción concreta, sin necesidad de romper la encapsulación ni de duplicar información.

```java
public class Soldado {
    protected String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}

public class Zapador extends Soldado {
    private int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public void ponerBomba() {
        System.out.println("El zapador " + nombre + " ha colocado una mina");
    }

    public int getMinas() {
        return minas;
    }
}
```

Este ejemplo muestra que el acceso protegido **equilibra encapsulación y reutilización**, permitiendo que las subclases cooperen con la implementación de la clase base sin exponer detalles internos al resto del programa.


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

En los lenguajes orientados a objetos **no existe una respuesta única** a la pregunta de si hay una clase base común para todos los objetos, ya que depende del lenguaje concreto y de su modelo de tipos. Conceptualmente, muchos lenguajes definen algún tipo raíz que representa a “cualquier objeto”, permitiendo tratar de forma uniforme a todas las instancias creadas en el sistema. Sin embargo, otros lenguajes permiten jerarquías desligadas o introducen excepciones por razones históricas o de eficiencia.

No todos los lenguajes orientados a objetos tienen una **clase base explícita y obligatoria**. Por ejemplo, en C++ no existe una clase base universal impuesta por el lenguaje: una clase puede no heredar de ninguna otra y no hay un tipo común automático para todas. En cambio, en lenguajes como Python o Ruby sí existe una clase raíz (`object` en Python) de la que derivan todos los objetos, aunque este hecho suele ser transparente para el programador. Por tanto, la existencia de una clase base universal es una decisión de diseño del lenguaje, no un requisito de la orientación a objetos.

En Java, **sí existe una clase base común para todos los objetos**, llamada `java.lang.Object`. Toda clase definida en Java hereda implícitamente de `Object` si no se especifica otra superclase. Esto significa que cualquier objeto, independientemente de su jerarquía concreta, es siempre un `Object`. Gracias a ello, todos los objetos comparten un conjunto mínimo de métodos comunes, como `toString()`, `equals()` o `hashCode()`, que pueden reutilizarse o sobrescribirse según sea necesario.

Esta decisión de diseño permite a Java tratar de forma uniforme a todos los objetos y facilita mecanismos como la compatibilidad de tipos, el uso de colecciones genéricas o la reflexión. Al mismo tiempo, refuerza la idea de que todo valor complejo en Java pertenece a una única jerarquía de herencia bien definida, con `Object` como raíz.


## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

La **herencia múltiple** es un mecanismo de la orientación a objetos mediante el cual una clase puede **heredar directamente de más de una clase base**. Esto implica que la subclase adquiere el estado y el comportamiento de todas sus superclases. Conceptualmente, permitiría modelar situaciones del tipo “A es‑un B y también es‑un C”. Aunque esta idea resulta potente, introduce problemas importantes, especialmente cuando varias superclases definen atributos o métodos con el mismo nombre.

Uno de los principales problemas de la herencia múltiple es la **ambigüedad**, conocida clásicamente como el *problema del diamante*. Si dos clases base heredan de una clase común y una subclase hereda de ambas, el compilador no puede determinar de forma unívoca qué versión de un método o atributo debe utilizarse. Además, la complejidad del modelo mental y del mantenimiento del código aumenta considerablemente, haciendo más difícil razonar sobre el comportamiento real de los objetos.

En **Java no existe herencia múltiple de clases**. Una clase solo puede extender directamente de **una única superclase** mediante la palabra clave `extends`. Esta decisión de diseño se tomó para simplificar el lenguaje y evitar los problemas de ambigüedad asociados a la herencia múltiple clásica. De este modo, la jerarquía de clases en Java es siempre un árbol bien definido, con `Object` como raíz.

No obstante, Java ofrece una alternativa controlada mediante las **interfaces**. Una clase puede implementar múltiples interfaces, lo que permite heredar múltiples *contratos de comportamiento* sin heredar estado. Al no existir atributos de instancia en las interfaces clásicas y tener reglas claras para los métodos por defecto, Java logra evitar los problemas típicos de la herencia múltiple, proporcionando a la vez una forma flexible de reutilización de comportamiento y compatibilidad de tipos.


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

En los lenguajes orientados a objetos, las **excepciones son objetos**, lo que implica que forman parte de la jerarquía de clases y pueden modelarse mediante herencia igual que cualquier otro tipo. Gracias a esto, es posible crear **excepciones personalizadas** que representen situaciones de error específicas del dominio del problema. Estas excepciones pueden contener estado propio, comportarse como cualquier otro objeto y transmitir información más rica que un simple mensaje de texto.

En Java, una excepción *no controlada* (unchecked) es aquella que **hereda de `RuntimeException`**. Este tipo de excepciones no obliga a ser declaradas en la cabecera de los métodos ni a ser capturadas explícitamente, lo que resulta adecuado para errores de lógica o estados inesperados del programa. Crear una excepción personalizada no controlada permite señalar claramente el tipo de error sin sobrecargar el código con manejo obligatorio de excepciones.

El hecho de que la excepción esté **compuesta con un objeto `Usuario`** significa que la excepción mantiene una referencia al usuario concreto que provocó el problema. Esto resulta muy útil para diagnóstico, registros (*logging*) o análisis posterior del error. En lugar de perder información al lanzar la excepción, esta queda encapsulada dentro del propio objeto excepción, respetando el enfoque orientado a objetos.

Además, es habitual permitir que una excepción **encapsule una causa subyacente**, usando el mecanismo estándar de encadenamiento de excepciones de Java. Para ello, se sobrecargan los constructores de la excepción, ofreciendo una versión simple y otra que acepte la causa (`Throwable`). Esto facilita la propagación de errores sin perder el contexto original.

```java
// Clase de dominio simple
public class Usuario {
    private String id;
    private String nombre;

    public Usuario(String id, String nombre) {
        this.id = id;
        this.nombre = nombre;
    }

    public String getId() {
        return id;
    }

    public String getNombre() {
        return nombre;
    }
}

// Excepción personalizada no controlada
public class UsuarioNoEncontradoException extends RuntimeException {

    private Usuario usuario;

    public UsuarioNoEncontradoException(Usuario usuario) {
        super("Usuario no encontrado: " + usuario.getId());
        this.usuario = usuario;
    }

    public UsuarioNoEncontradoException(Usuario usuario, Throwable causa) {
        super("Usuario no encontrado: " + usuario.getId(), causa);
        this.usuario = usuario;
    }

    public Usuario getUsuario() {
        return usuario;
    }
}
```

Este diseño muestra cómo las excepciones personalizadas permiten **modelar errores como entidades del dominio**, manteniendo coherencia, extensibilidad y un uso claro de la herencia y la composición en Java.


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

La herencia **no debería utilizarse únicamente como un mecanismo de reutilización de código** porque introduce una **relación semántica fuerte** entre clases: la relación “A es‑un B”. Al heredar, no solo se reutilizan métodos o atributos, sino que se afirma que la subclase es conceptualmente un tipo más específico de la superclase. Si esta relación no es verdadera desde el punto de vista del dominio del problema, el diseño resultante es incorrecto, aunque el código funcione. Forzar herencia solo para reutilizar implementación conduce a jerarquías artificiales y frágiles.

Otra razón importante es que la herencia crea un **acoplamiento fuerte** entre la subclase y la superclase. La subclase pasa a depender de los detalles internos y de la evolución futura de la clase base. Cambios en la superclase pueden afectar inesperadamente a todas las subclases, incluso aunque estas no se hayan modificado. Esto dificulta el mantenimiento y reduce la capacidad de evolución del sistema, especialmente cuando la jerarquía crece.

La **composición**, en cambio, permite reutilizar código sin imponer una relación “es‑un”. En lugar de heredar, una clase contiene (usa) a otra para delegar parte de su comportamiento. Esto da lugar a relaciones “tiene‑un”, que suelen ser más flexibles y mejor alineadas con el diseño modular. La composición permite cambiar la implementación interna sin afectar a los clientes y sin romper jerarquías existentes.

Por este motivo, se suele afirmar que **la herencia debe emplearse solo cuando existe una verdadera relación de especialización**, y no como una herramienta genérica de reutilización. La recomendación habitual es “**preferir composición sobre herencia**”, utilizando herencia únicamente cuando aporta sentido conceptual, compatibilidad de tipos y polimorfismo real, y no solo ahorro de código.


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

La recomendación de **favorecer la composición frente a la herencia** se basa en criterios de **flexibilidad, bajo acoplamiento y facilidad de mantenimiento**. La herencia establece una relación rígida y permanente entre clases, ya que una subclase queda estrechamente ligada a la implementación y evolución de su superclase. Cuando esta relación es incorrecta o demasiado forzada, pequeños cambios en la clase base pueden tener efectos colaterales inesperados en todas las subclases, dificultando la evolución del sistema.

La composición, en cambio, permite construir funcionalidades combinando objetos más simples, delegando responsabilidades en lugar de heredarlas. Este enfoque reduce el acoplamiento, ya que la clase que compone puede cambiar el objeto interno por otro equivalente sin alterar su interfaz pública. De este modo, el comportamiento puede variarse o ampliarse sin necesidad de modificar jerarquías de herencia ni afectar a otras clases no relacionadas, lo que facilita la adaptación del código a nuevos requisitos.

Otro aspecto clave es que la herencia expone implícitamente detalles internos de la clase base a las subclases, incluso aquellos que no estaban pensados para ser reutilizados. Esto puede provocar dependencias accidentales y violaciones de la encapsulación. La composición, al basarse en interfaces y delegación explícita, permite controlar con mayor precisión qué funcionalidades se reutilizan y cómo se accede a ellas, manteniendo fronteras más claras entre componentes.

Por estas razones, se aconseja utilizar herencia únicamente cuando exista una **verdadera relación de especialización** y se desee aprovechar la compatibilidad de tipos y el polimorfismo. En el resto de casos, especialmente cuando el objetivo principal es reutilizar comportamiento o combinar funcionalidades, la composición suele ofrecer un diseño más robusto, extensible y alineado con los principios de la programación orientada a objetos.


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

Cuando se afirma que **la herencia rompe la encapsulación**, se hace referencia a que una subclase pasa a depender de **detalles internos de la clase base** que, idealmente, deberían permanecer ocultos. La encapsulación persigue que el estado interno de una clase solo se manipule a través de una interfaz bien definida. Sin embargo, la herencia permite que las subclases conozcan y utilicen partes de la implementación de la superclase, lo que debilita esa barrera y expone internamente cómo está construida.

Esta ruptura no se refiere únicamente al uso de atributos `protected`, sino también a dependencias más sutiles. Una subclase puede empezar a asumir comportamientos concretos de métodos heredados, apoyarse en el orden de ejecución de la superclase o depender de efectos laterales no documentados. Cuando esto ocurre, la implementación interna de la clase base deja de ser libre de cambiar, ya que cualquier modificación puede afectar a las subclases, incluso si su interfaz pública no se ha alterado.

Como consecuencia, la herencia crea un **acoplamiento fuerte y bidireccional**: la subclase depende de la superclase, pero la superclase queda condicionada por las subclases existentes. Esto contradice uno de los objetivos de la encapsulación, que es permitir modificar la implementación interna sin afectar al código cliente. En cambio, con composición, la clase que reutiliza comportamiento lo hace a través de una interfaz explícita, sin acceder ni depender de los detalles internos del objeto que utiliza.

Por ello, cuando se dice que la herencia “rompe la encapsulación”, no se afirma que siempre sea incorrecta, sino que debe usarse con cuidado. La herencia es adecuada cuando existe una auténtica relación de especialización y se acepta conscientemente ese acoplamiento. En caso contrario, la composición ofrece una forma de reutilización que preserva mejor el aislamiento entre clases y mantiene la encapsulación de manera más sólida.


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

A continuación se muestran **dos formas distintas de modelar la misma situación**, ambas válidas, pero con implicaciones de diseño diferentes. En ambos casos se parte de la observación de que `Estudiante` y `Trabajador` **comparten datos comunes** (DNI y nombre). La diferencia clave está en **cómo se reutiliza esa información común**: mediante herencia o mediante composición.

***

### Opción 1: Modelado mediante **herencia**

En este enfoque se extraen los datos comunes a una superclase `Persona`, que representa el concepto general del que derivan tanto `Estudiante` como `Trabajador`. Aquí se afirma explícitamente que **un estudiante es una persona** y **un trabajador es una persona**. Esta relación es semántica, no solo técnica, y permite reutilizar directamente los atributos comunes.

La herencia proporciona compatibilidad de tipos y reutilización automática del estado, pero introduce una relación fuerte y rígida entre las clases. Cualquier cambio en `Persona` afecta potencialmente a todas las subclases.

```java
public class Persona {
    protected String dni;
    protected String nombre;

    public Persona(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }
}

public class Estudiante extends Persona {
    public Estudiante(String dni, String nombre) {
        super(dni, nombre);
    }
}

public class Trabajador extends Persona {
    public Trabajador(String dni, String nombre) {
        super(dni, nombre);
    }
}
```

***

### Opción 2: Modelado mediante **composición**

En este caso, los datos comunes se encapsulan en una clase independiente llamada `DatosPersonales`. Tanto `Estudiante` como `Trabajador` **no heredan de una clase común**, sino que *tienen* unos datos personales. Se establece así una relación “tiene‑un” en lugar de “es‑un”.

Este enfoque favorece la encapsulación y reduce el acoplamiento. Las clases `Estudiante` y `Trabajador` pueden evolucionar de forma independiente y reutilizan los datos comunes sin quedar ligadas a una jerarquía fija. Además, el uso de composición deja claro que lo compartido es solo la información, no el rol ni el comportamiento.

```java
public class DatosPersonales {
    private String dni;
    private String nombre;

    public DatosPersonales(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getNombre() {
        return nombre;
    }
}

public class Estudiante {
    private DatosPersonales datos;

    public Estudiante(DatosPersonales datos) {
        this.datos = datos;
    }
}

public class Trabajador {
    private DatosPersonales datos;

    public Trabajador(DatosPersonales datos) {
        this.datos = datos;
    }
}
```

***

Este ejemplo muestra con claridad que **herencia y composición pueden resolver el mismo problema**, pero con compromisos distintos. La herencia expresa una relación conceptual fuerte y aporta compatibilidad de tipos, mientras que la composición prioriza la modularidad, la reutilización flexible y una mejor preservación de la encapsulación.
