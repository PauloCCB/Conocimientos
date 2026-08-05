# Curso de Java — Nivel 3: Java Avanzado
## Tema 13: Colecciones (Collections Framework)

> **Nivel:** Intermedio-Avanzado | **Prerrequisitos:** Niveles 1 y 2 completos | **Duración estimada:** 5–6 horas

---

## Tabla de contenidos

1. [¿Qué es el Collections Framework?](#1-qué-es-el-collections-framework)
2. [La interfaz Collection y sus subinterfaces](#2-la-interfaz-collection-y-sus-subinterfaces)
3. [List — listas ordenadas](#3-list--listas-ordenadas)
4. [Set — conjuntos sin duplicados](#4-set--conjuntos-sin-duplicados)
5. [Map — pares clave-valor](#5-map--pares-clave-valor)
6. [Queue y Deque — colas](#6-queue-y-deque--colas)
7. [La clase Collections — utilidades](#7-la-clase-collections--utilidades)
8. [Iteradores](#8-iteradores)
9. [Colecciones inmutables (Java 9+)](#9-colecciones-inmutables-java-9)
10. [Colecciones sincronizadas y thread-safe](#10-colecciones-sincronizadas-y-thread-safe)
11. [Comparación de rendimiento](#11-comparación-de-rendimiento)
12. [Errores comunes con colecciones](#12-errores-comunes-con-colecciones)
13. [Ejercicios prácticos](#13-ejercicios-prácticos)
14. [Resumen del tema](#14-resumen-del-tema)

---

## 1. ¿Qué es el Collections Framework?

El **Collections Framework** es el conjunto de interfaces, clases y algoritmos que Java provee para almacenar, organizar y manipular grupos de objetos. Es una de las partes más usadas de la API estándar de Java.

### ¿Por qué no usar solo arrays?

Los arrays tienen limitaciones importantes:

```java
// ARRAYS: limitaciones
int[] arr = new int[5];  // Tamaño fijo desde el inicio
arr[5] = 10;             // Error: no puede crecer

// ¿Quieres eliminar un elemento del medio? Debes desplazar todo manualmente
// ¿Quieres verificar si contiene un valor? Debes recorrer todo manualmente
// ¿Quieres eliminar duplicados? Lógica manual compleja
```

```java
// COLECCIONES: dinámicas y potentes
List<Integer> lista = new ArrayList<>();
lista.add(1);        // Crece automáticamente
lista.add(2);
lista.remove(0);     // Elimina y reajusta
lista.contains(2);   // Búsqueda directa

Set<Integer> conjunto = new HashSet<>();
conjunto.add(1);
conjunto.add(1);     // El duplicado se ignora automáticamente
// conjunto.size() == 1
```

### La jerarquía del Collections Framework

```
                    Iterable<E>
                        │
                  Collection<E>
                 /      │       \
            List<E>   Set<E>   Queue<E>
           /     \    /    \       \
     ArrayList LinkedList HashSet TreeSet  Deque<E>
                SortedSet<E>           /        \
                    │           ArrayDeque   LinkedList
                TreeSet<E>

               Map<K,V>
              /    |    \
        HashMap  TreeMap  LinkedHashMap
                SortedMap<K,V>
                    │
                TreeMap<K,V>
```

### Las importaciones necesarias

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.HashSet;
import java.util.TreeSet;
import java.util.LinkedHashSet;
import java.util.HashMap;
import java.util.TreeMap;
import java.util.LinkedHashMap;
import java.util.ArrayDeque;
import java.util.PriorityQueue;
import java.util.Collections;
import java.util.List;
import java.util.Set;
import java.util.Map;
import java.util.Queue;
import java.util.Deque;
// O todo de una vez (para ejemplos):
import java.util.*;
```

---

## 2. La interfaz Collection y sus subinterfaces

### Métodos comunes de Collection\<E\>

Todos las implementaciones de `Collection` comparten estos métodos:

```java
// Supongamos que 'col' es cualquier Collection<String>
Collection<String> col = new ArrayList<>();

// Agregar
col.add("Java");              // Agrega un elemento
col.addAll(otraColeccion);    // Agrega todos los de otra colección

// Eliminar
col.remove("Java");           // Elimina la primera ocurrencia
col.removeAll(otraColeccion); // Elimina todos los que están en otraColeccion
col.retainAll(otraColeccion); // Conserva solo los que están en otraColeccion
col.clear();                  // Elimina todos los elementos

// Consultar
col.size();                   // Número de elementos
col.isEmpty();                // true si no hay elementos
col.contains("Java");         // true si existe ese elemento
col.containsAll(otraCol);     // true si contiene todos los de otraCol

// Convertir
col.toArray();                // Object[]
col.toArray(new String[0]);   // String[] (con tipo)

// Iterar
for (String s : col) { }     // for-each
col.forEach(s -> System.out.println(s));  // con lambda
col.iterator();               // Iterator explícito
```

---

## 3. List — listas ordenadas

Una `List` es una colección **ordenada** (mantiene el orden de inserción) que **permite duplicados** y acceso por **índice**.

### 3.1 ArrayList — la lista más usada

`ArrayList` almacena los elementos en un array dinámico interno. Es la lista más eficiente para acceso aleatorio.

```java
List<String> lista = new ArrayList<>();

// Agregar elementos
lista.add("Java");           // ["Java"]
lista.add("Python");         // ["Java", "Python"]
lista.add("C++");            // ["Java", "Python", "C++"]
lista.add(1, "Kotlin");      // ["Java", "Kotlin", "Python", "C++"] — en posición 1
lista.add("Java");           // ["Java", "Kotlin", "Python", "C++", "Java"] — duplicado OK

// Acceder por índice
String primero = lista.get(0);       // "Java"
String ultimo  = lista.get(lista.size() - 1); // "Java"

// Modificar
lista.set(2, "Rust");                // ["Java", "Kotlin", "Rust", "C++", "Java"]

// Eliminar
lista.remove(0);                     // Elimina por índice → ["Kotlin", "Rust", "C++", "Java"]
lista.remove("Java");                // Elimina primera ocurrencia del objeto → ["Kotlin", "Rust", "C++"]

// Buscar
int indice = lista.indexOf("Rust");  // 1 (o -1 si no existe)
boolean existe = lista.contains("Kotlin"); // true

// Sublista
List<String> sub = lista.subList(0, 2); // ["Kotlin", "Rust"] (0 inclusivo, 2 exclusivo)

// Ordenar
Collections.sort(lista);             // Orden natural (alfabético para Strings)
lista.sort(Comparator.reverseOrder()); // Orden inverso

// Tamaño
int tamaño = lista.size();   // 3
boolean vacia = lista.isEmpty(); // false

// Iterar
for (String s : lista) System.out.println(s);
lista.forEach(System.out::println);

// Convertir a array
String[] arr = lista.toArray(new String[0]);
```

### ArrayList con capacidad inicial

```java
// Si sabes aproximadamente cuántos elementos tendrás,
// puedes pre-dimensionar para mejorar rendimiento
List<Producto> catalogo = new ArrayList<>(500);
// Evita redimensionamientos internos frecuentes
```

### 3.2 LinkedList — lista doblemente enlazada

`LinkedList` almacena cada elemento en un nodo que contiene referencias al anterior y al siguiente. Es eficiente para inserciones y eliminaciones al principio y al final, pero lenta para acceso por índice.

```java
LinkedList<Integer> ll = new LinkedList<>();

// Operaciones de lista estándar
ll.add(1);          // [1]
ll.add(2);          // [1, 2]
ll.add(3);          // [1, 2, 3]

// Operaciones específicas de LinkedList (extremos)
ll.addFirst(0);     // [0, 1, 2, 3]
ll.addLast(4);      // [0, 1, 2, 3, 4]
ll.getFirst();      // 0
ll.getLast();       // 4
ll.removeFirst();   // Elimina y retorna 0 → [1, 2, 3, 4]
ll.removeLast();    // Elimina y retorna 4 → [1, 2, 3]

// Peeks (sin eliminar)
ll.peekFirst();     // 1 (null si vacía, no lanza excepción)
ll.peekLast();      // 3
```

### 3.3 Cuándo usar ArrayList vs. LinkedList

```
OPERACIÓN              ARRAYLIST       LINKEDLIST
───────────────────────────────────────────────────
get(índice)            O(1) ← Rápido  O(n) ← Lento
add(al final)          O(1) amortizado O(1)
add(al inicio/medio)   O(n)            O(1) (si tienes referencia)
remove(índice)         O(n)            O(n) (para encontrar)
remove(inicio/fin)     O(n)            O(1)
Memoria                Menos           Más (punteros extra)

REGLA PRÁCTICA:
  ✓ Usa ArrayList para casi todo (acceso aleatorio, iteración)
  ✓ Usa LinkedList como cola o deque (add/remove en extremos frecuente)
```

### 3.4 Operaciones avanzadas con List

```java
List<Integer> numeros = new ArrayList<>(List.of(5, 3, 8, 1, 9, 2, 7, 4, 6));

// Ordenar con Comparator
numeros.sort(Integer::compare);                // [1, 2, 3, 4, 5, 6, 7, 8, 9]
numeros.sort(Comparator.reverseOrder());       // [9, 8, 7, 6, 5, 4, 3, 2, 1]

// Buscar en lista ordenada (binary search)
Collections.sort(numeros);
int pos = Collections.binarySearch(numeros, 5); // Índice de 5 en la lista ordenada

// Reemplazar todos los elementos
numeros.replaceAll(n -> n * 2);  // [2, 4, 6, 8, 10, 12, 14, 16, 18]

// Eliminar con condición
numeros.removeIf(n -> n % 4 == 0);  // Elimina múltiplos de 4

// Mezclar aleatoriamente
Collections.shuffle(numeros);

// Frecuencia de un elemento
List<String> palabras = List.of("java", "java", "python", "java", "c++");
int freq = Collections.frequency(palabras, "java");  // 3
```

---

## 4. Set — conjuntos sin duplicados

Un `Set` es una colección que **no permite duplicados**. Basado en el concepto matemático de conjunto.

### 4.1 HashSet — el conjunto más rápido

`HashSet` usa una tabla hash internamente. Ofrece operaciones O(1) en promedio, pero **no garantiza ningún orden**.

```java
Set<String> lenguajes = new HashSet<>();

lenguajes.add("Java");
lenguajes.add("Python");
lenguajes.add("Kotlin");
lenguajes.add("Java");     // Duplicado: ignorado silenciosamente
lenguajes.add("Python");   // Duplicado: ignorado

System.out.println(lenguajes.size());       // 3
System.out.println(lenguajes.contains("Java")); // true

// El orden de iteración NO es predecible
for (String l : lenguajes) {
    System.out.println(l);  // Puede ser en cualquier orden
}

// Eliminar
lenguajes.remove("Python");

// Operaciones de conjuntos
Set<Integer> a = new HashSet<>(Set.of(1, 2, 3, 4, 5));
Set<Integer> b = new HashSet<>(Set.of(3, 4, 5, 6, 7));

// Unión: todos los de A + todos los de B
Set<Integer> union = new HashSet<>(a);
union.addAll(b);
System.out.println(union);  // [1, 2, 3, 4, 5, 6, 7]

// Intersección: solo los que están en A Y en B
Set<Integer> interseccion = new HashSet<>(a);
interseccion.retainAll(b);
System.out.println(interseccion);  // [3, 4, 5]

// Diferencia: los de A que NO están en B
Set<Integer> diferencia = new HashSet<>(a);
diferencia.removeAll(b);
System.out.println(diferencia);  // [1, 2]
```

### HashSet y equals/hashCode

Para que `HashSet` funcione correctamente con objetos propios, **debes** sobreescribir `equals()` y `hashCode()`:

```java
public class Punto {
    private int x, y;

    public Punto(int x, int y) { this.x = x; this.y = y; }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Punto p)) return false;
        return x == p.x && y == p.y;
    }

    @Override
    public int hashCode() {
        return java.util.Objects.hash(x, y);
    }
}

Set<Punto> puntos = new HashSet<>();
puntos.add(new Punto(1, 2));
puntos.add(new Punto(3, 4));
puntos.add(new Punto(1, 2));  // Duplicado: mismo x e y

System.out.println(puntos.size());  // 2 (correcto, gracias a equals/hashCode)

// SIN sobreescribir equals/hashCode:
// puntos.size() sería 3 (Java los trata como objetos distintos)
```

### 4.2 TreeSet — conjunto ordenado

`TreeSet` almacena los elementos en un árbol rojo-negro, manteniéndolos siempre **ordenados** según el orden natural o un `Comparator`.

```java
// Orden natural (alfabético para Strings)
TreeSet<String> ordenado = new TreeSet<>();
ordenado.add("Banana");
ordenado.add("Manzana");
ordenado.add("Cereza");
ordenado.add("Banana");   // Duplicado ignorado

System.out.println(ordenado);  // [Banana, Cereza, Manzana]

// Métodos específicos de TreeSet (NavigableSet)
System.out.println(ordenado.first());           // Banana
System.out.println(ordenado.last());            // Manzana
System.out.println(ordenado.floor("C"));        // Banana (el mayor ≤ "C")
System.out.println(ordenado.ceiling("C"));      // Cereza (el menor ≥ "C")
System.out.println(ordenado.lower("Cereza"));   // Banana (el mayor ESTRICTAMENTE menor)
System.out.println(ordenado.higher("Cereza"));  // Manzana (el menor ESTRICTAMENTE mayor)

// Subconjuntos
System.out.println(ordenado.headSet("Cereza"));        // [Banana] (menores que Cereza)
System.out.println(ordenado.tailSet("Cereza"));        // [Cereza, Manzana] (mayores o iguales)
System.out.println(ordenado.subSet("B", "M"));         // [Banana, Cereza]

// Con Comparator personalizado (por longitud de String)
TreeSet<String> porLongitud = new TreeSet<>(
    Comparator.comparingInt(String::length).thenComparing(Comparator.naturalOrder())
);
porLongitud.addAll(List.of("Java", "C", "Python", "Go", "Kotlin", "Rust"));
System.out.println(porLongitud);  // [C, Go, Java, Rust, Kotlin, Python]
```

### 4.3 LinkedHashSet — orden de inserción

`LinkedHashSet` mantiene el **orden de inserción** mientras elimina duplicados. Combina lo mejor de `HashSet` (velocidad) con orden predecible.

```java
Set<String> insertados = new LinkedHashSet<>();
insertados.add("Banana");
insertados.add("Manzana");
insertados.add("Cereza");
insertados.add("Banana");   // Ignorado

System.out.println(insertados);  // [Banana, Manzana, Cereza] — orden de inserción

// Caso de uso: eliminar duplicados manteniendo el orden de un List
List<String> conDuplicados = List.of("a", "b", "a", "c", "b", "d");
List<String> sinDuplicados = new ArrayList<>(new LinkedHashSet<>(conDuplicados));
System.out.println(sinDuplicados);  // [a, b, c, d] — sin duplicados, orden conservado
```

### 4.4 Comparativa de implementaciones de Set

```
IMPLEMENTACIÓN    ORDEN          VELOCIDAD    DUPLICADOS  NULOS
──────────────────────────────────────────────────────────────
HashSet           Ninguno        O(1)         No          1 null
LinkedHashSet     Inserción      O(1)         No          1 null
TreeSet           Natural/Custom O(log n)     No          No (lanza NPE)
```

---

## 5. Map — pares clave-valor

Un `Map` almacena pares **clave → valor**. Cada clave es única; los valores pueden repetirse. No extiende `Collection`.

### 5.1 HashMap — el mapa más usado

```java
Map<String, Integer> edades = new HashMap<>();

// Agregar pares
edades.put("Ana",    28);
edades.put("Luis",   35);
edades.put("María",  42);
edades.put("Carlos", 28);   // Valor repetido: OK
edades.put("Ana",    29);   // Clave repetida: sobreescribe el valor anterior

// Obtener valor por clave
Integer edadAna = edades.get("Ana");         // 29
Integer noExiste = edades.get("Pedro");      // null
Integer conDefault = edades.getOrDefault("Pedro", 0);  // 0 (valor por defecto)

// Verificar existencia
edades.containsKey("Luis");    // true
edades.containsValue(42);     // true

// Eliminar
edades.remove("Carlos");                     // Elimina la entrada de Carlos
edades.remove("Luis", 35);                   // Elimina solo si el valor también coincide

// Tamaño
edades.size();    // Número de pares clave-valor

// Iterar sobre el mapa
for (Map.Entry<String, Integer> entrada : edades.entrySet()) {
    System.out.println(entrada.getKey() + " → " + entrada.getValue());
}

// Solo claves
for (String clave : edades.keySet()) {
    System.out.println(clave);
}

// Solo valores
for (Integer valor : edades.values()) {
    System.out.println(valor);
}

// Con forEach y lambda
edades.forEach((nombre, edad) ->
    System.out.printf("%s tiene %d años%n", nombre, edad));
```

### Operaciones avanzadas de Map (Java 8+)

```java
Map<String, Integer> inventario = new HashMap<>();
inventario.put("Laptop", 10);
inventario.put("Mouse", 50);
inventario.put("Teclado", 25);

// putIfAbsent: solo agrega si la clave no existe
inventario.putIfAbsent("Monitor", 15);   // Agrega Monitor: 15
inventario.putIfAbsent("Laptop", 999);   // NO sobreescribe: Laptop sigue en 10

// computeIfAbsent: calcula el valor si la clave no existe
inventario.computeIfAbsent("Auriculares", k -> 20); // Agrega Auriculares: 20

// computeIfPresent: actualiza si la clave existe
inventario.computeIfPresent("Laptop", (k, v) -> v + 5);  // Laptop: 10 + 5 = 15

// compute: siempre calcula (independientemente de si existe)
inventario.compute("Mouse", (k, v) -> v == null ? 1 : v + 1); // Mouse: 51

// merge: combina el valor existente con el nuevo
inventario.merge("Teclado", 10, Integer::sum);  // Teclado: 25 + 10 = 35
inventario.merge("Webcam", 8, Integer::sum);    // Webcam no existe → lo agrega con 8

// replaceAll: reemplaza todos los valores
inventario.replaceAll((k, v) -> v * 2);  // Duplica todos los stocks
```

### 5.2 TreeMap — mapa ordenado por clave

```java
TreeMap<String, Double> preciosPorNombre = new TreeMap<>();
preciosPorNombre.put("Monitor",    299.99);
preciosPorNombre.put("Laptop",     999.99);
preciosPorNombre.put("Auriculares", 79.99);
preciosPorNombre.put("Teclado",    49.99);

// Siempre ordenado por clave (alfabéticamente aquí)
System.out.println(preciosPorNombre);
// {Auriculares=79.99, Laptop=999.99, Monitor=299.99, Teclado=49.99}

// Métodos específicos de TreeMap (NavigableMap)
System.out.println(preciosPorNombre.firstKey());         // Auriculares
System.out.println(preciosPorNombre.lastKey());          // Teclado
System.out.println(preciosPorNombre.floorKey("M"));      // Laptop (mayor ≤ "M")
System.out.println(preciosPorNombre.ceilingKey("M"));    // Monitor (menor ≥ "M")

// Submapas
Map<String, Double> sub = preciosPorNombre.subMap("K", "N");
System.out.println(sub);  // {Laptop=999.99, Monitor=299.99}

System.out.println(preciosPorNombre.headMap("M")); // {Auriculares=79.99, Laptop=999.99}
System.out.println(preciosPorNombre.tailMap("M")); // {Monitor=299.99, Teclado=49.99}
```

### 5.3 LinkedHashMap — orden de inserción o acceso

```java
// Orden de inserción (por defecto)
Map<String, Integer> insercion = new LinkedHashMap<>();
insercion.put("tercero", 3);
insercion.put("primero", 1);
insercion.put("segundo", 2);
System.out.println(insercion); // {tercero=3, primero=1, segundo=2}

// Orden de acceso (LRU Cache — Least Recently Used)
Map<String, String> cache = new LinkedHashMap<>(16, 0.75f, true) {
    @Override
    protected boolean removeEldestEntry(Map.Entry<String, String> eldest) {
        return size() > 3;  // Mantiene máximo 3 entradas (elimina la menos usada)
    }
};
cache.put("A", "Valor A");
cache.put("B", "Valor B");
cache.put("C", "Valor C");
cache.get("A");              // Accede a A (se mueve al final)
cache.put("D", "Valor D");   // B era el menos usado reciente → se elimina
System.out.println(cache.keySet()); // [C, A, D]
```

### 5.4 Comparativa de implementaciones de Map

```
IMPLEMENTACIÓN    ORDEN              VELOCIDAD    CLAVE NULL
──────────────────────────────────────────────────────────────
HashMap           Ninguno            O(1)         Sí (1 null)
LinkedHashMap     Inserción/Acceso   O(1)         Sí (1 null)
TreeMap           Natural/Custom     O(log n)     No (lanza NPE)
```

### 5.5 Patrones comunes con Map

```java
// FRECUENCIA DE ELEMENTOS
String texto = "el gato y el perro y el gato";
Map<String, Integer> frecuencia = new HashMap<>();
for (String palabra : texto.split(" ")) {
    frecuencia.merge(palabra, 1, Integer::sum);
}
System.out.println(frecuencia);
// {el=3, gato=2, y=2, perro=1}

// AGRUPAR ELEMENTOS
List<String> lenguajes = List.of("Java", "Python", "JavaScript", "Kotlin",
                                  "Go", "C++", "Julia", "Perl");
Map<Integer, List<String>> porLongitud = new HashMap<>();
for (String l : lenguajes) {
    int len = l.length();
    porLongitud.computeIfAbsent(len, k -> new ArrayList<>()).add(l);
}
porLongitud.forEach((len, lista) ->
    System.out.println("Longitud " + len + ": " + lista));

// CACHÉ SIMPLE
Map<Integer, Long> cacheFactorial = new HashMap<>();
public static long factorial(int n) {
    return cacheFactorial.computeIfAbsent(n,
        k -> k <= 1 ? 1L : k * factorial(k - 1));
}

// ÍNDICE INVERTIDO (valor → claves)
Map<String, Integer> original = Map.of("uno", 1, "dos", 2, "tres", 3);
Map<Integer, String> invertido = new HashMap<>();
original.forEach((k, v) -> invertido.put(v, k));
```

---

## 6. Queue y Deque — colas

### 6.1 Queue — cola FIFO

Una `Queue` sigue el orden **FIFO** (First In, First Out — el primero en entrar es el primero en salir).

```java
Queue<String> cola = new LinkedList<>();

// Agregar al final (enqueue)
cola.offer("Primero");   // [Primero]
cola.offer("Segundo");   // [Primero, Segundo]
cola.offer("Tercero");   // [Primero, Segundo, Tercero]

// Ver el frente sin eliminar
System.out.println(cola.peek());   // "Primero" (null si vacía)
System.out.println(cola.element()); // "Primero" (lanza excepción si vacía)

// Sacar del frente (dequeue)
System.out.println(cola.poll());    // "Primero" (null si vacía)
System.out.println(cola.remove());  // "Segundo" (lanza excepción si vacía)

System.out.println(cola);           // [Tercero]
```

#### Métodos de Queue: versiones que no lanzan excepción vs. las que sí

| Operación | Lanza excepción | Retorna null/false |
|-----------|-----------------|---------------------|
| Insertar  | `add(e)`        | `offer(e)` |
| Eliminar  | `remove()`      | `poll()` |
| Ver frente| `element()`     | `peek()` |

### 6.2 PriorityQueue — cola con prioridad

Los elementos se extraen según su **prioridad** (orden natural o `Comparator`), no según el orden de inserción.

```java
// Prioridad por valor natural (menor número = mayor prioridad)
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(30);
pq.offer(10);
pq.offer(50);
pq.offer(20);

System.out.println(pq.peek());   // 10 (el menor)
System.out.println(pq.poll());   // 10
System.out.println(pq.poll());   // 20
System.out.println(pq.poll());   // 30

// Con comparador personalizado (mayor número = mayor prioridad)
PriorityQueue<Integer> maxPQ = new PriorityQueue<>(Comparator.reverseOrder());
maxPQ.addAll(List.of(30, 10, 50, 20));
System.out.println(maxPQ.poll()); // 50

// Cola de tareas por prioridad
record Tarea(String nombre, int prioridad) implements Comparable<Tarea> {
    @Override
    public int compareTo(Tarea otra) {
        return Integer.compare(this.prioridad, otra.prioridad);  // menor número = mayor prioridad
    }
}

PriorityQueue<Tarea> tareas = new PriorityQueue<>();
tareas.offer(new Tarea("Enviar email", 3));
tareas.offer(new Tarea("Arreglar bug crítico", 1));
tareas.offer(new Tarea("Reunión", 2));
tareas.offer(new Tarea("Documentar", 5));

while (!tareas.isEmpty()) {
    Tarea t = tareas.poll();
    System.out.printf("[%d] %s%n", t.prioridad(), t.nombre());
}
// [1] Arreglar bug crítico
// [2] Reunión
// [3] Enviar email
// [5] Documentar
```

### 6.3 Deque — cola doble (Double-Ended Queue)

Un `Deque` (pronunciado "deck") permite agregar y quitar elementos de **ambos extremos**. Puede usarse tanto como pila (LIFO) como cola (FIFO).

```java
Deque<String> deque = new ArrayDeque<>();

// Agregar en ambos extremos
deque.offerFirst("Frente");     // [Frente]
deque.offerLast("Final");       // [Frente, Final]
deque.offerFirst("Muy frente"); // [Muy frente, Frente, Final]
deque.offerLast("Muy final");   // [Muy frente, Frente, Final, Muy final]

// Ver extremos sin eliminar
System.out.println(deque.peekFirst());  // Muy frente
System.out.println(deque.peekLast());   // Muy final

// Eliminar de ambos extremos
System.out.println(deque.pollFirst()); // Muy frente
System.out.println(deque.pollLast());  // Muy final

// Como PILA (LIFO — Last In, First Out)
Deque<String> pila = new ArrayDeque<>();
pila.push("Primero apilado");   // addFirst
pila.push("Segundo apilado");
pila.push("Tercero apilado");

System.out.println(pila.pop());   // "Tercero apilado" (removeFirst)
System.out.println(pila.peek());  // "Segundo apilado" (peekFirst)
```

#### ArrayDeque vs. LinkedList como Deque

```
ARRAYDEQUE: más rápido para la mayoría de las operaciones
  ✓ Implementado como array circular
  ✓ Sin overhead de nodos (menos memoria)
  ✓ Mejor localidad de caché

LINKEDLIST: solo cuando necesitas null elements o List interface también
```

---

## 7. La clase Collections — utilidades

`java.util.Collections` es una clase utilitaria con métodos estáticos para operar sobre colecciones.

```java
List<Integer> numeros = new ArrayList<>(List.of(3, 1, 4, 1, 5, 9, 2, 6, 5, 3));

// ORDENAR
Collections.sort(numeros);                           // [1, 1, 2, 3, 3, 4, 5, 5, 6, 9]
Collections.sort(numeros, Comparator.reverseOrder()); // [9, 6, 5, 5, 4, 3, 3, 2, 1, 1]

// MEZCLAR
Collections.shuffle(numeros);                        // Orden aleatorio
Collections.shuffle(numeros, new java.util.Random(42)); // Con semilla fija (reproducible)

// REVERTIR
Collections.reverse(numeros);                        // Invierte el orden actual

// BUSCAR (lista debe estar ordenada)
Collections.sort(numeros);
int indice = Collections.binarySearch(numeros, 5);   // Posición de 5

// EXTREMOS
System.out.println(Collections.max(numeros));        // 9
System.out.println(Collections.min(numeros));        // 1
System.out.println(Collections.max(numeros, Comparator.reverseOrder())); // 1 (mínimo natural)

// FRECUENCIA
int frecuencia = Collections.frequency(numeros, 5); // 2 (aparece dos veces)

// RELLENAR
Collections.fill(numeros, 0);                       // [0, 0, 0, 0, ...]

// COPIAR
List<Integer> destino = new ArrayList<>(Collections.nCopies(numeros.size(), 0));
Collections.copy(destino, numeros);                  // Copia numeros en destino

// ROTAR
List<String> letras = new ArrayList<>(List.of("A","B","C","D","E"));
Collections.rotate(letras, 2);   // ["D", "E", "A", "B", "C"] (rota 2 posiciones)

// INTERCAMBIAR
Collections.swap(letras, 0, 4);  // Intercambia posición 0 y 4

// NÚMERO DE COPIAS
List<String> tresJavas = Collections.nCopies(3, "Java");  // ["Java","Java","Java"]

// DISJOINT (sin elementos comunes)
boolean disjoint = Collections.disjoint(
    Set.of(1, 2, 3), Set.of(4, 5, 6));  // true
```

### Colecciones envueltas (Wrappers)

```java
// Lista no modificable (wrapper sobre lista existente)
List<String> original = new ArrayList<>(List.of("A", "B", "C"));
List<String> noMod = Collections.unmodifiableList(original);
// noMod.add("D");  // UnsupportedOperationException

// Set y Map no modificables
Set<String> noModSet = Collections.unmodifiableSet(new HashSet<>(Set.of("X", "Y")));
Map<String, Integer> noModMap = Collections.unmodifiableMap(new HashMap<>());

// Singleton (colección con un solo elemento)
Set<String> uno = Collections.singleton("Solo");
List<Integer> unoLista = Collections.singletonList(42);
Map<String, Integer> unoPar = Collections.singletonMap("clave", 1);

// Lista vacía (optimizada, comparte instancia)
List<String> vacia = Collections.emptyList();
Set<Integer> vacioSet = Collections.emptySet();
Map<String, Integer> vacioMap = Collections.emptyMap();
```

---

## 8. Iteradores

Un **Iterator** es un objeto que permite recorrer una colección de forma controlada, con la capacidad de eliminar elementos mientras se itera (algo que el for-each no permite directamente).

```java
List<String> lista = new ArrayList<>(List.of("Java", "Python", "C++", "Kotlin", "Go"));

// Iterator básico
Iterator<String> it = lista.iterator();
while (it.hasNext()) {
    String elemento = it.next();
    System.out.println(elemento);
    if (elemento.equals("C++")) {
        it.remove();  // Elimina C++ de forma segura durante la iteración
    }
}
System.out.println(lista);  // [Java, Python, Kotlin, Go]

// ListIterator — bidireccional y con más capacidades
ListIterator<String> lit = lista.listIterator(lista.size()); // Empieza al final
while (lit.hasPrevious()) {
    String elem = lit.previous();
    System.out.print(elem + " ");  // Go Kotlin Python Java
}

// Agregar o reemplazar durante la iteración
ListIterator<Integer> numIt = new ArrayList<>(List.of(1,2,3,4,5)).listIterator();
while (numIt.hasNext()) {
    int n = numIt.next();
    numIt.set(n * 2);   // Reemplaza el elemento actual
}

// El for-each NO permite eliminar durante la iteración
// (lanza ConcurrentModificationException si lo intentas)
for (String s : lista) {
    // lista.remove(s);  // ConcurrentModificationException
}

// Solución alternativa: removeIf (Java 8+)
lista.removeIf(s -> s.length() <= 2);  // Elimina los de longitud ≤ 2
```

---

## 9. Colecciones inmutables (Java 9+)

Java 9 introdujo métodos de fábrica para crear colecciones inmutables de forma concisa.

```java
// ANTES de Java 9: verbose
List<String> lista = Collections.unmodifiableList(
    new ArrayList<>(Arrays.asList("A", "B", "C")));

// DESDE Java 9: limpio y directo
List<String> lista = List.of("Java", "Kotlin", "Scala");
Set<Integer> conjunto = Set.of(1, 2, 3, 4, 5);
Map<String, Integer> mapa = Map.of("uno", 1, "dos", 2, "tres", 3);

// Para mapas con muchos pares (más de 10), usar Map.ofEntries
Map<String, Integer> grande = Map.ofEntries(
    Map.entry("a", 1),
    Map.entry("b", 2),
    Map.entry("c", 3),
    Map.entry("d", 4),
    Map.entry("e", 5),
    Map.entry("f", 6)
    // ... hasta los que necesites
);

// Características de las colecciones inmutables de Java 9:
// ✓ No se pueden modificar (add, remove, set lanzan UnsupportedOperationException)
// ✓ No aceptan null (lanza NullPointerException)
// ✓ Set y Map no garantizan orden
// ✓ Son más eficientes en memoria que las mutables
// ✓ Son thread-safe

// lista.add("D");  // UnsupportedOperationException
// List.of(null);   // NullPointerException

// Copiar a una lista mutable si necesitas modificarla
List<String> mutable = new ArrayList<>(lista);
mutable.add("Groovy");  // OK
```

### Colecciones no modificables vs. inmutables

```java
List<String> original = new ArrayList<>(List.of("A", "B", "C"));
List<String> noModificable = Collections.unmodifiableList(original);

// La vista no es modificable, pero el original SÍ lo es
original.add("D");
System.out.println(noModificable);  // [A, B, C, D] — refleja el cambio

// List.of() es verdaderamente inmutable
List<String> inmutable = List.of("X", "Y", "Z");
// No hay forma de modificarla desde afuera
```

---

## 10. Colecciones sincronizadas y thread-safe

Las colecciones estándar (`ArrayList`, `HashMap`, etc.) **no son thread-safe**. Si múltiples hilos las modifican simultáneamente, pueden corromperse.

### Wrappers sincronizados

```java
// Envolver en versión sincronizada
List<String> listaSync = Collections.synchronizedList(new ArrayList<>());
Map<String, Integer> mapaSync = Collections.synchronizedMap(new HashMap<>());
Set<String> setSync = Collections.synchronizedSet(new HashSet<>());

// Al iterar sobre una colección sincronizada, debes sincronizar manualmente
synchronized (listaSync) {
    for (String s : listaSync) {
        System.out.println(s);
    }
}
```

### Colecciones del paquete java.util.concurrent

Para aplicaciones multi-hilo, usa las versiones del paquete `concurrent` (más eficientes):

```java
import java.util.concurrent.*;

// CopyOnWriteArrayList: ideal para muchas lecturas, pocas escrituras
CopyOnWriteArrayList<String> cowList = new CopyOnWriteArrayList<>();

// ConcurrentHashMap: HashMap thread-safe con alto rendimiento
ConcurrentHashMap<String, Integer> concMap = new ConcurrentHashMap<>();
concMap.put("A", 1);
concMap.computeIfAbsent("B", k -> 2);

// BlockingQueue: cola thread-safe para productor-consumidor
BlockingQueue<String> bq = new LinkedBlockingQueue<>(100);
bq.put("elemento");       // Espera si está llena
String elem = bq.take();  // Espera si está vacía
```

---

## 11. Comparación de rendimiento

Entender la complejidad temporal de las operaciones te ayuda a elegir la colección correcta.

### Big-O de las operaciones principales

```
ESTRUCTURA          GET      ADD      REMOVE   CONTAINS  NOTAS
───────────────────────────────────────────────────────────────
ArrayList           O(1)     O(1)*    O(n)     O(n)      *amortizado al final
LinkedList          O(n)     O(1)**   O(1)**   O(n)      **en los extremos
HashSet             -        O(1)     O(1)     O(1)      -
LinkedHashSet       -        O(1)     O(1)     O(1)      -
TreeSet             -        O(log n) O(log n) O(log n)  siempre ordenado
HashMap             O(1)     O(1)     O(1)     O(1)***   ***por clave
LinkedHashMap       O(1)     O(1)     O(1)     O(1)      orden inserción
TreeMap             O(log n) O(log n) O(log n) O(log n)  ordenado por clave
ArrayDeque          O(1)     O(1)     O(1)     O(n)      mejor que Stack
PriorityQueue       -        O(log n) O(log n) O(n)      -
```

### Guía de selección rápida

```
¿Necesito acceso por índice?          → ArrayList
¿Inserto/elimino mucho en extremos?   → ArrayDeque / LinkedList
¿No quiero duplicados?
  ¿El orden importa?
    Inserción → LinkedHashSet
    Natural   → TreeSet
    No importa→ HashSet (el más rápido)
¿Necesito asociar clave a valor?
  ¿Ordenado por clave? → TreeMap
  ¿Orden inserción?    → LinkedHashMap
  ¿Solo velocidad?     → HashMap
¿FIFO?           → ArrayDeque (como Queue)
¿LIFO (pila)?    → ArrayDeque (como Stack)
¿Con prioridad?  → PriorityQueue
¿Multi-hilo?     → ConcurrentHashMap / CopyOnWriteArrayList
```

---

## 12. Errores comunes con colecciones

### Error 1: ConcurrentModificationException al modificar durante for-each

```java
List<String> lista = new ArrayList<>(List.of("A", "B", "C", "D"));

// ✗ MAL: ConcurrentModificationException
for (String s : lista) {
    if (s.equals("B")) lista.remove(s);
}

// ✓ BIEN opción 1: removeIf
lista.removeIf(s -> s.equals("B"));

// ✓ BIEN opción 2: Iterator
Iterator<String> it = lista.iterator();
while (it.hasNext()) {
    if (it.next().equals("B")) it.remove();
}

// ✓ BIEN opción 3: crear nueva lista
List<String> filtrada = lista.stream()
    .filter(s -> !s.equals("B"))
    .collect(java.util.stream.Collectors.toList());
```

### Error 2: Eliminar Integer por índice vs. por valor

```java
List<Integer> nums = new ArrayList<>(List.of(1, 2, 3, 4, 5));

// ✗ Confuso: remove(3) → elimina ÍNDICE 3 (el valor 4)
nums.remove(3);             // [1, 2, 3, 5]

// ✓ Para eliminar el valor 3, debes usar el objeto:
nums.remove(Integer.valueOf(3));  // [1, 2, 4, 5]
// O con cast:
nums.remove((Integer) 3);
```

### Error 3: HashSet/HashMap sin equals() y hashCode()

```java
// Sin sobreescribir equals/hashCode:
Set<Punto> set = new HashSet<>();
set.add(new Punto(1, 2));
set.add(new Punto(1, 2));  // Parece duplicado pero...
System.out.println(set.size()); // 2 (incorrecto: los trata como distintos)

// Con equals/hashCode correctamente sobreescritos:
System.out.println(set.size()); // 1 (correcto)
```

### Error 4: Modificar colección inmutable

```java
List<String> inmutable = List.of("A", "B", "C");
inmutable.add("D");    // UnsupportedOperationException

// Si necesitas modificar, crea una mutable a partir de ella:
List<String> mutable = new ArrayList<>(inmutable);
mutable.add("D");      // OK
```

### Error 5: Asumir orden en HashMap/HashSet

```java
Map<String, Integer> mapa = new HashMap<>();
mapa.put("c", 3);
mapa.put("a", 1);
mapa.put("b", 2);

// El orden de iteración NO es garantizado ni predecible
for (String clave : mapa.keySet()) {
    System.out.println(clave);  // Puede salir en cualquier orden
}

// Si necesitas orden: usa TreeMap o LinkedHashMap
Map<String, Integer> ordenado = new TreeMap<>(mapa); // {a=1, b=2, c=3}
```

### Error 6: NullPointerException con TreeSet/TreeMap

```java
TreeSet<String> ts = new TreeSet<>();
ts.add(null);  // NullPointerException: TreeSet no acepta null

TreeMap<String, Integer> tm = new TreeMap<>();
tm.put(null, 1);  // NullPointerException: TreeMap no acepta null como clave
```

---

## 13. Ejercicios prácticos

### Ejercicio 1 — Gestión de estudiantes (Nivel: Básico)

Crea `GestorEstudiantes.java` que use una `List<Estudiante>` donde `Estudiante` tiene nombre, carrera y promedio.

Implementa:
1. Agregar estudiantes (al menos 8 con datos distintos)
2. Mostrar todos ordenados por promedio descendente
3. Mostrar todos ordenados por nombre alfabéticamente
4. Filtrar y mostrar solo los de una carrera específica
5. Encontrar el de mayor promedio
6. Eliminar estudiantes con promedio menor a 6.0
7. Mostrar la lista final

---

### Ejercicio 2 — Contador de palabras (Nivel: Básico-Medio)

Crea `ContadorPalabras.java` que analice el siguiente texto:

```
"El elefante es el animal más grande que existe en tierra. El elefante africano
es más grande que el elefante asiático. Los elefantes viven en manadas lideradas
por la hembra más vieja. El elefante es conocido por su excelente memoria."
```

Usando un `HashMap<String, Integer>`:
1. Cuenta la frecuencia de cada palabra (ignorando mayúsculas y puntuación)
2. Muestra las 5 palabras más frecuentes
3. Muestra las palabras que aparecen exactamente una vez
4. Calcula la longitud promedio de las palabras únicas
5. Agrupa las palabras por su primera letra (usa `TreeMap<Character, List<String>>`)

---

### Ejercicio 3 — Sistema de inventario con Map (Nivel: Medio)

Crea `Inventario.java` usando `Map<String, Producto>` (clave: código del producto).

Implementa:
1. `agregar(Producto p)` — lanza excepción si ya existe
2. `actualizar(String codigo, int nuevaCantidad)` — lanza excepción si no existe
3. `retirar(String codigo, int cantidad)` — lanza excepción si no hay stock
4. `buscarPorCategoria(String categoria)` — retorna `List<Producto>` ordenada por precio
5. `productosConBajoStock(int umbral)` — retorna `TreeMap<Integer, List<String>>` donde la clave es el stock
6. `valorTotalInventario()` — suma precio × cantidad de todos
7. `reporteCompleto()` — imprime tabla formateada con todos los datos
8. `exportarCSV()` — retorna `String` con todos los productos en formato CSV

---

### Ejercicio 4 — Sistema de colas de atención (Nivel: Medio)

Simula el sistema de colas de un banco con `Queue` y `PriorityQueue`:

`Cliente` tiene: nombre, número de ticket, tipo ("Normal"/"VIP"/"Discapacitado"), hora de llegada.

Implementa `SistemaAtencion`:
- `Queue<Cliente>` para clientes normales
- `PriorityQueue<Cliente>` para prioritarios (VIP y Discapacitados tienen prioridad sobre normales; Discapacitados sobre VIP)
- `atenderSiguiente()` — siempre atiende primero de la cola prioritaria, luego la normal
- `agregarCliente(Cliente c)` — determina en qué cola va según su tipo
- `estadisticas()` — tiempo promedio de espera, clientes atendidos, etc.

Simula la llegada de 15 clientes (mezcla de tipos) y la atención de todos.

---

### Ejercicio 5 — Grafo con Map (Nivel: Avanzado)

Implementa un grafo no dirigido usando `Map<String, Set<String>>` (lista de adyacencia):

```java
public class Grafo {
    private Map<String, Set<String>> adyacencia;

    void agregarNodo(String nodo)
    void agregarArista(String nodo1, String nodo2)
    void eliminarArista(String nodo1, String nodo2)
    boolean sonAdyacentes(String nodo1, String nodo2)
    Set<String> vecinos(String nodo)
    List<String> bfs(String inicio)   // Búsqueda en amplitud
    List<String> dfs(String inicio)   // Búsqueda en profundidad
    boolean hayCamino(String desde, String hasta)
    int gradoNodo(String nodo)        // Número de vecinos
    void imprimirGrafo()
}
```

Prueba con el grafo de ciudades: Madrid-Barcelona, Madrid-Valencia, Barcelona-Zaragoza, Valencia-Alicante, Zaragoza-Pamplona.

---

### Ejercicio 6 — Caché LRU (Nivel: Avanzado)

Implementa una caché LRU (Least Recently Used) genérica usando `LinkedHashMap`:

```java
public class CacheLRU<K, V> {
    private final int capacidad;
    private final LinkedHashMap<K, V> cache;

    CacheLRU(int capacidad)
    V obtener(K clave)         // null si no existe; marca como recientemente usado
    void guardar(K clave, V valor) // Evicta el menos usado si está llena
    boolean contiene(K clave)
    int tamaño()
    void limpiar()
    Map<K, V> getEstadoActual()  // Copia del estado actual (orden LRU)
    void imprimirEstado()         // Muestra el estado con indicadores de uso
}
```

Demuestra:
- Llena la caché hasta la capacidad máxima
- Accede a elementos existentes (cambia su prioridad LRU)
- Agrega nuevos elementos (evicta los menos usados)
- Muestra el estado después de cada operación importante

---

## 14. Resumen del tema

### Las colecciones más importantes

```java
// LIST — ordenada, con duplicados, acceso por índice
List<T> lista = new ArrayList<>();          // O(1) aleatorio
List<T> lista = new LinkedList<>();         // O(1) extremos

// SET — sin duplicados
Set<T> set = new HashSet<>();               // O(1), sin orden
Set<T> set = new LinkedHashSet<>();         // O(1), orden inserción
Set<T> set = new TreeSet<>();               // O(log n), ordenado

// MAP — pares clave-valor, claves únicas
Map<K,V> map = new HashMap<>();             // O(1), sin orden
Map<K,V> map = new LinkedHashMap<>();       // O(1), orden inserción
Map<K,V> map = new TreeMap<>();             // O(log n), ordenado por clave

// QUEUE / DEQUE — colas
Queue<T> q  = new ArrayDeque<>();           // FIFO eficiente
Queue<T> pq = new PriorityQueue<>();        // Con prioridad
Deque<T> d  = new ArrayDeque<>();           // FIFO + LIFO (pila)

// INMUTABLES (Java 9+)
List<T> li  = List.of(a, b, c);
Set<T> si   = Set.of(a, b, c);
Map<K,V> mi = Map.of(k1,v1, k2,v2);

// UTILIDADES (Collections)
Collections.sort(lista);
Collections.shuffle(lista);
Collections.reverse(lista);
Collections.max(lista) / Collections.min(lista);
Collections.frequency(lista, elemento);
Collections.unmodifiableList(lista);
```

### Reglas de oro

| Situación | Regla |
|-----------|-------|
| Tipo genérico | Siempre declarar con la interfaz (`List<T>`, no `ArrayList<T>`) |
| Duplicados | Solo `List` los permite; `Set` los elimina automáticamente |
| Orden | `Tree*` ordena naturalmente; `Linked*` conserva inserción; `Hash*` no garantiza |
| equals/hashCode | Obligatorio sobreescribir para objetos propios en `HashSet`/`HashMap` |
| Modificar iterando | Usar `removeIf()` o `Iterator.remove()`, nunca modificar en for-each directo |
| Null en Tree* | `TreeSet` y `TreeMap` (claves) no aceptan `null` |
| Multi-hilo | Usar `ConcurrentHashMap` o `CopyOnWriteArrayList` en lugar de wrappers sync |
| Rendimiento | Especificar capacidad inicial si conoces el tamaño aproximado |
| Colecciones inmutables | Preferir `List.of()` sobre `Collections.unmodifiableList()` cuando sea posible |

---

## Próximo tema

**Tema 14: Genéricos**

Aprenderás a crear clases y métodos genéricos que funcionen con cualquier tipo, los wildcards (`?`, `? extends T`, `? super T`), el concepto de type erasure (borrado de tipos), y cómo los genéricos hacen el código más seguro y reutilizable. También veremos patrones comunes con genéricos en la API estándar de Java.

---

*Curso de Java — Nivel 3: Java Avanzado*
*Tema 13 de 24*
*Creado con fines educativos*
