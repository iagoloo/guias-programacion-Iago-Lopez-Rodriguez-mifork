<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

La composición en C puede modelarse definiendo estructuras que contienen otras estructuras. Para representar una línea compuesta por dos puntos, resulta natural definir primero un `struct Point` con las coordenadas `x` e `y`, y después un `struct Line` con dos campos de tipo `Point` que representen los extremos. Esta composición expresa la relación “una línea **tiene** dos puntos” de forma clara y directa, reutilizando el tipo `Point` como bloque de construcción dentro de `Line`.

Para calcular distancias, puede implementarse una función pura `distance(Point a, Point b)` que devuelva la distancia euclídea entre dos puntos, usando `sqrt` y `pow` o, preferiblemente, `hypot` de `<math.h>` por su mejor estabilidad numérica. A partir de esa función, la longitud de una línea se obtiene simplemente como la distancia entre sus dos puntos; por tanto, `line_length(Line l)` puede delegar en `distance(l.a, l.b)`. Se recomienda marcar los parámetros por valor como `const` cuando proceda y no se modifiquen, por claridad semántica.

A continuación se muestra un ejemplo completo, con definición de estructuras, funciones y un `main` mínimo de demostración. En C “clásico”, el paso por valor de estructuras pequeñas como estas es habitual y legible; si se trabajara con estructuras grandes, podría preferirse pasar punteros constantes (`const Point*`) para evitar copias innecesarias.

```c
#include <stdio.h>
#include <math.h>   // hypot, sqrt (compilar con -lm en algunos entornos)

typedef struct {
    double x;
    double y;
} Point;

typedef struct {
    Point a;  // extremo 1
    Point b;  // extremo 2
} Line;

// Distancia euclídea entre dos puntos
double distance(const Point a, const Point b) {
    // hypot(dx, dy) calcula sqrt(dx*dx + dy*dy) con buena estabilidad numérica
    return hypot(b.x - a.x, b.y - a.y);
}

// Longitud de una línea = distancia entre sus extremos
double line_length(const Line l) {
    return distance(l.a, l.b);
}

int main(void) {
    Point p1 = { .x = 0.0, .y = 0.0 };
    Point p2 = { .x = 3.0, .y = 4.0 };

    Line  seg = { .a = p1, .b = p2 };

    printf("Distancia p1-p2: %.2f\n", distance(p1, p2));  // 5.00
    printf("Longitud de la línea: %.2f\n", line_length(seg)); // 5.00

    return 0;
}
```

```bash
# Ejemplo de compilación (según plataforma puede ser necesario -lm):
gcc -std=c11 -O2 linea.c -o linea -lm
./linea
```


## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

La composición en orientación a objetos se expresa haciendo que una clase contenga instancias de otras clases como parte de su estado interno. En este caso, una línea “tiene dos” puntos: la clase `Linea` encapsula dos objetos `Punto`. Gracias a la encapsulación se puede ocultar la representación interna y, además, garantizar **inmutabilidad**: si los campos son `private final` y no se exponen setters, no se podrá cambiar ni las coordenadas de un punto ni los extremos de una línea tras su creación. Esta aproximación evita efectos laterales y facilita el razonamiento, algo especialmente útil al venir de C/C++ procedural.

En Java moderno, puede implementarse `Punto` como un tipo de valor con métodos que operen de forma pura (por ejemplo, `distanciaA(Punto otro)`) y `Linea` como un agregado inmutable que delega el cálculo de longitud en los puntos que contiene. Para blindar la inmutabilidad se recomienda (1) usar campos `final`, (2) devolver copias defensivas si se trabajara con tipos mutables (no necesario aquí, `Punto` también es inmutable) y (3) no exponer referencias modificables. A continuación se muestra una implementación simple, clara y segura.

```java
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double x() { return x; }
    public double y() { return y; }

    /** Distancia euclídea hasta otro punto */
    public double distanciaA(Punto otro) {
        double dx = otro.x - this.x;
        double dy = otro.y - this.y;
        return Math.hypot(dx, dy); // equivalente a sqrt(dx*dx + dy*dy)
    }

    @Override public String toString() { return "(" + x + ", " + y + ")"; }
}

public final class Linea {
    private final Punto a;
    private final Punto b;

    public Linea(Punto a, Punto b) {
        if (a == null || b == null) throw new IllegalArgumentException("Los puntos no pueden ser nulos");
        this.a = a; // Puntos ya son inmutables; no hacen falta copias defensivas
        this.b = b;
    }

    public Punto a() { return a; }
    public Punto b() { return b; }

    /** Longitud de la línea = distancia entre sus extremos */
    public double longitud() {
        return a.distanciaA(b);
    }

    @Override public String toString() { return "Linea " + a + " -> " + b + " (|L|=" + longitud() + ")"; }
}

// Ejemplo de uso
class Demo {
    public static void main(String[] args) {
        Punto p1 = new Punto(0, 0);
        Punto p2 = new Punto(3, 4);
        Linea l = new Linea(p1, p2);
        System.out.println("Distancia p1->p2: " + p1.distanciaA(p2)); // 5.0
        System.out.println("Longitud de la línea: " + l.longitud());  // 5.0
    }
}
```

