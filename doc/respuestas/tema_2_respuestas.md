<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

La encapsulación en la Programación Orientada a Objetos busca agrupar dentro de una misma clase tanto los datos como los métodos que operan sobre esos datos. Su propósito es asegurar que el estado interno del objeto esté controlado y solo pueda modificarse a través de interfaces bien definidas. Por su parte, la ocultación de información (information hiding) pretende restringir el acceso directo a los atributos internos, exponiendo únicamente lo necesario para usar el objeto sin revelar detalles de implementación que no son relevantes para el exterior.
Estas técnicas permiten que el diseño de una clase sea más robusto y mantenible. Al ocultar detalles internos, se evita que otras partes del código dependan de ellos, lo que facilita cambiar la implementación sin afectar al resto del programa. Además, se reduce la posibilidad de errores provocados por un mal uso de los datos internos, ya que el acceso queda regulado por métodos controlados, como getters y setters. También contribuyen a mejorar la seguridad del código y promueven una programación más modular y comprensible.

## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

La interfaz pública de una clase u objeto se entiende como el conjunto de métodos y atributos accesibles desde fuera de dicha clase. Representa la “puerta de entrada” mediante la cual otros objetos pueden interactuar con él. En Java, esta interfaz está constituida por los miembros declarados como public, y define qué operaciones pueden realizarse sin necesidad de conocer cómo están implementadas internamente. De este modo, se presenta una visión simplificada del objeto, mostrando únicamente lo necesario para su uso.
Su relación con la ocultación de información es directa. Al exponer solo la interfaz pública, se ocultan los detalles internos como variables privadas o métodos auxiliares, lo que permite proteger el estado del objeto y evitar dependencias indeseadas entre módulos. Además, esa separación clara hace posible modificar la implementación interna sin afectar al código que utiliza la clase, siempre que la interfaz pública se mantenga estable. Así se promueve un diseño más modular, mantenible y seguro.

## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

La interfaz pública de una clase debe diseñarse con cuidado porque actúa como el contrato que otros componentes del programa utilizarán para interactuar con ella. Una vez que otros módulos dependen de ese contrato, cualquier modificación puede afectar a múltiples partes del sistema. Por ello, se considera esencial pensar qué métodos deben exponerse realmente y cuáles deberían permanecer ocultos, evitando así que detalles internos queden accesibles sin necesidad. Diseñar la interfaz pública de forma deliberada ayuda a mantener el control sobre cómo se usa la clase y a prevenir dependencias innecesarias.
Cambiar una interfaz pública no suele ser fácil. Incluso cuando los cambios parecen pequeños, pueden provocar incompatibilidades en todo el código que utiliza la clase. Esto obliga a modificar también ese código externo, aumentando el coste de mantenimiento y el riesgo de introducir errores. Por este motivo, suele recomendarse mantener la interfaz pública lo más estable posible y exponer solo lo estrictamente necesario, de modo que la implementación interna pueda evolucionar sin afectar a los usuarios de la clase.

## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

Las **invariantes de clase** se entienden como condiciones que siempre deben cumplirse para que un objeto se considere válido durante todo su ciclo de vida. Estas condiciones suelen referirse a restricciones sobre los atributos —por ejemplo, que un valor no sea negativo o que un estado solo pueda adoptar ciertos rangos permitidos—. Aunque pueden verificarse explícitamente dentro de los métodos, el concepto en sí se refiere a mantener la coherencia interna de un objeto sin necesidad de comprobar continuamente su validez desde el exterior.

La **ocultación de información** ayuda directamente a mantener estas invariantes porque evita que el código externo modifique los atributos de manera arbitraria. Cuando los datos están protegidos mediante `private`, todos los cambios deben pasar por métodos controlados (getters o setters), que pueden imponer validaciones antes de aceptar un nuevo valor. Así se garantiza que las invariantes no se rompan accidentalmente, ya que la clase conserva el control absoluto sobre cómo se accede y se modifica su estado interno.

Además, esta ocultación permite aislar la lógica interna para que pueda evolucionar sin afectar al código que utiliza la clase. De este modo, si se necesita modificar las reglas de validación o ampliar la lógica asociada a la invariante, basta con ajustar los métodos que gestionan los datos, manteniendo intacta la interfaz pública. Esto refuerza la fiabilidad y la robustez del diseño orientado a objetos, facilitando un desarrollo más seguro y un mantenimiento más sencillo.


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

