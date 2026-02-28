<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

**Opción 1: devolver código de estado y usar un parámetro de salida para el resultado.** Esta aproximación separa claramente “éxito/fracaso” del valor calculado, evitando centinelas ambiguos. La función devuelve `int` (0 = ok, no‑cero = error) y escribe la raíz en un puntero si procede. El llamador comprueba el código y, si hay error, informa al usuario desde fuera.

```c
#include <stdio.h>
#include <math.h>

int raiz(double x, double *out_result) {
    if (x < 0.0) return 1;           // 1 = dominio inválido
    *out_result = sqrt(x);
    return 0;
}

int main(void) {
    double x = -9.0, r;
    int rc = raiz(x, &r);
    if (rc != 0) {
        printf("Error: argumento negativo (x = %.2f)\n", x);
    } else {
        printf("raiz(%.2f) = %.4f\n", x, r);
    }
    return 0;
}
```

**Opción 2: devolver el resultado y señalar el error mediante `errno` (y/o NaN).** Esta alternativa imita funciones de la librería C: se devuelve un `double` y, ante dominio inválido, se establece `errno = EDOM` y se retorna `NAN`. El llamador limpia `errno` antes de la llamada, comprueba si se ha modificado y decide cómo informar (sin que la función imprima nada).

```c
#define _GNU_SOURCE
#include <stdio.h>
#include <math.h>
#include <errno.h>
#include <fenv.h>

double raiz(double x) {
    if (x < 0.0) {
        errno = EDOM;                // dominio matemático inválido
        return NAN;                  // también puede alzarse FE_INVALID
    }
    errno = 0;
    return sqrt(x);
}

int main(void) {
    errno = 0;                       // limpiar antes de la llamada
    double x = -9.0;
    double r = raiz(x);
    if (errno == EDOM) {
        printf("Error: dominio inválido (x = %.2f)\n", x);
    } else {
        printf("raiz(%.2f) = %.4f\n", x, r);
    }
    return 0;
}
```

Ambos diseños permiten que la función sea “pura” en el sentido de no interactuar con E/S, dejando la responsabilidad de informar al usuario en el llamador. La opción 1 es explícita y segura (no depende de globales), mientras que la opción 2 se integra bien con el estilo de la libc y permite encadenar llamadas numéricas, a costa de depender de `errno` (estado global) y de chequear NaN o el código de error.


## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

Una **excepción** es un mecanismo que permite señalar y gestionar condiciones anómalas que ocurren durante la ejecución de un programa. A diferencia de los códigos de error tradicionales, una excepción detiene el flujo normal y transfiere el control a un bloque encargado de manejar la situación. Esto proporciona un modo estructurado y coherente de tratar problemas sin mezclar la lógica principal con comprobaciones constantes de error.

El programador utiliza excepciones al **implementar** funciones cuando desea comunicar que una operación no puede completarse correctamente, pero sin obligar a que todos los llamadores revisen manualmente códigos de retorno. Con ello se consigue separar la lógica de cálculo de la lógica de manejo de errores, lo que mejora la claridad y la mantenibilidad del código.

Al **llamar** funciones, el programador usa las excepciones para reaccionar ante situaciones imprevistas o inválidas. Este enfoque permite concentrar el manejo de errores en un solo lugar, normalmente en bloques `try`/`catch`, evitando dispersar verificaciones en cada punto donde pueda ocurrir un fallo. Como resultado, el flujo del programa es más limpio y la gestión de condiciones excepcionales resulta más fiable y expresiva.


## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

En Java, el error por argumento negativo puede señalizarse lanzando una excepción desde el método `raiz` y controlándolo desde fuera con `try`/`catch`. A diferencia de C, no se devuelven códigos de error; se interrumpe el flujo normal y se captura la condición anómala donde convenga. A continuación, un diseño usando una **excepción no comprobada** (`IllegalArgumentException`), apropiada para precondiciones incumplidas:

```java
public class Calculadora {

    public static double raiz(double x) {
        if (x < 0.0) {
            throw new IllegalArgumentException("Argumento negativo: " + x);
        }
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        double x = -9.0;
        try {
            double r = Calculadora.raiz(x);
            System.out.printf("raiz(%.2f) = %.4f%n", x, r);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage()); // control desde fuera
        }
    }
}
```