Si se deseara reforzar aún más la semántica de “tipo de valor”, podría usarse `record` en Java 16+ para `Punto` (`record Punto(double x, double y) { ... }`), manteniendo el método `distanciaA` como instancia. En cualquier caso, el concepto clave de composición permanece: `Linea` **contiene** dos `Punto` y su longitud **se deriva** de los objetos que encapsula, sin exponer operaciones mutadoras que pudieran romper invariantes.


## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

La **multiplicidad** en composición describe cuántas instancias de una clase participan en la relación con otra clase. Se utiliza principalmente en modelos conceptuales y diagramas UML para indicar de forma precisa si un objeto está compuesto por uno, varios o muchos otros objetos. Expresarla con claridad permite entender mejor las dependencias estructurales y el nivel de acoplamiento entre los tipos, lo que facilita la verificación de invariantes y la correcta construcción del sistema. En composición, la multiplicidad se interpreta siempre como una relación fuerte: si el objeto “todo” existe, también deben existir las partes que lo componen.

En el ejemplo de `Linea` y `Punto`, la clase `Linea` contiene exactamente **dos** `Punto`. No existe una línea sin sus dos puntos y, además, estos puntos no se pueden cambiar después de construir la línea si se respeta la inmutabilidad. Por tanto, desde el punto de vista de `Linea` hacia `Punto`, la multiplicidad es **2** (a veces expresada como `2..2`). Esta notación recoge que la línea necesita exactamente dos puntos para tener sentido geométrico y para mantener su invariante interno.

En la dirección inversa, desde `Punto` hacia `Linea`, la multiplicidad es distinta. Un mismo `Punto` puede formar parte de **cero** líneas (si simplemente se crea aislado), de **una** línea o de **varias** líneas distintas, según el uso que haga el programa. De este modo, la multiplicidad desde `Punto` hacia `Linea` se expresa como **0..**\*, indicando que un punto puede no pertenecer a ninguna línea o participar en un número arbitrario de ellas. Esta asimetría es habitual en composición: la estructura “todo–parte” se fija desde la clase que contiene, no desde la que es contenida.

En resumen, la multiplicidad de la composición en este caso es:

*   **De `Linea` → `Punto`: 2**
*   **De `Punto` → `Linea`: 0..**\*

Esta representación recoge adecuadamente la relación geométrica y la forma en que se ha definido la composición en el diseño orientado a objetos.


## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

La distinción entre **composición fuerte** y **composición débil** describe el grado de dependencia que existe entre un objeto “todo” y los objetos “parte” que lo conforman. En una composición fuerte, las partes no tienen un ciclo de vida independiente: solo existen mientras exista el objeto que las contiene, y normalmente no son reutilizables fuera de esa relación. Este modelo expresa una pertenencia exclusiva y un vínculo semántico profundo. En cambio, en una composición débil, las partes pueden existir por separado y suelen poder compartirse o ser utilizadas en otros contextos sin estar estrictamente ligadas al objeto que las agrupa.

La consecuencia más importante en relación con el **ciclo de vida de los objetos** es quién controla la creación y destrucción (o, en Java, la creación y la pérdida de referencias) de las partes. En composición fuerte, el objeto contenedor es responsable de crear y de dejar de usar las partes, y estas desaparecen conceptualmente cuando el “todo” deja de existir. En composición débil, los objetos pueden ser creados y gestionados fuera del contenedor, de modo que un mismo objeto puede pertenecer a varios agregados o continuar existiendo incluso después de que el agregado deje de utilizarlo. En lenguajes con gestión manual de memoria, esta diferencia afecta directamente a la liberación de recursos; en Java afecta al momento en que los objetos quedan disponibles para el recolector de basura.

En terminología UML y en muchos manuales de diseño, la **composición fuerte** es la que se denomina **composición** propiamente dicha y se representa mediante un rombo negro. Esta indica posesión exclusiva y dependencia total. Por su parte, la **composición débil** suele denominarse **asociación o agregación**, y se representa mediante un rombo blanco, indicando que la relación es más flexible y que las partes pueden existir al margen del “todo”. Esta distinción ayuda a diseñar modelos más precisos y a entender si los objetos deben ser inmutables, compartidos o gestionados de manera coordinada.


## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

Cuando una clase utiliza a otra únicamente de forma puntual —por ejemplo, recibiéndola como parámetro, devolviéndola como resultado de un método, creándola dentro de una operación concreta o empleándola como variable local— no se considera que exista composición, sino **dependencia**. Este tipo de relación es temporal y contextual: la clase no mantiene la otra como parte de su estado interno ni controla su ciclo de vida más allá del uso inmediato. En términos de diseño, se trata de una relación más débil y efímera, cuya finalidad es permitir que una operación se lleve a cabo con la ayuda de otro objeto, sin adquirir responsabilidad sobre él.

La dependencia no refleja un vínculo estructural entre clases, sino un uso funcional limitado al ámbito del método donde aparece. Los objetos implicados pueden existir antes y después de ese uso, y la clase que los emplea no asegura su creación ni su permanencia. Por esa razón, la dependencia no introduce invariantes de composición ni exige gestionar la destrucción o la inmutabilidad de las partes, ya que estas no forman parte del estado interno. En lenguajes como Java, este tipo de relación es muy común y suele aparecer en operaciones auxiliares, transformaciones, validaciones o cálculos temporales.

Frente a ello, la **composición** implica que una clase contiene objetos de otra como parte estable de su estructura, controlando su ciclo de vida y manteniendo una relación permanente mientras el objeto “todo” exista. La distinción se vuelve evidente al analizar si el objeto usado es un **atributo** mantenido en el tiempo o simplemente una **ayuda momentánea** para ejecutar una operación concreta. Así, cuando solo se usa un objeto de otra clase dentro de un método sin formar parte de su estado, se está hablando claramente de dependencia y no de composición.


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

La diferencia entre **composición fuerte** y **composición débil** no es de mutabilidad (aquí ambos tipos seguirán siendo inmutables), sino de **propiedad y ciclo de vida**. En composición fuerte, la clase “todo” **crea y posee** sus partes: los puntos **nacen** con la línea y su existencia queda conceptualmente ligada a ella. En composición débil (agregación), la línea **recibe** puntos ya existentes, que pueden **compartirse** entre varias líneas y **sobrevivir** aunque la línea deje de usarse. Esto permite observar el mismo `Punto` en múltiples `Linea`, mientras que en la composición fuerte cada `Linea` encapsula sus propios puntos.

A continuación se muestra primero una versión con **composición fuerte**: `LineaFuerte` no acepta puntos externos; se construye **siempre** a partir de coordenadas y crea internamente sus `Punto`. Los objetos `Punto` y `LineaFuerte` son inmutables (`private final` sin setters), y la longitud se calcula delegando en `distanciaA`. El acceso a los extremos se expone mediante getters; dado que `Punto` es inmutable, no hace falta copia defensiva.

En la variante de **composición débil**, `LineaDebil` **recibe** los `Punto` por constructor y mantiene sus referencias. Esto permite **compartir** un mismo `Punto` entre múltiples líneas o conservar los puntos aunque la línea deje de referenciarlos. La inmutabilidad sigue vigente, pero la **propiedad** ya no es exclusiva: los puntos no están ligados al ciclo de vida de la línea, sino al de quienes los referencien.

```java
// ---- Tipo de valor inmutable: Punto ----
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x; this.y = y;
    }
    public double x() { return x; }
    public double y() { return y; }

    /** Distancia euclídea hasta otro punto */
    public double distanciaA(Punto otro) {
        double dx = otro.x - this.x;
        double dy = otro.y - this.y;
        return Math.hypot(dx, dy);
    }

    @Override public String toString() { return "(" + x + ", " + y + ")"; }
}

// ---- COMPOSICIÓN FUERTE: la línea crea y posee sus puntos ----
public final class LineaFuerte {
    private final Punto a;
    private final Punto b;

    /** Se fuerza la creación interna: no se aceptan puntos externos */
    public LineaFuerte(double ax, double ay, double bx, double by) {
        this.a = new Punto(ax, ay);
        this.b = new Punto(bx, by);
    }

    public Punto a() { return a; }
    public Punto b() { return b; }
    public double longitud() { return a.distanciaA(b); }

    @Override public String toString() {
        return "LineaFuerte " + a + " -> " + b + " (|L|=" + longitud() + ")";
    }
}

// ---- COMPOSICIÓN DÉBIL (AGREGACIÓN): la línea recibe puntos externos ----
import java.util.Objects;

public final class LineaDebil {
    private final Punto a;
    private final Punto b;

    /** Se aceptan referencias externas; pueden compartirse entre líneas */
    public LineaDebil(Punto a, Punto b) {
        this.a = Objects.requireNonNull(a);
        this.b = Objects.requireNonNull(b);
    }

    public Punto a() { return a; }
    public Punto b() { return b; }
    public double longitud() { return a.distanciaA(b); }

    @Override public String toString() {
        return "LineaDebil " + a + " -> " + b + " (|L|=" + longitud() + ")";
    }
}

// ---- Demostración mínima ----
class Demo {
    public static void main(String[] args) {
        // Composición fuerte: puntos creados y "poseídos" por la línea
        LineaFuerte lf = new LineaFuerte(0, 0, 3, 4);
        System.out.println(lf); // puntos nacen con la línea

        // Composición débil: puntos externos compartidos
        Punto p0 = new Punto(0, 0);
        Punto p1 = new Punto(3, 4);
        Punto p2 = new Punto(2, 0);

        LineaDebil ld1 = new LineaDebil(p0, p1);
        LineaDebil ld2 = new LineaDebil(p0, p2); // p0 compartido entre dos líneas

        System.out.println(ld1);
        System.out.println(ld2);

        // Si ld1 y ld2 dejan de usarse, p0, p1, p2 pueden seguir vivos si alguien más los referencia:
        // en agregación, el ciclo de vida de los puntos no está ligado al de la línea.
    }
}
```


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

