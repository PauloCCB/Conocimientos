# Curso de Java — Nivel 1: Fundamentos
## Tema 3: Operadores

> **Nivel:** Principiante | **Prerrequisitos:** Temas 1 y 2 completados | **Duración estimada:** 3–4 horas

---

## Tabla de contenidos

1. [¿Qué es un operador?](#1-qué-es-un-operador)
2. [Operadores aritméticos](#2-operadores-aritméticos)
3. [Operadores de asignación](#3-operadores-de-asignación)
4. [Operadores de incremento y decremento](#4-operadores-de-incremento-y-decremento)
5. [Operadores de comparación (relacionales)](#5-operadores-de-comparación-relacionales)
6. [Operadores lógicos](#6-operadores-lógicos)
7. [Operador ternario](#7-operador-ternario)
8. [Operadores a nivel de bits (Bitwise)](#8-operadores-a-nivel-de-bits-bitwise)
9. [Operador instanceof](#9-operador-instanceof)
10. [Precedencia y asociatividad de operadores](#10-precedencia-y-asociatividad-de-operadores)
11. [Errores comunes con operadores](#11-errores-comunes-con-operadores)
12. [Ejercicios prácticos](#12-ejercicios-prácticos)
13. [Resumen del tema](#13-resumen-del-tema)

---

## 1. ¿Qué es un operador?

Un **operador** es un símbolo especial que le indica a Java que realice una operación sobre uno o más valores. Los valores sobre los que actúa un operador se llaman **operandos**.

```
  10    +    5    =    15
  ↑          ↑          ↑
operando  operador   resultado
```

### Clasificación por cantidad de operandos

- **Unarios:** actúan sobre un solo operando. Ejemplo: `-x`, `!flag`, `++i`
- **Binarios:** actúan sobre dos operandos. Ejemplo: `a + b`, `x > y`
- **Ternario:** actúa sobre tres operandos. Ejemplo: `condicion ? valorA : valorB`

Java tiene operadores para realizar matemáticas, comparar valores, combinar condiciones lógicas, manipular bits y mucho más. En este tema los cubriremos todos con detalle.

---

## 2. Operadores aritméticos

Los operadores aritméticos realizan las operaciones matemáticas básicas. Funcionan con todos los tipos numéricos: `byte`, `short`, `int`, `long`, `float` y `double`.

### Tabla de operadores aritméticos

| Operador | Nombre | Ejemplo | Resultado |
|----------|--------|---------|-----------|
| `+` | Suma | `10 + 3` | `13` |
| `-` | Resta | `10 - 3` | `7` |
| `*` | Multiplicación | `10 * 3` | `30` |
| `/` | División | `10 / 3` | `3` |
| `%` | Módulo (resto) | `10 % 3` | `1` |

### 2.1 Suma, resta y multiplicación

Funcionan exactamente como en matemáticas:

```java
int a = 15;
int b = 4;

System.out.println(a + b);  // 19
System.out.println(a - b);  // 11
System.out.println(a * b);  // 60

// Con decimales
double x = 7.5;
double y = 2.0;
System.out.println(x + y);  // 9.5
System.out.println(x - y);  // 5.5
System.out.println(x * y);  // 15.0
```

### 2.2 División — Entera vs. Decimal

Este es uno de los conceptos que más confunde a los principiantes. El resultado de la división depende del **tipo de los operandos**:

**División entera (ambos operandos son enteros):**

```java
int a = 10;
int b = 3;
int resultado = a / b;
System.out.println(resultado);  // 3  ← no 3.33, trunca la parte decimal
```

Cuando divides dos enteros, Java descarta la parte decimal y devuelve solo la parte entera. No redondea, simplemente trunca.

```java
System.out.println(7 / 2);    // 3  (no 3.5)
System.out.println(1 / 4);    // 0  (no 0.25)
System.out.println(9 / 3);    // 3  (exacto, sin problema)
System.out.println(-7 / 2);   // -3 (trunca hacia cero, no hacia el menos infinito)
```

**División decimal (al menos un operando es double o float):**

```java
double resultado1 = 10.0 / 3;     // 3.3333333333333335
double resultado2 = 10 / 3.0;     // 3.3333333333333335
double resultado3 = (double) 10 / 3;  // 3.3333333333333335  ← cast explícito

// TRAMPA COMÚN: esto sigue siendo división entera
double trampa = 10 / 3;           // 3.0  ← primero divide como enteros (=3), luego convierte a double
```

**Cómo forzar división decimal entre enteros:**

```java
int a = 10;
int b = 3;

// Opción 1: cast a double
double resultado = (double) a / b;       // 3.3333...

// Opción 2: multiplicar por 1.0
double resultado2 = 1.0 * a / b;        // 3.3333...

// Opción 3: declarar uno como double
double ad = a;
double resultado3 = ad / b;             // 3.3333...
```

**División por cero:**

```java
// División entera por cero → lanza ArithmeticException (error en tiempo de ejecución)
int error = 10 / 0;  // Exception in thread "main" java.lang.ArithmeticException: / by zero

// División decimal por cero → no lanza error, devuelve Infinity o NaN
double infinito = 10.0 / 0.0;   // Infinity
double negInfinito = -10.0 / 0.0; // -Infinity
double nanValor = 0.0 / 0.0;     // NaN (Not a Number)

System.out.println(Double.isInfinite(infinito));  // true
System.out.println(Double.isNaN(nanValor));        // true
```

### 2.3 Módulo (%) — El resto de la división

El operador módulo devuelve el **resto** que queda después de una división entera. Es extremadamente útil en programación.

```java
System.out.println(10 % 3);   // 1  (10 = 3×3 + 1)
System.out.println(15 % 5);   // 0  (15 = 5×3 + 0, divisible exactamente)
System.out.println(7  % 2);   // 1  (7 = 2×3 + 1)
System.out.println(4  % 7);   // 4  (4 = 7×0 + 4, cuando dividendo < divisor)
System.out.println(-7 % 3);   // -1 (el signo sigue al dividendo en Java)
```

**Usos prácticos del módulo:**

```java
// 1. Verificar si un número es par o impar
int numero = 17;
if (numero % 2 == 0) {
    System.out.println("Es par");
} else {
    System.out.println("Es impar");  // Se imprime esto
}

// 2. Verificar si un número es divisible por otro
int n = 100;
if (n % 25 == 0) {
    System.out.println(n + " es divisible por 25");  // Se imprime esto
}

// 3. Obtener el último dígito de un número
int numero2 = 12345;
int ultimoDigito = numero2 % 10;  // 5

// 4. Limitar un índice para que dé la vuelta (circular)
int posicion = 0;
int tamañoArray = 5;
posicion = (posicion + 1) % tamañoArray;  // 0→1→2→3→4→0→1...

// 5. Convertir segundos a horas, minutos y segundos
int totalSegundos = 3723;
int horas   = totalSegundos / 3600;        // 1
int minutos = (totalSegundos % 3600) / 60; // 2
int segundos = totalSegundos % 60;         // 3
System.out.printf("%dh %dm %ds%n", horas, minutos, segundos); // 1h 2m 3s
```

### 2.4 El operador + con Strings (concatenación)

Cuando al menos uno de los operandos es un `String`, el operador `+` actúa como concatenador, no como suma:

```java
String s = "Resultado: ";
int n = 42;
System.out.println(s + n);          // "Resultado: 42"

// Cuidado con el orden de evaluación:
System.out.println("Suma: " + 1 + 2);   // "Suma: 12"  ← concatena izquierda a derecha
System.out.println("Suma: " + (1 + 2)); // "Suma: 3"   ← paréntesis fuerzan la suma primero
System.out.println(1 + 2 + " es el resultado"); // "3 es el resultado" ← suma primero, luego concatena
```

### 2.5 Tipos del resultado en operaciones mixtas

Cuando mezclas tipos numéricos en una operación, Java aplica **promoción de tipos**: el resultado adopta el tipo más grande involucrado.

```java
// int + int = int
int r1 = 5 + 3;             // int

// int + long = long
long r2 = 5 + 3L;           // long

// int + double = double
double r3 = 5 + 3.0;        // double

// byte + byte = int  (¡inesperado!)
byte b1 = 10, b2 = 20;
// byte suma = b1 + b2;      // ERROR: el resultado de byte+byte es int
int suma = b1 + b2;          // Correcto: 30
```

---

## 3. Operadores de asignación

El operador de asignación básico es `=`. Asigna el valor de la derecha a la variable de la izquierda. Pero Java también ofrece **operadores de asignación compuesta** que combinan una operación aritmética con la asignación.

### 3.1 Asignación simple

```java
int x = 10;        // Asigna 10 a x
x = x + 5;        // Evalúa x+5 (=15) y lo asigna de vuelta a x
System.out.println(x); // 15
```

### 3.2 Operadores de asignación compuesta

Estos operadores son atajos que combinan una operación con la asignación. Hacen que el código sea más conciso.

| Operador | Equivalente a | Ejemplo | Resultado si x=10 |
|----------|--------------|---------|-------------------|
| `+=` | `x = x + n` | `x += 5` | `x` vale 15 |
| `-=` | `x = x - n` | `x -= 3` | `x` vale 7 |
| `*=` | `x = x * n` | `x *= 2` | `x` vale 20 |
| `/=` | `x = x / n` | `x /= 4` | `x` vale 2 |
| `%=` | `x = x % n` | `x %= 3` | `x` vale 1 |

```java
int puntos = 100;

puntos += 50;   // puntos = 100 + 50 = 150
puntos -= 20;   // puntos = 150 - 20 = 130
puntos *= 2;    // puntos = 130 * 2  = 260
puntos /= 4;    // puntos = 260 / 4  = 65
puntos %= 10;   // puntos = 65  % 10 = 5

System.out.println(puntos); // 5
```

### 3.3 Uso con Strings

El operador `+=` también funciona para concatenar Strings:

```java
String mensaje = "Hola";
mensaje += " Mundo";     // mensaje = "Hola" + " Mundo" = "Hola Mundo"
mensaje += "!";          // mensaje = "Hola Mundo" + "!" = "Hola Mundo!"
System.out.println(mensaje); // Hola Mundo!
```

### 3.4 Asignación en cadena

Java permite asignar el mismo valor a múltiples variables en una sola línea (se evalúa de derecha a izquierda):

```java
int a, b, c;
a = b = c = 0;  // c=0, luego b=c=0, luego a=b=c=0
System.out.println(a + " " + b + " " + c); // 0 0 0
```

---

## 4. Operadores de incremento y decremento

Son operadores unarios que aumentan (`++`) o disminuyen (`--`) el valor de una variable numérica en exactamente 1.

### 4.1 Incremento ++

```java
int x = 5;
x++;  // Equivale a: x = x + 1
System.out.println(x); // 6
```

### 4.2 Decremento --

```java
int y = 5;
y--;  // Equivale a: y = y - 1
System.out.println(y); // 4
```

### 4.3 Prefijo vs. Postfijo — La diferencia crucial

El operador puede colocarse **antes** (prefijo) o **después** (postfijo) de la variable. La diferencia solo importa cuando se usa en una expresión más grande:

**Postfijo (`x++`, `x--`):** Devuelve el valor **actual** de x y *después* lo modifica.

**Prefijo (`++x`, `--x`):** Modifica x *primero* y luego devuelve el **nuevo** valor.

```java
int a = 5;
int b = a++;  // b recibe el valor ACTUAL de a (5), luego a se incrementa
System.out.println("a = " + a); // a = 6
System.out.println("b = " + b); // b = 5

int c = 5;
int d = ++c;  // c se incrementa PRIMERO (6), luego d recibe el nuevo valor
System.out.println("c = " + c); // c = 6
System.out.println("d = " + d); // d = 6
```

Otro ejemplo para dejarlo claro:

```java
int x = 10;

// Postfijo: usa 10, luego incrementa
System.out.println(x++);  // Imprime 10, x ahora es 11
System.out.println(x);    // Imprime 11

// Prefijo: incrementa primero, luego usa el nuevo valor
System.out.println(++x);  // Incrementa a 12, imprime 12
System.out.println(x);    // Imprime 12
```

### 4.4 Uso en bucles

El uso más común de `++` y `--` es en los bucles `for`, donde se usa para avanzar el contador:

```java
// Conteo ascendente
for (int i = 0; i < 5; i++) {
    System.out.print(i + " "); // 0 1 2 3 4
}

// Conteo descendente
for (int i = 5; i > 0; i--) {
    System.out.print(i + " "); // 5 4 3 2 1
}
```

En los bucles `for`, `i++` y `++i` son equivalentes porque el valor devuelto no se usa en ninguna expresión más amplia.

---

## 5. Operadores de comparación (relacionales)

Los operadores de comparación comparan dos valores y devuelven siempre un resultado **booleano**: `true` o `false`. Son la base de toda toma de decisiones en programación.

### Tabla de operadores de comparación

| Operador | Significado | Ejemplo | Resultado |
|----------|-------------|---------|-----------|
| `==` | Igual a | `5 == 5` | `true` |
| `!=` | Diferente de | `5 != 3` | `true` |
| `>` | Mayor que | `5 > 3` | `true` |
| `<` | Menor que | `5 < 3` | `false` |
| `>=` | Mayor o igual que | `5 >= 5` | `true` |
| `<=` | Menor o igual que | `3 <= 5` | `true` |

```java
int a = 10;
int b = 20;

System.out.println(a == b);   // false
System.out.println(a != b);   // true
System.out.println(a > b);    // false
System.out.println(a < b);    // true
System.out.println(a >= 10);  // true
System.out.println(b <= 20);  // true
```

### 5.1 Guardar el resultado en una variable boolean

El resultado de una comparación es un `boolean`, que puedes guardar en una variable:

```java
int edad = 20;
boolean esMayorDeEdad = edad >= 18;   // true
boolean puedeJubilar  = edad >= 65;   // false
boolean esExactamente20 = edad == 20; // true

System.out.println(esMayorDeEdad);    // true
System.out.println(puedeJubilar);     // false
```

### 5.2 Comparar tipos distintos

```java
// Comparar int con double: Java hace la conversión automáticamente
int entero = 5;
double decimal = 5.0;
System.out.println(entero == decimal);  // true (5 == 5.0 → true)

// Comparar char con int
char letra = 'A';  // código ASCII 65
System.out.println(letra == 65);  // true
System.out.println(letra > 'B');  // false (65 > 66 → false)
```

### 5.3 NO usar == para comparar Strings

Como vimos en el Tema 2, `==` compara referencias de objetos, no contenido:

```java
String s1 = "Java";
String s2 = "Java";
String s3 = new String("Java");

System.out.println(s1 == s2);       // true  (mismo objeto en el String Pool)
System.out.println(s1 == s3);       // false (s3 es un nuevo objeto en el heap)
System.out.println(s1.equals(s3));  // true  (mismo contenido)
```

**Regla de oro:** Para Strings (y cualquier objeto), siempre usa `.equals()`.

---

## 6. Operadores lógicos

Los operadores lógicos trabajan con valores booleanos y permiten combinar múltiples condiciones en una sola expresión. Son fundamentales para escribir condiciones complejas.

### Tabla de operadores lógicos

| Operador | Nombre | Descripción |
|----------|--------|-------------|
| `&&` | AND lógico | `true` solo si **ambos** operandos son `true` |
| `\|\|` | OR lógico | `true` si **al menos uno** de los operandos es `true` |
| `!` | NOT lógico | Invierte el valor booleano |
| `&` | AND sin cortocircuito | Como `&&` pero evalúa siempre ambos lados |
| `\|` | OR sin cortocircuito | Como `\|\|` pero evalúa siempre ambos lados |
| `^` | XOR lógico | `true` si los operandos son **distintos** entre sí |

### 6.1 AND lógico (&&)

Devuelve `true` únicamente cuando **ambas** condiciones son verdaderas.

**Tabla de verdad de &&:**

| A | B | A && B |
|---|---|--------|
| true | true | true |
| true | false | false |
| false | true | false |
| false | false | false |

```java
int edad = 25;
double salario = 3000.0;

boolean puedeAccederAlPrestamo = edad >= 18 && salario >= 1500.0;
System.out.println(puedeAccederAlPrestamo); // true

boolean cumpleAmbas = edad > 30 && salario > 2000.0;
System.out.println(cumpleAmbas); // false  (la primera condición es false)
```

### 6.2 OR lógico (||)

Devuelve `true` cuando **al menos una** de las condiciones es verdadera.

**Tabla de verdad de ||:**

| A | B | A \|\| B |
|---|---|---------|
| true | true | true |
| true | false | true |
| false | true | true |
| false | false | false |

```java
boolean esEstudiante = true;
boolean esTrabajador = false;

boolean tieneDescuento = esEstudiante || esTrabajador;
System.out.println(tieneDescuento); // true  (al menos una es true)

boolean esFinDeSemana = false;
boolean esFeriado = false;
boolean diaLibre = esFinDeSemana || esFeriado;
System.out.println(diaLibre); // false  (ninguna es true)
```

### 6.3 NOT lógico (!)

Invierte el valor booleano: convierte `true` en `false` y viceversa.

```java
boolean activo = true;
System.out.println(!activo);    // false

boolean esMenor = false;
System.out.println(!esMenor);   // true

int edad = 15;
boolean esAdulto = edad >= 18;
System.out.println(!esAdulto);  // true  (NO es adulto)
```

### 6.4 XOR lógico (^)

Devuelve `true` solo cuando los dos operandos tienen **valores distintos**.

```java
System.out.println(true  ^ true);   // false  (iguales)
System.out.println(true  ^ false);  // true   (distintos)
System.out.println(false ^ true);   // true   (distintos)
System.out.println(false ^ false);  // false  (iguales)

// Uso: verificar que exactamente una condición sea verdadera
boolean tieneVisa = true;
boolean tienePasaporte = true;
boolean soloUnoDeLosDos = tieneVisa ^ tienePasaporte;
System.out.println(soloUnoDeLosDos); // false (ambos son true, no son distintos)
```

### 6.5 Cortocircuito (Short-Circuit Evaluation)

`&&` y `||` usan evaluación de **cortocircuito**: si el resultado ya puede determinarse con el primer operando, el segundo ni siquiera se evalúa.

```java
// Con &&: si el primero es false, el segundo no se evalúa
int x = 0;
boolean resultado = (x != 0) && (10 / x > 2); // Si evaluara el segundo → división por cero
// Pero como (x != 0) es false, Java ni evalúa (10/x > 2)
// Por eso NO lanza ArithmeticException
System.out.println(resultado); // false  (sin error)

// Con ||: si el primero es true, el segundo no se evalúa
String nombre = null;
boolean esValido = (nombre != null) || (nombre.length() > 0); // Si evaluara el segundo → NullPointerException
// Como (nombre != null) es false... espera, aquí sí evaluaría el segundo
// Pero si fuera: nombre = "Java"
String nombre2 = "Java";
boolean esValido2 = (nombre2 != null) || (nombre2.length() > 0);
// Como (nombre2 != null) es true, el || hace cortocircuito y no evalúa el segundo
System.out.println(esValido2); // true  (sin error)
```

**Uso importante del cortocircuito para evitar NullPointerException:**

```java
String texto = null;

// INCORRECTO: puede lanzar NullPointerException
if (texto.length() > 0) { ... }

// CORRECTO: el cortocircuito protege del NPE
if (texto != null && texto.length() > 0) { ... }
// Si texto es null, la primera condición es false y Java no evalúa texto.length()
```

### 6.6 Combinar operadores lógicos

Puedes encadenar múltiples condiciones. Usa paréntesis para hacer explícita la precedencia:

```java
int edad = 22;
double salario = 2500.0;
boolean tieneTrabajo = true;
boolean tieneDeudas = false;

// Sin paréntesis (&&  tiene mayor precedencia que ||)
boolean resultado1 = edad >= 18 && salario >= 1000 || tieneTrabajo;

// Con paréntesis explícitos (más claro y seguro)
boolean resultado2 = (edad >= 18 && salario >= 1000) || tieneTrabajo;
boolean resultado3 = edad >= 18 && (salario >= 1000 || tieneTrabajo);
// resultado2 y resultado3 pueden dar valores diferentes

// Condición compleja bien estructurada
boolean puedeObtenerPrestamo = (edad >= 18 && edad <= 70)
                             && (salario >= 1500.0)
                             && tieneTrabajo
                             && !tieneDeudas;
System.out.println(puedeObtenerPrestamo); // true
```

---

## 7. Operador ternario

El operador ternario es el único operador de Java que trabaja con **tres operandos**. Es una forma compacta de escribir una expresión `if-else` simple que devuelve un valor.

### Sintaxis

```
variable = condicion ? valorSiTrue : valorSiFalse;
```

Se lee así: "Si la condición es verdadera, usa `valorSiTrue`; de lo contrario, usa `valorSiFalse`."

### Ejemplos básicos

```java
int edad = 20;
String categoria = edad >= 18 ? "Adulto" : "Menor";
System.out.println(categoria); // "Adulto"

int a = 15, b = 8;
int mayor = a > b ? a : b;
System.out.println(mayor); // 15

double precio = 100.0;
double precioFinal = precio > 50.0 ? precio * 0.9 : precio; // 10% descuento si > 50
System.out.println(precioFinal); // 90.0
```

### Equivalencia con if-else

```java
// Con if-else (3 líneas):
String mensaje;
if (temperatura > 30) {
    mensaje = "Hace calor";
} else {
    mensaje = "Temperatura agradable";
}

// Con operador ternario (1 línea):
String mensaje = temperatura > 30 ? "Hace calor" : "Temperatura agradable";
```

### Ternarios anidados (úsalos con cuidado)

Puedes anidar operadores ternarios, pero con más de dos niveles el código se vuelve difícil de leer:

```java
int nota = 75;

// Ternario anidado (funciona pero puede ser difícil de leer)
String calificacion = nota >= 90 ? "Sobresaliente"
                    : nota >= 70 ? "Notable"
                    : nota >= 50 ? "Aprobado"
                    : "Suspendido";
System.out.println(calificacion); // "Notable"
```

Para más de dos o tres casos, es mejor usar `if-else if` o `switch` (Tema 4).

### Casos de uso apropiados para el ternario

```java
// 1. Asignación condicional
int absValue = numero >= 0 ? numero : -numero;

// 2. Impresión condicional
System.out.println("El número es " + (numero % 2 == 0 ? "par" : "impar"));

// 3. Valor por defecto
String nombre = (input != null) ? input : "Sin nombre";

// 4. Selección de mensaje
String estado = activo ? "Activo" : "Inactivo";
```

---

## 8. Operadores a nivel de bits (Bitwise)

Los operadores bitwise trabajan directamente sobre los bits de los valores enteros. Son operaciones de bajo nivel muy útiles en programación de sistemas, redes, criptografía y optimización de rendimiento.

Para entenderlos necesitas saber que los números enteros en Java se representan en binario (base 2):

```
Decimal  →  Binario (8 bits)
  0      →  00000000
  1      →  00000001
  5      →  00000101
 10      →  00001010
 15      →  00001111
255      →  11111111
```

### Tabla de operadores bitwise

| Operador | Nombre | Descripción |
|----------|--------|-------------|
| `&` | AND bit a bit | 1 solo si ambos bits son 1 |
| `\|` | OR bit a bit | 1 si al menos un bit es 1 |
| `^` | XOR bit a bit | 1 si los bits son distintos |
| `~` | NOT bit a bit | Invierte todos los bits |
| `<<` | Desplazamiento izquierda | Multiplica por 2^n |
| `>>` | Desplazamiento derecha | Divide por 2^n (conserva signo) |
| `>>>` | Desplazamiento derecha sin signo | Divide por 2^n (rellena con 0) |

### 8.1 AND, OR y XOR bit a bit

```java
int a = 5;   // 0101 en binario
int b = 3;   // 0011 en binario

System.out.println(a & b);   // 0001 = 1  (AND: 1 solo donde ambos son 1)
System.out.println(a | b);   // 0111 = 7  (OR:  1 donde al menos uno es 1)
System.out.println(a ^ b);   // 0110 = 6  (XOR: 1 donde son distintos)
System.out.println(~a);      // ...11111010 = -6 (NOT: invierte todos los bits)
```

Visualización:
```
  a = 0 1 0 1  (5)
  b = 0 0 1 1  (3)
      -------
a&b = 0 0 0 1  (1)  ← AND
a|b = 0 1 1 1  (7)  ← OR
a^b = 0 1 1 0  (6)  ← XOR
```

### 8.2 Desplazamiento de bits

```java
int n = 1;

// Desplazamiento a la izquierda: equivale a multiplicar por 2^n
System.out.println(n << 1);  // 2  (1 × 2^1 = 2)
System.out.println(n << 2);  // 4  (1 × 2^2 = 4)
System.out.println(n << 3);  // 8  (1 × 2^3 = 8)
System.out.println(1 << 8);  // 256

// Desplazamiento a la derecha: equivale a dividir por 2^n
int m = 16;
System.out.println(m >> 1);  // 8   (16 / 2^1 = 8)
System.out.println(m >> 2);  // 4   (16 / 2^2 = 4)
System.out.println(m >> 3);  // 2   (16 / 2^3 = 2)
```

### 8.3 Usos prácticos de los operadores bitwise

```java
// 1. Verificar si un número es par (bit menos significativo)
int numero = 7;
boolean esPar = (numero & 1) == 0;  // false (7 es impar)

// 2. Verificar si un bit específico está activo
int permisos = 0b00001101;  // bits: lectura=1, escritura=0, ejecucion=1
int LEER    = 0b00000001;   // bit 0
int ESCRIBIR = 0b00000010;  // bit 1
int EJECUTAR = 0b00000100;  // bit 2

boolean puedeLeer    = (permisos & LEER) != 0;    // true
boolean puedeEscribir = (permisos & ESCRIBIR) != 0; // false
boolean puedeEjecutar = (permisos & EJECUTAR) != 0; // true

// 3. Activar un bit (OR)
permisos = permisos | ESCRIBIR;  // Activa el permiso de escritura

// 4. Desactivar un bit (AND con NOT)
permisos = permisos & ~EJECUTAR;  // Desactiva el permiso de ejecución

// 5. Multiplicación/división rápida por potencias de 2
int valor = 100;
int doble   = valor << 1;  // 200  (más rápido que valor * 2)
int mitad   = valor >> 1;  // 50   (más rápido que valor / 2)
int cuadruplicado = valor << 2; // 400
```

---

## 9. Operador instanceof

El operador `instanceof` verifica si un objeto es una instancia de una clase o implementa una interfaz. Devuelve `boolean`.

```java
String texto = "Hola Java";
System.out.println(texto instanceof String);   // true
System.out.println(texto instanceof Object);   // true  (String hereda de Object)

Integer numero = 42;
System.out.println(numero instanceof Integer); // true
System.out.println(numero instanceof Number);  // true  (Integer hereda de Number)
System.out.println(numero instanceof String);  // false

Object obj = "un String";
if (obj instanceof String) {
    // Aquí sabemos que obj es un String, podemos hacer cast seguro
    String s = (String) obj;
    System.out.println(s.toUpperCase()); // "UN STRING"
}
```

### instanceof con Pattern Matching (Java 16+)

A partir de Java 16, puedes combinar `instanceof` con una declaración de variable en la misma expresión:

```java
Object obj = "Hola Mundo";

// Forma clásica (antes de Java 16)
if (obj instanceof String) {
    String s = (String) obj;  // Cast explícito necesario
    System.out.println(s.length());
}

// Con Pattern Matching (Java 16+): más conciso y seguro
if (obj instanceof String s) {
    System.out.println(s.length());  // s está disponible directamente
}
```

---

## 10. Precedencia y asociatividad de operadores

Cuando una expresión tiene múltiples operadores, Java necesita saber en qué orden evaluarlos. Esto se llama **precedencia de operadores**: los operadores con mayor precedencia se evalúan primero, igual que en matemáticas (multiplicación antes que suma).

### Tabla de precedencia (de mayor a menor)

| Nivel | Operadores | Asociatividad |
|-------|-----------|---------------|
| 1 (mayor) | `++` `--` (postfijo), `()`, `[]`, `.` | Izquierda a derecha |
| 2 | `++` `--` (prefijo), `+` `-` (unario), `!`, `~`, `(tipo)` | Derecha a izquierda |
| 3 | `*` `/` `%` | Izquierda a derecha |
| 4 | `+` `-` | Izquierda a derecha |
| 5 | `<<` `>>` `>>>` | Izquierda a derecha |
| 6 | `<` `<=` `>` `>=` `instanceof` | Izquierda a derecha |
| 7 | `==` `!=` | Izquierda a derecha |
| 8 | `&` (AND bitwise) | Izquierda a derecha |
| 9 | `^` (XOR bitwise) | Izquierda a derecha |
| 10 | `\|` (OR bitwise) | Izquierda a derecha |
| 11 | `&&` | Izquierda a derecha |
| 12 | `\|\|` | Izquierda a derecha |
| 13 | `? :` (ternario) | Derecha a izquierda |
| 14 (menor) | `=` `+=` `-=` `*=` `/=` `%=` etc. | Derecha a izquierda |

### Ejemplos de precedencia

```java
// Sin paréntesis: * antes que +
int r1 = 2 + 3 * 4;      // 2 + 12 = 14  (no 20)

// Con paréntesis: cambia el orden
int r2 = (2 + 3) * 4;    // 5 * 4 = 20

// Mezcla de operadores
int r3 = 10 + 2 * 5 - 3 / 3 + 1;
// Paso 1: 2*5=10 y 3/3=1
// Paso 2: 10 + 10 - 1 + 1 = 20

// Comparación y lógicos
int x = 5;
boolean r4 = x > 3 && x < 10;
// Paso 1: x > 3 → true, x < 10 → true
// Paso 2: true && true → true

// Mezcla con asignación
int a = 5;
a += 3 * 2;  // Primero 3*2=6, luego a += 6 → a = 11
```

### La regla práctica: usa paréntesis

En lugar de memorizar toda la tabla, usa paréntesis siempre que la precedencia no sea obvia. Los paréntesis hacen el código más legible y evitan errores:

```java
// Ambiguo:
boolean resultado = a > 0 && b > 0 || c > 0;

// Claro:
boolean resultado = (a > 0 && b > 0) || c > 0;
// o bien:
boolean resultado = a > 0 && (b > 0 || c > 0);
// Estas dos expresiones pueden dar resultados DIFERENTES
```

### Asociatividad

Cuando dos operadores tienen la misma precedencia, la **asociatividad** determina el orden de evaluación:

**Izquierda a derecha (la mayoría):**
```java
int r = 10 - 3 - 2;  // (10-3)-2 = 7-2 = 5  (no 10-(3-2)=9)
int s = 12 / 3 / 2;  // (12/3)/2 = 4/2 = 2
```

**Derecha a izquierda (asignaciones):**
```java
int a, b, c;
a = b = c = 5;  // c=5, luego b=5, luego a=5
```

---

## 11. Errores comunes con operadores

### Error 1: Confundir = (asignación) con == (comparación)

```java
int x = 5;

// ERROR LÓGICO (no de compilación en algunos contextos):
if (x = 10) { ... }  // En Java esto da error de compilación porque x=10 es int, no boolean
                     // En C/C++ sería un bug silencioso. En Java el compilador lo atrapa.

// CORRECTO:
if (x == 10) { ... }  // Compara si x es igual a 10
```

### Error 2: División entera cuando se esperaba decimal

```java
int a = 5, b = 2;
double resultado = a / b;           // 2.0  ← NO es 2.5!
                                    // Primero divide como enteros (5/2=2), luego convierte a double

double resultadoCorrecto = (double) a / b;  // 2.5  ← correcto
```

### Error 3: Desbordamiento en operaciones intermedias

```java
int millones = 1_000_000;
int resultado = millones * millones;  // Desbordamiento: 10^12 no cabe en int
System.out.println(resultado);        // -727379968  ← incorrecto

// Solución: usar long
long resultadoCorrecto = (long) millones * millones;  // 1000000000000
```

### Error 4: Precedencia incorrecta con NOT

```java
boolean a = true, b = false;

// ERROR de razonamiento: ¿qué se niega?
boolean r = !a && b;    // (!a) && b = false && false = false
boolean r2 = !(a && b); // !(true && false) = !(false) = true
// Son diferentes
```

### Error 5: Comparar doubles con ==

```java
double x = 0.1 + 0.2;
System.out.println(x == 0.3);   // false  ← por imprecisión de punto flotante
System.out.println(x);           // 0.30000000000000004

// Solución: comparar con un margen de tolerancia (epsilon)
double epsilon = 1e-9;
boolean sonIguales = Math.abs(x - 0.3) < epsilon;
System.out.println(sonIguales);  // true
```

### Error 6: Uso incorrecto de incremento en expresiones

```java
int i = 5;
int j = i++ + ++i;
// Paso 1: i++ devuelve 5, i pasa a 6
// Paso 2: ++i incrementa a 7, devuelve 7
// j = 5 + 7 = 12, i = 7

// Esta clase de expresiones son confusas e impredecibles.
// NUNCA uses ++ o -- en expresiones compuestas. Úsalos solo solos:
i++;       // Correcto
j = i + 1; // Correcto
```

### Error 7: NullPointerException con operadores en objetos

```java
String s = null;
int longitud = s.length();  // NullPointerException

// Solución: verificar null primero
int longitud = (s != null) ? s.length() : 0;
```

---

## 12. Ejercicios prácticos

### Ejercicio 1 — Calculadora básica (Nivel: Básico)

Crea un programa `CalculadoraBasica.java` que declare dos variables `double` con los valores `17.5` y `4.0`, y muestre el resultado de todas las operaciones aritméticas entre ellas, incluyendo la división entera (con cast a `int`) y el módulo.

**Resultado esperado:**
```
a = 17.5 | b = 4.0
Suma         : 21.5
Resta        : 13.5
Multiplicación: 70.0
División     : 4.375
División entera: 4
Módulo       : 1.5
```

---

### Ejercicio 2 — Operaciones de asignación compuesta (Nivel: Básico)

Crea un programa `AsignacionCompuesta.java` que empiece con `int puntos = 500` y aplique en orden:
1. Suma 150 puntos
2. Multiplica por 2
3. Resta 300 puntos
4. Divide entre 4
5. Calcula el módulo entre 100

Muestra el valor de `puntos` después de cada operación.

---

### Ejercicio 3 — Par, impar y divisibilidad (Nivel: Básico-Medio)

Crea un programa `Divisibilidad.java` con un número `int n = 360` que muestre:
1. Si es par o impar (usando `%`)
2. Si es divisible por 3, por 5, por 7 y por 9
3. Si es divisible por 3 Y por 5 al mismo tiempo (usando `&&`)
4. Si es divisible por 7 O por 9 (usando `||`)
5. El último dígito del número (usando `%`)
6. La suma de sus dos últimos dígitos

---

### Ejercicio 4 — Verificador de condiciones (Nivel: Medio)

Crea un programa `VerificadorCondiciones.java` con estas variables:

```java
int edad = 22;
double salario = 1800.0;
boolean tieneTrabajo = true;
boolean tieneDeudas = false;
int añosExperiencia = 2;
```

Calcula y muestra con `true`/`false` cada una de estas condiciones:

1. ¿Es mayor de edad? (`>= 18`)
2. ¿Tiene buen salario? (`>= 1500`)
3. ¿Puede pedir un crédito? (mayor de edad, tiene trabajo, sin deudas, salario >= 1500)
4. ¿Puede acceder a nivel senior? (experiencia >= 5 O salario >= 3000)
5. ¿Necesita formación adicional? (experiencia < 3 Y salario < 2000)
6. ¿Perfil básico aprobado? (tiene trabajo Y no tiene deudas Y es mayor de edad)

---

### Ejercicio 5 — Operador ternario (Nivel: Medio)

Crea un programa `OperadorTernario.java` que use **exclusivamente el operador ternario** (sin `if/else`) para:

1. Dado `int temperatura = 28`, mostrar: "Caluroso" si > 25, "Fresco" si <= 25
2. Dado `double nota = 6.8`, mostrar: "Aprobado" si >= 5.0, "Reprobado" si < 5.0
3. Dado `int numero = -7`, mostrar su valor absoluto (sin usar `Math.abs`)
4. Dado `int a = 45, b = 78`, mostrar el mayor de los dos
5. Dado `int año = 2024`, mostrar "Bisiesto" si es divisible por 4 (simplificado), "No bisiesto" si no

---

### Ejercicio 6 — Conversión de tiempo (Nivel: Medio)

Crea un programa `ConversorTiempo.java` que convierta un total de segundos en horas, minutos y segundos usando **solo operadores aritméticos** (`/` y `%`). Prueba con `int totalSegundos = 86399` (un segundo antes de medianoche).

**Resultado esperado:**
```
Total de segundos: 86399
Horas  : 23
Minutos: 59
Segundos: 59
Formato: 23:59:59
```

---

### Ejercicio 7 — Bits y permisos (Nivel: Avanzado para el tema)

Crea un programa `SistemaPermisos.java` que simule un sistema de permisos usando operadores bitwise.

Define estas constantes:
```java
final int LEER    = 0b0001;  // bit 0
final int ESCRIBIR = 0b0010; // bit 1
final int EJECUTAR = 0b0100; // bit 2
final int ADMIN   = 0b1000;  // bit 3
```

Luego:
1. Crea un usuario con permisos de LEER y EJECUTAR combinados con `|`
2. Verifica con `&` si tiene permiso de ESCRIBIR (debe ser false)
3. Agrega el permiso de ESCRIBIR al usuario con `|=`
4. Verifica de nuevo si tiene permiso de ESCRIBIR (debe ser true ahora)
5. Quita el permiso de EJECUTAR usando `&= ~EJECUTAR`
6. Muestra el estado final de los permisos del usuario

---

## 13. Resumen del tema

### Todos los operadores de un vistazo

```java
// Aritméticos
int r1 = 10 + 3;    // 13  suma
int r2 = 10 - 3;    // 7   resta
int r3 = 10 * 3;    // 30  multiplicación
int r4 = 10 / 3;    // 3   división entera
int r5 = 10 % 3;    // 1   módulo (resto)

// Asignación compuesta
x += 5;  x -= 5;  x *= 5;  x /= 5;  x %= 5;

// Incremento y decremento
x++;  x--;   // postfijo: usa el valor actual, luego modifica
++x;  --x;   // prefijo:  modifica primero, luego usa el nuevo valor

// Comparación → boolean
5 == 5   // true    igual a
5 != 3   // true    diferente de
5 > 3    // true    mayor que
3 < 5    // true    menor que
5 >= 5   // true    mayor o igual que
3 <= 5   // true    menor o igual que

// Lógicos
true && false   // false   AND: ambos deben ser true
true || false   // true    OR:  al menos uno debe ser true
!true           // false   NOT: invierte el boolean

// Ternario
String s = edad >= 18 ? "Adulto" : "Menor";

// Bitwise
5 & 3    // 1    AND bit a bit
5 | 3    // 7    OR bit a bit
5 ^ 3    // 6    XOR bit a bit
~5       // -6   NOT bit a bit
1 << 3   // 8    desplazamiento izquierda (* 2^3)
8 >> 2   // 2    desplazamiento derecha   (/ 2^2)
```

### Reglas de oro

| Situación | Regla |
|-----------|-------|
| División entre enteros | Siempre devuelve entero; usar cast para decimal |
| Comparar Strings | Siempre usar `.equals()`, nunca `==` |
| Comparar doubles | Usar margen de tolerancia (epsilon), nunca `==` |
| Precedencia | Usar paréntesis cuando haya duda |
| `++`/`--` | No mezclar en expresiones complejas |
| Cortocircuito `&&` | Colocar la condición más restrictiva primero |
| Cortocircuito `\|\|` | Colocar la condición que más probable es `true` primero |
| Overflow | Elegir el tipo correcto; si puede ser grande, usar `long` |

---

## Próximo tema

**Tema 4: Estructuras de control**

Aprenderás a tomar decisiones con `if`, `else if` y `else`; a seleccionar entre múltiples opciones con `switch` y `switch expressions`; y a repetir bloques de código con los bucles `for`, `while` y `do-while`. También verás `break`, `continue` y bucles anidados.

---

*Curso de Java — Nivel 1: Fundamentos*
*Tema 3 de 6*
*Creado con fines educativos*