Si se prefiere obligar a los llamadores a tratar el caso (como si fuera un contrato explícito), puede definirse una **excepción comprobada** personalizada (extiende `Exception`). Esto hace que el compilador exija `throws` y `try/catch` en quienes llamen al método:

```java
// Excepción personalizada comprobada
class DominioInvalidoException extends Exception {
    public DominioInvalidoException(String mensaje) { super(mensaje); }
}

public class Calculadora {

    public static double raiz(double x) throws DominioInvalidoException {
        if (x < 0.0) throw new DominioInvalidoException("Dominio inválido: " + x);
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        double x = -9.0;
        try {
            double r = Calculadora.raiz(x);
            System.out.printf("raiz(%.2f) = %.4f%n", x, r);
        } catch (DominioInvalidoException e) {
            System.out.println("Error: " + e.getMessage()); // control desde fuera
        }
    }
}
```

La primera variante (unchecked) resulta concisa para precondiciones y fallos de programación; la segunda (checked) fuerza un manejo explícito donde el error forma parte del flujo esperado de la API. En ambos casos, el método no hace E/S y el **control y la comunicación del error se concentran fuera** del cálculo, manteniendo el código más limpio.


## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

**“Lanzar”** una excepción consiste en interrumpir el flujo normal de ejecución en un punto concreto con `throw`, creando/emitindo un objeto que describe la condición anómala. En el ejemplo, `Calculadora.raiz(double)` lanza `IllegalArgumentException` cuando la precondición (x ≥ 0) no se cumple: `throw new IllegalArgumentException("Argumento negativo: " + x);`. En ese instante, el método **no continúa** ejecutándose y empieza la búsqueda de un manejador adecuado.

**“Controlar”** o **“capturar”** una excepción significa envolver el código potencialmente fallido en un `try` y proveer un `catch` para el tipo de excepción. Si el llamador proporciona un `catch (IllegalArgumentException e)`, la ejecución se transfiere a ese bloque, donde puede registrarse el error, informar al usuario, traducir la excepción, o recuperar. Opcionalmente, `finally` se ejecuta siempre (haya o no haya excepción) para liberar recursos. Ejemplo mínimo:

```java
public static double raiz(double x) {
    if (x < 0.0) throw new IllegalArgumentException("Argumento negativo: " + x);
    return Math.sqrt(x);
}

public static void main(String[] args) {
    double x = -9.0;
    try {
        double r = raiz(x);
        System.out.printf("raiz(%.2f) = %.4f%n", x, r);
    } catch (IllegalArgumentException e) {
        System.out.println("Error: " + e.getMessage()); // control desde fuera
    } finally {
        System.out.println("Fin (liberación/registro si procede)");
    }
}
```

**“Propagar”** una excepción es dejarla salir del método actual sin capturarla, para que suba por la **pila de llamadas** hasta encontrar un `catch` compatible. En esa subida ocurre el **stack unwinding**: cada función intermedia se aborta y se “desenrolla”; no se reanudan después. Se ejecutan sus bloques `finally` (si existen) y se liberan los recursos que estén bajo *try-with-resources* o en finalizadores de estructuras. Las funciones que no la controlan **no continúan** tras la instrucción que falló; su ejecución termina en ese punto.

Para visualizar la propagación, puede interponerse un llamador intermedio que no captura y otro que sí captura. El intermedio no se reanuda; su `finally` sí se ejecuta:

```java
static double calculaDesdeServicio(double x) { // no captura, deja propagar
    try {
        return raiz(x);                        // aquí se lanza si x < 0
    } finally {
        System.out.println("Limpieza intermedia"); // se ejecuta al desenrollar
    }
}

public static void main(String[] args) {
    try {
        double r = calculaDesdeServicio(-9.0);
        System.out.println("Resultado: " + r); // nunca se alcanza
    } catch (IllegalArgumentException e) {     // primer catch compatible
        System.out.println("Notificación al usuario: " + e.getMessage());
    }
}
```

En este flujo, `raiz(-9)` lanza, `calculaDesdeServicio` no la captura (pero ejecuta su `finally`), la excepción sigue subiendo y **solo** se continúa en el primer `catch` compatible encontrado (en `main`). Ninguna de las funciones por las que se propagó se reanuda “después” del punto de fallo.


## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

La **propagación natural** de excepciones ofrece varias ventajas importantes frente a los mecanismos manuales típicos de C, donde se deben comprobar códigos de retorno o `errno` tras cada llamada. En primer lugar, permite separar de forma clara la lógica de cálculo de la lógica de tratamiento de errores. De este modo, las funciones intermedias no necesitan verificar condiciones anómalas si no están en posición de resolverlas; simplemente dejan que la excepción ascienda por la pila. Esto reduce el riesgo de errores por **olvido de comprobaciones**, muy frecuente en C, ya que cada función debe recordar verificar si la anterior falló y actuar en consecuencia.

Además, la propagación automática consigue un código más limpio y legible, ya que desaparecen las constantes comprobaciones de estados y valores centinela que saturan los programas en C. En vez de mezclar en cada punto la lógica normal con la detección de fallos, Java permite concentrar el manejo en bloques `try`/`catch` específicos, normalmente cerca de la frontera con el usuario o en capas superiores del programa. Así se evita repetir código de control de errores, lo que mejora la mantenibilidad y reduce inconsistencias entre diferentes partes del sistema.

También supone una ventaja en términos de **seguridad y robustez**, porque durante el desenrollado de la pila se ejecutan automáticamente bloques `finally` o mecanismos de liberación de recursos (*try-with-resources*). En C, esta limpieza depende enteramente del programador y puede ser fácilmente olvidada si los caminos de error no están cuidadosamente diseñados, creando fugas de memoria o recursos no cerrados. En cambio, Java garantiza que la liberación se realice incluso cuando la excepción pasa por varias funciones que no la capturan.

Por último, la propagación natural proporciona una forma más expresiva de comunicar fallos. Al usar diferentes tipos de excepción, se puede indicar de manera precisa la naturaleza del problema, mientras que en C el programador debe interpretar códigos de error numéricos o globales como `errno`. Gracias a ello, el flujo del programa en Java resulta más claro: las funciones se interrumpen exactamente donde ocurre el fallo y no se reanudan después, lo que elimina estados intermedios inconsistentes y facilita una estructura de control más fiable y coherente.


## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

En orientación a objetos, las excepciones suelen representarse como **objetos** porque encapsulan de forma natural toda la información relacionada con el error. Al ser instancias de clases, pueden contener un mensaje descriptivo, un tipo concreto que indica la naturaleza del fallo y, opcionalmente, datos adicionales como la causa original. Esta representación es coherente con la filosofía de la POO, donde cada elemento del programa se modela como un objeto con estado y comportamiento definido.

Desde el punto de vista de la **encapsulación**, usar objetos permite que los detalles internos del error queden protegidos y bien organizados. El código que lanza la excepción no necesita revelar cómo se almacena o interpreta la información del fallo; simplemente crea y lanza un objeto. A su vez, el código que captura la excepción interactúa con ella a través de métodos públicos bien definidos (por ejemplo, `getMessage()`), sin depender de estructuras globales como `errno` en C. Esto reduce el acoplamiento y hace que la gestión de errores sea más modular y maintainable.

El hecho de que las excepciones sean objetos permite **crear excepciones personalizadas**, adaptadas a las necesidades de una aplicación. Basta con definir una clase que herede de `Exception` o de alguna subclase adecuada dentro de la jerarquía de excepciones de Java. Con esto, se pueden expresar situaciones de error específicas (por ejemplo, `DominioInvalidoException`), lo que mejora la claridad del código y facilita capturar únicamente los problemas que interesan. Además, estas clases pueden incluir información extra o métodos que ayuden a describir o resolver el error de manera más precisa.

En conjunto, el uso de objetos para las excepciones aporta claridad, extensibilidad y mayor separación entre la lógica del programa y el manejo de errores. Gracias a la encapsulación y la posibilidad de crear clases especializadas, el sistema de excepciones en Java resulta expresivo y fácil de adaptar a situaciones complejas o a diseños formales basados en contratos y precondiciones.


## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

Un **objeto excepción** en Java siempre transporta información esencial que resulta muy útil al llegar a un manejador. La pieza más visible es el **mensaje descriptivo** (`getMessage()`), que permite conocer la causa concreta del fallo sin necesidad de que las funciones intermedias la expresen explícitamente ni repitan comprobaciones. Esto mejora la encapsulación porque el detalle del error queda asociado al propio objeto, no disperso en códigos numéricos ni en convenciones externas como en C.

