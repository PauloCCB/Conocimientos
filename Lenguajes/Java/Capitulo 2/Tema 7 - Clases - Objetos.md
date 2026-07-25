# Curso de Java — Nivel 2: Programación Orientada a Objetos
## Tema 7: Clases y Objetos

> **Nivel:** Intermedio | **Prerrequisitos:** Nivel 1 completo (Temas 1–6) | **Duración estimada:** 5–6 horas

---

## Tabla de contenidos

1. [¿Qué es la Programación Orientada a Objetos?](#1-qué-es-la-programación-orientada-a-objetos)
2. [¿Qué es una Clase?](#2-qué-es-una-clase)
3. [¿Qué es un Objeto?](#3-qué-es-un-objeto)
4. [Anatomía completa de una Clase](#4-anatomía-completa-de-una-clase)
5. [Atributos (variables de instancia)](#5-atributos-variables-de-instancia)
6. [Constructores](#6-constructores)
7. [Métodos de instancia](#7-métodos-de-instancia)
8. [La referencia this](#8-la-referencia-this)
9. [Instanciación y uso de objetos](#9-instanciación-y-uso-de-objetos)
10. [Miembros estáticos (static)](#10-miembros-estáticos-static)
11. [El ciclo de vida de un objeto](#11-el-ciclo-de-vida-de-un-objeto)
12. [Clases en archivos separados](#12-clases-en-archivos-separados)
13. [Errores comunes con Clases y Objetos](#13-errores-comunes-con-clases-y-objetos)
14. [Ejercicios prácticos](#14-ejercicios-prácticos)
15. [Resumen del tema](#15-resumen-del-tema)

---

## 1. ¿Qué es la Programación Orientada a Objetos?

La **Programación Orientada a Objetos** (POO, o en inglés OOP — *Object-Oriented Programming*) es un paradigma de programación que organiza el software en torno a **objetos** que combinan datos y comportamiento, en lugar de en torno a funciones y lógica separadas.

El mundo real está lleno de objetos: un automóvil, una cuenta bancaria, un empleado, un producto en una tienda. Cada uno tiene:

- **Características** (datos): el automóvil tiene marca, modelo, color, velocidad actual.
- **Comportamientos** (acciones): el automóvil puede arrancar, frenar, acelerar, girar.

La POO modela el software de la misma manera: agrupa los datos y los comportamientos relacionados en una unidad llamada **objeto**.

### Los cuatro pilares de la POO

Java implementa los cuatro principios fundamentales de la POO. Los iremos cubriendo a lo largo del Nivel 2:

```
┌─────────────────────────────────────────────────────────────┐
│                   PILARES DE LA POO                         │
├──────────────────┬──────────────────┬────────────────────────┤
│  ENCAPSULAMIENTO │    HERENCIA      │     POLIMORFISMO       │
│  (Tema 8)        │    (Tema 9)      │     (Tema 10)          │
│                  │                  │                        │
│  Ocultar detalles│  Una clase puede │  Un objeto puede       │
│  internos y      │  heredar atributos  tomar múltiples      │
│  exponer solo    │  y métodos de    │  formas según el       │
│  lo necesario    │  otra clase      │  contexto              │
├──────────────────┴──────────────────┴────────────────────────┤
│                    ABSTRACCIÓN (Tema 11)                     │
│   Modelar solo las características relevantes del problema  │
└─────────────────────────────────────────────────────────────┘
```

### POO vs. Programación Procedural

```
PROCEDURAL (Nivel 1)          ORIENTADA A OBJETOS (Nivel 2)
────────────────────          ──────────────────────────────
Datos y funciones             Datos y funciones agrupados
separados                     en objetos

String nombre = "Ana";        Persona persona = new Persona();
int edad = 25;                persona.nombre = "Ana";
double salario = 3000;        persona.edad = 25;
                              persona.salario = 3000;
imprimirPersona(nombre,       persona.imprimir();
  edad, salario);

calcularBonus(salario);       persona.calcularBonus();
```

Con la POO, cada objeto es responsable de sus propios datos y sabe cómo operarlos.

---

## 2. ¿Qué es una Clase?

Una **clase** es un **molde** o **plantilla** que define la estructura y el comportamiento que tendrán todos los objetos creados a partir de ella. La clase en sí no es un objeto; es la definición de cómo serán los objetos.

Analogía: si los objetos son galletas, la clase es el molde para galletas. El molde define la forma; las galletas son las instancias reales.

```
CLASE (Molde / Plantilla)          OBJETOS (Instancias)
─────────────────────────          ────────────────────
clase Persona {                    Persona p1 = new Persona();
  String nombre;                   p1.nombre = "Ana";
  int edad;                        p1.edad = 28;
  double salario;
                                   Persona p2 = new Persona();
  void saludar() { ... }           p2.nombre = "Luis";
  double calcularBonus() { ... }   p2.edad = 35;
}
                                   Persona p3 = new Persona();
                                   p3.nombre = "María";
                                   p3.edad = 42;
```

Cada objeto (p1, p2, p3) es una **instancia** de la clase `Persona`. Todos tienen la misma estructura (nombre, edad, salario), pero con valores diferentes.

### Una clase define:

- **Atributos** (también llamados campos o variables de instancia): los datos que cada objeto tendrá.
- **Constructores**: código especial para inicializar el objeto al crearlo.
- **Métodos**: las acciones que el objeto puede realizar.

---

## 3. ¿Qué es un Objeto?

Un **objeto** es una **instancia concreta** de una clase. Cuando creas un objeto, Java reserva espacio en la memoria (en el heap) para almacenar sus datos y le asocia los métodos definidos en la clase.

```
MEMORIA cuando ejecutas: Persona p1 = new Persona("Ana", 28, 3000.0);

Stack                          Heap
┌──────────────┐               ┌─────────────────────────────┐
│ p1 → @FF3A21 │ ────────────► │ Objeto Persona              │
└──────────────┘               │ nombre  = "Ana"             │
                               │ edad    = 28                │
                               │ salario = 3000.0            │
                               └─────────────────────────────┘

MEMORIA con dos objetos:

Stack                          Heap
┌──────────────┐               ┌─────────────────────────────┐
│ p1 → @FF3A21 │ ────────────► │ Objeto Persona: Ana/28/3000 │
├──────────────┤               └─────────────────────────────┘
│ p2 → @FF4B92 │ ────────────► ┌─────────────────────────────┐
└──────────────┘               │ Objeto Persona: Luis/35/2500│
                               └─────────────────────────────┘
```

Cada objeto tiene su propio espacio de memoria independiente. Cambiar los datos de `p1` no afecta a `p2`.

### Estado e identidad de un objeto

- **Estado:** los valores actuales de sus atributos (nombre="Ana", edad=28).
- **Identidad:** su dirección única en memoria (lo que diferencia p1 de p2 aunque tengan los mismos datos).
- **Comportamiento:** lo que puede hacer (métodos definidos en la clase).

---

## 4. Anatomía completa de una Clase

Veamos la estructura completa de una clase Java con todos sus componentes:

```java
// 1. Declaración de paquete (opcional)
package com.miempresa.modelos;

// 2. Importaciones (si necesitas clases externas)
import java.time.LocalDate;

// 3. Declaración de la clase
public class Empleado {

    // ─────────────────────────────────────────
    // 4. ATRIBUTOS (variables de instancia)
    //    Describen el estado del objeto
    // ─────────────────────────────────────────
    private String nombre;
    private String apellido;
    private int edad;
    private double salario;
    private LocalDate fechaContratacion;

    // ─────────────────────────────────────────
    // 5. ATRIBUTOS ESTÁTICOS (de clase)
    //    Compartidos por todos los objetos
    // ─────────────────────────────────────────
    private static int totalEmpleados = 0;
    public static final double SALARIO_MINIMO = 1200.0;

    // ─────────────────────────────────────────
    // 6. CONSTRUCTORES
    //    Inicializan el objeto al crearlo
    // ─────────────────────────────────────────
    public Empleado() {
        totalEmpleados++;
    }

    public Empleado(String nombre, String apellido, double salario) {
        this.nombre = nombre;
        this.apellido = apellido;
        this.salario = salario;
        this.fechaContratacion = LocalDate.now();
        totalEmpleados++;
    }

    // ─────────────────────────────────────────
    // 7. MÉTODOS DE INSTANCIA
    //    Definen el comportamiento del objeto
    // ─────────────────────────────────────────
    public String getNombre() { return nombre; }
    public void setNombre(String nombre) { this.nombre = nombre; }

    public double getSalario() { return salario; }
    public void setSalario(double salario) {
        if (salario >= SALARIO_MINIMO) this.salario = salario;
    }

    public double calcularBonus(double porcentaje) {
        return salario * (porcentaje / 100.0);
    }

    public String obtenerNombreCompleto() {
        return nombre + " " + apellido;
    }

    // ─────────────────────────────────────────
    // 8. MÉTODOS ESTÁTICOS (de clase)
    // ─────────────────────────────────────────
    public static int getTotalEmpleados() {
        return totalEmpleados;
    }

    // ─────────────────────────────────────────
    // 9. toString() — representación como texto
    // ─────────────────────────────────────────
    @Override
    public String toString() {
        return String.format("Empleado{nombre='%s %s', edad=%d, salario=%.2f}",
                nombre, apellido, edad, salario);
    }
}
```

---

## 5. Atributos (variables de instancia)

Los **atributos** (también llamados campos, fields o variables de instancia) son las variables declaradas directamente dentro de la clase, fuera de cualquier método. Representan el **estado** del objeto.

### 5.1 Declaración de atributos

```java
public class Producto {
    // Tipo de acceso + tipo de dato + nombre
    String nombre;          // Sin modificador de acceso (package-private)
    public int codigo;      // Público (accesible desde cualquier lugar)
    private double precio;  // Privado (solo accesible dentro de esta clase)
    protected int stock;    // Protegido (accesible en subclases)
}
```

**Regla de buenas prácticas:** Los atributos deben ser `private`. Esto es el **encapsulamiento** (Tema 8). Por ahora los usaremos sin modificador para simplificar, pero aprenderemos a hacerlo bien en el Tema 8.

### 5.2 Valores por defecto de los atributos

A diferencia de las variables locales (que deben inicializarse antes de usarse), los atributos se inicializan automáticamente con valores por defecto:

| Tipo | Valor por defecto |
|------|-------------------|
| `byte`, `short`, `int`, `long` | `0` |
| `float`, `double` | `0.0` |
| `boolean` | `false` |
| `char` | `'\u0000'` |
| Objetos (`String`, etc.) | `null` |

```java
public class Ejemplo {
    int numero;         // 0
    double decimal;     // 0.0
    boolean activo;     // false
    String texto;       // null
    int[] arreglo;      // null
}
```

### 5.3 Diferencia entre atributos y variables locales

```java
public class Diferencia {
    int atributo = 10;  // Variable de instancia: pertenece al objeto, tiene valor por defecto

    public void metodo() {
        int local = 20;  // Variable local: pertenece al método, NO tiene valor por defecto
        System.out.println(atributo);  // 10  ← acceso al atributo del objeto
        System.out.println(local);     // 20  ← variable local
    }
    // 'local' deja de existir cuando el método termina
    // 'atributo' sigue existiendo mientras el objeto exista
}
```

### 5.4 Atributos con inicialización directa

Puedes darle un valor inicial a un atributo directamente en su declaración:

```java
public class Configuracion {
    String idioma = "español";
    int intentosMaximos = 3;
    boolean modoDebug = false;
    double version = 1.0;
    int[] puntajes = new int[10];
}
```

---

## 6. Constructores

Un **constructor** es un bloque de código especial que se ejecuta automáticamente cuando creas un nuevo objeto con `new`. Su propósito es **inicializar el objeto** a un estado válido desde el primer momento.

### 6.1 Características de los constructores

- Tienen el **mismo nombre que la clase** (con la misma capitalización).
- **No tienen tipo de retorno** (ni siquiera `void`).
- Pueden recibir parámetros (o ninguno).
- Pueden estar **sobrecargados** (múltiples constructores con distintos parámetros).
- Si no defines ninguno, Java proporciona uno por defecto sin parámetros.

### 6.2 Constructor sin parámetros (por defecto)

```java
public class Punto {
    double x;
    double y;

    // Constructor sin parámetros
    public Punto() {
        x = 0.0;
        y = 0.0;
        System.out.println("Punto creado en el origen.");
    }
}

// Uso:
Punto p = new Punto();  // Llama al constructor → "Punto creado en el origen."
System.out.println(p.x + ", " + p.y);  // 0.0, 0.0
```

### 6.3 Constructor con parámetros

```java
public class Punto {
    double x;
    double y;

    public Punto(double x, double y) {
        this.x = x;  // this.x es el atributo; x es el parámetro
        this.y = y;
    }
}

// Uso:
Punto p = new Punto(3.0, 4.0);
System.out.println(p.x + ", " + p.y);  // 3.0, 4.0
```

### 6.4 Sobrecarga de constructores

Puedes tener múltiples constructores con diferentes parámetros. Esto da flexibilidad al crear objetos:

```java
public class Rectangulo {
    double ancho;
    double alto;
    String color;

    // Constructor 1: sin parámetros (cuadrado unitario blanco por defecto)
    public Rectangulo() {
        this.ancho = 1.0;
        this.alto  = 1.0;
        this.color = "blanco";
    }

    // Constructor 2: solo dimensiones (color por defecto)
    public Rectangulo(double ancho, double alto) {
        this.ancho = ancho;
        this.alto  = alto;
        this.color = "blanco";
    }

    // Constructor 3: dimensiones y color
    public Rectangulo(double ancho, double alto, String color) {
        this.ancho = ancho;
        this.alto  = alto;
        this.color = color;
    }

    // Constructor 4: cuadrado (mismo valor para ancho y alto)
    public Rectangulo(double lado) {
        this(lado, lado, "blanco");  // ← llama al constructor 3 con this()
    }
}

// Uso:
Rectangulo r1 = new Rectangulo();                  // 1×1, blanco
Rectangulo r2 = new Rectangulo(5.0, 3.0);          // 5×3, blanco
Rectangulo r3 = new Rectangulo(4.0, 2.0, "rojo");  // 4×2, rojo
Rectangulo r4 = new Rectangulo(6.0);               // 6×6, blanco (cuadrado)
```

### 6.5 this() — llamar a otro constructor

Desde dentro de un constructor, puedes llamar a otro constructor de la misma clase con `this(argumentos)`. Debe ser la **primera instrucción** del constructor:

```java
public class Circulo {
    double radio;
    String color;

    public Circulo(double radio, String color) {
        this.radio = radio;
        this.color = color;
    }

    public Circulo(double radio) {
        this(radio, "azul");  // Llama al constructor de arriba
    }

    public Circulo() {
        this(1.0);  // Llama al constructor de arriba → que llama al de arriba
    }
}
```

### 6.6 El constructor por defecto de Java

Si **no defines ningún constructor**, Java proporciona automáticamente un constructor sin parámetros que no hace nada (solo inicializa los atributos con sus valores por defecto).

```java
public class Simple {
    int valor;
    // Java agrega implícitamente:
    // public Simple() { }
}

Simple s = new Simple();  // Funciona gracias al constructor por defecto
```

**Importante:** Si defines **al menos un constructor con parámetros**, Java ya **no agrega** el constructor por defecto. Si todavía necesitas crear objetos sin parámetros, debes definirlo tú explícitamente.

```java
public class Problema {
    int valor;

    public Problema(int valor) {  // Definiste un constructor con parámetros
        this.valor = valor;
    }
    // Java ya NO agrega el constructor por defecto
}

Problema p1 = new Problema(5);   // OK
Problema p2 = new Problema();    // ERROR: no existe constructor sin parámetros
```

---

## 7. Métodos de instancia

Los **métodos de instancia** son funciones definidas en la clase que operan sobre el estado del objeto. A diferencia de los métodos `static`, necesitan un objeto para ser llamados y tienen acceso a todos los atributos del objeto a través de `this`.

### 7.1 Métodos de consulta (getters)

Retornan información sobre el estado del objeto sin modificarlo:

```java
public class Circulo {
    private double radio;

    public Circulo(double radio) {
        this.radio = radio;
    }

    // Métodos de consulta
    public double getRadio()         { return radio; }
    public double getDiametro()      { return radio * 2; }
    public double getCircunferencia(){ return 2 * Math.PI * radio; }
    public double getArea()          { return Math.PI * radio * radio; }

    public String describir() {
        return String.format(
            "Círculo: radio=%.2f, área=%.2f, circ=%.2f",
            radio, getArea(), getCircunferencia()
        );
    }
}
```

### 7.2 Métodos de modificación (setters y acciones)

Cambian el estado del objeto o realizan una acción:

```java
public class CuentaBancaria {
    private String titular;
    private double saldo;
    private int numeroCuenta;

    public CuentaBancaria(String titular, double saldoInicial) {
        this.titular = titular;
        this.saldo = saldoInicial;
    }

    // Métodos de modificación
    public void depositar(double monto) {
        if (monto > 0) {
            saldo += monto;
            System.out.printf("Depósito: +$%.2f | Saldo: $%.2f%n", monto, saldo);
        } else {
            System.out.println("El monto debe ser positivo.");
        }
    }

    public boolean retirar(double monto) {
        if (monto > 0 && monto <= saldo) {
            saldo -= monto;
            System.out.printf("Retiro: -$%.2f | Saldo: $%.2f%n", monto, saldo);
            return true;
        }
        System.out.println("Saldo insuficiente o monto inválido.");
        return false;
    }

    public void transferir(CuentaBancaria destino, double monto) {
        if (retirar(monto)) {
            destino.depositar(monto);
            System.out.printf("Transferencia de %s a %s: $%.2f%n",
                titular, destino.titular, monto);
        }
    }

    public double getSaldo()    { return saldo; }
    public String getTitular()  { return titular; }
}
```

### 7.3 Los métodos de instancia acceden a todos los atributos

Desde cualquier método de instancia puedes acceder a los atributos y otros métodos del mismo objeto directamente (con o sin `this`):

```java
public class Temperatura {
    private double celsius;

    public Temperatura(double celsius) {
        this.celsius = celsius;
    }

    public double getCelsius()    { return celsius; }
    public double getFahrenheit() { return celsius * 9.0 / 5.0 + 32; }
    public double getKelvin()     { return celsius + 273.15; }

    public void aumentar(double grados) { celsius += grados; }
    public void disminuir(double grados) { celsius -= grados; }

    public String describir() {
        // Accede a otros métodos del mismo objeto
        return String.format("%.1f°C = %.1f°F = %.2f K",
            getCelsius(), getFahrenheit(), getKelvin());
    }
}
```

---

## 8. La referencia this

`this` es una referencia especial que apunta al **objeto actual**: el objeto sobre el que se está ejecutando el método en ese momento.

### 8.1 Resolver ambigüedad entre atributo y parámetro

El uso más común de `this` es cuando un parámetro tiene el mismo nombre que un atributo:

```java
public class Persona {
    String nombre;  // atributo
    int edad;       // atributo

    public Persona(String nombre, int edad) {
        // SIN this: ambigüedad — el compilador usaría el parámetro para ambos lados
        nombre = nombre;  // ¡Esto no hace nada útil! El parámetro se asigna a sí mismo

        // CON this: queda claro cuál es el atributo y cuál el parámetro
        this.nombre = nombre;  // this.nombre = atributo; nombre = parámetro
        this.edad   = edad;
    }
}
```

### 8.2 Llamar a otro método del mismo objeto

```java
public class Calculadora {
    double resultado;

    public void sumar(double a, double b) {
        resultado = a + b;
    }

    public void mostrarResultado() {
        System.out.println("Resultado: " + resultado);
    }

    public void sumarYMostrar(double a, double b) {
        this.sumar(a, b);          // Llama a sumar del mismo objeto
        this.mostrarResultado();   // Llama a mostrarResultado del mismo objeto
        // El 'this.' es opcional aquí, pero hace el código más explícito
    }
}
```

### 8.3 Retornar el objeto actual (patrón Builder/fluent interface)

Retornar `this` desde un método permite encadenar llamadas (method chaining):

```java
public class QueryBuilder {
    private String tabla = "";
    private String condicion = "";
    private int limite = 100;

    public QueryBuilder desde(String tabla) {
        this.tabla = tabla;
        return this;  // Retorna el mismo objeto para encadenar
    }

    public QueryBuilder donde(String condicion) {
        this.condicion = condicion;
        return this;
    }

    public QueryBuilder limite(int n) {
        this.limite = n;
        return this;
    }

    public String construir() {
        return String.format("SELECT * FROM %s WHERE %s LIMIT %d",
            tabla, condicion, limite);
    }
}

// Uso con encadenamiento (fluent interface)
String query = new QueryBuilder()
    .desde("empleados")
    .donde("edad > 30")
    .limite(50)
    .construir();

System.out.println(query);
// SELECT * FROM empleados WHERE edad > 30 LIMIT 50
```

### 8.4 Pasar el objeto actual como argumento

```java
public class Nodo {
    int valor;
    Nodo siguiente;

    public Nodo(int valor) {
        this.valor = valor;
    }

    public void conectarCon(Nodo otro) {
        this.siguiente = otro;
        System.out.println("Nodo " + this.valor + " conectado a " + otro.valor);
    }

    public void registrarse(ListaConectada lista) {
        lista.agregar(this);  // Pasa el objeto actual a otro método
    }
}
```

---

## 9. Instanciación y uso de objetos

### 9.1 Crear objetos con new

El operador `new` realiza tres cosas:
1. Reserva espacio en el heap para el objeto.
2. Inicializa los atributos con valores por defecto.
3. Llama al constructor especificado.

```java
// Sintaxis: TipoClase nombreVariable = new TipoClase(argumentos);
Persona persona = new Persona("Ana", 28);
CuentaBancaria cuenta = new CuentaBancaria("Luis", 5000.0);
Rectangulo rect = new Rectangulo(10.0, 5.0, "azul");
```

### 9.2 Acceder a atributos y métodos

Se usa el operador punto `.` para acceder a miembros del objeto:

```java
// Acceder a atributos (si son públicos o package-private)
persona.nombre = "Carlos";
System.out.println(persona.edad);

// Llamar a métodos
cuenta.depositar(1000.0);
boolean exito = cuenta.retirar(500.0);
System.out.println(cuenta.getSaldo());
```

### 9.3 Múltiples objetos son independientes

```java
CuentaBancaria c1 = new CuentaBancaria("Ana", 1000.0);
CuentaBancaria c2 = new CuentaBancaria("Luis", 2500.0);

c1.depositar(500.0);   // Solo afecta a c1
c2.retirar(200.0);     // Solo afecta a c2

System.out.println(c1.getSaldo());  // 1500.0
System.out.println(c2.getSaldo());  // 2300.0
```

### 9.4 Referencias y null

Una variable de tipo de referencia puede valer `null`, lo que significa que no apunta a ningún objeto:

```java
Persona p = null;           // p no apunta a ningún objeto
p = new Persona("Ana", 28); // Ahora sí apunta a un objeto

if (p != null) {
    System.out.println(p.nombre);
}

// Peligro:
Persona q = null;
System.out.println(q.nombre);  // NullPointerException
```

### 9.5 Dos referencias al mismo objeto

Cuando asignas una referencia a otra variable, ambas apuntan al **mismo objeto**:

```java
Persona original = new Persona("Ana", 28);
Persona alias = original;  // alias apunta al mismo objeto que original

alias.nombre = "Beatriz";  // Modifica el objeto compartido
System.out.println(original.nombre);  // "Beatriz" ← también cambió
System.out.println(alias == original);  // true ← misma dirección de memoria
```

```
Stack                    Heap
┌──────────────┐         ┌────────────────────────────┐
│original→@A1  │ ──────► │ Persona: nombre="Beatriz"  │
├──────────────┤    ┌──► │           edad=28          │
│alias   →@A1  │ ───┘    └────────────────────────────┘
└──────────────┘
```

### 9.6 Comparar objetos

El operador `==` compara referencias (¿son el mismo objeto?). Para comparar el contenido, se sobreescribe el método `equals()`:

```java
Persona p1 = new Persona("Ana", 28);
Persona p2 = new Persona("Ana", 28);
Persona p3 = p1;

System.out.println(p1 == p2);  // false  ← distintos objetos en memoria
System.out.println(p1 == p3);  // true   ← misma referencia
System.out.println(p1.equals(p2));  // Depende de si sobreescribiste equals()
```

---

## 10. Miembros estáticos (static)

Los miembros `static` pertenecen a la **clase**, no a ningún objeto. Son compartidos por todas las instancias.

### 10.1 Atributos static

```java
public class Empleado {
    // Atributo de instancia: cada objeto tiene el suyo
    String nombre;
    double salario;

    // Atributo estático: uno solo, compartido por TODOS los objetos
    static int totalEmpleados = 0;
    static final double SALARIO_MINIMO = 1200.0;  // Constante de clase

    public Empleado(String nombre, double salario) {
        this.nombre = nombre;
        this.salario = salario;
        totalEmpleados++;  // Se incrementa cada vez que se crea un empleado
    }
}

// Uso:
Empleado e1 = new Empleado("Ana", 2500.0);
Empleado e2 = new Empleado("Luis", 3000.0);
Empleado e3 = new Empleado("María", 2800.0);

System.out.println(Empleado.totalEmpleados);  // 3
System.out.println(Empleado.SALARIO_MINIMO);  // 1200.0
```

### 10.2 Métodos static en una clase con instancias

```java
public class MathUtils {
    // Atributo estático
    private static int totalOperaciones = 0;

    // Método estático: no necesita objeto
    public static double calcularCircunferencia(double radio) {
        totalOperaciones++;
        return 2 * Math.PI * radio;
    }

    public static int getTotalOperaciones() {
        return totalOperaciones;
    }
}

// Llamada sin crear objetos:
double c = MathUtils.calcularCircunferencia(5.0);
System.out.println(MathUtils.getTotalOperaciones());  // 1
```

### 10.3 Bloque de inicialización estático

Se ejecuta una sola vez cuando la clase se carga en memoria, antes de cualquier constructor:

```java
public class Configuracion {
    static String version;
    static int maxConexiones;

    // Bloque de inicialización estático
    static {
        version = "2.4.1";
        maxConexiones = 100;
        System.out.println("Configuración cargada.");
    }
}
// "Configuración cargada." se imprime la primera vez que se usa la clase
```

### 10.4 Bloque de inicialización de instancia

Se ejecuta cada vez que se crea un objeto, justo antes del constructor:

```java
public class Ejemplo {
    int valor;

    // Bloque de inicialización de instancia
    {
        valor = 42;
        System.out.println("Objeto inicializado.");
    }

    public Ejemplo() {
        System.out.println("Constructor sin parámetros.");
    }

    public Ejemplo(int v) {
        valor = v;
        System.out.println("Constructor con parámetro.");
    }
}
// new Ejemplo()   → "Objeto inicializado." → "Constructor sin parámetros."
// new Ejemplo(10) → "Objeto inicializado." → "Constructor con parámetro."
```

---

## 11. El ciclo de vida de un objeto

### Fase 1: Declaración

```java
Persona p;  // Solo se crea la variable en el stack. No existe ningún objeto. p = null.
```

### Fase 2: Creación (instanciación)

```java
p = new Persona("Ana", 28);
// 1. Se reserva espacio en el heap
// 2. Los atributos se inicializan con valores por defecto
// 3. Se ejecuta el constructor
// 4. La referencia se almacena en p
```

### Fase 3: Uso

```java
p.nombre = "Ana López";
p.saludar();
double bonus = p.calcularBonus(0.10);
```

### Fase 4: Pérdida de referencia

```java
p = null;           // p ya no apunta al objeto
// p = new Persona("Luis", 35);  // o apunta a otro objeto
```

### Fase 5: Garbage Collection

Cuando ninguna referencia apunta al objeto, el **Garbage Collector** lo marca para eliminación. La JVM eventualmente libera esa memoria. No es necesario hacerlo manualmente.

```
Declaración → Creación → Uso → Pérdida de referencia → GC
    p;      new Persona()  p.saludar()     p = null       (automático)
```

### El método finalize() (deprecated)

Históricamente, `finalize()` se llamaba antes de que el GC eliminara el objeto. Está obsoleto desde Java 9 y eliminado en Java 18. No debes usarlo.

---

## 12. Clases en archivos separados

En proyectos reales, cada clase va en su propio archivo `.java`. Esto facilita la organización, el mantenimiento y la colaboración en equipo.

### Estructura de un proyecto típico

```
mi-proyecto/
│
├── src/
│   ├── Main.java              ← Punto de entrada (contiene main)
│   ├── Persona.java           ← Clase Persona
│   ├── CuentaBancaria.java    ← Clase CuentaBancaria
│   └── Producto.java          ← Clase Producto
│
└── out/                       ← Archivos .class compilados (generados)
```

### Archivo: Persona.java

```java
public class Persona {
    String nombre;
    int edad;
    String email;

    public Persona(String nombre, int edad, String email) {
        this.nombre = nombre;
        this.edad   = edad;
        this.email  = email;
    }

    public void presentarse() {
        System.out.printf("Hola, soy %s, tengo %d años.%n", nombre, edad);
    }

    @Override
    public String toString() {
        return String.format("Persona{nombre='%s', edad=%d, email='%s'}",
            nombre, edad, email);
    }
}
```

### Archivo: Main.java

```java
public class Main {
    public static void main(String[] args) {
        // Usa la clase Persona definida en Persona.java
        Persona p1 = new Persona("Ana", 28, "ana@email.com");
        Persona p2 = new Persona("Luis", 35, "luis@email.com");

        p1.presentarse();
        p2.presentarse();

        System.out.println(p1);  // Llama a toString()
    }
}
```

### El método toString()

`toString()` es un método especial heredado de `Object` (la clase raíz de toda la jerarquía de Java). Se llama automáticamente cuando usas un objeto en un contexto de String (como `System.out.println(obj)` o la concatenación `"" + obj`).

```java
public class Producto {
    String nombre;
    double precio;
    int stock;

    public Producto(String nombre, double precio, int stock) {
        this.nombre = nombre;
        this.precio = precio;
        this.stock  = stock;
    }

    @Override
    public String toString() {
        return String.format("[%s] Precio: $%.2f | Stock: %d", nombre, precio, stock);
    }
}

Producto p = new Producto("Laptop", 999.99, 15);
System.out.println(p);                // [Laptop] Precio: $999.99 | Stock: 15
System.out.println("Producto: " + p); // Producto: [Laptop] Precio: $999.99 | Stock: 15
```

La anotación `@Override` le indica al compilador que estás sobreescribiendo un método de la clase padre. Si escribes mal el nombre del método, el compilador te avisa (sin `@Override`, lo trataría como un método nuevo y no te avisaría del error).

---

## 13. Errores comunes con Clases y Objetos

### Error 1: NullPointerException al usar un objeto no inicializado

```java
Persona p;          // p no fue inicializada, vale null
p.nombre = "Ana";   // NullPointerException

// CORRECTO:
Persona p = new Persona("Ana", 28);
p.nombre = "Ana";
```

### Error 2: Confundir la clase con el objeto

```java
// ERROR: no puedes llamar métodos de instancia directamente en la clase
Persona.saludar();  // Error: saludar() es un método de instancia, no estático

// CORRECTO: necesitas un objeto
Persona p = new Persona("Ana", 28);
p.saludar();
```

### Error 3: Olvidar el constructor por defecto al agregar uno con parámetros

```java
public class Animal {
    String nombre;

    public Animal(String nombre) {
        this.nombre = nombre;
    }
    // Java ya NO agrega el constructor por defecto
}

Animal a = new Animal();  // ERROR: no existe constructor sin parámetros

// SOLUCIÓN: agregar el constructor sin parámetros explícitamente
public Animal() {
    this.nombre = "Sin nombre";
}
```

### Error 4: Alias involuntario (dos referencias al mismo objeto)

```java
Persona original = new Persona("Ana", 28);
Persona copia = original;  // ¡No es una copia! Es el mismo objeto

copia.nombre = "Luis";     // También cambia original.nombre
System.out.println(original.nombre);  // "Luis"  ← inesperado

// SOLUCIÓN: crear un objeto nuevo con los mismos valores
Persona verdaderaCopia = new Persona(original.nombre, original.edad);
```

### Error 5: Acceder a miembro estático a través de un objeto (confuso pero válido)

```java
Empleado e = new Empleado("Ana", 2500.0);

// Funciona pero es confuso: parece que totalEmpleados pertenece al objeto
System.out.println(e.totalEmpleados);

// CORRECTO y claro: acceder por la clase
System.out.println(Empleado.totalEmpleados);
```

### Error 6: this() no es la primera instrucción

```java
public class Ejemplo {
    int valor;

    public Ejemplo() {
        System.out.println("Hola");  // ERROR: debe ir DESPUÉS de this()
        this(10);                    // Error: this() debe ser la primera instrucción
    }

    public Ejemplo(int v) {
        this.valor = v;
    }

    // CORRECTO:
    public Ejemplo() {
        this(10);                    // Primera instrucción
        System.out.println("Hola"); // Ahora sí puede ir aquí
    }
}
```

### Error 7: Modificar atributo directamente en vez de usar el método

```java
public class CuentaBancaria {
    double saldo;

    public void depositar(double monto) {
        if (monto > 0) saldo += monto;
    }
}

CuentaBancaria c = new CuentaBancaria("Ana", 1000);
c.saldo = -5000;  // Sin validación: estado inválido

// SOLUCIÓN: usar encapsulamiento (Tema 8) para hacer saldo privado
// y solo modificarlo a través de métodos que validen
```

---

## 14. Ejercicios prácticos

### Ejercicio 1 — Clase Persona completa (Nivel: Básico)

Crea el archivo `Persona.java` con los atributos: `nombre` (String), `apellido` (String), `edad` (int), `altura` (double en metros), `peso` (double en kg).

Define:
- Constructor sin parámetros (valores por defecto razonables)
- Constructor con todos los parámetros
- Métodos: `getNombreCompleto()`, `calcularIMC()`, `clasificarIMC()`, `esMayorDeEdad()`
- `toString()` con formato profesional

En `Main.java`, crea 3 personas con distintos datos y muestra toda su información.

---

### Ejercicio 2 — Clase Rectángulo (Nivel: Básico-Medio)

Crea `Rectangulo.java` con atributos `ancho` y `alto` (double) y `color` (String).

Define:
- 3 constructores sobrecargados (sin parámetros, solo dimensiones, dimensiones y color)
- `calcularArea()`, `calcularPerimetro()`, `calcularDiagonal()`
- `esCuadrado()` — boolean
- `escalar(double factor)` — multiplica dimensiones por el factor
- `toString()` con todos los datos y cálculos

Crea al menos 4 rectángulos con distintos constructores y muestra sus propiedades.

---

### Ejercicio 3 — Sistema bancario (Nivel: Medio)

Crea `CuentaBancaria.java` con atributos: `numeroCuenta` (int, generado automáticamente), `titular` (String), `saldo` (double), y un atributo estático `contadorCuentas` para asignar números únicos.

Define:
- Constructor que recibe titular y saldo inicial
- `depositar(double monto)` — con validación (monto > 0)
- `retirar(double monto)` — retorna boolean, valida saldo suficiente
- `transferir(CuentaBancaria destino, double monto)` — usa retirar y depositar
- `getSaldo()`, `getTitular()`, `getNumeroCuenta()`
- `static int getTotalCuentas()`
- `toString()` con formato de estado de cuenta

En `Main.java`, simula las siguientes operaciones:
1. Crear 3 cuentas con saldos distintos
2. Hacer depósitos y retiros
3. Una transferencia entre dos cuentas
4. Mostrar el estado final de todas las cuentas
5. Mostrar el total de cuentas creadas

---

### Ejercicio 4 — Catálogo de productos (Nivel: Medio)

Crea `Producto.java` con: `codigo` (int, auto-generado), `nombre` (String), `precio` (double), `stock` (int), `categoria` (String).

Define:
- Constructor completo
- `vender(int cantidad)` — retorna boolean, descuenta del stock si hay suficiente
- `reabastecer(int cantidad)` — suma al stock
- `aplicarDescuento(double porcentaje)` — modifica el precio
- `estaDisponible()` — boolean
- `calcularValorInventario()` — precio × stock
- `static double calcularValorTotal(Producto[] catalogo)` — suma el valor de todos

En `Main.java`, crea un catálogo de 5 productos, simula ventas y reabastecimientos, y muestra el valor total del inventario.

---

### Ejercicio 5 — Clase Estudiante con historial (Nivel: Avanzado)

Crea `Estudiante.java` con: `nombre` (String), `matricula` (String, auto-generada con prefijo "EST-"), `calificaciones` (double[], máximo 10 materias), `numCalificaciones` (int).

Define:
- Constructor que recibe nombre
- `agregarCalificacion(String materia, double calificacion)` — agrega al historial
- `calcularPromedio()` — double
- `obtenerMaxima()` y `obtenerMinima()` — double
- `aprobo()` — boolean (promedio >= 6.0)
- `obtenerEstatus()` — String: "Sobresaliente" (>=9), "Bien" (>=8), "Regular" (>=7), "Suficiente" (>=6), "Reprobado" (<6)
- `toString()` — reporte completo con todas las calificaciones y estadísticas

Crea 3 estudiantes, agrega varias calificaciones a cada uno y muestra sus reportes completos.

---

### Ejercicio 6 — Desafío: sistema de reservas (Nivel: Avanzado)

Crea tres clases en archivos separados:

**`Hotel.java`:** nombre, ciudad, estrellas (int 1-5), `Habitacion[] habitaciones`
- `agregarHabitacion(Habitacion h)`
- `buscarDisponible(int capacidad)` — retorna primera Habitacion disponible o null
- `ocupacion()` — porcentaje de habitaciones ocupadas
- `toString()`

**`Habitacion.java`:** numero (int), tipo ("simple"/"doble"/"suite"), capacidad, precioPorNoche, disponible (boolean)
- `reservar()` — cambia disponible a false
- `liberar()` — cambia disponible a true
- `calcularCostoEstadia(int noches)` — double
- `toString()`

**`Main.java`:** Crea un hotel con 6 habitaciones de distintos tipos. Simula 4 reservas, muestra la ocupación, cancela una reserva y muestra el estado final del hotel.

---

## 15. Resumen del tema

### Clase y objeto en una página

```java
// ═══════════════════════════════════
// DEFINICIÓN DE LA CLASE (plantilla)
// ═══════════════════════════════════
public class MiClase {

    // Atributos de instancia (estado)
    TipoA atributo1;
    TipoB atributo2;

    // Atributos de clase (compartidos)
    static int contadorTotal = 0;
    static final CONSTANTE = valor;

    // Constructor
    public MiClase(TipoA a, TipoB b) {
        this.atributo1 = a;
        this.atributo2 = b;
        contadorTotal++;
    }

    // Métodos de instancia
    public TipoRetorno miMetodo(params) {
        // usa this.atributo1, this.atributo2
        return valor;
    }

    // Método estático
    public static int getTotal() {
        return contadorTotal;
    }

    // Representación como String
    @Override
    public String toString() {
        return "MiClase{atributo1=" + atributo1 + "}";
    }
}

// ═══════════════════════════════════
// USO DE LA CLASE (instanciación)
// ═══════════════════════════════════
MiClase obj1 = new MiClase(valorA, valorB);  // Crea objeto
obj1.miMetodo(args);                          // Llama método
System.out.println(obj1);                     // Llama toString()
System.out.println(MiClase.getTotal());       // Llama método estático
```

### Conceptos clave del tema

| Concepto | Descripción |
|----------|-------------|
| **Clase** | Molde/plantilla que define estructura y comportamiento |
| **Objeto** | Instancia concreta de una clase, vive en el heap |
| **Atributo** | Variable de instancia, describe el estado del objeto |
| **Constructor** | Código que inicializa el objeto al crearlo con `new` |
| **this** | Referencia al objeto actual dentro de sus métodos |
| **static** | Miembro que pertenece a la clase, compartido por todos los objetos |
| **toString()** | Método que devuelve representación textual del objeto |
| **new** | Operador que crea un objeto en el heap |
| **null** | Referencia que no apunta a ningún objeto |
| **GC** | Garbage Collector — libera objetos sin referencias automáticamente |

### Reglas de oro

| Situación | Regla |
|-----------|-------|
| Atributos | Declarar como `private` (Tema 8 lo explica en profundidad) |
| Constructores | Validar parámetros, inicializar todos los atributos |
| Sobrecarga | Usar `this(args)` para reutilizar lógica entre constructores |
| toString() | Siempre sobreescribir para facilitar depuración |
| Comparar objetos | Usar `equals()`, nunca `==` para comparar contenido |
| Copiar objetos | Nunca `obj2 = obj1`; crear un objeto nuevo con los mismos valores |
| static | Solo para datos/métodos que NO dependen de un objeto concreto |
| null | Verificar siempre antes de usar una referencia que pueda ser null |

---

## Próximo tema

**Tema 8: Encapsulamiento**

Aprenderás por qué los atributos deben ser `private`, cómo controlar el acceso con getters y setters, la diferencia entre los cuatro modificadores de acceso (`public`, `private`, `protected`, sin modificador), y cómo diseñar clases robustas con validación integrada que garanticen que los objetos siempre estén en un estado válido.

---

*Curso de Java — Nivel 2: Programación Orientada a Objetos*
*Tema 7 de 12*
*Creado con fines educativos*