En Java, incluso en una **composición fuerte**, el objeto contenedor no destruye explícitamente a los objetos que contiene porque el lenguaje no proporciona un mecanismo de destrucción manual como en C o C++. En lugar de ello, Java emplea un **sistema de gestión automática de memoria** basado en **referencias** y un **recolector de basura** (garbage collector). Esto significa que los objetos se crean mediante `new`, pero no se destruyen de forma explícita mediante ninguna instrucción equivalente a `free` o `delete`. En su lugar, un objeto queda marcado para eliminación cuando **ninguna parte del programa conserva una referencia** hacia él.

En el caso de una composición fuerte, los objetos parte (como los `Punto` dentro de una `Linea`) **viven mientras el contenedor mantenga referencias hacia ellos**. Como la `Linea` guarda sus puntos en atributos `private final`, esos puntos permanecen accesibles mientras la línea siga estando referenciada en el programa. Cuando la línea deja de usarse —por ejemplo, se sale del ámbito en un método o se elimina la última referencia que apuntaba a ella—, entonces indirectamente también dejan de usarse los puntos que contiene.

La consecuencia directa es que la destrucción de los objetos ocurre **automáticamente** cuando ya no son alcanzables desde el programa. Si `Linea` deja de tener referencias activas, entonces también los objetos `Punto` que contiene se vuelven inalcanzables, lo que permite al recolector de basura liberarlos en algún momento posterior. Este proceso es transparente para el programador y evita la necesidad de gestión manual de memoria, reduciendo errores clásicos como fugas de memoria o dobles liberaciones.

En resumen, en Java la composición fuerte **no implica destrucción explícita**, sino una relación en la que el ciclo de vida de las partes queda **subordinado a la accesibilidad** del objeto contenedor. Cuando el contenedor deja de ser accesible, sus partes lo hacen también, y el recolector de basura se encarga de eliminar ambos sin intervención directa del código.


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

La **composición débil (agregación)** se modela manteniendo **referencias** a objetos externos sin apropiarse de su ciclo de vida. En el ejemplo, el `Departamento` guarda a sus profesores y un director (que **siempre** debe existir y **siempre** debe pertenecer a la lista del departamento), pero no crea ni destruye a los `Profesor`: simplemente los **usa** y preserva la **invariante**. Para no romper la encapsulación, se oculta la estructura interna (un array `Profesor[]` de capacidad máxima 50) y se expone una API mínima: añadir al final, eliminar por posición, consultar cuántos hay y obtener un profesor por posición. El director se puede cambiar, pero únicamente por un profesor que ya figure en la lista.

La invariante principal es: **“el director forma parte de la lista en todo momento”**. En consecuencia, al intentar eliminar un profesor, si ese profesor es el director, debe lanzarse una excepción y solicitar antes el cambio de director. Al cambiar el director, si el nuevo no está en la lista, también se lanza excepción. Para evitar incoherencias, se valida nulidad, capacidad, duplicados por identidad de dominio (por ejemplo, DNI) y rangos de índice. Los `Profesor` se implementan como **inmutables** para que devolver una referencia no comprometa la encapsulación.