Otro elemento fundamental es el **tipo de la excepción**, es decir, la clase concreta a la que pertenece. Gracias a esto, el manejador puede distinguir entre distintos fallos y capturar solo aquellos relevantes, lo que no es posible en C sin diseñar manualmente códigos simbólicos o sistemas propios. La jerarquía de clases de excepción actúa como un sistema de clasificación que estructura el manejo de errores de manera clara y coherente.

Además, toda excepción lleva implícita una **traza de pila (stack trace)**, que registra automáticamente la ruta exacta de llamadas hasta el punto donde ocurrió el error. Esta información es extremadamente útil para diagnosticar problemas, especialmente cuando la excepción ha viajado por varios métodos que no la capturan. En C, obtener una traza similar requiere herramientas externas, depuradores o bibliotecas no estándar, mientras que en Java forma parte intrínseca del objeto excepción.

En conjunto, el mensaje, el tipo y la traza hacen que un objeto excepción encapsule todo lo necesario para reconstruir qué ha sucedido y dónde. Gracias a esto, el manejador puede tomar decisiones informadas sin que las funciones intermedias deban intervenir, lo que simplifica el diseño y mejora tanto la legibilidad como la mantenibilidad del código.


## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

En Java, un bloque `try` puede ir seguido de **varios bloques `catch`**, cada uno destinado a capturar un tipo distinto de excepción. Esto permite reaccionar de forma específica según la naturaleza del error, aprovechando la jerarquía de clases de excepciones. El orden es importante: los `catch` más específicos deben colocarse antes que los más generales, ya que Java selecciona el primero cuyo tipo sea compatible con la excepción lanzada.

Cuando ocurre una excepción, **solo se ejecuta un único bloque `catch`**. En cuanto se encuentra el primer `catch` cuyo tipo coincide (o es superclase) de la excepción lanzada, se transfiere allí el control y se ignoran los demás. Tras ejecutarse ese `catch`, el flujo continúa después de toda la estructura `try‑catch` (o tras un posible `finally`, si existe). Por tanto, no hay múltiples manejadores encadenados para una misma excepción dentro del mismo `try`.

```java
try {
    double r = Calculadora.raiz(-9);   // lanza IllegalArgumentException
} catch (ArithmeticException e) {
    System.out.println("Error aritmético.");
} catch (IllegalArgumentException e) {
    System.out.println("Argumento inválido.");   // ← este se ejecuta
} catch (Exception e) {
    System.out.println("Otro error.");
}
```

Este comportamiento garantiza un manejo claro y determinista de las excepciones: se escoge **exactamente un** `catch`, evitando ambigüedad y manteniendo la encapsulación propia del sistema de tipos de Java.


## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

El bloque `finally` garantiza la ejecución de un código de limpieza **siempre**, ocurra o no una excepción, y tanto si esta es capturada como si se sigue propagando. De este modo, puede asegurarse el cierre de ficheros, la liberación de recursos nativos o la reversión de estados, incluso cuando el flujo normal se interrumpe por una excepción. El `finally` se ejecuta tras el `try` (y tras el `catch` si lo hay) antes de continuar con la ejecución normal o de volver a lanzar/propagar la excepción.

Con **`catch` + `finally`**, se captura la excepción para informar o traducirla, pero el `finally` se ejecuta incondicionalmente y cierra el recurso. Este patrón es útil cuando se desea manejar el error localmente y, aun así, asegurar la limpieza:

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class EjemploFinally {
    public static void main(String[] args) {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader("datos.txt"));
            String linea = br.readLine();             // puede lanzar IOException
            System.out.println("Primera línea: " + linea);
        } catch (IOException e) {
            System.out.println("Error de E/S: " + e.getMessage());
        } finally {
            // Se ejecuta siempre: éxito, fallo capturado o incluso return en try/catch
            if (br != null) {
                try { br.close(); } catch (IOException ignore) {}
            }
            System.out.println("Limpieza finalizada.");
        }
    }
}
```

Sin **`catch`**, la excepción no se controla localmente y **se propaga**, pero el `finally` igualmente se ejecuta antes de salir del método. Este patrón es útil cuando la capa actual no decide el manejo del error y solo garantiza la liberación de recursos:

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class EjemploFinallySinCatch {
    static String leerPrimeraLinea(String ruta) throws IOException {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader(ruta));
            return br.readLine();                     // si falla, salta al finally
        } finally {
            if (br != null) {
                try { br.close(); } catch (IOException ignore) {}
            }
            System.out.println("Recurso cerrado (aunque la excepción se propague).");
        }
    }

    public static void main(String[] args) {
        try {
            String l = leerPrimeraLinea("datos.txt");
            System.out.println(l);
        } catch (IOException e) {
            System.out.println("Notificación al usuario: " + e.getMessage());
        }
    }
}
```

