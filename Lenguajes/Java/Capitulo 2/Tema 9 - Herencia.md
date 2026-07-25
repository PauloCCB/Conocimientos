# Curso de Java — Nivel 2: Programación Orientada a Objetos
## Tema 9: Herencia

> **Nivel:** Intermedio | **Prerrequisitos:** Temas 1–8 completados | **Duración estimada:** 5–6 horas

---

## Tabla de contenidos

1. [¿Qué es la Herencia?](#1-qué-es-la-herencia)
2. [Sintaxis de herencia con extends](#2-sintaxis-de-herencia-con-extends)
3. [La clase Object — raíz de todo](#3-la-clase-object--raíz-de-todo)
4. [Constructores y la palabra super](#4-constructores-y-la-palabra-super)
5. [Sobreescritura de métodos — @Override](#5-sobreescritura-de-métodos--override)
6. [super para acceder al padre](#6-super-para-acceder-al-padre)
7. [Jerarquías de herencia multinivel](#7-jerarquías-de-herencia-multinivel)
8. [Modificador final en herencia](#8-modificador-final-en-herencia)
9. [Clases abstractas](#9-clases-abstractas)
10. [Herencia y encapsulamiento — protected](#10-herencia-y-encapsulamiento--protected)
11. [Upcasting y Downcasting](#11-upcasting-y-downcasting)
12. [Herencia vs. Composición](#12-herencia-vs-composición)
13. [Errores comunes con Herencia](#13-errores-comunes-con-herencia)
14. [Ejercicios prácticos](#14-ejercicios-prácticos)
15. [Resumen del tema](#15-resumen-del-tema)

---

## 1. ¿Qué es la Herencia?

La **herencia** es el segundo pilar de la POO. Permite crear una nueva clase basada en una clase existente, **heredando** automáticamente sus atributos y métodos, y añadiendo o modificando lo que se necesite.

La herencia modela una relación **"es un tipo de"** entre clases:

- Un `Perro` **es un tipo de** `Animal`
- Un `Empleado` **es un tipo de** `Persona`
- Una `CuentaAhorro` **es un tipo de** `CuentaBancaria`
- Un `Circulo` **es un tipo de** `Figura`

### El problema que resuelve la herencia

Sin herencia, si tienes `Perro`, `Gato` y `Pájaro`, cada clase repite el mismo código:

```java
// SIN herencia: código duplicado en cada clase
public class Perro {
    private String nombre;   // Duplicado
    private int edad;        // Duplicado
    private double peso;     // Duplicado
    public void comer() { System.out.println(nombre + " está comiendo."); }  // Duplicado
    public void dormir() { System.out.println(nombre + " está durmiendo."); } // Duplicado
    public void ladrar() { System.out.println("¡Guau!"); }  // Específico de Perro
}

public class Gato {
    private String nombre;   // Duplicado
    private int edad;        // Duplicado
    private double peso;     // Duplicado
    public void comer() { System.out.println(nombre + " está comiendo."); }  // Duplicado
    public void dormir() { System.out.println(nombre + " está durmiendo."); } // Duplicado
    public void maullar() { System.out.println("¡Miau!"); }  // Específico de Gato
}
```

Con herencia:

```java
// CON herencia: el código común va en la clase padre
public class Animal {
    private String nombre;   // Definido UNA VEZ
    private int edad;
    private double peso;
    public void comer()  { System.out.println(nombre + " está comiendo."); }
    public void dormir() { System.out.println(nombre + " está durmiendo."); }
}

// Las subclases heredan todo y solo añaden lo suyo
public class Perro extends Animal {
    public void ladrar()   { System.out.println("¡Guau!"); }
}

public class Gato extends Animal {
    public void maullar()  { System.out.println("¡Miau!"); }
}

public class Pajaro extends Animal {
    public void volar()    { System.out.println("El pájaro vuela."); }
    public void cantar()   { System.out.println("¡Pío pío!"); }
}
```

### Terminología

- **Superclase** (clase padre / clase base): la clase de la que se hereda. Ejemplo: `Animal`.
- **Subclase** (clase hija / clase derivada): la clase que hereda. Ejemplo: `Perro`, `Gato`.
- Una subclase **extiende** a su superclase.
- La subclase **hereda** todos los atributos y métodos no privados de la superclase.

### Qué se hereda y qué no

```
SE HEREDA:                          NO SE HEREDA:
──────────────────────────          ────────────────────────────
✓ Atributos public                  ✗ Atributos private
✓ Atributos protected               ✗ Constructores
✓ Atributos package-private         ✗ Métodos private
✓ Métodos public                    ✗ La relación "es un" no aplica
✓ Métodos protected                   para static members
✓ Métodos package-private
✓ Métodos static (accesibles,
  no sobreescribibles)
```

---

## 2. Sintaxis de herencia con extends

La palabra clave `extends` establece la relación de herencia:

```java
public class Subclase extends Superclase {
    // Atributos y métodos adicionales propios de la subclase
}
```

### Ejemplo completo y progresivo

**Superclase — Figura:**

```java
public class Figura {
    protected String color;
    protected String nombre;

    public Figura(String nombre, String color) {
        this.nombre = nombre;
        this.color  = color;
    }

    public String getColor()  { return color; }
    public String getNombre() { return nombre; }

    public double calcularArea() {
        return 0.0;  // Las subclases lo sobreescribirán
    }

    public double calcularPerimetro() {
        return 0.0;
    }

    public void describir() {
        System.out.printf("%s %s → Área: %.2f | Perímetro: %.2f%n",
            color, nombre, calcularArea(), calcularPerimetro());
    }

    @Override
    public String toString() {
        return String.format("%s[color=%s, área=%.2f]", nombre, color, calcularArea());
    }
}
```

**Subclase — Circulo:**

```java
public class Circulo extends Figura {
    private double radio;

    public Circulo(String color, double radio) {
        super("Círculo", color);  // Llama al constructor de Figura
        this.radio = radio;
    }

    public double getRadio() { return radio; }

    @Override
    public double calcularArea() {
        return Math.PI * radio * radio;
    }

    @Override
    public double calcularPerimetro() {
        return 2 * Math.PI * radio;
    }
}
```

**Subclase — Rectangulo:**

```java
public class Rectangulo extends Figura {
    private double ancho;
    private double alto;

    public Rectangulo(String color, double ancho, double alto) {
        super("Rectángulo", color);
        this.ancho = ancho;
        this.alto  = alto;
    }

    public double getAncho() { return ancho; }
    public double getAlto()  { return alto; }

    @Override
    public double calcularArea() {
        return ancho * alto;
    }

    @Override
    public double calcularPerimetro() {
        return 2 * (ancho + alto);
    }

    public boolean esCuadrado() {
        return ancho == alto;
    }
}
```

**Subclase — Triangulo:**

```java
public class Triangulo extends Figura {
    private double ladoA;
    private double ladoB;
    private double ladoC;

    public Triangulo(String color, double a, double b, double c) {
        super("Triángulo", color);
        this.ladoA = a;
        this.ladoB = b;
        this.ladoC = c;
    }

    @Override
    public double calcularPerimetro() {
        return ladoA + ladoB + ladoC;
    }

    @Override
    public double calcularArea() {
        // Fórmula de Herón
        double s = calcularPerimetro() / 2;
        return Math.sqrt(s * (s-ladoA) * (s-ladoB) * (s-ladoC));
    }
}
```

**Uso en Main:**

```java
public class Main {
    public static void main(String[] args) {
        Circulo c    = new Circulo("Rojo", 5.0);
        Rectangulo r = new Rectangulo("Azul", 4.0, 6.0);
        Triangulo t  = new Triangulo("Verde", 3.0, 4.0, 5.0);

        c.describir();   // Rojo Círculo → Área: 78.54 | Perímetro: 31.42
        r.describir();   // Azul Rectángulo → Área: 24.00 | Perímetro: 20.00
        t.describir();   // Verde Triángulo → Área: 6.00 | Perímetro: 12.00

        // Los métodos heredados funcionan en las subclases
        System.out.println(c.getColor());   // Rojo  ← heredado de Figura
        System.out.println(r.getNombre());  // Rectángulo ← heredado

        // Los métodos propios de la subclase también están disponibles
        System.out.println(r.esCuadrado()); // false
        System.out.println(c.getRadio());   // 5.0
    }
}
```

### Java solo permite herencia simple

A diferencia de C++, Java **no permite herencia múltiple de clases** (una clase no puede tener dos superclases directas). Esto evita el "problema del diamante":

```java
public class A { void metodo() { } }
public class B { void metodo() { } }

// IMPOSIBLE en Java:
public class C extends A, B { }  // Error de compilación

// Para herencia de múltiples comportamientos, Java usa interfaces (Tema 11)
```

---

## 3. La clase Object — raíz de todo

En Java, **toda clase hereda implícitamente de `Object`**. Si no declaras `extends`, Java añade automáticamente `extends Object`. Esto significa que todos los objetos en Java comparten los métodos básicos de `Object`.

```java
public class MiClase { }
// Es exactamente lo mismo que:
public class MiClase extends Object { }
```

### La jerarquía completa

```
           Object
          /   |   \
    Animal  String  Integer  ...
    /    \
  Perro  Gato
```

### Métodos importantes de Object

| Método | Descripción |
|--------|-------------|
| `toString()` | Representación textual del objeto |
| `equals(Object o)` | Comparación de igualdad |
| `hashCode()` | Código hash del objeto |
| `getClass()` | Devuelve la clase del objeto en tiempo de ejecución |
| `clone()` | Crea una copia del objeto |
| `finalize()` | Llamado antes de GC (obsoleto desde Java 9) |

### toString() heredado de Object

Si no sobreescribes `toString()`, Java usa el de `Object`, que devuelve algo como `NombreClase@codigoHex`:

```java
public class Punto {
    double x, y;
    public Punto(double x, double y) { this.x = x; this.y = y; }
}

Punto p = new Punto(3, 4);
System.out.println(p);  // Punto@7852e922  ← el toString() de Object, poco útil
```

Por eso siempre debes sobreescribir `toString()`.

### equals() y hashCode() heredados de Object

Por defecto, `equals()` de Object compara referencias (como `==`). Para comparar contenido, debes sobreescribirlo:

```java
public class Punto {
    private double x;
    private double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;          // Misma referencia
        if (obj == null) return false;          // null nunca es igual
        if (getClass() != obj.getClass()) return false;  // Distinto tipo

        Punto otro = (Punto) obj;               // Cast seguro
        return Double.compare(x, otro.x) == 0
            && Double.compare(y, otro.y) == 0;
    }

    @Override
    public int hashCode() {
        return java.util.Objects.hash(x, y);
        // Regla: si dos objetos son equals(), deben tener el mismo hashCode()
    }
}

// Uso:
Punto p1 = new Punto(3, 4);
Punto p2 = new Punto(3, 4);
Punto p3 = new Punto(1, 2);

System.out.println(p1.equals(p2));  // true  ← mismo contenido
System.out.println(p1.equals(p3));  // false
System.out.println(p1 == p2);       // false ← referencias distintas
```

---

## 4. Constructores y la palabra super

Los constructores **no se heredan**. Cuando creas un objeto de una subclase, Java debe inicializar también la parte de la superclase. Para eso se usa `super()`.

### Regla de oro de los constructores en herencia

La **primera línea** de todo constructor de una subclase debe ser una llamada a un constructor del padre (`super(...)`) o a otro constructor de la misma clase (`this(...)`). Si no escribes nada, Java añade implícitamente `super()` (sin argumentos).

### Llamada implícita a super()

```java
public class Animal {
    protected String nombre;

    public Animal() {
        System.out.println("Constructor de Animal sin parámetros");
        this.nombre = "Sin nombre";
    }
}

public class Perro extends Animal {
    private String raza;

    public Perro(String raza) {
        // Java añade implícitamente: super();
        System.out.println("Constructor de Perro");
        this.raza = raza;
    }
}

// Al ejecutar:
Perro p = new Perro("Labrador");
// Constructor de Animal sin parámetros ← se llama primero
// Constructor de Perro
```

### Llamada explícita a super(argumentos)

Cuando la superclase no tiene constructor sin parámetros, **debes** llamar explícitamente al constructor correcto:

```java
public class Vehiculo {
    private String marca;
    private String modelo;
    private int año;

    public Vehiculo(String marca, String modelo, int año) {
        this.marca  = marca;
        this.modelo = modelo;
        this.año    = año;
    }

    public String getMarca()  { return marca; }
    public String getModelo() { return modelo; }
    public int getAño()       { return año; }

    @Override
    public String toString() {
        return marca + " " + modelo + " (" + año + ")";
    }
}

public class Automovil extends Vehiculo {
    private int numeroPuertas;
    private String tipoCombustible;

    public Automovil(String marca, String modelo, int año,
                     int puertas, String combustible) {
        super(marca, modelo, año);  // OBLIGATORIO: no existe Vehiculo() sin params
        this.numeroPuertas   = puertas;
        this.tipoCombustible = combustible;
    }

    public int getNumeroPuertas()    { return numeroPuertas; }
    public String getTipoCombustible(){ return tipoCombustible; }

    @Override
    public String toString() {
        return super.toString() + " [" + numeroPuertas + " puertas, " + tipoCombustible + "]";
    }
}

public class Camion extends Vehiculo {
    private double capacidadToneladas;

    public Camion(String marca, String modelo, int año, double capacidad) {
        super(marca, modelo, año);
        this.capacidadToneladas = capacidad;
    }

    @Override
    public String toString() {
        return super.toString() + " [Camión " + capacidadToneladas + "T]";
    }
}

// Uso:
Automovil auto = new Automovil("Toyota", "Corolla", 2022, 4, "Gasolina");
Camion camion  = new Camion("Mercedes", "Actros", 2021, 20.0);

System.out.println(auto);    // Toyota Corolla (2022) [4 puertas, Gasolina]
System.out.println(camion);  // Mercedes Actros (2021) [Camión 20.0T]
System.out.println(auto.getMarca());  // Toyota ← heredado de Vehiculo
```

### Orden de ejecución de constructores

En una cadena de herencia, los constructores se ejecutan de **arriba hacia abajo** (del padre al hijo):

```java
public class A {
    public A() { System.out.println("Constructor A"); }
}
public class B extends A {
    public B() { System.out.println("Constructor B"); }
}
public class C extends B {
    public C() { System.out.println("Constructor C"); }
}

new C();
// Salida:
// Constructor A  ← primero el abuelo
// Constructor B  ← luego el padre
// Constructor C  ← finalmente el hijo
```

---

## 5. Sobreescritura de métodos — @Override

La **sobreescritura** (overriding) permite que una subclase proporcione su propia implementación de un método heredado de la superclase. El método de la subclase **reemplaza** al del padre cuando se llama sobre un objeto de la subclase.

### Reglas de la sobreescritura

Para sobreescribir un método, la subclase debe:
1. Tener el **mismo nombre** que el método del padre.
2. Tener los **mismos parámetros** (tipo y orden).
3. Tener el **mismo tipo de retorno** o un subtipo (covarianza).
4. Tener un nivel de acceso **igual o más permisivo** (no más restrictivo).
5. No puede sobreescribir métodos `final` o `static`.

### La anotación @Override

`@Override` le indica al compilador que este método intenta sobreescribir uno de la superclase. Si hay un error (como un nombre mal escrito), el compilador lo detecta:

```java
public class Animal {
    public void hacerSonido() {
        System.out.println("El animal hace un sonido.");
    }
    public String toString() { return "Animal"; }
}

public class Perro extends Animal {
    @Override
    public void hacerSonido() {               // Sobreescritura correcta
        System.out.println("¡Guau! ¡Guau!");
    }

    @Override
    public void hacerSonidoo() { }            // ERROR: typo detectado por @Override
    // Error: method does not override a method from its superclass

    public void hacerSonidoo() { }            // Sin @Override: compila sin error
                                              // pero es un método NUEVO, no sobreescritura
}
```

### Ejemplo completo de sobreescritura

```java
public class Empleado {
    protected String nombre;
    protected double salarioBase;

    public Empleado(String nombre, double salarioBase) {
        this.nombre      = nombre;
        this.salarioBase = salarioBase;
    }

    public double calcularSalario() {
        return salarioBase;
    }

    public String obtenerInfo() {
        return String.format("Empleado: %s | Salario: $%.2f", nombre, calcularSalario());
    }

    @Override
    public String toString() {
        return obtenerInfo();
    }
}

public class EmpleadoConComision extends Empleado {
    private double ventas;
    private double porcentajeComision;

    public EmpleadoConComision(String nombre, double salarioBase,
                                double ventas, double porcentaje) {
        super(nombre, salarioBase);
        this.ventas             = ventas;
        this.porcentajeComision = porcentaje;
    }

    @Override
    public double calcularSalario() {
        return salarioBase + (ventas * porcentajeComision / 100.0);
    }

    @Override
    public String obtenerInfo() {
        return String.format(
            "Vendedor: %s | Base: $%.2f | Comisión: $%.2f | Total: $%.2f",
            nombre, salarioBase,
            ventas * porcentajeComision / 100.0,
            calcularSalario());
    }
}

public class Gerente extends Empleado {
    private double bonoAnual;
    private int equipoACargo;

    public Gerente(String nombre, double salarioBase, double bono, int equipo) {
        super(nombre, salarioBase);
        this.bonoAnual    = bono;
        this.equipoACargo = equipo;
    }

    @Override
    public double calcularSalario() {
        return salarioBase + (bonoAnual / 12.0);  // Bono mensual prorrateado
    }

    @Override
    public String obtenerInfo() {
        return String.format(
            "Gerente: %s | Base: $%.2f | Bono mensual: $%.2f | Equipo: %d personas",
            nombre, salarioBase, bonoAnual / 12.0, equipoACargo);
    }
}

// Uso:
Empleado e = new Empleado("Pedro", 2000);
EmpleadoConComision v = new EmpleadoConComision("Laura", 1500, 10000, 5);
Gerente g = new Gerente("Carlos", 5000, 24000, 8);

System.out.println(e);
System.out.println(v);
System.out.println(g);
// Empleado: Pedro | Salario: $2000.00
// Vendedor: Laura | Base: $1500.00 | Comisión: $500.00 | Total: $2000.00
// Gerente: Carlos | Base: $5000.00 | Bono mensual: $2000.00 | Equipo: 8 personas
```

---

## 6. super para acceder al padre

La palabra clave `super` tiene dos usos:
1. Llamar al constructor del padre: `super(args)` — visto en la sección anterior.
2. Acceder a métodos o atributos del padre que han sido sobreescritos.

### Acceder al método sobreescrito del padre

```java
public class Figura {
    protected String color;

    public String describir() {
        return "Figura de color " + color;
    }
}

public class Circulo extends Figura {
    private double radio;

    @Override
    public String describir() {
        // Llama al método describir() de Figura y le añade información
        return super.describir() + ", radio=" + radio;
    }
}

Circulo c = new Circulo("rojo", 5.0);
System.out.println(c.describir());
// Figura de color rojo, radio=5.0
```

### Acceder a atributos del padre con super

```java
public class Base {
    protected int valor = 10;
}

public class Derivada extends Base {
    private int valor = 20;  // Shadow del atributo del padre

    public void mostrar() {
        System.out.println(valor);         // 20 (local)
        System.out.println(super.valor);   // 10 (del padre)
    }
}
```

### toString() que extiende al padre

Un patrón muy común es que el `toString()` de la subclase llame al del padre y añada su información:

```java
public class Persona {
    private String nombre;
    private int edad;

    @Override
    public String toString() {
        return String.format("Persona{nombre='%s', edad=%d}", nombre, edad);
    }
}

public class Estudiante extends Persona {
    private String carrera;
    private double promedio;

    @Override
    public String toString() {
        return super.toString()  // Reutiliza el toString() del padre
            + String.format(", carrera='%s', promedio=%.2f", carrera, promedio);
    }
}

// Salida: Persona{nombre='Ana', edad=22}, carrera='Ingeniería', promedio=9.20
```

---

## 7. Jerarquías de herencia multinivel

Java permite cadenas de herencia donde una subclase puede ser a su vez superclase de otra clase. No hay límite de niveles (aunque más de 3-4 niveles suele ser un indicador de mal diseño).

### Jerarquía de cuentas bancarias

```java
// Nivel 1: Clase base
public class CuentaBancaria {
    protected String numeroCuenta;
    protected String titular;
    protected double saldo;

    public CuentaBancaria(String numero, String titular, double saldoInicial) {
        this.numeroCuenta = numero;
        this.titular      = titular;
        this.saldo        = saldoInicial;
    }

    public boolean depositar(double monto) {
        if (monto <= 0) return false;
        saldo += monto;
        return true;
    }

    public boolean retirar(double monto) {
        if (monto <= 0 || monto > saldo) return false;
        saldo -= monto;
        return true;
    }

    public double getSaldo()          { return saldo; }
    public String getTitular()        { return titular; }
    public String getNumeroCuenta()   { return numeroCuenta; }

    @Override
    public String toString() {
        return String.format("[%s] %s: $%.2f", numeroCuenta, titular, saldo);
    }
}

// Nivel 2: CuentaAhorro extiende CuentaBancaria
public class CuentaAhorro extends CuentaBancaria {
    private double tasaInteres;  // Anual en porcentaje
    private int retirosMes;
    private static final int MAX_RETIROS = 3;

    public CuentaAhorro(String numero, String titular,
                         double saldoInicial, double tasaInteres) {
        super(numero, titular, saldoInicial);
        this.tasaInteres = tasaInteres;
        this.retirosMes  = 0;
    }

    @Override
    public boolean retirar(double monto) {
        if (retirosMes >= MAX_RETIROS) {
            System.out.println("Límite de retiros mensuales alcanzado.");
            return false;
        }
        boolean exito = super.retirar(monto);
        if (exito) retirosMes++;
        return exito;
    }

    public void aplicarInteres() {
        double interesMensual = saldo * (tasaInteres / 100.0 / 12.0);
        saldo += interesMensual;
        System.out.printf("Interés aplicado: +$%.2f%n", interesMensual);
    }

    public void resetRetirosMes()    { retirosMes = 0; }
    public double getTasaInteres()   { return tasaInteres; }

    @Override
    public String toString() {
        return super.toString() + String.format(" [Ahorro %.2f%%]", tasaInteres);
    }
}

// Nivel 2: CuentaCorriente extiende CuentaBancaria
public class CuentaCorriente extends CuentaBancaria {
    private double limiteDescubierto;  // Cuánto puede estar en negativo

    public CuentaCorriente(String numero, String titular,
                            double saldoInicial, double limiteDescubierto) {
        super(numero, titular, saldoInicial);
        this.limiteDescubierto = limiteDescubierto;
    }

    @Override
    public boolean retirar(double monto) {
        if (monto <= 0 || monto > saldo + limiteDescubierto) return false;
        saldo -= monto;  // Puede quedar negativo hasta el límite
        return true;
    }

    public double getLimiteDescubierto() { return limiteDescubierto; }

    @Override
    public String toString() {
        return super.toString() + String.format(" [Corriente, descubierto hasta $%.2f]",
            limiteDescubierto);
    }
}

// Nivel 3: CuentaPremium extiende CuentaCorriente
public class CuentaPremium extends CuentaCorriente {
    private String asesorPersonal;
    private boolean tarjetaOro;

    public CuentaPremium(String numero, String titular, double saldo,
                          double descubierto, String asesor) {
        super(numero, titular, saldo, descubierto);
        this.asesorPersonal = asesor;
        this.tarjetaOro     = saldo >= 50000;
    }

    public String getAsesor() { return asesorPersonal; }
    public boolean tieneTarjetaOro() { return tarjetaOro; }

    @Override
    public String toString() {
        return super.toString()
            + String.format(" [Premium | Asesor: %s | Oro: %s]",
                asesorPersonal, tarjetaOro ? "Sí" : "No");
    }
}
```

### Diagrama de la jerarquía

```
          CuentaBancaria
         /              \
   CuentaAhorro    CuentaCorriente
                        |
                  CuentaPremium
```

---

## 8. Modificador final en herencia

### Método final — no puede sobreescribirse

```java
public class Forma {
    // Este método define el algoritmo y no puede ser alterado por subclases
    public final void dibujar() {
        System.out.println("Iniciando dibujo...");
        renderizar();  // Este sí puede sobreescribirse
        System.out.println("Dibujo completado.");
    }

    protected void renderizar() {
        System.out.println("Renderizado genérico.");
    }
}

public class Circulo extends Forma {
    @Override
    protected void renderizar() {
        System.out.println("Renderizando círculo.");  // OK: renderizar() no es final
    }

    // @Override
    // public void dibujar() { }  // ERROR: dibujar() es final, no puede sobreescribirse
}
```

### Clase final — no puede extenderse

```java
public final class Constantes {
    public static final double PI   = 3.141592653589793;
    public static final double E    = 2.718281828459045;
    public static final int MAX     = Integer.MAX_VALUE;

    private Constantes() { }  // Constructor privado: no se puede instanciar
}

// IMPOSIBLE:
// public class MisConstantes extends Constantes { }  // ERROR: no puede extender clase final
```

Java usa `final` extensamente: `String`, `Integer`, `Math` son clases `final`.

### Atributo final — no puede reasignarse

```java
public class Empleado {
    private final String dni;      // No puede cambiar después de la construcción
    private String nombre;         // Sí puede cambiar

    public Empleado(String dni, String nombre) {
        this.dni    = dni;         // OK: asignación en el constructor
        this.nombre = nombre;
    }

    // public void setDni(String dni) { this.dni = dni; }  // ERROR: field es final
}
```

---

## 9. Clases abstractas

Una **clase abstracta** es una clase que no puede instanciarse directamente. Existe como base para ser extendida por otras clases. Puede contener métodos abstractos (sin implementación) que las subclases **deben** implementar.

### Declaración

```java
public abstract class NombreClase {
    // Puede tener atributos normales
    // Puede tener constructores
    // Puede tener métodos normales (con implementación)
    // Puede tener métodos abstractos (sin implementación)

    public abstract tipoRetorno metodoAbstracto(params);
}
```

### Cuándo usar clases abstractas

Cuando quieres:
- Definir una plantilla de comportamiento común para un grupo de clases relacionadas.
- Forzar a las subclases a implementar ciertos métodos.
- Proporcionar implementaciones parciales reutilizables.
- Evitar que se creen instancias de la clase base (no tiene sentido crear un `Animal` genérico).

### Ejemplo completo — Sistema de figuras

```java
public abstract class Figura {
    protected String color;

    public Figura(String color) {
        this.color = color;
    }

    // Métodos abstractos: las subclases DEBEN implementarlos
    public abstract double calcularArea();
    public abstract double calcularPerimetro();
    public abstract String getTipo();

    // Método concreto: implementación compartida por todas las figuras
    public void describir() {
        System.out.printf("%-12s | Color: %-8s | Área: %8.2f | Perímetro: %8.2f%n",
            getTipo(), color, calcularArea(), calcularPerimetro());
    }

    // Método concreto que usa métodos abstractos (Template Method Pattern)
    public boolean esMasGrande(Figura otra) {
        return this.calcularArea() > otra.calcularArea();
    }

    public String getColor() { return color; }
}

// Cada subclase DEBE implementar los métodos abstractos
public class Circulo extends Figura {
    private double radio;

    public Circulo(String color, double radio) {
        super(color);
        this.radio = radio;
    }

    @Override public String getTipo()              { return "Círculo"; }
    @Override public double calcularArea()         { return Math.PI * radio * radio; }
    @Override public double calcularPerimetro()    { return 2 * Math.PI * radio; }
}

public class Rectangulo extends Figura {
    private double ancho, alto;

    public Rectangulo(String color, double ancho, double alto) {
        super(color);
        this.ancho = ancho;
        this.alto  = alto;
    }

    @Override public String getTipo()              { return "Rectángulo"; }
    @Override public double calcularArea()         { return ancho * alto; }
    @Override public double calcularPerimetro()    { return 2 * (ancho + alto); }
}

public class TrianguloEquilatero extends Figura {
    private double lado;

    public TrianguloEquilatero(String color, double lado) {
        super(color);
        this.lado = lado;
    }

    @Override public String getTipo()           { return "Triángulo Equil."; }
    @Override public double calcularArea()      { return (Math.sqrt(3) / 4) * lado * lado; }
    @Override public double calcularPerimetro() { return 3 * lado; }
}

// NO puedes hacer: new Figura("rojo")  ← ERROR: clase abstracta no instanciable
// SÍ puedes hacer: Figura f = new Circulo("rojo", 5.0)  ← polimorfismo

// Main:
Figura[] figuras = {
    new Circulo("Rojo", 5.0),
    new Rectangulo("Azul", 4.0, 6.0),
    new TrianguloEquilatero("Verde", 8.0),
    new Circulo("Amarillo", 3.0)
};

System.out.println("TIPO         | COLOR    | ÁREA     | PERÍMETRO");
System.out.println("─".repeat(55));
for (Figura f : figuras) {
    f.describir();
}

// Comparar figuras
System.out.println("\n¿El círculo rojo es más grande que el rectángulo?");
System.out.println(figuras[0].esMasGrande(figuras[1]));  // true (78.54 > 24.0)
```

### Clases abstractas vs. Clases concretas

| Característica | Clase abstracta | Clase concreta |
|----------------|-----------------|----------------|
| Instanciación | No (`new` da error) | Sí |
| Métodos abstractos | Puede tenerlos | No puede tenerlos |
| Implementación parcial | Sí | Completa o ninguna |
| Herencia | Se extiende | Se extiende o instancia |
| Uso | Base de jerarquía | Uso directo |

---

## 10. Herencia y encapsulamiento — protected

El modificador `protected` es el puente entre herencia y encapsulamiento. Permite que las subclases accedan directamente a ciertos atributos y métodos del padre, sin exponerlos al mundo exterior.

```java
public class Animal {
    private   String nombre;    // Solo visible en Animal
    protected int    energia;   // Visible en Animal y todas sus subclases
    public    String especie;   // Visible en cualquier lugar

    public Animal(String nombre, int energia) {
        this.nombre  = nombre;
        this.energia = energia;
    }

    // Método protegido: las subclases pueden llamarlo
    protected void consumirEnergia(int cantidad) {
        energia = Math.max(0, energia - cantidad);
    }

    public String getNombre() { return nombre; }  // Getter público para nombre privado
}

public class Leon extends Animal {
    private double velocidadMaxima;

    public Leon(String nombre) {
        super(nombre, 100);
        this.velocidadMaxima = 80.0;
    }

    public void cazar() {
        if (energia < 20) {
            System.out.println("El León no tiene energía para cazar.");
            return;
        }
        consumirEnergia(30);       // OK: consumirEnergia es protected
        energia += 50;             // OK: energia es protected (acceso directo)
        System.out.printf("%s cazó. Energía: %d%n", getNombre(), energia);
        // this.nombre  ← ERROR: nombre es private en Animal
    }
}
```

### Regla práctica para atributos en herencia

```
Si el atributo debe ser:
  ✓ Solo usado internamente por la clase → private + getter/setter en la clase
  ✓ Accedido directamente por subclases → protected
  ✓ Accedido por todos → public (raro para atributos)
```

---

## 11. Upcasting y Downcasting

Una de las características más poderosas de la herencia es poder tratar un objeto de una subclase como si fuera de la superclase, y viceversa.

### Upcasting — de subclase a superclase (implícito y seguro)

Un objeto de una subclase siempre puede tratarse como un objeto de la superclase:

```java
Perro perro = new Perro("Rex", "Labrador");
Animal animal = perro;  // Upcasting: automático e implícito, siempre seguro

// A través de la referencia 'animal', solo puedes acceder a los métodos de Animal
animal.comer();        // OK: comer() está en Animal
// animal.ladrar();    // ERROR en compilación: ladrar() no existe en Animal
```

```java
// Muy útil para trabajar con colecciones de tipos mixtos
Animal[] animales = new Animal[3];
animales[0] = new Perro("Rex", "Labrador");   // Upcasting implícito
animales[1] = new Gato("Luna", "Siamés");
animales[2] = new Pajaro("Pío", "Canario");

for (Animal a : animales) {
    a.comer();       // Cada uno come a su manera (polimorfismo)
    a.hacerSonido(); // Cada uno hace su sonido propio
}
```

### Downcasting — de superclase a subclase (explícito y arriesgado)

```java
Animal animal = new Perro("Rex", "Labrador");  // Upcasting

// Necesitas acceder a ladrar(), que solo existe en Perro
// Downcasting explícito con (TipoSubclase)
Perro perro = (Perro) animal;  // Downcast explícito
perro.ladrar();  // Ahora sí tienes acceso a métodos de Perro
```

### El riesgo del downcasting — ClassCastException

```java
Animal animal = new Gato("Luna", "Siamés");
Perro perro = (Perro) animal;  // ClassCastException en tiempo de ejecución
// Un Gato no puede tratarse como Perro
```

### Uso seguro con instanceof

Antes de hacer downcasting, verifica el tipo con `instanceof`:

```java
Animal animal = obtenerAnimalAleatorio();

if (animal instanceof Perro) {
    Perro p = (Perro) animal;
    p.ladrar();
} else if (animal instanceof Gato) {
    Gato g = (Gato) animal;
    g.maullar();
} else if (animal instanceof Pajaro) {
    Pajaro pj = (Pajaro) animal;
    pj.volar();
}
```

### Pattern Matching con instanceof (Java 16+)

La forma moderna combina la verificación y el cast en una sola línea:

```java
// Forma clásica (verbose):
if (animal instanceof Perro) {
    Perro p = (Perro) animal;
    p.ladrar();
}

// Pattern Matching (Java 16+): más limpio
if (animal instanceof Perro p) {
    p.ladrar();  // p ya está disponible como Perro directamente
}

// En un switch (Java 21+):
String descripcion = switch (animal) {
    case Perro p  -> p.getNombre() + " ladra: ¡Guau!";
    case Gato g   -> g.getNombre() + " maúlla: ¡Miau!";
    case Pajaro pj -> pj.getNombre() + " canta: ¡Pío!";
    default       -> "Animal desconocido";
};
```

### getClass() — conocer el tipo exacto en tiempo de ejecución

```java
Animal a = new Perro("Rex", "Labrador");

System.out.println(a.getClass().getName());         // nombrePaquete.Perro
System.out.println(a.getClass().getSimpleName());   // Perro
System.out.println(a instanceof Animal);            // true
System.out.println(a instanceof Perro);             // true
System.out.println(a.getClass() == Perro.class);   // true
System.out.println(a.getClass() == Animal.class);  // false (es Perro, no Animal puro)
```

---

## 12. Herencia vs. Composición

La herencia no siempre es la mejor solución. Un principio de diseño importante es:

> **Prefiere composición sobre herencia** cuando la relación no es claramente "es un tipo de".

### Cuándo usar herencia

La herencia es apropiada cuando existe una relación genuina de **"es un tipo de"**:

```
Perro ES UN TIPO DE Animal         → extends correcto
CuentaAhorro ES UN TIPO DE Cuenta  → extends correcto
Administrador ES UN TIPO DE Usuario→ extends correcto
Cuadrado ES UN TIPO DE Rectángulo  → ¡cuidado! (problemas con el LSP)
```

### Cuándo usar composición

La **composición** es cuando una clase **contiene** (tiene) objetos de otras clases:

```
Auto TIENE UN motor            → composición
Persona TIENE UNA dirección    → composición
Computadora TIENE UNA CPU      → composición
Universidad TIENE Estudiantes  → composición
```

```java
// HERENCIA (incorrecta aquí): un Auto NO ES UN Motor
// public class Auto extends Motor { }  ← conceptualmente incorrecto

// COMPOSICIÓN (correcta): un Auto TIENE UN Motor
public class Motor {
    private int cilindros;
    private double cilindrada;
    private String tipo;  // "gasolina", "diesel", "eléctrico"

    public Motor(int cilindros, double cilindrada, String tipo) {
        this.cilindros  = cilindros;
        this.cilindrada = cilindrada;
        this.tipo       = tipo;
    }

    public void arrancar() { System.out.println("Motor encendido."); }
    public void apagar()   { System.out.println("Motor apagado."); }
    public String getTipo(){ return tipo; }

    @Override
    public String toString() {
        return cilindros + " cilindros, " + cilindrada + "L, " + tipo;
    }
}

public class Auto {
    private String marca;
    private String modelo;
    private Motor motor;           // COMPOSICIÓN: Auto tiene un Motor
    private String[] pasajeros;

    public Auto(String marca, String modelo, Motor motor) {
        this.marca    = marca;
        this.modelo   = modelo;
        this.motor    = motor;
    }

    public void encender() {
        motor.arrancar();  // Delega al objeto Motor
        System.out.println(marca + " " + modelo + " listo.");
    }

    public void apagar() {
        motor.apagar();
    }

    @Override
    public String toString() {
        return marca + " " + modelo + " [Motor: " + motor + "]";
    }
}

// Uso:
Motor motorV8 = new Motor(8, 5.0, "gasolina");
Auto auto = new Auto("Ford", "Mustang", motorV8);
auto.encender();
System.out.println(auto);
// Motor encendido.
// Ford Mustang listo.
// Ford Mustang [Motor: 8 cilindros, 5.0L, gasolina]
```

### Comparación herencia vs. composición

| Aspecto | Herencia | Composición |
|---------|----------|-------------|
| Relación | "es un tipo de" | "tiene un" |
| Acoplamiento | Alto (cambios en padre afectan hijos) | Bajo (cambios internos son locales) |
| Flexibilidad | Rígida (decidida en compilación) | Flexible (puede cambiar en runtime) |
| Reutilización | Heredas todo (quieras o no) | Eliges qué delegar |
| Profundidad | Puede crecer mucho | Plana y clara |
| Cuándo usar | Jerarquías naturales de tipo | Todo lo demás |

---

## 13. Errores comunes con Herencia

### Error 1: Olvidar llamar a super() en el constructor

```java
public class Animal {
    protected String nombre;

    public Animal(String nombre) {
        this.nombre = nombre;
    }
}

public class Perro extends Animal {
    private String raza;

    public Perro(String nombre, String raza) {
        // ERROR: Animal no tiene constructor sin parámetros
        // Java intenta super() implícitamente y falla
        this.raza = raza;

        // CORRECTO:
        // super(nombre);
        // this.raza = raza;
    }
}
```

### Error 2: No usar @Override y crear un método nuevo sin querer

```java
public class Animal {
    public void hacerSonido() {
        System.out.println("Sonido genérico");
    }
}

public class Perro extends Animal {
    // ERROR SILENCIOSO: typo en el nombre — no sobreescribe, crea un método nuevo
    public void hacerSonidos() {  // 's' extra
        System.out.println("Guau");
    }
    // Animal.hacerSonido() seguirá imprimiendo "Sonido genérico" para Perro

    // CON @Override el compilador detectaría el error
    @Override
    public void hacerSonido() {  // Correcto
        System.out.println("Guau");
    }
}
```

### Error 3: Downcasting sin verificar instanceof

```java
Animal a = new Gato("Luna");
Perro p = (Perro) a;  // ClassCastException en tiempo de ejecución

// CORRECTO:
if (a instanceof Perro) {
    Perro p2 = (Perro) a;
    p2.ladrar();
}
```

### Error 4: Usar herencia cuando la relación es "tiene un"

```java
// INCORRECTO: una Pila NO ES UN ArrayList
public class Pila extends ArrayList<Integer> {
    public void apilar(int elemento) { add(elemento); }
    public int desapilar() { return remove(size() - 1); }
    // Problema: hereda también add(index, element), set(), remove(index)...
    // que rompen el comportamiento correcto de una pila
}

// CORRECTO: composición
public class Pila {
    private ArrayList<Integer> elementos = new ArrayList<>();
    public void apilar(int e)  { elementos.add(e); }
    public int desapilar()     { return elementos.remove(elementos.size() - 1); }
    public boolean estaVacia() { return elementos.isEmpty(); }
    public int tamaño()        { return elementos.size(); }
}
```

### Error 5: Cadenas de herencia demasiado profundas

```java
// Señal de mal diseño: más de 3-4 niveles suelen ser innecesarios
class A extends B {}
class B extends C {}
class C extends D {}
class D extends E {}
class E extends F {}  // ¡6 niveles! Muy difícil de entender y mantener
```

### Error 6: Llamar a método sobreescribible en el constructor

```java
public class Base {
    public Base() {
        inicializar();  // Peligroso: si la subclase sobreescribe inicializar(),
                        // se llamará la versión de la subclase antes de que
                        // el constructor de la subclase haya corrido
    }

    public void inicializar() {
        System.out.println("Base inicializada");
    }
}

public class Derivada extends Base {
    private String dato = "valor";

    @Override
    public void inicializar() {
        System.out.println("Dato: " + dato);  // dato puede ser null aquí
        // El constructor de Derivada aún no ha corrido cuando se llama esto
    }
}
```

---

## 14. Ejercicios prácticos

### Ejercicio 1 — Jerarquía de figuras geométricas (Nivel: Básico)

Implementa la jerarquía completa:

- `Figura` (abstracta): `color`, `calcularArea()` (abstracto), `calcularPerimetro()` (abstracto), `describir()` (concreto)
- `Circulo extends Figura`: `radio`
- `Rectangulo extends Figura`: `ancho`, `alto`, `esCuadrado()`
- `Triangulo extends Figura`: `ladoA`, `ladoB`, `ladoC`, `getTipo()` (equilátero/isósceles/escaleno)
- `Rombo extends Figura`: `diagonalMayor`, `diagonalMenor`

En `Main`, crea un array de `Figura[]` con al menos 2 de cada tipo, imprime la descripción de todas, y encuentra la de mayor y menor área.

---

### Ejercicio 2 — Sistema de empleados (Nivel: Básico-Medio)

Implementa la jerarquía:

- `Empleado` (abstracta): `nombre`, `id`, `departamento`, `calcularSalario()` (abstracto), `obtenerInfo()` (concreto)
- `EmpleadoPorHora extends Empleado`: `horasTrabajadas`, `tarifaPorHora`
- `EmpleadoAsalariado extends Empleado`: `salarioMensual`
- `EmpleadoConComision extends EmpleadoAsalariado`: `ventas`, `porcentajeComision`
- `Gerente extends EmpleadoAsalariado`: `bonoAnual`, `equipoACargo` (int)

Crea una nómina de 8 empleados de distintos tipos y calcula el total a pagar.

---

### Ejercicio 3 — Jerarquía de vehículos (Nivel: Medio)

- `Vehiculo` (abstracta): `marca`, `modelo`, `año`, `velocidadActual`, `calcularConsumo(double km)` (abstracto)
- `VehiculoElectrico extends Vehiculo`: `bateriaKwh`, `autonomiaKm`, `porcentajeBateria`
- `VehiculoCombustion extends Vehiculo`: `litrosTanque`, `consumoLtKm`
- `Hibrido extends VehiculoCombustion`: `bateriaKwh`, `modoElectrico`
- `Motocicleta extends VehiculoCombustion`: `cilindrada`, `tipoCasco`

Implementa `calcularConsumo(double km)` de forma apropiada para cada tipo. En `Main`, simula un viaje de 200 km con cada vehículo y muestra el costo del combustible (precio: $1.50/litro, $0.20/kWh).

---

### Ejercicio 4 — Sistema de animales con polimorfismo (Nivel: Medio)

- `Animal` (abstracta): `nombre`, `edad`, `peso`, `hacerSonido()` (abstracto), `moverse()` (abstracto), `comer()` (concreto)
- `Mamifero extends Animal`: `tipoPelo`, `amamantaCreias()` (concreto)
- `Ave extends Animal`: `envergaduraAlas`, `puedeVolar` (boolean), `moverse()` sobreescrito
- `Reptil extends Animal`: `esVenenoso`, `moverse()` sobreescrito
- `Perro extends Mamifero`: `raza`, `hacerSonido()`, actividades específicas
- `Aguila extends Ave`: `velocidadVuelo`, `hacerSonido()`
- `Cocodrilo extends Reptil`: `longitudMetros`, `hacerSonido()`

En `Main`, crea un zoológico (array de `Animal[]`), recórrelo llamando a `hacerSonido()` y `moverse()` de cada uno (polimorfismo puro).

---

### Ejercicio 5 — Herencia y encapsulamiento integrados (Nivel: Avanzado)

Diseña el sistema de una universidad:

- `Persona` (abstracta): `nombre`, `dni` (final), `edad`, `email` — con validaciones completas
- `Estudiante extends Persona`: `matricula` (auto-generada), `carrera`, `double[] calificaciones`, métodos: `agregarCalificacion()`, `calcularPromedio()`, `getEstatus()`
- `Profesor extends Persona`: `codigo`, `especialidad`, `salario`, `List<String> materias`, métodos: `asignarMateria()`, `calcularSalarioAnual()`
- `ProfesorInvestigador extends Profesor`: `proyectos` (List\<String\>), `publicaciones` (int), bono de investigación que aumenta el salario
- `Decano extends ProfesorInvestigador`: `facultad`, presupuesto de facultad

Implementa validaciones en todos los setters, `toString()` en cada nivel que use `super.toString()`, y un `Main` que demuestre toda la jerarquía.

---

### Ejercicio 6 — Herencia vs. Composición (Nivel: Avanzado)

**Parte A — Herencia:** Implementa la jerarquía de instrumentos musicales:
- `Instrumento` (abstracta): `nombre`, `material`, `tocar()` (abstracto)
- `InstrumentoViento`, `InstrumentoPercusion`, `InstrumentosCuerda` — cada uno con características propias
- Al menos 2 instrumentos concretos por categoría

**Parte B — Composición:** Diseña un `Orquesta` que:
- Contenga una `List<Instrumento>` (composición)
- Permita `agregarInstrumento()`, `eliminarInstrumento()`
- Tenga `tocarConcierto()` — llama a `tocar()` de cada instrumento
- Retorne estadísticas: cuántos de cada tipo, lista de instrumentos por material

Demuestra que `Orquesta` usa composición (no herencia) para contener instrumentos.

---

## 15. Resumen del tema

### La jerarquía en una imagen

```
            Object
              │
           Figura (abstracta)
          / │ │  \
    Circulo Rect Triang  ...
                │
           Cuadrado   ← extiende Rectangulo
```

### Herencia en una página

```java
// ═══════════════════════════
// SUPERCLASE
// ═══════════════════════════
public abstract class Padre {
    protected TipoA atributo;         // Accesible en subclases

    public Padre(TipoA valor) {       // Constructor
        this.atributo = valor;
    }

    public void metodoConcreto() { }  // Heredado tal cual

    public abstract void metodoAbstracto();  // Subclases DEBEN implementar

    public final void metodoFinal() { }      // Subclases NO pueden sobreescribir
}

// ═══════════════════════════
// SUBCLASE
// ═══════════════════════════
public class Hijo extends Padre {
    private TipoB atributoPropio;

    public Hijo(TipoA a, TipoB b) {
        super(a);                     // PRIMERA línea: llama constructor padre
        this.atributoPropio = b;
    }

    @Override                         // SIEMPRE usar @Override al sobreescribir
    public void metodoAbstracto() {
        // Implementación obligatoria
    }

    @Override
    public void metodoConcreto() {
        super.metodoConcreto();       // Llamar al padre si necesitas su lógica
        // Añadir lógica propia
    }
}

// ═══════════════════════════
// USO
// ═══════════════════════════
Padre obj = new Hijo(valorA, valorB); // Upcasting implícito
obj.metodoConcreto();                 // Versión sobreescrita de Hijo
obj.metodoAbstracto();

if (obj instanceof Hijo h) {          // Pattern matching (Java 16+)
    h.metodoPropio();
}
```

### Reglas de oro de la Herencia

| Situación | Regla |
|-----------|-------|
| Relación | Solo usar herencia si la relación es genuinamente "es un tipo de" |
| `super()` | Siempre primera instrucción del constructor de la subclase |
| `@Override` | Siempre usarla al sobreescribir para que el compilador valide |
| Acceso | Usar `protected` para atributos que las subclases necesitan directamente |
| Downcasting | Siempre verificar con `instanceof` antes de hacer el cast |
| Profundidad | Evitar jerarquías de más de 3-4 niveles |
| Abstracta | Usar cuando la clase base no tiene sentido como objeto solo |
| `final` | Usar en métodos que definen el algoritmo sin permitir variaciones |
| Composición | Preferir sobre herencia cuando la relación es "tiene un" |
| Constructor | Nunca llamar métodos sobreescribibles desde el constructor |

---

## Próximo tema

**Tema 10: Polimorfismo**

Aprenderás cómo un mismo método puede comportarse de maneras distintas según el tipo real del objeto, la diferencia entre polimorfismo estático (sobrecarga) y dinámico (sobreescritura), el despacho dinámico de métodos, y cómo el polimorfismo junto con la herencia permite escribir código flexible que trabaja con familias enteras de clases.

---

*Curso de Java — Nivel 2: Programación Orientada a Objetos*
*Tema 9 de 12*
*Creado con fines educativos*
