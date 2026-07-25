# Curso de Java — Nivel 1: Fundamentos
## Tema 4: Estructuras de Control

> **Nivel:** Principiante | **Prerrequisitos:** Temas 1, 2 y 3 completados | **Duración estimada:** 4–5 horas

---

## Tabla de contenidos

1. [¿Qué son las estructuras de control?](#1-qué-son-las-estructuras-de-control)
2. [Estructura if](#2-estructura-if)
3. [Estructura if-else](#3-estructura-if-else)
4. [Estructura if-else if-else](#4-estructura-if-else-if-else)
5. [Estructura switch](#5-estructura-switch)
6. [Switch Expression (Java 14+)](#6-switch-expression-java-14)
7. [Bucle while](#7-bucle-while)
8. [Bucle do-while](#8-bucle-do-while)
9. [Bucle for](#9-bucle-for)
10. [Bucle for-each (Enhanced for)](#10-bucle-for-each-enhanced-for)
11. [Sentencias break y continue](#11-sentencias-break-y-continue)
12. [Bucles anidados](#12-bucles-anidados)
13. [Etiquetas en bucles (Labels)](#13-etiquetas-en-bucles-labels)
14. [Errores comunes con estructuras de control](#14-errores-comunes-con-estructuras-de-control)
15. [Ejercicios prácticos](#15-ejercicios-prácticos)
16. [Resumen del tema](#16-resumen-del-tema)

---

## 1. ¿Qué son las estructuras de control?

Un programa sin estructuras de control ejecuta sus instrucciones en orden lineal, una tras otra, de arriba a abajo. Eso es útil, pero extremadamente limitado. Las **estructuras de control** permiten alterar ese flujo de ejecución de tres maneras fundamentales:

```
FLUJO LINEAL          SELECCIÓN (if/switch)     REPETICIÓN (bucles)
─────────────         ──────────────────────     ───────────────────
  instrucción1          condición ──true──►  A      ┌── condición ◄─┐
      ↓                     │                        └──► instrucción─┘
  instrucción2              └──false──► B
      ↓
  instrucción3
```

Las tres categorías de estructuras de control son:

- **Secuencia:** ejecución línea a línea (ya la conocemos, es el flujo por defecto).
- **Selección:** ejecutar un bloque u otro según una condición. (`if`, `switch`)
- **Iteración:** repetir un bloque de código mientras se cumpla una condición. (`while`, `do-while`, `for`)

Comprender bien estas estructuras es el salto más grande en programación: con ellas, tu programa deja de ser una lista de instrucciones y se convierte en algo que toma decisiones y realiza trabajo repetitivo de forma automática.

---

## 2. Estructura if

La estructura `if` es la más fundamental de todas. Ejecuta un bloque de código **solo si** una condición es verdadera. Si la condición es falsa, el bloque se omite completamente.

### Sintaxis

```java
if (condicion) {
    // Este bloque se ejecuta SOLO si condicion es true
    // Puede tener tantas instrucciones como necesites
}
```

La `condicion` debe ser una expresión que devuelva `boolean` (`true` o `false`).

### Funcionamiento interno

```
  ┌──────────────────────────────┐
  │     Evalúa la condición      │
  └──────────────┬───────────────┘
                 │
        ┌────────┴────────┐
       true             false
        │                  │
        ▼                  ▼
  ┌──────────────┐    (se omite el
  │ Ejecuta el   │     bloque y
  │ bloque if    │     continúa)
  └──────┬───────┘
         │
         ▼
  continúa el programa
```

### Ejemplos progresivos

**Ejemplo básico:**

```java
int temperatura = 35;

if (temperatura > 30) {
    System.out.println("Hace mucho calor hoy.");
    System.out.println("Recuerda hidratarte.");
}
// Si temperatura fuera 25, no se imprimiría nada
```

**Con variables boolean:**

```java
boolean esDomingo = true;

if (esDomingo) {
    System.out.println("Hoy no hay clases.");
}
```

**Con condiciones compuestas:**

```java
int edad = 22;
double salario = 2000.0;

if (edad >= 18 && salario >= 1500.0) {
    System.out.println("Eres elegible para el préstamo.");
}
```

### If con una sola instrucción (sin llaves)

Si el bloque `if` contiene **exactamente una** instrucción, las llaves son técnicamente opcionales:

```java
if (edad >= 18)
    System.out.println("Mayor de edad");
```

Sin embargo, **se recomienda siempre usar llaves** por claridad y para evitar errores al añadir más líneas después:

```java
// TRAMPA CLÁSICA: el programador cree que ambas líneas están en el if
if (edad >= 18)
    System.out.println("Mayor de edad");
    System.out.println("Puede votar");  // ¡Esta línea SIEMPRE se ejecuta!
                                        // No pertenece al if aunque parezca que sí

// CORRECTO con llaves:
if (edad >= 18) {
    System.out.println("Mayor de edad");
    System.out.println("Puede votar");  // Ahora sí pertenece al if
}
```

---

## 3. Estructura if-else

Añade una alternativa: si la condición es verdadera ejecuta el primer bloque; si es falsa, ejecuta el segundo. Siempre se ejecuta uno de los dos bloques, nunca los dos, nunca ninguno.

### Sintaxis

```java
if (condicion) {
    // Se ejecuta si condicion es true
} else {
    // Se ejecuta si condicion es false
}
```

### Funcionamiento interno

```
  ┌──────────────────────────────┐
  │     Evalúa la condición      │
  └──────────────┬───────────────┘
                 │
        ┌────────┴────────┐
       true             false
        │                  │
        ▼                  ▼
  ┌──────────────┐   ┌──────────────┐
  │  Bloque if   │   │ Bloque else  │
  └──────┬───────┘   └──────┬───────┘
         └─────────┬─────────┘
                   ▼
           continúa el programa
```

### Ejemplos

**Par o impar:**

```java
int numero = 17;

if (numero % 2 == 0) {
    System.out.println(numero + " es par");
} else {
    System.out.println(numero + " es impar");  // Se imprime esto
}
```

**Sistema de aprobación:**

```java
double nota = 4.8;

if (nota >= 5.0) {
    System.out.println("APROBADO ✓");
    System.out.println("Nota: " + nota);
} else {
    System.out.println("REPROBADO ✗");
    System.out.println("Nota insuficiente: " + nota);
}
```

**Comparación de valores:**

```java
int a = 45;
int b = 78;

if (a > b) {
    System.out.println("El mayor es: " + a);
} else {
    System.out.println("El mayor es: " + b);
}
// Salida: El mayor es: 78
```

**Verificación de null antes de usar un objeto:**

```java
String nombre = null;

if (nombre != null) {
    System.out.println("Hola, " + nombre.toUpperCase());
} else {
    System.out.println("No se proporcionó un nombre.");
}
```

---

## 4. Estructura if-else if-else

Permite evaluar múltiples condiciones en cadena. Java las evalúa de arriba a abajo y ejecuta el bloque de la **primera condición que sea verdadera**. Si ninguna lo es, ejecuta el bloque `else` final (si existe).

### Sintaxis

```java
if (condicion1) {
    // Se ejecuta si condicion1 es true
} else if (condicion2) {
    // Se ejecuta si condicion1 es false y condicion2 es true
} else if (condicion3) {
    // Se ejecuta si condicion1 y condicion2 son false y condicion3 es true
} else {
    // Se ejecuta si NINGUNA condición anterior fue true
    // El bloque else final es opcional
}
```

### Importante: solo se ejecuta UN bloque

Una vez que Java encuentra la primera condición verdadera, ejecuta su bloque y **salta todo lo demás**. No importa si otras condiciones también serían verdaderas.

### Ejemplo — Calificaciones

```java
double nota = 7.5;

if (nota >= 9.0) {
    System.out.println("Sobresaliente");
} else if (nota >= 7.0) {
    System.out.println("Notable");          // Se ejecuta esto
} else if (nota >= 5.0) {
    System.out.println("Aprobado");
} else if (nota >= 3.0) {
    System.out.println("Insuficiente");
} else {
    System.out.println("Muy deficiente");
}
```

### Ejemplo — Rango de edades

```java
int edad = 17;

if (edad < 0) {
    System.out.println("Edad no válida");
} else if (edad < 13) {
    System.out.println("Niño");
} else if (edad < 18) {
    System.out.println("Adolescente");     // Se ejecuta esto
} else if (edad < 65) {
    System.out.println("Adulto");
} else {
    System.out.println("Adulto mayor");
}
```

### Ejemplo — Índice de Masa Corporal (IMC)

```java
double peso = 72.0;   // kg
double altura = 1.75; // m
double imc = peso / (altura * altura);

System.out.printf("IMC: %.2f%n", imc);  // IMC: 23.51

if (imc < 18.5) {
    System.out.println("Categoría: Bajo peso");
} else if (imc < 25.0) {
    System.out.println("Categoría: Peso normal");   // Se ejecuta esto
} else if (imc < 30.0) {
    System.out.println("Categoría: Sobrepeso");
} else {
    System.out.println("Categoría: Obesidad");
}
```

### If anidados

Un `if` puede contener otro `if` dentro. Se llaman **if anidados**:

```java
int edad = 25;
boolean tieneLicencia = true;

if (edad >= 18) {
    System.out.println("Es mayor de edad.");
    if (tieneLicencia) {
        System.out.println("Puede conducir.");
    } else {
        System.out.println("No tiene licencia de conducir.");
    }
} else {
    System.out.println("Es menor de edad, no puede conducir.");
}
```

Evita demasiados niveles de anidamiento (más de 2-3 niveles hace el código difícil de leer). Si necesitas muchos niveles, considera refactorizar en métodos separados.

---

## 5. Estructura switch

La estructura `switch` es una alternativa al `if-else if` cuando necesitas comparar **una sola variable o expresión** contra múltiples valores concretos (no rangos). Es más legible cuando hay muchos casos.

### Sintaxis

```java
switch (expresion) {
    case valor1:
        // instrucciones si expresion == valor1
        break;
    case valor2:
        // instrucciones si expresion == valor2
        break;
    case valor3:
    case valor4:
        // instrucciones si expresion == valor3 O valor4 (casos agrupados)
        break;
    default:
        // instrucciones si ningún case coincide (opcional)
}
```

### Tipos permitidos en el switch (Java clásico)

- `byte`, `short`, `int`, `char`
- `String` (desde Java 7)
- Tipos envolventes: `Byte`, `Short`, `Integer`, `Character`
- `enum` (desde Java 5)
- **NO** se puede usar: `long`, `float`, `double`, `boolean`

### El papel crucial de break

La sentencia `break` es fundamental en `switch`. Sin ella, Java continúa ejecutando los casos siguientes aunque no coincidan. Esto se llama **fall-through** (caída).

**Con break (comportamiento esperado):**

```java
int dia = 3;

switch (dia) {
    case 1:
        System.out.println("Lunes");
        break;
    case 2:
        System.out.println("Martes");
        break;
    case 3:
        System.out.println("Miércoles");   // Solo esto se ejecuta
        break;
    case 4:
        System.out.println("Jueves");
        break;
    case 5:
        System.out.println("Viernes");
        break;
    default:
        System.out.println("Fin de semana");
}
// Salida: Miércoles
```

**Sin break (fall-through):**

```java
int dia = 3;

switch (dia) {
    case 1:
        System.out.println("Lunes");
    case 2:
        System.out.println("Martes");
    case 3:
        System.out.println("Miércoles");   // Empieza aquí (case 3 coincide)
    case 4:
        System.out.println("Jueves");      // TAMBIÉN se ejecuta (fall-through)
    case 5:
        System.out.println("Viernes");     // TAMBIÉN se ejecuta
    default:
        System.out.println("Fin de semana"); // TAMBIÉN se ejecuta
}
// Salida:
// Miércoles
// Jueves
// Viernes
// Fin de semana
```

### Fall-through intencional

A veces el fall-through es útil para agrupar casos que comparten el mismo comportamiento:

```java
int mes = 4;
int diasEnElMes;

switch (mes) {
    case 1: case 3: case 5: case 7:
    case 8: case 10: case 12:
        diasEnElMes = 31;
        break;
    case 4: case 6: case 9: case 11:
        diasEnElMes = 30;    // Abril (mes 4) llega aquí
        break;
    case 2:
        diasEnElMes = 28;    // Simplificado (sin considerar bisiesto)
        break;
    default:
        diasEnElMes = -1;    // Mes inválido
}
System.out.println("Días en el mes: " + diasEnElMes); // 30
```

### Switch con String

```java
String idioma = "español";

switch (idioma) {
    case "español":
        System.out.println("Hola");
        break;
    case "inglés":
        System.out.println("Hello");
        break;
    case "francés":
        System.out.println("Bonjour");
        break;
    case "alemán":
        System.out.println("Hallo");
        break;
    default:
        System.out.println("Idioma no reconocido");
}
// Salida: Hola
```

### Switch con enum

```java
enum Estacion { PRIMAVERA, VERANO, OTONO, INVIERNO }

Estacion estacion = Estacion.VERANO;

switch (estacion) {
    case PRIMAVERA:
        System.out.println("Florecen las flores.");
        break;
    case VERANO:
        System.out.println("Hace calor, tiempo de playa.");  // Se ejecuta
        break;
    case OTONO:
        System.out.println("Caen las hojas.");
        break;
    case INVIERNO:
        System.out.println("Hace frío, tiempo de nieve.");
        break;
}
```

---

## 6. Switch Expression (Java 14+)

A partir de **Java 14** (y estable en Java 16), Java introduce el **switch expression**, una forma moderna y más segura del switch tradicional. Sus ventajas:

- Usa `->` en lugar de `:` y `break`, eliminando el riesgo de fall-through accidental.
- Puede devolver un valor directamente.
- El compilador verifica que todos los casos posibles estén cubiertos.
- Es mucho más conciso.

### Sintaxis con arrow (->)

```java
// Como expresión que devuelve un valor
String resultado = switch (expresion) {
    case valor1 -> "texto1";
    case valor2 -> "texto2";
    case valor3, valor4 -> "texto3 o 4";  // múltiples valores en un case
    default -> "otro";
};
```

### Comparación: switch clásico vs. switch expression

```java
// CLÁSICO: verboso, propenso a errores por fall-through
int dia = 3;
String nombreDia;
switch (dia) {
    case 1:  nombreDia = "Lunes";      break;
    case 2:  nombreDia = "Martes";     break;
    case 3:  nombreDia = "Miércoles";  break;
    case 4:  nombreDia = "Jueves";     break;
    case 5:  nombreDia = "Viernes";    break;
    case 6:  nombreDia = "Sábado";     break;
    case 7:  nombreDia = "Domingo";    break;
    default: nombreDia = "Inválido";
}

// MODERNO: conciso, seguro, sin fall-through
String nombreDia = switch (dia) {
    case 1 -> "Lunes";
    case 2 -> "Martes";
    case 3 -> "Miércoles";
    case 4 -> "Jueves";
    case 5 -> "Viernes";
    case 6 -> "Sábado";
    case 7 -> "Domingo";
    default -> "Inválido";
};
System.out.println(nombreDia); // Miércoles
```

### Switch expression con bloques y yield

Si necesitas ejecutar varias instrucciones en un case, usa un bloque `{ }` con `yield` para devolver el valor:

```java
int mes = 2;
int año = 2024;

int diasDelMes = switch (mes) {
    case 1, 3, 5, 7, 8, 10, 12 -> 31;
    case 4, 6, 9, 11 -> 30;
    case 2 -> {
        // Lógica para determinar si es bisiesto
        boolean esBisiesto = (año % 4 == 0 && año % 100 != 0) || (año % 400 == 0);
        yield esBisiesto ? 29 : 28;   // yield devuelve el valor del bloque
    }
    default -> throw new IllegalArgumentException("Mes inválido: " + mes);
};
System.out.println("Días en el mes: " + diasDelMes); // 29 (2024 es bisiesto)
```

### Switch con Pattern Matching (Java 21+)

Java 21 introduce el Pattern Matching en switch, que permite hacer comparaciones de tipo:

```java
Object obj = 42;

String descripcion = switch (obj) {
    case Integer i when i > 0 -> "Entero positivo: " + i;
    case Integer i            -> "Entero no positivo: " + i;
    case String s             -> "Texto: " + s;
    case null                 -> "Es nulo";
    default                   -> "Otro tipo";
};
System.out.println(descripcion); // Entero positivo: 42
```

---

## 7. Bucle while

El bucle `while` repite un bloque de código **mientras** una condición sea verdadera. Antes de cada iteración verifica la condición; si es falsa desde el principio, el bloque no se ejecuta ni una vez.

### Sintaxis

```java
while (condicion) {
    // Bloque que se repite mientras condicion sea true
    // Debe haber algo que eventualmente haga la condición false
    // para evitar un bucle infinito
}
```

### Funcionamiento interno

```
  ┌──────────────────────────┐
  │    Evalúa condición      │ ◄──────────────┐
  └──────────┬───────────────┘                │
             │                                │
    ┌────────┴────────┐                       │
   true             false                     │
    │                  │                      │
    ▼                  ▼                      │
  ┌──────────────┐  (sale del                 │
  │ Ejecuta el   │   bucle)                   │
  │ bloque       │                            │
  └──────┬───────┘                            │
         │                                    │
         └────────────────────────────────────┘
```

### Ejemplo básico — Conteo

```java
int contador = 1;

while (contador <= 5) {
    System.out.println("Iteración: " + contador);
    contador++;  // Si olvidas esto, el bucle es infinito
}
// Salida:
// Iteración: 1
// Iteración: 2
// Iteración: 3
// Iteración: 4
// Iteración: 5
```

### Ejemplo — Suma de dígitos

```java
int numero = 12345;
int suma = 0;

while (numero > 0) {
    suma += numero % 10;   // Obtiene el último dígito y lo suma
    numero /= 10;          // Elimina el último dígito
}
System.out.println("Suma de dígitos: " + suma); // 15 (1+2+3+4+5)
```

### Ejemplo — Validación de entrada (patrón común)

El `while` es ideal para repetir una acción hasta que el usuario proporcione una entrada válida:

```java
import java.util.Scanner;

Scanner scanner = new Scanner(System.in);
int edad = -1;

while (edad < 0 || edad > 150) {
    System.out.print("Ingresa tu edad (0-150): ");
    edad = scanner.nextInt();
    if (edad < 0 || edad > 150) {
        System.out.println("Edad no válida. Intenta de nuevo.");
    }
}
System.out.println("Edad registrada: " + edad);
```

### Bucle while infinito controlado con break

A veces es útil crear un bucle `while(true)` y salir con `break` cuando se cumpla una condición:

```java
int intentos = 0;
final int MAX_INTENTOS = 3;

while (true) {
    intentos++;
    System.out.println("Intento " + intentos);

    if (intentos >= MAX_INTENTOS) {
        System.out.println("Se agotaron los intentos.");
        break;   // Sale del bucle
    }
}
```

### ¿Cuándo usar while?

Usa `while` cuando **no sabes de antemano cuántas veces** se repetirá el bucle. La cantidad de repeticiones depende de una condición que puede cumplirse en cualquier momento.

---

## 8. Bucle do-while

Similar al `while`, pero con una diferencia clave: **evalúa la condición al final**, después de ejecutar el bloque. Esto garantiza que el bloque se ejecute **al menos una vez**, sin importar si la condición es verdadera o falsa.

### Sintaxis

```java
do {
    // Bloque que se ejecuta AL MENOS UNA VEZ
    // y luego se repite mientras condicion sea true
} while (condicion);  // ← Nota el punto y coma al final
```

### Funcionamiento interno

```
  ┌──────────────────────────┐
  │    Ejecuta el bloque     │ ◄──────────────┐
  └──────────┬───────────────┘                │
             │                                │
  ┌──────────▼───────────────┐                │
  │    Evalúa condición      │                │
  └──────────┬───────────────┘                │
             │                                │
    ┌────────┴────────┐                       │
   true             false                     │
    │                  │                      │
    └──────────────────┘ (sale)               │
             │                                │
             └────────────────────────────────┘
```

### Comparación while vs. do-while

```java
// WHILE: si la condición es false desde el inicio, no ejecuta nada
int x = 10;
while (x < 5) {
    System.out.println("while: " + x);  // No se imprime nada
}

// DO-WHILE: siempre ejecuta al menos una vez
int y = 10;
do {
    System.out.println("do-while: " + y);  // Se imprime "do-while: 10"
} while (y < 5);
```

### Ejemplo — Menú de opciones (caso de uso clásico)

El `do-while` es perfecto para menús: siempre muestras el menú al menos una vez y repites hasta que el usuario elija salir.

```java
import java.util.Scanner;

Scanner sc = new Scanner(System.in);
int opcion;

do {
    System.out.println("\n===== MENÚ =====");
    System.out.println("1. Ver saldo");
    System.out.println("2. Depositar");
    System.out.println("3. Retirar");
    System.out.println("4. Salir");
    System.out.print("Elige una opción: ");
    opcion = sc.nextInt();

    switch (opcion) {
        case 1 -> System.out.println("Tu saldo es: $1,500.00");
        case 2 -> System.out.println("Depósito realizado.");
        case 3 -> System.out.println("Retiro realizado.");
        case 4 -> System.out.println("¡Hasta luego!");
        default -> System.out.println("Opción no válida.");
    }
} while (opcion != 4);
```

### Ejemplo — Cuenta regresiva

```java
int cuenta = 5;

do {
    System.out.println(cuenta);
    cuenta--;
} while (cuenta > 0);

System.out.println("¡Despegue!");
// Salida: 5, 4, 3, 2, 1, ¡Despegue!
```

### ¿Cuándo usar do-while?

Usa `do-while` cuando necesitas que el bloque se ejecute **obligatoriamente al menos una vez** antes de verificar la condición. El caso de uso más común es el menú interactivo.

---

## 9. Bucle for

El bucle `for` es el más usado cuando sabes **exactamente cuántas veces** quieres repetir algo. Concentra en una sola línea la inicialización, la condición y la actualización del contador.

### Sintaxis

```java
for (inicialización; condicion; actualización) {
    // Bloque que se repite mientras condicion sea true
}
```

Las tres partes se separan con punto y coma:

1. **Inicialización:** se ejecuta UNA SOLA VEZ al principio (antes del primer ciclo).
2. **Condición:** se verifica ANTES de cada iteración. Si es false, el bucle termina.
3. **Actualización:** se ejecuta DESPUÉS de cada iteración del bloque.

### Flujo de ejecución del for

```
  Inicialización
       │
       ▼
  ┌────────────┐
  │  Condición │ ──false──► (sale del bucle)
  └─────┬──────┘
       true
        │
        ▼
  ┌────────────┐
  │   Bloque   │
  └─────┬──────┘
        │
        ▼
  Actualización
        │
        └────────────────────► (vuelve a Condición)
```

### Ejemplos básicos

**Conteo del 1 al 10:**

```java
for (int i = 1; i <= 10; i++) {
    System.out.print(i + " ");
}
// Salida: 1 2 3 4 5 6 7 8 9 10
```

**Conteo regresivo:**

```java
for (int i = 10; i >= 1; i--) {
    System.out.print(i + " ");
}
System.out.println("¡Ya!");
// Salida: 10 9 8 7 6 5 4 3 2 1 ¡Ya!
```

**Paso de 2 en 2:**

```java
for (int i = 0; i <= 20; i += 2) {
    System.out.print(i + " ");
}
// Salida: 0 2 4 6 8 10 12 14 16 18 20
```

### Ejemplos prácticos

**Tabla de multiplicar:**

```java
int numero = 7;

System.out.println("Tabla del " + numero + ":");
for (int i = 1; i <= 10; i++) {
    System.out.printf("%d × %d = %d%n", numero, i, numero * i);
}
// Salida:
// 7 × 1 = 7
// 7 × 2 = 14
// ...
// 7 × 10 = 70
```

**Suma de los primeros N números:**

```java
int n = 100;
int suma = 0;

for (int i = 1; i <= n; i++) {
    suma += i;
}
System.out.println("Suma del 1 al " + n + ": " + suma); // 5050
```

**Factorial:**

```java
int n = 6;
long factorial = 1;

for (int i = 2; i <= n; i++) {
    factorial *= i;
}
System.out.println(n + "! = " + factorial); // 6! = 720
```

**Recorrer un arreglo:**

```java
int[] numeros = {10, 25, 7, 42, 13};

for (int i = 0; i < numeros.length; i++) {
    System.out.println("numeros[" + i + "] = " + numeros[i]);
}
```

### Variaciones del for

**Múltiples variables en el for:**

```java
for (int i = 0, j = 10; i <= j; i++, j--) {
    System.out.println("i=" + i + ", j=" + j);
}
// i=0,j=10  →  i=1,j=9  →  i=2,j=8  →  ...  →  i=5,j=5
```

**For con partes vacías:**

```java
// Inicialización fuera del for
int i = 0;
for (; i < 5; i++) {
    System.out.print(i + " ");
}

// Bucle infinito con for (equivalente a while(true))
for (;;) {
    // se ejecuta infinitamente hasta un break
    break;
}
```

**La variable del for no existe fuera de él:**

```java
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}
System.out.println(i);  // ERROR: i no existe aquí
```

---

## 10. Bucle for-each (Enhanced for)

El bucle `for-each` (también llamado "enhanced for" o "for mejorado") es una versión simplificada del `for` diseñada para **recorrer colecciones y arreglos** de forma más legible. No necesitas manejar el índice manualmente.

### Sintaxis

```java
for (tipo variable : coleccion) {
    // variable toma el valor de cada elemento en cada iteración
}
```

Se lee: "Para cada `variable` en `coleccion`..."

### Ejemplos con arrays

**Recorrer un array de enteros:**

```java
int[] numeros = {5, 10, 15, 20, 25};

for (int numero : numeros) {
    System.out.println(numero);
}
// Salida: 5  10  15  20  25
```

**Sumar todos los elementos:**

```java
int[] valores = {3, 7, 2, 9, 4, 6};
int suma = 0;

for (int valor : valores) {
    suma += valor;
}
System.out.println("Suma total: " + suma); // 31
```

**Con array de Strings:**

```java
String[] frutas = {"Manzana", "Banana", "Cereza", "Durazno"};

for (String fruta : frutas) {
    System.out.println("Fruta: " + fruta);
}
```

**Con colecciones (ArrayList):**

```java
import java.util.ArrayList;

ArrayList<String> ciudades = new ArrayList<>();
ciudades.add("Madrid");
ciudades.add("Barcelona");
ciudades.add("Valencia");

for (String ciudad : ciudades) {
    System.out.println("Ciudad: " + ciudad);
}
```

### Comparación: for clásico vs. for-each

```java
int[] datos = {4, 8, 15, 16, 23, 42};

// FOR clásico (cuando necesitas el índice)
for (int i = 0; i < datos.length; i++) {
    System.out.println("datos[" + i + "] = " + datos[i]);
}

// FOR-EACH (cuando solo necesitas el valor, no el índice)
for (int dato : datos) {
    System.out.println(dato);
}
```

### Limitaciones del for-each

El for-each es más legible, pero tiene restricciones:

1. **No puedes modificar el arreglo original** (la variable es una copia del valor):

```java
int[] numeros = {1, 2, 3, 4, 5};
for (int n : numeros) {
    n = n * 2;  // Modifica la copia local, NO el arreglo original
}
// numeros sigue siendo {1, 2, 3, 4, 5}

// Para modificar el arreglo, usa el for clásico:
for (int i = 0; i < numeros.length; i++) {
    numeros[i] = numeros[i] * 2;  // Modifica el arreglo real
}
```

2. **No tienes acceso al índice** actual (si lo necesitas, usa el for clásico).
3. **Solo avanza hacia adelante** (no puedes recorrerlo en reversa directamente).

---

## 11. Sentencias break y continue

### 11.1 break — Salir del bucle inmediatamente

La sentencia `break` termina el bucle más cercano que lo contiene (`for`, `while`, `do-while` o `switch`) y continúa la ejecución después de él.

```java
// Buscar el primer número divisible por 7
for (int i = 1; i <= 100; i++) {
    if (i % 7 == 0) {
        System.out.println("Primer múltiplo de 7: " + i);
        break;  // Encontramos lo que buscábamos, salimos del bucle
    }
}
// Salida: Primer múltiplo de 7: 7
```

**Ejemplo — Búsqueda en un array:**

```java
int[] numeros = {4, 9, 2, 7, 11, 5, 8};
int buscar = 7;
boolean encontrado = false;

for (int i = 0; i < numeros.length; i++) {
    if (numeros[i] == buscar) {
        System.out.println(buscar + " encontrado en la posición " + i);
        encontrado = true;
        break;  // Ya lo encontramos, no tiene sentido seguir buscando
    }
}

if (!encontrado) {
    System.out.println(buscar + " no está en el arreglo");
}
```

**Ejemplo — Validación de entrada con break:**

```java
Scanner sc = new Scanner(System.in);

while (true) {
    System.out.print("Ingresa un número positivo: ");
    int n = sc.nextInt();

    if (n > 0) {
        System.out.println("Número aceptado: " + n);
        break;
    }
    System.out.println("El número debe ser positivo.");
}
```

### 11.2 continue — Saltar a la siguiente iteración

La sentencia `continue` no sale del bucle: **omite el resto del bloque en la iteración actual** y pasa directamente a la siguiente iteración (verificando la condición de nuevo).

```java
// Imprimir solo números impares del 1 al 10
for (int i = 1; i <= 10; i++) {
    if (i % 2 == 0) {
        continue;  // Si es par, salta esta iteración
    }
    System.out.print(i + " ");  // Solo se llega aquí si i es impar
}
// Salida: 1 3 5 7 9
```

**Ejemplo — Omitir valores específicos:**

```java
int[] datos = {3, -1, 7, -5, 2, 8, -3, 9};
int sumaPositivos = 0;

for (int dato : datos) {
    if (dato < 0) {
        continue;  // Omitir negativos
    }
    sumaPositivos += dato;
}
System.out.println("Suma de positivos: " + sumaPositivos); // 29
```

**Ejemplo — Omitir un elemento concreto:**

```java
String[] nombres = {"Ana", "Luis", "Carlos", "María", "Luis", "Pedro"};

System.out.println("Nombres (sin repetir Luis):");
for (String nombre : nombres) {
    if (nombre.equals("Luis")) {
        continue;
    }
    System.out.println(nombre);
}
// Salida: Ana, Carlos, María, Pedro
```

### Diferencia visual entre break y continue

```
BREAK:                          CONTINUE:
  iteración 1 ✓                   iteración 1 ✓
  iteración 2 ✓                   iteración 2 ✓
  iteración 3 → BREAK             iteración 3 → CONTINUE (salta al 4)
  [sale del bucle]                iteración 4 ✓
  código después del bucle        iteración 5 ✓
                                  [fin normal del bucle]
```

---

## 12. Bucles anidados

Un **bucle anidado** es un bucle dentro de otro bucle. Por cada iteración del bucle externo, el bucle interno se ejecuta completamente.

### Ejemplo básico

```java
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        System.out.print("(" + i + "," + j + ") ");
    }
    System.out.println();  // Salto de línea después de cada fila
}
// Salida:
// (1,1) (1,2) (1,3)
// (2,1) (2,2) (2,3)
// (3,1) (3,2) (3,3)
```

El número total de iteraciones es el producto de las iteraciones de cada bucle. En este caso: 3 × 3 = 9 iteraciones.

### Ejemplo — Tabla de multiplicar completa

```java
System.out.println("TABLA DE MULTIPLICAR");
System.out.println("====================");

// Encabezado
System.out.printf("%4s", "×");
for (int j = 1; j <= 10; j++) {
    System.out.printf("%4d", j);
}
System.out.println();

// Cuerpo de la tabla
for (int i = 1; i <= 10; i++) {
    System.out.printf("%4d", i);
    for (int j = 1; j <= 10; j++) {
        System.out.printf("%4d", i * j);
    }
    System.out.println();
}
```

### Ejemplo — Figura de asteriscos (triángulo)

```java
int filas = 5;

// Triángulo hacia arriba
for (int i = 1; i <= filas; i++) {
    for (int j = 1; j <= i; j++) {
        System.out.print("* ");
    }
    System.out.println();
}
// Salida:
// *
// * *
// * * *
// * * * *
// * * * * *

// Triángulo invertido
for (int i = filas; i >= 1; i--) {
    for (int j = 1; j <= i; j++) {
        System.out.print("* ");
    }
    System.out.println();
}
// Salida:
// * * * * *
// * * * *
// * * *
// * *
// *
```

### Ejemplo — Pirámide centrada

```java
int filas = 5;

for (int i = 1; i <= filas; i++) {
    // Espacios antes de los asteriscos
    for (int s = filas - i; s > 0; s--) {
        System.out.print(" ");
    }
    // Asteriscos de la fila
    for (int j = 1; j <= (2 * i - 1); j++) {
        System.out.print("*");
    }
    System.out.println();
}
// Salida:
//     *
//    ***
//   *****
//  *******
// *********
```

### Ejemplo — Números primos hasta N

```java
int limite = 50;
System.out.print("Primos hasta " + limite + ": ");

for (int n = 2; n <= limite; n++) {
    boolean esPrimo = true;

    for (int divisor = 2; divisor <= Math.sqrt(n); divisor++) {
        if (n % divisor == 0) {
            esPrimo = false;
            break;  // Break sale solo del bucle interno
        }
    }

    if (esPrimo) {
        System.out.print(n + " ");
    }
}
// Salida: 2 3 5 7 11 13 17 19 23 29 31 37 41 43 47
```

---

## 13. Etiquetas en bucles (Labels)

Cuando tienes bucles anidados, `break` y `continue` solo afectan al bucle más interno. Las **etiquetas** te permiten indicar exactamente a qué bucle quieres aplicar el `break` o `continue`.

### Sintaxis

```java
etiqueta:
for (...) {
    for (...) {
        break etiqueta;     // Sale del bucle marcado con "etiqueta"
        continue etiqueta;  // Va a la siguiente iteración del bucle "etiqueta"
    }
}
```

### Ejemplo — break con etiqueta

```java
// Buscar un par (i, j) tal que i * j == 12
boolean encontrado = false;

externo:
for (int i = 1; i <= 5; i++) {
    for (int j = 1; j <= 5; j++) {
        if (i * j == 12) {
            System.out.println("Encontrado: " + i + " × " + j + " = 12");
            break externo;  // Sale de AMBOS bucles, no solo del interno
        }
    }
}
// Sin la etiqueta, el break solo saldría del bucle interno
// y el externo continuaría buscando más combinaciones
```

### Ejemplo — continue con etiqueta

```java
externo:
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        if (j == 2) {
            continue externo;  // Salta al siguiente i, no al siguiente j
        }
        System.out.println("i=" + i + " j=" + j);
    }
}
// Salida:
// i=1 j=1   (cuando j=2, salta directo al siguiente i)
// i=2 j=1
// i=3 j=1
```

Las etiquetas se usan raramente en código moderno. Antes de usarlas, considera si el problema se puede resolver de otra manera (por ejemplo, con un método que retorne cuando encuentre el resultado).

---

## 14. Errores comunes con estructuras de control

### Error 1: Punto y coma después del if o while (if/while vacío)

```java
int x = 5;

// ERROR SILENCIOSO: el punto y coma termina el if inmediatamente
if (x > 0);  // ← Este punto y coma es el "cuerpo" del if (vacío)
{
    System.out.println("Positivo");  // Esto SIEMPRE se ejecuta, no pertenece al if
}

// CORRECTO:
if (x > 0) {
    System.out.println("Positivo");
}
```

```java
// ERROR: bucle while vacío con punto y coma
int i = 0;
while (i++ < 5);    // El bucle hace sus 5 iteraciones pero sin hacer nada visible
System.out.println("Valor de i: " + i);  // Esto solo se ejecuta una vez al final
```

### Error 2: Bucle infinito por no actualizar la variable

```java
int i = 0;
while (i < 10) {
    System.out.println(i);
    // OLVIDÉ el i++  → bucle infinito
}

// CORRECTO:
while (i < 10) {
    System.out.println(i);
    i++;
}
```

### Error 3: Olvidar el break en switch

```java
int opcion = 2;

switch (opcion) {
    case 1:
        System.out.println("Uno");
    case 2:
        System.out.println("Dos");   // Se imprime esto
    case 3:
        System.out.println("Tres");  // ¡TAMBIÉN se imprime! (fall-through)
    default:
        System.out.println("Otro");  // ¡TAMBIÉN se imprime!
}
// Salida no deseada: Dos, Tres, Otro
```

### Error 4: Error de uno (off-by-one)

Es uno de los errores más comunes: el bucle itera una vez de más o de menos.

```java
int[] array = {10, 20, 30, 40, 50};

// ERROR: array.length = 5, índices válidos: 0-4
for (int i = 0; i <= array.length; i++) {  // <= en lugar de <
    System.out.println(array[i]);  // ArrayIndexOutOfBoundsException cuando i=5
}

// CORRECTO:
for (int i = 0; i < array.length; i++) {
    System.out.println(array[i]);
}
```

### Error 5: Comparar con = en lugar de ==

```java
int x = 5;

// Java lo detecta como error de compilación (a diferencia de C/C++)
if (x = 10) { ... }   // Error: tipo int no es boolean

// CORRECTO:
if (x == 10) { ... }
```

### Error 6: Modificar la variable de iteración del for-each

```java
int[] numeros = {1, 2, 3, 4, 5};

// No modifica el arreglo original
for (int n : numeros) {
    n = n * 2;  // Solo modifica la copia local
}
// numeros sigue siendo {1, 2, 3, 4, 5}

// Para modificar, usar for clásico:
for (int i = 0; i < numeros.length; i++) {
    numeros[i] = numeros[i] * 2;
}
```

### Error 7: Condición que nunca es false (bucle infinito lógico)

```java
// Si se olvida el break en un while(true)
int intentos = 0;
while (true) {
    intentos++;
    System.out.println("Intento: " + intentos);
    if (intentos >= 3) {
        // Se olvidó el break  → el bucle sigue para siempre
    }
}

// CORRECTO:
while (true) {
    intentos++;
    System.out.println("Intento: " + intentos);
    if (intentos >= 3) {
        break;
    }
}
```

---

## 15. Ejercicios prácticos

### Ejercicio 1 — Clasificador de números (Nivel: Básico)

Crea un programa `ClasificadorNumeros.java` que dado `int numero = -7` determine e imprima:
1. Si es positivo, negativo o cero
2. Si es par o impar
3. Si tiene un solo dígito (entre -9 y 9 inclusive)
4. Si es múltiplo de 3 y de 5 al mismo tiempo

---

### Ejercicio 2 — Calculadora con menú (Nivel: Básico-Medio)

Crea un programa `MenuCalculadora.java` que use un `do-while` para mostrar repetidamente un menú con las opciones: sumar, restar, multiplicar, dividir y salir. Para cada operación declara dos variables `double` y muestra el resultado. El programa termina cuando el usuario elige "Salir". Usa `switch` o `switch expression` para manejar las opciones.

---

### Ejercicio 3 — Secuencia de Fibonacci (Nivel: Medio)

Crea un programa `Fibonacci.java` que use un bucle `for` para imprimir los primeros 15 números de la secuencia de Fibonacci: 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377.

*Pista: Cada número es la suma de los dos anteriores. Necesitarás dos variables para recordar los dos últimos valores.*

---

### Ejercicio 4 — Estadísticas de un arreglo (Nivel: Medio)

Crea un programa `EstadisticasArray.java` con el arreglo `int[] datos = {45, 12, 78, 23, 56, 89, 34, 67, 11, 90}`.

Usando bucles, calcula y muestra:
1. El valor máximo
2. El valor mínimo
3. La suma total
4. El promedio
5. Cuántos números son mayores que el promedio
6. Cuántos son pares y cuántos impares

---

### Ejercicio 5 — Pirámide de números (Nivel: Medio)

Crea un programa `PiramideNumeros.java` que con `int filas = 5` imprima el siguiente patrón usando bucles anidados:

```
1
1 2
1 2 3
1 2 3 4
1 2 3 4 5
```

Y también el patrón inverso:

```
1 2 3 4 5
1 2 3 4
1 2 3
1 2
1
```

---

### Ejercicio 6 — Números primos y perfectos (Nivel: Medio-Avanzado)

Crea un programa `NumerosPrimosYPerfectos.java` que recorra los números del 1 al 100 y:

1. Imprima todos los números primos (divisibles solo por 1 y por sí mismos)
2. Imprima todos los números perfectos (un número perfecto es igual a la suma de sus divisores propios: ej. 6 = 1+2+3)

*Pista: Para números perfectos, los divisores propios de 6 son 1, 2 y 3 (no incluye el mismo 6).*

---

### Ejercicio 7 — Desafío: adivina el número (Nivel: Avanzado)

Crea un programa `AdivinaElNumero.java` que simule un juego:

- El número secreto es `int secreto = 42`
- El jugador tiene máximo 7 intentos
- En cada intento se lee un número del usuario
- Si es demasiado alto, muestra "Demasiado alto"
- Si es demasiado bajo, muestra "Demasiado bajo"
- Si acierta, muestra "¡Correcto! Lo lograste en X intentos" y termina
- Si se acaban los intentos sin acertar, muestra "Perdiste. El número era 42"
- Usa `while` o `do-while` para los intentos y estructuras `if-else` para las pistas

---

## 16. Resumen del tema

### Cuándo usar cada estructura

| Estructura | Úsala cuando... |
|------------|-----------------|
| `if` | Quieres ejecutar algo solo si una condición es verdadera |
| `if-else` | Tienes exactamente dos caminos posibles |
| `if-else if-else` | Tienes tres o más condiciones que evaluar en secuencia |
| `switch` | Comparas una variable contra múltiples valores concretos |
| `switch expression` | Igual que switch pero necesitas devolver un valor o más concisión |
| `while` | No sabes cuántas repeticiones habrá; puede ser 0 |
| `do-while` | Necesitas al menos una ejecución garantizada (menús, validación) |
| `for` | Sabes exactamente cuántas veces repetir |
| `for-each` | Recorres todos los elementos de un array o colección |

### Sintaxis resumida

```java
// IF - ELSE IF - ELSE
if (condicion1) {
    // ...
} else if (condicion2) {
    // ...
} else {
    // ...
}

// SWITCH CLÁSICO
switch (variable) {
    case valor1: instrucciones; break;
    case valor2: instrucciones; break;
    default: instrucciones;
}

// SWITCH EXPRESSION (Java 14+)
String resultado = switch (variable) {
    case valor1 -> "A";
    case valor2 -> "B";
    default -> "C";
};

// WHILE
while (condicion) {
    // instrucciones
}

// DO-WHILE
do {
    // instrucciones
} while (condicion);

// FOR
for (int i = 0; i < n; i++) {
    // instrucciones
}

// FOR-EACH
for (Tipo elemento : coleccion) {
    // instrucciones
}

// BREAK: sale del bucle actual
// CONTINUE: salta a la siguiente iteración
```

### Reglas de oro

| Situación | Regla |
|-----------|-------|
| Punto y coma tras `if`/`while` | Nunca — crea un bloque vacío silencioso |
| Llaves en `if` | Siempre usarlas, aunque el bloque sea de una línea |
| `switch` sin `break` | Peligroso: causa fall-through; usa siempre `break` o switch expression |
| Variable de `for` | Solo existe dentro del bucle `for` |
| Variable en `for-each` | Es una copia; no modifica la colección original |
| Bucle infinito | Siempre asegúrate de que la condición eventualmente sea `false` |
| `break` en anidados | Solo afecta al bucle más interno (usa etiquetas para salir de varios) |
| División en condición | Verificar divisor != 0 antes de dividir |

---

## Próximo tema

**Tema 5: Arrays y Strings**

Profundizarás en los arreglos unidimensionales y bidimensionales (matrices), aprenderás a declararlos, inicializarlos, recorrerlos y manipularlos. También dominarás la clase `Arrays` de Java con métodos como `sort()`, `copyOf()` y `binarySearch()`, y reforzarás las operaciones avanzadas con `String` y `StringBuilder`.

---

*Curso de Java — Nivel 1: Fundamentos*
*Tema 4 de 6*
*Creado con fines educativos*