Como mejora idiomática, en operaciones con recursos autocerrables conviene usar **try-with-resources** (`try (BufferedReader br = ...) { ... }`), que garantiza el cierre sin necesidad de `finally`. No obstante, el propósito de la pregunta se cumple con `finally`: asegurar que el código crítico de liberación se ejecute, tanto si hay `catch` como si la excepción continúa su propagación.


## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

Sí, en Java puede usarse `finally` **sin** `catch`: la forma `try { ... } finally { ... }` es válida y se emplea cuando no se quiere manejar la excepción en ese nivel, pero sí garantizar la liberación de recursos antes de que la excepción se **propague**. El bloque `finally` se ejecuta **tanto si ocurre como si no ocurre una excepción**, y también después de ejecutar un bloque `catch` si lo hubiera. Su objetivo es asegurar acciones críticas (cerrar ficheros, soltar locks, restaurar estado) sin duplicar lógica de limpieza.

Incluso si hay un `return` dentro del `try` (o del `catch`), el `finally` se **ejecuta antes** de devolver el control. Es decir, el `return` no salta el `finally`; primero corre el código de limpieza y luego se completa el retorno. De forma análoga, si en el `try` salta una excepción que no se captura, `finally` corre y **después** la excepción continúa su propagación. Existen excepciones raras: si el proceso termina abruptamente (`System.exit`), si la JVM muere por un error fatal, o si se lanza otra excepción en el propio `finally` (lo cual puede **ocultar** la original); por ello no se recomienda poner `return` ni lógica que pueda fallar dentro de `finally`.

**Ejemplos:**

```java
// try + finally SIN catch: la excepción se propaga, pero el finally siempre se ejecuta antes.
void procesa(String ruta) throws java.io.IOException {
    java.io.BufferedReader br = null;
    try {
        br = new java.io.BufferedReader(new java.io.FileReader(ruta)); // puede lanzar IOException
        // ... usar br ...
        if (ruta.endsWith(".ok")) return; // incluso con return, se ejecuta finally
    } finally {
        if (br != null) try { br.close(); } catch (java.io.IOException ignore) {}
        System.out.println("Limpieza hecha (con o sin excepción, con o sin return).");
    }
}
```

```java
// try + catch + finally: se maneja localmente y SIEMPRE se limpia.
void lee() {
    java.io.BufferedReader br = null;
    try {
        br = new java.io.BufferedReader(new java.io.FileReader("datos.txt"));
        System.out.println(br.readLine());
    } catch (java.io.IOException e) {
        System.out.println("Error de E/S: " + e.getMessage());
    } finally {
        if (br != null) try { br.close(); } catch (java.io.IOException ignore) {}
    }
}
```


## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

En Java, las excepciones **controladas (checked)** son aquellas que el compilador exige declarar con `throws` o capturar con `try/catch` porque representan condiciones recuperables y esperables (subclases de `Exception` **que no** derivan de `RuntimeException`). Las **no controladas (unchecked)** son las que **no** obligan a declarar ni capturar y suelen indicar errores de programación o precondiciones incumplidas (subclases de `RuntimeException` y también `Error`, aunque esta última no se debe manejar). `RuntimeException` es la raíz de las no controladas: indica problemas como referencias nulas, índices fuera de rango o argumentos inválidos; se usa para fallos que normalmente conviene corregir cambiando el código, no añadiendo manejo ceremonial.