A continuación se muestra una clase `Punto` en Java que **encapsula** sus datos (`x` e `y`) usando campos `private` y expone solo una **interfaz pública** mínima para crear puntos, leer sus coordenadas y calcular la distancia al origen. Se usa `Math.hypot` para evitar problemas de precisión al calcular la raíz de la suma de cuadrados.

```java
public final class Punto {
    // Campos ocultos (información encapsulada)
    private final double x;
    private final double y;

    // Interfaz pública: constructor y métodos que pueden usar los clientes
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Lectura controlada (sin setters => objeto inmutable)
    public double getX() { return x; }
    public double getY() { return y; }

    // Comportamiento relevante expuesto
    public double calcularDistanciaAOrigen() {
        // hipot calcula sqrt(x^2 + y^2) de forma numéricamente estable
        return Math.hypot(x, y);
    }
}
```

La **interfaz pública** de la clase `Punto` está formada por todo lo que es accesible desde fuera de la clase: su **constructor público** `Punto(double, double)`, los **métodos públicos** `getX()`, `getY()` y `calcularDistanciaAOrigen()`. En cambio, los **detalles internos** (los campos `x` e `y`) quedan **ocultos** (`private`) y no se pueden modificar directamente desde el código cliente. Esto permite cambiar la implementación interna (por ejemplo, validar datos, cambiar la representación, añadir cacheo) sin romper a quien usa la clase.

En Java, `public` significa **accesible desde cualquier lugar** (cualquier clase de cualquier paquete puede invocarlo). `private` significa **accesible solo dentro de la propia clase**, ni siquiera las subclases o clases del mismo paquete pueden verlo. Esta diferencia materializa la **ocultación de información**: se decide qué formar parte del “contrato” visible y qué permanecer como detalle interno. En comparación con C/C no OO, podría verse como exponer solo las funciones del “header” y mantener la estructura concreta oculta en el `.c`; aquí, los campos son privados y el acceso se canaliza por métodos públicos que preservan invariantes.


## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

En Java, los modificadores public y private pueden aplicarse tanto a clases, como a miembros internos de una clase (atributos, métodos y constructores). En el caso de las clases, solo pueden llevar public o no llevar modificador (lo que implica acceso package‑private). El modificador private no puede aplicarse a clases de nivel superior, únicamente a clases anidadas dentro de otra clase. De este modo, Java permite controlar si una clase es visible desde cualquier paquete o únicamente desde el paquete donde se declara.
En lo referente a los miembros internos de una clase (atributos, métodos, constructores y clases internas), sí pueden usarse tanto public como private. El modificador private restringe el acceso exclusivamente a la propia clase, lo que resulta fundamental para la encapsulación. Por su parte, public permite que el miembro sea accesible desde cualquier otra clase, sin restricciones de paquete. Estos modificadores permiten definir con claridad qué parte de la implementación constituye la interfaz pública y qué parte pertenece a los detalles internos, manteniendo así un diseño más seguro y modular.
Si se trata de clases internas (nested classes), pueden declararse public, private, protected o sin modificador. Esto permite encapsular incluso tipos completos dentro de otros tipos, algo útil para clases auxiliares que no deben ser visibles desde el exterior. Así, el sistema de modificadores de acceso en Java ofrece distintos niveles de visibilidad, lo que facilita el control preciso de cómo se expone o se oculta la funcionalidad de cada componente del código.

## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

En programación orientada a objetos, la visibilidad no se limita únicamente a los niveles público y privado. Muchos lenguajes incluyen niveles intermedios que permiten ajustar con más precisión qué partes del código son accesibles desde distintos contextos. La idea general es proporcionar control fino sobre qué elementos forman parte de la interfaz pública y cuáles pertenecen a los detalles internos. Este conjunto de niveles de visibilidad suele variar entre lenguajes, pero todos buscan equilibrar la encapsulación con la usabilidad.
En Java existen cuatro niveles de visibilidad: public, private, protected y el nivel package‑private (sin modificador). public permite acceso desde cualquier lugar, mientras que private restringe el acceso solo a la clase. El nivel package‑private permite el acceso únicamente dentro del mismo paquete, y protected amplía ese acceso al paquete y a las subclases aunque estén en paquetes diferentes. Este conjunto de niveles ofrece una gradación suficientemente precisa para organizar código en módulos lógicos (paquetes) y controlar la herencia sin exponer detalles innecesarios.
En otros lenguajes aparecen variaciones interesantes del mismo concepto. En C++, por ejemplo, existen tres niveles tradicionales (public, private, protected), pero también se introducen mecanismos adicionales como friend classes y friend functions, que permiten otorgar accesos específicos a determinadas entidades externas. En lenguajes como C# existen niveles adicionales como internal (visible dentro del ensamblado) o protected internal y private protected, que combinan restricciones de herencia y ensamblado. Otros lenguajes, como Python, no imponen visibilidad estricta, pero usan convenciones como el guion bajo para indicar atributos internos, confiando en la disciplina del programador en lugar de imponer restricciones rígidas.
Así, la visibilidad en POO no se limita a un simple binario entre público o privado, sino que constituye un mecanismo flexible que depende del lenguaje. Java adopta un modelo de cuatro niveles basado en clases, herencia y paquetes, mientras que otros lenguajes amplían o simplifican esta idea según sus principios de diseño. En todos los casos, el objetivo es el mismo: permitir encapsular correctamente los detalles internos y exponer solo lo que forma parte del contrato que debe conocer el usuario de una clase.

## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

**Respuesta breve:**  
En Java, los miembros de instancia `private` **están ocultos frente a otras clases** (opción **a**), pero **no están ocultos entre instancias de la misma clase** (la opción **b** es falsa). Es decir, cualquier **método de la propia clase** puede acceder a los campos `private` **de cualquier objeto de ese mismo tipo**, no solo a los suyos. Esta regla permite implementar operaciones que comparan o combinan el estado de dos instancias sin romper la encapsulación hacia el exterior.

Se muestra un ejemplo con un método `calcularDistanciaAPunto(Punto otro)` dentro de la misma clase. Obsérvese que el método accede **directamente** a `otro.x` y `otro.y` aun siendo privados, porque el acceso ocurre **desde código de la clase `Punto`**. Si se intentara leer esos campos desde otra clase, el compilador lo prohibiría.

```java
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double calcularDistanciaAOrigen() {
        return Math.hypot(x, y);
    }

    public double calcularDistanciaAPunto(Punto otro) {
        // Acceso permitido: se está dentro de la clase Punto,
        // así que puede leerse x e y de "otro" aunque sean private.
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.hypot(dx, dy);
    }

    public double getX() { return x; }
    public double getY() { return y; }
}
```

En resumen, la **ocultación** que proporciona `private` se aplica a **fronteras de clase**, no a **fronteras entre instancias**. Por ello, los métodos de `Punto` pueden comparar estados de dos `Punto` sin exponer sus campos al exterior. Esto conserva la encapsulación (otras clases no ven los detalles internos) a la vez que permite implementar comportamientos naturales (como calcular distancias) de forma eficiente y segura.


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

Los métodos *getter* y *setter* son funciones utilizadas para acceder y modificar atributos privados de un objeto sin exponerlos directamente. Un *getter* permite **leer** el valor de un atributo encapsulado, mientras que un *setter* permite **cambiar** ese valor pasando por un punto de control que puede validar o restringir la modificación. Estos métodos se emplean para mantener la encapsulación y asegurar que el estado interno del objeto solo se manipule de forma segura y coherente.


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

No. Cuando se dice que la ocultación de información mejora la “seguridad” del programa, no se hace referencia a evitar que el sistema sea *hackeado* en el sentido habitual de ataques externos. El término se usa en el contexto de diseño de software y se refiere a la **seguridad del estado interno del objeto**, es decir, a evitar que otras partes del programa modifiquen o accedan indebidamente a datos que deberían estar controlados. Esta seguridad está orientada a la **corrección lógica** y a mantener invariantes, no a la protección frente a atacantes reales.

La ocultación de información evita que código externo pueda dejar un objeto en un estado inválido o incoherente. Al limitar el acceso directo a los atributos, solo los métodos autorizados pueden realizar cambios, lo que reduce errores, inconsistencias y efectos colaterales inesperados. De este modo, el programa es "más seguro" en el sentido de que se comporta de forma más predecible y robusta, manteniendo un control claro sobre su propio estado interno.

Aunque la encapsulación puede contribuir indirectamente a mejorar la seguridad real —por ejemplo, dificultando usos indebidos de datos sensibles dentro del propio código— no constituye un mecanismo de defensa frente a ataques externos. En ese ámbito se necesitan medidas específicas, como controles de acceso, criptografía, validación de entradas o políticas de seguridad del sistema. Aquí, el término “seguridad” debe entenderse como **protección del diseño y la integridad interna del software**, no como ciberseguridad.


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

Un **miembro de instancia** es un atributo o método cuyo valor pertenece a **cada objeto individual**. Cada instancia de la clase mantiene su propia copia del estado asociado a esos miembros. Esto implica que modificar un miembro de instancia en un objeto no afecta a los demás. Este tipo de miembros representa el comportamiento y los datos que varían entre objetos distintos, como las coordenadas `x` e `y` de un punto o el saldo de una cuenta bancaria.