```java
// Tipo de valor inmutable que identifica a un profesor por DNI.
import java.util.Objects;

public final class Profesor {
    private final String dni;
    private final String nombre;

    public Profesor(String dni, String nombre) {
        if (dni == null || dni.isBlank()) throw new IllegalArgumentException("DNI obligatorio");
        if (nombre == null || nombre.isBlank()) throw new IllegalArgumentException("Nombre obligatorio");
        this.dni = dni;
        this.nombre = nombre;
    }

    public String dni()    { return dni; }
    public String nombre() { return nombre; }

    @Override public String toString() { return nombre + " [" + dni + "]"; }

    // Identidad por DNI: evita duplicados lógicos en el departamento
    @Override public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Profesor)) return false;
        Profesor that = (Profesor) o;
        return dni.equals(that.dni);
    }
    @Override public int hashCode() { return Objects.hash(dni); }
}
```

```java
// Agregación (composición débil): el departamento mantiene referencias a Profesores externos.
// Invariantes: (1) siempre existe director; (2) el director pertenece a la lista.
import java.util.Objects;

public final class Departamento {
    private static final int MAX = 50;

    private final Profesor[] profesores = new Profesor[MAX];
    private int size = 0;
    private int directorIdx = -1;

    /** Crea el departamento con un director inicial, que pasa a formar parte de la lista. */
    public Departamento(Profesor directorInicial) {
        setUpDirectorInicial(directorInicial);
    }

    private void setUpDirectorInicial(Profesor p) {
        Objects.requireNonNull(p, "El director inicial no puede ser nulo");
        profesores[0] = p;
        size = 1;
        directorIdx = 0;
    }

    /** Añade al final; no revela que internamente se emplea un array. */
    public void anadirProfesor(Profesor p) {
        Objects.requireNonNull(p, "Profesor nulo");
        if (size == MAX) throw new IllegalStateException("Capacidad máxima (" + MAX + ") alcanzada");
        if (contains(p)) throw new IllegalArgumentException("El profesor ya existe en el departamento");
        profesores[size++] = p;
    }

    /** Elimina por posición compactando el array. No permite eliminar al director. */
    public void eliminarProfesorPorPosicion(int pos) {
        validarRango(pos);
        if (pos == directorIdx) {
            throw new IllegalStateException("No se puede eliminar al director; cámbielo antes");
        }
        // Desplazar a la izquierda desde pos+1
        for (int i = pos + 1; i < size; i++) {
            profesores[i - 1] = profesores[i];
        }
        profesores[--size] = null; // liberar referencia

        // Ajustar índice del director si estaba después del eliminado
        if (directorIdx > pos) directorIdx--;
    }

    /** Cambia el director. Debe ser un profesor ya incluido en el departamento. */
    public void cambiarDirector(Profesor nuevoDirector) {
        Objects.requireNonNull(nuevoDirector, "Director nulo");
        int idx = indexOf(nuevoDirector);
        if (idx == -1) {
            throw new IllegalArgumentException("El director debe pertenecer al departamento");
        }
        directorIdx = idx;
    }

    /** Número de profesores actuales (incluye al director). */
    public int numeroProfesores() {
        return size;
    }

    /** Obtiene un profesor por posición [0..size-1]. */
    public Profesor profesorEn(int pos) {
        validarRango(pos);
        return profesores[pos]; // Seguro porque Profesor es inmutable
    }

    /** Devuelve el director actual. */
    public Profesor director() {
        asegurarDirector();
        return profesores[directorIdx];
    }

    // ------- Utilidades privadas -------
    private void validarRango(int pos) {
        if (pos < 0 || pos >= size) {
            throw new IndexOutOfBoundsException("Posición fuera de rango: " + pos);
        }
    }

    private int indexOf(Profesor p) {
        for (int i = 0; i < size; i++) {
            if (profesores[i].equals(p)) return i;
        }
        return -1;
    }

    private boolean contains(Profesor p) {
        return indexOf(p) != -1;
    }

    private void asegurarDirector() {
        if (directorIdx < 0 || directorIdx >= size || profesores[directorIdx] == null) {
            throw new IllegalStateException("Invariante rota: no hay director válido");
        }
    }
}
```