Ejemplos típicos **controlados** que se pueden usar: `IOException` (fallos E/S al leer archivos o sockets), `SQLException` (errores de base de datos), `ParseException` o una excepción propia `DominioInvalidoException extends Exception` si se quiere forzar a los llamadores a tratar un caso previsto. Ejemplos **no controlados**: `IllegalArgumentException`, `NullPointerException`, `IndexOutOfBoundsException`, `ArithmeticException`, o una propia `ConfiguracionInvalidaException extends RuntimeException` cuando una precondición o mal uso de la API debe quedar patente sin ruido de `try/catch`.

Situaciones donde se **prefiere checked**:

*   Fallos de E/S o red donde el llamador puede reintentar o informar (p. ej., `IOException`).
*   Operaciones que forman parte del **contrato** de la API con resultados alternativos esperables (p. ej., validaciones formales o `ParseException`).
*   Integración con servicios externos donde el error es frecuente y recuperable (DB, ficheros, colas).
*   Pipelines donde se desea **propagar obligatoriamente** hasta un punto central de manejo.

Situaciones donde se **prefiere unchecked**:

*   **Precondiciones incumplidas** o uso incorrecto de la API (p. ej., `IllegalArgumentException`).
*   Errores de programación que deben arreglarse en desarrollo, no en runtime (p. ej., `NullPointerException`, `IndexOutOfBoundsException`).
*   Invariantes rotas y estados inconsistentes de los que no se puede recuperar limpiamente.
*   Validaciones ligeras en capas internas donde forzar `try/catch` haría el código verboso sin plan de recuperación real.


## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

La palabra clave **`throws`** se utiliza en la firma de un método para **declarar** que dicho método puede lanzar una o varias **excepciones controladas (checked)**. Con ello se informa al compilador y a los llamadores de que una condición excepcional puede producirse y que no se gestiona dentro del propio método. Esta declaración forma parte del “contrato” del método: quien lo invoque debe decidir si quiere capturar esa excepción o seguir propagándola hacia arriba.

El uso de `throws` aparece cuando no se desea (o no tiene sentido) **capturar** una excepción controlada dentro del método que la genera. En lugar de introducir un bloque `try‑catch` local, se delega la responsabilidad en el llamador. Esto es una alternativa válida porque las excepciones controladas obligan a tratar el posible error, y `throws` satisface al compilador informando de que el método no lo gestionará, pero sí lo declara.

Gracias a este mecanismo, el código puede mantenerse más limpio y modular: una función que no sabe qué hacer cuando ocurre un error recuperable no necesita manejarlo, sino simplemente declararlo. Así, niveles superiores del programa pueden centralizar la gestión o decidir cuándo es apropiado capturar y reaccionar. Un ejemplo típico es una función que lee un fichero y declara `throws IOException`, dejando que la interfaz de usuario, el controlador o la capa superior decidan cómo informar o actuar.


## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