Un **miembro de clase** (también llamado *static*) pertenece a la **clase en sí misma** y no a cada objeto. Solo existe una única copia compartida entre todas las instancias, de modo que su valor es global dentro del contexto de esa clase. Estos miembros se utilizan para almacenar información común a todas las instancias o para implementar funciones auxiliares que no dependen del estado individual de un objeto concreto. Su acceso se realiza a través del nombre de la clase, lo que refuerza la idea de que no forman parte del estado de los objetos.

Los miembros de clase también pueden **ocultarse** utilizando los mismos modificadores de acceso que los miembros de instancia. Declararlos `private` impide que otras clases accedan directamente a ellos, lo que permite mantener controlado su uso a través de métodos públicos o protegidos. Esta capacidad resulta útil cuando se gestionan recursos compartidos o se mantienen contadores internos cuya manipulación directa podría comprometer la coherencia global de la clase.


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

Sí, tiene sentido que los constructores sean privados en ciertos diseños. Un constructor privado impide que otras clases creen instancias libremente, lo que permite a la propia clase controlar completamente el proceso de creación. Este enfoque es habitual cuando se desea limitar el número de objetos existentes, como ocurre en el patrón *singleton*, donde solo puede existir una única instancia gestionada internamente.

También es útil cuando la clase ofrece métodos de creación específicos (*factory methods*) que devuelven objetos ya configurados o reutilizados, ocultando así los detalles del proceso de construcción. Asimismo, resulta conveniente en clases que solo contienen miembros estáticos y no deben ser instanciadas; en estos casos, declarar un constructor privado evita instancias accidentales y deja claro que la clase actúa como contenedor de utilidades.


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.
```java
public final class Punto {
    // Estado de instancia (encapsulado)
    private final double x;
    private final double y;

    // Miembros de clase (compartidos por todas las instancias)
    private static double maxX = Double.NEGATIVE_INFINITY;
    private static double maxY = Double.NEGATIVE_INFINITY;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
        // Actualización de máximos globales (de clase)
        if (x > maxX) maxX = x;
        if (y > maxY) maxY = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    public double calcularDistanciaAOrigen() {
        return Math.hypot(x, y);
    }

    // Métodos de clase para consultar los máximos globales
    public static double getMaxX() { return maxX; }
    public static double getMaxY() { return maxY; }
}
```

## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

```java
public static Punto crearRedondeado(double x, double y) {
    return new Punto(Math.rint(x), Math.rint(y));
}
```

Sí, se ha usado `static` porque un método factoría pertenece a la **clase**, no a una instancia concreta.


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

```java
public final class Punto {
    // Representación interna cambiada a un array de dos posiciones
    private final double[] coord;

    // Misma interfaz pública: constructor y métodos
    public Punto(double x, double y) {
        this.coord = new double[] { x, y };
    }

    public double getX() { return coord[0]; }
    public double getY() { return coord[1]; }

    public double calcularDistanciaAOrigen() {
        return Math.hypot(coord[0], coord[1]);
    }

    public double calcularDistanciaAPunto(Punto otro) {
        double dx = this.coord[0] - otro.coord[0];
        double dy = this.coord[1] - otro.coord[1];
        return Math.hypot(dx, dy);
    }

    // (Opcional, ya definido anteriormente) Método factoría conservando la firma
    public static Punto crearRedondeado(double x, double y) {
        return new Punto(Math.rint(x), Math.rint(y));
    }
}
```

La **interfaz pública** se mantiene (constructor, `getX`, `getY`, `calcularDistanciaAOrigen` y `calcularDistanciaAPunto`), mientras que cambia únicamente la **representación interna**: en lugar de dos `double` separados, se usa un `double[]` de tamaño 2. La encapsulación asegura que este cambio sea transparente para el código cliente, al no exponerse el array en la API.

Se conserva la **inmutabilidad observable**: el array es `private final` y nunca se expone, por lo que no hay aliasing externo que pueda modificar el estado. Este enfoque permite, en el futuro, extender o sustituir la representación interna sin romper a los consumidores de la clase, manteniendo las mismas pre/postcondiciones de los métodos públicos.


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

