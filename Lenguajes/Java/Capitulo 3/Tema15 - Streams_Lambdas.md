# Curso de Java — Nivel 3: Java Avanzado
## Tema 15: Streams y Lambdas

> **Nivel:** Intermedio-Avanzado | **Prerrequisitos:** Temas 1–14 completados | **Duración estimada:** 6–7 horas

---

## Tabla de contenidos

1. [Programación funcional en Java](#1-programación-funcional-en-java)
2. [Expresiones lambda — repaso y profundización](#2-expresiones-lambda--repaso-y-profundización)
3. [Referencias a métodos](#3-referencias-a-métodos)
4. [¿Qué es un Stream?](#4-qué-es-un-stream)
5. [Crear Streams](#5-crear-streams)
6. [Operaciones intermedias](#6-operaciones-intermedias)
7. [Operaciones terminales](#7-operaciones-terminales)
8. [Collectors — recolectores avanzados](#8-collectors--recolectores-avanzados)
9. [Streams especializados — IntStream, LongStream, DoubleStream](#9-streams-especializados--intstream-longstream-doublestream)
10. [Optional — manejo elegante de null](#10-optional--manejo-elegante-de-null)
11. [Streams paralelos](#11-streams-paralelos)
12. [Streams y excepciones](#12-streams-y-excepciones)
13. [Errores comunes con Streams](#13-errores-comunes-con-streams)
14. [Ejercicios prácticos](#14-ejercicios-prácticos)
15. [Resumen del tema](#15-resumen-del-tema)

---

## 1. Programación funcional en Java

La **programación funcional** es un paradigma que trata las funciones como valores de primera clase: pueden pasarse como argumentos, retornarse de otras funciones y asignarse a variables.

Java 8 incorporó el estilo funcional sin abandonar la POO. La combinación permite escribir código más conciso, expresivo y fácil de paralelizar.

### Estilo imperativo vs. funcional

```java
List<Empleado> empleados = obtenerEmpleados();

// ESTILO IMPERATIVO (cómo hacerlo paso a paso)
List<String> nombresAltoSalario = new ArrayList<>();
for (Empleado e : empleados) {
    if (e.getSalario() > 3000) {
        nombresAltoSalario.add(e.getNombre().toUpperCase());
    }
}
Collections.sort(nombresAltoSalario);

// ESTILO FUNCIONAL (qué queremos, no cómo)
List<String> nombresAltoSalario = empleados.stream()
    .filter(e -> e.getSalario() > 3000)
    .map(e -> e.getNombre().toUpperCase())
    .sorted()
    .collect(Collectors.toList());
```

### Los tres pilares funcionales en Java

```
1. LAMBDAS              2. STREAMS             3. OPTIONAL
────────────────────    ─────────────────────  ────────────────
Funciones anónimas      Pipeline de            Contenedor para
como objetos            operaciones sobre      valores que pueden
                        colecciones de         ser null
int→int:  n -> n*2      datos                  Optional.of(valor)
String→bool: s->s>5     lista.stream()         .map(...)
                        .filter(...)           .orElse(default)
                        .collect(...)
```

---

## 2. Expresiones lambda — repaso y profundización

### Sintaxis completa

```java
// Forma más corta (un parámetro, una expresión)
Runnable r = () -> System.out.println("Hola");
Consumer<String> c = s -> System.out.println(s);
Function<String, Integer> f = s -> s.length();
Predicate<Integer> p = n -> n > 0;

// Con paréntesis y tipo explícito
Function<String, Integer> f2 = (String s) -> s.length();

// Con bloque de código (múltiples líneas)
Function<Integer, String> clasif = n -> {
    if (n < 0) return "Negativo";
    if (n == 0) return "Cero";
    return "Positivo";
};

// Múltiples parámetros
BinaryOperator<Integer> suma = (a, b) -> a + b;
Comparator<String> comp = (a, b) -> a.compareTo(b);

// Sin parámetros
Supplier<String> saludo = () -> "¡Hola Mundo!";
```

### Las interfaces funcionales de java.util.function

```java
// Function<T, R>: T → R
Function<String, Integer> longitud    = String::length;
Function<Integer, Integer> cuadrado   = n -> n * n;
Function<String, String> mayusculas   = String::toUpperCase;

// Composición de funciones
Function<String, Integer> longMayusc  = mayusculas.andThen(longitud);
Function<String, Integer> longMayusc2 = longitud.compose(mayusculas); // mismo efecto

// BiFunction<T, U, R>: (T, U) → R
BiFunction<String, Integer, String> repetir = (s, n) -> s.repeat(n);
System.out.println(repetir.apply("Java", 3)); // JavaJavaJava

// Predicate<T>: T → boolean
Predicate<String> esVacio  = String::isEmpty;
Predicate<String> esLargo  = s -> s.length() > 5;
Predicate<Integer> esPar   = n -> n % 2 == 0;

// Combinar predicados
Predicate<String> esLargoYNoVacio = esLargo.and(esVacio.negate());
Predicate<Integer> esParONeg      = esPar.or(n -> n < 0);
Predicate<Integer> esImpar        = esPar.negate();

// Consumer<T>: T → void
Consumer<String> imprimir = System.out::println;
Consumer<String> loguear  = s -> System.err.println("[LOG] " + s);
Consumer<String> ambos    = imprimir.andThen(loguear);

// Supplier<T>: () → T
Supplier<String> saludo      = () -> "¡Hola!";
Supplier<List<String>> lista = ArrayList::new;

// UnaryOperator<T>: T → T (mismo tipo)
UnaryOperator<String> limpiar = String::trim;
UnaryOperator<Integer> doble  = n -> n * 2;

// BinaryOperator<T>: (T, T) → T
BinaryOperator<Integer> suma  = Integer::sum;
BinaryOperator<String> concat = String::concat;
BinaryOperator<Integer> max   = Integer::max;

// Comparator<T>: ((T, T) → int) + métodos default útiles
Comparator<String> porLong   = Comparator.comparingInt(String::length);
Comparator<String> porLongDesc = porLong.reversed();
Comparator<String> porLongLuegoAlf = porLong.thenComparing(Comparator.naturalOrder());
```

### Captura de variables en lambdas

Las lambdas pueden capturar variables del ámbito externo, pero estas deben ser **efectivamente finales** (no modificadas después de la captura):

```java
String prefijo = "Java";     // Efectivamente final (no se reasigna)
List<String> resultado = lista.stream()
    .filter(s -> s.startsWith(prefijo))  // Captura 'prefijo'
    .collect(Collectors.toList());

// ERROR: la variable capturada no puede ser reasignada
// prefijo = "Python";       // Esto rompería la lambda

// Contadores: problema clásico con lambdas
int[] contador = {0};        // Workaround: array de un elemento (el array es final)
lista.forEach(s -> contador[0]++);  // Modifica el contenido, no la referencia
System.out.println(contador[0]);
```

---

## 3. Referencias a métodos

Las **referencias a métodos** son una forma aún más concisa de lambdas cuando la lambda simplemente llama a un método existente.

### Los cuatro tipos de referencias

```java
// TIPO 1: Referencia a método estático
// Lambda:     n -> Math.abs(n)
// Referencia: Math::abs
Function<Integer, Integer> abs = Math::abs;
Function<String, Integer> parseInt = Integer::parseInt;

// TIPO 2: Referencia a método de instancia de un objeto específico
String prefijo = "Hola";
// Lambda:     s -> prefijo.startsWith(s)
// Referencia: prefijo::startsWith
Predicate<String> empieza = prefijo::startsWith;

// TIPO 3: Referencia a método de instancia de tipo arbitrario
// Lambda:     s -> s.toUpperCase()
// Referencia: String::toUpperCase
Function<String, String> mayusc = String::toUpperCase;
Function<String, Integer> len   = String::length;
Predicate<String> isEmpty       = String::isEmpty;

// TIPO 4: Referencia a constructor
// Lambda:     () -> new ArrayList<>()
// Referencia: ArrayList::new
Supplier<List<String>> crearLista   = ArrayList::new;
Function<String, StringBuilder> sbF = StringBuilder::new;
BiFunction<Integer, Integer, int[]> arr = int[]::new;  // Array constructor
```

### Comparación lambda vs. referencia

```java
// Antes (lambda)        →  Después (referencia)
s -> s.toUpperCase()    →  String::toUpperCase
s -> System.out.println(s) → System.out::println
(a, b) -> a.compareTo(b) → String::compareTo
n -> new Persona(n)     →  Persona::new
() -> new ArrayList<>() →  ArrayList::new
s -> s.isEmpty()        →  String::isEmpty
n -> Math.sqrt(n)       →  Math::sqrt
```

### Uso en streams

```java
List<String> nombres = List.of("Ana", "Luis", "María", "Carlos", "Sofía");

// Con lambdas
nombres.stream()
    .filter(s -> !s.isEmpty())
    .map(s -> s.toUpperCase())
    .forEach(s -> System.out.println(s));

// Con referencias a métodos (más legible)
nombres.stream()
    .filter(Predicate.not(String::isEmpty))
    .map(String::toUpperCase)
    .forEach(System.out::println);
```

---

## 4. ¿Qué es un Stream?

Un **Stream** es una secuencia de elementos que soporta operaciones de procesamiento en forma de pipeline. No almacena datos; los procesa sobre la marcha desde una fuente (colección, array, archivo, etc.).

### Características fundamentales de los Streams

```
1. NO ALMACENAN DATOS      2. SON PEREZOSOS (LAZY)     3. SON CONSUMIBLES
────────────────────────   ─────────────────────────   ──────────────────
Un stream no es una        Las operaciones intermedias  Un stream solo puede
colección. Obtiene datos   no se ejecutan hasta que     usarse una vez. Después
de su fuente y los         una operación terminal       de la operación terminal,
procesa sin copiarlos.     lo activa.                   está cerrado.

4. PUEDEN SER INFINITOS    5. PUEDEN PARALELIZARSE
────────────────────────   ────────────────────────────
Stream.iterate(0, n->n+1)  stream.parallel()
genera infinitos enteros   usa múltiples hilos
(procesados lazily)        automáticamente
```

### El pipeline de un Stream

```
FUENTE          OPERACIONES INTERMEDIAS         OPERACIÓN TERMINAL
──────────────  ────────────────────────────    ──────────────────
Lista           filter → map → sorted → limit   collect / forEach
Array           (LAZY: no se ejecutan             / reduce / count
Archivo         hasta que se necesita)            / findFirst / ...
Rango
Generador
```

```java
// Visualizando el pipeline:
List<String> resultado = Stream.of("Java", "Python", "C++", "Kotlin", "Go", "Rust")
    .filter(s -> s.length() > 3)   // [Java, Python, Kotlin, Rust]
    .map(String::toLowerCase)       // [java, python, kotlin, rust]
    .sorted()                       // [java, kotlin, python, rust]
    .limit(3)                       // [java, kotlin, python]
    .collect(Collectors.toList());  // ← AQUÍ se ejecuta todo el pipeline

System.out.println(resultado); // [java, kotlin, python]
```

---

## 5. Crear Streams

### Desde colecciones

```java
List<String> lista = List.of("A", "B", "C");
Stream<String> s1 = lista.stream();          // Stream secuencial
Stream<String> s2 = lista.parallelStream();  // Stream paralelo

Set<Integer> set = Set.of(1, 2, 3);
Stream<Integer> s3 = set.stream();

Map<String, Integer> map = Map.of("a", 1, "b", 2);
Stream<Map.Entry<String, Integer>> s4 = map.entrySet().stream();
Stream<String> claves  = map.keySet().stream();
Stream<Integer> valores = map.values().stream();
```

### Desde arrays

```java
String[] arr = {"Java", "Kotlin", "Scala"};
Stream<String> s1 = Arrays.stream(arr);
Stream<String> s2 = Arrays.stream(arr, 1, 3);  // Subarray [Kotlin, Scala]
Stream<String> s3 = Stream.of(arr);             // Equivalente

int[] ints = {1, 2, 3, 4, 5};
IntStream s4 = Arrays.stream(ints);             // IntStream (primitivo)
```

### Stream.of() y Stream.empty()

```java
Stream<String> s1 = Stream.of("uno", "dos", "tres");
Stream<Integer> s2 = Stream.of(1, 2, 3, 4, 5);
Stream<String> s3 = Stream.empty();             // Stream sin elementos
```

### Stream.generate() e Stream.iterate() — streams infinitos

```java
// generate: genera elementos llamando al Supplier repetidamente
Stream<Double> aleatorios = Stream.generate(Math::random);
Stream<String> holas      = Stream.generate(() -> "Hola");
Stream<UUID>   uuids      = Stream.generate(UUID::randomUUID);

// Usar con limit para no procesar infinitamente
aleatorios.limit(5).forEach(System.out::println);

// iterate: genera secuencias con una semilla y una función
Stream<Integer> enteros = Stream.iterate(0, n -> n + 1);        // 0,1,2,3,...
Stream<Integer> pares   = Stream.iterate(0, n -> n + 2);        // 0,2,4,6,...
Stream<Long>    fib     = Stream.iterate(new long[]{0, 1},
    a -> new long[]{a[1], a[0]+a[1]}).map(a -> a[0]);

enteros.limit(10).forEach(n -> System.out.print(n + " ")); // 0 1 2 3 4 5 6 7 8 9

// iterate con Predicate (Java 9+): como un for con condición
Stream<Integer> hastaCien = Stream.iterate(0, n -> n < 100, n -> n + 5);
// 0, 5, 10, 15, ..., 95
```

### Desde cadenas de texto

```java
// Stream de líneas de un String
String texto = "Línea 1\nLínea 2\nLínea 3";
Stream<String> lineas = texto.lines();  // Java 11+

// Stream de caracteres (IntStream)
IntStream chars = "Hello".chars();
chars.forEach(c -> System.out.print((char) c + " ")); // H e l l o

// Stream de patrones
Stream<String> palabras = java.util.regex.Pattern
    .compile("\\s+")
    .splitAsStream("Hola Mundo Java");
palabras.forEach(System.out::println); // Hola / Mundo / Java
```

### Desde Builder

```java
Stream.Builder<String> builder = Stream.builder();
builder.add("Java");
builder.add("Kotlin");
if (condicion) builder.add("Scala");
Stream<String> stream = builder.build();
```

---

## 6. Operaciones intermedias

Las operaciones intermedias transforman un Stream en otro Stream. Son **lazy**: no se ejecutan hasta que hay una operación terminal.

### 6.1 filter — filtrar elementos

```java
List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Solo pares
List<Integer> pares = numeros.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());  // [2, 4, 6, 8, 10]

// Múltiples condiciones
List<Empleado> empleados = obtenerEmpleados();
List<Empleado> filtrados = empleados.stream()
    .filter(e -> e.getDepartamento().equals("IT"))
    .filter(e -> e.getSalario() > 2500)
    .filter(e -> e.getAñosExperiencia() >= 3)
    .collect(Collectors.toList());

// Con Predicate compuesto
Predicate<Empleado> esIT   = e -> e.getDepartamento().equals("IT");
Predicate<Empleado> bienPagado = e -> e.getSalario() > 2500;
empleados.stream()
    .filter(esIT.and(bienPagado))
    .forEach(System.out::println);
```

### 6.2 map — transformar elementos

```java
List<String> nombres = List.of("Ana", "Luis", "María");

// Transformar tipo
List<Integer> longitudes = nombres.stream()
    .map(String::length)
    .collect(Collectors.toList());  // [3, 4, 5]

// Transformar a otro tipo de objeto
List<String> mayusculas = nombres.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());  // [ANA, LUIS, MARÍA]

// Extraer campo de objeto
List<Empleado> empleados = obtenerEmpleados();
List<String> nombres = empleados.stream()
    .map(Empleado::getNombre)
    .collect(Collectors.toList());

List<Double> salarios = empleados.stream()
    .map(Empleado::getSalario)
    .collect(Collectors.toList());

// Transformaciones encadenadas
List<String> resultado = empleados.stream()
    .map(e -> e.getNombre() + " - " + e.getDepartamento())
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

### 6.3 flatMap — aplanar streams anidados

`flatMap` transforma cada elemento en un Stream y luego aplana todos esos Streams en uno solo.

```java
// Sin flatMap: Stream de Listas → Stream<List<String>>
List<List<String>> listaDeListas = List.of(
    List.of("Java", "Kotlin"),
    List.of("Python", "Go"),
    List.of("C++", "Rust", "C")
);

// CON flatMap: aplanamos a Stream<String>
List<String> todos = listaDeListas.stream()
    .flatMap(Collection::stream)  // Aplana cada lista
    .collect(Collectors.toList());
// [Java, Kotlin, Python, Go, C++, Rust, C]

// Caso práctico: palabras de múltiples oraciones
List<String> oraciones = List.of(
    "Hola mundo Java",
    "Programación funcional",
    "Streams son potentes"
);

List<String> palabras = oraciones.stream()
    .flatMap(s -> Arrays.stream(s.split(" ")))
    .distinct()
    .sorted()
    .collect(Collectors.toList());
// [Hola, Java, Programación, Streams, funcional, mundo, potentes, son]

// flatMap con Optional (muy útil)
List<Optional<String>> opcionales = List.of(
    Optional.of("Java"),
    Optional.empty(),
    Optional.of("Kotlin")
);
List<String> presentes = opcionales.stream()
    .flatMap(Optional::stream)   // Java 9+: Optional.stream()
    .collect(Collectors.toList()); // [Java, Kotlin]
```

### 6.4 sorted — ordenar

```java
List<String> lenguajes = List.of("Java", "Python", "C", "Kotlin", "Go");

// Orden natural
lenguajes.stream()
    .sorted()
    .forEach(System.out::println);  // C, Go, Java, Kotlin, Python

// Con Comparator
lenguajes.stream()
    .sorted(Comparator.comparingInt(String::length))
    .forEach(System.out::println);  // C, Go, Java, Python, Kotlin

// Orden descendente
lenguajes.stream()
    .sorted(Comparator.reverseOrder())
    .forEach(System.out::println);  // Python, Kotlin, Java, Go, C

// Múltiples criterios
empleados.stream()
    .sorted(Comparator.comparing(Empleado::getDepartamento)
        .thenComparing(Comparator.comparingDouble(Empleado::getSalario).reversed()))
    .forEach(System.out::println);
```

### 6.5 distinct — eliminar duplicados

```java
List<Integer> conDuplicados = List.of(1, 2, 2, 3, 3, 3, 4, 5, 5);

List<Integer> sinDuplicados = conDuplicados.stream()
    .distinct()
    .collect(Collectors.toList());  // [1, 2, 3, 4, 5]
```

### 6.6 limit y skip — paginar

```java
Stream<Integer> infinitoNaturales = Stream.iterate(1, n -> n + 1);

// Tomar solo los primeros N
List<Integer> primerosCinco = infinitoNaturales
    .limit(5)
    .collect(Collectors.toList());  // [1, 2, 3, 4, 5]

// Saltar los primeros N (paginación)
List<Integer> pagina2 = Stream.iterate(1, n -> n + 1)
    .skip(10)   // Salta los primeros 10
    .limit(5)   // Toma los siguientes 5
    .collect(Collectors.toList());  // [11, 12, 13, 14, 15]
```

### 6.7 peek — inspeccionar sin modificar

`peek` es útil para depuración: permite ver los elementos en un punto del pipeline sin modificarlos.

```java
List<String> resultado = Stream.of("Java", "Python", "C++", "Kotlin")
    .peek(s -> System.out.println("Antes del filter: " + s))
    .filter(s -> s.length() > 3)
    .peek(s -> System.out.println("Después del filter: " + s))
    .map(String::toUpperCase)
    .peek(s -> System.out.println("Después del map: " + s))
    .collect(Collectors.toList());
```

### 6.8 mapToInt, mapToDouble, mapToLong

Convierten a streams especializados de primitivos (más eficientes):

```java
List<String> palabras = List.of("Java", "Python", "Go");

// mapToInt: accede a operaciones de IntStream
int totalChars = palabras.stream()
    .mapToInt(String::length)
    .sum();  // 4 + 6 + 2 = 12

double promedio = palabras.stream()
    .mapToInt(String::length)
    .average()
    .orElse(0);  // 4.0

// mapToDouble para salarios
OptionalDouble promSalario = empleados.stream()
    .mapToDouble(Empleado::getSalario)
    .average();
```

### 6.9 takeWhile y dropWhile (Java 9+)

```java
// takeWhile: toma elementos MIENTRAS se cumple la condición
List<Integer> numeros = List.of(2, 4, 6, 7, 8, 10, 12);
List<Integer> pares = numeros.stream()
    .takeWhile(n -> n % 2 == 0)  // Para cuando encuentra 7 (impar)
    .collect(Collectors.toList());  // [2, 4, 6]

// dropWhile: descarta elementos MIENTRAS se cumple la condición
List<Integer> resto = numeros.stream()
    .dropWhile(n -> n % 2 == 0)  // Descarta hasta 7
    .collect(Collectors.toList());  // [7, 8, 10, 12]
```

---

## 7. Operaciones terminales

Las operaciones terminales producen un resultado (o efecto secundario) y **consumen** el stream. Después de una operación terminal, el stream no puede reutilizarse.

### 7.1 collect — recopilar en una colección

Es la operación terminal más versátil (la veremos en profundidad en la sección 8).

```java
List<String> lista = stream.collect(Collectors.toList());
Set<String> set = stream.collect(Collectors.toSet());
```

### 7.2 forEach y forEachOrdered

```java
List<String> nombres = List.of("Ana", "Luis", "María");

// forEach: procesa cada elemento (el orden no está garantizado en paralelo)
nombres.stream().forEach(System.out::println);

// forEachOrdered: garantiza el orden del encuentro
nombres.parallelStream().forEachOrdered(System.out::println);
```

### 7.3 count — contar elementos

```java
long cantPares = numeros.stream()
    .filter(n -> n % 2 == 0)
    .count();

long cantEmpleados = empleados.stream()
    .filter(e -> e.getSalario() > 3000)
    .count();
```

### 7.4 min y max

```java
Optional<Integer> minimo = numeros.stream().min(Integer::compare);
Optional<Integer> maximo = numeros.stream().max(Integer::compare);

Optional<Empleado> mejor = empleados.stream()
    .max(Comparator.comparingDouble(Empleado::getSalario));

mejor.ifPresent(e ->
    System.out.println("Mejor pagado: " + e.getNombre()));
```

### 7.5 findFirst y findAny

```java
// findFirst: el primer elemento que pase el filtro (respeta orden)
Optional<String> primero = nombres.stream()
    .filter(s -> s.startsWith("M"))
    .findFirst();  // Optional[María]

// findAny: cualquier elemento (más eficiente en paralelo)
Optional<String> cualquiera = nombres.parallelStream()
    .filter(s -> s.length() > 3)
    .findAny();
```

### 7.6 anyMatch, allMatch, noneMatch

```java
List<Integer> nums = List.of(2, 4, 6, 7, 8);

boolean hayImpar    = nums.stream().anyMatch(n -> n % 2 != 0);   // true
boolean todosPares  = nums.stream().allMatch(n -> n % 2 == 0);   // false
boolean ningunNeg   = nums.stream().noneMatch(n -> n < 0);       // true

// Con short-circuit: para tan pronto como puede determinar el resultado
// anyMatch para cuando encuentra el primero
// allMatch para cuando encuentra el primero que falla
// noneMatch para cuando encuentra el primero que cumple
```

### 7.7 reduce — reducir a un solo valor

```java
List<Integer> nums = List.of(1, 2, 3, 4, 5);

// Con valor inicial (identidad)
int suma    = nums.stream().reduce(0, Integer::sum);        // 15
int producto = nums.stream().reduce(1, (a, b) -> a * b);   // 120
int max     = nums.stream().reduce(Integer.MIN_VALUE, Integer::max); // 5

// Sin valor inicial: devuelve Optional
Optional<Integer> sumaOpt = nums.stream().reduce(Integer::sum);
sumaOpt.ifPresent(s -> System.out.println("Suma: " + s)); // 15

// Reduce más complejo: concatenar strings
List<String> palabras = List.of("Java", "es", "genial");
String frase = palabras.stream()
    .reduce("", (a, b) -> a.isEmpty() ? b : a + " " + b);
// "Java es genial"

// BinaryOperator para strings
String resultado = palabras.stream()
    .reduce((a, b) -> a + " " + b)
    .orElse("");
```

### 7.8 toArray — convertir a array

```java
String[] arr1 = stream.toArray(String[]::new);
Object[] arr2 = stream.toArray();  // Object[] (sin cast)
```

### 7.9 iterator y spliterator

```java
// Obtener iterador para iterar manualmente
Iterator<String> it = stream.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
}
```

---

## 8. Collectors — recolectores avanzados

La clase `Collectors` proporciona implementaciones predefinidas de `Collector` para las operaciones de recolección más comunes.

### 8.1 Colecciones básicas

```java
import static java.util.stream.Collectors.*;

List<String> lista          = stream.collect(toList());
List<String> listaNoModif   = stream.collect(toUnmodifiableList());  // Java 10+
Set<String> conjunto        = stream.collect(toSet());
Set<String> setNoModif      = stream.collect(toUnmodifiableSet());

// toCollection: control del tipo exacto
LinkedList<String> ll       = stream.collect(toCollection(LinkedList::new));
TreeSet<String> ts          = stream.collect(toCollection(TreeSet::new));
```

### 8.2 joining — unir strings

```java
List<String> nombres = List.of("Ana", "Luis", "María", "Carlos");

String simple    = nombres.stream().collect(joining());            // AnaLuisMaríaCarlos
String conSep    = nombres.stream().collect(joining(", "));        // Ana, Luis, María, Carlos
String completo  = nombres.stream().collect(joining(", ", "[", "]")); // [Ana, Luis, María, Carlos]

// Caso práctico: construir SQL
String campos = List.of("nombre", "edad", "email").stream()
    .collect(joining(", "));
// "nombre, edad, email"
String sql = "SELECT " + campos + " FROM usuarios";
```

### 8.3 counting, summingInt, averagingDouble

```java
long cantidad = empleados.stream().collect(counting());

int totalHoras = empleados.stream()
    .collect(summingInt(Empleado::getHorasTrabajadas));

double promSalario = empleados.stream()
    .collect(averagingDouble(Empleado::getSalario));

// IntSummaryStatistics: varios estadísticos de una vez
IntSummaryStatistics stats = empleados.stream()
    .collect(summarizingInt(Empleado::getEdad));
System.out.println("Count: " + stats.getCount());
System.out.println("Sum:   " + stats.getSum());
System.out.println("Min:   " + stats.getMin());
System.out.println("Max:   " + stats.getMax());
System.out.println("Avg:   " + stats.getAverage());
```

### 8.4 groupingBy — agrupar

`groupingBy` es uno de los collectors más poderosos.

```java
// Agrupación simple
Map<String, List<Empleado>> porDepto = empleados.stream()
    .collect(groupingBy(Empleado::getDepartamento));
// {"IT": [e1, e3, e7], "RRHH": [e2, e5], ...}

// Agrupación con downstream collector
Map<String, Long> contPorDepto = empleados.stream()
    .collect(groupingBy(Empleado::getDepartamento, counting()));
// {"IT": 3, "RRHH": 2, ...}

Map<String, Double> promSalPorDepto = empleados.stream()
    .collect(groupingBy(Empleado::getDepartamento,
        averagingDouble(Empleado::getSalario)));

Map<String, List<String>> nombresPorDepto = empleados.stream()
    .collect(groupingBy(Empleado::getDepartamento,
        mapping(Empleado::getNombre, toList())));

Map<String, Optional<Empleado>> mejorPorDepto = empleados.stream()
    .collect(groupingBy(Empleado::getDepartamento,
        maxBy(Comparator.comparingDouble(Empleado::getSalario))));

// Agrupación multinivel (groupingBy anidado)
Map<String, Map<String, List<Empleado>>> porDeptoYCiudad = empleados.stream()
    .collect(groupingBy(Empleado::getDepartamento,
        groupingBy(Empleado::getCiudad)));
```

### 8.5 partitioningBy — particionar en dos grupos

```java
// Divide en exactamente DOS grupos: true y false
Map<Boolean, List<Empleado>> partition = empleados.stream()
    .collect(partitioningBy(e -> e.getSalario() > 3000));

List<Empleado> bienPagados = partition.get(true);
List<Empleado> normalPago  = partition.get(false);

// Con downstream
Map<Boolean, Long> contParticion = empleados.stream()
    .collect(partitioningBy(e -> e.getSalario() > 3000, counting()));

Map<Boolean, Double> promPorParticion = empleados.stream()
    .collect(partitioningBy(e -> e.getEdad() >= 30,
        averagingDouble(Empleado::getSalario)));
```

### 8.6 toMap — recolectar a Map

```java
// Básico: clave → valor
Map<Integer, Empleado> porId = empleados.stream()
    .collect(toMap(Empleado::getId, e -> e));
// O más conciso:
Map<Integer, Empleado> porId2 = empleados.stream()
    .collect(toMap(Empleado::getId, Function.identity()));

// Solo nombres
Map<Integer, String> idANombre = empleados.stream()
    .collect(toMap(Empleado::getId, Empleado::getNombre));

// Cuando puede haber claves duplicadas: merge function
Map<String, Double> salarioPorDepto = empleados.stream()
    .collect(toMap(
        Empleado::getDepartamento,
        Empleado::getSalario,
        Double::sum));  // Si hay colisión: suma los salarios

// Control del tipo del mapa resultante
Map<Integer, String> linkedMap = empleados.stream()
    .collect(toMap(
        Empleado::getId,
        Empleado::getNombre,
        (v1, v2) -> v1,          // En caso de duplicado: mantener el primero
        LinkedHashMap::new));     // Tipo del mapa: LinkedHashMap
```

### 8.7 Collector personalizado con Collectors.teeing (Java 12+)

```java
// Aplica dos collectors y combina sus resultados
record EstadisticasSimples(double promedio, long cantidad) {}

EstadisticasSimples stats = empleados.stream()
    .collect(Collectors.teeing(
        averagingDouble(Empleado::getSalario),
        counting(),
        EstadisticasSimples::new));

System.out.printf("Promedio: %.2f | Cantidad: %d%n",
    stats.promedio(), stats.cantidad());
```

---

## 9. Streams especializados — IntStream, LongStream, DoubleStream

Los streams de primitivos evitan el boxing/unboxing y son más eficientes para operaciones numéricas.

```java
// Crear IntStream
IntStream r1 = IntStream.range(1, 6);        // 1, 2, 3, 4, 5 (sin incluir 6)
IntStream r2 = IntStream.rangeClosed(1, 5);  // 1, 2, 3, 4, 5 (incluye 5)
IntStream r3 = IntStream.of(10, 20, 30);
IntStream r4 = "Hello".chars();              // Stream de códigos ASCII

// Operaciones estadísticas directas
int suma      = IntStream.rangeClosed(1, 100).sum();           // 5050
double prom   = IntStream.rangeClosed(1, 10).average().getAsDouble(); // 5.5
int min       = IntStream.of(5, 2, 8, 1).min().getAsInt();    // 1
int max       = IntStream.of(5, 2, 8, 1).max().getAsInt();    // 8
long count    = IntStream.range(0, 100).filter(n -> n%2==0).count(); // 50

IntSummaryStatistics stats = IntStream.rangeClosed(1, 10).summaryStatistics();

// Convertir entre tipos
IntStream    ints    = IntStream.range(1, 6);
LongStream   longs   = ints.asLongStream();
DoubleStream doubles = IntStream.range(1, 6).asDoubleStream();
Stream<Integer> boxed = IntStream.range(1, 6).boxed(); // int → Integer

// mapToObj: de primitivo a objeto
Stream<String> strings = IntStream.range(1, 6)
    .mapToObj(n -> "Número " + n);

// Streams numéricos útiles para rangos
List<Integer> listaRango = IntStream.rangeClosed(1, 10)
    .boxed()
    .collect(Collectors.toList());

// Factorial con reduce
long factorial10 = LongStream.rangeClosed(1, 10).reduce(1L, Long::product);

// Suma de cuadrados
int sumaCuadrados = IntStream.rangeClosed(1, 5)
    .map(n -> n * n)
    .sum();  // 1+4+9+16+25 = 55

// Números primos hasta N
int N = 50;
List<Integer> primos = IntStream.rangeClosed(2, N)
    .filter(n -> IntStream.rangeClosed(2, (int) Math.sqrt(n))
        .allMatch(d -> n % d != 0))
    .boxed()
    .collect(Collectors.toList());
```

---

## 10. Optional — manejo elegante de null

`Optional<T>` es un contenedor que puede o no contener un valor no-null. Su propósito es evitar los `NullPointerException` y hacer explícito cuando un valor puede estar ausente.

### Crear Optional

```java
// Crear con valor presente
Optional<String> presente = Optional.of("Java");         // Lanza NPE si null
Optional<String> nullable = Optional.ofNullable(valor);  // Acepta null → empty
Optional<String> vacio    = Optional.empty();             // Siempre vacío
```

### Verificar y obtener el valor

```java
Optional<String> opt = Optional.of("Hola");

// Verificar
opt.isPresent();    // true
opt.isEmpty();      // false (Java 11+)

// Obtener — distintas formas
opt.get();                               // "Hola" (lanza NoSuchElementException si vacío)
opt.orElse("Default");                  // "Hola" (o "Default" si vacío)
opt.orElseGet(() -> calcularDefault()); // "Hola" (o calcula si vacío — lazy)
opt.orElseThrow();                      // "Hola" (lanza NoSuchElementException si vacío)
opt.orElseThrow(() -> new MiException("No encontrado")); // Excepción personalizada
```

### Transformar Optional

```java
Optional<String> nombre = Optional.of("ana garcía");

// map: transforma el valor si está presente
Optional<String> mayusc = nombre.map(String::toUpperCase);  // Optional["ANA GARCÍA"]
Optional<Integer> longitud = nombre.map(String::length);    // Optional[10]

// flatMap: cuando la función ya devuelve Optional
Optional<String> resultado = nombre
    .flatMap(n -> n.isEmpty() ? Optional.empty() : Optional.of(n));

// filter: filtra el valor
Optional<String> largo = nombre.filter(s -> s.length() > 5); // Optional["ana garcía"]
Optional<String> corto = nombre.filter(s -> s.length() < 3); // Optional.empty()

// ifPresent: ejecuta acción si está presente
nombre.ifPresent(n -> System.out.println("Nombre: " + n));

// ifPresentOrElse (Java 9+): rama para presente y para vacío
nombre.ifPresentOrElse(
    n -> System.out.println("Presente: " + n),
    () -> System.out.println("Vacío")
);

// or (Java 9+): proporciona Optional alternativo si está vacío
Optional<String> fallback = nombre
    .filter(n -> n.startsWith("Z"))
    .or(() -> Optional.of("Sin nombre"));
```

### Encadenamiento fluido con Optional

```java
// Sin Optional: anidamiento de nulls
String ciudad = null;
if (usuario != null) {
    Direccion dir = usuario.getDireccion();
    if (dir != null) {
        ciudad = dir.getCiudad();
    }
}
String resultado = ciudad != null ? ciudad : "Desconocida";

// Con Optional: fluido y sin nulls
String resultado = Optional.ofNullable(usuario)
    .map(Usuario::getDireccion)
    .map(Direccion::getCiudad)
    .orElse("Desconocida");
```

### Optional en métodos — cuándo usarlo

```java
// BIEN: como tipo de retorno cuando el valor puede estar ausente
public Optional<Empleado> buscarPorId(int id) {
    return empleados.stream()
        .filter(e -> e.getId() == id)
        .findFirst();
}

// USO correcto:
buscarPorId(42)
    .map(Empleado::getNombre)
    .ifPresent(nombre -> System.out.println("Encontrado: " + nombre));

// MAL: no usar Optional como parámetro de método
public void procesar(Optional<String> nombre) { }  // Antipatrón

// MAL: no usar Optional como campo de clase
private Optional<String> nombre;  // Antipatrón (no es Serializable)

// BIEN: usar Optional solo como tipo de retorno
```

---

## 11. Streams paralelos

Los streams paralelos dividen el trabajo entre múltiples hilos automáticamente usando el ForkJoin pool de Java.

```java
// Crear stream paralelo
Stream<Integer> paralelo1 = lista.parallelStream();
Stream<Integer> paralelo2 = lista.stream().parallel();

// El mismo código que el secuencial, pero más rápido para grandes colecciones
long suma = LongStream.rangeClosed(1, 1_000_000_000L)
    .parallel()
    .sum();

// Comparación de tiempo:
long inicio = System.currentTimeMillis();
long sumaSeq = LongStream.rangeClosed(1, 100_000_000L).sum();
System.out.println("Secuencial: " + (System.currentTimeMillis() - inicio) + "ms");

inicio = System.currentTimeMillis();
long sumaPar = LongStream.rangeClosed(1, 100_000_000L).parallel().sum();
System.out.println("Paralelo:   " + (System.currentTimeMillis() - inicio) + "ms");
```

### Cuándo usar streams paralelos

```
ÚSALOS cuando:                      NO LOS USES cuando:
────────────────────────────────    ─────────────────────────────
✓ Colecciones grandes (>10,000)     ✗ Colecciones pequeñas
✓ Operaciones CPU-intensivas        ✗ Operaciones de I/O (archivos, red)
✓ Operaciones independientes        ✗ Operaciones con efectos secundarios
✓ Sin estado compartido mutable     ✗ Orden del resultado importa mucho
✓ El costo de sincronización        ✗ Estructuras no thread-safe
  es pequeño vs. el trabajo
```

### Cuidados con streams paralelos

```java
// PELIGRO: efecto secundario no thread-safe
List<Integer> resultados = new ArrayList<>();  // No thread-safe
IntStream.range(0, 100).parallel().forEach(resultados::add); // Comportamiento indefinido

// BIEN: usar collect (thread-safe)
List<Integer> resultados = IntStream.range(0, 100)
    .parallel()
    .boxed()
    .collect(Collectors.toList());  // Seguro

// PELIGRO: orden no garantizado con forEach en paralelo
IntStream.range(0, 5).parallel().forEach(System.out::println);
// Puede imprimir: 2, 0, 3, 1, 4 (orden no determinista)

// BIEN: forEachOrdered si el orden importa (pero más lento)
IntStream.range(0, 5).parallel().forEachOrdered(System.out::println);
// Siempre imprime: 0, 1, 2, 3, 4
```

---

## 12. Streams y excepciones

Las lambdas en streams no pueden lanzar excepciones checked directamente. Hay varias estrategias para manejarlo.

### El problema

```java
// ERROR: Lambda no puede lanzar IOException (checked)
List<String> lineas = archivos.stream()
    .map(f -> Files.readString(f))  // ERROR: Unhandled exception
    .collect(Collectors.toList());
```

### Solución 1: Envolver en try-catch dentro de la lambda

```java
List<String> lineas = archivos.stream()
    .map(f -> {
        try {
            return Files.readString(f);
        } catch (IOException e) {
            return "";  // Valor por defecto o relanzar como unchecked
        }
    })
    .collect(Collectors.toList());
```

### Solución 2: Método wrapper que convierte checked en unchecked

```java
// Método de utilidad general
public static <T, R> Function<T, R> unchecked(FunctionChecked<T, R> f) {
    return t -> {
        try {
            return f.apply(t);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    };
}

@FunctionalInterface
interface FunctionChecked<T, R> {
    R apply(T t) throws Exception;
}

// Uso limpio
List<String> lineas = archivos.stream()
    .map(unchecked(f -> Files.readString(f)))
    .collect(Collectors.toList());
```

### Solución 3: Usar Optional para manejar errores

```java
// Función que retorna Optional en lugar de lanzar
Function<Path, Optional<String>> leerSeguros = f -> {
    try {
        return Optional.of(Files.readString(f));
    } catch (IOException e) {
        System.err.println("Error leyendo " + f + ": " + e.getMessage());
        return Optional.empty();
    }
};

List<String> lineas = archivos.stream()
    .map(leerSeguros)
    .flatMap(Optional::stream)   // Descarta los vacíos
    .collect(Collectors.toList());
```

---

## 13. Errores comunes con Streams

### Error 1: Reutilizar un stream ya consumido

```java
Stream<String> stream = List.of("A", "B", "C").stream();
stream.forEach(System.out::println);  // OK
stream.count();  // IllegalStateException: stream has already been operated upon or closed
```

### Error 2: Efectos secundarios en operaciones intermedias

```java
List<String> externo = new ArrayList<>();

// ✗ MAL: efecto secundario en operación intermedia
Stream.of("A", "B", "C")
    .filter(s -> { externo.add(s); return true; })  // No determinista en paralelo
    .count();

// ✓ BIEN: collect para resultados
List<String> resultado = Stream.of("A", "B", "C").collect(Collectors.toList());
```

### Error 3: Olvidar la operación terminal (el stream nunca se ejecuta)

```java
// ✗ MAL: sin operación terminal, NADA se ejecuta (lazy evaluation)
empleados.stream()
    .filter(e -> e.getSalario() > 5000)
    .map(Empleado::getNombre);
// Las operaciones son lazy: sin terminal no pasa nada

// ✓ BIEN: siempre terminar con una operación terminal
List<String> nombres = empleados.stream()
    .filter(e -> e.getSalario() > 5000)
    .map(Empleado::getNombre)
    .collect(Collectors.toList());  // ← Terminal obligatorio
```

### Error 4: Usar streams para modificar la colección fuente

```java
// ✗ MAL: modificar la fuente mientras se procesa
for (String s : lista) {
    if (condicion(s)) lista.remove(s);  // ConcurrentModificationException
}

// ✓ BIEN: crear nueva lista o usar removeIf
List<String> nueva = lista.stream()
    .filter(s -> !condicion(s))
    .collect(Collectors.toList());

// O más simple:
lista.removeIf(s -> condicion(s));
```

### Error 5: Optional.get() sin verificar

```java
Optional<String> opt = encontrarNombre();
String nombre = opt.get();  // NoSuchElementException si está vacío

// ✓ BIEN:
String nombre = opt.orElse("Sin nombre");
String nombre2 = opt.orElseThrow(() -> new RuntimeException("No encontrado"));
```

### Error 6: Usar peek como operación de negocio (no solo para debug)

```java
// ✗ MAL: usar peek para lógica de negocio (no garantizado en paralelo)
stream.peek(empleado -> guardarEnBD(empleado))
      .collect(Collectors.toList());

// ✓ BIEN: usar forEach como terminal si el propósito es el efecto
stream.forEach(empleado -> guardarEnBD(empleado));
```

---

## 14. Ejercicios prácticos

### Ejercicio 1 — Procesamiento de lista de empleados (Nivel: Básico)

Dado un `List<Empleado>` con nombre, departamento, salario, edad y años de experiencia:

1. Lista de nombres de empleados del depto "IT" ordenados alfabéticamente
2. Salario total de la empresa
3. Salario promedio por departamento (`Map<String, Double>`)
4. El empleado con mayor salario de cada departamento
5. Número de empleados por departamento
6. Lista de los 3 empleados mejor pagados
7. ¿Hay algún empleado con más de 10 años de experiencia?
8. ¿Todos los empleados tienen salario positivo?
9. Los nombres de todos los empleados separados por coma
10. Empleados agrupados en "senior" (>5 años) y "junior" (<=5 años)

---

### Ejercicio 2 — Análisis de texto (Nivel: Básico-Medio)

Dado un texto largo (usa el discurso de apertura de un libro o similar):

1. Contar el número total de palabras
2. Contar el número de palabras únicas (sin distinción de mayúsculas)
3. Las 10 palabras más frecuentes con su frecuencia
4. Palabras de más de 7 letras ordenadas por longitud descendente (sin duplicados)
5. Longitud promedio de las palabras
6. Porcentaje de palabras que empiezan con vocal
7. La línea más larga
8. Número de líneas que contienen la palabra "Java" (o la que elijas)

---

### Ejercicio 3 — Pipeline de transformación de datos (Nivel: Medio)

Crea un sistema de procesamiento de pedidos:

```java
record Producto(String codigo, String nombre, String categoria,
                double precio, int stockDisponible) {}

record LineaPedido(Producto producto, int cantidad) {
    double subtotal() { return producto.precio() * cantidad; }
}

record Pedido(String id, String cliente, LocalDate fecha,
              List<LineaPedido> lineas, String estado) {
    double total() { return lineas.stream().mapToDouble(LineaPedido::subtotal).sum(); }
}
```

Con streams, calcula:
1. Total de ingresos de pedidos "COMPLETADO"
2. Categorías de productos más vendidas (por cantidad)
3. Clientes con más de $500 en pedidos completados
4. Productos agotados después de aplicar todos los pedidos
5. Ticket promedio por cliente
6. Top 5 productos más vendidos
7. Pedidos del mes actual agrupados por estado
8. Resumen estadístico de montos: min, max, promedio, suma

---

### Ejercicio 4 — Generación de datos con streams infinitos (Nivel: Medio)

Usando `Stream.generate()` e `Stream.iterate()`:

1. Genera los primeros 20 números de Fibonacci
2. Genera la secuencia de potencias de 2 hasta 2^20
3. Genera 10 contraseñas aleatorias de 12 caracteres (letras + números)
4. Simula el lanzamiento de un dado hasta que salgan 3 seises consecutivos y muestra cuántos lanzamientos tomó
5. Genera la serie de Taylor del seno de π/6 hasta que el término sea menor a 1e-10 y compara con `Math.sin(Math.PI/6)`

---

### Ejercicio 5 — Collectors avanzados (Nivel: Avanzado)

Con una lista de `Transaccion(id, tipo, monto, moneda, fecha, exitosa)`:

1. Total por moneda de transacciones exitosas (`Map<String, Double>`)
2. Transacciones agrupadas por mes y por tipo (`Map<Month, Map<String, List<Transaccion>>>`)
3. La transacción de mayor monto por tipo
4. Distribución por rangos de monto: (<100, 100-500, 500-1000, >1000) → cantidad y suma en cada rango
5. Crear un reporte como String usando `joining` con encabezado, cada línea formateada y pie con totales
6. Encontrar duplicados: transacciones con el mismo monto en la misma fecha y moneda

---

### Ejercicio 6 — Mini motor de consultas (Nivel: Avanzado)

Implementa un `Consulta<T>` genérica que encadene operaciones de forma fluida:

```java
public class Consulta<T> {
    private Stream<T> stream;

    public static <T> Consulta<T> de(Collection<T> coleccion)

    public Consulta<T> donde(Predicate<T> condicion)
    public Consulta<T> ordenadoPor(Comparator<T> comparator)
    public Consulta<T> limitado(int n)
    public Consulta<T> saltando(int n)
    public <R> Consulta<R> seleccionar(Function<T, R> selector)
    public <R> Consulta<R> expandir(Function<T, Collection<R>> expandidor)

    public List<T> toList()
    public Set<T> toSet()
    public Optional<T> primero()
    public long contar()
    public boolean existe(Predicate<T> condicion)
    public <K> Map<K, List<T>> agruparPor(Function<T, K> clasificador)
    public <K, V> Map<K, V> aMapa(Function<T, K> clave, Function<T, V> valor)
    public String unir(String separador)
}

// Uso final esperado (estilo SQL):
List<String> resultado = Consulta.de(empleados)
    .donde(e -> e.getDepartamento().equals("IT"))
    .donde(e -> e.getSalario() > 2000)
    .ordenadoPor(Comparator.comparing(Empleado::getNombre))
    .limitado(5)
    .seleccionar(Empleado::getNombre)
    .toList();
```

---

## 15. Resumen del tema

### El pipeline de Streams en una página

```java
// CREAR STREAM
stream = coleccion.stream()
stream = Arrays.stream(array)
stream = Stream.of(e1, e2, e3)
stream = Stream.generate(supplier)
stream = Stream.iterate(seed, f)
stream = IntStream.range(0, 10)

// OPERACIONES INTERMEDIAS (lazy, devuelven Stream)
.filter(predicate)          // Mantiene los que cumplen
.map(function)              // Transforma cada elemento
.flatMap(f)                 // Transforma y aplana
.sorted()                   // Ordena
.sorted(comparator)         // Ordena con Comparator
.distinct()                 // Elimina duplicados
.limit(n)                   // Toma los primeros n
.skip(n)                    // Salta los primeros n
.peek(consumer)             // Inspecciona sin modificar
.mapToInt/Long/Double(f)    // Convierte a stream primitivo
.takeWhile(predicate)       // Toma mientras se cumple
.dropWhile(predicate)       // Descarta mientras se cumple

// OPERACIONES TERMINALES (ejecutan el pipeline)
.collect(collector)         // Recopila en colección
.forEach(consumer)          // Procesa cada elemento
.count()                    // Cuenta elementos
.min(comparator)            // Mínimo
.max(comparator)            // Máximo
.findFirst()                // Primer elemento
.findAny()                  // Cualquier elemento
.anyMatch(predicate)        // ¿Alguno cumple?
.allMatch(predicate)        // ¿Todos cumplen?
.noneMatch(predicate)       // ¿Ninguno cumple?
.reduce(identity, op)       // Reduce a un valor
.toArray(T[]::new)          // Convierte a array

// COLLECTORS MÁS USADOS
Collectors.toList()
Collectors.toSet()
Collectors.toUnmodifiableList()
Collectors.joining(sep, prefix, suffix)
Collectors.groupingBy(classifier)
Collectors.groupingBy(classifier, downstream)
Collectors.partitioningBy(predicate)
Collectors.toMap(keyF, valueF)
Collectors.counting()
Collectors.summingInt/Double(f)
Collectors.averagingDouble(f)
Collectors.summarizingInt(f)

// OPTIONAL
Optional.of(valor)
Optional.ofNullable(valor)
Optional.empty()
.isPresent() / .isEmpty()
.get() / .orElse(def) / .orElseGet(sup)
.orElseThrow(exceptionSupplier)
.map(f) / .flatMap(f) / .filter(p)
.ifPresent(consumer)
.ifPresentOrElse(consumer, runnable)
```

### Reglas de oro

| Situación | Regla |
|-----------|-------|
| Reutilizar stream | Nunca: un stream se consume una sola vez |
| Operación terminal | Siempre debe haber una, si no el pipeline no se ejecuta |
| Efectos secundarios | Evitar en operaciones intermedias; usar solo en `forEach` |
| Colecciones grandes | Considerar `parallelStream()` para CPU-intensivo |
| Null | Usar `Optional` en lugar de retornar null desde métodos |
| `Optional.get()` | Nunca sin verificar; usar `orElse` o `orElseThrow` |
| `peek` | Solo para depuración, nunca para lógica de negocio |
| Modificar fuente | Nunca modificar la colección fuente durante el stream |
| Checked exceptions | Envolver en unchecked dentro de la lambda |
| Legibilidad | Preferir referencias a métodos sobre lambdas cuando sea más claro |

---

## Próximo tema

**Tema 16: Entrada/Salida (I/O)**

Aprenderás a trabajar con archivos y directorios usando la API clásica (`File`, `FileReader`, `BufferedReader`) y la moderna NIO.2 (`Path`, `Files`, `Paths`), leer y escribir archivos de texto y binarios, serialización de objetos, y cómo procesar archivos grandes de forma eficiente con streams de líneas.

---

*Curso de Java — Nivel 3: Java Avanzado*
*Tema 15 de 24*
*Creado con fines educativos*