Se ilustra una función que **declara** con `throws` que puede fallar al abrir/leer un fichero y **no** maneja localmente la excepción; en su lugar, la deja **propagar**. Aun así, se garantiza la liberación del recurso con `finally`. La firma deja claro el contrato: `throws IOException`.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class Ficheros {

    // Firma con throws: no se maneja IOException aquí; se propaga hacia arriba.
    public static String leerPrimeraLinea(String ruta) throws IOException {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader(ruta)); // puede lanzar FileNotFoundException/IOException
            return br.readLine();                           // puede lanzar IOException
        } finally {
            // Se ejecuta siempre, ocurra o no excepción (y antes de propagarla o del return)
            if (br != null) {
                try { br.close(); } catch (IOException ignore) {}
            }
        }
    }

    // Ejemplo de uso: aquí sí se decide capturar; alternativamente, también podría declararse throws en main.
    public static void main(String[] args) {
        try {
            String linea = leerPrimeraLinea("datos.txt");
            System.out.println("Primera línea: " + linea);
        } catch (IOException e) {
            System.out.println("No se pudo leer el fichero: " + e.getMessage());
        }
    }
}
```

Este patrón satisface al compilador para **excepciones controladas**: el método que realiza E/S declara `throws IOException` y no introduce `catch` locales si no hay política de recuperación; la limpieza queda asegurada con `finally`. En capas superiores (p. ej., `main` o un controlador) se decide si capturar, registrar, reintentar o volver a propagar, manteniendo separados el cálculo, la política de manejo y la liberación de recursos.


## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

Sí, se pueden listar excepciones **no controladas** (subclases de `RuntimeException`) en la cláusula `throws`, pero es **opcional** y no cambia el comportamiento del compilador: los llamadores **no** están obligados a capturarlas ni a declararlas. Incluirlas en `throws` tiene sobre todo un valor **documental/contractual**: indica que el método puede fallar por una precondición incumplida o por un uso incorrecto de la API (p. ej., `IllegalArgumentException`, `NullPointerException`) sin imponer `try/catch` ceremoniales.

El método llamador **solo debería** poner `try/catch` para una `RuntimeException` si **puede** y **tiene sentido** recuperarse localmente (por ejemplo, capturar `NumberFormatException` para pedir de nuevo una entrada al usuario). Si no hay una estrategia real de recuperación, es preferible **no** capturarla y dejar que se **propague** hasta un manejador superior o un punto central (logging global, frontera de UI/servicio). Capturar indiscriminadamente `RuntimeException` suele ocultar errores de programación y dificultar el diagnóstico.

Tiene sentido declarar `throws RuntimeException` cuando se quiere **hacer explícito** en la firma que el método puede lanzar cierta excepción no controlada como parte del contrato (y reflejarlo en la Javadoc con `@throws`). Por ejemplo:

```java
public int buscarIndice(String[] arr, String clave) throws NullPointerException, IllegalArgumentException {
    if (arr == null) throw new NullPointerException("arr");
    if (clave == null) throw new IllegalArgumentException("clave no puede ser null");
    // ...
    return 0;
}
```

Aquí la cláusula `throws` no obliga a nada al llamador, pero **documenta** precondiciones. En resumen: se puede declarar `RuntimeException` en `throws`, el llamador no está forzado a capturarla, y solo debería hacerlo cuando tenga una política de recuperación clara; de lo contrario, es mejor dejarla propagar.


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

 Las **excepciones controladas (checked)** se recomiendan cuando la situación de error es **esperable y recuperable**, formando parte normal del contrato de uso de una API. Casos típicos son los de **E/S** (`IOException`), acceso a bases de datos (`SQLException`) o parsing formal (`ParseException`). En estas situaciones el llamador **sí puede decidir qué hacer**: reintentar, informar al usuario, usar un valor alternativo o cancelar la operación. Por eso el compilador obliga a declarar o capturar estas excepciones: se entiende que el programador debe estar al tanto de estas condiciones y gestionarlas.

Las **excepciones no controladas (unchecked)**, como `IllegalArgumentException`, se recomiendan cuando el error indica **uso incorrecto de la API**, violación de una precondición o fallo de programación. En estos casos no suele haber una recuperación razonable: lo apropiado es corregir el código, no introducir bloques `try‑catch`. Las unchecked permiten mantener las firmas más limpias y evitan obligar al llamador a manejar errores que no son realmente recuperables. Ejemplos típicos son índices fuera de rango, null indebidos o parámetros inválidos.

No todos los lenguajes distinguen entre excepciones controladas y no controladas. Muchos lenguajes modernos (Python, JavaScript, C#, Ruby, Kotlin) **solo** disponen de excepciones no controladas, es decir, no obligan a declarar ni capturar nada. En esos lenguajes la opción habitual es el modelo “unchecked”, porque se considera que forzar manejo explícito introduce ruido y rara vez mejora la robustez. Aun así, los programadores de esos entornos suelen documentar las excepciones esperables, aunque el lenguaje no las diferencie formalmente.

En conjunto, las **controladas** se usan para errores recuperables y previstos, mientras que las **no controladas** se reservan para fallos que indican mal uso o inconsistencias internas. Cuando un lenguaje solo ofrece un modelo, suele alinearse con el enfoque de **excepciones no controladas**, privilegiando la simplicidad y la libertad del programador para decidir dónde y cómo manejar los errores.



## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

Tiene sentido **lanzar una nueva excepción dentro de un `catch`** cuando se desea *traducir* el error a un tipo más adecuado para la capa o dominio, o cuando se quiere añadir **contexto** (p. ej., qué se estaba intentando hacer). Este patrón mantiene encapsulación: la capa inferior expone detalles técnicos (`IOException`), mientras que la capa superior trabaja con conceptos del dominio (`ServicioNoDisponibleException`). Al crear una nueva excepción, conviene **encadenar la causa** (`new ... (mensaje, e)`) para no perder la traza original.

```java
// Traducción con encadenamiento de causa (checked → checked o unchecked)
class ServicioNoDisponibleException extends RuntimeException {
    public ServicioNoDisponibleException(String msg, Throwable cause) { super(msg, cause); }
}