```java
// Demostración mínima de uso y de las invariantes.
public class DemoDepartamento {
    public static void main(String[] args) {
        Profesor p1 = new Profesor("111A", "Ana");
        Profesor p2 = new Profesor("222B", "Bernardo");
        Profesor p3 = new Profesor("333C", "Celia");

        Departamento d = new Departamento(p1); // p1 se añade y es director
        d.anadirProfesor(p2);
        d.anadirProfesor(p3);

        System.out.println("Profesores: " + d.numeroProfesores());       // 3
        System.out.println("Director: " + d.director());                 // Ana
        System.out.println("Profesor(1): " + d.profesorEn(1));           // Bernardo

        d.cambiarDirector(p3);                                           // Director -> Celia
        System.out.println("Nuevo director: " + d.director());           // Celia

        // d.eliminarProfesorPorPosicion(2); // OK, borra a Celia? No: ahora Celia es director (índice 2).
        // Al intentar eliminarlo, se lanzará excepción:
        try {
            d.eliminarProfesorPorPosicion(2);
        } catch (IllegalStateException e) {
            System.out.println("Esperado: " + e.getMessage());
        }

        // Cambiar director a Ana y ahora sí eliminar a Celia
        d.cambiarDirector(p1);
        d.eliminarProfesorPorPosicion(2);
        System.out.println("Profesores tras borrar: " + d.numeroProfesores()); // 2
        System.out.println("Director actual: " + d.director());                // Ana
    }
}
```

**Notas de diseño:** se ha mantenido la **agregación** (composición débil) al no crear ni destruir `Profesor` dentro de `Departamento`. La encapsulación del array se preserva ofreciendo un **mínimo interfaz**; al devolverse referencias directas a `Profesor`, la inmutabilidad del tipo garantiza que no se comprometa el estado interno. Las excepciones (`IllegalArgumentException`, `IllegalStateException`, `IndexOutOfBoundsException`) señalan violaciones de contrato e invariantes, especialmente la condición de que el director **siempre** pertenezca al departamento.


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

Al usar `List<Profesor>` en lugar de un array primitivo, la implementación se simplifica y gana expresividad sin romper la encapsulación. La idea de agregación (composición débil) se mantiene: el `Departamento` **mantiene referencias** a profesores externos y asegura la **invariante** de que **siempre hay director** y de que **el director pertenece a la lista**. Con `List` ya no se gestiona manualmente la capacidad, el corrimiento al eliminar, ni la detección de huecos; además, operaciones como `contains`, `indexOf` y `remove(int)` expresan directamente la intención y evitan código propenso a errores. Se mantiene el límite de 50 elementos como regla de negocio, validándolo antes de cada inserción.

Respecto a la encapsulación, el método `profesorEn(int)` sigue siendo seguro si `Profesor` es inmutable, pero **devolver directamente la lista interna sería un “escape” de representación**: el código cliente podría modificarla (añadir/eliminar) y **romper invariantes** (por ejemplo, eliminar al director) fuera de los puntos de control que lanzan excepciones. Para evitarlo, se recomienda devolver una **copia defensiva** o una **vista inmodificable** (`Collections.unmodifiableList(...)`) que preserve el control del `Departamento`. Si se necesita inmutabilidad “fuerte”, `List.copyOf(...)` (Java 10+) devuelve una copia verdaderamente inmodificable.

A continuación, una versión con `List` que implementa: añadir al final, eliminar por posición, contar, obtener por posición y cambiar director garantizando la invariante. Además, se incluye un método de acceso seguro a la lista completa devolviendo una vista **no modificable** (o, si se prefiere, una copia).

```java
import java.util.*;

/** Tipo de valor inmutable que identifica a un profesor por DNI. */
public final class Profesor {
    private final String dni;
    private final String nombre;

    public Profesor(String dni, String nombre) {
        if (dni == null || dni.isBlank()) throw new IllegalArgumentException("DNI obligatorio");
        if (nombre == null || nombre.isBlank()) throw new IllegalArgumentException("Nombre obligatorio");
        this.dni = dni;
        this.nombre = nombre;
    }

    public String dni()    { return dni; }
    public String nombre() { return nombre; }

    @Override public String toString() { return nombre + " [" + dni + "]"; }
    @Override public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Profesor)) return false;
        Profesor that = (Profesor) o;
        return dni.equals(that.dni);
    }
    @Override public int hashCode() { return Objects.hash(dni); }
}
```