Normalmente no es conveniente declarar un atributo como público aunque exista un *getter* y un *setter* que simplemente devuelvan o modifiquen su valor. La razón es que, al exponer el atributo directamente, se pierde la capacidad de controlar su acceso o su modificación en el futuro. Un atributo público queda totalmente abierto a cualquier código externo, y si posteriormente se desea añadir validaciones, restricciones o efectos colaterales, ya no es posible hacerlo sin romper compatibilidad con el código que depende de él. En cambio, mantener el atributo privado y acceder mediante métodos permite introducir dichos controles sin necesidad de modificar cómo se usa la clase desde fuera.

La convención habitual en Java —y en la mayoría de lenguajes orientados a objetos— es declarar **todos los atributos como privados** y proporcionar *getters* y *setters* solo cuando realmente son necesarios. Esta convención busca reforzar la encapsulación, de modo que el estado interno de un objeto quede protegido de modificaciones externas no controladas. Incluso en casos donde se necesitan ambos métodos, se mantiene la privacidad del atributo porque estos métodos proporcionan un punto centralizado para gestionar el acceso y la asignación.

Este enfoque está profundamente relacionado con la idea de **invariantes de clase**. Una invariante de clase es una condición que debe cumplirse siempre para que el estado interno del objeto sea válido, por ejemplo: “la edad debe ser siempre mayor o igual a cero”, o “la lista interna nunca puede ser nula”. Cuando los atributos son privados, las invariantes pueden mantenerse porque cualquier cambio en el estado debe pasar por los *setters*, donde pueden verificarse las condiciones necesarias. Si los atributos fueran públicos, se perdería la capacidad de garantizar estas invariantes, ya que cualquier parte del programa podría modificar el estado sin respetar dichas reglas.

Por tanto, declarar atributos como privados no es solo una convención estilística, sino una herramienta esencial para preservar la integridad del objeto. A través de los *getters* y *setters*, se controla el flujo de acceso y modificación, permitiendo que la clase mantenga sus invariantes y evitando que el estado evolucione hacia configuraciones inválidas o inconsistentes. Esta separación clara entre estado interno y uso externo es uno de los pilares de la encapsulación en la programación orientada a objetos.


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

Una clase **inmutable** es aquella cuyo estado no puede cambiar después de haberse creado el objeto. Esto implica que todos sus atributos deben inicializarse en el constructor y no modificarse posteriormente. Para lograrlo, suele declararse los atributos como `private` y `final`, y no se proporcionan métodos que alteren su valor. De esta forma, una vez que el objeto ha sido construido, se garantiza que su contenido permanecerá constante durante toda su vida útil, lo que facilita razonar sobre su comportamiento y evita efectos colaterales inesperados.

Un **método modificador** es cualquier método que altere el estado interno del objeto, ya sea cambiando el valor de un atributo o modificando estructuras internas (como añadir elementos a una lista). Aunque un *setter* es un tipo de método modificador, no todos los métodos modificadores son *setters*. Por ejemplo, un método que incremente un contador, que elimine un elemento de una colección o que cambie parcialmente un atributo complejo también entra dentro de esta categoría. La característica común es que todos ellos modifican el estado interno del objeto y, por tanto, afectan a su inmutabilidad.

En una clase inmutable, evidentemente, no se incluyen métodos modificadores, ya que estos romperían la garantía de que el estado no cambia. Esto trae consigo varias ventajas importantes. La primera es la **seguridad frente a errores**, ya que no existe riesgo de que otro código altere el estado de manera imprevista. La segunda es la **facilidad para razonar y depurar**, ya que un objeto inmutable siempre representa exactamente el mismo valor desde su creación. La tercera es la **seguridad en concurrencia**, pues los objetos inmutables pueden compartirse entre hilos sin necesidad de sincronización explícita.

Además, las clases inmutables encajan bien con patrones de diseño funcional y con estructuras de datos seguras frente a modificaciones accidentales. En lenguajes como Java, clases como `String`, `Integer` o `LocalDate` son inmutables por estas razones. Este enfoque no siempre es adecuado para todas las situaciones, pero cuando es posible utilizarlo, suele aportar claridad, robustez y menos posibilidades de introducir errores relacionados con estados inconsistentes.


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

No es recomendable incluir métodos *setter* de forma automática ni como una convención general. La práctica habitual en diseño orientado a objetos consiste en añadir solo aquellos *setters* que sean realmente necesarios para el funcionamiento correcto de la clase. Incluir *setters* sin necesidad puede exponer el estado interno del objeto de forma innecesaria, debilitando la encapsulación y permitiendo modificaciones que quizá no deberían permitirse. Esto puede hacer que la clase sea más difícil de mantener y que su estado pueda llegar a situaciones inconsistentes.