String cargarPerfil(String id) {
    try {
        return Ficheros.leerPrimeraLinea("perfiles/" + id + ".txt"); // throws IOException
    } catch (java.io.IOException e) {
        // Se añade contexto de dominio y se propaga como unchecked
        throw new ServicioNoDisponibleException("No se pudo cargar el perfil " + id, e);
    }
}
```

También se puede **relanzar la misma excepción capturada** (p. ej., para registrar y volver a propagar) usando `throw e;`. Esto **no reanuda** el método; la excepción sigue subiendo y se conserva la traza original. Tiene sentido cuando la capa actual no puede recuperarse, pero quiere **loggear/medir** o garantizar limpieza adicional antes de que continúe la propagación. Si se necesita cambiar el tipo o enriquecer el mensaje, se prefiere la traducción con causa.

```java
void procesar(String ruta) throws java.io.IOException {
    java.io.BufferedReader br = null;
    try {
        br = new java.io.BufferedReader(new java.io.FileReader(ruta));
        // ... lógica ...
    } catch (java.io.IOException e) {
        System.err.println("Fallo accediendo a " + ruta + ": " + e.getMessage());
        throw e; // relanzar la misma excepción (se conserva stack trace)
    } finally {
        if (br != null) try { br.close(); } catch (java.io.IOException ignore) {}
    }
}
```

En resumen: **lanzar dentro de `catch`** es útil para **traducir** o **enriquecer** el error con contexto; **relanzar la misma** excepción es apropiado para **registrar** o ejecutar pasos de limpieza sin alterar el tipo ni el contrato. Se evita capturar y “tragar” silenciosamente; si no hay estrategia real de recuperación, es mejor **propagar** (rethrow) o **traducir con causa** para mantener diagnósticos y responsabilidades claros.


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

Ser la **“causa”** de otra excepción significa que una excepción de bajo nivel (la que realmente ocurrió primero) queda **encadenada** dentro de una excepción de más alto nivel que se lanza después para dar contexto o traducir el error al dominio. En Java, todas las excepciones heredan de `Throwable`, que soporta **encadenamiento** mediante constructores que aceptan un `cause` y/o el método `initCause`. Gracias a esto, no se pierde la traza original y puede reconstruirse el origen real del problema con `getCause()`.

Un patrón común es **capturar** una excepción técnica (`IOException`) y **envolverla** en una excepción de dominio propia (por ejemplo, `ServicioNoDisponibleException`) conservando la causa. Cuando se imprime la traza (`printStackTrace()` o el volcado que hace la JVM), se muestra una sección adicional con `Caused by: ...` que incluye la pila de la causa; si hay varias capas de causas, aparecen en cadena.

```java
// Excepción de alto nivel del dominio
class ServicioNoDisponibleException extends RuntimeException {
    public ServicioNoDisponibleException(String mensaje, Throwable causa) {
        super(mensaje, causa); // encadenamiento de causa
    }
}

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class PerfilService {
    public String cargarPerfil(String id) {
        try (BufferedReader br = new BufferedReader(new FileReader("perfiles/" + id + ".txt"))) {
            return br.readLine();
        } catch (IOException e) {
            // Traducir y añadir contexto de dominio, preservando la causa técnica
            throw new ServicioNoDisponibleException("No se pudo cargar el perfil con id=" + id, e);
        }
    }

    public static void main(String[] args) {
        new PerfilService().cargarPerfil("123"); // si falta el fichero, se encadena IOException
    }
}
```

Al salir por pantalla (por ejemplo, por una excepción no capturada), se verá algo como:  
`ServicioNoDisponibleException: No se pudo cargar el perfil con id=123` seguido de su traza, y después una sección **`Caused by: java.io.FileNotFoundException: ...`** con la traza original. Esto confirma que la excepción de dominio fue **causada por** la de bajo nivel, preservando diagnósticos y facilitando depuración sin exponer detalles técnicos en las capas superiores.