```java
/** Agregación con List: el director siempre pertenece a la lista y debe existir en todo momento. */
public final class Departamento {
    private static final int MAX = 50;

    // Representación oculta
    private final List<Profesor> profesores = new ArrayList<>();
    private int directorIdx = -1;

    /** Crea el departamento con director inicial (que entra en la lista). */
    public Departamento(Profesor directorInicial) {
        Objects.requireNonNull(directorInicial, "El director inicial no puede ser nulo");
        profesores.add(directorInicial);
        directorIdx = 0;
    }

    /** Añade al final; evita duplicados lógicos por DNI y respeta el límite MAX. */
    public void anadirProfesor(Profesor p) {
        Objects.requireNonNull(p, "Profesor nulo");
        if (profesores.size() == MAX) throw new IllegalStateException("Capacidad máxima (" + MAX + ") alcanzada");
        if (profesores.contains(p)) throw new IllegalArgumentException("El profesor ya existe en el departamento");
        profesores.add(p);
        // directorIdx no cambia
    }

    /** Elimina por posición; prohíbe eliminar al director. */
    public void eliminarProfesorPorPosicion(int pos) {
        validarRango(pos);
        if (pos == directorIdx) {
            throw new IllegalStateException("No se puede eliminar al director; cámbielo antes");
        }
        profesores.remove(pos);
        if (directorIdx > pos) directorIdx--; // el índice del director se desplaza si estaba después
    }

    /** Cambia el director por otro que ya esté en la lista. */
    public void cambiarDirector(Profesor nuevoDirector) {
        Objects.requireNonNull(nuevoDirector, "Director nulo");
        int idx = profesores.indexOf(nuevoDirector);
        if (idx == -1) throw new IllegalArgumentException("El director debe pertenecer al departamento");
        directorIdx = idx;
    }

    /** Número de profesores (incluye al director). */
    public int numeroProfesores() {
        return profesores.size();
    }

    /** Profesor por posición [0..size-1]. */
    public Profesor profesorEn(int pos) {
        validarRango(pos);
        return profesores.get(pos); // seguro porque Profesor es inmutable
    }

    /** Director actual (siempre válido). */
    public Profesor director() {
        asegurarDirector();
        return profesores.get(directorIdx);
    }

    /** Acceso seguro a la colección completa (vista no modificable). */
    public List<Profesor> profesores() {
        // Alternativa 1 (vista no modificable, refleja cambios futuros internos):
        return Collections.unmodifiableList(profesores);
        // Alternativa 2 (copia defensiva inmodificable, “snapshot”):
        // return List.copyOf(profesores); // Java 10+
    }

    // ----- utilidades privadas -----
    private void validarRango(int pos) {
        if (pos < 0 || pos >= profesores.size()) {
            throw new IndexOutOfBoundsException("Posición fuera de rango: " + pos);
        }
    }

    private void asegurarDirector() {
        if (directorIdx < 0 || directorIdx >= profesores.size()) {
            throw new IllegalStateException("Invariante rota: no hay director válido");
        }
    }
}
```

**¿Qué código se ha ahorrado?** No es necesario gestionar manualmente la **capacidad**, ni **compactar** el array al eliminar, ni **desplazar elementos** con bucles, ni mantener funciones auxiliares como `indexOf` y `contains` (la `List` ya las ofrece). Además, la **validación de rango** se reduce a comprobar los límites y `List` aporta operaciones atómicas (`add`, `remove`, `get`) más legibles. En cuanto a exponer “todos los profesores”, **no debe devolverse la lista interna**: permitiría al consumidor modificarla y **violar invariantes** (por ejemplo, quitar al director). Para resolverlo, se debe retornar **una vista inmodificable** (`Collections.unmodifiableList`) o **una copia defensiva** (`List.copyOf`) según se prefiera exponer una vista viva o un “snapshot” inmutable.


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

La **composición recursiva** aparece cuando un tipo contiene referencias a instancias del mismo tipo, formando estructuras en cadena o en árbol. En Java puede modelarse una `Persona` inmutable que “tiene una madre” como otra `Persona`. La inmutabilidad se garantiza con campos `private final`, ausencia de setters y validaciones en el constructor; para los ancestros más altos, la madre puede faltar y representarse con `Optional<Persona>` (o con `null` si se prefiere, aunque `Optional` comunica mejor la ausencia). Esta relación recursiva permite recorrer ascendentes (madre, abuela, bisabuela, …) sin exponer mutaciones que rompan invariantes.

A continuación se muestra una implementación inmutable de `Persona` con un método de utilidad para recorrer la cadena de ascendencia. En el `main` se instancia una familia sencilla: abuela → madre → nieto, y se imprimen sus datos. Obsérvese que no existe destrucción explícita; el recolector de basura se encargará cuando las personas dejen de ser alcanzables. La recursividad se manifiesta en que cada `Persona` contiene opcionalmente otra `Persona` del mismo tipo como madre.

Otros ejemplos clásicos de composiciones recursivas incluyen **nodos de árbol** (cada nodo tiene hijos que son nodos), **listas enlazadas** (un nodo apunta al siguiente nodo), **sistemas de archivos** (una carpeta contiene archivos y subcarpetas que son a su vez carpetas), **árboles de sintaxis (AST)** en compiladores, **estructura DOM/HTML** (un elemento contiene otros elementos), y **excepciones encadenadas** (cada excepción puede envolver una causa que es otra excepción).

