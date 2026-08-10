# Curso de Java — Nivel 3: Java Avanzado
## Tema 14: Genéricos (Generics)

> **Nivel:** Intermedio-Avanzado | **Prerrequisitos:** Niveles 1 y 2 completos, Tema 13 | **Duración estimada:** 5–6 horas

---

## Tabla de contenidos

1. [¿Qué son los Genéricos?](#1-qué-son-los-genéricos)
2. [Clases genéricas](#2-clases-genéricas)
3. [Métodos genéricos](#3-métodos-genéricos)
4. [Interfaces genéricas](#4-interfaces-genéricas)
5. [Wildcards — comodines](#5-wildcards--comodines)
6. [Bounded Type Parameters — límites de tipo](#6-bounded-type-parameters--límites-de-tipo)
7. [Type Erasure — borrado de tipos](#7-type-erasure--borrado-de-tipos)
8. [Genéricos y herencia](#8-genéricos-y-herencia)
9. [Genéricos con múltiples parámetros de tipo](#9-genéricos-con-múltiples-parámetros-de-tipo)
10. [Patrones comunes con genéricos](#10-patrones-comunes-con-genéricos)
11. [Restricciones de los genéricos](#11-restricciones-de-los-genéricos)
12. [Errores comunes con Genéricos](#12-errores-comunes-con-genéricos)
13. [Ejercicios prácticos](#13-ejercicios-prácticos)
14. [Resumen del tema](#14-resumen-del-tema)

---

## 1. ¿Qué son los Genéricos?

Los **genéricos** (introducidos en Java 5) permiten escribir clases, métodos e interfaces que funcionan con **cualquier tipo de dato**, manteniendo la seguridad de tipos en tiempo de compilación.

### El problema sin genéricos

Antes de Java 5, las colecciones almacenaban `Object`, lo que requería castings manuales y era inseguro:

```java
// SIN genéricos (Java anterior a 5)
List lista = new ArrayList();    // Raw type: sin tipo específico
lista.add("Hola");
lista.add(42);                   // Cualquier tipo puede entrar
lista.add(new Persona("Ana"));

// Al recuperar, debes hacer cast manual — arriesgado
String s   = (String) lista.get(0);   // OK
String err = (String) lista.get(1);   // ClassCastException en TIEMPO DE EJECUCIÓN
```

```java
// CON genéricos (Java 5+)
List<String> lista = new ArrayList<>();  // Solo acepta Strings
lista.add("Hola");
// lista.add(42);       // ERROR en TIEMPO DE COMPILACIÓN ← mucho mejor

String s = lista.get(0);  // Sin cast necesario
```

### Los tres beneficios clave de los genéricos

```
1. SEGURIDAD DE TIPOS      2. ELIMINACIÓN DE CASTS    3. REUTILIZACIÓN
────────────────────────   ──────────────────────────  ─────────────────
Errores detectados en      El compilador sabe el tipo  Un solo método o
compilación, no en         exacto → no necesitas        clase para todos
ejecución                  castings manuales            los tipos
```

### Nomenclatura de parámetros de tipo

Por convención, se usan letras mayúsculas simples:

| Letra | Significado convencional |
|-------|--------------------------|
| `T`   | Type (tipo genérico) |
| `E`   | Element (elemento, usado en colecciones) |
| `K`   | Key (clave, usado en mapas) |
| `V`   | Value (valor, usado en mapas) |
| `N`   | Number (número) |
| `R`   | Return type (tipo de retorno) |
| `S`, `U`, `W` | 2do, 3er, 4to tipo cuando se necesitan varios |

---

## 2. Clases genéricas

Una **clase genérica** declara uno o más parámetros de tipo entre ángulos `<T>`. Al instanciar la clase, se especifica el tipo concreto.

### Sintaxis básica

```java
public class Caja<T> {
    private T contenido;

    public Caja(T contenido) {
        this.contenido = contenido;
    }

    public T getContenido()         { return contenido; }
    public void setContenido(T c)   { this.contenido = c; }

    public boolean estaVacia()      { return contenido == null; }

    @Override
    public String toString() {
        return "Caja[" + contenido + "]";
    }
}
```

### Uso con distintos tipos

```java
// La misma clase, distintos tipos concretos
Caja<String>  cajaTexto   = new Caja<>("Hola Java");
Caja<Integer> cajaNumero  = new Caja<>(42);
Caja<Double>  cajaDecimal = new Caja<>(3.14);
Caja<Persona> cajaPersona = new Caja<>(new Persona("Ana", 28));

System.out.println(cajaTexto.getContenido());    // Hola Java
System.out.println(cajaNumero.getContenido());   // 42

// El compilador conoce el tipo exacto en cada caso
String texto = cajaTexto.getContenido();    // Sin cast
int    num   = cajaNumero.getContenido();   // Unboxing automático

// Caja<String> no acepta un Integer:
// cajaTexto.setContenido(42);  // Error de compilación ← seguridad garantizada
```

### Diamond operator `<>` (Java 7+)

Java puede inferir el tipo del lado derecho cuando el lado izquierdo lo especifica:

```java
// Forma explícita (redundante)
Caja<String> c1 = new Caja<String>("Hola");

// Con diamond operator (recomendado)
Caja<String> c2 = new Caja<>("Hola");

// También funciona en colecciones
List<Map<String, List<Integer>>> complejo = new ArrayList<>();  // Diamond infiere
```

### Clase genérica completa — Pila genérica

```java
public class Pila<E> {
    private Object[] elementos;   // Array de Object (por type erasure — lo veremos)
    private int tope;
    private static final int CAPACIDAD_INICIAL = 10;

    @SuppressWarnings("unchecked")
    public Pila() {
        elementos = new Object[CAPACIDAD_INICIAL];
        tope = -1;
    }

    public void apilar(E elemento) {
        if (elemento == null)
            throw new IllegalArgumentException("No se permiten elementos null.");
        if (tope == elementos.length - 1) expandir();
        elementos[++tope] = elemento;
    }

    @SuppressWarnings("unchecked")
    public E desapilar() {
        if (estaVacia())
            throw new java.util.EmptyStackException();
        E elem = (E) elementos[tope];
        elementos[tope--] = null;  // Evitar memory leak
        return elem;
    }

    @SuppressWarnings("unchecked")
    public E ver() {
        if (estaVacia())
            throw new java.util.EmptyStackException();
        return (E) elementos[tope];
    }

    public boolean estaVacia() { return tope == -1; }
    public int tamaño()        { return tope + 1; }

    private void expandir() {
        elementos = java.util.Arrays.copyOf(elementos, elementos.length * 2);
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder("Pila[");
        for (int i = tope; i >= 0; i--) {
            sb.append(elementos[i]);
            if (i > 0) sb.append(", ");
        }
        return sb.append("]").toString();
    }
}

// Uso con cualquier tipo:
Pila<String> pilaStrings = new Pila<>();
pilaStrings.apilar("Java");
pilaStrings.apilar("Python");
pilaStrings.apilar("Kotlin");
System.out.println(pilaStrings.desapilar()); // Kotlin
System.out.println(pilaStrings.ver());       // Python

Pila<Integer> pilaInts = new Pila<>();
pilaInts.apilar(1);
pilaInts.apilar(2);
pilaInts.apilar(3);
System.out.println(pilaInts);               // Pila[3, 2, 1]
```

### Clase genérica — Par (Tuple de dos elementos)

```java
public class Par<A, B> {
    private final A primero;
    private final B segundo;

    public Par(A primero, B segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public A getPrimero()  { return primero; }
    public B getSegundo()  { return segundo; }

    // Fábrica estática para sintaxis más limpia
    public static <A, B> Par<A, B> de(A a, B b) {
        return new Par<>(a, b);
    }

    // Intercambiar los elementos
    public Par<B, A> intercambiar() {
        return new Par<>(segundo, primero);
    }

    @Override
    public String toString() {
        return "(" + primero + ", " + segundo + ")";
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Par<?, ?> p)) return false;
        return java.util.Objects.equals(primero, p.primero)
            && java.util.Objects.equals(segundo, p.segundo);
    }

    @Override
    public int hashCode() {
        return java.util.Objects.hash(primero, segundo);
    }
}

// Uso:
Par<String, Integer> persona = Par.de("Ana", 28);
Par<String, Double>  precio  = Par.de("Laptop", 999.99);
Par<Integer, String> coord   = Par.de(10, "Norte");

System.out.println(persona);            // (Ana, 28)
System.out.println(persona.intercambiar()); // (28, Ana)

// Como clave en un mapa
Map<Par<Integer, Integer>, String> mapa = new HashMap<>();
mapa.put(Par.de(0, 0), "Origen");
mapa.put(Par.de(1, 0), "Derecha");
```

---

## 3. Métodos genéricos

Un **método genérico** declara sus propios parámetros de tipo independientemente de si la clase es genérica o no. El parámetro de tipo va antes del tipo de retorno.

### Sintaxis

```java
public static <T> TipoRetorno nombreMetodo(T parametro) {
    // ...
}
```

### Métodos genéricos en clase no genérica

```java
public class Utilidades {

    // Método genérico: imprime cualquier elemento
    public static <T> void imprimir(T elemento) {
        System.out.println(elemento);
    }

    // Retorna el mismo tipo que recibe
    public static <T> T primerElemento(List<T> lista) {
        if (lista == null || lista.isEmpty())
            throw new java.util.NoSuchElementException("Lista vacía.");
        return lista.get(0);
    }

    // Intercambia dos elementos de un array
    public static <T> void intercambiar(T[] arr, int i, int j) {
        T temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    // Verifica si una lista contiene un elemento (cualquier tipo)
    public static <T> boolean contiene(T[] arr, T elemento) {
        for (T item : arr) {
            if (java.util.Objects.equals(item, elemento)) return true;
        }
        return false;
    }

    // Crea una lista con N copias del elemento dado
    public static <T> List<T> repetir(T elemento, int veces) {
        List<T> lista = new ArrayList<>(veces);
        for (int i = 0; i < veces; i++) lista.add(elemento);
        return lista;
    }

    // Convierte un array en lista (versión propia de Arrays.asList)
    @SafeVarargs
    public static <T> List<T> aLista(T... elementos) {
        return new ArrayList<>(java.util.Arrays.asList(elementos));
    }
}

// Uso — el compilador infiere T automáticamente:
Utilidades.imprimir("Hola");           // T = String
Utilidades.imprimir(42);               // T = Integer
Utilidades.imprimir(new Persona("Ana", 28)); // T = Persona

String s = Utilidades.primerElemento(List.of("A", "B", "C")); // s = "A"
int n    = Utilidades.primerElemento(List.of(1, 2, 3));        // n = 1

Integer[] nums = {1, 2, 3, 4, 5};
Utilidades.intercambiar(nums, 0, 4);  // [5, 2, 3, 4, 1]

List<String> tresJavas = Utilidades.repetir("Java", 3); // ["Java","Java","Java"]
```

### Inferencia de tipos en métodos genéricos

El compilador infiere los tipos de los argumentos automáticamente:

```java
// Inferencia automática:
List<String> lista1 = Utilidades.aLista("A", "B", "C");   // T = String
List<Integer> lista2 = Utilidades.aLista(1, 2, 3);        // T = Integer

// Especificación explícita (cuando la inferencia no es posible o es ambigua):
List<Number> nums = Utilidades.<Number>aLista(1, 2.0, 3L);
```

### Métodos genéricos con retorno del mismo tipo del parámetro

```java
public class Transformador {

    // Aplica una función y retorna el resultado del mismo tipo
    public static <T> T aplicarSiNoNull(T valor, java.util.function.UnaryOperator<T> op) {
        return valor != null ? op.apply(valor) : null;
    }

    // Retorna el mayor de dos Comparable
    public static <T extends Comparable<T>> T maximo(T a, T b) {
        return a.compareTo(b) >= 0 ? a : b;
    }

    // Retorna el menor de tres Comparable
    public static <T extends Comparable<T>> T minimo(T a, T b, T c) {
        T min = a.compareTo(b) <= 0 ? a : b;
        return min.compareTo(c) <= 0 ? min : c;
    }
}

// Uso:
String resultado = Transformador.aplicarSiNoNull("hola", String::toUpperCase); // "HOLA"
String nulo = Transformador.aplicarSiNoNull(null, String::toUpperCase);         // null

System.out.println(Transformador.maximo(10, 20));         // 20
System.out.println(Transformador.maximo("Ana", "Luis"));  // Luis
System.out.println(Transformador.minimo(5, 2, 8));        // 2
```

---

## 4. Interfaces genéricas

Las interfaces también pueden ser genéricas. Cuando una clase las implementa, puede especificar el tipo concreto o mantenerlo genérico.

```java
// Interfaz genérica
public interface Repositorio<T, ID> {
    void guardar(T entidad);
    T buscarPorId(ID id);
    List<T> buscarTodos();
    void actualizar(T entidad);
    boolean eliminar(ID id);
    int contarTotal();
}

// Implementación con tipos concretos
public class RepositorioUsuario implements Repositorio<Usuario, Integer> {
    private Map<Integer, Usuario> almacen = new HashMap<>();
    private int siguienteId = 1;

    @Override
    public void guardar(Usuario u) {
        almacen.put(siguienteId++, u);
    }

    @Override
    public Usuario buscarPorId(Integer id) {
        return almacen.get(id);
    }

    @Override
    public List<Usuario> buscarTodos() {
        return new ArrayList<>(almacen.values());
    }

    @Override
    public void actualizar(Usuario u) {
        almacen.entrySet().stream()
            .filter(e -> e.getValue().getId() == u.getId())
            .findFirst()
            .ifPresent(e -> e.setValue(u));
    }

    @Override
    public boolean eliminar(Integer id) {
        return almacen.remove(id) != null;
    }

    @Override
    public int contarTotal() { return almacen.size(); }
}

// Implementación genérica (mantiene T e ID genéricos)
public class RepositorioEnMemoria<T, ID> implements Repositorio<T, ID> {
    protected Map<ID, T> almacen = new HashMap<>();

    @Override public void guardar(T e)          { /* ... */ }
    @Override public T buscarPorId(ID id)       { return almacen.get(id); }
    @Override public List<T> buscarTodos()      { return new ArrayList<>(almacen.values()); }
    @Override public void actualizar(T e)       { /* ... */ }
    @Override public boolean eliminar(ID id)    { return almacen.remove(id) != null; }
    @Override public int contarTotal()          { return almacen.size(); }
}

// Uso de la implementación genérica
RepositorioEnMemoria<Producto, String> repoProductos = new RepositorioEnMemoria<>();
RepositorioEnMemoria<Empleado, Long>   repoEmpleados = new RepositorioEnMemoria<>();
```

### Interfaz genérica funcional

```java
// Interfaz genérica funcional para transformaciones
@FunctionalInterface
public interface Transformador<T, R> {
    R transformar(T entrada);

    // Métodos default para composición
    default <V> Transformador<T, V> luego(Transformador<R, V> despues) {
        return entrada -> despues.transformar(this.transformar(entrada));
    }
}

// Uso con lambda:
Transformador<String, Integer> longitud = String::length;
Transformador<Integer, String> aTexto   = n -> "Longitud: " + n;
Transformador<String, Boolean> esLarga  = s -> s.length() > 5;

System.out.println(longitud.transformar("Hola Java"));        // 9
System.out.println(esLarga.transformar("Hi"));                // false

// Composición de transformadores
Transformador<String, String> longitudComoTexto = longitud.luego(aTexto);
System.out.println(longitudComoTexto.transformar("Hola"));    // Longitud: 4
```

---

## 5. Wildcards — comodines

Los **wildcards** (`?`) representan un tipo desconocido. Son necesarios cuando quieres trabajar con colecciones de distintos tipos relacionados.

### El problema que resuelven los wildcards

```java
// Sin wildcards: demasiado restrictivo
public static double sumarLista(List<Number> lista) {
    double suma = 0;
    for (Number n : lista) suma += n.doubleValue();
    return suma;
}

List<Integer> ints    = List.of(1, 2, 3);
List<Double>  doubles = List.of(1.5, 2.5, 3.5);

sumarLista(ints);    // ERROR: List<Integer> no es List<Number>
sumarLista(doubles); // ERROR: List<Double> no es List<Number>
// Aunque Integer y Double extienden Number,
// List<Integer> NO es subclase de List<Number>
```

```java
// Con wildcard: acepta cualquier lista de Number o sus subclases
public static double sumarLista(List<? extends Number> lista) {
    double suma = 0;
    for (Number n : lista) suma += n.doubleValue();
    return suma;
}

sumarLista(ints);    // OK: Integer extiende Number
sumarLista(doubles); // OK: Double extiende Number
sumarLista(List.of(1L, 2L, 3L)); // OK: Long extiende Number
```

### Los tres tipos de wildcard

```
?                → Wildcard sin límite (unbounded)
? extends Tipo   → Wildcard acotado superior (upper bounded)
? super Tipo     → Wildcard acotado inferior (lower bounded)
```

### 5.1 Wildcard sin límite — `<?>`

Acepta una colección de **cualquier tipo**. Solo puedes leer como `Object`, no puedes agregar nada (excepto `null`).

```java
// Imprime cualquier lista, sin importar el tipo
public static void imprimirLista(List<?> lista) {
    for (Object elemento : lista) {   // Solo puede acceder como Object
        System.out.println(elemento);
    }
    // lista.add("algo");  // ERROR: no puedes agregar nada
    // lista.add(null);    // null sí está permitido (pero inútil)
}

imprimirLista(List.of("Java", "Python"));  // OK
imprimirLista(List.of(1, 2, 3));           // OK
imprimirLista(List.of(true, false));       // OK
```

### 5.2 Upper bounded wildcard — `<? extends T>`

Acepta colecciones de `T` o **cualquier subclase de T**. Permite **leer** (obtienes `T`), pero **no puedes agregar** (el tipo exacto es desconocido).

```java
// PRODUCTOR: lee de la colección
public static double calcularTotal(List<? extends Number> numeros) {
    return numeros.stream().mapToDouble(Number::doubleValue).sum();
}

public static <T extends Comparable<T>> T encontrarMaximo(List<? extends T> lista) {
    if (lista.isEmpty()) throw new java.util.NoSuchElementException();
    T max = lista.get(0);
    for (T elemento : lista) {
        if (elemento.compareTo(max) > 0) max = elemento;
    }
    return max;
}

// Uso:
List<Integer> enteros = List.of(3, 1, 4, 1, 5, 9, 2, 6);
List<Double>  reales  = List.of(1.5, 2.5, 0.5);

System.out.println(calcularTotal(enteros)); // 31.0
System.out.println(calcularTotal(reales));  // 4.5

System.out.println(encontrarMaximo(enteros));        // 9
System.out.println(encontrarMaximo(List.of("Mango", "Banana", "Pera"))); // Pera
```

### 5.3 Lower bounded wildcard — `<? super T>`

Acepta colecciones de `T` o **cualquier superclase de T**. Permite **agregar** (puedes agregar `T`), pero solo puedes **leer como `Object`**.

```java
// CONSUMIDOR: escribe en la colección
public static void agregarNumeros(List<? super Integer> lista) {
    // Puedes agregar Integer y sus subtipos (no tiene subtipos aquí)
    lista.add(1);
    lista.add(2);
    lista.add(3);
    // No puedes leer como Integer, solo como Object
    // Integer n = lista.get(0);  // ERROR
    Object o = lista.get(0);      // OK, pero pierdes el tipo
}

List<Integer> listaInt    = new ArrayList<>();
List<Number>  listaNum    = new ArrayList<>();
List<Object>  listaObj    = new ArrayList<>();

agregarNumeros(listaInt);  // OK: Integer acepta Integer
agregarNumeros(listaNum);  // OK: Number es superclase de Integer
agregarNumeros(listaObj);  // OK: Object es superclase de Integer
```

### El principio PECS

> **PECS: Producer Extends, Consumer Super**
>
> - Si la colección **produce** (solo lees de ella) → `<? extends T>`
> - Si la colección **consume** (solo escribes en ella) → `<? super T>`
> - Si haces ambas cosas → usa el tipo específico `<T>`

```java
// Ejemplo clásico de PECS: copiar de origen a destino
public static <T> void copiar(List<? extends T> origen,   // Productor: extiende T
                               List<? super T> destino) { // Consumidor: super T
    for (T elemento : origen) {
        destino.add(elemento);
    }
}

List<Integer> ints  = new ArrayList<>(List.of(1, 2, 3));
List<Number>  nums  = new ArrayList<>();
List<Object>  objs  = new ArrayList<>();

copiar(ints, nums);   // Copia de Integer a Number
copiar(ints, objs);   // Copia de Integer a Object
copiar(nums, objs);   // Copia de Number a Object
```

---

## 6. Bounded Type Parameters — límites de tipo

Los bounded type parameters restringen el tipo `T` a ser una clase específica o sus subclases/superclases.

### Upper bound: `<T extends ClaseOInterfaz>`

```java
// T debe ser Number o subclase de Number
public static <T extends Number> double sumar(T a, T b) {
    return a.doubleValue() + b.doubleValue();
}

System.out.println(sumar(3, 4));        // 7.0  — Integer
System.out.println(sumar(2.5, 1.5));    // 4.0  — Double
System.out.println(sumar(10L, 20L));    // 30.0 — Long
// sumar("hola", "mundo");  // ERROR: String no extiende Number
```

### Múltiples límites (Multiple Bounds)

```java
// T debe extender Comparable Y ser serializable
public static <T extends Comparable<T> & java.io.Serializable> T clamp(T valor, T min, T max) {
    if (valor.compareTo(min) < 0) return min;
    if (valor.compareTo(max) > 0) return max;
    return valor;
}

// Clase que cumple ambas condiciones (String extiende Comparable y Serializable)
System.out.println(clamp("M", "A", "Z")); // M (ya está en rango)
System.out.println(clamp(15, 0, 10));     // 10 (supera el máximo)
System.out.println(clamp(-5, 0, 10));     // 0 (está por debajo del mínimo)
```

### Bounded en clases genéricas

```java
// Solo acepta tipos que implementen Comparable
public class ColeccionOrdenada<T extends Comparable<T>> {
    private List<T> elementos = new ArrayList<>();

    public void agregar(T elemento) {
        elementos.add(elemento);
        java.util.Collections.sort(elementos);  // Puede ordenar porque T es Comparable
    }

    public T getMinimo() {
        if (elementos.isEmpty()) throw new java.util.NoSuchElementException();
        return elementos.get(0);
    }

    public T getMaximo() {
        if (elementos.isEmpty()) throw new java.util.NoSuchElementException();
        return elementos.get(elementos.size() - 1);
    }

    public boolean estaEnRango(T valor) {
        return valor.compareTo(getMinimo()) >= 0
            && valor.compareTo(getMaximo()) <= 0;
    }

    @Override
    public String toString() { return elementos.toString(); }
}

ColeccionOrdenada<Integer> nums = new ColeccionOrdenada<>();
nums.agregar(5); nums.agregar(2); nums.agregar(8); nums.agregar(1);
System.out.println(nums);              // [1, 2, 5, 8]
System.out.println(nums.getMinimo());  // 1
System.out.println(nums.getMaximo());  // 8
System.out.println(nums.estaEnRango(4)); // true
System.out.println(nums.estaEnRango(10)); // false
```

---

## 7. Type Erasure — borrado de tipos

El **type erasure** es el mecanismo mediante el cual el compilador de Java **elimina toda la información de tipos genéricos** en tiempo de compilación y la reemplaza con sus límites (o `Object` si no hay límites). En el bytecode resultante no existen los genéricos.

### Por qué existe el type erasure

Java introdujo los genéricos en la versión 5 manteniendo **compatibilidad hacia atrás** con código Java antiguo (sin genéricos). La solución fue hacer los genéricos solo una construcción en tiempo de compilación.

### Cómo funciona

```java
// Lo que escribes:
public class Caja<T> {
    private T contenido;
    public T getContenido() { return contenido; }
}

// Lo que el compilador genera (después de type erasure):
public class Caja {
    private Object contenido;      // T → Object (sin límite)
    public Object getContenido() { return contenido; }
}

// Con bounded type:
public class Caja<T extends Number> {
    private T contenido;
    public T getContenido() { return contenido; }
}
// Después de erasure:
public class Caja {
    private Number contenido;      // T → Number (el límite)
    public Number getContenido() { return contenido; }
}
```

### Consecuencias del type erasure

```java
// 1. No puedes crear instancias del tipo genérico
public class Ejemplo<T> {
    // T obj = new T();  // ERROR: no se puede instanciar tipo genérico
}

// 2. No puedes crear arrays del tipo genérico
public class Ejemplo<T> {
    // T[] arr = new T[10];  // ERROR
    Object[] arr = new Object[10]; // Workaround (requiere cast)

    @SuppressWarnings("unchecked")
    public T get(int i) { return (T) arr[i]; }
}

// 3. No puedes usar instanceof con tipos genéricos
List<String> lista = new ArrayList<>();
// lista instanceof List<String>  // ERROR: illegal generic type for instanceof
lista instanceof List<?>;          // OK: wildcard funciona

// 4. No puedes sobrecargar con solo el tipo genérico diferente
// ERROR: después del erasure, ambos métodos son idénticos
// public void procesar(List<String> lista) { }
// public void procesar(List<Integer> lista) { }
```

### Cómo verificar el tipo en tiempo de ejecución

```java
// Pasar la clase como parámetro (Class token)
public static <T> T convertir(Object objeto, Class<T> tipo) {
    if (!tipo.isInstance(objeto)) {
        throw new ClassCastException(
            "No se puede convertir " + objeto.getClass().getSimpleName()
            + " a " + tipo.getSimpleName());
    }
    return tipo.cast(objeto);
}

Object obj = "Hola Java";
String s   = convertir(obj, String.class);   // OK
// Integer n = convertir(obj, Integer.class); // ClassCastException

// Verificar tipo concreto con Class token
public class ContenedorTipado<T> {
    private T valor;
    private Class<T> tipo;

    public ContenedorTipado(Class<T> tipo) {
        this.tipo = tipo;
    }

    public void setValor(Object obj) {
        this.valor = tipo.cast(obj);  // Lanza ClassCastException si no coincide
    }

    public T getValor()   { return valor; }
    public Class<T> getTipo() { return tipo; }
}

ContenedorTipado<String> ct = new ContenedorTipado<>(String.class);
ct.setValor("Hola");
System.out.println(ct.getValor()); // Hola
```

---

## 8. Genéricos y herencia

La relación de herencia en genéricos es más compleja que en clases normales.

### La invarianza de los genéricos

```
Si Perro extends Animal:
  Perro        ES UN   Animal           ← TRUE (herencia normal)
  List<Perro>  ES UN   List<Animal>     ← FALSE (genéricos son invariantes)
```

```java
List<Perro>  perros  = new ArrayList<>();
List<Animal> animales;

// Esto NO es válido aunque Perro extends Animal:
// animales = perros;  // ERROR de compilación

// ¿Por qué? Seguridad de tipos:
// Si fuera válido, podrías hacer:
// animales.add(new Gato());  // Gato también es Animal
// Perro p = perros.get(0);   // ¡Pero sería un Gato! ClassCastException

// Solución: usar wildcards
List<? extends Animal> vista = perros;  // OK
// vista.add(new Gato());  // ERROR: no puedes agregar con extends wildcard
```

### Arrays son covariantes (diferente a genéricos)

```java
// Arrays SÍ son covariantes (pero potencialmente inseguros):
Animal[] animales = new Perro[3];  // Compila (covarianza de arrays)
animales[0] = new Gato();          // ArrayStoreException en tiempo de ejecución

// Genéricos son invariantes (seguros en compilación):
List<Animal> lista = new ArrayList<Perro>(); // ERROR en compilación ← más seguro
```

### Heredar de clase genérica

```java
// Clase hija especifica el tipo
public class ListaStrings extends ArrayList<String> {
    public String unirTodo(String separador) {
        return String.join(separador, this);
    }
}

// Clase hija mantiene el genérico
public class ListaOrdenada<T extends Comparable<T>> extends ArrayList<T> {
    @Override
    public boolean add(T elemento) {
        boolean resultado = super.add(elemento);
        java.util.Collections.sort(this);
        return resultado;
    }
}

ListaOrdenada<Integer> lo = new ListaOrdenada<>();
lo.add(5); lo.add(2); lo.add(8); lo.add(1);
System.out.println(lo); // [1, 2, 5, 8]
```

---

## 9. Genéricos con múltiples parámetros de tipo

```java
// Tres o más parámetros de tipo
public class Triple<A, B, C> {
    private final A primero;
    private final B segundo;
    private final C tercero;

    public Triple(A a, B b, C c) {
        this.primero = a;
        this.segundo = b;
        this.tercero = c;
    }

    public A getPrimero() { return primero; }
    public B getSegundo() { return segundo; }
    public C getTercero() { return tercero; }

    public static <A, B, C> Triple<A, B, C> de(A a, B b, C c) {
        return new Triple<>(a, b, c);
    }

    @Override
    public String toString() {
        return "(" + primero + ", " + segundo + ", " + tercero + ")";
    }
}

Triple<String, Integer, Boolean> t = Triple.de("Java", 21, true);
System.out.println(t.getPrimero());  // Java
System.out.println(t);               // (Java, 21, true)
```

### Resultado genérico con estado

```java
// Clase Result<T> para representar éxito o fallo
public class Resultado<T> {
    private final T valor;
    private final String error;
    private final boolean exitoso;

    private Resultado(T valor, String error, boolean exitoso) {
        this.valor   = valor;
        this.error   = error;
        this.exitoso = exitoso;
    }

    public static <T> Resultado<T> exito(T valor) {
        return new Resultado<>(valor, null, true);
    }

    public static <T> Resultado<T> fallo(String mensajeError) {
        return new Resultado<>(null, mensajeError, false);
    }

    public boolean isExitoso()  { return exitoso; }
    public T getValor()         {
        if (!exitoso) throw new IllegalStateException("La operación falló: " + error);
        return valor;
    }
    public String getError()    { return error; }

    public <R> Resultado<R> mapear(java.util.function.Function<T, R> f) {
        if (!exitoso) return Resultado.fallo(error);
        try {
            return Resultado.exito(f.apply(valor));
        } catch (Exception e) {
            return Resultado.fallo(e.getMessage());
        }
    }

    @Override
    public String toString() {
        return exitoso ? "Éxito(" + valor + ")" : "Fallo(" + error + ")";
    }
}

// Uso:
Resultado<Integer> r1 = Resultado.exito(42);
Resultado<String>  r2 = r1.mapear(n -> "El número es: " + n);
Resultado<Integer> r3 = Resultado.fallo("División por cero");

System.out.println(r1);             // Éxito(42)
System.out.println(r2);             // Éxito(El número es: 42)
System.out.println(r3);             // Fallo(División por cero)
System.out.println(r1.getValor());  // 42
// r3.getValor();                   // IllegalStateException
```

---

## 10. Patrones comunes con genéricos

### 10.1 Builder genérico

```java
public abstract class Builder<T, B extends Builder<T, B>> {
    @SuppressWarnings("unchecked")
    protected B self() { return (B) this; }

    public abstract T build();
}

public class PersonaBuilder extends Builder<Persona, PersonaBuilder> {
    private String nombre;
    private int    edad;
    private String email;

    public PersonaBuilder nombre(String n) { this.nombre = n; return self(); }
    public PersonaBuilder edad(int e)      { this.edad = e;   return self(); }
    public PersonaBuilder email(String m)  { this.email = m;  return self(); }

    @Override
    public Persona build() {
        if (nombre == null || nombre.isEmpty())
            throw new IllegalStateException("Nombre requerido.");
        return new Persona(nombre, edad, email);
    }
}

// Uso fluido:
Persona p = new PersonaBuilder()
    .nombre("Ana")
    .edad(28)
    .email("ana@email.com")
    .build();
```

### 10.2 Caché genérica

```java
public class Cache<K, V> {
    private final Map<K, V> almacen;
    private final int capacidadMaxima;

    public Cache(int capacidad) {
        this.capacidadMaxima = capacidad;
        this.almacen = new java.util.LinkedHashMap<>(16, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
                return size() > capacidadMaxima;
            }
        };
    }

    public synchronized void guardar(K clave, V valor) {
        almacen.put(clave, valor);
    }

    public synchronized V obtener(K clave) {
        return almacen.get(clave);
    }

    public synchronized V obtenerOCalcular(K clave, java.util.function.Function<K, V> calculador) {
        return almacen.computeIfAbsent(clave, calculador);
    }

    public synchronized boolean contiene(K clave) { return almacen.containsKey(clave); }
    public synchronized int tamaño()              { return almacen.size(); }
    public synchronized void limpiar()            { almacen.clear(); }
}

// Uso:
Cache<Integer, Long> cacheFactorial = new Cache<>(100);
for (int i = 1; i <= 20; i++) {
    int n = i;
    long resultado = cacheFactorial.obtenerOCalcular(n, k -> {
        long fact = 1;
        for (int j = 2; j <= k; j++) fact *= j;
        return fact;
    });
    System.out.println(n + "! = " + resultado);
}
```

### 10.3 Observador genérico

```java
public interface Observador<T> {
    void onEvento(T evento);
}

public class Publicador<T> {
    private final List<Observador<T>> observadores = new ArrayList<>();

    public void suscribir(Observador<T> obs)    { observadores.add(obs); }
    public void desuscribir(Observador<T> obs)  { observadores.remove(obs); }

    public void publicar(T evento) {
        observadores.forEach(obs -> obs.onEvento(evento));
    }
}

// Uso con cualquier tipo de evento:
Publicador<String> pubMensajes = new Publicador<>();
pubMensajes.suscribir(msg -> System.out.println("[LOG] " + msg));
pubMensajes.suscribir(msg -> System.out.println("[EMAIL] Alerta: " + msg));
pubMensajes.publicar("Error crítico detectado");

Publicador<Integer> pubNumeros = new Publicador<>();
pubNumeros.suscribir(n -> System.out.println("Recibido: " + n));
pubNumeros.publicar(42);
```

### 10.4 Pipeline genérico

```java
public class Pipeline<T> {
    private T valor;

    private Pipeline(T valor) { this.valor = valor; }

    public static <T> Pipeline<T> de(T valor) {
        return new Pipeline<>(valor);
    }

    public <R> Pipeline<R> transformar(java.util.function.Function<T, R> f) {
        return new Pipeline<>(f.apply(valor));
    }

    public Pipeline<T> filtrar(java.util.function.Predicate<T> condicion) {
        if (!condicion.test(valor))
            throw new java.util.NoSuchElementException("Valor filtrado: " + valor);
        return this;
    }

    public Pipeline<T> ejecutar(java.util.function.Consumer<T> accion) {
        accion.accept(valor);
        return this;
    }

    public T obtener() { return valor; }
}

// Uso:
String resultado = Pipeline.de("  Hola Mundo  ")
    .transformar(String::trim)
    .transformar(String::toUpperCase)
    .filtrar(s -> s.length() > 5)
    .ejecutar(s -> System.out.println("Procesando: " + s))
    .transformar(s -> s.replace(" ", "_"))
    .obtener();

System.out.println(resultado); // HOLA_MUNDO
```

---

## 11. Restricciones de los genéricos

Por el type erasure, los genéricos tienen varias restricciones importantes:

```java
public class Restricciones<T> {

    // 1. No puedes instanciar T
    // T obj = new T();  // ERROR

    // 2. No puedes crear arrays de T
    // T[] arr = new T[10];  // ERROR

    // 3. No puedes usar primitivos como tipo genérico
    // List<int>  lista;  // ERROR — usar List<Integer>
    // List<char> lista;  // ERROR — usar List<Character>

    // 4. No puedes usar instanceof con tipo genérico parametrizado
    List<String> lista = new ArrayList<>();
    // lista instanceof List<String>  // ERROR
    // lista instanceof List<?>       // OK

    // 5. No puedes crear instancias de tipos genéricos
    // new ArrayList<T>()  donde T es parámetro de tipo de la clase → requiere workaround

    // 6. No puedes sobrecargar con mismo erasure
    // void m(List<String> l) {} y void m(List<Integer> l) {} → ERROR

    // 7. No puedes lanzar ni capturar instancias de tipos genéricos
    // class MiException<T> extends Exception {}  // ERROR
    // try {} catch (T e) {}  // ERROR

    // WORKAROUNDS comunes:
    // Para arrays: usa @SuppressWarnings("unchecked") con Object[]
    @SuppressWarnings("unchecked")
    T[] crearArray(int n) {
        return (T[]) new Object[n];
    }

    // Para instancias: pasa la clase como parámetro
    T crearInstancia(Class<T> clase) throws Exception {
        return clase.getDeclaredConstructor().newInstance();
    }
}
```

---

## 12. Errores comunes con Genéricos

### Error 1: Raw types (tipos sin parámetros)

```java
// ✗ MAL: raw type — sin seguridad de tipos
List lista = new ArrayList();
lista.add("texto");
lista.add(42);  // Sin errores en compilación
String s = (String) lista.get(1);  // ClassCastException en ejecución

// ✓ BIEN: con tipo explícito
List<String> lista = new ArrayList<>();
// lista.add(42);  // Error en compilación ← mucho mejor
```

### Error 2: Mezclar arrays y genéricos

```java
// ✗ MAL: arrays de tipos genéricos
List<String>[] arr = new ArrayList<>[10];  // Error de compilación

// ✓ BIEN: lista de listas
List<List<String>> lista = new ArrayList<>();

// O con @SuppressWarnings si es absolutamente necesario el array:
@SuppressWarnings("unchecked")
List<String>[] arr = new ArrayList[10];  // Warning, no error
```

### Error 3: Asumir covarianza en genéricos

```java
List<Integer> ints = List.of(1, 2, 3);
// List<Number> nums = ints;  // ERROR: genéricos son invariantes

// ✓ BIEN: usar wildcard
List<? extends Number> nums = ints;  // OK para lectura
```

### Error 4: No usar PECS correctamente

```java
// ✗ MAL: demasiado restrictivo
public void agregar(List<Animal> lista, Animal a) {
    lista.add(a);
}
// List<Object> no puede pasarse aquí aunque Object sea superclase de Animal

// ✓ BIEN: PECS — Consumer Super
public void agregar(List<? super Animal> lista, Animal a) {
    lista.add(a);  // Funciona con List<Animal>, List<Object>
}
```

### Error 5: Confundir `<? extends T>` con `<T extends Tipo>`

```java
// <T extends Number> en la DECLARACIÓN del método/clase:
public static <T extends Number> double suma(T a, T b) {
    return a.doubleValue() + b.doubleValue();
}

// <? extends Number> en el TIPO DE PARÁMETRO (wildcard):
public static double sumaLista(List<? extends Number> lista) {
    return lista.stream().mapToDouble(Number::doubleValue).sum();
}

// Son diferentes: el primero crea un tipo nominal T, el segundo es anónimo
```

### Error 6: Intentar usar instanceof con tipo parametrizado

```java
List<String> lista = new ArrayList<>();
// if (lista instanceof List<String>) { }  // ERROR de compilación

// ✓ BIEN:
if (lista instanceof List<?>) { }  // OK
if (lista instanceof List) { }     // OK (raw type, con warning)
```

---

## 13. Ejercicios prácticos

### Ejercicio 1 — Colección genérica (Nivel: Básico)

Implementa `ListaCircular<T>` genérica que almacena elementos en un círculo:
- `agregar(T elemento)` — agrega al final
- `siguiente()` — retorna el siguiente elemento (vuelve al primero al llegar al final)
- `anterior()` — retorna el anterior
- `actual()` — retorna el elemento actual sin avanzar
- `tamaño()`
- `contiene(T elemento)`
- `toString()` — muestra todos con el actual marcado con `[*]`

Prueba con `ListaCircular<String>` de días de la semana y `ListaCircular<Integer>` de colores (representados como números).

---

### Ejercicio 2 — Métodos genéricos de utilidad (Nivel: Básico-Medio)

Crea `ArrayUtils` con estos métodos genéricos estáticos:
1. `<T> T[] filtrar(T[] arr, Predicate<T> condicion)` — retorna nuevo array con los que cumplen la condición
2. `<T, R> R[] transformar(T[] arr, Function<T, R> f, Class<R> clase)` — aplica función a cada elemento
3. `<T extends Comparable<T>> void ordenar(T[] arr)` — ordenamiento burbuja genérico
4. `<T> boolean todosIguales(T[] arr)` — true si todos los elementos son iguales según equals
5. `<T> Map<T, Integer> frecuencias(T[] arr)` — mapa de frecuencias de cada elemento
6. `<T> Optional<T> buscar(T[] arr, Predicate<T> condicion)` — primer elemento que cumple la condición

Demuestra cada método con al menos dos tipos distintos.

---

### Ejercicio 3 — Repositorio genérico (Nivel: Medio)

Diseña un framework de repositorio genérico:

```java
public interface Entidad<ID> {
    ID getId();
}

public interface RepositorioBase<T extends Entidad<ID>, ID> {
    void guardar(T entidad);
    Optional<T> buscarPorId(ID id);
    List<T> buscarTodos();
    List<T> buscarPor(Predicate<T> criterio);
    void actualizar(T entidad);
    boolean eliminar(ID id);
    long contar();
    boolean existe(ID id);
}
```

Implementa `RepositorioMemoria<T extends Entidad<ID>, ID>` que cumpla la interfaz.

Crea dos entidades: `Producto` y `Usuario`, y demuestra el repositorio con ambos.

---

### Ejercicio 4 — Pipeline de procesamiento (Nivel: Medio)

Extiende el patrón Pipeline con más capacidades:

```java
public class Pipeline<T> {
    // Además de lo visto en el tema, agrega:

    // Mapear a tipo diferente
    public <R> Pipeline<R> mapear(Function<T, R> f)

    // Combinar dos pipelines
    public Pipeline<T> combinar(Pipeline<T> otro, BinaryOperator<T> combinador)

    // Ejecutar solo si cumple la condición
    public Pipeline<T> ejecutarSi(Predicate<T> cond, Consumer<T> accion)

    // Recuperarse de errores
    public Pipeline<T> recuperar(T valorPorDefecto)

    // Bifurcar en dos ramas
    public Par<Pipeline<T>, Pipeline<T>> bifurcar(Predicate<T> condicion)

    // Registrar log automático en cada paso
    public Pipeline<T> conLog(String etapa)
}
```

Demuestra con un pipeline de procesamiento de texto que: limpia, divide en palabras, filtra, cuenta, etc.

---

### Ejercicio 5 — Árbol binario de búsqueda genérico (Nivel: Avanzado)

Implementa `ArbolBST<T extends Comparable<T>>` con:
- `insertar(T valor)`
- `contiene(T valor)` — búsqueda
- `eliminar(T valor)`
- `inorden()` — `List<T>` con elementos en orden ascendente
- `preorden()`, `postorden()` — listas con esos recorridos
- `altura()` — altura del árbol
- `minimo()`, `maximo()`
- `tamano()`
- `estaBalanceado()` — true si la diferencia de alturas de subárboles es ≤ 1
- `toString()` — representación visual del árbol

Prueba con `ArbolBST<Integer>` y `ArbolBST<String>`.

---

### Ejercicio 6 — Framework de validación genérico (Nivel: Avanzado)

Diseña un framework de validación genérico encadenado:

```java
public class Validador<T> {
    private final T valor;
    private final String campo;
    private final List<String> errores;

    public static <T> Validador<T> de(T valor, String campo)

    // Reglas genéricas
    public Validador<T> noNulo()
    public Validador<T> satisface(Predicate<T> regla, String mensajeError)
    public Validador<T> transformar(UnaryOperator<T> transformacion) // modifica el valor

    // Reglas para String (cuando T es String)
    public Validador<T> noVacio()
    public Validador<T> longitudMinima(int min)
    public Validador<T> longitudMaxima(int max)
    public Validador<T> coincideCon(String regex, String mensaje)

    // Reglas para Comparable
    public Validador<T> mayorQue(T minimo)
    public Validador<T> menorQue(T maximo)
    public Validador<T> enRango(T min, T max)

    // Resultados
    public boolean esValido()
    public List<String> getErrores()
    public T getValor()         // lanza excepción si no es válido
    public Optional<T> toOptional()
    public ResultadoValidacion<T> validar()
}
```

Demuestra validando: un formulario de registro completo (nombre, email, contraseña, edad, precio).

---

## 14. Resumen del tema

### Sintaxis de genéricos en una página

```java
// ════════════════════════════════════
// CLASE GENÉRICA
// ════════════════════════════════════
public class Caja<T> {
    private T contenido;
    public Caja(T c)         { this.contenido = c; }
    public T getContenido()  { return contenido; }
}
Caja<String>  cs = new Caja<>("Hola");  // Diamond <>
Caja<Integer> ci = new Caja<>(42);

// ════════════════════════════════════
// MÉTODO GENÉRICO
// ════════════════════════════════════
public static <T> T primero(List<T> lista) {
    return lista.get(0);
}
String s = primero(List.of("A","B","C")); // T inferido como String

// ════════════════════════════════════
// BOUNDED TYPE
// ════════════════════════════════════
public static <T extends Number> double suma(T a, T b) {
    return a.doubleValue() + b.doubleValue();
}
public static <T extends Comparable<T> & Cloneable> T max(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}

// ════════════════════════════════════
// WILDCARDS
// ════════════════════════════════════
void leer(List<?> lista)                 // Cualquier tipo (solo lectura como Object)
void producir(List<? extends Number> l)  // Number o subclases (solo lectura como Number)
void consumir(List<? super Integer> l)   // Integer o superclases (puede agregar Integer)

// PECS: Producer Extends, Consumer Super

// ════════════════════════════════════
// INTERFAZ GENÉRICA
// ════════════════════════════════════
public interface Repositorio<T, ID> {
    void guardar(T entidad);
    T buscarPorId(ID id);
}
public class RepoProducto implements Repositorio<Producto, Integer> {
    @Override public void guardar(Producto p) { ... }
    @Override public Producto buscarPorId(Integer id) { ... }
}
```

### Reglas de oro de los Genéricos

| Situación | Regla |
|-----------|-------|
| Raw types | Nunca usar raw types (`List`, `Map`); siempre especificar el tipo |
| Diamond `<>` | Usar siempre del lado derecho de la asignación (Java 7+) |
| Primitivos | Los tipos primitivos no pueden usarse como parámetros genéricos: usar wrappers (`int` → `Integer`) |
| PECS | Producer Extends (lee), Consumer Super (escribe) |
| `instanceof` | No usar con tipo parametrizado; usar `instanceof List<?>` |
| Arrays genéricos | Evitar arrays de tipos genéricos; usar listas |
| Type erasure | Los genéricos solo existen en compilación; en bytecode son `Object` o el bound |
| Bounded types | Usar `<T extends Comparable<T>>` cuando necesitas comparar |
| `@SuppressWarnings` | Usar solo cuando estás seguro del cast; documentar por qué es seguro |
| Wildcards | `<?>` cuando no necesitas el tipo; `<? extends>` para leer; `<? super>` para escribir |

---

## Próximo tema

**Tema 15: Streams y Lambdas**

Aprenderás la API Stream de Java 8, el estilo de programación funcional en Java, operaciones intermedias (`filter`, `map`, `sorted`, `distinct`, `limit`) y terminales (`collect`, `reduce`, `count`, `forEach`, `findFirst`), los Collectors más importantes, y cómo procesar colecciones de datos de forma elegante y eficiente con pipelines de streams.

---

*Curso de Java — Nivel 3: Java Avanzado*
*Tema 14 de 24*
*Creado con fines educativos*
