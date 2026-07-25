# Curso de Java — Nivel 2: Programación Orientada a Objetos
## Tema 10: Polimorfismo

> **Nivel:** Intermedio | **Prerrequisitos:** Temas 1–9 completados | **Duración estimada:** 5–6 horas

---

## Tabla de contenidos

1. [¿Qué es el Polimorfismo?](#1-qué-es-el-polimorfismo)
2. [Polimorfismo estático — Sobrecarga](#2-polimorfismo-estático--sobrecarga)
3. [Polimorfismo dinámico — Sobreescritura](#3-polimorfismo-dinámico--sobreescritura)
4. [Despacho dinámico de métodos](#4-despacho-dinámico-de-métodos)
5. [Polimorfismo con arrays y colecciones](#5-polimorfismo-con-arrays-y-colecciones)
6. [Polimorfismo con parámetros de métodos](#6-polimorfismo-con-parámetros-de-métodos)
7. [Polimorfismo y clases abstractas](#7-polimorfismo-y-clases-abstractas)
8. [El patrón Strategy con polimorfismo](#8-el-patrón-strategy-con-polimorfismo)
9. [Polimorfismo y el tipo Object](#9-polimorfismo-y-el-tipo-object)
10. [Covarianza en tipos de retorno](#10-covarianza-en-tipos-de-retorno)
11. [Principio de Sustitución de Liskov (LSP)](#11-principio-de-sustitución-de-liskov-lsp)
12. [Polimorfismo vs. condicionales](#12-polimorfismo-vs-condicionales)
13. [Errores comunes con Polimorfismo](#13-errores-comunes-con-polimorfismo)
14. [Ejercicios prácticos](#14-ejercicios-prácticos)
15. [Resumen del tema](#15-resumen-del-tema)

---

## 1. ¿Qué es el Polimorfismo?

El **polimorfismo** es el tercer pilar de la POO. La palabra viene del griego: *polys* (muchos) + *morphé* (forma). En programación significa que **un mismo nombre puede tomar múltiples formas** según el contexto.

En Java, el polimorfismo se manifiesta de dos grandes maneras:

1. **Polimorfismo estático (en tiempo de compilación):** el compilador decide qué versión de un método usar según los argumentos. Se logra mediante **sobrecarga** de métodos.

2. **Polimorfismo dinámico (en tiempo de ejecución):** la JVM decide qué versión de un método ejecutar según el tipo real del objeto, no el tipo de la referencia. Se logra mediante **sobreescritura** de métodos.

### La magia del polimorfismo dinámico

```java
// Imagina que tienes una referencia de tipo Animal
Animal animal;

// Puede apuntar a distintos tipos de objetos
animal = new Perro("Rex");
animal.hacerSonido();     // ¡Guau! ← decide en tiempo de ejecución

animal = new Gato("Luna");
animal.hacerSonido();     // ¡Miau! ← decide en tiempo de ejecución

animal = new Pajaro("Pío");
animal.hacerSonido();     // ¡Pío!  ← decide en tiempo de ejecución
```

La misma llamada `animal.hacerSonido()` produce resultados diferentes según el objeto real. El código que llama al método **no necesita saber** qué tipo exacto de animal tiene — solo sabe que puede llamar `hacerSonido()`.

### ¿Por qué es tan poderoso?

Sin polimorfismo, para manejar distintos tipos necesitas condiciones explícitas:

```java
// SIN polimorfismo: código frágil y difícil de extender
public static void hacerSonido(Object animal) {
    if (animal instanceof Perro) {
        ((Perro) animal).ladrar();
    } else if (animal instanceof Gato) {
        ((Gato) animal).maullar();
    } else if (animal instanceof Pajaro) {
        ((Pajaro) animal).piar();
    }
    // Cada nuevo tipo requiere modificar este método
}

// CON polimorfismo: código flexible y extensible
public static void hacerSonido(Animal animal) {
    animal.hacerSonido();  // La JVM sabe qué versión llamar automáticamente
    // Añadir nuevos tipos no requiere modificar este método
}
```

---

## 2. Polimorfismo estático — Sobrecarga

Ya lo vimos en el Tema 6 (Métodos) y en el Tema 9 (Herencia), pero lo revisamos aquí en su contexto completo como forma de polimorfismo.

La **sobrecarga** (overloading) permite que múltiples métodos compartan el mismo nombre pero con diferente lista de parámetros. El compilador resuelve cuál llamar en **tiempo de compilación** basándose en los tipos y cantidad de argumentos.

### Ejemplo completo de sobrecarga

```java
public class Impresora {

    // Versión 1: un entero
    public void imprimir(int valor) {
        System.out.println("int: " + valor);
    }

    // Versión 2: un double
    public void imprimir(double valor) {
        System.out.println("double: " + valor);
    }

    // Versión 3: un String
    public void imprimir(String texto) {
        System.out.println("String: " + texto);
    }

    // Versión 4: un array de ints
    public void imprimir(int[] arr) {
        System.out.print("int[]: ");
        for (int n : arr) System.out.print(n + " ");
        System.out.println();
    }

    // Versión 5: dos parámetros
    public void imprimir(String etiqueta, int valor) {
        System.out.println(etiqueta + " = " + valor);
    }

    // Versión 6: varargs
    public void imprimir(String... textos) {
        System.out.print("varargs: ");
        for (String t : textos) System.out.print(t + " | ");
        System.out.println();
    }
}

// El compilador elige la versión correcta según los argumentos:
Impresora imp = new Impresora();
imp.imprimir(42);                        // int: 42
imp.imprimir(3.14);                      // double: 3.14
imp.imprimir("Hola");                    // String: Hola
imp.imprimir(new int[]{1, 2, 3});        // int[]: 1 2 3
imp.imprimir("edad", 25);               // edad = 25
imp.imprimir("Java", "Python", "C++");  // varargs: Java | Python | C++ |
```

### Resolución de la sobrecarga por el compilador

El compilador busca la versión que mejor coincida siguiendo este orden:

```
1. Coincidencia exacta de tipos
2. Promoción de tipos primitivos (byte→short→int→long→float→double)
3. Autoboxing (int → Integer)
4. Varargs
```

```java
public class Resolucion {
    public static void metodo(int n)    { System.out.println("int: " + n); }
    public static void metodo(long n)   { System.out.println("long: " + n); }
    public static void metodo(double n) { System.out.println("double: " + n); }
}

Resolucion.metodo(5);     // int: 5       ← coincidencia exacta con int
Resolucion.metodo(5L);    // long: 5      ← coincidencia exacta con long
Resolucion.metodo(5.0);   // double: 5.0  ← coincidencia exacta con double

byte b = 10;
Resolucion.metodo(b);     // int: 10      ← byte promovido a int (no a long ni double)
```

### Sobrecarga en herencia

La sobrecarga puede ocurrir entre la superclase y la subclase:

```java
public class Base {
    public void procesar(String s) {
        System.out.println("Base procesa String: " + s);
    }
}

public class Derivada extends Base {
    // Sobrecarga (no sobreescritura): mismo nombre, diferente parámetro
    public void procesar(int n) {
        System.out.println("Derivada procesa int: " + n);
    }
}

Derivada d = new Derivada();
d.procesar("Hola");  // Base procesa String: Hola  ← heredado de Base
d.procesar(42);      // Derivada procesa int: 42   ← propio de Derivada
```

---

## 3. Polimorfismo dinámico — Sobreescritura

El **polimorfismo dinámico** es el corazón del polimorfismo en Java. Cuando una subclase sobreescribe un método de la superclase y accedes al objeto a través de una referencia de la superclase, Java siempre ejecuta la versión de la subclase (el tipo real del objeto), no la de la superclase (el tipo de la referencia).

### La regla fundamental

> **El método que se ejecuta depende del tipo real del objeto, no del tipo de la referencia.**

```java
public class Figura {
    public void dibujar() {
        System.out.println("Dibujando figura genérica.");
    }
}

public class Circulo extends Figura {
    @Override
    public void dibujar() {
        System.out.println("Dibujando un círculo ○");
    }
}

public class Cuadrado extends Figura {
    @Override
    public void dibujar() {
        System.out.println("Dibujando un cuadrado □");
    }
}

public class Triangulo extends Figura {
    @Override
    public void dibujar() {
        System.out.println("Dibujando un triángulo △");
    }
}

// La REFERENCIA es de tipo Figura, pero el OBJETO real es de distintos tipos
Figura f1 = new Circulo();    // Referencia: Figura | Objeto real: Circulo
Figura f2 = new Cuadrado();   // Referencia: Figura | Objeto real: Cuadrado
Figura f3 = new Triangulo();  // Referencia: Figura | Objeto real: Triangulo
Figura f4 = new Figura();     // Referencia: Figura | Objeto real: Figura

f1.dibujar();  // Dibujando un círculo ○     ← versión de Circulo
f2.dibujar();  // Dibujando un cuadrado □    ← versión de Cuadrado
f3.dibujar();  // Dibujando un triángulo △   ← versión de Triangulo
f4.dibujar();  // Dibujando figura genérica. ← versión de Figura (sin override)
```

### La diferencia entre tipo de referencia y tipo de objeto

```
Variable    Tipo de referencia    Tipo real del objeto
────────    ──────────────────    ─────────────────────
f1          Figura                Circulo
f2          Figura                Cuadrado
f3          Figura                Triangulo

El tipo de referencia determina:
  ✓ Qué métodos PUEDES llamar (los de Figura)
  ✗ Qué versión del método se EJECUTA (siempre la del objeto real)
```

---

## 4. Despacho dinámico de métodos

El mecanismo que hace posible el polimorfismo dinámico se llama **despacho dinámico de métodos** (dynamic method dispatch) o **late binding** (enlace tardío).

### Cómo funciona la JVM

Cuando la JVM encuentra una llamada a un método sobreescrito, en lugar de decidir en tiempo de compilación, consulta en tiempo de ejecución la tabla de métodos virtuales (vtable) del objeto real:

```
TIEMPO DE COMPILACIÓN:
  El compilador verifica que el método dibujar() existe en Figura → OK
  No decide qué versión ejecutar

TIEMPO DE EJECUCIÓN:
  La JVM mira el tipo real del objeto (ej: Circulo)
  Busca en la vtable de Circulo si tiene dibujar() → Sí, tiene una sobreescritura
  Ejecuta Circulo.dibujar()

FLUJO DE BÚSQUEDA (bottom-up):
  Objeto real: Circulo → ¿Tiene dibujar()? SÍ → ejecuta Circulo.dibujar()
  Si no tuviera → sube a Figura → ¿Tiene dibujar()? SÍ → ejecuta Figura.dibujar()
  Si no tuviera → sube a Object → ¿Tiene dibujar()? NO → error
```

### Ejemplo detallado con jerarquía profunda

```java
public class A {
    public void metodo() { System.out.println("A"); }
}

public class B extends A {
    @Override
    public void metodo() { System.out.println("B"); }
}

public class C extends B {
    // No sobreescribe metodo() — hereda la versión de B
}

public class D extends C {
    @Override
    public void metodo() { System.out.println("D"); }
}

// Despacho dinámico en acción:
A obj1 = new A();  obj1.metodo();  // A  ← tipo real: A
A obj2 = new B();  obj2.metodo();  // B  ← tipo real: B
A obj3 = new C();  obj3.metodo();  // B  ← tipo real: C, pero hereda versión de B
A obj4 = new D();  obj4.metodo();  // D  ← tipo real: D

// La referencia siempre es A, pero el comportamiento varía
```

### Late binding vs. Early binding

```
EARLY BINDING (enlace temprano):          LATE BINDING (enlace tardío):
  Métodos static                            Métodos de instancia sobreescribibles
  Métodos private                           Se resuelve en tiempo de ejecución
  Métodos final                             Base del polimorfismo dinámico
  Resuelto en tiempo de compilación
```

```java
public class Animal {
    // Early binding: static no se despacha dinámicamente
    public static void dormir() { System.out.println("Animal duerme (static)"); }

    // Late binding: se despacha dinámicamente
    public void respirar() { System.out.println("Animal respira"); }
}

public class Perro extends Animal {
    // Esto OCULTA el método static del padre (no lo sobreescribe)
    public static void dormir() { System.out.println("Perro duerme (static)"); }

    @Override
    public void respirar() { System.out.println("Perro respira rápido"); }
}

Animal a = new Perro();
a.dormir();    // Animal duerme (static)  ← early binding: usa tipo de REFERENCIA
a.respirar();  // Perro respira rápido   ← late binding: usa tipo REAL del objeto
```

---

## 5. Polimorfismo con arrays y colecciones

El uso más práctico y frecuente del polimorfismo es con arrays y colecciones que contienen objetos de distintos tipos de una misma jerarquía.

### Arrays polimórficos

```java
// Un array de Figura puede contener Circulo, Rectangulo, Triangulo...
Figura[] figuras = {
    new Circulo("Rojo", 5.0),
    new Rectangulo("Azul", 4.0, 6.0),
    new Triangulo("Verde", 3.0, 4.0, 5.0),
    new Circulo("Amarillo", 2.5),
    new Rectangulo("Negro", 8.0, 3.0)
};

// Procesar todas las figuras con polimorfismo — sin saber qué tipo específico son
double areaTotal = 0;
for (Figura f : figuras) {
    f.describir();               // Cada una describe su propia forma
    areaTotal += f.calcularArea(); // Cada una calcula su propia área
}
System.out.printf("Área total: %.2f%n", areaTotal);
```

### Encontrar el máximo con polimorfismo

```java
public static Figura encontrarMayor(Figura[] figuras) {
    if (figuras == null || figuras.length == 0) return null;
    Figura mayor = figuras[0];
    for (int i = 1; i < figuras.length; i++) {
        if (figuras[i].calcularArea() > mayor.calcularArea()) {
            mayor = figuras[i];
        }
    }
    return mayor;
}

Figura mayor = encontrarMayor(figuras);
System.out.println("La figura más grande: " + mayor);
```

### Colecciones polimórficas con ArrayList

```java
import java.util.ArrayList;
import java.util.List;

List<Empleado> nomina = new ArrayList<>();
nomina.add(new EmpleadoAsalariado("Ana",   "IT",       3000.0));
nomina.add(new EmpleadoPorHora("Luis",     "Planta",   15.0, 160));
nomina.add(new EmpleadoConComision("María","Ventas",   1500.0, 20000.0, 5.0));
nomina.add(new Gerente("Carlos",           "RRHH",     5000.0, 24000.0, 8));
nomina.add(new EmpleadoPorHora("Pedro",    "Logística",12.0, 140));

// Calcular nómina total con polimorfismo
double totalNomina = 0;
System.out.println("=== NÓMINA DEL MES ===");
for (Empleado e : nomina) {
    double salario = e.calcularSalario();  // Versión correcta para cada tipo
    System.out.printf("%-20s $%10.2f%n", e.getNombre(), salario);
    totalNomina += salario;
}
System.out.printf("%-20s $%10.2f%n", "TOTAL:", totalNomina);
```

### Ordenar con Comparable y polimorfismo

```java
public abstract class Figura implements Comparable<Figura> {
    // ...
    @Override
    public int compareTo(Figura otra) {
        return Double.compare(this.calcularArea(), otra.calcularArea());
    }
}

import java.util.Collections;

List<Figura> lista = new ArrayList<>();
lista.add(new Circulo("Rojo", 3.0));
lista.add(new Rectangulo("Azul", 5.0, 2.0));
lista.add(new Triangulo("Verde", 6.0, 8.0, 10.0));

Collections.sort(lista);  // Usa compareTo() polimórficamente

for (Figura f : lista) {
    System.out.printf("%-12s área=%.2f%n", f.getTipo(), f.calcularArea());
}
// Ordenadas de menor a mayor área
```

---

## 6. Polimorfismo con parámetros de métodos

Cuando un método recibe un parámetro de tipo superclase, acepta cualquier objeto de esa clase o sus subclases.

### Métodos que trabajan con cualquier tipo de la jerarquía

```java
public class GestorFiguras {

    // Este método acepta CUALQUIER figura: Circulo, Rectangulo, Triangulo...
    public static void imprimirInfo(Figura figura) {
        System.out.printf("Tipo: %-12s | Color: %-8s | Área: %8.2f%n",
            figura.getTipo(), figura.getColor(), figura.calcularArea());
    }

    // Acepta array de cualquier Figura
    public static double sumarAreas(Figura[] figuras) {
        double total = 0;
        for (Figura f : figuras) total += f.calcularArea();
        return total;
    }

    // Compara dos figuras cualesquiera
    public static Figura laMasGrande(Figura a, Figura b) {
        return a.calcularArea() >= b.calcularArea() ? a : b;
    }

    // Filtra figuras por área mínima
    public static List<Figura> filtrarPorArea(List<Figura> lista, double areaMinima) {
        List<Figura> resultado = new ArrayList<>();
        for (Figura f : lista) {
            if (f.calcularArea() >= areaMinima) resultado.add(f);
        }
        return resultado;
    }
}

// Uso: el mismo método funciona para todos los tipos
Circulo c      = new Circulo("Rojo", 5.0);
Rectangulo r   = new Rectangulo("Azul", 4.0, 6.0);
Triangulo t    = new Triangulo("Verde", 3.0, 4.0, 5.0);

GestorFiguras.imprimirInfo(c);   // funciona con Circulo
GestorFiguras.imprimirInfo(r);   // funciona con Rectangulo
GestorFiguras.imprimirInfo(t);   // funciona con Triangulo

Figura mayor = GestorFiguras.laMasGrande(c, r);
System.out.println("La más grande: " + mayor.getTipo());
```

### El poder: añadir nuevos tipos sin modificar el método

```java
// Mañana agregas Pentagono:
public class Pentagono extends Figura {
    private double lado;
    // implementa calcularArea(), calcularPerimetro(), getTipo()...
}

// GestorFiguras.imprimirInfo() funciona con Pentagono SIN MODIFICAR NADA
Pentagono p = new Pentagono("Morado", 6.0);
GestorFiguras.imprimirInfo(p);  // Funciona automáticamente
```

---

## 7. Polimorfismo y clases abstractas

Las clases abstractas son la base más natural para el polimorfismo porque definen contratos que las subclases deben cumplir.

### Sistema completo de pagos

```java
// Clase abstracta: define el contrato
public abstract class MetodoPago {
    protected String titular;
    protected String moneda;

    public MetodoPago(String titular, String moneda) {
        this.titular = titular;
        this.moneda  = moneda;
    }

    // Contrato: todas las formas de pago deben poder procesar
    public abstract boolean procesarPago(double monto);

    // Contrato: todas deben poder validar
    public abstract boolean validar();

    // Contrato: todas deben identificarse
    public abstract String getTipo();

    // Implementación común reutilizada por todas
    public void imprimirRecibo(double monto) {
        System.out.printf("=== RECIBO ====%n");
        System.out.printf("Método  : %s%n", getTipo());
        System.out.printf("Titular : %s%n", titular);
        System.out.printf("Monto   : %.2f %s%n", monto, moneda);
        System.out.printf("Estado  : %s%n", procesarPago(monto) ? "APROBADO" : "RECHAZADO");
        System.out.println("==============");
    }
}

// Tarjeta de crédito
public class TarjetaCredito extends MetodoPago {
    private String numeroTarjeta;
    private String cvv;
    private double limiteCredito;
    private double saldoUsado;

    public TarjetaCredito(String titular, String numero, String cvv, double limite) {
        super(titular, "USD");
        this.numeroTarjeta = numero;
        this.cvv           = cvv;
        this.limiteCredito = limite;
        this.saldoUsado    = 0;
    }

    @Override
    public boolean validar() {
        return numeroTarjeta != null && numeroTarjeta.length() == 16 && cvv.length() == 3;
    }

    @Override
    public boolean procesarPago(double monto) {
        if (!validar()) return false;
        if (saldoUsado + monto > limiteCredito) {
            System.out.println("Límite de crédito insuficiente.");
            return false;
        }
        saldoUsado += monto;
        return true;
    }

    @Override
    public String getTipo() { return "Tarjeta de Crédito ****" + numeroTarjeta.substring(12); }

    public double getCreditoDisponible() { return limiteCredito - saldoUsado; }
}

// Transferencia bancaria
public class TransferenciaBancaria extends MetodoPago {
    private String cuentaOrigen;
    private String banco;
    private double saldoCuenta;

    public TransferenciaBancaria(String titular, String cuenta, String banco, double saldo) {
        super(titular, "USD");
        this.cuentaOrigen = cuenta;
        this.banco        = banco;
        this.saldoCuenta  = saldo;
    }

    @Override
    public boolean validar() {
        return cuentaOrigen != null && cuentaOrigen.length() >= 10;
    }

    @Override
    public boolean procesarPago(double monto) {
        if (!validar() || monto > saldoCuenta) return false;
        saldoCuenta -= monto;
        return true;
    }

    @Override
    public String getTipo() { return "Transferencia Bancaria (" + banco + ")"; }
}

// Billetera digital
public class BilleteraDigital extends MetodoPago {
    private String email;
    private double saldo;
    private boolean verificada;

    public BilleteraDigital(String titular, String email, double saldo) {
        super(titular, "USD");
        this.email      = email;
        this.saldo      = saldo;
        this.verificada = email.contains("@");
    }

    @Override
    public boolean validar() {
        return verificada && saldo > 0;
    }

    @Override
    public boolean procesarPago(double monto) {
        if (!validar() || monto > saldo) return false;
        saldo -= monto;
        return true;
    }

    @Override
    public String getTipo() { return "Billetera Digital (" + email + ")"; }
}

// Sistema de pago: trabaja con CUALQUIER MetodoPago polimórficamente
public class ProcesadorPagos {
    public static void procesarOrden(MetodoPago metodo, double monto) {
        System.out.printf("%nProcesando pago de $%.2f con %s...%n", monto, metodo.getTipo());
        if (metodo.validar()) {
            metodo.imprimirRecibo(monto);
        } else {
            System.out.println("Método de pago no válido.");
        }
    }

    public static double totalProcesado(List<MetodoPago> metodos, double[] montos) {
        double total = 0;
        for (int i = 0; i < metodos.size(); i++) {
            if (metodos.get(i).procesarPago(montos[i])) {
                total += montos[i];
            }
        }
        return total;
    }
}

// Uso completamente polimórfico:
List<MetodoPago> metodos = new ArrayList<>();
metodos.add(new TarjetaCredito("Ana García", "1234567890123456", "123", 5000.0));
metodos.add(new TransferenciaBancaria("Luis Pérez", "ES9121000418450200051332", "BBVA", 3000.0));
metodos.add(new BilleteraDigital("María López", "maria@paypal.com", 500.0));

for (MetodoPago m : metodos) {
    ProcesadorPagos.procesarOrden(m, 250.0);
}
```

---

## 8. El patrón Strategy con polimorfismo

El **patrón Strategy** es un patrón de diseño que usa el polimorfismo para hacer intercambiable el algoritmo que usa una clase. Es uno de los usos más elegantes y prácticos del polimorfismo.

### Problema: ordenar de distintas maneras

```java
// Sin polimorfismo: condiciones if/else para cada estrategia
public void ordenar(int[] datos, String criterio) {
    if (criterio.equals("burbuja")) {
        // implementación burbuja...
    } else if (criterio.equals("insercion")) {
        // implementación inserción...
    } else if (criterio.equals("seleccion")) {
        // implementación selección...
    }
    // Cada nueva estrategia requiere modificar este método
}
```

### Solución con polimorfismo — Strategy Pattern

```java
// 1. Define el contrato (la "estrategia")
public abstract class EstrategiaOrdenamiento {
    public abstract void ordenar(int[] datos);
    public abstract String getNombre();

    // Método auxiliar compartido
    protected void intercambiar(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i]   = arr[j];
        arr[j]   = temp;
    }
}

// 2. Implementaciones concretas de la estrategia
public class OrdenamientoBurbuja extends EstrategiaOrdenamiento {
    @Override
    public void ordenar(int[] datos) {
        int n = datos.length;
        for (int i = 0; i < n - 1; i++)
            for (int j = 0; j < n - i - 1; j++)
                if (datos[j] > datos[j + 1])
                    intercambiar(datos, j, j + 1);
    }

    @Override
    public String getNombre() { return "Bubble Sort"; }
}

public class OrdenamientoInsercion extends EstrategiaOrdenamiento {
    @Override
    public void ordenar(int[] datos) {
        for (int i = 1; i < datos.length; i++) {
            int clave = datos[i];
            int j = i - 1;
            while (j >= 0 && datos[j] > clave) {
                datos[j + 1] = datos[j];
                j--;
            }
            datos[j + 1] = clave;
        }
    }

    @Override
    public String getNombre() { return "Insertion Sort"; }
}

public class OrdenamientoSeleccion extends EstrategiaOrdenamiento {
    @Override
    public void ordenar(int[] datos) {
        int n = datos.length;
        for (int i = 0; i < n - 1; i++) {
            int minIdx = i;
            for (int j = i + 1; j < n; j++)
                if (datos[j] < datos[minIdx]) minIdx = j;
            intercambiar(datos, minIdx, i);
        }
    }

    @Override
    public String getNombre() { return "Selection Sort"; }
}

// 3. El contexto que usa la estrategia polimórficamente
public class Ordenador {
    private EstrategiaOrdenamiento estrategia;

    public Ordenador(EstrategiaOrdenamiento estrategia) {
        this.estrategia = estrategia;
    }

    // Cambiar la estrategia en tiempo de ejecución
    public void setEstrategia(EstrategiaOrdenamiento estrategia) {
        this.estrategia = estrategia;
    }

    public void ordenar(int[] datos) {
        System.out.print("Ordenando con " + estrategia.getNombre() + "... ");
        long inicio = System.nanoTime();
        estrategia.ordenar(datos);          // Polimorfismo: delega a la estrategia
        long fin = System.nanoTime();
        System.out.printf("Listo en %d ns%n", (fin - inicio));
    }
}

// 4. Uso: intercambiar estrategias sin modificar el Ordenador
int[] datos1 = {64, 34, 25, 12, 22, 11, 90};
int[] datos2 = datos1.clone();
int[] datos3 = datos1.clone();

Ordenador ordenador = new Ordenador(new OrdenamientoBurbuja());
ordenador.ordenar(datos1);

ordenador.setEstrategia(new OrdenamientoInsercion());
ordenador.ordenar(datos2);

ordenador.setEstrategia(new OrdenamientoSeleccion());
ordenador.ordenar(datos3);

// Ordenando con Bubble Sort... Listo en X ns
// Ordenando con Insertion Sort... Listo en X ns
// Ordenando con Selection Sort... Listo en X ns
```

---

## 9. Polimorfismo y el tipo Object

Como toda clase hereda de `Object`, puedes usar `Object` como el tipo más general de todos. Esto permite escribir métodos completamente genéricos.

### Métodos que aceptan cualquier objeto

```java
public class Utilidades {

    // Acepta absolutamente cualquier objeto
    public static void imprimirTipo(Object obj) {
        if (obj == null) {
            System.out.println("null");
            return;
        }
        System.out.printf("Tipo: %-20s | Valor: %s%n",
            obj.getClass().getSimpleName(), obj.toString());
    }

    // Compara cualquier par de objetos
    public static boolean sonIguales(Object a, Object b) {
        if (a == null && b == null) return true;
        if (a == null || b == null) return false;
        return a.equals(b);
    }

    // Array de cualquier tipo de objeto
    public static void imprimirArray(Object[] arr) {
        System.out.print("[");
        for (int i = 0; i < arr.length; i++) {
            System.out.print(arr[i]);
            if (i < arr.length - 1) System.out.print(", ");
        }
        System.out.println("]");
    }
}

// Uso con cualquier tipo:
Utilidades.imprimirTipo(42);                  // Tipo: Integer | Valor: 42
Utilidades.imprimirTipo("Hola");              // Tipo: String  | Valor: Hola
Utilidades.imprimirTipo(new Circulo("R", 5)); // Tipo: Circulo | Valor: ...
Utilidades.imprimirTipo(3.14);                // Tipo: Double  | Valor: 3.14
Utilidades.imprimirTipo(null);                // null

Object[] mezcla = {"Hola", 42, true, 3.14, new Circulo("Rojo", 5.0)};
Utilidades.imprimirArray(mezcla);
```

### Los métodos de Object disponibles en todos los objetos

```java
Object obj = new Circulo("Azul", 3.0);

// Todos estos métodos están disponibles en CUALQUIER objeto
obj.toString();                    // Representación como String
obj.equals(new Circulo("R", 3));   // Comparación de igualdad
obj.hashCode();                    // Código hash
obj.getClass();                    // Clase del objeto
obj.getClass().getName();          // "Circulo" (o el paquete completo)
obj.getClass().getSimpleName();    // "Circulo"
```

---

## 10. Covarianza en tipos de retorno

Java permite que cuando sobreescribes un método, el tipo de retorno puede ser un **subtipo** del tipo de retorno original. Esto se llama **covarianza** y es una forma de polimorfismo en los tipos de retorno.

```java
public class Animal {
    // Retorna Animal
    public Animal criarCria() {
        return new Animal();
    }
}

public class Perro extends Animal {
    // Retorna Perro (subtipo de Animal) — covarianza válida en Java
    @Override
    public Perro criarCria() {
        return new Perro();
    }
}

public class Gato extends Animal {
    @Override
    public Gato criarCria() {
        return new Gato();
    }
}

// Uso:
Perro perro = new Perro();
Perro cria  = perro.criarCria();  // No necesitas cast: ya es Perro

Animal animal = new Perro();
Animal criaN  = animal.criarCria();  // Devuelve Perro (polimorfismo)
```

### Covarianza en el patrón Builder/Factory

```java
public abstract class ConstructorBase<T extends ConstructorBase<T>> {
    protected String nombre;
    protected String color;

    @SuppressWarnings("unchecked")
    public T conNombre(String nombre) {
        this.nombre = nombre;
        return (T) this;
    }

    @SuppressWarnings("unchecked")
    public T conColor(String color) {
        this.color = color;
        return (T) this;
    }

    public abstract Figura construir();
}

public class ConstructorCirculo extends ConstructorBase<ConstructorCirculo> {
    private double radio;

    public ConstructorCirculo conRadio(double radio) {
        this.radio = radio;
        return this;
    }

    @Override
    public Circulo construir() {  // Covarianza: retorna Circulo, no Figura
        return new Circulo(color, radio);
    }
}

// Uso fluido con covarianza:
Circulo c = new ConstructorCirculo()
    .conColor("Rojo")
    .conRadio(5.0)
    .construir();
```

---

## 11. Principio de Sustitución de Liskov (LSP)

El **Principio de Sustitución de Liskov** (LSP) establece:

> Si S es un subtipo de T, entonces los objetos de tipo T pueden sustituirse por objetos de tipo S sin alterar las propiedades del programa.

En palabras simples: una subclase debe poder usarse en cualquier lugar donde se espere la superclase, sin romper el comportamiento esperado.

### El problema del Cuadrado-Rectángulo

Este es el ejemplo clásico de violación del LSP:

```java
// VIOLACIÓN DEL LSP
public class Rectangulo {
    protected double ancho;
    protected double alto;

    public void setAncho(double ancho) { this.ancho = ancho; }
    public void setAlto(double alto)   { this.alto = alto; }
    public double calcularArea()       { return ancho * alto; }
}

// Matemáticamente, un cuadrado ES un rectángulo
// Pero en código, esta herencia viola el LSP:
public class Cuadrado extends Rectangulo {
    @Override
    public void setAncho(double lado) {
        this.ancho = lado;
        this.alto  = lado;  // Un cuadrado mantiene igual ancho y alto
    }

    @Override
    public void setAlto(double lado) {
        this.ancho = lado;
        this.alto  = lado;
    }
}

// PROBLEMA:
public static void probarRectangulo(Rectangulo r) {
    r.setAncho(5);
    r.setAlto(4);
    // Esperamos área = 5 × 4 = 20
    System.out.println(r.calcularArea());
    // Con Rectangulo: imprime 20 ← correcto
    // Con Cuadrado:   imprime 16 ← ¡incorrecto! El cuadrado cambió el ancho también
}

Rectangulo rect = new Rectangulo();
Cuadrado   cuad = new Cuadrado();

probarRectangulo(rect);  // 20.0 ← OK
probarRectangulo(cuad);  // 16.0 ← Viola el LSP: el comportamiento es distinto
```

### Solución que respeta el LSP

```java
// En vez de herencia, usar abstracción común
public abstract class Figura {
    public abstract double calcularArea();
}

public class Rectangulo extends Figura {
    private double ancho;
    private double alto;

    public Rectangulo(double ancho, double alto) {
        this.ancho = ancho;
        this.alto  = alto;
    }
    // sin setters separados para ancho y alto

    @Override
    public double calcularArea() { return ancho * alto; }
}

public class Cuadrado extends Figura {
    private double lado;

    public Cuadrado(double lado) { this.lado = lado; }

    @Override
    public double calcularArea() { return lado * lado; }
}

// Ahora se cumple el LSP: ambas figuras se comportan correctamente
public static void mostrarArea(Figura f) {
    System.out.println("Área: " + f.calcularArea());
}

mostrarArea(new Rectangulo(5, 4));  // Área: 20.0
mostrarArea(new Cuadrado(5));       // Área: 25.0
```

### Checklist para verificar el LSP

Antes de usar herencia, verifica:

```
✓ ¿La subclase cumple TODAS las precondiciones de la superclase?
✓ ¿La subclase garantiza TODAS las postcondiciones de la superclase?
✓ ¿La subclase NO lanza excepciones nuevas que la superclase no lanzaría?
✓ ¿El comportamiento de la subclase es consistente con lo que el cliente espera?
✓ ¿Puedes reemplazar cualquier instancia de la superclase por la subclase sin romper nada?

Si alguna respuesta es NO → considera composición en vez de herencia.
```

---

## 12. Polimorfismo vs. condicionales

Uno de los beneficios más importantes del polimorfismo es **eliminar estructuras if-else largas** que comprueban el tipo de un objeto.

### El código sin polimorfismo — frágil y difícil de mantener

```java
// MAL DISEÑO: cada operación nueva requiere modificar TODOS estos if-else
public class ProcesadorSinPolimorfismo {

    public static double calcularArea(Object figura) {
        if (figura instanceof Circulo c) {
            return Math.PI * c.getRadio() * c.getRadio();
        } else if (figura instanceof Rectangulo r) {
            return r.getAncho() * r.getAlto();
        } else if (figura instanceof Triangulo t) {
            // fórmula de Herón...
            return 0;
        }
        // ¿Y si agrego Pentagono? Debo modificar AQUÍ también
        return 0;
    }

    public static void dibujar(Object figura) {
        if (figura instanceof Circulo) {
            System.out.println("Dibujando círculo");
        } else if (figura instanceof Rectangulo) {
            System.out.println("Dibujando rectángulo");
        }
        // ¿Y si agrego Pentagono? Debo modificar AQUÍ también
    }

    public static String describir(Object figura) {
        if (figura instanceof Circulo) {
            return "Es un círculo";
        } else if (figura instanceof Rectangulo) {
            return "Es un rectángulo";
        }
        // ¿Y si agrego Pentagono? Debo modificar AQUÍ también
        return "Desconocido";
    }
}
```

### El código con polimorfismo — robusto y extensible

```java
// BUEN DISEÑO: añadir un nuevo tipo solo requiere crear una nueva clase
public abstract class Figura {
    public abstract double calcularArea();
    public abstract void dibujar();
    public abstract String describir();
}

public class Pentagono extends Figura {
    private double lado;
    public Pentagono(double lado) { this.lado = lado; }

    @Override public double calcularArea()  { return (Math.sqrt(25 + 10*Math.sqrt(5)) / 4) * lado * lado; }
    @Override public void dibujar()         { System.out.println("Dibujando pentágono ⬠"); }
    @Override public String describir()     { return "Pentágono de lado " + lado; }
}

// El código que usa Figura NO necesita modificarse al añadir Pentagono
public class Procesador {
    public static void procesar(List<Figura> figuras) {
        for (Figura f : figuras) {
            System.out.println(f.describir());
            f.dibujar();
            System.out.printf("Área: %.2f%n", f.calcularArea());
        }
    }
}

// Añadir Pentagono no requiere cambiar Procesador
List<Figura> lista = new ArrayList<>();
lista.add(new Circulo("Rojo", 5.0));
lista.add(new Rectangulo("Azul", 4.0, 6.0));
lista.add(new Pentagono(5.0));   // ¡Nuevo tipo, sin modificar nada más!

Procesador.procesar(lista);
```

### El principio Open/Closed

El polimorfismo es la base del **Principio Open/Closed** (OCP):

> Las entidades de software deben estar **abiertas para extensión** pero **cerradas para modificación**.

```
SIN polimorfismo:
  Añadir un nuevo tipo  →  Modificar el código existente  →  Riesgo de romper algo

CON polimorfismo:
  Añadir un nuevo tipo  →  Crear una nueva clase  →  El código existente no cambia
```

---

## 13. Errores comunes con Polimorfismo

### Error 1: Intentar acceder a métodos específicos de la subclase sin downcast

```java
Figura f = new Circulo("Rojo", 5.0);

// ERROR en compilación: getRadio() no existe en Figura
double radio = f.getRadio();

// CORRECTO:
if (f instanceof Circulo c) {
    double radio = c.getRadio();  // Pattern matching (Java 16+)
}
```

### Error 2: Confundir sobrecarga con sobreescritura

```java
public class Animal {
    public void comer(String comida) { System.out.println("Come " + comida); }
}

public class Perro extends Animal {
    // SOBRECARGA: diferente tipo de parámetro → método NUEVO
    public void comer(int cantidad) {
        System.out.println("Come " + cantidad + " porciones");
    }

    // Para SOBREESCRIBIR debe tener la MISMA firma:
    @Override
    public void comer(String comida) {
        System.out.println("El perro devora " + comida);
    }
}
```

### Error 3: ClassCastException por downcast incorrecto

```java
Animal a = new Gato("Luna");
// INCORRECTO: un Gato no ES un Perro
Perro p = (Perro) a;  // ClassCastException en tiempo de ejecución

// CORRECTO: siempre verificar antes
if (a instanceof Perro perro) {
    perro.ladrar();
} else {
    System.out.println("No es un Perro: " + a.getClass().getSimpleName());
}
```

### Error 4: Confundir métodos static con polimorfismo

```java
public class Animal {
    public static String getTipo() { return "Animal"; }
    public String sonido()         { return "..."; }
}

public class Perro extends Animal {
    public static String getTipo() { return "Perro"; }  // OCULTA, no sobreescribe
    @Override
    public String sonido()         { return "Guau"; }   // SÍ sobreescribe
}

Animal a = new Perro();
System.out.println(a.getTipo());  // "Animal" ← early binding: tipo de REFERENCIA
System.out.println(a.sonido());   // "Guau"   ← late binding: tipo REAL del objeto
```

### Error 5: Violación del LSP sin saberlo

```java
public class Ave {
    public void volar() { System.out.println("El ave vuela."); }
}

// Pingüino ES un Ave pero NO puede volar → violación del LSP
public class Pinguino extends Ave {
    @Override
    public void volar() {
        throw new UnsupportedOperationException("Los pingüinos no vuelan.");
    }
}

// PROBLEMA: código que espera Ave no puede usar Pinguino sin romperse
public static void hacerVolar(Ave ave) {
    ave.volar();  // Lanza excepción si es Pinguino
}

// SOLUCIÓN: rediseñar la jerarquía
public abstract class Ave { public abstract void moverse(); }
public abstract class AveVoladora extends Ave {
    public void volar() { System.out.println("Vuela"); }
    @Override public void moverse() { volar(); }
}
public class Pinguino extends Ave {
    @Override public void moverse() { System.out.println("El pingüino nada."); }
}
```

### Error 6: Pérdida de tipo en colecciones de Object

```java
// Usar Object como tipo en colecciones pierde el tipo
List<Object> lista = new ArrayList<>();
lista.add(new Circulo("Rojo", 5.0));
lista.add("Un texto");  // Se puede añadir cualquier cosa

// Requiere instanceof y downcast para cada operación
for (Object obj : lista) {
    if (obj instanceof Circulo c) {
        System.out.println(c.calcularArea());
    }
}

// MEJOR: usar el tipo más específico posible
List<Figura> figuras = new ArrayList<>();  // Solo Figuras, no cualquier Object
figuras.add(new Circulo("Rojo", 5.0));
// figuras.add("Un texto");  // ERROR en compilación — mucho mejor
```

---

## 14. Ejercicios prácticos

### Ejercicio 1 — Zoológico polimórfico (Nivel: Básico)

Crea la jerarquía:
- `Animal` (abstracta): `nombre`, `edad`, `hacerSonido()` (abstracto), `moverse()` (abstracto), `comer()` (concreto: imprime qué come)
- `Mamifero extends Animal`: `tipoPelo`
- `Ave extends Animal`: `envergadura`, `puedeVolar`
- `Pez extends Animal`: `aguaDulce` (boolean)
- Concretas: `Perro`, `Leon`, `Aguila`, `Pinguino`, `Salmon`, `Tiburon`

En `Main`:
1. Crea un `Animal[]` con al menos 2 de cada tipo
2. Con un solo bucle, llama a `hacerSonido()`, `moverse()` y `comer()` para cada uno
3. Cuenta cuántos de cada tipo hay usando `instanceof`
4. Encuentra el animal más viejo sin saber qué tipo es

---

### Ejercicio 2 — Sistema de descuentos con Strategy (Nivel: Básico-Medio)

Implementa el patrón Strategy para calcular descuentos:

- `EstrategiaDescuento` (abstracta): `calcularDescuento(double precioOriginal)` (abstracto), `getNombre()` (abstracto)
- `SinDescuento`: retorna 0
- `DescuentoPorcentaje`: descuento de un porcentaje dado
- `DescuentoFijo`: resta una cantidad fija (sin bajar de 0)
- `DescuentoEscalonado`: sin descuento si < $50, 10% si $50–$100, 20% si > $100
- `DescuentoTemporada`: el doble del porcentaje durante temporada de ofertas

Crea `Producto` con `nombre`, `precio` y `EstrategiaDescuento`. El método `getPrecioFinal()` aplica la estrategia polimórficamente.

Crea un carrito de 6 productos con distintas estrategias y muestra el resumen total.

---

### Ejercicio 3 — Renderer gráfico (Nivel: Medio)

Simula un motor de renderizado con polimorfismo:

- `Elemento` (abstracta): `x`, `y`, `visible`, `render()` (abstracto), `mover(dx, dy)` (concreto), `toggleVisible()` (concreto)
- `Texto extends Elemento`: `contenido`, `fuente`, `tamano`
- `Imagen extends Elemento`: `ruta`, `ancho`, `alto`
- `Boton extends Elemento`: `etiqueta`, `color`, `habilitado`, `onClick()` (abstracto)
- `BotonPrimario extends Boton`: color azul por defecto, `onClick()` imprime acción primaria
- `BotonPeligro extends Boton`: color rojo por defecto, `onClick()` pide confirmación

Crea una `Pantalla` con `List<Elemento>`:
- `agregar(Elemento)`, `renderizarTodo()` (llama `render()` de cada visible)
- `moverTodo(dx, dy)`, `contarVisibles()`
- `buscarPorTipo(Class<?>)` — retorna lista filtrada

---

### Ejercicio 4 — Calculadora extensible (Nivel: Medio)

Diseña una calculadora donde cada operación es una clase:

- `Operacion` (abstracta): `aplicar(double a, double b)` (abstracto), `getSimbolo()` (abstracto), `getNombre()` (abstracto)
- Implementaciones: `Suma`, `Resta`, `Multiplicacion`, `Division`, `Potencia`, `Modulo`, `MaximoEntreAmbos`, `MinimoEntreAmbos`

Crea `Calculadora` con:
- `List<Operacion> operacionesRegistradas`
- `registrarOperacion(Operacion op)`
- `ejecutar(double a, double b, String simbolo)` — busca la operación por símbolo y la aplica
- `ejecutarTodas(double a, double b)` — aplica todas y muestra resultados
- `historial` — guarda los últimos 10 resultados

Demuestra añadiendo una operación nueva (`RaizCuadradaDeA`) sin modificar la clase `Calculadora`.

---

### Ejercicio 5 — Sistema de notificaciones (Nivel: Avanzado)

Implementa un sistema de notificaciones con polimorfismo completo:

- `CanalNotificacion` (abstracta): `destinatario`, `enviar(Mensaje m)` (abstracto), `validarDestinatario()` (abstracto), `getDescripcion()` (abstracto)
- `NotificacionEmail extends CanalNotificacion`: valida formato email, simula envío
- `NotificacionSMS extends CanalNotificacion`: valida formato teléfono, límite 160 chars
- `NotificacionPush extends CanalNotificacion`: valida token, envía inmediatamente
- `NotificacionWhatsApp extends CanalNotificacion`: valida número con código de país

- `Mensaje`: `asunto`, `contenido`, `prioridad` (BAJA/MEDIA/ALTA/CRITICA), `timestamp`

- `GestorNotificaciones`:
  - `List<CanalNotificacion> canales`
  - `registrarCanal(CanalNotificacion c)`
  - `enviarATodos(Mensaje m)` — polimórfico
  - `enviarConFiltro(Mensaje m, String tipoCanal)` — solo ciertos tipos
  - `enviarPorPrioridad(Mensaje m)` — CRITICA usa todos, ALTA solo email+push, etc.
  - `estadisticas()` — cuántos mensajes envió cada canal

---

### Ejercicio 6 — Desafío: motor de juego simplificado (Nivel: Avanzado)

Diseña el núcleo de un motor de juego 2D usando polimorfismo:

- `EntidadJuego` (abstracta): `x`, `y`, `nombre`, `activo`, `actualizar()` (abstracto), `renderizar()` (abstracto), `colisionaCon(EntidadJuego otra)` (concreto: comprueba coordenadas)
- `Jugador extends EntidadJuego`: `vida`, `puntuacion`, `velocidad`, `mover(String direccion)`, `atacar(Enemigo e)`
- `Enemigo extends EntidadJuego` (abstracta): `vida`, `danio`, `atacar(Jugador j)` (abstracto)
- `EnemigoBosico extends Enemigo`: movimiento simple (sigue al jugador)
- `EnemigoBoss extends Enemigo`: movimiento en patrón, doble daño
- `Proyectil extends EntidadJuego`: `velocidad`, `danio`, desaparece al colisionar
- `PowerUp extends EntidadJuego`: `tipo` (VIDA/VELOCIDAD/PUNTOS), `aplicar(Jugador j)`

`Motor`:
- `List<EntidadJuego> entidades`
- `cicloJuego()` — llama `actualizar()` de todas las entidades activas
- `renderizarEscena()` — llama `renderizar()` de todas las activas
- `detectarColisiones()` — verifica colisiones entre todas las entidades
- `limpiarInactivos()` — elimina entidades no activas

---

## 15. Resumen del tema

### Tipos de polimorfismo en Java

```
POLIMORFISMO
    │
    ├── ESTÁTICO (compilación)
    │   └── Sobrecarga (overloading)
    │       ├── Mismo nombre, distintos parámetros
    │       ├── Resuelto por el compilador
    │       └── El tipo de REFERENCIA determina la versión
    │
    └── DINÁMICO (ejecución)
        └── Sobreescritura (overriding)
            ├── Mismo nombre, mismos parámetros
            ├── Resuelto por la JVM (late binding)
            └── El tipo REAL del objeto determina la versión
```

### El polimorfismo dinámico en una página

```java
// 1. Define el tipo base con el método que varía
public abstract class Base {
    public abstract void metodo();    // Contrato
    public void comun() { }          // Implementación compartida
}

// 2. Cada subtipo implementa su versión
public class A extends Base {
    @Override public void metodo() { System.out.println("A"); }
}
public class B extends Base {
    @Override public void metodo() { System.out.println("B"); }
}

// 3. Usa la referencia del tipo base
Base obj1 = new A();   // Upcasting
Base obj2 = new B();

obj1.metodo();  // "A" ← la JVM decide en tiempo de ejecución
obj2.metodo();  // "B"

// 4. Colecciones polimórficas
List<Base> lista = new ArrayList<>();
lista.add(new A());
lista.add(new B());
lista.add(new A());

for (Base b : lista) {
    b.metodo();  // Versión correcta para cada objeto
}

// 5. Métodos que aceptan el tipo base
public static void procesar(Base b) {
    b.metodo();  // Polimórfico: funciona con cualquier subclase
}

procesar(new A());  // "A"
procesar(new B());  // "B"
```

### Reglas de oro del Polimorfismo

| Situación | Regla |
|-----------|-------|
| Sobrecarga | Diferente firma → el compilador decide → polimorfismo estático |
| Sobreescritura | Misma firma → la JVM decide → polimorfismo dinámico |
| `@Override` | Siempre usarla para que el compilador verifique la sobreescritura |
| `static` | No participa en el polimorfismo dinámico (early binding) |
| `private` | No participa en el polimorfismo dinámico |
| `final` | No puede sobreescribirse; no participa en el polimorfismo dinámico |
| Downcast | Siempre verificar con `instanceof` antes de hacer el cast |
| LSP | La subclase debe poder sustituir a la superclase sin romper el comportamiento |
| if-else por tipo | Si ves muchos `instanceof` en el código, considera reemplazarlos con polimorfismo |
| Colecciones | Usar el tipo más específico posible (`List<Figura>` mejor que `List<Object>`) |

---

## Próximo tema

**Tema 11: Abstracción e Interfaces**

Aprenderás la diferencia entre clases abstractas e interfaces, cuándo usar cada una, cómo las interfaces permiten simular herencia múltiple, los métodos `default` y `static` en interfaces (Java 8+), las interfaces funcionales, y cómo el polimorfismo se combina con interfaces para crear diseños extremadamente flexibles.

---

*Curso de Java — Nivel 2: Programación Orientada a Objetos*
*Tema 10 de 12*
*Creado con fines educativos*
