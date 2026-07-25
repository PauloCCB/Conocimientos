# Curso de Java — Nivel 1: Fundamentos
## Tema 5: Arrays y Strings

> **Nivel:** Principiante | **Prerrequisitos:** Temas 1 al 4 completados | **Duración estimada:** 4–5 horas

---

## Tabla de contenidos

1. [¿Qué es un Array?](#1-qué-es-un-array)
2. [Arrays unidimensionales](#2-arrays-unidimensionales)
3. [Recorrer arrays](#3-recorrer-arrays)
4. [La clase Arrays — utilidades esenciales](#4-la-clase-arrays--utilidades-esenciales)
5. [Arrays multidimensionales (matrices)](#5-arrays-multidimensionales-matrices)
6. [Arrays irregulares (Jagged Arrays)](#6-arrays-irregulares-jagged-arrays)
7. [Arrays como parámetros y retorno de métodos](#7-arrays-como-parámetros-y-retorno-de-métodos)
8. [Strings — repaso y profundización](#8-strings--repaso-y-profundización)
9. [StringBuilder y StringBuffer](#9-stringbuilder-y-stringbuffer)
10. [Expresiones regulares con Strings](#10-expresiones-regulares-con-strings)
11. [Errores comunes con Arrays y Strings](#11-errores-comunes-con-arrays-y-strings)
12. [Ejercicios prácticos](#12-ejercicios-prácticos)
13. [Resumen del tema](#13-resumen-del-tema)

---

## 1. ¿Qué es un Array?

Un **array** (arreglo) es una estructura de datos que permite almacenar múltiples valores del **mismo tipo** bajo un único nombre, organizados en posiciones numeradas llamadas **índices**.

Sin arrays, para guardar 100 calificaciones necesitarías 100 variables distintas:

```java
// Sin array: inmanejable
int calificacion1 = 85;
int calificacion2 = 90;
int calificacion3 = 78;
// ... hasta calificacion100
```

Con un array, todo se organiza en una sola estructura:

```java
// Con array: elegante y escalable
int[] calificaciones = new int[100];
```

### Representación visual de un array

Un array es como una fila de cajas numeradas donde cada caja guarda un valor:

```
  Nombre del array: numeros
  ┌─────┬─────┬─────┬─────┬─────┐
  │ 10  │ 25  │  7  │ 42  │ 13  │
  └─────┴─────┴─────┴─────┴─────┘
     0     1     2     3     4      ← índices (empiezan en 0)
```

### Características fundamentales de los arrays en Java

1. **Tamaño fijo:** una vez creado, el tamaño del array no puede cambiar.
2. **Mismo tipo:** todos los elementos deben ser del mismo tipo de dato.
3. **Índices base 0:** el primer elemento está en la posición 0, el último en `longitud - 1`.
4. **Tipo de referencia:** los arrays son objetos en Java; la variable guarda una referencia al array en el heap.
5. **Tamaño accesible:** puedes obtener el tamaño con la propiedad `.length`.

---

## 2. Arrays unidimensionales

### 2.1 Declaración

Hay dos formas de declarar un array. La primera es la recomendada:

```java
// Forma recomendada: los corchetes van junto al tipo
int[] numeros;
String[] nombres;
double[] precios;
boolean[] flags;
char[] letras;
```

```java
// Forma alternativa (válida pero menos usada en Java moderno)
int numeros[];
String nombres[];
```

En este punto la variable existe pero no apunta a ningún array (`null`).

### 2.2 Creación con new

Para crear el array en memoria y asignarle tamaño:

```java
int[] numeros = new int[5];       // Array de 5 enteros, todos inicializados en 0
String[] nombres = new String[3]; // Array de 3 Strings, todos inicializados en null
double[] precios = new double[4]; // Array de 4 doubles, todos en 0.0
boolean[] flags = new boolean[2]; // Array de 2 booleans, todos en false
```

**Valores por defecto al crear con `new`:**

| Tipo | Valor por defecto |
|------|-------------------|
| `byte`, `short`, `int`, `long` | `0` |
| `float`, `double` | `0.0` |
| `char` | `'\u0000'` (carácter nulo) |
| `boolean` | `false` |
| Objetos (`String`, etc.) | `null` |

### 2.3 Inicialización con valores

Puedes declarar e inicializar un array con valores en la misma línea usando la **sintaxis de inicialización directa**:

```java
// El tamaño se deduce automáticamente de los valores proporcionados
int[] numeros = {10, 25, 7, 42, 13};           // 5 elementos
String[] dias = {"Lunes", "Martes", "Miércoles", "Jueves", "Viernes"};
double[] precios = {9.99, 24.50, 3.75, 15.00};
boolean[] estados = {true, false, true, true};

// También válido con new (útil cuando el array ya fue declarado antes)
int[] datos;
datos = new int[]{1, 2, 3, 4, 5};
```

### 2.4 Acceso a elementos

Se accede a cada elemento usando el nombre del array seguido del índice entre corchetes:

```java
int[] numeros = {10, 25, 7, 42, 13};

// Leer un elemento
System.out.println(numeros[0]);  // 10  (primer elemento)
System.out.println(numeros[2]);  // 7
System.out.println(numeros[4]);  // 13  (último elemento)

// Modificar un elemento
numeros[1] = 100;
System.out.println(numeros[1]);  // 100

// Obtener el tamaño
System.out.println(numeros.length);  // 5

// Último elemento siempre en índice length-1
System.out.println(numeros[numeros.length - 1]);  // 13
```

### 2.5 Operaciones comunes en arrays

**Encontrar el máximo y mínimo:**

```java
int[] datos = {34, 12, 89, 5, 67, 23, 45};

int maximo = datos[0];
int minimo = datos[0];

for (int valor : datos) {
    if (valor > maximo) maximo = valor;
    if (valor < minimo) minimo = valor;
}

System.out.println("Máximo: " + maximo);  // 89
System.out.println("Mínimo: " + minimo);  // 5
```

**Calcular promedio:**

```java
int[] calificaciones = {85, 92, 78, 95, 88, 72, 90};
int suma = 0;

for (int cal : calificaciones) {
    suma += cal;
}

double promedio = (double) suma / calificaciones.length;
System.out.printf("Promedio: %.2f%n", promedio);  // 85.71
```

**Invertir un array:**

```java
int[] original = {1, 2, 3, 4, 5};
int n = original.length;

for (int i = 0; i < n / 2; i++) {
    // Intercambiar original[i] con original[n-1-i]
    int temp = original[i];
    original[i] = original[n - 1 - i];
    original[n - 1 - i] = temp;
}

// original ahora es {5, 4, 3, 2, 1}
for (int num : original) {
    System.out.print(num + " ");
}
```

**Contar ocurrencias:**

```java
int[] votos = {1, 2, 1, 3, 1, 2, 1, 3, 2, 1};
int contar = 1;  // Contar cuántas veces aparece el 1
int conteo = 0;

for (int voto : votos) {
    if (voto == contar) conteo++;
}

System.out.println("El " + contar + " aparece " + conteo + " veces");  // 5 veces
```

**Buscar un elemento (búsqueda lineal):**

```java
int[] array = {4, 9, 2, 7, 11, 5, 8};
int buscar = 11;
int posicion = -1;  // -1 indica "no encontrado"

for (int i = 0; i < array.length; i++) {
    if (array[i] == buscar) {
        posicion = i;
        break;
    }
}

if (posicion != -1) {
    System.out.println(buscar + " está en la posición " + posicion);
} else {
    System.out.println(buscar + " no está en el array");
}
```

**Copiar un array manualmente:**

```java
int[] original = {1, 2, 3, 4, 5};
int[] copia = new int[original.length];

for (int i = 0; i < original.length; i++) {
    copia[i] = original[i];
}

// IMPORTANTE: esto NO copia un array, solo copia la referencia
int[] referenciaErronea = original;  // Ambas variables apuntan al MISMO array
referenciaErronea[0] = 999;
System.out.println(original[0]);     // 999  ← ¡original también cambió!
```

### 2.6 Array de objetos

```java
String[] planetas = {"Mercurio", "Venus", "Tierra", "Marte"};

for (String planeta : planetas) {
    System.out.println(planeta + " tiene " + planeta.length() + " letras");
}

// Array de objetos con new
String[] colores = new String[3];
colores[0] = "Rojo";
colores[1] = "Verde";
colores[2] = "Azul";
```

---

## 3. Recorrer arrays

Hay múltiples formas de recorrer un array. Cada una tiene sus ventajas según el contexto.

### 3.1 Con for clásico (cuando necesitas el índice)

```java
int[] numeros = {10, 20, 30, 40, 50};

for (int i = 0; i < numeros.length; i++) {
    System.out.println("numeros[" + i + "] = " + numeros[i]);
}
```

Úsalo cuando necesitas: el índice actual, modificar elementos, recorrer en reversa, saltar elementos o comparar elementos entre sí.

```java
// Recorrer en reversa
for (int i = numeros.length - 1; i >= 0; i--) {
    System.out.print(numeros[i] + " ");  // 50 40 30 20 10
}
```

### 3.2 Con for-each (cuando solo necesitas los valores)

```java
int[] numeros = {10, 20, 30, 40, 50};

for (int numero : numeros) {
    System.out.println(numero);
}
```

Más legible y seguro cuando solo necesitas leer los valores, sin importar el índice.

### 3.3 Con while (para condiciones dinámicas)

```java
int[] numeros = {5, 8, 3, 9, 1, 7, 4};
int i = 0;

// Detener al encontrar un número mayor que 8
while (i < numeros.length && numeros[i] <= 8) {
    System.out.print(numeros[i] + " ");
    i++;
}
// Salida: 5 8 3  (se detiene antes del 9)
```

### 3.4 Con Arrays.stream() (Java 8+, introducción)

```java
import java.util.Arrays;

int[] numeros = {3, 1, 4, 1, 5, 9, 2, 6};

// Imprimir todos
Arrays.stream(numeros).forEach(n -> System.out.print(n + " "));

// Suma total
int suma = Arrays.stream(numeros).sum();
System.out.println("\nSuma: " + suma);  // 31

// Promedio
double promedio = Arrays.stream(numeros).average().orElse(0);
System.out.printf("Promedio: %.2f%n", promedio);

// Máximo y mínimo
int max = Arrays.stream(numeros).max().getAsInt();
int min = Arrays.stream(numeros).min().getAsInt();
```

---

## 4. La clase Arrays — utilidades esenciales

Java provee la clase `java.util.Arrays` con métodos estáticos muy útiles para trabajar con arrays. Debes importarla:

```java
import java.util.Arrays;
```

### 4.1 Arrays.toString() — Imprimir el array

Sin este método, imprimir un array directamente muestra una referencia de memoria, no los valores:

```java
int[] nums = {5, 2, 8, 1, 9};

System.out.println(nums);               // [I@6d06d69c  ← referencia inútil
System.out.println(Arrays.toString(nums)); // [5, 2, 8, 1, 9]  ← útil
```

Para arrays de objetos:

```java
String[] frutas = {"Mango", "Pera", "Uva"};
System.out.println(Arrays.toString(frutas)); // [Mango, Pera, Uva]
```

### 4.2 Arrays.sort() — Ordenar

Ordena el array **en su lugar** (modifica el original) en orden ascendente:

```java
int[] numeros = {5, 2, 8, 1, 9, 3, 7};
System.out.println("Antes: " + Arrays.toString(numeros)); // [5, 2, 8, 1, 9, 3, 7]

Arrays.sort(numeros);
System.out.println("Después: " + Arrays.toString(numeros)); // [1, 2, 3, 5, 7, 8, 9]
```

**Ordenar solo una parte del array:**

```java
int[] datos = {5, 2, 8, 1, 9, 3, 7};
// Ordenar solo desde el índice 2 hasta el 5 (sin incluir 5)
Arrays.sort(datos, 2, 5);
System.out.println(Arrays.toString(datos)); // [5, 2, 1, 8, 9, 3, 7]
```

**Ordenar Strings (alfabéticamente):**

```java
String[] nombres = {"Carlos", "Ana", "Beatriz", "David"};
Arrays.sort(nombres);
System.out.println(Arrays.toString(nombres)); // [Ana, Beatriz, Carlos, David]
```

**Ordenar en orden descendente (solo para arrays de objetos):**

```java
Integer[] nums = {5, 2, 8, 1, 9};  // Integer, no int (para usar Comparator)
Arrays.sort(nums, (a, b) -> b - a); // Comparator lambda descendente
System.out.println(Arrays.toString(nums)); // [9, 8, 5, 2, 1]
```

### 4.3 Arrays.binarySearch() — Búsqueda binaria

Busca un elemento en un array **ya ordenado** y devuelve su índice. Si no lo encuentra, devuelve un número negativo.

**Importante:** el array debe estar ordenado antes de usar `binarySearch()`.

```java
int[] numeros = {1, 3, 5, 7, 9, 11, 13, 15};  // ya ordenado

int indice1 = Arrays.binarySearch(numeros, 7);
System.out.println("7 está en índice: " + indice1);  // 3

int indice2 = Arrays.binarySearch(numeros, 6);
System.out.println("6 está en índice: " + indice2);  // negativo (no existe)
```

La búsqueda binaria es mucho más eficiente que la lineal para arrays grandes: en lugar de revisar cada elemento uno por uno, divide el array a la mitad en cada paso.

```
Búsqueda lineal de 7 en {1,3,5,7,9,11,13,15}:
  Revisa 1, 3, 5, 7 → encontrado en 4 pasos

Búsqueda binaria de 7 en {1,3,5,7,9,11,13,15}:
  Paso 1: compara con el centro (7) → ¡encontrado en 1 paso!
```

### 4.4 Arrays.copyOf() — Copiar arrays

Crea una nueva copia del array con el tamaño especificado:

```java
int[] original = {1, 2, 3, 4, 5};

// Copia con el mismo tamaño
int[] copia = Arrays.copyOf(original, original.length);
copia[0] = 999;
System.out.println(original[0]);  // 1  ← no se afectó
System.out.println(copia[0]);     // 999

// Copia más pequeña (trunca)
int[] parcial = Arrays.copyOf(original, 3);
System.out.println(Arrays.toString(parcial));  // [1, 2, 3]

// Copia más grande (rellena con 0 los nuevos)
int[] expandida = Arrays.copyOf(original, 8);
System.out.println(Arrays.toString(expandida)); // [1, 2, 3, 4, 5, 0, 0, 0]
```

### 4.5 Arrays.copyOfRange() — Copiar un rango

```java
int[] datos = {10, 20, 30, 40, 50, 60, 70};

// Copiar del índice 2 al 5 (sin incluir 5)
int[] rango = Arrays.copyOfRange(datos, 2, 5);
System.out.println(Arrays.toString(rango));  // [30, 40, 50]
```

### 4.6 Arrays.fill() — Rellenar con un valor

```java
int[] array = new int[5];
Arrays.fill(array, 7);
System.out.println(Arrays.toString(array));  // [7, 7, 7, 7, 7]

// Rellenar un rango
int[] datos = {1, 2, 3, 4, 5, 6, 7};
Arrays.fill(datos, 2, 5, 0);  // índices 2, 3 y 4
System.out.println(Arrays.toString(datos));  // [1, 2, 0, 0, 0, 6, 7]
```

### 4.7 Arrays.equals() — Comparar arrays

```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};
int[] c = {1, 2, 4};

System.out.println(a == b);              // false ← compara referencias
System.out.println(Arrays.equals(a, b)); // true  ← compara contenido
System.out.println(Arrays.equals(a, c)); // false
```

### 4.8 Resumen de métodos de Arrays

| Método | Descripción |
|--------|-------------|
| `Arrays.toString(arr)` | Convierte el array a String legible |
| `Arrays.sort(arr)` | Ordena el array en su lugar (ascendente) |
| `Arrays.sort(arr, from, to)` | Ordena un rango del array |
| `Arrays.binarySearch(arr, key)` | Busca un elemento (array debe estar ordenado) |
| `Arrays.copyOf(arr, length)` | Copia el array con nuevo tamaño |
| `Arrays.copyOfRange(arr, from, to)` | Copia un rango del array |
| `Arrays.fill(arr, value)` | Rellena todo el array con un valor |
| `Arrays.fill(arr, from, to, value)` | Rellena un rango con un valor |
| `Arrays.equals(arr1, arr2)` | Compara el contenido de dos arrays |
| `Arrays.deepEquals(arr1, arr2)` | Compara arrays multidimensionales |
| `Arrays.deepToString(arr)` | Convierte array multidimensional a String |

---

## 5. Arrays multidimensionales (matrices)

Un array multidimensional es un array de arrays. El más común es el **bidimensional** (también llamado **matriz**), que organiza los datos en filas y columnas.

### 5.1 Declaración y creación de matrices

```java
// Declaración
int[][] matriz;

// Creación: 3 filas y 4 columnas
matriz = new int[3][4];

// Declaración y creación juntas
int[][] tabla = new int[5][5];

// Declaración, creación e inicialización
int[][] m = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
```

### Representación visual de una matriz 3×4

```
        col0  col1  col2  col3
fila0  [ 1    2    3    4  ]
fila1  [ 5    6    7    8  ]
fila2  [ 9   10   11   12  ]

Acceso: matriz[fila][columna]
matriz[0][0] = 1
matriz[1][2] = 7
matriz[2][3] = 12
```

### 5.2 Acceso a elementos de una matriz

```java
int[][] m = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

System.out.println(m[0][0]);  // 1  (fila 0, columna 0)
System.out.println(m[1][1]);  // 5  (fila 1, columna 1)
System.out.println(m[2][2]);  // 9  (fila 2, columna 2 — diagonal principal)

// Modificar un elemento
m[0][1] = 100;
System.out.println(m[0][1]);  // 100

// Tamaño de la matriz
System.out.println(m.length);     // 3  (número de filas)
System.out.println(m[0].length);  // 3  (número de columnas en fila 0)
```

### 5.3 Recorrer una matriz con bucles anidados

```java
int[][] matriz = {
    {1,  2,  3,  4},
    {5,  6,  7,  8},
    {9, 10, 11, 12}
};

// Recorrido fila por fila
for (int i = 0; i < matriz.length; i++) {
    for (int j = 0; j < matriz[i].length; j++) {
        System.out.printf("%4d", matriz[i][j]);
    }
    System.out.println();
}
// Salida:
//    1   2   3   4
//    5   6   7   8
//    9  10  11  12
```

**Con for-each:**

```java
for (int[] fila : matriz) {
    for (int elemento : fila) {
        System.out.printf("%4d", elemento);
    }
    System.out.println();
}
```

### 5.4 Operaciones clásicas con matrices

**Suma de todos los elementos:**

```java
int[][] m = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
int suma = 0;

for (int[] fila : m) {
    for (int elem : fila) {
        suma += elem;
    }
}
System.out.println("Suma total: " + suma);  // 45
```

**Suma de la diagonal principal:**

```java
int[][] m = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
int diagonal = 0;

for (int i = 0; i < m.length; i++) {
    diagonal += m[i][i];  // elementos donde fila == columna
}
System.out.println("Diagonal principal: " + diagonal);  // 1+5+9 = 15
```

**Transponer una matriz (filas ↔ columnas):**

```java
int[][] original = {{1, 2, 3}, {4, 5, 6}};  // 2x3
int filas = original.length;                 // 2
int cols = original[0].length;               // 3
int[][] transpuesta = new int[cols][filas];  // 3x2

for (int i = 0; i < filas; i++) {
    for (int j = 0; j < cols; j++) {
        transpuesta[j][i] = original[i][j];
    }
}

// Imprimir transpuesta
System.out.println(Arrays.deepToString(transpuesta));
// [[1, 4], [2, 5], [3, 6]]
```

**Suma de dos matrices:**

```java
int[][] a = {{1, 2}, {3, 4}};
int[][] b = {{5, 6}, {7, 8}};
int[][] resultado = new int[2][2];

for (int i = 0; i < 2; i++) {
    for (int j = 0; j < 2; j++) {
        resultado[i][j] = a[i][j] + b[i][j];
    }
}
System.out.println(Arrays.deepToString(resultado));  // [[6, 8], [10, 12]]
```

### 5.5 Arrays de tres o más dimensiones

Java soporta cualquier número de dimensiones:

```java
// Array 3D: como un cubo o un conjunto de matrices
int[][][] cubo = new int[3][4][5];  // 3 capas, 4 filas, 5 columnas

// Inicialización
cubo[0][1][2] = 42;

// Recorrido con tres bucles
for (int i = 0; i < cubo.length; i++) {
    for (int j = 0; j < cubo[i].length; j++) {
        for (int k = 0; k < cubo[i][j].length; k++) {
            System.out.print(cubo[i][j][k] + " ");
        }
    }
}
```

---

## 6. Arrays irregulares (Jagged Arrays)

En Java, los arrays multidimensionales pueden tener filas de **tamaño diferente**. Esto se llama **jagged array** (array dentado).

```java
// Declarar sin especificar las columnas
int[][] triangulo = new int[4][];

// Asignar diferente número de columnas a cada fila
triangulo[0] = new int[]{1};
triangulo[1] = new int[]{1, 2};
triangulo[2] = new int[]{1, 2, 3};
triangulo[3] = new int[]{1, 2, 3, 4};

// Recorrer el array irregular
for (int i = 0; i < triangulo.length; i++) {
    for (int j = 0; j < triangulo[i].length; j++) {
        System.out.print(triangulo[i][j] + " ");
    }
    System.out.println();
}
// Salida:
// 1
// 1 2
// 1 2 3
// 1 2 3 4
```

**Caso de uso:** el Triángulo de Pascal es un ejemplo clásico de array irregular:

```java
int filas = 6;
int[][] pascal = new int[filas][];

for (int i = 0; i < filas; i++) {
    pascal[i] = new int[i + 1];
    pascal[i][0] = 1;
    pascal[i][i] = 1;

    for (int j = 1; j < i; j++) {
        pascal[i][j] = pascal[i-1][j-1] + pascal[i-1][j];
    }
}

for (int[] fila : pascal) {
    System.out.println(Arrays.toString(fila));
}
// [1]
// [1, 1]
// [1, 2, 1]
// [1, 3, 3, 1]
// [1, 4, 6, 4, 1]
// [1, 5, 10, 10, 5, 1]
```

---

## 7. Arrays como parámetros y retorno de métodos

### 7.1 Pasar un array a un método

Cuando pasas un array a un método, estás pasando la **referencia** (dirección de memoria), no una copia. Esto significa que si el método modifica el array, los cambios afectan al array original.

```java
public static void duplicarElementos(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        arr[i] *= 2;  // Modifica el array original
    }
}

public static void main(String[] args) {
    int[] numeros = {1, 2, 3, 4, 5};
    System.out.println(Arrays.toString(numeros));  // [1, 2, 3, 4, 5]

    duplicarElementos(numeros);
    System.out.println(Arrays.toString(numeros));  // [2, 4, 6, 8, 10]
}
```

### 7.2 Retornar un array desde un método

```java
public static int[] generarRango(int inicio, int fin) {
    int[] rango = new int[fin - inicio + 1];
    for (int i = 0; i < rango.length; i++) {
        rango[i] = inicio + i;
    }
    return rango;
}

public static int[] filtrarPositivos(int[] datos) {
    int conteo = 0;
    for (int d : datos) if (d > 0) conteo++;

    int[] positivos = new int[conteo];
    int idx = 0;
    for (int d : datos) {
        if (d > 0) positivos[idx++] = d;
    }
    return positivos;
}

public static void main(String[] args) {
    int[] rango = generarRango(5, 12);
    System.out.println(Arrays.toString(rango));
    // [5, 6, 7, 8, 9, 10, 11, 12]

    int[] mezcla = {3, -1, 7, -5, 2, -8, 9};
    int[] positivos = filtrarPositivos(mezcla);
    System.out.println(Arrays.toString(positivos));
    // [3, 7, 2, 9]
}
```

### 7.3 Varargs — número variable de argumentos

Java permite que un método acepte un número variable de argumentos del mismo tipo con `...` (varargs). Internamente se trata como un array:

```java
public static int sumar(int... numeros) {
    int total = 0;
    for (int n : numeros) total += n;
    return total;
}

public static void main(String[] args) {
    System.out.println(sumar(1, 2));             // 3
    System.out.println(sumar(1, 2, 3));          // 6
    System.out.println(sumar(1, 2, 3, 4, 5));   // 15
    System.out.println(sumar());                  // 0
}
```

---

## 8. Strings — repaso y profundización

Ya vimos `String` en el Tema 2. Aquí profundizamos en aspectos avanzados y métodos adicionales esenciales.

### 8.1 Strings y arrays de caracteres

Un `String` es internamente una secuencia de caracteres. Puedes convertir entre `String` y `char[]`:

```java
String texto = "Java Programming";

// String → array de caracteres
char[] caracteres = texto.toCharArray();
System.out.println(caracteres[0]);  // 'J'
System.out.println(caracteres.length);  // 16

// Array de caracteres → String
char[] letras = {'H', 'o', 'l', 'a'};
String nuevoString = new String(letras);     // "Hola"
String otroString = String.valueOf(letras);  // "Hola"

// Procesar carácter por carácter
for (char c : texto.toCharArray()) {
    if (Character.isUpperCase(c)) {
        System.out.print(c);  // JP
    }
}
```

### 8.2 Métodos de String esenciales (completo)

#### Búsqueda y verificación

```java
String s = "El lenguaje Java es muy poderoso";

// Buscar subcadenas
s.contains("Java");             // true
s.startsWith("El");             // true
s.endsWith("poderoso");         // true
s.indexOf("Java");              // 12  (primera ocurrencia)
s.lastIndexOf("a");             // 30  (última ocurrencia de 'a')
s.indexOf("Python");            // -1  (no existe)

// indexOf con posición de inicio
s.indexOf("a", 15);             // Busca 'a' desde la posición 15 en adelante

// Verificaciones rápidas
s.isEmpty();                    // false (tiene contenido)
s.isBlank();                    // false (Java 11+, incluye espacios)
"   ".isBlank();                // true
"".isEmpty();                   // true
```

#### Extracción

```java
String s = "Java Programming Language";

s.charAt(5);                    // 'P'  (carácter en posición 5)
s.substring(5);                 // "Programming Language"  (desde posición 5)
s.substring(5, 16);             // "Programming"  (del 5 al 15)

// Dividir por un separador
String csv = "Ana,Luis,María,Pedro";
String[] partes = csv.split(",");
// partes = ["Ana", "Luis", "María", "Pedro"]

// Dividir limitando la cantidad de partes
String[] primeras2 = csv.split(",", 2);
// primeras2 = ["Ana", "Luis,María,Pedro"]

// Dividir por espacio (una o más espacios)
String frase = "Hola   mundo   Java";
String[] palabras = frase.split("\\s+");
// palabras = ["Hola", "mundo", "Java"]
```

#### Transformación

```java
String s = "  Hola Mundo  ";

s.trim();                       // "Hola Mundo"  (elimina espacios inicio/fin)
s.strip();                      // "Hola Mundo"  (como trim pero Unicode-aware, Java 11+)
s.stripLeading();               // "Hola Mundo  "  (solo inicio)
s.stripTrailing();              // "  Hola Mundo"  (solo fin)
s.toUpperCase();                // "  HOLA MUNDO  "
s.toLowerCase();                // "  hola mundo  "
s.replace("Mundo", "Java");    // "  Hola Java  "
s.replaceAll("\\s+", "_");     // "__Hola_Mundo__"  (con regex)
s.replaceFirst("\\s", "-");    // "- Hola Mundo  "  (solo la primera ocurrencia)
```

#### Unión

```java
// String.join() — unir con separador
String resultado = String.join(", ", "Ana", "Luis", "María");
System.out.println(resultado);  // Ana, Luis, María

// Unir un array
String[] nombres = {"Ana", "Luis", "María"};
String unido = String.join(" - ", nombres);
System.out.println(unido);  // Ana - Luis - María

// String.format() — crear strings con formato
String info = String.format("Nombre: %s | Edad: %d | Promedio: %.2f",
                             "Carlos", 25, 8.75);
System.out.println(info);  // Nombre: Carlos | Edad: 25 | Promedio: 8.75
```

#### Comparación completa

```java
String a = "Manzana";
String b = "manzana";
String c = "Pera";

a.equals(b);                    // false  (sensible a mayúsculas)
a.equalsIgnoreCase(b);          // true   (ignora mayúsculas)
a.compareTo(c);                 // negativo: 'M' < 'P' (orden lexicográfico)
a.compareToIgnoreCase(b);       // 0  (son iguales ignorando mayúsculas)

// Comparar con null de forma segura
String nombre = null;
// nombre.equals("Java")  ← NullPointerException
"Java".equals(nombre);          // false  ← seguro (el literal no es null)
Objects.equals(nombre, "Java"); // false  ← también seguro (Java 7+)
```

#### Métodos nuevos (Java 11–15+)

```java
String s = "Java";

// repeat() — repetir el string
s.repeat(3);                    // "JavaJavaJava"  (Java 11+)

// lines() — dividir por saltos de línea
"Línea1\nLínea2\nLínea3"
    .lines()
    .forEach(System.out::println);

// formatted() — equivalente a String.format() (Java 15+)
"Hola %s, tienes %d años".formatted("Ana", 30);

// indent() — añadir indentación (Java 12+)
"Texto\nOtro texto\n".indent(4);

// stripIndent() — eliminar indentación común en text blocks (Java 15+)
```

### 8.3 Convertir String a array y viceversa

```java
// String → array de palabras
String oracion = "El gato come pescado";
String[] palabras = oracion.split(" ");
// ["El", "gato", "come", "pescado"]

// Array → String
String[] partes = {"Java", "es", "genial"};
String unido1 = String.join(" ", partes);   // "Java es genial"
String unido2 = Arrays.toString(partes);    // "[Java, es, genial]"

// Contar palabras en una oración
String texto = "Esto es una frase de ejemplo con siete palabras";
int cantPalabras = texto.split("\\s+").length;
System.out.println("Palabras: " + cantPalabras);  // 8
```

---

## 9. StringBuilder y StringBuffer

### 9.1 El problema de concatenar Strings en bucles

Los `String` en Java son inmutables. Cada vez que usas `+` para concatenar, se crea un nuevo objeto `String` en memoria. Esto es ineficiente cuando concatenas en un bucle:

```java
// MAL: crea miles de objetos String temporales en el heap
String resultado = "";
for (int i = 0; i < 10000; i++) {
    resultado += i;  // Cada iteración crea un nuevo String y descarta el anterior
}
// Esto crea ~10,000 objetos String intermedios, muy lento para n grande
```

### 9.2 StringBuilder — la solución eficiente

`StringBuilder` es una clase que representa un String **mutable**: puedes modificarlo sin crear objetos nuevos en cada operación. Es mucho más eficiente para construir Strings dinámicamente.

```java
// BIEN: StringBuilder es mutable, no crea objetos intermedios
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append(i);  // Modifica el mismo objeto
}
String resultado = sb.toString();  // Convierte a String al final
```

### 9.3 Métodos principales de StringBuilder

```java
StringBuilder sb = new StringBuilder("Hola");

// append() — añadir al final (acepta cualquier tipo)
sb.append(" Mundo");         // "Hola Mundo"
sb.append("!");              // "Hola Mundo!"
sb.append(42);               // "Hola Mundo!42"
sb.append(3.14);             // "Hola Mundo!423.14"
sb.append(true);             // "Hola Mundo!423.14true"

// insert() — insertar en posición específica
sb.insert(4, " Java");       // Inserta en posición 4

// delete() — eliminar un rango de caracteres
sb.delete(4, 9);             // Elimina del índice 4 al 8

// replace() — reemplazar un rango
sb.replace(0, 4, "Adiós");  // Reemplaza del índice 0 al 3

// reverse() — invertir el contenido
sb.reverse();                // Invierte todos los caracteres

// deleteCharAt() — eliminar un carácter
sb.deleteCharAt(0);          // Elimina el carácter en posición 0

// charAt() y setCharAt() — leer y modificar un carácter
char c = sb.charAt(2);       // Lee el carácter en posición 2
sb.setCharAt(0, 'J');        // Cambia el carácter en posición 0

// length() y capacity()
sb.length();                 // Número de caracteres actuales
sb.capacity();               // Capacidad del buffer interno

// toString() — convertir a String inmutable
String resultado = sb.toString();
```

### 9.4 Encadenamiento de métodos (method chaining)

Los métodos de `StringBuilder` devuelven el mismo `StringBuilder`, lo que permite encadenarlos:

```java
String resultado = new StringBuilder()
    .append("Nombre: ")
    .append("Carlos")
    .append(" | Edad: ")
    .append(28)
    .append(" | Ciudad: ")
    .append("Madrid")
    .toString();

System.out.println(resultado);
// Nombre: Carlos | Edad: 28 | Ciudad: Madrid
```

### 9.5 StringBuilder vs. StringBuffer

| Característica | StringBuilder | StringBuffer |
|----------------|---------------|--------------|
| Mutable | Sí | Sí |
| Thread-safe | No | Sí (sincronizado) |
| Rendimiento | Más rápido | Más lento (por sincronización) |
| Uso recomendado | Un solo hilo (la mayoría de los casos) | Múltiples hilos concurrentes |

Para el 99% de los casos en programación de un solo hilo, usa `StringBuilder`.

### 9.6 Cuándo usar cada uno

```java
// Usa + para concatenaciones simples (el compilador las optimiza)
String s = "Hola" + " " + "Mundo";  // Optimizado por el compilador

// Usa StringBuilder para bucles o construcciones dinámicas
StringBuilder sb = new StringBuilder();
for (String linea : listaDeLineas) {
    sb.append(linea).append("\n");
}
String texto = sb.toString();

// Usa String.format() o formatted() para templates complejos
String info = String.format("%-20s %5d %8.2f", nombre, edad, salario);
```

---

## 10. Expresiones regulares con Strings

Las **expresiones regulares** (regex) son patrones que describen conjuntos de Strings. Son muy poderosas para validar, buscar y transformar texto.

### 10.1 Métodos de String que usan regex

```java
// matches() — verifica si el String completo coincide con el patrón
String email = "usuario@ejemplo.com";
boolean esEmail = email.matches("[a-zA-Z0-9+_.-]+@[a-zA-Z0-9.-]+");
System.out.println(esEmail);  // true

// replaceAll() — reemplaza todas las coincidencias del patrón
String texto = "El   perro   come    mucho";
String limpio = texto.replaceAll("\\s+", " ");
System.out.println(limpio);  // "El perro come mucho"

// split() — divide usando el patrón como separador
String datos = "Ana:25:Madrid;Luis:30:Barcelona";
String[] personas = datos.split(";");
// ["Ana:25:Madrid", "Luis:30:Barcelona"]
```

### 10.2 Patrones regex más usados

| Patrón | Significado | Ejemplo |
|--------|-------------|---------|
| `\d` | Un dígito (0-9) | `\d+` → uno o más dígitos |
| `\D` | No es un dígito | `\D+` → uno o más no-dígitos |
| `\w` | Letra, dígito o `_` | `\w+` → una palabra |
| `\W` | No es letra/dígito/_ | |
| `\s` | Espacio en blanco | `\s+` → uno o más espacios |
| `\S` | No es espacio | |
| `.` | Cualquier carácter | `a.b` → "axb", "a1b"... |
| `*` | 0 o más veces | `a*` → "", "a", "aa"... |
| `+` | 1 o más veces | `a+` → "a", "aa"... |
| `?` | 0 o 1 vez | `colou?r` → "color" o "colour" |
| `{n}` | Exactamente n veces | `\d{4}` → exactamente 4 dígitos |
| `{n,m}` | Entre n y m veces | `\d{2,4}` → 2 a 4 dígitos |
| `^` | Inicio del String | `^Hola` → empieza con "Hola" |
| `$` | Fin del String | `mundo$` → termina con "mundo" |
| `[abc]` | a, b o c | `[aeiou]` → vocal |
| `[^abc]` | Ni a, ni b, ni c | |
| `[a-z]` | Rango de a a z | `[A-Za-z]` → letra |
| `a\|b` | a o b | `gato\|perro` |

**Nota:** En Java, en los Strings literales se necesita `\\` para representar `\` porque el backslash es un carácter de escape del String.

### 10.3 Validaciones comunes con regex

```java
// Validar número entero positivo
"12345".matches("\\d+");               // true
"-12".matches("\\d+");                 // false

// Validar número con decimales
"3.14".matches("\\d+\\.\\d+");        // true
"42".matches("\\d+\\.?\\d*");         // true (decimal opcional)

// Validar código postal de 5 dígitos
"28001".matches("\\d{5}");            // true
"2800".matches("\\d{5}");             // false

// Validar solo letras
"Carlos".matches("[a-zA-ZáéíóúÁÉÍÓÚñÑ]+");  // true
"Carlos123".matches("[a-zA-Z]+");              // false

// Validar contraseña: al menos 8 chars, una mayúscula, un número
String pwd = "Segura1!";
boolean valida = pwd.matches("(?=.*[A-Z])(?=.*\\d).{8,}");
System.out.println(valida);  // true

// Validar formato de fecha DD/MM/YYYY
"25/12/2024".matches("\\d{2}/\\d{2}/\\d{4}");  // true
```

---

## 11. Errores comunes con Arrays y Strings

### Error 1: ArrayIndexOutOfBoundsException

El error más frecuente con arrays: acceder a un índice que no existe.

```java
int[] arr = {1, 2, 3};

System.out.println(arr[3]);  // ERROR: índices válidos son 0, 1, 2
System.out.println(arr[-1]); // ERROR: no existen índices negativos

// CORRECTO: el último elemento está en length-1
System.out.println(arr[arr.length - 1]);  // 3
```

### Error 2: NullPointerException al usar un array no inicializado

```java
int[] arr;  // Declarado pero no inicializado (es null)
arr[0] = 5;  // NullPointerException

// CORRECTO: siempre inicializar antes de usar
int[] arr = new int[5];
arr[0] = 5;
```

### Error 3: Copiar array por referencia (alias)

```java
int[] original = {1, 2, 3};
int[] alias = original;  // ¡No es una copia! Ambos apuntan al mismo array

alias[0] = 999;
System.out.println(original[0]);  // 999 ← original también cambió

// CORRECTO: usar Arrays.copyOf() o clone()
int[] copia = Arrays.copyOf(original, original.length);
int[] copia2 = original.clone();
```

### Error 4: Comparar arrays con ==

```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

System.out.println(a == b);              // false ← compara referencias
System.out.println(Arrays.equals(a, b)); // true  ← compara contenido

// Para matrices:
int[][] m1 = {{1,2},{3,4}};
int[][] m2 = {{1,2},{3,4}};
System.out.println(Arrays.deepEquals(m1, m2)); // true
```

### Error 5: Off-by-one en el límite del bucle

```java
int[] arr = {10, 20, 30};

// ERROR: i <= arr.length llega hasta índice 3, que no existe
for (int i = 0; i <= arr.length; i++) {
    System.out.println(arr[i]);  // ArrayIndexOutOfBoundsException en la última iteración
}

// CORRECTO: i < arr.length (el último índice válido es 2)
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
```

### Error 6: binarySearch en array no ordenado

```java
int[] desordenado = {5, 2, 8, 1, 9};
int idx = Arrays.binarySearch(desordenado, 8);
System.out.println(idx);  // Resultado impredecible (puede ser incorrecto)

// CORRECTO: ordenar primero
Arrays.sort(desordenado);
idx = Arrays.binarySearch(desordenado, 8);
System.out.println(idx);  // 3 (posición correcta en array ordenado)
```

### Error 7: Comparar Strings con == dentro de arrays

```java
String[] nombres = {"Ana", "Luis", "Ana"};

// INCORRECTO para comparar contenido
System.out.println(nombres[0] == nombres[2]);         // puede ser true o false

// CORRECTO
System.out.println(nombres[0].equals(nombres[2]));    // true siempre
```

### Error 8: NullPointerException en arrays de objetos

```java
String[] arr = new String[3];  // Todos son null por defecto
System.out.println(arr[0].length());  // NullPointerException

// CORRECTO: verificar null o inicializar
if (arr[0] != null) {
    System.out.println(arr[0].length());
}
// O inicializar todos los elementos:
Arrays.fill(arr, "");  // Rellena con String vacío
```

---

## 12. Ejercicios prácticos

### Ejercicio 1 — Operaciones básicas con array (Nivel: Básico)

Crea `OperacionesArray.java` con el array `int[] datos = {15, 42, 8, 73, 29, 56, 11, 88, 37, 64}`.

Sin usar `Arrays.sort()` ni ningún método de la clase Arrays, calcula manualmente:
1. El valor máximo y mínimo
2. La suma y el promedio
3. Cuántos números son mayores al promedio
4. Cuántos son pares y cuántos impares
5. Imprímelo todo con formato

---

### Ejercicio 2 — Ordenamiento burbuja (Nivel: Básico-Medio)

Crea `OrdenamientoBurbuja.java` que implemente el algoritmo de **Bubble Sort** manualmente (sin usar `Arrays.sort()`):

El algoritmo compara pares de elementos adyacentes y los intercambia si están en el orden incorrecto. Repite este proceso hasta que el array esté completamente ordenado.

Prueba con: `int[] arr = {64, 34, 25, 12, 22, 11, 90}`

Muestra el array antes y después de ordenar.

*Pista: Necesitarás dos bucles `for` anidados y una variable temporal para el intercambio.*

---

### Ejercicio 3 — Rotación de array (Nivel: Medio)

Crea `RotacionArray.java` con el array `int[] arr = {1, 2, 3, 4, 5, 6, 7}`.

Implementa dos métodos:
1. `rotarIzquierda(int[] arr, int k)`: rota k posiciones hacia la izquierda
2. `rotarDerecha(int[] arr, int k)`: rota k posiciones hacia la derecha

Con k=3 el resultado debe ser:
```
Original:       [1, 2, 3, 4, 5, 6, 7]
Rotar izquierda 3: [4, 5, 6, 7, 1, 2, 3]
Rotar derecha 3:   [5, 6, 7, 1, 2, 3, 4]
```

---

### Ejercicio 4 — Multiplicación de matrices (Nivel: Medio)

Crea `MultiplicacionMatrices.java` que multiplique dos matrices 3×3.

Matrices de prueba:
```
A = {{1,2,3},{4,5,6},{7,8,9}}
B = {{9,8,7},{6,5,4},{3,2,1}}
```

La multiplicación de matrices: `C[i][j] = suma de A[i][k] * B[k][j]` para k de 0 a n-1.

Muestra las tres matrices (A, B y el resultado C) con formato de tabla.

---

### Ejercicio 5 — Procesamiento de texto (Nivel: Medio)

Crea `ProcesadorTexto.java` con el String:

```
"El zorro rápido salta sobre el perro perezoso. El zorro es muy ágil."
```

Sin usar expresiones regulares avanzadas, determina:
1. Número total de caracteres (con y sin espacios)
2. Número de palabras
3. Número de vocales (a, e, i, o, u, mayúsculas y minúsculas)
4. Número de consonantes
5. La palabra más larga
6. Cuántas veces aparece la palabra "El" (con cualquier capitalización)
7. El texto reemplazando "zorro" por "jaguar"
8. El texto con la primera letra de cada palabra en mayúscula

---

### Ejercicio 6 — Validador de datos (Nivel: Medio-Avanzado)

Crea `ValidadorDatos.java` que use expresiones regulares para validar:

Prueba cada uno con al menos 3 ejemplos (válidos e inválidos):

1. **Código postal:** exactamente 5 dígitos
2. **Número de teléfono:** formato `+XX-XXXXXXXXXX` (código de país + número)
3. **Correo electrónico:** formato básico `algo@dominio.extension`
4. **Contraseña fuerte:** mínimo 8 caracteres, al menos 1 mayúscula, 1 minúscula, 1 dígito
5. **Nombre completo:** solo letras (incluyendo acentos y ñ) y espacios, mínimo 2 palabras
6. **Fecha:** formato `DD/MM/YYYY`

Para cada validación, muestra el valor y si es válido o no.

---

### Ejercicio 7 — Constructor de reportes con StringBuilder (Nivel: Avanzado)

Crea `ReporteVentas.java` que use `StringBuilder` para construir un reporte completo a partir de los siguientes datos:

```java
String[] productos = {"Laptop", "Mouse", "Teclado", "Monitor", "Auriculares"};
int[] cantidades    = {3, 15, 8, 5, 12};
double[] precios    = {999.99, 29.99, 49.99, 299.99, 79.99};
```

El reporte debe tener este formato:

```
╔══════════════════════════════════════════════════════╗
║              REPORTE DE VENTAS - 2024                ║
╠══════════════════════════════════════════════════════╣
║ Producto         │ Cant │   Precio │     Total       ║
╠══════════════════════════════════════════════════════╣
║ Laptop           │    3 │  $999.99 │  $2,999.97      ║
║ Mouse            │   15 │   $29.99 │    $449.85      ║
║ Teclado          │    8 │   $49.99 │    $399.92      ║
║ Monitor          │    5 │  $299.99 │  $1,499.95      ║
║ Auriculares      │   12 │   $79.99 │    $959.88      ║
╠══════════════════════════════════════════════════════╣
║ TOTAL GENERAL    │   43 │          │  $6,309.57      ║
╚══════════════════════════════════════════════════════╝
```

---

## 13. Resumen del tema

### Arrays en una página

```java
// Declarar y crear
int[] arr = new int[5];              // Tamaño 5, valores en 0
int[] arr = {1, 2, 3, 4, 5};        // Inicialización directa

// Acceder y modificar
arr[0]                               // Primer elemento
arr[arr.length - 1]                  // Último elemento
arr[i] = valor;                      // Modificar elemento

// Recorrer
for (int i = 0; i < arr.length; i++) { }   // Con índice
for (int n : arr) { }                       // Sin índice (for-each)

// Clase Arrays (import java.util.Arrays)
Arrays.toString(arr)                 // Imprimir legible
Arrays.sort(arr)                     // Ordenar
Arrays.binarySearch(arr, clave)      // Buscar (array debe estar ordenado)
Arrays.copyOf(arr, nuevoTamaño)      // Copiar
Arrays.fill(arr, valor)              // Rellenar
Arrays.equals(arr1, arr2)            // Comparar contenido

// Matrices
int[][] m = new int[filas][cols];
int[][] m = {{1,2},{3,4}};
m[fila][col]                         // Acceso
m.length                             // Número de filas
m[0].length                          // Número de columnas
Arrays.deepToString(m)               // Imprimir matriz
```

### Strings en una página

```java
// Crear
String s = "texto";
String s = new String("texto");  // Evitar

// Métodos clave
s.length()                       // Longitud
s.charAt(i)                      // Carácter en posición i
s.substring(ini, fin)            // Subcadena
s.indexOf("sub")                 // Posición de subcadena (-1 si no existe)
s.contains("sub")                // ¿Contiene la subcadena?
s.startsWith("x") / endsWith("x")
s.toUpperCase() / toLowerCase()
s.trim() / strip()               // Eliminar espacios
s.replace("a", "b")              // Reemplazar
s.split(",")                     // Dividir en array
s.equals(s2)                     // Comparar contenido (no ==)
s.equalsIgnoreCase(s2)           // Comparar sin importar mayúsculas
String.join(sep, ...)            // Unir strings
String.format("...", ...)        // Formatear
s.matches(regex)                 // Validar con expresión regular

// StringBuilder (mutable, eficiente en bucles)
StringBuilder sb = new StringBuilder();
sb.append("texto").append(42);
sb.insert(pos, "texto");
sb.delete(ini, fin);
sb.reverse();
String resultado = sb.toString();
```

### Reglas de oro

| Situación | Regla |
|-----------|-------|
| Índices | Siempre de 0 a `length - 1` |
| Imprimir array | Usar `Arrays.toString()` o `Arrays.deepToString()` |
| Copiar array | Usar `Arrays.copyOf()`, nunca `array2 = array1` |
| Comparar arrays | Usar `Arrays.equals()`, nunca `==` |
| Comparar Strings | Usar `.equals()`, nunca `==` |
| Concatenar en bucle | Usar `StringBuilder`, no `+` |
| Buscar con binarySearch | Ordenar primero con `Arrays.sort()` |
| Array de objetos sin inicializar | Los elementos son `null`, verificar antes de usar |

---

## Próximo tema

**Tema 6: Métodos y funciones**

Aprenderás a declarar y llamar métodos, la diferencia entre métodos con y sin retorno, paso de parámetros por valor y por referencia, sobrecarga de métodos, recursividad, y el ámbito de las variables. Este tema es el último del Nivel 1 y te prepara para la Programación Orientada a Objetos del Nivel 2.

---

*Curso de Java — Nivel 1: Fundamentos*
*Tema 5 de 6*
*Creado con fines educativos*
