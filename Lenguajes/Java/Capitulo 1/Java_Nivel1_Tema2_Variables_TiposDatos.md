# Curso de Java — Nivel 1: Fundamentos
## Tema 2: Variables y Tipos de Datos

> **Nivel:** Principiante | **Prerrequisitos:** Tema 1 completado | **Duración estimada:** 3–4 horas

---

## Tabla de contenidos

1. [¿Qué es una variable?](#1-qué-es-una-variable)
2. [Declaración e inicialización de variables](#2-declaración-e-inicialización-de-variables)
3. [Tipos de datos primitivos](#3-tipos-de-datos-primitivos)
4. [Tipos de datos de referencia](#4-tipos-de-datos-de-referencia)
5. [El tipo String en profundidad](#5-el-tipo-string-en-profundidad)
6. [Constantes con final](#6-constantes-con-final)
7. [Inferencia de tipos con var](#7-inferencia-de-tipos-con-var)
8. [Conversión de tipos (Casting)](#8-conversión-de-tipos-casting)
9. [Autoboxing y Unboxing](#9-autoboxing-y-unboxing)
10. [Ámbito de las variables (Scope)](#10-ámbito-de-las-variables-scope)
11. [Reglas y convenciones para nombrar variables](#11-reglas-y-convenciones-para-nombrar-variables)
12. [Errores comunes con variables](#12-errores-comunes-con-variables)
13. [Ejercicios prácticos](#13-ejercicios-prácticos)
14. [Resumen del tema](#14-resumen-del-tema)

---

## 1. ¿Qué es una variable?

Una **variable** es un espacio reservado en la memoria del computador que tiene un nombre, un tipo de dato y un valor. Puedes imaginarla como una caja con una etiqueta: la etiqueta es el nombre, el tipo de caja determina qué puede guardar, y lo que hay dentro es el valor.

```
  ┌─────────────┐
  │     25      │  ← valor almacenado
  └─────────────┘
       edad        ← nombre de la variable
       (int)       ← tipo de dato
```

Cuando tu programa se ejecuta, Java reserva un espacio específico en la memoria RAM para cada variable. Cada vez que usas el nombre de la variable en el código, Java va a ese espacio de memoria y obtiene (o guarda) el valor.

### ¿Por qué necesitamos variables?

Sin variables, un programa no podría recordar información entre instrucciones. Imagina una calculadora que suma dos números: necesita guardar el primer número, el segundo número y el resultado en algún lugar mientras trabaja. Esos "lugares" son las variables.

```java
int primerNumero = 10;
int segundoNumero = 25;
int resultado = primerNumero + segundoNumero;
System.out.println("La suma es: " + resultado); // La suma es: 35
```

### Las tres características de toda variable

1. **Nombre (identificador):** Cómo se llama la variable. Ejemplo: `edad`, `nombreUsuario`, `totalVentas`.
2. **Tipo de dato:** Qué clase de información puede guardar. Ejemplo: `int` para enteros, `String` para texto.
3. **Valor:** La información almacenada en ese momento. El valor puede cambiar durante la ejecución del programa (de ahí el nombre "variable").

---

## 2. Declaración e inicialización de variables

### 2.1 Declaración

**Declarar** una variable significa anunciarle al compilador que existirá una variable con ese nombre y ese tipo. Se hace así:

```java
tipo nombreVariable;
```

Ejemplos:

```java
int edad;
double precio;
String nombre;
boolean activo;
```

En este punto, la variable existe en memoria pero no tiene un valor asignado todavía. Si intentas usarla sin valor, el compilador te dará un error.

### 2.2 Inicialización

**Inicializar** una variable significa darle un valor por primera vez:

```java
edad = 28;
precio = 19.99;
nombre = "Ana López";
activo = true;
```

### 2.3 Declaración e inicialización en una sola línea (lo más común)

En la práctica, casi siempre declaras e inicializas en la misma línea:

```java
int edad = 28;
double precio = 19.99;
String nombre = "Ana López";
boolean activo = true;
```

### 2.4 Declarar múltiples variables del mismo tipo

Puedes declarar varias variables del mismo tipo separadas por comas:

```java
int x = 10, y = 20, z = 30;
double alto = 1.75, ancho = 0.90;
```

Aunque es válido, se considera mejor práctica declararlas en líneas separadas para mayor claridad.

### 2.5 Cambiar el valor de una variable

Una vez declarada, puedes cambiar su valor tantas veces como quieras:

```java
int puntuacion = 0;
System.out.println(puntuacion); // 0

puntuacion = 100;
System.out.println(puntuacion); // 100

puntuacion = 250;
System.out.println(puntuacion); // 250
```

Importante: cuando cambias el valor, el valor anterior se pierde. La memoria ahora contiene solo el nuevo valor.

---

## 3. Tipos de datos primitivos

Java tiene **8 tipos de datos primitivos**. Son los bloques de construcción más básicos del lenguaje. Se llaman "primitivos" porque no son objetos, sino valores simples almacenados directamente en memoria.

### Visión general de los 8 tipos primitivos

| Tipo | Tamaño | Rango de valores | Valor por defecto | Uso típico |
|------|--------|-----------------|-------------------|------------|
| `byte` | 8 bits | -128 a 127 | 0 | Ahorro de memoria en arrays grandes |
| `short` | 16 bits | -32,768 a 32,767 | 0 | Números enteros pequeños |
| `int` | 32 bits | -2,147,483,648 a 2,147,483,647 | 0 | Enteros en general (el más usado) |
| `long` | 64 bits | -9.2×10¹⁸ a 9.2×10¹⁸ | 0L | Enteros muy grandes |
| `float` | 32 bits | ±3.4×10³⁸ (7 decimales) | 0.0f | Decimales con poca precisión |
| `double` | 64 bits | ±1.7×10³⁰⁸ (15 decimales) | 0.0 | Decimales en general (el más usado) |
| `char` | 16 bits | '\u0000' a '\uffff' (0 a 65,535) | '\u0000' | Un solo carácter Unicode |
| `boolean` | 1 bit* | `true` o `false` | false | Condiciones lógicas |

*El tamaño real del boolean en JVM depende de la implementación, pero conceptualmente representa 1 bit de información.

---

### 3.1 Tipos enteros: byte, short, int y long

Todos almacenan números enteros (sin decimales). La diferencia es el rango de valores que pueden contener.

#### `int` — El entero más usado

```java
int temperatura = -5;
int poblacionCiudad = 1_500_000;    // Puedes usar _ como separador visual (Java 7+)
int añoActual = 2024;
int saldoBancario = -200;           // Puede ser negativo
```

El tipo `int` es el estándar para trabajar con números enteros en Java. A menos que tengas una razón específica para usar otro tipo, usa `int`.

#### `long` — Para números muy grandes

Cuando el valor supera el límite de `int` (~2 mil millones), necesitas `long`. Debes añadir la letra `L` al final del número para indicarle a Java que es un `long`:

```java
long poblacionMundial = 8_000_000_000L;    // 8 mil millones, necesita L al final
long distanciaAlSolKm = 149_600_000_000L;  // 149.6 mil millones de km
long milisegundosEnUnAño = 31_536_000_000L;

// ERROR: sin la L, Java interpreta el literal como int y da desbordamiento
long error = 8_000_000_000; // Error de compilación: integer number too large
```

#### `byte` y `short` — Para ahorrar memoria

```java
byte nivelDificultad = 3;       // Valores entre -128 y 127
byte porcentaje = 100;          // Útil cuando sabes que el valor es pequeño

short codigoPais = 34;          // España: +34
short temperaturaMaxima = 320;  // Kelvin, por ejemplo
```

Estos tipos se usan principalmente cuando tienes millones de valores (como en procesamiento de imágenes o audio) y el ahorro de memoria importa. En código cotidiano, usa `int`.

#### Límites de los tipos enteros

```java
System.out.println(Integer.MAX_VALUE);  // 2147483647
System.out.println(Integer.MIN_VALUE);  // -2147483648
System.out.println(Long.MAX_VALUE);     // 9223372036854775807
System.out.println(Byte.MAX_VALUE);     // 127
System.out.println(Short.MAX_VALUE);    // 32767
```

#### ¿Qué pasa si superas el límite? — Desbordamiento (Overflow)

```java
int maximo = Integer.MAX_VALUE;  // 2147483647
System.out.println(maximo + 1);  // -2147483648  ← DESBORDAMIENTO, vuelve al mínimo
```

El desbordamiento no causa un error en Java; simplemente el número "da la vuelta". Es un bug muy difícil de detectar, por eso es importante elegir el tipo correcto.

---

### 3.2 Tipos decimales: float y double

Almacenan números con parte decimal. Internamente usan el estándar IEEE 754 para representar números de punto flotante.

#### `double` — El decimal más usado

```java
double pi = 3.141592653589793;
double precioProducto = 29.99;
double temperatura = -12.5;
double porcentajeIVA = 0.21;  // 21%
double distancia = 1.496e11;  // Notación científica: 1.496 × 10^11
```

`double` usa 64 bits y ofrece hasta 15-16 cifras decimales de precisión. Es el tipo decimal estándar en Java.

#### `float` — Precisión reducida

Debes añadir la letra `f` al final del literal:

```java
float temperatura = 36.5f;
float coordenadaX = 10.25f;
float aproximacion = 3.14f;

// ERROR: sin la f, Java interpreta el literal como double
float error = 36.5;  // Error: posible pérdida de precisión (double → float)
```

`float` usa 32 bits y ofrece hasta 7 cifras decimales de precisión. Se usa cuando el ahorro de memoria es crítico (como en gráficos 3D o shaders).

#### Problema de precisión en números decimales

Este es un concepto importante: los números de punto flotante no pueden representar todos los decimales con exactitud perfecta porque internamente se almacenan en base 2 (binario), y muchas fracciones decimales no tienen representación binaria exacta.

```java
double a = 0.1;
double b = 0.2;
System.out.println(a + b);          // 0.30000000000000004  ← ¡No es 0.3!
System.out.println(a + b == 0.3);   // false

// Para cálculos financieros precisos, usa BigDecimal en lugar de double:
import java.math.BigDecimal;
BigDecimal x = new BigDecimal("0.1");
BigDecimal y = new BigDecimal("0.2");
System.out.println(x.add(y));  // 0.3  ← Exacto
```

**Regla práctica:**
- Para cálculos científicos, estadísticos, o donde la ligera imprecisión no importa: usa `double`.
- Para dinero, precios, cálculos financieros: usa `BigDecimal`.

---

### 3.3 Tipo carácter: char

Almacena un único carácter Unicode. En Java, un `char` puede representar cualquier carácter del estándar Unicode, lo que incluye letras latinas, acentos, caracteres chinos, japoneses, árabes, emojis, y mucho más.

```java
char inicial = 'A';          // Siempre comillas simples, no dobles
char numero = '7';           // El carácter '7', NO el número entero 7
char espacio = ' ';          // El carácter espacio también es un char
char acento = 'á';           // Caracteres con acento
char simbolo = '€';          // Símbolo del euro
char unicode = '\u0041';     // Código Unicode de 'A' (forma alternativa)
char nueva_linea = '\n';     // Secuencia de escape
```

#### char y su relación con los enteros

Un `char` en Java es internamente un número entero sin signo de 16 bits. Cada carácter tiene un código numérico (código ASCII o Unicode). Esto permite hacer operaciones aritméticas con caracteres:

```java
char letra = 'A';
System.out.println((int) letra);     // 65  ← el código ASCII de 'A'

char siguiente = (char)(letra + 1);
System.out.println(siguiente);       // B  ← el siguiente carácter

// Recorrer el alfabeto
for (char c = 'a'; c <= 'z'; c++) {
    System.out.print(c + " ");
}
// Salida: a b c d e f g h i j k l m n o p q r s t u v w x y z
```

#### Tabla de códigos ASCII importantes

| Carácter | Código decimal |
|----------|---------------|
| '0' – '9' | 48 – 57 |
| 'A' – 'Z' | 65 – 90 |
| 'a' – 'z' | 97 – 122 |
| ' ' (espacio) | 32 |
| '\n' (nueva línea) | 10 |

---

### 3.4 Tipo lógico: boolean

El tipo `boolean` solo puede tener dos valores posibles: `true` (verdadero) o `false` (falso). Es fundamental para la toma de decisiones en el código.

```java
boolean esMayorDeEdad = true;
boolean tieneCuenta = false;
boolean sesionActiva = true;
boolean esFeriado = false;
```

Los valores `true` y `false` se escriben en minúsculas. A diferencia de otros lenguajes, en Java no puedes usar `1` o `0` en lugar de `true` o `false`.

```java
// Esto es CORRECTO:
boolean correcto = true;

// Esto es ERROR en Java (a diferencia de C o Python):
boolean error1 = 1;          // Error de compilación
boolean error2 = "true";     // Error: es un String, no un boolean
boolean error3 = True;       // Error: True con mayúscula no existe en Java
```

#### Los booleanos son el resultado de comparaciones

```java
int edad = 20;
boolean esMayor = edad >= 18;         // true
boolean esAdolescente = edad < 18;    // false
boolean esExactamente18 = edad == 18; // false

String nombre = "Carlos";
boolean esCarlos = nombre.equals("Carlos"); // true
```

Los boolean se usan principalmente en estructuras de control como `if`, `while` y `for`, que veremos en el Tema 4.

---

## 4. Tipos de datos de referencia

A diferencia de los tipos primitivos, los tipos de referencia no almacenan el valor directamente. En cambio, almacenan la **dirección de memoria** (referencia) donde vive el objeto real. En Java, todos los objetos son tipos de referencia.

```
Tipo primitivo:
  ┌──────────────────┐
  │ edad  │    28    │  ← el valor está AQUÍ directamente
  └──────────────────┘

Tipo de referencia:
  ┌──────────────────┐       ┌───────────────────────────┐
  │nombre │ @A3F2B1  │ ───►  │  Objeto String: "Ana"     │
  └──────────────────┘       └───────────────────────────┘
     la variable guarda          el valor real está en
     una DIRECCIÓN               el Heap de la JVM
```

Los tipos de referencia más importantes son:
- `String` (cadenas de texto)
- Arrays (arreglos)
- Clases (objetos que creas tú o que provee Java)
- Interfaces

El valor especial `null` indica que una variable de referencia no apunta a ningún objeto:

```java
String nombre = null;  // nombre no apunta a ningún objeto String
```

---

## 5. El tipo String en profundidad

`String` es el tipo de dato más usado en Java después de `int`. Representa una cadena de caracteres (texto de cualquier longitud). Aunque se comporta de manera especial (puedes crearlo con comillas dobles sin usar `new`), en realidad `String` es una clase, no un tipo primitivo.

### 5.1 Crear Strings

```java
// Forma literal (recomendada, usa el String Pool)
String saludo = "Hola Mundo";
String vacio = "";               // String vacío, no es null
String soloEspacios = "   ";     // Solo espacios, tampoco es null

// Forma con new (crea siempre un objeto nuevo, menos eficiente)
String otroSaludo = new String("Hola Mundo");
```

#### El String Pool

Java optimiza el uso de Strings mediante el "String Pool" (piscina de Strings): una zona especial en memoria donde se reutilizan Strings con el mismo contenido. Si creas dos Strings literales con el mismo texto, apuntan al mismo objeto en memoria.

```java
String a = "Hola";
String b = "Hola";
System.out.println(a == b);  // true  ← mismo objeto en el String Pool

String c = new String("Hola");
System.out.println(a == c);  // false ← c es un nuevo objeto en el Heap
```

Por esta razón, **nunca compares Strings con `==`**. Usa siempre `.equals()`:

```java
String s1 = "Java";
String s2 = "Java";

// INCORRECTO para comparar contenido:
if (s1 == s2) { ... }          // Compara referencias, no contenido

// CORRECTO para comparar contenido:
if (s1.equals(s2)) { ... }     // Compara el texto real: true
if (s1.equalsIgnoreCase(s2)) { ... }  // Sin importar mayúsculas
```

### 5.2 Concatenación de Strings

Unir dos o más Strings se llama **concatenación** y se hace con el operador `+`:

```java
String nombre = "Carlos";
String apellido = "García";
String nombreCompleto = nombre + " " + apellido;
System.out.println(nombreCompleto); // Carlos García

// Puedes concatenar con cualquier tipo de dato
int edad = 30;
String mensaje = "Tengo " + edad + " años";
System.out.println(mensaje); // Tengo 30 años

// Cuidado con el orden de operaciones
System.out.println("Resultado: " + 2 + 3);   // Resultado: 23  (concatena)
System.out.println("Resultado: " + (2 + 3)); // Resultado: 5   (suma primero)
```

### 5.3 Inmutabilidad de los Strings

Los Strings en Java son **inmutables**: una vez creado un String, no puede ser modificado. Cuando parece que modificas un String, en realidad estás creando uno nuevo.

```java
String original = "Hola";
String modificado = original + " Mundo";

System.out.println(original);   // Hola    ← no cambió
System.out.println(modificado); // Hola Mundo  ← es un nuevo objeto
```

Esto tiene implicaciones de rendimiento: si necesitas construir un String a partir de muchas operaciones (como en un bucle), usa `StringBuilder` en lugar del operador `+`.

### 5.4 Métodos principales de String

La clase String viene con decenas de métodos útiles. Estos son los más importantes:

#### Información sobre el String

```java
String texto = "  Hola Mundo  ";

texto.length();              // 14 — número de caracteres (incluye espacios)
texto.isEmpty();             // false — true si length() == 0
texto.isBlank();             // false — true si solo tiene espacios (Java 11+)
texto.trim();                // "Hola Mundo" — elimina espacios al inicio y fin
texto.strip();               // "Hola Mundo" — como trim() pero reconoce Unicode
```

#### Búsqueda dentro del String

```java
String frase = "El lenguaje Java es poderoso";

frase.contains("Java");         // true — ¿contiene esta subcadena?
frase.startsWith("El");         // true — ¿empieza con este texto?
frase.endsWith("poderoso");     // true — ¿termina con este texto?
frase.indexOf("Java");          // 12 — posición donde empieza "Java" (0-based)
frase.lastIndexOf("a");         // 26 — última posición de 'a'
frase.indexOf("Python");        // -1 — no existe, devuelve -1
```

#### Extracción de partes del String

```java
String texto = "Programación en Java";

texto.charAt(0);                  // 'P' — carácter en posición 0
texto.substring(14);              // "Java" — desde posición 14 hasta el final
texto.substring(0, 12);           // "Programación" — desde 0, sin incluir 12
```

Importante: las posiciones (índices) en Java siempre empiezan en **0**, no en 1:

```
"Java"
  J  a  v  a
  0  1  2  3   ← índices
```

#### Transformaciones del String

```java
String texto = "Hola Mundo";

texto.toUpperCase();            // "HOLA MUNDO"
texto.toLowerCase();            // "hola mundo"
texto.replace("Mundo", "Java"); // "Hola Java"
texto.replaceAll("\\s+", "_");  // "Hola_Mundo" — reemplaza con regex
"a,b,c,d".split(",");           // ["a", "b", "c", "d"] — divide en array
```

#### Comparación de Strings

```java
String s1 = "Java";
String s2 = "java";

s1.equals(s2);                  // false — sensible a mayúsculas
s1.equalsIgnoreCase(s2);        // true  — ignora mayúsculas/minúsculas
s1.compareTo(s2);               // número negativo: s1 < s2 (por ASCII)
```

#### Conversión entre String y tipos primitivos

```java
// String → número
int n = Integer.parseInt("42");           // 42
double d = Double.parseDouble("3.14");    // 3.14
boolean b = Boolean.parseBoolean("true"); // true

// Número → String
String s1 = String.valueOf(42);       // "42"
String s2 = Integer.toString(42);     // "42"
String s3 = "" + 42;                  // "42" (forma corta pero menos clara)
```

### 5.5 Text Blocks (Java 15+)

Los Text Blocks permiten escribir Strings de múltiples líneas de forma legible, sin necesidad de concatenar o usar `\n`:

```java
// Antes (Java < 15): difícil de leer
String json = "{\n" +
              "  \"nombre\": \"Carlos\",\n" +
              "  \"edad\": 30\n" +
              "}";

// Ahora con Text Block: mucho más limpio
String json = """
              {
                "nombre": "Carlos",
                "edad": 30
              }
              """;
```

---

## 6. Constantes con final

Una **constante** es una variable cuyo valor no puede cambiar una vez asignado. En Java se declaran con la palabra clave `final`:

```java
final int DIAS_SEMANA = 7;
final double PI = 3.141592653589793;
final String NOMBRE_EMPRESA = "Tech Solutions S.A.";
final int MAX_INTENTOS_LOGIN = 3;
```

Por convención, las constantes se escriben en `MAYUSCULAS_CON_GUION_BAJO`.

Si intentas cambiar el valor de una constante, el compilador lanza un error:

```java
final int VELOCIDAD_LUZ = 299_792_458;
VELOCIDAD_LUZ = 300_000_000; // Error: cannot assign a value to final variable
```

### ¿Por qué usar constantes?

```java
// Sin constantes: el número 3 aparece mágicamente. ¿Qué significa?
if (intentos > 3) {
    bloquearCuenta();
}

// Con constantes: el código se explica solo
final int MAX_INTENTOS = 3;
if (intentos > MAX_INTENTOS) {
    bloquearCuenta();
}
```

Las constantes hacen el código más legible y más fácil de mantener: si el valor cambia (por ejemplo, se decide que ahora son 5 intentos), solo lo cambias en un lugar.

### Constantes de clase con static final

Cuando una constante debe ser compartida por todo el programa, se declara como `static final`:

```java
public class Configuracion {
    public static final double IVA = 0.21;
    public static final int TIMEOUT_SEGUNDOS = 30;
    public static final String VERSION_APP = "2.4.1";
}

// Uso desde cualquier parte del programa:
double total = precio * (1 + Configuracion.IVA);
```

---

## 7. Inferencia de tipos con var

Desde **Java 10**, puedes usar `var` en lugar del tipo explícito cuando declaras una variable local. El compilador infiere automáticamente el tipo según el valor asignado.

```java
// Forma tradicional:
int edad = 25;
String nombre = "María";
double precio = 9.99;
ArrayList<String> lista = new ArrayList<>();

// Con var (el compilador deduce el tipo):
var edad = 25;             // int
var nombre = "María";      // String
var precio = 9.99;         // double
var lista = new ArrayList<String>(); // ArrayList<String>
```

### Reglas de var

**1. Solo para variables locales.** No puede usarse en parámetros de métodos, campos de clase, ni tipos de retorno:

```java
public class Ejemplo {
    var campo = "error";  // ERROR: no se puede usar en campos de clase

    public var metodo() { }  // ERROR: no se puede usar en tipo de retorno

    public void ejemplo(var param) { }  // ERROR: no se puede usar en parámetros

    public void correcto() {
        var local = "esto sí funciona";  // CORRECTO: variable local
    }
}
```

**2. Debe inicializarse en la misma línea:**

```java
var sinValor;          // ERROR: var necesita un valor para inferir el tipo
var conValor = 42;     // CORRECTO
```

**3. No puede inicializarse con null sin cast:**

```java
var nulo = null;          // ERROR: no se puede inferir el tipo de null
var nulo = (String) null; // CORRECTO
```

### ¿Cuándo usar var?

Usa `var` cuando el tipo es obvio por contexto y cuando hace el código más legible. No lo uses cuando elimina información útil:

```java
// BIEN: el tipo es obvio, var simplifica
var mensaje = "Bienvenido al sistema";
var contador = 0;
var conexion = new ConexionBaseDatos(); // el tipo se ve en el lado derecho

// MAL: el tipo no es obvio, var reduce claridad
var x = calcularResultado(); // ¿qué tipo devuelve esto?
var d = metodoOscuro();      // imposible saber sin ir al método
```

---

## 8. Conversión de tipos (Casting)

Java es un lenguaje fuertemente tipado, lo que significa que no puedes usar un tipo de dato donde se espera otro sin una conversión explícita. Hay dos tipos de conversión:

### 8.1 Conversión implícita (Widening Casting)

Ocurre automáticamente cuando conviertes de un tipo más pequeño a uno más grande. No hay pérdida de información.

```
byte → short → int → long → float → double
```

```java
int entero = 100;
long largo = entero;        // int → long: automático, sin pérdida
float decimal = entero;     // int → float: automático
double doble = entero;      // int → double: automático

byte b = 42;
int i = b;                  // byte → int: automático

System.out.println(doble);  // 100.0
```

### 8.2 Conversión explícita (Narrowing Casting)

Ocurre cuando conviertes de un tipo más grande a uno más pequeño. **Puede haber pérdida de información.** Debes indicarlo explícitamente con el tipo entre paréntesis:

```
double → float → long → int → short → byte
```

```java
double precio = 9.99;
int precioEntero = (int) precio;     // 9  ← trunca, NO redondea
System.out.println(precioEntero);    // 9

long numerandoGrande = 1_000_000_000_000L;
int reducido = (int) numerandoGrande; // Posible desbordamiento y pérdida de datos

double d = 3.99;
int i = (int) d;                      // 3  ← siempre trunca hacia cero
System.out.println(i);                // 3, no 4
```

Para **redondear** en lugar de truncar, usa `Math.round()`:

```java
double precio = 9.99;
long redondeado = Math.round(precio);   // 10
int r = (int) Math.round(precio);       // 10
```

### 8.3 Tabla de compatibilidad de tipos

| Origen → Destino | ¿Automático? | ¿Pérdida posible? |
|-----------------|--------------|-------------------|
| `byte` → `int` | Sí | No |
| `int` → `long` | Sí | No |
| `int` → `double` | Sí | No (mínima) |
| `double` → `int` | No (cast necesario) | Sí (parte decimal) |
| `long` → `int` | No (cast necesario) | Sí (desbordamiento) |
| `double` → `float` | No (cast necesario) | Sí (precisión) |

### 8.4 Conversión con Strings

```java
// String → tipos primitivos
int n = Integer.parseInt("123");
double d = Double.parseDouble("3.14");
boolean b = Boolean.parseBoolean("true");
long l = Long.parseLong("9876543210");

// Tipos primitivos → String
String s1 = String.valueOf(42);
String s2 = String.valueOf(3.14);
String s3 = String.valueOf(true);
String s4 = Integer.toString(42);
String s5 = "" + 42;  // Concatenación (funciona pero String.valueOf es más claro)
```

---

## 9. Autoboxing y Unboxing

Java tiene dos versiones de cada tipo primitivo: el tipo primitivo en sí y su correspondiente **clase envoltorio** (Wrapper Class). Las clases envoltorio son objetos que "envuelven" un valor primitivo y añaden métodos útiles.

### Tabla de primitivos y sus Wrapper Classes

| Tipo primitivo | Wrapper Class |
|----------------|---------------|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

### ¿Por qué existen las Wrapper Classes?

Los tipos primitivos no son objetos, por lo que no pueden usarse en lugares donde Java requiere un objeto (como las colecciones `ArrayList`, `HashMap`, etc.). Las Wrapper Classes solucionan esto.

```java
// Un ArrayList no puede contener int directamente
ArrayList<int> lista;          // ERROR: no se puede usar primitivo aquí
ArrayList<Integer> lista;      // CORRECTO: Integer es un objeto
```

### Autoboxing — De primitivo a objeto (automático)

Java convierte automáticamente un primitivo a su Wrapper Class cuando es necesario:

```java
int primitivo = 42;
Integer objeto = primitivo;    // Autoboxing automático: int → Integer

// En colecciones:
ArrayList<Integer> numeros = new ArrayList<>();
numeros.add(10);   // Autoboxing: Java convierte int 10 → Integer 10
numeros.add(20);
numeros.add(30);
```

### Unboxing — De objeto a primitivo (automático)

La conversión inversa también es automática:

```java
Integer objeto = 100;
int primitivo = objeto;   // Unboxing automático: Integer → int

// En operaciones aritméticas:
Integer a = 5;
Integer b = 3;
int suma = a + b;  // Unboxing para la suma, devuelve int
```

### Métodos útiles de las Wrapper Classes

```java
// Integer
Integer.MAX_VALUE           // 2147483647
Integer.MIN_VALUE           // -2147483648
Integer.parseInt("42")      // Convierte String a int
Integer.toBinaryString(10)  // "1010" — representación binaria
Integer.toHexString(255)    // "ff"   — representación hexadecimal
Integer.toOctalString(8)    // "10"   — representación octal

// Double
Double.MAX_VALUE             // 1.7976931348623157E308
Double.isNaN(0.0/0.0)        // true (Not a Number)
Double.isInfinite(1.0/0.0)   // true

// Character
Character.isLetter('A')      // true
Character.isDigit('5')       // true
Character.isWhitespace(' ')  // true
Character.toUpperCase('a')   // 'A'
Character.toLowerCase('Z')   // 'z'
```

---

## 10. Ámbito de las variables (Scope)

El **ámbito** (scope) de una variable define en qué partes del código esa variable existe y puede ser usada. En Java, el ámbito está determinado por las llaves `{ }`.

### Variable local (dentro de un método)

```java
public static void main(String[] args) {
    int x = 10;  // x existe desde aquí
    System.out.println(x);  // Correcto: x está en ámbito
}
// x deja de existir aquí
System.out.println(x);  // Error: x no existe fuera del método
```

### Variable de bloque

```java
public static void main(String[] args) {
    int a = 5;

    {
        int b = 10;       // b existe solo dentro de este bloque
        System.out.println(a);  // Correcto: a sigue en ámbito
        System.out.println(b);  // Correcto: b está en ámbito
    }

    System.out.println(a);  // Correcto: a sigue disponible
    System.out.println(b);  // ERROR: b ya no existe
}
```

### Variables en estructuras de control

```java
public static void main(String[] args) {
    for (int i = 0; i < 5; i++) {
        System.out.println(i);  // Correcto: i existe dentro del for
    }
    System.out.println(i);  // ERROR: i no existe fuera del for

    if (true) {
        int mensaje = 42;  // solo existe en este if
    }
    System.out.println(mensaje);  // ERROR: mensaje no existe aquí
}
```

### Variable de instancia vs. variable local — Shadowing

Cuando una variable local tiene el mismo nombre que una variable de clase, "oculta" (shadow) a la de clase dentro de ese bloque:

```java
public class Persona {
    int edad = 30;  // variable de instancia

    public void mostrar() {
        int edad = 25;  // variable local — hace shadow a la de instancia
        System.out.println(edad);       // 25 (variable local)
        System.out.println(this.edad);  // 30 (variable de instancia, con this)
    }
}
```

---

## 11. Reglas y convenciones para nombrar variables

### Reglas del compilador (obligatorias)

1. El nombre solo puede contener: letras (`a-z`, `A-Z`), dígitos (`0-9`), guión bajo (`_`) y el símbolo dólar (`$`).
2. El nombre no puede comenzar con un dígito.
3. No puede ser una palabra reservada de Java.
4. No puede contener espacios.

```java
int edad = 25;         // CORRECTO
int _contador = 0;     // CORRECTO (pero no recomendado por estilo)
int $valor = 10;       // CORRECTO (pero solo se usa en código generado)
int 2numero = 5;       // ERROR: empieza con número
int mi variable = 3;   // ERROR: contiene espacio
int class = 1;         // ERROR: "class" es una palabra reservada
```

### Palabras reservadas de Java (no puedes usarlas como nombre de variable)

```
abstract  assert    boolean   break     byte      case
catch     char      class     const     continue  default
do        double    else      enum      extends   final
finally   float     for       goto      if        implements
import    instanceof int      interface long      native
new       package   private   protected public    return
short     static    strictfp  super     switch    synchronized
this      throw     throws    transient try       void
volatile  while
```

### Convenciones de nomenclatura (recomendadas)

**Variables y métodos → camelCase**

La primera palabra en minúscula, las siguientes con mayúscula inicial:

```java
int edad = 25;
String nombreCompleto = "Carlos García";
double precioConIVA = 29.99;
boolean estaConectado = true;
int numeroMaximoIntentos = 3;
```

**Nombres descriptivos, no abreviaciones crípticas**

```java
// MAL: ¿qué significa esto?
int n = 5;
double p = 29.99;
String nc = "Ana López";

// BIEN: el nombre explica qué guarda la variable
int numeroDePaginas = 5;
double precioUnitario = 29.99;
String nombreCliente = "Ana López";
```

**Para booleanos, usa prefijos que expresen estado o pregunta**

```java
boolean esMayorDeEdad = true;
boolean tieneCuenta = false;
boolean estaActivo = true;
boolean puedeProcesar = false;
boolean hayErrores = false;
```

**Evita nombres de una sola letra** (excepto en bucles cortos como `i`, `j`, `k`)

```java
// Aceptable en bucles simples:
for (int i = 0; i < 10; i++) { ... }

// No aceptable en general:
int a = 25;
String b = "María";
```

---

## 12. Errores comunes con variables

### Error 1: Usar una variable sin inicializar

```java
public static void main(String[] args) {
    int numero;
    System.out.println(numero); // Error: variable numero might not have been initialized
}

// Solución: inicializar siempre antes de usar
int numero = 0;
System.out.println(numero); // 0
```

### Error 2: Desbordamiento silencioso

```java
int max = Integer.MAX_VALUE;  // 2147483647
int siguiente = max + 1;
System.out.println(siguiente); // -2147483648  ← ¡No es un error, pero es incorrecto!

// Solución: usar long cuando los valores puedan ser grandes
long siguiente = (long) max + 1;
System.out.println(siguiente); // 2147483648  ← Correcto
```

### Error 3: Comparar Strings con ==

```java
String a = new String("Hola");
String b = new String("Hola");
System.out.println(a == b);     // false  ← Compara referencias, no contenido
System.out.println(a.equals(b)); // true  ← Correcto

// Solución: SIEMPRE usa .equals() para comparar Strings
```

### Error 4: NullPointerException al usar un String null

```java
String nombre = null;
System.out.println(nombre.length()); // NullPointerException: no puedes llamar métodos en null

// Solución 1: verificar antes de usar
if (nombre != null) {
    System.out.println(nombre.length());
}

// Solución 2: inicializar con String vacío en lugar de null
String nombre = "";
```

### Error 5: Truncación silenciosa al hacer cast

```java
double precio = 9.99;
int descuento = (int)(precio * 0.1); // 0  ← 0.999 se trunca a 0, no se redondea a 1

// Solución: redondear explícitamente si lo necesitas
int descuento = (int) Math.round(precio * 0.1); // 1  ← correcto
```

### Error 6: Confundir el tipo char con String

```java
char inicial = "A";    // ERROR: "A" es String (comillas dobles), debe ser 'A'
char inicial = 'A';    // CORRECTO: char usa comillas simples

String texto = 'H';    // ERROR: 'H' es char (comillas simples), debe ser "H"
String texto = "H";    // CORRECTO
```

### Error 7: Modificar una constante

```java
final double PI = 3.14159;
PI = 3.14; // Error: cannot assign a value to final variable PI
```

---

## 13. Ejercicios prácticos

### Ejercicio 1 — Ficha personal (Nivel: Básico)

Crea un programa llamado `FichaPersonal.java` que declare variables para almacenar tu nombre, apellido, edad, altura en metros, peso en kilogramos y si estás estudiando actualmente. Luego imprime toda la información con formato usando `printf`.

**Resultado esperado (con tus propios datos):**
```
========== FICHA PERSONAL ==========
Nombre completo : Carlos García
Edad            : 28 años
Altura          : 1.78 m
Peso            : 72.50 kg
Estudiando      : true
=====================================
```

---

### Ejercicio 2 — Calculadora de área (Nivel: Básico)

Crea un programa llamado `CalculadoraArea.java` que calcule e imprima el área de un rectángulo, un círculo y un triángulo usando variables `double`. Define el valor de PI como constante `final`.

**Resultado esperado:**
```
--- Cálculo de áreas ---
Rectángulo (base=5.0, altura=3.0): 15.00
Círculo    (radio=4.0):            50.27
Triángulo  (base=6.0, altura=4.0): 12.00
```

---

### Ejercicio 3 — Conversión de temperatura (Nivel: Básico-Medio)

Crea un programa llamado `ConversionTemperatura.java` que convierta una temperatura dada en Celsius a Fahrenheit y a Kelvin.

**Fórmulas:**
- Fahrenheit = (Celsius × 9/5) + 32
- Kelvin = Celsius + 273.15

**Resultado esperado (con Celsius = 100.0):**
```
Temperatura en Celsius  : 100.0 °C
Temperatura en Fahrenheit: 212.0 °F
Temperatura en Kelvin   : 373.15 K
```

---

### Ejercicio 4 — Operaciones con Strings (Nivel: Medio)

Crea un programa llamado `OperacionesString.java` que reciba el String `"  Bienvenido al Curso de Java 2024  "` y aplique los siguientes métodos, mostrando el resultado de cada uno:

1. Eliminar los espacios al inicio y al final
2. Convertir todo a mayúsculas
3. Convertir todo a minúsculas
4. Contar cuántos caracteres tiene (después de eliminar espacios)
5. Comprobar si contiene la palabra "Java"
6. Reemplazar "Java" por "Python"
7. Extraer solo la palabra "Bienvenido"
8. Mostrar el carácter en la posición 13

---

### Ejercicio 5 — Casting y conversiones (Nivel: Medio)

Crea un programa llamado `ConversionTipos.java` que demuestre:

1. La conversión implícita de `int` a `double`
2. La conversión explícita de `double` a `int` (truncamiento)
3. La misma conversión pero redondeando con `Math.round()`
4. La conversión de un `String` numérico a `int`
5. La conversión de un `int` a `String`
6. Qué pasa con el desbordamiento de `int` al superar `Integer.MAX_VALUE`

Para cada caso, muestra el valor antes y después de la conversión.

---

### Ejercicio 6 — Explorar char (Nivel: Medio)

Crea un programa llamado `ExplorandoChar.java` que:

1. Declare la variable `char letra = 'A'`
2. Imprima su valor numérico (código ASCII)
3. Sume 1 al carácter e imprima el siguiente carácter del alfabeto
4. Imprima las letras del alfabeto de la A a la Z usando una variable `char` que se incrementa
5. Muestre el carácter correspondiente a los códigos 48, 57, 65, 90, 97, 122

---

### Ejercicio 7 — Desafío (Nivel: Avanzado para el tema)

Crea un programa llamado `CalculadoraIMC.java` que calcule el Índice de Masa Corporal (IMC) de una persona.

**Fórmula:** IMC = peso (kg) / altura (m)²

Declara el peso y la altura como constantes `final`. Calcula el IMC y clasifícalo usando las siguientes categorías. Para la clasificación, usa una variable `String` que se asigne según el valor del IMC:

| IMC | Categoría |
|-----|-----------|
| Menos de 18.5 | Bajo peso |
| 18.5 – 24.9 | Peso normal |
| 25.0 – 29.9 | Sobrepeso |
| 30.0 o más | Obesidad |

**Resultado esperado:**
```
========== CALCULADORA DE IMC ==========
Peso   : 72.0 kg
Altura : 1.75 m
IMC    : 23.51
Estado : Peso normal
=========================================
```

*Pista: Para las condiciones necesitarás el `if/else` del Tema 4, pero intenta investigar por tu cuenta como adelanto.*

---

## 14. Resumen del tema

### Tipos primitivos en resumen

```java
byte  b = 100;                    // -128 a 127
short s = 30000;                  // -32,768 a 32,767
int   i = 2_000_000;              // ~-2 mil millones a ~2 mil millones
long  l = 9_000_000_000L;         // números muy grandes, añadir L

float  f = 3.14f;                 // decimal 7 cifras, añadir f
double d = 3.141592653589793;     // decimal 15 cifras (el más usado)

char    c = 'A';                  // un carácter entre comillas simples
boolean flag = true;              // solo true o false
```

### Lo más importante del tema

| Concepto | Regla clave |
|----------|-------------|
| Declarar | `tipo nombre = valor;` |
| Constante | `final TIPO NOMBRE = valor;` (MAYÚSCULAS) |
| Inferencia | `var nombre = valor;` (Java 10+, solo local) |
| Cast explícito | `int x = (int) miDouble;` (puede perder datos) |
| Comparar Strings | Siempre `.equals()`, nunca `==` |
| String nulo | Verificar `!= null` antes de usar métodos |
| Desbordamiento | Elegir el tipo correcto para el rango esperado |
| Naming | `camelCase` para variables, `UPPER_CASE` para constantes |

---

## Próximo tema

**Tema 3: Operadores**

Aprenderás los operadores aritméticos (`+`, `-`, `*`, `/`, `%`), de asignación (`=`, `+=`, `-=`...), de comparación (`==`, `!=`, `>`, `<`...), lógicos (`&&`, `||`, `!`), el operador ternario `? :` y la precedencia de operadores.

---

*Curso de Java — Nivel 1: Fundamentos*
*Tema 2 de 6*
*Creado con fines educativos*
