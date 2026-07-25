# Curso de Java — Nivel 1: Fundamentos
## Tema 6: Métodos y Funciones

> **Nivel:** Principiante | **Prerrequisitos:** Temas 1 al 5 completados | **Duración estimada:** 4–5 horas

---

## Tabla de contenidos

1. [¿Qué es un método?](#1-qué-es-un-método)
2. [Anatomía de un método](#2-anatomía-de-un-método)
3. [Métodos sin retorno (void)](#3-métodos-sin-retorno-void)
4. [Métodos con retorno](#4-métodos-con-retorno)
5. [Parámetros y argumentos](#5-parámetros-y-argumentos)
6. [Paso por valor vs. paso por referencia](#6-paso-por-valor-vs-paso-por-referencia)
7. [Sobrecarga de métodos (Overloading)](#7-sobrecarga-de-métodos-overloading)
8. [Recursividad](#8-recursividad)
9. [Ámbito de variables en métodos](#9-ámbito-de-variables-en-métodos)
10. [Métodos static vs. de instancia](#10-métodos-static-vs-de-instancia)
11. [El método main en profundidad](#11-el-método-main-en-profundidad)
12. [Buenas prácticas en el diseño de métodos](#12-buenas-prácticas-en-el-diseño-de-métodos)
13. [Errores comunes con métodos](#13-errores-comunes-con-métodos)
14. [Ejercicios prácticos](#14-ejercicios-prácticos)
15. [Resumen del tema y del Nivel 1](#15-resumen-del-tema-y-del-nivel-1)

---

## 1. ¿Qué es un método?

Un **método** es un bloque de código con nombre que realiza una tarea específica y puede ser invocado (llamado) desde otras partes del programa tantas veces como sea necesario.

Sin métodos, si necesitas calcular el área de un círculo en 10 lugares distintos del programa, tendrías que escribir la misma fórmula 10 veces. Con un método, la escribes una vez y la llamas 10 veces.

```java
// SIN métodos: código repetido en cada lugar
double area1 = 3.14159 * 5.0 * 5.0;
double area2 = 3.14159 * 3.0 * 3.0;
double area3 = 3.14159 * 7.0 * 7.0;

// CON un método: escribe una vez, llama muchas veces
double area1 = calcularAreaCirculo(5.0);
double area2 = calcularAreaCirculo(3.0);
double area3 = calcularAreaCirculo(7.0);
```

### ¿Por qué usar métodos?

Los métodos son uno de los pilares de la programación bien estructurada. Sus beneficios son:

**1. Reutilización de código (Don't Repeat Yourself — DRY)**
Escribes la lógica una sola vez y la reutilizas en cualquier parte del programa.

**2. Modularidad**
Divides un problema grande en partes pequeñas y manejables. Cada método hace una sola cosa bien definida.

**3. Legibilidad**
Un programa compuesto de llamadas a métodos con nombres descriptivos es mucho más fácil de leer que un bloque monolítico de 500 líneas.

**4. Mantenimiento**
Si necesitas corregir o cambiar la lógica de una tarea, solo modificas el método en un lugar, y el cambio se aplica en todos los sitios que lo llaman.

**5. Testabilidad**
Los métodos pequeños y bien definidos son mucho más fáciles de probar individualmente.

```
Problema grande
       │
  ┌────┴────────────────────┐
  │                         │
método1()            método2()
  │                         │
  ├── submétodoA()    ├── submétodoC()
  └── submétodoB()    └── submétodoD()
```

---

## 2. Anatomía de un método

Todo método en Java tiene una estructura definida llamada **firma** (signature) o declaración, seguida de un **cuerpo**.

### Estructura completa

```java
modificadorDeAcceso  modificadorOpcional  tipoDeRetorno  nombreDelMetodo(parámetros)  {
    // cuerpo del método
    // instrucciones
    return valor;  // solo si tipoDeRetorno no es void
}
```

### Ejemplo anotado

```java
public static double calcularAreaCirculo(double radio) {
//  ↑      ↑       ↑                     ↑
//  │      │       │                     └─ Parámetro: tipo + nombre
//  │      │       └─ Tipo de retorno: devuelve un double
//  │      └─ Modificador: pertenece a la clase, no a un objeto
//  └─ Modificador de acceso: visible desde cualquier lugar

    final double PI = 3.141592653589793;
    double area = PI * radio * radio;
    return area;  // ← Devuelve el resultado al lugar que llamó al método
}
```

### Las partes en detalle

**Modificador de acceso:**
Controla desde dónde puede ser llamado el método.
- `public`: visible desde cualquier clase
- `private`: visible solo dentro de la misma clase
- `protected`: visible en la misma clase y subclases
- *(sin modificador)*: visible solo en el mismo paquete

**Modificador opcional:**
- `static`: el método pertenece a la clase, no a un objeto
- `final`: no puede ser sobreescrito en subclases
- `abstract`: sin cuerpo, debe ser implementado por subclases
- `synchronized`: para hilos concurrentes

**Tipo de retorno:**
El tipo de dato que devuelve el método. Si no devuelve nada, se usa `void`.

**Nombre del método:**
Debe seguir la convención `camelCase` y describir claramente la acción que realiza. Convención: usar verbos. Ejemplos: `calcular`, `obtener`, `validar`, `imprimir`, `convertir`.

**Parámetros:**
Lista de variables que el método recibe al ser llamado. Cada parámetro tiene un tipo y un nombre, separados por comas. Puede estar vacío.

**Cuerpo:**
El bloque de código entre `{ }` que contiene la lógica del método.

**return:**
Termina la ejecución del método y opcionalmente devuelve un valor. En métodos `void`, `return;` es opcional (el método termina al llegar a la llave de cierre).

---

## 3. Métodos sin retorno (void)

Un método `void` realiza una acción pero no devuelve ningún valor al código que lo llamó. Se usan cuando el objetivo es ejecutar algo (imprimir, modificar datos, mostrar un menú) sin necesitar un resultado.

### Sintaxis

```java
public static void nombreDelMetodo(parámetros) {
    // instrucciones
    // NO hay return con valor
}
```

### Ejemplos

**Método de impresión:**

```java
public static void imprimirSeparador() {
    System.out.println("═══════════════════════════════════════");
}

public static void imprimirTitulo(String titulo) {
    imprimirSeparador();
    System.out.println("  " + titulo);
    imprimirSeparador();
}
```

**Llamada:**

```java
public static void main(String[] args) {
    imprimirTitulo("Reporte de Ventas");
    // Salida:
    // ═══════════════════════════════════════
    //   Reporte de Ventas
    // ═══════════════════════════════════════
}
```

**Método que modifica un array:**

```java
public static void rellenarConCeros(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        arr[i] = 0;
    }
    // No retorna nada; el array original ya fue modificado
}

public static void imprimirArray(int[] arr) {
    System.out.print("[");
    for (int i = 0; i < arr.length; i++) {
        System.out.print(arr[i]);
        if (i < arr.length - 1) System.out.print(", ");
    }
    System.out.println("]");
}
```

**return en un método void (para salir anticipadamente):**

```java
public static void imprimirPositivos(int[] arr) {
    if (arr == null) {
        System.out.println("El array es null.");
        return;  // Sale del método inmediatamente
    }

    for (int n : arr) {
        if (n > 0) {
            System.out.print(n + " ");
        }
    }
    System.out.println();
}
```

---

## 4. Métodos con retorno

Un método con retorno devuelve un valor al código que lo llamó. El tipo de ese valor debe declararse en la firma del método. La instrucción `return` es obligatoria y debe aparecer en todos los posibles caminos de ejecución.

### Sintaxis

```java
public static tipoDeRetorno nombreDelMetodo(parámetros) {
    // instrucciones
    return valor;  // valor debe ser del tipo declarado
}
```

### Ejemplos con distintos tipos de retorno

**Retorno de int:**

```java
public static int sumar(int a, int b) {
    return a + b;
}

public static int maximo(int a, int b) {
    return a > b ? a : b;
}

public static int factorial(int n) {
    int resultado = 1;
    for (int i = 2; i <= n; i++) {
        resultado *= i;
    }
    return resultado;
}
```

**Retorno de double:**

```java
public static double calcularAreaCirculo(double radio) {
    return Math.PI * radio * radio;
}

public static double calcularPromedio(int[] datos) {
    int suma = 0;
    for (int d : datos) suma += d;
    return (double) suma / datos.length;
}
```

**Retorno de boolean:**

```java
public static boolean esPar(int numero) {
    return numero % 2 == 0;
}

public static boolean esPrimo(int n) {
    if (n < 2) return false;
    for (int i = 2; i <= Math.sqrt(n); i++) {
        if (n % i == 0) return false;
    }
    return true;
}

public static boolean esVocal(char c) {
    c = Character.toLowerCase(c);
    return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
}
```

**Retorno de String:**

```java
public static String obtenerDiaSemana(int numero) {
    return switch (numero) {
        case 1 -> "Lunes";
        case 2 -> "Martes";
        case 3 -> "Miércoles";
        case 4 -> "Jueves";
        case 5 -> "Viernes";
        case 6 -> "Sábado";
        case 7 -> "Domingo";
        default -> "Día inválido";
    };
}

public static String invertirString(String s) {
    return new StringBuilder(s).reverse().toString();
}
```

**Retorno de array:**

```java
public static int[] generarFibonacci(int cantidad) {
    int[] fib = new int[cantidad];
    if (cantidad >= 1) fib[0] = 0;
    if (cantidad >= 2) fib[1] = 1;
    for (int i = 2; i < cantidad; i++) {
        fib[i] = fib[i-1] + fib[i-2];
    }
    return fib;
}
```

### Uso del valor retornado

El valor que retorna un método puede usarse de distintas maneras:

```java
// 1. Guardarlo en una variable
int resultado = sumar(10, 20);

// 2. Usarlo directamente en una expresión
System.out.println("La suma es: " + sumar(10, 20));

// 3. Pasarlo como argumento a otro método
System.out.println(obtenerDiaSemana(sumar(1, 2)));

// 4. Usarlo en una condición
if (esPrimo(17)) {
    System.out.println("17 es primo");
}

// 5. Ignorarlo (válido pero inusual)
factorial(5);  // Se calcula pero el resultado se descarta
```

### El return termina el método inmediatamente

En cuanto Java ejecuta un `return`, el método termina sin importar si hay más código después:

```java
public static String clasificarNumero(int n) {
    if (n < 0) return "Negativo";    // Sale aquí si n < 0
    if (n == 0) return "Cero";       // Sale aquí si n == 0
    return "Positivo";               // Solo llega aquí si n > 0
    // Cualquier código después de return sería inalcanzable (unreachable code)
}
```

### Todos los caminos deben retornar un valor

```java
// ERROR: si n es 0, ningún return se ejecuta
public static int dividir(int a, int b) {
    if (b != 0) {
        return a / b;
    }
    // ERROR: missing return statement
}

// CORRECTO:
public static int dividir(int a, int b) {
    if (b != 0) {
        return a / b;
    }
    return 0;  // Valor por defecto si b == 0
}

// O también:
public static int dividir(int a, int b) {
    if (b == 0) return 0;
    return a / b;
}
```

---

## 5. Parámetros y argumentos

### Terminología

- **Parámetro:** la variable declarada en la firma del método. Existe solo dentro del método.
- **Argumento:** el valor concreto que se pasa al llamar al método.

```java
// radio es un PARÁMETRO (declarado en la firma)
public static double calcularArea(double radio) {
    return Math.PI * radio * radio;
}

// 5.0 es un ARGUMENTO (valor pasado al llamar)
double area = calcularArea(5.0);
```

### Métodos sin parámetros

```java
public static void saludar() {
    System.out.println("¡Hola!");
}

public static double obtenerPI() {
    return Math.PI;
}

// Llamada sin argumentos
saludar();
double pi = obtenerPI();
```

### Métodos con múltiples parámetros

```java
public static double calcularHipotenusa(double cateto1, double cateto2) {
    return Math.sqrt(cateto1 * cateto1 + cateto2 * cateto2);
}

public static String formatearNombre(String nombre, String apellido, String titulo) {
    return titulo + " " + nombre + " " + apellido;
}

// Los argumentos deben pasarse EN EL MISMO ORDEN y tipo que los parámetros
double h = calcularHipotenusa(3.0, 4.0);          // 5.0
String nombre = formatearNombre("Ana", "García", "Dra.");
```

### Parámetros con tipos de referencia

Cuando el parámetro es un objeto (String, array, etc.), lo que se pasa es la referencia al objeto, no una copia del objeto. Más detalles en la sección 6.

```java
public static void imprimirArray(int[] arr, String etiqueta) {
    System.out.print(etiqueta + ": ");
    for (int n : arr) System.out.print(n + " ");
    System.out.println();
}

int[] datos = {1, 2, 3, 4, 5};
imprimirArray(datos, "Datos");  // Datos: 1 2 3 4 5
```

### Varargs — número variable de argumentos

Con `...` (varargs) el método acepta cero o más argumentos del mismo tipo, tratándolos internamente como un array:

```java
public static double promedio(double... numeros) {
    if (numeros.length == 0) return 0;
    double suma = 0;
    for (double n : numeros) suma += n;
    return suma / numeros.length;
}

public static String concatenar(String separador, String... palabras) {
    return String.join(separador, palabras);
}

// Uso:
System.out.println(promedio(5, 10, 15));            // 10.0
System.out.println(promedio(3, 6, 9, 12, 15));      // 9.0
System.out.println(concatenar(" | ", "A", "B", "C")); // A | B | C
```

**Reglas de varargs:**
- Solo puede haber un parámetro varargs por método.
- Debe ser el último parámetro en la lista.
- `String...` es equivalente a `String[]` internamente.

---

## 6. Paso por valor vs. paso por referencia

Este es uno de los conceptos más importantes y frecuentemente malentendidos de Java. Entenderlo correctamente evita muchos bugs sutiles.

### 6.1 La regla de Java: SIEMPRE pasa por valor

Java siempre pasa los argumentos **por valor**. Sin excepción. La diferencia está en qué es ese "valor":

- Para **tipos primitivos**: el valor es el dato en sí (el número, el boolean, el char).
- Para **tipos de referencia**: el valor es la **dirección de memoria** (la referencia al objeto).

### 6.2 Primitivos — la copia es independiente

Cuando pasas un primitivo a un método, el método recibe una **copia** del valor. Cualquier cambio dentro del método no afecta a la variable original.

```java
public static void intentarModificar(int numero) {
    numero = 999;  // Modifica la COPIA local, no el original
    System.out.println("Dentro del método: " + numero);  // 999
}

public static void main(String[] args) {
    int x = 42;
    System.out.println("Antes: " + x);   // 42
    intentarModificar(x);
    System.out.println("Después: " + x); // 42  ← no cambió
}
```

```
main                    intentarModificar
┌──────────┐            ┌──────────┐
│ x = 42   │  copia───► │ numero=42│
└──────────┘            │ numero=999
                        └──────────┘
  x sigue siendo 42     (copia destruida al salir del método)
```

### 6.3 Objetos y arrays — la referencia se copia, el objeto se comparte

Cuando pasas un objeto o array, el método recibe una **copia de la referencia** (dirección de memoria). Ambas referencias apuntan al **mismo objeto**. Por eso, si el método modifica el contenido del objeto, el cambio sí se refleja afuera.

```java
public static void duplicarElementos(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        arr[i] *= 2;  // Modifica el array ORIGINAL a través de la referencia
    }
}

public static void main(String[] args) {
    int[] datos = {1, 2, 3, 4, 5};
    System.out.println(Arrays.toString(datos));  // [1, 2, 3, 4, 5]
    duplicarElementos(datos);
    System.out.println(Arrays.toString(datos));  // [2, 4, 6, 8, 10]
}
```

```
main                        duplicarElementos
┌──────────────┐            ┌──────────────┐
│ datos → @A1  │  copia───► │ arr → @A1    │
└──────────────┘            └──────────────┘
         │                           │
         └─────────┬─────────────────┘
                   ▼
           ┌──────────────────────┐
           │ Heap: [1, 2, 3, 4, 5]│ ← mismo objeto
           └──────────────────────┘
```

Sin embargo, si el método **reasigna** la referencia (apunta a un nuevo objeto), el cambio no se ve afuera:

```java
public static void reasignarArray(int[] arr) {
    arr = new int[]{99, 99, 99};  // arr ahora apunta a un NUEVO array
    // El array original no se ve afectado
}

public static void main(String[] args) {
    int[] datos = {1, 2, 3};
    reasignarArray(datos);
    System.out.println(Arrays.toString(datos));  // [1, 2, 3]  ← no cambió
}
```

### 6.4 El caso especial de String

Aunque `String` es un objeto (tipo de referencia), es **inmutable**. Ningún método puede modificar el contenido de un String existente; solo puede crear uno nuevo:

```java
public static void intentarModificarString(String s) {
    s = s.toUpperCase();  // Crea un NUEVO String, reasigna la copia local de la referencia
    System.out.println("Dentro: " + s);  // HOLA
}

public static void main(String[] args) {
    String saludo = "Hola";
    intentarModificarString(saludo);
    System.out.println("Afuera: " + saludo);  // Hola  ← no cambió
}
```

### 6.5 Resumen visual

```
TIPO PRIMITIVO            TIPO DE REFERENCIA (Array/Objeto)
──────────────            ────────────────────────────────
Se copia el VALOR         Se copia la REFERENCIA

main → [x = 42]           main → [datos → @A1]
             │                              │
             ↓                              ↓
método → [num = 42]        método → [arr → @A1]  ← mismo objeto
         num = 999                   arr[0] = 99  ← afecta el original
(x en main sigue = 42)    (datos[0] en main = 99)
```

---

## 7. Sobrecarga de métodos (Overloading)

La **sobrecarga** (overloading) permite definir múltiples métodos con el **mismo nombre** pero con **diferentes parámetros** (diferente número, tipo u orden). Java determina cuál versión llamar basándose en los argumentos proporcionados.

### Reglas de la sobrecarga

Para que dos métodos estén sobrecargados, deben diferir en al menos uno de:
- Número de parámetros
- Tipo de los parámetros
- Orden de los parámetros

**NO cuenta** como sobrecarga:
- Diferente tipo de retorno (sería un error de compilación)
- Diferente nombre de parámetros

### Ejemplos de sobrecarga

**Método `calcularArea` sobrecargado:**

```java
// Área de un cuadrado
public static double calcularArea(double lado) {
    return lado * lado;
}

// Área de un rectángulo
public static double calcularArea(double base, double altura) {
    return base * altura;
}

// Área de un triángulo
public static double calcularArea(double base, double altura, boolean esTriangulo) {
    return (base * altura) / 2;
}

// Área de un círculo
public static double calcularArea(String figura, double radio) {
    if (figura.equals("circulo")) {
        return Math.PI * radio * radio;
    }
    return 0;
}
```

**Llamadas: Java elige la versión correcta automáticamente:**

```java
System.out.println(calcularArea(5.0));              // Cuadrado: 25.0
System.out.println(calcularArea(4.0, 6.0));         // Rectángulo: 24.0
System.out.println(calcularArea(3.0, 4.0, true));   // Triángulo: 6.0
System.out.println(calcularArea("circulo", 3.0));   // Círculo: 28.27
```

**Método `imprimir` sobrecargado (similar a System.out.println):**

```java
public static void imprimir(int valor) {
    System.out.println("int: " + valor);
}

public static void imprimir(double valor) {
    System.out.println("double: " + valor);
}

public static void imprimir(String valor) {
    System.out.println("String: " + valor);
}

public static void imprimir(boolean valor) {
    System.out.println("boolean: " + valor);
}

public static void imprimir(int[] arr) {
    System.out.println("array: " + Arrays.toString(arr));
}
```

**Sobrecarga en constructores (adelanto del Nivel 2):**

La sobrecarga es especialmente común en constructores de clases, donde diferentes conjuntos de parámetros permiten crear objetos de distintas maneras.

### Promoción de tipos en la sobrecarga

Cuando no hay una versión exacta para el tipo del argumento, Java promueve automáticamente el tipo al siguiente compatible:

```java
public static void mostrar(long n) {
    System.out.println("long: " + n);
}

// No existe mostrar(int), pero int puede promoverse a long
mostrar(42);  // Funciona: Java convierte int → long
```

### ¿Cuándo usar sobrecarga?

La sobrecarga es útil cuando tienes variaciones de una misma operación conceptual. Evita usarla cuando los métodos hacen cosas radicalmente distintas; en ese caso, nombres diferentes son más claros.

---

## 8. Recursividad

Un método **recursivo** es un método que se llama a sí mismo. Es una técnica muy poderosa para resolver problemas que tienen una estructura naturalmente repetitiva o jerárquica.

### Estructura de un método recursivo

Todo método recursivo necesita obligatoriamente dos partes:

1. **Caso base:** la condición de parada. Sin caso base, el método se llama a sí mismo infinitamente hasta causar un `StackOverflowError`.
2. **Caso recursivo:** la llamada al mismo método con un argumento que se acerca al caso base.

```java
public static tipoRetorno metodoRecursivo(parámetros) {
    // CASO BASE: condición de parada
    if (condicionDeParada) {
        return valorBase;
    }

    // CASO RECURSIVO: llamada al mismo método acercándose al caso base
    return metodoRecursivo(parámetrosMásSimples);
}
```

### 8.1 Factorial recursivo

El factorial de n es: `n! = n × (n-1) × (n-2) × ... × 1`, con `0! = 1`.

```java
public static long factorial(int n) {
    // Caso base
    if (n == 0 || n == 1) return 1;

    // Caso recursivo: n! = n × (n-1)!
    return n * factorial(n - 1);
}
```

Traza de ejecución para `factorial(5)`:

```
factorial(5)
  └── 5 * factorial(4)
              └── 4 * factorial(3)
                          └── 3 * factorial(2)
                                      └── 2 * factorial(1)
                                                  └── 1  ← caso base

Retornando:
  factorial(1) = 1
  factorial(2) = 2 * 1 = 2
  factorial(3) = 3 * 2 = 6
  factorial(4) = 4 * 6 = 24
  factorial(5) = 5 * 24 = 120
```

### 8.2 Fibonacci recursivo

```java
public static int fibonacci(int n) {
    // Caso base
    if (n <= 0) return 0;
    if (n == 1) return 1;

    // Caso recursivo: fib(n) = fib(n-1) + fib(n-2)
    return fibonacci(n - 1) + fibonacci(n - 2);
}
```

**Advertencia:** Esta implementación es muy ineficiente para n grandes porque recalcula los mismos valores muchas veces. `fibonacci(50)` haría miles de millones de llamadas. Para valores grandes, usa la versión iterativa o memoización.

### 8.3 Suma de dígitos recursiva

```java
public static int sumaDigitos(int n) {
    n = Math.abs(n);        // Manejo de negativos
    if (n < 10) return n;  // Caso base: un solo dígito
    return n % 10 + sumaDigitos(n / 10);  // Caso recursivo
}

// sumaDigitos(12345)
// = 5 + sumaDigitos(1234)
// = 5 + 4 + sumaDigitos(123)
// = 5 + 4 + 3 + sumaDigitos(12)
// = 5 + 4 + 3 + 2 + sumaDigitos(1)
// = 5 + 4 + 3 + 2 + 1 = 15
```

### 8.4 Búsqueda binaria recursiva

```java
public static int busquedaBinaria(int[] arr, int clave, int inicio, int fin) {
    // Caso base: no se encontró
    if (inicio > fin) return -1;

    int medio = inicio + (fin - inicio) / 2;

    // Caso base: encontrado
    if (arr[medio] == clave) return medio;

    // Caso recursivo
    if (clave < arr[medio]) {
        return busquedaBinaria(arr, clave, inicio, medio - 1);  // Buscar en mitad izquierda
    } else {
        return busquedaBinaria(arr, clave, medio + 1, fin);     // Buscar en mitad derecha
    }
}

// Uso:
int[] ordenado = {1, 3, 5, 7, 9, 11, 13, 15};
int idx = busquedaBinaria(ordenado, 9, 0, ordenado.length - 1);
System.out.println("9 está en índice: " + idx);  // 4
```

### 8.5 Potencia recursiva

```java
public static double potencia(double base, int exponente) {
    if (exponente == 0) return 1;              // caso base
    if (exponente < 0) return 1.0 / potencia(base, -exponente);  // exponente negativo
    if (exponente % 2 == 0) {
        double mitad = potencia(base, exponente / 2);
        return mitad * mitad;                  // Optimización: base^n = (base^n/2)^2
    }
    return base * potencia(base, exponente - 1);
}
```

### 8.6 Recursividad vs. iteración

| Aspecto | Recursividad | Iteración |
|---------|-------------|-----------|
| Claridad | Alta para problemas jerárquicos | Alta para problemas lineales |
| Rendimiento | Puede ser lenta (overhead de llamadas) | Generalmente más rápida |
| Memoria | Usa el stack (riesgo de StackOverflow) | Usa variables locales |
| Casos de uso | Árboles, grafos, fractures, divide y vencerás | Bucles simples, procesamiento lineal |

**Regla práctica:** Si el problema se puede resolver de manera natural con iteración, úsala. Usa recursividad cuando la naturaleza del problema es jerárquica o divide-y-vencerás (árboles, grafos, algoritmos de búsqueda avanzados).

---

## 9. Ámbito de variables en métodos

El **ámbito** (scope) determina dónde es visible y utilizable una variable. En el contexto de los métodos, hay tres categorías importantes.

### 9.1 Variables locales

Las variables declaradas dentro de un método son **locales**: existen solo mientras el método se ejecuta y desaparecen cuando termina.

```java
public static int calcularDoble(int numero) {
    int resultado = numero * 2;  // 'resultado' es local a este método
    return resultado;
}  // 'resultado' y 'numero' dejan de existir aquí

public static void main(String[] args) {
    int x = 5;
    int doble = calcularDoble(x);
    // System.out.println(resultado);  // ERROR: 'resultado' no existe aquí
    System.out.println(doble);  // 10
}
```

### 9.2 Variables de clase (static)

Las variables declaradas con `static` a nivel de clase son compartidas por todos los métodos de esa clase. Persisten durante toda la ejecución del programa.

```java
public class Contador {
    static int totalLlamadas = 0;  // Variable de clase, compartida

    public static void incrementar() {
        totalLlamadas++;  // Accede a la variable de clase
    }

    public static int obtenerTotal() {
        return totalLlamadas;
    }

    public static void main(String[] args) {
        incrementar();
        incrementar();
        incrementar();
        System.out.println(obtenerTotal());  // 3
    }
}
```

### 9.3 Parámetros como variables locales

Los parámetros se comportan exactamente como variables locales: son privados al método y desaparecen cuando éste termina.

```java
public static void ejemplo(int a, String b) {
    // a y b son variables locales, inicializadas con los argumentos
    a = 100;    // Solo modifica la copia local
    b = "otro"; // Solo reasigna la referencia local
}
```

### 9.4 Shadowing (ocultamiento de variables)

Si una variable local tiene el mismo nombre que una variable de clase, la local "oculta" a la de clase dentro de ese método:

```java
public class EjemploShadow {
    static int valor = 10;  // Variable de clase

    public static void demostrar() {
        int valor = 99;  // Variable local que hace shadow a la de clase
        System.out.println(valor);       // 99  (local)
        System.out.println(EjemploShadow.valor);  // 10  (clase, acceso explícito)
    }
}
```

**Recomendación:** Evita el shadowing; usa nombres distintos para evitar confusión.

---

## 10. Métodos static vs. de instancia

Esta distinción es fundamental y es el puente directo hacia la Programación Orientada a Objetos del Nivel 2.

### 10.1 Métodos static

Un método `static` pertenece a la **clase**, no a ningún objeto específico. Puede llamarse sin crear una instancia de la clase, usando directamente el nombre de la clase.

```java
public class Matematicas {
    public static double circunferencia(double radio) {
        return 2 * Math.PI * radio;
    }

    public static int sumar(int a, int b) {
        return a + b;
    }
}

// Llamada sin crear ningún objeto:
double c = Matematicas.circunferencia(5.0);
int s = Matematicas.sumar(3, 4);
```

En el mismo archivo, si llamas a un método `static` desde otro método `static`, puedes omitir el nombre de la clase:

```java
public class Calculadora {
    public static int sumar(int a, int b) { return a + b; }
    public static int restar(int a, int b) { return a - b; }

    public static void main(String[] args) {
        System.out.println(sumar(10, 5));   // No necesitas Calculadora.sumar(...)
        System.out.println(restar(10, 5));
    }
}
```

**Limitaciones de los métodos static:**
- No pueden acceder a variables de instancia (no static).
- No pueden llamar a métodos de instancia (no static).
- No pueden usar la referencia `this`.

### 10.2 Métodos de instancia (adelanto)

Un método de instancia pertenece a un **objeto** específico. Requiere que primero crees un objeto de la clase para poder llamarlo:

```java
public class Persona {
    String nombre;   // Variable de instancia
    int edad;

    // Método de instancia: usa variables del OBJETO
    public void saludar() {
        System.out.println("Hola, soy " + nombre + " y tengo " + edad + " años.");
    }
}

// Uso:
Persona p = new Persona();  // Crear el objeto
p.nombre = "Carlos";
p.edad = 30;
p.saludar();  // Hola, soy Carlos y tengo 30 años.
```

Profundizaremos enormemente en este concepto en el Nivel 2 (Clases y Objetos).

### 10.3 ¿Cuándo usar static?

- Métodos de **utilidad** que no dependen del estado de ningún objeto (como los métodos de `Math`, `Arrays`, `String`).
- Métodos **helpers** dentro de una misma clase.
- El método `main` (siempre es static porque la JVM lo llama sin crear objetos).

---

## 11. El método main en profundidad

El método `main` es el punto de entrada de cualquier aplicación Java de consola. La JVM lo busca específicamente para comenzar la ejecución.

### La firma exacta

```java
public static void main(String[] args) {
```

- `public`: la JVM necesita acceder a él desde afuera.
- `static`: la JVM lo llama sin crear un objeto de la clase.
- `void`: no devuelve nada a la JVM.
- `main`: nombre exacto que busca la JVM.
- `String[] args`: array de argumentos de línea de comandos.

### Argumentos de línea de comandos

`args` es un array de Strings que recibe los argumentos pasados al ejecutar el programa desde la terminal:

```bash
java MiPrograma hola 42 true
```

```java
public class MiPrograma {
    public static void main(String[] args) {
        System.out.println("Número de argumentos: " + args.length);  // 3

        for (int i = 0; i < args.length; i++) {
            System.out.println("args[" + i + "] = " + args[i]);
        }
        // args[0] = hola
        // args[1] = 42
        // args[2] = true

        // Convertir argumentos a otros tipos
        if (args.length > 1) {
            int numero = Integer.parseInt(args[1]);  // "42" → 42
            System.out.println("El número al cuadrado: " + (numero * numero));
        }
    }
}
```

### Múltiples métodos main en el mismo proyecto

Cada clase puede tener su propio `main`. Al ejecutar, especificas cuál clase usar:

```bash
java ClaseA   # Ejecuta el main de ClaseA
java ClaseB   # Ejecuta el main de ClaseB
```

### main en Java 21+ (instanced main, preview)

Java 21 introduce (en preview) una forma simplificada del main:

```java
// Java 21+ preview: main de instancia
class HolaMundo {
    void main() {
        System.out.println("¡Hola Mundo!");
    }
}
```

Esto reduce la "ceremonia" para principiantes, aunque el formato tradicional sigue siendo el estándar en producción.

---

## 12. Buenas prácticas en el diseño de métodos

Escribir métodos correctamente es una habilidad que marca la diferencia entre código amateur y código profesional.

### 12.1 Principio de Responsabilidad Única (SRP)

Un método debe hacer **una sola cosa** y hacerla bien. Si el nombre del método necesita "y" para describir lo que hace, probablemente debería dividirse.

```java
// MAL: hace demasiadas cosas
public static void procesarYMostrarYGuardarDatos(int[] datos) {
    // calcula estadísticas
    // imprime en pantalla
    // guarda en archivo
    // envía por correo
}

// BIEN: cada método tiene una responsabilidad
public static double calcularPromedio(int[] datos) { ... }
public static void imprimirEstadisticas(int[] datos) { ... }
public static void guardarEnArchivo(int[] datos, String ruta) { ... }
```

### 12.2 Nombres descriptivos

El nombre del método debe decir exactamente qué hace, sin necesidad de leer el cuerpo:

```java
// MAL: ¿qué hace esto?
public static int proc(int x) { ... }
public static boolean chk(String s) { ... }
public static void doit() { ... }

// BIEN: el nombre comunica la intención
public static int calcularEdadEnMeses(int años) { ... }
public static boolean esEmailValido(String email) { ... }
public static void mostrarMenuPrincipal() { ... }
```

### 12.3 Métodos cortos

Un método idealmente cabe en una pantalla (20-30 líneas máximo). Si es más largo, considera dividirlo en submétodos.

### 12.4 Validar parámetros al inicio

Verifica los casos inválidos al principio del método y falla rápido con mensajes claros:

```java
public static double dividir(double dividendo, double divisor) {
    if (divisor == 0) {
        throw new IllegalArgumentException("El divisor no puede ser cero.");
    }
    return dividendo / divisor;
}

public static double calcularRaiz(double numero) {
    if (numero < 0) {
        throw new IllegalArgumentException("No se puede calcular raíz de número negativo.");
    }
    return Math.sqrt(numero);
}

public static String obtenerElemento(String[] arr, int indice) {
    if (arr == null) throw new IllegalArgumentException("El array no puede ser null.");
    if (indice < 0 || indice >= arr.length) throw new IndexOutOfBoundsException("Índice fuera de rango.");
    return arr[indice];
}
```

### 12.5 Evitar efectos secundarios inesperados

Un método idealmente no modifica nada fuera de su ámbito, excepto cuando eso es su propósito explícito.

```java
// MAL: el nombre sugiere que calcula, pero también imprime y modifica
public static int calcularSumaYLimpiarArray(int[] arr) {
    int suma = 0;
    for (int i = 0; i < arr.length; i++) {
        suma += arr[i];
        arr[i] = 0;         // efecto secundario inesperado
    }
    System.out.println(suma); // efecto secundario inesperado
    return suma;
}

// BIEN: separar responsabilidades
public static int calcularSuma(int[] arr) {
    int suma = 0;
    for (int n : arr) suma += n;
    return suma;
}
```

### 12.6 Comentarios Javadoc en métodos públicos

```java
/**
 * Calcula el índice de masa corporal (IMC).
 *
 * @param pesoKg   El peso en kilogramos (debe ser > 0)
 * @param alturaM  La altura en metros (debe ser > 0)
 * @return         El valor del IMC como número decimal
 * @throws IllegalArgumentException si peso o altura son <= 0
 */
public static double calcularIMC(double pesoKg, double alturaM) {
    if (pesoKg <= 0 || alturaM <= 0) {
        throw new IllegalArgumentException("Peso y altura deben ser positivos.");
    }
    return pesoKg / (alturaM * alturaM);
}
```

### 12.7 Número de parámetros

Un método con más de 4–5 parámetros es difícil de usar y mantener. Considera agrupar parámetros relacionados en un objeto.

```java
// MAL: demasiados parámetros
public static void registrarPersona(String nombre, String apellido,
    int edad, String email, String telefono, String ciudad, String país) { ... }

// BIEN: usar un objeto (anticipando el Nivel 2)
// Persona persona = new Persona(...);
// registrarPersona(persona);
```

---

## 13. Errores comunes con métodos

### Error 1: Missing return statement

```java
// ERROR: no todos los caminos retornan un valor
public static String clasificar(int n) {
    if (n > 0) return "Positivo";
    if (n < 0) return "Negativo";
    // El compilador no garantiza que n sea 0 aquí — pide return
}

// CORRECTO:
public static String clasificar(int n) {
    if (n > 0) return "Positivo";
    if (n < 0) return "Negativo";
    return "Cero";  // Cubre el caso restante
}
```

### Error 2: Llamar a método de instancia desde contexto static

```java
public class Ejemplo {
    int valor = 10;  // Variable de instancia

    public int obtenerValor() {  // Método de instancia
        return valor;
    }

    public static void main(String[] args) {
        // ERROR: no puedes llamar método de instancia desde static sin objeto
        System.out.println(obtenerValor());

        // CORRECTO: crear un objeto primero
        Ejemplo obj = new Ejemplo();
        System.out.println(obj.obtenerValor());
    }
}
```

### Error 3: Sobrecarga con solo diferente tipo de retorno

```java
// ERROR: esto NO es sobrecarga válida, es error de compilación
public static int calcular(int a) { return a * 2; }
public static double calcular(int a) { return a * 2.0; }  // misma firma, diferente retorno
```

### Error 4: StackOverflowError por falta de caso base

```java
// ERROR: recursividad infinita
public static int factorial(int n) {
    return n * factorial(n - 1);  // Nunca se detiene
}

// CORRECTO: siempre incluir el caso base
public static int factorial(int n) {
    if (n <= 1) return 1;  // Caso base
    return n * factorial(n - 1);
}
```

### Error 5: Esperar que un método modifique una variable primitiva

```java
public static void incrementar(int x) {
    x++;  // Solo modifica la copia local
}

int contador = 0;
incrementar(contador);
System.out.println(contador);  // 0  ← no cambió

// CORRECTO: retornar el nuevo valor
public static int incrementar(int x) {
    return x + 1;
}
contador = incrementar(contador);  // ahora sí se actualiza
```

### Error 6: Código inalcanzable después de return

```java
public static int ejemplo() {
    return 42;
    System.out.println("Nunca llego aquí");  // Error: unreachable statement
}
```

### Error 7: Confundir parámetros con variables de clase

```java
public class Ejemplo {
    static int total = 0;

    public static void agregar(int total) {  // El parámetro oculta la variable de clase
        total += 10;  // Modifica el parámetro local, NO la variable de clase
    }

    // CORRECTO: usar nombre diferente o acceso explícito
    public static void agregarAlTotal(int cantidad) {
        total += cantidad;  // Modifica la variable de clase
    }
}
```

---

## 14. Ejercicios prácticos

### Ejercicio 1 — Biblioteca de matemáticas (Nivel: Básico)

Crea `BibliotecaMatematicas.java` con los siguientes métodos estáticos y un `main` que los pruebe todos:

1. `int sumar(int a, int b)` — suma dos enteros
2. `double potencia(double base, int exponente)` — base elevada a exponente (sin usar `Math.pow`)
3. `boolean esDivisible(int numero, int divisor)` — si número es divisible entre divisor
4. `int maximoDeTres(int a, int b, int c)` — retorna el mayor de tres números
5. `double hipotenusa(double cateto1, double cateto2)` — usando el teorema de Pitágoras
6. `boolean esNumeroPerfecto(int n)` — un número es perfecto si la suma de sus divisores propios es igual a sí mismo (ej: 6 = 1+2+3)

---

### Ejercicio 2 — Sobrecarga de imprimir (Nivel: Básico-Medio)

Crea `ImpresoraSobrecargada.java` con versiones sobrecargadas del método `formatear` que acepten:

1. `formatear(String titulo)` — devuelve el título centrado entre `===`
2. `formatear(String titulo, int ancho)` — centrado con el ancho especificado
3. `formatear(String titulo, char relleno)` — centrado con el carácter de relleno dado
4. `formatear(String etiqueta, int valor)` — devuelve `"etiqueta: valor"`
5. `formatear(String etiqueta, double valor, int decimales)` — con precisión decimal

Demuestra en `main` que Java llama a la versión correcta según los argumentos.

---

### Ejercicio 3 — Validaciones (Nivel: Medio)

Crea `Validaciones.java` con métodos que retornen `boolean`:

1. `esEnteroPositivo(String s)` — si el String representa un entero positivo
2. `esEmailValido(String email)` — formato básico con `@` y un punto en el dominio
3. `esPasswordSeguro(String pwd)` — mínimo 8 caracteres, al menos 1 mayúscula y 1 dígito
4. `esPalindromo(String s)` — si el String es palíndromo (igual al derecho que al revés, ignorando espacios y mayúsculas)
5. `estaDentroDelRango(int valor, int min, int max)` — si valor está en [min, max]

Prueba cada método con casos válidos e inválidos.

---

### Ejercicio 4 — Recursividad (Nivel: Medio)

Crea `EjerciciosRecursivos.java` e implementa de forma recursiva:

1. `int sumaHasta(int n)` — suma de 1 + 2 + 3 + ... + n
2. `int contarDigitos(int n)` — número de dígitos de un entero
3. `boolean esPalindromo(String s)` — de forma recursiva (compara primer y último carácter)
4. `int mcd(int a, int b)` — Máximo Común Divisor usando el algoritmo de Euclides: `mcd(a, b) = mcd(b, a % b)`, con caso base `mcd(a, 0) = a`
5. `void imprimirBinario(int n)` — imprime la representación binaria de n de forma recursiva

Muestra la traza de al menos uno de los métodos con comentarios.

---

### Ejercicio 5 — Manipulador de arrays (Nivel: Medio)

Crea `ManipuladorArrays.java` con métodos que trabajen sobre arrays:

1. `int[] fusionar(int[] a, int[] b)` — combina dos arrays en uno nuevo
2. `int[] eliminarDuplicados(int[] arr)` — devuelve array sin elementos repetidos
3. `int[] intersecion(int[] a, int[] b)` — elementos que aparecen en ambos arrays
4. `boolean estaOrdenado(int[] arr)` — si el array está ordenado ascendentemente
5. `int[] ordenarSinSort(int[] arr)` — ordena el array usando cualquier algoritmo manual (sin `Arrays.sort`)

---

### Ejercicio 6 — Calculadora científica con menú (Nivel: Avanzado)

Crea `CalculadoraCientifica.java` que combine todo lo aprendido en el Nivel 1. Debe tener:

**Métodos de cálculo (todos estáticos, con validación de parámetros):**
- `double seno(double angulGrados)` — seno de un ángulo en grados
- `double coseno(double anguloGrados)` — coseno
- `double logaritmo(double numero)` — logaritmo natural (validar que número > 0)
- `double logaritmoBase10(double numero)` — log base 10
- `double raizCuadrada(double numero)` — validar que número >= 0
- `long combinaciones(int n, int k)` — C(n,k) = n! / (k! × (n-k)!) usando el método factorial recursivo

**Menú interactivo** (con `Scanner` y `do-while`):
- Muestra un menú numerado con todas las operaciones
- Lee la opción del usuario con `switch expression`
- Lee los parámetros necesarios según la operación elegida
- Muestra el resultado formateado con `printf`
- Repite hasta que el usuario elija "Salir"
- Maneja errores de entrada inválida con mensajes claros

---

## 15. Resumen del tema y del Nivel 1

### Métodos en una página

```java
// Método void (sin retorno)
public static void saludar(String nombre) {
    System.out.println("Hola, " + nombre);
}

// Método con retorno
public static int sumar(int a, int b) {
    return a + b;
}

// Sobrecarga: mismo nombre, diferentes parámetros
public static double sumar(double a, double b) {
    return a + b;
}

// Varargs: número variable de argumentos
public static int sumarTodos(int... nums) {
    int total = 0;
    for (int n : nums) total += n;
    return total;
}

// Recursivo: se llama a sí mismo
public static int factorial(int n) {
    if (n <= 1) return 1;        // caso base
    return n * factorial(n - 1); // caso recursivo
}

// Llamadas
saludar("Ana");                  // void: no devuelve nada
int r = sumar(3, 4);             // guarda el retorno
System.out.println(sumar(3, 4)); // usa el retorno directamente
System.out.println(sumarTodos(1, 2, 3, 4, 5)); // varargs
```

### Reglas de oro de los métodos

| Situación | Regla |
|-----------|-------|
| Nombre | Verbo en camelCase: `calcular`, `obtener`, `validar`, `imprimir` |
| Tamaño | Máximo 20–30 líneas; si es mayor, dividir en submétodos |
| Responsabilidad | Una sola tarea por método (principio SRP) |
| Parámetros | Máximo 4–5; si son más, agrupar en un objeto |
| Validación | Verificar parámetros al inicio, fallar rápido |
| Recursividad | Siempre incluir el caso base para evitar StackOverflow |
| Primitivos | Se pasan por valor; los cambios dentro no afectan el original |
| Objetos/arrays | La referencia se pasa por valor; el contenido sí puede modificarse |
| Sobrecarga | Misma operación conceptual, diferentes tipos/cantidad de parámetros |
| static | Para utilidades que no dependen del estado de ningún objeto |

---

### Resumen completo del Nivel 1: Fundamentos

¡Felicidades! Has completado el **Nivel 1** del curso. Estos son todos los conocimientos que dominaste:

| Tema | Conceptos clave |
|------|-----------------|
| **T1: Introducción** | JVM, JRE, JDK, bytecode, compilación, Hola Mundo, comentarios |
| **T2: Variables** | 8 tipos primitivos, String, constantes, var, casting, autoboxing |
| **T3: Operadores** | Aritméticos, asignación, incremento, comparación, lógicos, ternario, bitwise |
| **T4: Estructuras de control** | if/else, switch, while, do-while, for, for-each, break, continue, anidados |
| **T5: Arrays y Strings** | Arrays 1D y 2D, clase Arrays, StringBuilder, regex, métodos de String |
| **T6: Métodos** | Declaración, void, retorno, parámetros, paso por valor/referencia, sobrecarga, recursividad |

---

## ¿Qué sigue? — Nivel 2: Programación Orientada a Objetos

El **Nivel 2** es el corazón de Java. Con las bases sólidas del Nivel 1, aprenderás:

| Tema | Contenido |
|------|-----------|
| **T7: Clases y Objetos** | Declarar clases, atributos, constructores, instanciación con `new`, `this` |
| **T8: Encapsulamiento** | `private`, `public`, getters y setters, diseño de APIs seguras |
| **T9: Herencia** | `extends`, `super`, sobreescritura (`@Override`), jerarquía de clases |
| **T10: Polimorfismo** | Polimorfismo estático y dinámico, binding, upcasting, downcasting |
| **T11: Interfaces y clases abstractas** | `abstract`, `interface`, implementación múltiple, contratos |
| **T12: Manejo de excepciones** | `try/catch/finally`, `throws`, excepciones personalizadas, jerarquía de errores |

---

*Curso de Java — Nivel 1: Fundamentos*
*Tema 6 de 6 — ¡Nivel 1 completado!*
*Creado con fines educativos*