El uso indiscriminado de *setters* suele ser un síntoma de que la clase no está bien diseñada en términos de responsabilidades. Muchas veces, en lugar de permitir que código externo modifique datos libremente, puede ser preferible proporcionar métodos que expresen acciones específicas o reglas de negocio concretas. Por ejemplo, en vez de permitir cambiar un atributo `saldo` con un *setter*, podría existir un método `ingresar()` o `retirar()`, que controle internamente las condiciones necesarias para mantener la validez del objeto.

Además, evitar los *setters* cuando no son imprescindibles favorece la creación de clases más robustas e incluso permite acercarse a patrones de inmutabilidad parcial o total. Si algunos atributos no deben cambiar nunca después de la construcción del objeto, la ausencia de *setters* ayuda a transmitir esa intención y refuerza las invariantes de la clase. De este modo, se reduce la posibilidad de errores derivados de cambios inesperados en el estado.

En resumen, los *setters* deben verse como herramientas útiles pero no como requisitos obligatorios. Es preferible analizarlos caso por caso y decidir si aportan valor o, por el contrario, exponen demasiado la estructura interna del objeto. La prioridad debe ser siempre proteger el estado y garantizar que la clase mantiene un comportamiento coherente y estable en todo momento.


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

La clase `String` en Java es **inmutable**. Esto significa que una vez creado un objeto `String`, su contenido no puede modificarse. Cuando se realiza una operación que parece alterar una cadena, en realidad no se modifica el objeto original, sino que se crea uno nuevo con el resultado de la operación. Este comportamiento está diseñado para asegurar la seguridad, la coherencia y la eficiencia en contextos como la concurrencia o el uso de cadenas como claves en estructuras de datos.

Cuando se concatenan dos cadenas con el operador `+`, lo que ocurre internamente es la creación de un nuevo objeto `String` que contiene el resultado de la concatenación. El proceso implica copiar el contenido de ambas cadenas, lo que puede resultar costoso si se repite muchas veces. En código aparentemente inofensivo como `cadena = cadena + "algo";`, cada concatenación genera un nuevo objeto y provoca múltiples copias intermedias, especialmente dentro de bucles.

Si se necesita construir una cadena extensa mediante múltiples concatenaciones, lo recomendable es utilizar **`StringBuilder`** (o `StringBuffer` si se requiere sincronización). Estas clases son mutables y permiten añadir texto sin crear nuevos objetos en cada paso. Por ejemplo:

```java
StringBuilder sb = new StringBuilder();
sb.append("Hola");
sb.append(" ");
sb.append("mundo");
String resultado = sb.toString();
```

Este enfoque evita el coste de crear y copiar repetidamente objetos `String`, y es la solución estándar cuando se construyen cadenas de manera incremental. De este modo, se gana eficiencia y se reduce el impacto en memoria y tiempo de procesamiento, especialmente en escenarios con iteraciones numerosas o concatenaciones intensivas.


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

En programación orientada a objetos, los objetos pueden compararse por **identidad** o por **contenido**. La identidad se refiere a que dos referencias apuntan exactamente al mismo objeto en memoria, mientras que el contenido se refiere a que ambos objetos representan el mismo estado interno, aunque sean instancias distintas. En Java, el operador `==` compara siempre la **identidad**, y esto vale tanto para objetos de clases propias como para objetos de bibliotecas estándar.

El método `equals()` es el mecanismo que Java proporciona para comparar objetos por **contenido lógico**. Es un método heredado de `Object`, y su implementación por defecto compara exactamente igual que el operador `==`, es decir, compara identidades. Para que una clase pueda compararse por contenido, debe **sobrescribirse** `equals()` definiendo qué significa que dos instancias tienen el mismo valor o estado. En paralelo, cuando se sobrescribe `equals()`, también se debe sobrescribir `hashCode()` para mantener la coherencia al usar objetos en colecciones como `HashMap` o `HashSet`.

En el caso concreto de las cadenas, Java sobrescribe `equals()` en la clase `String` para que compare el **contenido textual** en lugar de la identidad del objeto. Por ese motivo, dos cadenas distintas en memoria pero con el mismo texto se consideran iguales al usar `equals()`. Por ejemplo: `"hola".equals(new String("hola"))` devuelve `true`, aunque las referencias sean diferentes.

Por lo tanto, para comparar cadenas en Java, siempre debe utilizarse el método **`equals()`** o, si se desea ignorar mayúsculas y minúsculas, **`equalsIgnoreCase()`**. Usar el operador `==` para este propósito puede dar resultados erróneos, excepto en casos muy específicos donde ambas referencias provengan del *string pool*, lo cual no debe considerarse como una forma fiable de comparación.


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