```java
import java.util.Objects;
import java.util.Optional;

public final class Persona {
    private final String nombre;
    private final Optional<Persona> madre; // recursivo: una Persona puede contener otra Persona

    public Persona(String nombre, Optional<Persona> madre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre es obligatorio");
        }
        this.nombre = nombre;
        this.madre = Objects.requireNonNull(madre, "madre no puede ser null");
    }

    public String nombre() { return nombre; }
    public Optional<Persona> madre() { return madre; }

    /** Devuelve el ancestro materno en el nivel dado: 0 = esta persona, 1 = madre, 2 = abuela, ... */
    public Optional<Persona> ancestroMaterno(int niveles) {
        if (niveles < 0) throw new IllegalArgumentException("niveles debe ser >= 0");
        Persona actual = this;
        for (int i = 0; i < niveles; i++) {
            if (actual.madre.isEmpty()) return Optional.empty();
            actual = actual.madre.get();
        }
        return Optional.of(actual);
    }

    @Override public String toString() {
        return "Persona{" + "nombre='" + nombre + '\'' +
               (madre.isPresent() ? ", madre='" + madre.get().nombre + '\'' : ", sin madre conocida") +
               '}';
    }
}
```

```java
public class DemoFamilia {
    public static void main(String[] args) {
        Persona abuela = new Persona("María", Optional.empty());
        Persona madre  = new Persona("Lucía", Optional.of(abuela));
        Persona nieto  = new Persona("Carlos", Optional.of(madre));

        System.out.println("Nieto:  " + nieto);
        System.out.println("Madre:  " + madre);
        System.out.println("Abuela: " + abuela);

        // Acceso recursivo a ascendentes
        System.out.println("Madre del nieto: " +
            nieto.ancestroMaterno(1).map(Persona::nombre).orElse("<desconocida>"));
        System.out.println("Abuela del nieto: " +
            nieto.ancestroMaterno(2).map(Persona::nombre).orElse("<desconocida>"));
        System.out.println("Bisabuela del nieto: " +
            nieto.ancestroMaterno(3).map(Persona::nombre).orElse("<desconocida>"));
    }
}
```

> Variante sin `Optional`: podría usarse `Persona madre` y permitir `null`, documentándolo y controlándolo con comprobaciones explícitas. No obstante, `Optional<Persona>` mejora la legibilidad y reduce errores al indicar claramente que la madre puede no existir en el modelo.

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

Las **relaciones de composición bidireccionales** son aquellas en las que **cada clase mantiene una referencia a la otra**, de modo que ambas “se conocen” mutuamente. En un modelo orientado a objetos esto implica que el vínculo no es solo estructural en un sentido (“A tiene a B”), sino que también lo es en el inverso (“B sabe a qué A pertenece”). Este tipo de relación debe manejarse con cuidado porque puede introducir **dependencias cíclicas**, problemas de inicialización incoherente y riesgos de **romper invariantes** si no se mantiene la consistencia entre ambos lados. En general, una relación bidireccional exige asegurarse de que **ambas referencias se actualizan juntas** y que ningún lado queda desincronizado.

En el caso del ejemplo `Profesor`–`Departamento`, la relación actual es **unidireccional**: el `Departamento` conoce a sus profesores, pero los `Profesor` no saben en qué departamento trabajan. Para hacerla **bidireccional**, habría que añadir una referencia al `Departamento` dentro de `Profesor`. Esto implicaría modificar el constructor y establecer que cada vez que se añada un profesor al departamento, se actualice también la referencia interna del profesor (`setDepartamento(this)`). A la inversa, al eliminar un profesor, debería borrarse esa referencia. Del mismo modo, si se cambiara el director, ese profesor seguiría apuntando al departamento; pero si un profesor se elimina del departamento, su campo `departamento` debería pasar a `null` o a un `Optional.empty()`, según el diseño elegido.

Por tanto, implementar una relación bidireccional en este caso requeriría dos acciones coordinadas:

1.  **Modificar `Profesor`** para incluir un atributo `Departamento departamento` (idealmente privado, quizá con getter, y sin setter público para no romper la encapsulación).
2.  **Modificar `Departamento`** para que cada operación que añada o elimine profesores **actualice la referencia inversa** en esos profesores, garantizando que la relación se mantiene en ambos sentidos y que la invariante sigue respetándose.

Además, habría que extremar la precaución para no permitir estados incoherentes, como un profesor que apunte a un departamento del que en realidad no forma parte. Por esta razón, los modelos bien diseñados suelen preferir relaciones **unidireccionales** siempre que sea posible, pues simplifican el mantenimiento de invariantes; la bidireccionalidad únicamente se utiliza cuando se necesita por razones semánticas claras o por eficiencia en la navegación del modelo.