Las **clases *wrapper*** son clases que encapsulan un tipo primitivo dentro de un objeto. En lenguajes como Java, los tipos primitivos (`int`, `double`, `boolean`, etc.) no poseen métodos ni forman parte del sistema completo de herencia. Para integrarlos en estructuras que requieren objetos —como colecciones de la biblioteca estándar— se utilizan clases que “envuelven” ese valor primitivo. Así, cada tipo primitivo tiene su clase asociada: `Integer`, `Double`, `Boolean`, entre otras. Estas clases permiten tratar los valores como objetos y acceder a métodos útiles, como conversiones de formato o comparaciones avanzadas.

En Java, este proceso puede hacerse explícitamente creando un objeto *wrapper*, por ejemplo: `Integer x = new Integer(5);`, aunque esa sintaxis ya no se recomienda. Desde Java 5, el lenguaje incorpora **autoboxing** y **unboxing**, que realizan la conversión entre primitivo y wrapper de manera automática. Por ejemplo, al asignar `Integer x = 5;`, el compilador convierte implícitamente ese `5` en un `Integer`. A la inversa, cuando un `Integer` participa en una operación aritmética, se convierte automáticamente en `int`. Esto facilita el trabajo del programador eliminando código repetitivo y permitiendo que los primitivos encajen sin esfuerzo en contextos orientados a objetos.

Las clases *wrapper* ofrecen varias ventajas. En primer lugar, permiten utilizar tipos primitivos en estructuras que requieren objetos, como `ArrayList<Integer>`. También proporcionan métodos adicionales, como `Integer.parseInt()` o `Double.isNaN()`, que enriquecen el manejo de datos. Asimismo, posibilitan representar valores especiales como `null`, algo imposible con primitivos. Esto es útil en bases de datos, operaciones opcionales o cuando se necesita distinguir entre "valor cero" y "valor no asignado".

No todos los lenguajes orientados a objetos necesitan *wrappers*, porque no todos distinguen entre tipos primitivos y tipos objeto. Por ejemplo, lenguajes como **Python** o **Ruby** tratan todos los valores como objetos, por lo que no requieren envoltorios adicionales. En cambio, lenguajes como **Java** o **C#** sí poseen tipos primitivos por razones de eficiencia, y por ello necesitan clases o estructuras que actúen como contenedores. La existencia de wrappers depende, por tanto, del diseño del lenguaje y del equilibrio que busque entre rendimiento y uniformidad del sistema de tipos.


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

Un **tipo de dato enumerado** en programación orientada a objetos es un conjunto finito y cerrado de valores posibles que representan estados o categorías bien definidas. Su función es ofrecer un tipo seguro que solo pueda tomar uno de esos valores permitidos, evitando errores como usar cadenas arbitrarias o números sin significado. Esto resulta especialmente útil cuando se trabaja con conceptos que poseen un número limitado de opciones, como colores, direcciones, niveles de prioridad o estados de un proceso.

En Java, un enumerado (**`enum`**) es efectivamente una **clase especial**, más concreta y restringida que una clase convencional. Cada valor definido dentro del `enum` es una instancia única y constante de esa clase. Además, un enumerado puede incluir métodos, atributos y constructores privados, lo que permite encapsular comportamiento asociado a cada valor. A pesar de su naturaleza especial, se trata de un tipo de referencia y se comporta de manera similar a un objeto, aunque con características adicionales como la garantía de unicidad de sus instancias.

Desde el punto de vista de la encapsulación, los enumerados en Java ofrecen ventajas significativas. En primer lugar, restringen de forma estricta el conjunto de valores permitidos, lo que mejora la fiabilidad y reduce errores al evitar que se creen valores inválidos. En segundo lugar, permiten asociar comportamiento directamente a cada valor del enumerado, encapsulando tanto datos como operaciones relacionadas dentro de un mismo tipo, lo que refuerza la cohesión. Esta combinación de valores controlados y comportamiento localizado contribuye a mantener invariantes claras y un diseño más robusto.

Además, los enumerados evitan exponer detalles internos innecesarios, ya que su constructor es siempre privado y no pueden crearse instancias adicionales desde el exterior. Este control total sobre su ciclo de vida simplifica la gestión del estado y garantiza que el sistema opere dentro de límites bien definidos. Gracias a estas propiedades, los `enum` de Java representan una mejora sustancial frente a enumeraciones más simples de otros lenguajes y constituyen una herramienta eficaz para diseñar código más seguro y mantenible.


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado. Añade además cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

Se propone un `enum` llamado `Mes` con doce instancias, cada una con dos atributos privados: el **ordinal en el año (1–12)** y los **días en año no bisiesto**. Se exponen métodos para leer ambos datos: `ordinalEnAnio()` y `dias()`, además de una variante `dias(boolean esBisiesto)` que ajusta **febrero** a 29 días cuando corresponda. De este modo, se mantiene la encapsulación (atributos `private final`) y se utiliza un **constructor privado del propio `enum`** para inicializar cada constante.

Adicionalmente, se incluyen cuatro métodos estacionales —`esDePrimavera`, `esDeVerano`, `esDeOtono`, `esDeInvierno`— que reciben un booleano indicando si se evalúa el **hemisferio norte**. Se ha seguido la convención **meteorológica** (MAM, JJA, SON, DJF) e invertida en el hemisferio sur, interpretando “tiene algunos días de…” como pertenencia del mes a esa estación. Para el ordinal 1–12 se evita usar `ordinal()` de `Enum` (que es 0–11) y se ofrece un método explícito que devuelve el índice humano.

```java
public enum Mes {
    ENERO(1, 31),
    FEBRERO(2, 28),
    MARZO(3, 31),
    ABRIL(4, 30),
    MAYO(5, 31),
    JUNIO(6, 30),
    JULIO(7, 31),
    AGOSTO(8, 31),
    SEPTIEMBRE(9, 30),
    OCTUBRE(10, 31),
    NOVIEMBRE(11, 30),
    DICIEMBRE(12, 31);

    private final int numero;          // ordinal 1..12
    private final int diasNoBisiesto;  // días en año no bisiesto

    Mes(int numero, int diasNoBisiesto) {
        this.numero = numero;
        this.diasNoBisiesto = diasNoBisiesto;
    }

    /** Ordinal del mes en el año (1..12). */
    public int ordinalEnAnio() {
        return numero;
    }

    /** Días del mes asumiendo año no bisiesto (febrero = 28). */
    public int dias() {
        return diasNoBisiesto;
    }

    /** Días del mes, ajustando febrero si es año bisiesto. */
    public int dias(boolean esBisiesto) {
        return (this == FEBRERO && esBisiesto) ? 29 : diasNoBisiesto;
    }

    /** ¿El mes tiene días de primavera? */
    public boolean esDePrimavera(boolean esHemisferioNorte) {
        if (esHemisferioNorte) {
            switch (this) {
                case MARZO:
                case ABRIL:
                case MAYO:
                    return true;
                default:
                    return false;
            }
        } else {
            switch (this) {
                case SEPTIEMBRE:
                case OCTUBRE:
                case NOVIEMBRE:
                    return true;
                default:
                    return false;
            }
        }
    }

    /** ¿El mes tiene días de verano? */
    public boolean esDeVerano(boolean esHemisferioNorte) {
        if (esHemisferioNorte) {
            switch (this) {
                case JUNIO:
                case JULIO:
                case AGOSTO:
                    return true;
                default:
                    return false;
            }
        } else {
            switch (this) {
                case DICIEMBRE:
                case ENERO:
                case FEBRERO:
                    return true;
                default:
                    return false;
            }
        }
    }

    /** ¿El mes tiene días de otoño? */
    public boolean esDeOtono(boolean esHemisferioNorte) {
        if (esHemisferioNorte) {
            switch (this) {
                case SEPTIEMBRE:
                case OCTUBRE:
                case NOVIEMBRE:
                    return true;
                default:
                    return false;
            }
        } else {
            switch (this) {
                case MARZO:
                case ABRIL:
                case MAYO:
                    return true;
                default:
                    return false;
            }
        }
    }

    /** ¿El mes tiene días de invierno? */
    public boolean esDeInvierno(boolean esHemisferioNorte) {
        if (esHemisferioNorte) {
            switch (this) {
                case DICIEMBRE:
                case ENERO:
                case FEBRERO:
                    return true;
                default:
                    return false;
            }
        } else {
            switch (this) {
                case JUNIO:
                case JULIO:
                case AGOSTO:
                    return true;
                default:
                    return false;
            }
        }
    }
}
```

> Nota: si se prefiere calcular automáticamente si un año es bisiesto, puede añadirse un método auxiliar (`static boolean esBisiesto(int anio)`) y una sobrecarga `dias(int anio)` que llame internamente a `dias(esBisiesto(anio))`. Esto mantiene la lógica encapsulada y evita errores de cálculo en el código cliente.
