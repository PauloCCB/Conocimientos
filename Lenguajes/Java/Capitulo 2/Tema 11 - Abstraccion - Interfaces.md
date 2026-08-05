# Curso de Java — Nivel 2: Programación Orientada a Objetos
## Tema 11: Abstracción e Interfaces

> **Nivel:** Intermedio | **Prerrequisitos:** Temas 1–10 completados | **Duración estimada:** 5–6 horas

---

## Tabla de contenidos

1. [¿Qué es la Abstracción?](#1-qué-es-la-abstracción)
2. [Clases abstractas — repaso profundo](#2-clases-abstractas--repaso-profundo)
3. [¿Qué es una Interfaz?](#3-qué-es-una-interfaz)
4. [Declarar e implementar interfaces](#4-declarar-e-implementar-interfaces)
5. [Implementación múltiple de interfaces](#5-implementación-múltiple-de-interfaces)
6. [Interfaces vs. Clases abstractas](#6-interfaces-vs-clases-abstractas)
7. [Métodos default en interfaces (Java 8+)](#7-métodos-default-en-interfaces-java-8)
8. [Métodos static en interfaces (Java 8+)](#8-métodos-static-en-interfaces-java-8)
9. [Métodos private en interfaces (Java 9+)](#9-métodos-private-en-interfaces-java-9)
10. [Interfaces funcionales y expresiones lambda](#10-interfaces-funcionales-y-expresiones-lambda)
11. [Interfaces de la API estándar de Java](#11-interfaces-de-la-api-estándar-de-java)
12. [Herencia entre interfaces](#12-herencia-entre-interfaces)
13. [Polimorfismo con interfaces](#13-polimorfismo-con-interfaces)
14. [Errores comunes con Interfaces](#14-errores-comunes-con-interfaces)
15. [Ejercicios prácticos](#15-ejercicios-prácticos)
16. [Resumen del tema](#16-resumen-del-tema)

---

## 1. ¿Qué es la Abstracción?

La **abstracción** es el cuarto y último pilar de la POO. Consiste en modelar solo las características relevantes de algo para el problema que estás resolviendo, ocultando los detalles de implementación innecesarios.

Abstraer es enfocarse en el **qué** (el contrato, la interfaz pública) y ocultar el **cómo** (la implementación concreta).

### La analogía del volante

Cuando conduces un auto, interactúas con abstracciones:

```
ABSTRACCIÓN (lo que ves):          IMPLEMENTACIÓN (lo que se oculta):
────────────────────────           ───────────────────────────────────
Volante → girar                    Dirección hidráulica, piñón y cremallera
Pedal → acelerar                   Inyectores, mariposa del acelerador
Pedal → frenar                     Pinzas, discos, ABS
Palanca → cambiar marcha           Embrague, sincronizadores

No necesitas saber cómo funciona   Solo necesitas saber cómo usarlo
el motor para conducir el auto     (la interfaz pública)
```

### Abstracción en Java

Java ofrece dos mecanismos para implementar la abstracción:

1. **Clases abstractas:** abstracción parcial (pueden tener implementación)
2. **Interfaces:** abstracción total del contrato (tradicional), o parcial con métodos `default`

```
NIVEL DE ABSTRACCIÓN:

  Clase concreta          Clase abstracta         Interfaz
  (0% obligatorio)        (parcial)               (100% contrato)
  ────────────────        ─────────────           ────────────────
  Todo implementado       Mezcla de impl.         Solo define QUÉ,
  Instanciable            y métodos abs.          no el CÓMO
                          No instanciable         No instanciable
```

---

## 2. Clases abstractas — repaso profundo

Ya las vimos en el Tema 9. Aquí profundizamos en aspectos avanzados.

### Cuándo una clase debe ser abstracta

Una clase debe ser abstracta cuando:
- Representa un concepto general que no tiene sentido instanciar por sí solo.
- Define un comportamiento común pero la implementación de algunas operaciones depende del subtipo.
- Quieres forzar a las subclases a implementar ciertos métodos.

### El patrón Template Method

Las clases abstractas son la base natural del patrón **Template Method**: defines el esqueleto de un algoritmo en la clase padre y dejas que las subclases implementen los pasos variables.

```java
public abstract class GeneradorReporte {

    // TEMPLATE METHOD: define el algoritmo completo (no se puede sobreescribir)
    public final void generarReporte(String titulo) {
        abrirDocumento();
        escribirEncabezado(titulo);
        escribirCuerpo();         // ← Paso variable: cada subclase lo implementa
        escribirPieDePageina();   // ← Paso variable: cada subclase lo implementa
        cerrarDocumento();
        System.out.println("Reporte '" + titulo + "' generado como " + getFormato());
    }

    // Pasos concretos (comunes a todos)
    private void abrirDocumento() {
        System.out.println("Abriendo documento " + getFormato() + "...");
    }

    private void cerrarDocumento() {
        System.out.println("Cerrando documento...");
    }

    // Pasos abstractos (variables): las subclases DEBEN implementarlos
    protected abstract void escribirEncabezado(String titulo);
    protected abstract void escribirCuerpo();
    protected abstract void escribirPieDePageina();
    protected abstract String getFormato();
}

// Implementación para PDF
public class GeneradorPDF extends GeneradorReporte {
    private String[] datos;

    public GeneradorPDF(String[] datos) { this.datos = datos; }

    @Override
    protected String getFormato() { return "PDF"; }

    @Override
    protected void escribirEncabezado(String titulo) {
        System.out.println("=== " + titulo.toUpperCase() + " ===");
    }

    @Override
    protected void escribirCuerpo() {
        System.out.println("Generando tablas PDF con " + datos.length + " registros...");
        for (String d : datos) System.out.println("  • " + d);
    }

    @Override
    protected void escribirPieDePageina() {
        System.out.println("Página 1 de 1 | Generado: " + java.time.LocalDate.now());
    }
}

// Implementación para CSV
public class GeneradorCSV extends GeneradorReporte {
    private String[] datos;

    public GeneradorCSV(String[] datos) { this.datos = datos; }

    @Override protected String getFormato()              { return "CSV"; }
    @Override protected void escribirEncabezado(String t){ System.out.println("\"" + t + "\""); }
    @Override protected void escribirPieDePageina()      { System.out.println("---FIN---"); }

    @Override
    protected void escribirCuerpo() {
        System.out.println("id,valor");
        for (int i = 0; i < datos.length; i++) {
            System.out.println((i+1) + ",\"" + datos[i] + "\"");
        }
    }
}

// Uso polimórfico:
String[] ventas = {"Laptop $999", "Mouse $29", "Teclado $49"};
GeneradorReporte[] generadores = {
    new GeneradorPDF(ventas),
    new GeneradorCSV(ventas)
};

for (GeneradorReporte g : generadores) {
    g.generarReporte("Reporte de Ventas Q4");
    System.out.println();
}
```

---

## 3. ¿Qué es una Interfaz?

Una **interfaz** es un contrato que una clase se compromete a cumplir. Define un conjunto de métodos que la clase que la implemente **debe** proporcionar, sin decir nada sobre cómo se implementan.

Piénsalo como una promesa: "Yo, `ArrayList`, prometo que implementaré todos los métodos que define la interfaz `List`: `add()`, `get()`, `remove()`, `size()`..."

### La analogía del conector USB

Una interfaz es como el estándar USB:

```
ESTÁNDAR USB (interfaz):
  - transferirDatos()
  - suministrarEnergía()
  - conectar()

Dispositivos que implementan USB (clases):
  Memoria Flash → implementa USB a su manera
  Disco Duro    → implementa USB a su manera
  Teclado       → implementa USB a su manera
  Ratón         → implementa USB a su manera

El computador (cliente) solo conoce el estándar USB,
no le importa qué dispositivo concreto está conectado.
```

### Diferencia conceptual con la clase abstracta

```
CLASE ABSTRACTA:                    INTERFAZ:
  "Soy un Animal incompleto"          "Puedo hacer estas cosas"
  Relación de SER                     Relación de CAPACIDAD
  Un Perro ES UN Animal               Un Perro PUEDE SER Serializable
  Herencia: "es un tipo de"           Implementación: "es capaz de"
```

---

## 4. Declarar e implementar interfaces

### Sintaxis de declaración

```java
public interface NombreInterfaz {

    // Constantes (implícitamente public static final)
    int MAXIMO = 100;
    String VERSION = "1.0";

    // Métodos abstractos (implícitamente public abstract)
    void metodoAbstracto();
    int calcular(int a, int b);
    String obtenerInfo();

    // Desde Java 8: métodos default y static (los veremos después)
}
```

**Todo en una interfaz es implícitamente:**
- Los atributos: `public static final` (constantes)
- Los métodos abstractos: `public abstract`
- No necesitas escribir esas palabras clave, pero puedes hacerlo por claridad

### Implementar una interfaz con implements

```java
public interface Figura2D {
    double calcularArea();
    double calcularPerimetro();
    String getTipo();

    default void describir() {  // Java 8+
        System.out.printf("%s → Área: %.2f | Perímetro: %.2f%n",
            getTipo(), calcularArea(), calcularPerimetro());
    }
}

// La clase DEBE implementar TODOS los métodos abstractos de la interfaz
public class Circulo implements Figura2D {
    private double radio;
    private String color;

    public Circulo(double radio, String color) {
        this.radio = radio;
        this.color = color;
    }

    @Override
    public double calcularArea() {
        return Math.PI * radio * radio;
    }

    @Override
    public double calcularPerimetro() {
        return 2 * Math.PI * radio;
    }

    @Override
    public String getTipo() { return "Círculo"; }

    // También puede tener sus propios métodos
    public double getRadio() { return radio; }
    public String getColor() { return color; }
}

public class Rectangulo implements Figura2D {
    private double ancho, alto;

    public Rectangulo(double ancho, double alto) {
        this.ancho = ancho;
        this.alto  = alto;
    }

    @Override public double calcularArea()       { return ancho * alto; }
    @Override public double calcularPerimetro()  { return 2 * (ancho + alto); }
    @Override public String getTipo()            { return "Rectángulo"; }
}
```

### Usar la interfaz como tipo

```java
// Variable del tipo interfaz: acepta cualquier implementación
Figura2D f1 = new Circulo(5.0, "Rojo");
Figura2D f2 = new Rectangulo(4.0, 6.0);

f1.describir();  // Círculo → Área: 78.54 | Perímetro: 31.42
f2.describir();  // Rectángulo → Área: 24.00 | Perímetro: 20.00

// Array de interfaz
Figura2D[] figuras = {
    new Circulo(3.0, "Azul"),
    new Rectangulo(5.0, 2.0),
    new Circulo(1.5, "Verde")
};

double totalArea = 0;
for (Figura2D f : figuras) {
    totalArea += f.calcularArea();
}
System.out.printf("Área total: %.2f%n", totalArea);
```

---

## 5. Implementación múltiple de interfaces

Una de las ventajas más poderosas de las interfaces es que una clase puede implementar **múltiples** interfaces, simulando herencia múltiple de comportamiento.

```java
// Interfaz 1: capacidad de ser serializable
public interface Serializable {
    String serializar();
    void deserializar(String datos);
}

// Interfaz 2: capacidad de ser comparable
public interface Comparable<T> {
    int compareTo(T otro);
}

// Interfaz 3: capacidad de ser validable
public interface Validable {
    boolean esValido();
    String getMensajeError();
}

// Interfaz 4: capacidad de ser imprimible
public interface Imprimible {
    void imprimir();
    void imprimirDetallado();
}

// Una clase puede implementar TODAS las interfaces
public class Producto implements Serializable, Comparable<Producto>,
                                  Validable, Imprimible {
    private int codigo;
    private String nombre;
    private double precio;
    private int stock;

    public Producto(int codigo, String nombre, double precio, int stock) {
        this.codigo = codigo;
        this.nombre = nombre;
        this.precio = precio;
        this.stock  = stock;
    }

    // Implementación de Serializable
    @Override
    public String serializar() {
        return codigo + "|" + nombre + "|" + precio + "|" + stock;
    }

    @Override
    public void deserializar(String datos) {
        String[] partes = datos.split("\\|");
        this.codigo = Integer.parseInt(partes[0]);
        this.nombre = partes[1];
        this.precio = Double.parseDouble(partes[2]);
        this.stock  = Integer.parseInt(partes[3]);
    }

    // Implementación de Comparable
    @Override
    public int compareTo(Producto otro) {
        return Double.compare(this.precio, otro.precio);
    }

    // Implementación de Validable
    @Override
    public boolean esValido() {
        return nombre != null && !nombre.isEmpty()
            && precio >= 0 && stock >= 0 && codigo > 0;
    }

    @Override
    public String getMensajeError() {
        if (codigo <= 0)  return "Código inválido.";
        if (nombre == null || nombre.isEmpty()) return "Nombre vacío.";
        if (precio < 0)   return "Precio negativo.";
        if (stock < 0)    return "Stock negativo.";
        return "Producto válido.";
    }

    // Implementación de Imprimible
    @Override
    public void imprimir() {
        System.out.printf("[%04d] %-20s $%8.2f%n", codigo, nombre, precio);
    }

    @Override
    public void imprimirDetallado() {
        System.out.println("─".repeat(40));
        System.out.printf("Código  : %d%n", codigo);
        System.out.printf("Nombre  : %s%n", nombre);
        System.out.printf("Precio  : $%.2f%n", precio);
        System.out.printf("Stock   : %d unidades%n", stock);
        System.out.printf("Válido  : %s%n", esValido() ? "✓" : "✗ " + getMensajeError());
        System.out.println("─".repeat(40));
    }

    // Propios de la clase
    public int getCodigo()  { return codigo; }
    public String getNombre(){ return nombre; }
    public double getPrecio(){ return precio; }
    public int getStock()   { return stock; }
}
```

### Combinando las capacidades de forma polimórfica

```java
Producto p1 = new Producto(1, "Laptop",     999.99, 15);
Producto p2 = new Producto(2, "Mouse",       29.99, 50);
Producto p3 = new Producto(3, "Monitor",    299.99,  8);
Producto p4 = new Producto(0, "",           -10.0, -5); // Inválido

// Como Imprimible
Imprimible[] imprimibles = {p1, p2, p3};
for (Imprimible imp : imprimibles) imp.imprimir();

// Como Validable
Validable[] validables = {p1, p2, p3, p4};
for (Validable v : validables) {
    System.out.println(v.esValido() ? "✓ Válido" : "✗ " + v.getMensajeError());
}

// Como Comparable (ordenar)
Producto[] productos = {p1, p2, p3};
java.util.Arrays.sort(productos);  // Usa compareTo
for (Producto p : productos) p.imprimir();
```

---

## 6. Interfaces vs. Clases abstractas

Esta es una de las preguntas más frecuentes en Java. La respuesta depende de lo que necesitas modelar.

### Tabla comparativa completa

| Característica | Clase abstracta | Interfaz |
|----------------|-----------------|----------|
| Herencia | `extends` (solo UNA) | `implements` (MÚLTIPLES) |
| Instanciación | No | No |
| Constructores | Sí | No |
| Atributos de instancia | Sí, cualquier tipo | No (solo `public static final`) |
| Métodos abstractos | Sí | Sí |
| Métodos concretos | Sí | Solo `default` (Java 8+) |
| Métodos static | Sí | Sí (Java 8+) |
| Modificadores de acceso en métodos | Cualquiera | Solo `public` |
| Estado interno | Puede mantener estado | No puede (sin atributos de instancia) |
| Relación que modela | "es un tipo de" | "es capaz de" / "tiene la capacidad de" |

### Cuándo usar clase abstracta

```java
// USA CLASE ABSTRACTA cuando:
// 1. Hay código común que reutilizar entre las subclases
// 2. La relación es claramente "es un tipo de"
// 3. Necesitas atributos de instancia compartidos
// 4. Necesitas constructores para inicializar el estado común

public abstract class Animal {
    protected String nombre;      // Estado compartido
    protected int energia;        // Estado compartido

    public Animal(String nombre, int energia) { // Constructor compartido
        this.nombre  = nombre;
        this.energia = energia;
    }

    // Comportamiento compartido y concreto
    public void comer() {
        energia += 20;
        System.out.println(nombre + " come. Energía: " + energia);
    }

    // Comportamiento que varía entre subclases
    public abstract void hacerSonido();
    public abstract void moverse();
}
```

### Cuándo usar interfaz

```java
// USA INTERFAZ cuando:
// 1. Defines un contrato de comportamiento sin implementación
// 2. Una clase necesita "adquirir" múltiples capacidades
// 3. La relación es "puede hacer X" en lugar de "es un tipo de X"
// 4. Quieres que clases de jerarquías distintas compartan un contrato

public interface Serializable {
    String serializar();
    void deserializar(String datos);
}

// Clases sin relación entre sí pueden implementar la misma interfaz
public class Empleado    implements Serializable { ... }
public class Producto    implements Serializable { ... }
public class Temperatura implements Serializable { ... }
```

### La combinación más poderosa: ambas juntas

```java
// La interfaz define el contrato
public interface Vehiculo {
    void encender();
    void apagar();
    void acelerar(double velocidad);
    double getVelocidadActual();
}

// La clase abstracta implementa el contrato parcialmente
// y añade estado e implementación común
public abstract class VehiculoBase implements Vehiculo {
    protected String marca;
    protected String modelo;
    protected double velocidadActual;
    protected boolean encendido;

    public VehiculoBase(String marca, String modelo) {
        this.marca  = marca;
        this.modelo = modelo;
    }

    @Override
    public void encender() {
        encendido = true;
        System.out.println(marca + " " + modelo + " encendido.");
    }

    @Override
    public void apagar() {
        encendido = false;
        velocidadActual = 0;
        System.out.println(marca + " " + modelo + " apagado.");
    }

    @Override
    public double getVelocidadActual() { return velocidadActual; }

    // Deja acelerar() como abstracto para que cada tipo lo implemente
    @Override
    public abstract void acelerar(double velocidad);
}

// Las clases concretas solo implementan lo que varía
public class AutoElectrico extends VehiculoBase {
    private double bateria;

    public AutoElectrico(String marca, String modelo, double bateria) {
        super(marca, modelo);
        this.bateria = bateria;
    }

    @Override
    public void acelerar(double velocidad) {
        if (!encendido || bateria <= 0) {
            System.out.println("Sin energía para acelerar.");
            return;
        }
        velocidadActual = Math.min(velocidad, 200);
        bateria -= velocidad * 0.1;
        System.out.printf("%s acelera a %.1f km/h (Batería: %.1f%%)%n",
            marca, velocidadActual, bateria);
    }
}
```

---

## 7. Métodos default en interfaces (Java 8+)

Antes de Java 8, las interfaces solo podían tener métodos abstractos. Esto creaba un problema: si querías añadir un nuevo método a una interfaz, todas las clases que la implementaban se rompían.

Java 8 introdujo los **métodos `default`**: métodos con implementación dentro de la interfaz. Las clases que implementan la interfaz pueden usarlos tal cual o sobreescribirlos.

### Declaración y uso básico

```java
public interface Coleccion<T> {
    void agregar(T elemento);
    void eliminar(T elemento);
    boolean contiene(T elemento);
    int tamaño();

    // Métodos default: tienen implementación
    default boolean estaVacia() {
        return tamaño() == 0;
    }

    default void agregarTodos(T[] elementos) {
        for (T e : elementos) agregar(e);
    }

    default void imprimirInfo() {
        System.out.println("Colección con " + tamaño() + " elementos.");
        System.out.println("¿Vacía? " + (estaVacia() ? "Sí" : "No"));
    }
}
```

### Sobreescribir métodos default

```java
public class ListaOrdenada<T extends Comparable<T>> implements Coleccion<T> {
    private java.util.ArrayList<T> datos = new java.util.ArrayList<>();

    @Override public void agregar(T e)           { datos.add(e); java.util.Collections.sort(datos); }
    @Override public void eliminar(T e)          { datos.remove(e); }
    @Override public boolean contiene(T e)       { return datos.contains(e); }
    @Override public int tamaño()               { return datos.size(); }

    // Sobreescribe el método default para una versión más específica
    @Override
    public void imprimirInfo() {
        System.out.println("Lista ordenada: " + datos);
        System.out.println("Tamaño: " + tamaño());
    }
}
```

### El problema del diamante con default

Cuando una clase implementa dos interfaces con el mismo método `default`, debe resolver el conflicto sobreescribiendo el método:

```java
public interface InterfazA {
    default void saludar() { System.out.println("Hola desde A"); }
}

public interface InterfazB {
    default void saludar() { System.out.println("Hola desde B"); }
}

// ERROR si no resuelves el conflicto:
public class MiClase implements InterfazA, InterfazB {
    // OBLIGATORIO sobreescribir para resolver el conflicto
    @Override
    public void saludar() {
        InterfazA.super.saludar();  // Puedes llamar a una de las dos versiones
        // InterfazB.super.saludar();  // o a la otra
        System.out.println("Hola desde MiClase");
    }
}
```

### Evolución de APIs con default — sin romper código existente

```java
// Versión 1 de la interfaz (2020)
public interface Guardable {
    void guardar();
    void cargar();
}

// Versión 2 (2024): añadir funcionalidad sin romper implementaciones existentes
public interface Guardable {
    void guardar();
    void cargar();

    // Nuevo: las implementaciones existentes NO necesitan actualizar su código
    default void guardarYRespaldar() {
        guardar();
        System.out.println("Respaldo creado automáticamente.");
    }

    default boolean existeRespaldo() {
        return false;  // Implementación por defecto conservadora
    }
}

// Clases que implementaban la versión 1 siguen funcionando sin cambios
public class BaseDeDatos implements Guardable {
    @Override public void guardar() { System.out.println("BD guardada."); }
    @Override public void cargar()  { System.out.println("BD cargada."); }
    // guardarYRespaldar() y existeRespaldo() se heredan de la interfaz
}
```

---

## 8. Métodos static en interfaces (Java 8+)

Las interfaces también pueden tener métodos `static`. A diferencia de los `default`, los métodos `static` de interfaz no se heredan: debes llamarlos con el nombre de la interfaz.

```java
public interface Conversor {
    double convertir(double valor);

    // Métodos de utilidad estáticos
    static double celsiusAFahrenheit(double celsius) {
        return celsius * 9.0 / 5.0 + 32;
    }

    static double fahrenheitACelsius(double fahrenheit) {
        return (fahrenheit - 32) * 5.0 / 9.0;
    }

    static double kmAMillas(double km) {
        return km * 0.621371;
    }

    static double millasAKm(double millas) {
        return millas / 0.621371;
    }

    // Fábrica estática: crea instancias de tipos específicos
    static Conversor crearConversionCelsius() {
        return celsius -> celsius * 9.0 / 5.0 + 32;  // Lambda (lo veremos pronto)
    }
}

// Uso:
System.out.println(Conversor.celsiusAFahrenheit(100));  // 212.0
System.out.println(Conversor.kmAMillas(100));            // 62.14

// NO se puede llamar así (los static de interfaz no se heredan):
// class MiConversor implements Conversor { }
// MiConversor.celsiusAFahrenheit(100);  // Error en compilación
```

---

## 9. Métodos private en interfaces (Java 9+)

Java 9 añadió métodos `private` en interfaces para evitar la duplicación de código entre métodos `default` y `static`.

```java
public interface Validador<T> {

    boolean validar(T valor);

    default boolean validarYLoguear(T valor) {
        boolean resultado = validar(valor);
        loguear(valor, resultado);  // Llama al método privado
        return resultado;
    }

    default boolean validarMultiples(T[] valores) {
        boolean todosValidos = true;
        for (T v : valores) {
            if (!validar(v)) {
                loguear(v, false);
                todosValidos = false;
            }
        }
        return todosValidos;
    }

    // Método privado: evita duplicar la lógica de logueo
    private void loguear(T valor, boolean resultado) {
        System.out.printf("[Validación] %s → %s%n",
            valor, resultado ? "✓ VÁLIDO" : "✗ INVÁLIDO");
    }

    // Método private static: utilidad interna compartida
    static private String formatearMensaje(String tipo, String detalle) {
        return "[" + tipo + "] " + detalle;
    }
}

// Implementación:
public class ValidadorEmail implements Validador<String> {
    @Override
    public boolean validar(String email) {
        return email != null && email.matches("[a-zA-Z0-9+_.-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}");
    }
}

ValidadorEmail v = new ValidadorEmail();
v.validarYLoguear("user@email.com");     // ✓ VÁLIDO
v.validarYLoguear("correo-invalido");   // ✗ INVÁLIDO
```

---

## 10. Interfaces funcionales y expresiones lambda

Una **interfaz funcional** es una interfaz que tiene exactamente **un solo método abstracto**. Son la base de las expresiones lambda y la programación funcional en Java.

### La anotación @FunctionalInterface

```java
@FunctionalInterface
public interface Operacion {
    double calcular(double a, double b);
    // Solo UN método abstracto (puede tener default y static)
    // Si añades otro método abstracto, el compilador da error
}
```

### Expresiones lambda — sintaxis

Una **lambda** es una forma compacta de implementar una interfaz funcional sin crear una clase:

```java
// Forma tradicional: clase anónima
Operacion suma = new Operacion() {
    @Override
    public double calcular(double a, double b) {
        return a + b;
    }
};

// Con lambda: mucho más conciso
Operacion suma     = (a, b) -> a + b;
Operacion resta    = (a, b) -> a - b;
Operacion multi    = (a, b) -> a * b;
Operacion division = (a, b) -> b != 0 ? a / b : 0;
Operacion potencia = (a, b) -> Math.pow(a, b);

// Uso:
System.out.println(suma.calcular(10, 3));      // 13.0
System.out.println(resta.calcular(10, 3));     // 7.0
System.out.println(potencia.calcular(2, 8));   // 256.0
```

### Sintaxis completa de las lambdas

```java
// Sin parámetros:
Runnable r = () -> System.out.println("Hola desde lambda");

// Un parámetro (paréntesis opcionales):
Consumer<String> imprimir = s -> System.out.println(s);
Consumer<String> imprimir2 = (String s) -> System.out.println(s);  // Tipo explícito

// Múltiples parámetros:
Comparator<Integer> comp = (a, b) -> a - b;

// Cuerpo de múltiples líneas (requiere llaves y return):
Operacion compleja = (a, b) -> {
    double suma = a + b;
    double producto = a * b;
    return suma + producto;
};

// Referencia a método (shorthand para lambdas que llaman un método):
Consumer<String> print = System.out::println;  // Equivalente a s -> System.out.println(s)
Function<String, Integer> longitud = String::length;  // Equivalente a s -> s.length()
```

### Interfaces funcionales como parámetros

```java
// El método acepta cualquier operación como parámetro
public static double aplicar(double a, double b, Operacion op) {
    return op.calcular(a, b);
}

// Llamada con lambda directamente:
System.out.println(aplicar(10, 3, (a, b) -> a + b));    // 13.0
System.out.println(aplicar(10, 3, (a, b) -> a * b));    // 30.0
System.out.println(aplicar(10, 3, Math::pow));           // 1000.0 (referencia a método)

// Estrategia con lambda:
public static void ordenarLista(List<String> lista, Comparator<String> criterio) {
    lista.sort(criterio);
}

List<String> nombres = new ArrayList<>(List.of("Carlos", "Ana", "Beatriz", "David"));
ordenarLista(nombres, (a, b) -> a.compareTo(b));           // Alfabético
ordenarLista(nombres, (a, b) -> a.length() - b.length()); // Por longitud
ordenarLista(nombres, String::compareTo);                   // Referencia a método
```

---

## 11. Interfaces de la API estándar de Java

Java provee decenas de interfaces en su API estándar. Conocer las más importantes es esencial para programar en Java de forma profesional.

### Comparable\<T\> — comparación natural

```java
public interface Comparable<T> {
    int compareTo(T otro);
    // Devuelve: negativo si this < otro, 0 si iguales, positivo si this > otro
}

public class Producto implements Comparable<Producto> {
    private String nombre;
    private double precio;

    public Producto(String nombre, double precio) {
        this.nombre = nombre;
        this.precio = precio;
    }

    @Override
    public int compareTo(Producto otro) {
        return Double.compare(this.precio, otro.precio);  // Por precio
    }

    @Override public String toString() {
        return nombre + "($" + precio + ")";
    }
}

Producto[] productos = {
    new Producto("Laptop", 999.99),
    new Producto("Mouse", 29.99),
    new Producto("Monitor", 299.99)
};

Arrays.sort(productos);  // Usa compareTo
System.out.println(Arrays.toString(productos));
// [Mouse($29.99), Monitor($299.99), Laptop($999.99)]
```

### Comparator\<T\> — comparación externa

```java
import java.util.Comparator;

// Comparador externo: no necesita modificar la clase
Comparator<Producto> porNombre = (p1, p2) -> p1.getNombre().compareTo(p2.getNombre());
Comparator<Producto> porPrecioDesc = (p1, p2) -> Double.compare(p2.getPrecio(), p1.getPrecio());
Comparator<Producto> porNombreLuegoPrecio = porNombre.thenComparing(porPrecioDesc);

List<Producto> lista = new ArrayList<>(List.of(
    new Producto("Laptop", 999.99),
    new Producto("Mouse", 29.99),
    new Producto("Monitor", 299.99),
    new Producto("Teclado", 49.99)
));

lista.sort(porNombre);          // Alfabético
lista.sort(porPrecioDesc);      // Por precio descendente
lista.sort(porNombreLuegoPrecio); // Nombre, luego precio
```

### Iterable\<T\> e Iterator\<T\> — recorrido

```java
public interface Iterable<T> {
    Iterator<T> iterator();
    // Habilita el uso del for-each en objetos propios
}

// Implementar Iterable permite usar for-each en tu clase
public class Rango implements Iterable<Integer> {
    private int inicio;
    private int fin;

    public Rango(int inicio, int fin) {
        this.inicio = inicio;
        this.fin    = fin;
    }

    @Override
    public java.util.Iterator<Integer> iterator() {
        return new java.util.Iterator<Integer>() {
            int actual = inicio;

            @Override public boolean hasNext() { return actual <= fin; }
            @Override public Integer next()    { return actual++; }
        };
    }
}

// Ahora puedes usar for-each:
for (int n : new Rango(1, 5)) {
    System.out.print(n + " ");  // 1 2 3 4 5
}
```

### Runnable y Callable — ejecución de tareas

```java
// Runnable: tarea sin retorno
Runnable tarea1 = () -> System.out.println("Tarea ejecutada.");
tarea1.run();

// Callable: tarea con retorno (para hilos, Tema 17)
java.util.concurrent.Callable<Integer> tarea2 = () -> {
    int resultado = 42;
    return resultado;
};
```

### AutoCloseable — recursos que deben cerrarse

```java
public interface AutoCloseable {
    void close() throws Exception;
}

// Implementar AutoCloseable habilita try-with-resources
public class ConexionBD implements AutoCloseable {
    private String url;

    public ConexionBD(String url) {
        this.url = url;
        System.out.println("Conexión abierta: " + url);
    }

    public void ejecutarQuery(String sql) {
        System.out.println("Ejecutando: " + sql);
    }

    @Override
    public void close() {
        System.out.println("Conexión cerrada: " + url);
    }
}

// Try-with-resources: close() se llama automáticamente
try (ConexionBD conexion = new ConexionBD("jdbc:mysql://localhost/db")) {
    conexion.ejecutarQuery("SELECT * FROM usuarios");
} // close() se llama aquí automáticamente, incluso si hay excepción
```

### Las interfaces funcionales de java.util.function

```java
import java.util.function.*;

// Function<T, R>: recibe T, devuelve R
Function<String, Integer> longitud = String::length;
Function<Integer, Integer> cuadrado = n -> n * n;
Function<String, String> mayusculas = String::toUpperCase;

System.out.println(longitud.apply("Hola"));    // 4
System.out.println(cuadrado.apply(5));         // 25

// Encadenar funciones
Function<String, Integer> longitudMayusculas = mayusculas.andThen(longitud);
System.out.println(longitudMayusculas.apply("hola"));  // 4

// Predicate<T>: recibe T, devuelve boolean
Predicate<String> esVacio   = String::isEmpty;
Predicate<String> esLargo   = s -> s.length() > 5;
Predicate<String> esValidoEmail = s -> s.contains("@");

System.out.println(esVacio.test(""));           // true
System.out.println(esLargo.test("Hola"));      // false
System.out.println(esLargo.and(esValidoEmail).test("usuario@email.com"));  // true

// Consumer<T>: recibe T, no devuelve nada
Consumer<String> imprimir  = System.out::println;
Consumer<String> guardar   = s -> System.out.println("Guardando: " + s);
Consumer<String> ambas     = imprimir.andThen(guardar);

ambas.accept("Java es genial");

// Supplier<T>: no recibe nada, devuelve T
Supplier<String> saludo      = () -> "¡Hola Mundo!";
Supplier<Double> aleatorio   = Math::random;

System.out.println(saludo.get());        // ¡Hola Mundo!
System.out.println(aleatorio.get());    // número aleatorio

// BiFunction<T, U, R>: recibe T y U, devuelve R
BiFunction<String, Integer, String> repetir = (s, n) -> s.repeat(n);
System.out.println(repetir.apply("Java", 3));  // JavaJavaJava
```

---

## 12. Herencia entre interfaces

Las interfaces pueden extender (heredar de) otras interfaces, incluso de múltiples interfaces simultáneamente.

```java
// Interfaces base
public interface Legible {
    String leer();
    boolean puedeLeerse();
}

public interface Escribible {
    void escribir(String contenido);
    boolean puedeEscribirse();
}

public interface Buscable {
    List<String> buscar(String criterio);
    boolean contiene(String texto);
}

// Interfaz que hereda de múltiples interfaces
public interface Almacenamiento extends Legible, Escribible, Buscable {
    // Añade sus propios métodos abstractos
    void eliminar(String clave);
    int tamaño();
    void limpiar();

    // Y puede añadir métodos default
    default boolean estaVacio() { return tamaño() == 0; }
}

// Una clase que implementa Almacenamiento debe implementar
// TODOS los métodos de: Legible + Escribible + Buscable + Almacenamiento
public class AlmacenamientoMemoria implements Almacenamiento {
    private java.util.HashMap<String, String> datos = new java.util.HashMap<>();

    @Override public String leer()              { return datos.toString(); }
    @Override public boolean puedeLeerse()      { return true; }
    @Override public void escribir(String c)    { datos.put("key_" + datos.size(), c); }
    @Override public boolean puedeEscribirse()  { return true; }
    @Override public List<String> buscar(String criterio) {
        List<String> result = new ArrayList<>();
        for (String v : datos.values()) if (v.contains(criterio)) result.add(v);
        return result;
    }
    @Override public boolean contiene(String t) { return datos.containsValue(t); }
    @Override public void eliminar(String clave){ datos.remove(clave); }
    @Override public int tamaño()              { return datos.size(); }
    @Override public void limpiar()            { datos.clear(); }
}
```

### Jerarquía de interfaces en la API de Java

Un ejemplo real de herencia de interfaces en la API de Java:

```
Iterable<E>
    └── Collection<E>
            ├── List<E>
            │     ├── ArrayList<E>  (clase concreta)
            │     └── LinkedList<E> (clase concreta)
            ├── Set<E>
            │     ├── HashSet<E>    (clase concreta)
            │     └── TreeSet<E>    (clase concreta)
            └── Queue<E>
                  └── LinkedList<E> (implementa List Y Queue)
```

---

## 13. Polimorfismo con interfaces

Las interfaces habilitan el polimorfismo más poderoso porque clases de jerarquías completamente distintas pueden compartir el mismo contrato.

### El patrón Observer con interfaces

```java
// La interfaz define el contrato del observador
public interface Observador {
    void actualizar(String evento, Object datos);
}

// La clase Observable gestiona los observadores
public class EventoSistema {
    private List<Observador> observadores = new ArrayList<>();
    private String nombre;

    public EventoSistema(String nombre) { this.nombre = nombre; }

    public void suscribir(Observador obs) { observadores.add(obs); }
    public void desuscribir(Observador obs) { observadores.remove(obs); }

    public void notificarTodos(String evento, Object datos) {
        System.out.println("\n[EVENTO: " + evento + "]");
        for (Observador obs : observadores) {
            obs.actualizar(evento, datos);  // Polimorfismo con interfaz
        }
    }
}

// Distintas clases implementan Observador
public class LoggerArchivo implements Observador {
    @Override
    public void actualizar(String evento, Object datos) {
        System.out.println("[LOG] " + java.time.LocalTime.now() + " " + evento + ": " + datos);
    }
}

public class AlertaEmail implements Observador {
    private String destinatario;
    public AlertaEmail(String dest) { this.destinatario = dest; }

    @Override
    public void actualizar(String evento, Object datos) {
        System.out.println("[EMAIL → " + destinatario + "] Alerta: " + evento);
    }
}

public class PanelMonitoreo implements Observador {
    @Override
    public void actualizar(String evento, Object datos) {
        System.out.println("[PANEL] Actualizando UI: " + evento + " = " + datos);
    }
}

// Uso completamente polimórfico:
EventoSistema sistema = new EventoSistema("Servidor");
sistema.suscribir(new LoggerArchivo());
sistema.suscribir(new AlertaEmail("admin@empresa.com"));
sistema.suscribir(new PanelMonitoreo());

// Con lambda (la interfaz funcional Observador acepta lambda)
sistema.suscribir((evento, datos) ->
    System.out.println("[SMS] Alerta SMS: " + evento));

sistema.notificarTodos("SERVIDOR_CAIDO", "Error 503");
sistema.notificarTodos("NUEVO_USUARIO", "usuario123");
```

---

## 14. Errores comunes con Interfaces

### Error 1: No implementar todos los métodos abstractos

```java
public interface Animal {
    void hacerSonido();
    void moverse();
    String getTipo();
}

// ERROR: Perro no implementa moverse() ni getTipo()
public class Perro implements Animal {
    @Override
    public void hacerSonido() { System.out.println("Guau"); }
    // Falta: moverse() y getTipo()
    // Error de compilación: Perro is not abstract and does not override
    //   abstract method moverse() in Animal
}

// CORRECTO: implementar todos
public class Perro implements Animal {
    @Override public void hacerSonido() { System.out.println("Guau"); }
    @Override public void moverse()     { System.out.println("Corre."); }
    @Override public String getTipo()   { return "Perro"; }
}
```

### Error 2: Intentar instanciar una interfaz directamente

```java
Figura2D f = new Figura2D();  // ERROR: Figura2D es una interfaz, no puede instanciarse

// CORRECTO: instanciar una clase que la implementa
Figura2D f = new Circulo(5.0, "Rojo");
// O con clase anónima:
Figura2D anonima = new Figura2D() {
    @Override public double calcularArea()      { return 0; }
    @Override public double calcularPerimetro() { return 0; }
    @Override public String getTipo()           { return "Anónima"; }
};
```

### Error 3: Intentar añadir atributos de instancia a una interfaz

```java
public interface Configurable {
    // ERROR: las interfaces no pueden tener atributos de instancia
    // private int timeout = 30;  // Error de compilación

    // CORRECTO: solo constantes (public static final)
    int TIMEOUT_DEFAULT = 30;  // Implícitamente public static final
}
```

### Error 4: Confundir extends e implements

```java
// Una clase IMPLEMENTA una interfaz con 'implements'
public class Perro implements Animal { }        // CORRECTO
// public class Perro extends Animal { }         // Solo si Animal es clase

// Una interfaz EXTIENDE otra interfaz con 'extends'
public interface Mascota extends Animal { }     // CORRECTO
// public interface Mascota implements Animal { } // ERROR: interfaces usan extends
```

### Error 5: No resolver el conflicto de métodos default duplicados

```java
interface A { default void hola() { System.out.println("A"); } }
interface B { default void hola() { System.out.println("B"); } }

// ERROR: ambigüedad no resuelta
public class C implements A, B {
    // Debe sobreescribir hola() para resolver el conflicto
}

// CORRECTO:
public class C implements A, B {
    @Override
    public void hola() {
        A.super.hola();  // Elige explícitamente la versión de A
    }
}
```

### Error 6: Usar interfaz cuando la relación es "es un tipo de"

```java
// MAL: Perro y Gato "son animales", no "implementan Animal"
// La herencia de comportamiento complejo se modela mejor con clases abstractas
public interface Animal {
    default void respirar() { System.out.println("Respira."); }
    default void dormir()   { System.out.println("Duerme."); }
    // Si casi todo es default con implementación, tal vez debería ser clase abstracta
}

// BIEN: usa clase abstracta para comportamiento compartido con estado
public abstract class Animal {
    protected String nombre;
    protected int energia;
    // atributos de estado + implementación común
}
// Y usa interfaz para capacidades adicionales:
public interface Domesticable {
    void entrenar(String comando);
    boolean obedeceComando(String comando);
}
```

---

## 15. Ejercicios prácticos

### Ejercicio 1 — Sistema de formas 2D y 3D (Nivel: Básico)

Define las interfaces:
- `Forma2D`: `calcularArea()`, `calcularPerimetro()`, `getTipo()`
- `Forma3D`: `calcularVolumen()`, `calcularAreaSuperficie()`, `getTipo()`
- `Dibujable`: `dibujar()` (default que imprime el tipo y dimensiones)

Implementa:
- `Circulo`, `Cuadrado`, `Triangulo` → implementan `Forma2D` y `Dibujable`
- `Esfera`, `Cubo`, `Cilindro` → implementan `Forma3D` y `Dibujable`

En Main: crea arrays polimórficos de `Forma2D[]` y `Forma3D[]`, muestra todas las formas usando `Dibujable`.

---

### Ejercicio 2 — Interfaces funcionales propias (Nivel: Básico-Medio)

Crea tus propias interfaces funcionales:
- `Transformador<T, R>`: transforma T en R
- `Filtro<T>`: decide si T cumple una condición (boolean)
- `Combinador<T>`: combina dos T en uno
- `Accion<T>`: realiza una acción sobre T sin retorno

Implementa un `Procesador<T>` genérico con:
- `List<T> transformarLista(List<T> lista, Transformador<T, T> t)`
- `List<T> filtrarLista(List<T> lista, Filtro<T> f)`
- `T reducir(List<T> lista, T identidad, Combinador<T> c)`
- `void ejecutarEnTodos(List<T> lista, Accion<T> a)`

Úsalo con lambdas para procesar una lista de enteros y otra de Strings.

---

### Ejercicio 3 — Plugin de sistema de reportes (Nivel: Medio)

Diseña un sistema extensible de reportes con interfaces:

```java
interface FuenteDatos { List<String[]> obtenerDatos(); String getCabecera(); }
interface FormateadorReporte { String formatear(List<String[]> datos, String cabecera); }
interface ExportadorReporte { void exportar(String contenido, String nombre); }
```

Implementa:
- `FuenteDatos`: `DatosCSV`, `DatosJSON` (simulados con datos hardcoded)
- `FormateadorReporte`: `FormateadorTabla`, `FormateadorHTML`, `FormateadorMarkdown`
- `ExportadorReporte`: `ExportadorConsola`, `ExportadorArchivo` (simula con println)

Crea `GeneradorReporte` que combina los tres: `generar(FuenteDatos, FormateadorReporte, ExportadorReporte)`.

Demuestra 4 combinaciones diferentes en Main.

---

### Ejercicio 4 — Sistema de eventos con Observer (Nivel: Medio)

Implementa el patrón Observer completo:

```java
interface Suscriptor<T> {
    void onEvento(T datos);
    String getNombre();
}

interface PublicadorEventos<T> {
    void suscribir(Suscriptor<T> s);
    void desuscribir(Suscriptor<T> s);
    void publicar(T datos);
}
```

Crea un sistema de cotizaciones de bolsa:
- `MercadoBolsa implements PublicadorEventos<Double>`: gestiona precio de una acción
- `InversorBot implements Suscriptor<Double>`: compra si precio baja, vende si sube
- `AlertaPrecio implements Suscriptor<Double>`: alerta si el precio supera umbrales
- `HistorialPrecios implements Suscriptor<Double>`: guarda todos los precios

Simula 10 cambios de precio y muestra las reacciones de cada suscriptor.

---

### Ejercicio 5 — Framework de validación (Nivel: Avanzado)

Diseña un framework de validación extensible:

```java
@FunctionalInterface
interface Regla<T> {
    ResultadoValidacion validar(T valor);
}

class ResultadoValidacion {
    boolean valido;
    String mensaje;
    // constructor, getters, toString
}
```

Crea `Validador<T>` con:
- `List<Regla<T>> reglas`
- `Validador<T> agregarRegla(Regla<T> regla)` — chainable
- `ResultadoValidacion validarPrimeroFallido(T valor)` — para cuando falla la primera
- `List<ResultadoValidacion> validarTodas(T valor)` — para recopilar todos los errores
- `boolean esValido(T valor)` — simplificado

Implementa validadores para:
- `String`: no vacío, longitud mínima/máxima, formato email, sin caracteres especiales
- `Integer`: rango min-max, par/impar, primo
- `Double`: positivo, rango, máximo de decimales

Úsalos con lambdas para crear validadores complejos reutilizables.

---

### Ejercicio 6 — Sistema de plugins (Nivel: Avanzado)

Diseña una arquitectura de plugins usando interfaces:

```java
interface Plugin {
    String getNombre();
    String getVersion();
    String getDescripcion();
    void inicializar(ConfigPlugin config);
    void ejecutar(ContextoEjecucion ctx);
    void finalizar();
}

interface ConfigPlugin {
    String obtener(String clave);
    void establecer(String clave, String valor);
    boolean contiene(String clave);
}

interface ContextoEjecucion {
    void registrarLog(String mensaje);
    void registrarError(String error);
    Object obtenerRecurso(String nombre);
    void publicarResultado(String clave, Object valor);
}
```

Implementa:
- `PluginCalculadora`: suma, resta, multiplica los números en el contexto
- `PluginTransformadorTexto`: convierte texto a mayúsculas, invierte, cuenta palabras
- `PluginEstadisticas`: calcula media, mediana, moda de una lista de números
- `GestorPlugins`: registra, busca y ejecuta plugins, gestiona el ciclo de vida

Demuestra el sistema registrando los 3 plugins y ejecutándolos con distintas configuraciones.

---

## 16. Resumen del tema

### Abstracción e Interfaces en una página

```java
// ═══════════════════════════════════════
// INTERFAZ: contrato puro
// ═══════════════════════════════════════
public interface MiInterfaz {
    // Constante (public static final implícito)
    int CONSTANTE = 42;

    // Método abstracto (public abstract implícito)
    void metodoAbstracto();
    int calcular(int a, int b);

    // Método default (Java 8+): implementación por defecto
    default void metodoDefault() {
        System.out.println("Implementación por defecto");
    }

    // Método static (Java 8+): utilidad de la interfaz
    static MiInterfaz crear() {
        return (a, b) -> a + b;  // Lambda si es @FunctionalInterface
    }

    // Método private (Java 9+): ayudante interno
    private void ayudante() { }
}

// ═══════════════════════════════════════
// IMPLEMENTACIÓN
// ═══════════════════════════════════════
public class MiClase implements InterfazA, InterfazB, InterfazC {
    // Implementar TODOS los métodos abstractos de TODAS las interfaces
    @Override public void metodoDeA() { }
    @Override public void metodoDeB() { }
    @Override public void metodoDeC() { }
    // Los métodos default se heredan automáticamente
}

// ═══════════════════════════════════════
// INTERFAZ FUNCIONAL + LAMBDA
// ═══════════════════════════════════════
@FunctionalInterface
interface Operacion { double calcular(double a, double b); }

Operacion suma  = (a, b) -> a + b;
Operacion multi = (a, b) -> a * b;
double resultado = suma.calcular(5, 3);  // 8.0
```

### Cuándo usar qué

| Necesito... | Solución |
|-------------|----------|
| Comportamiento compartido con estado | Clase abstracta |
| Forzar implementación sin estado compartido | Interfaz |
| Múltiples "capacidades" en una clase | Múltiples interfaces |
| Implementación por defecto evolucionable | Método `default` |
| Utilidad estática relacionada con la interfaz | Método `static` |
| Función de una sola operación con lambda | Interfaz funcional |
| Mezcla de ambos | Interfaz + clase abstracta que la implementa parcialmente |

### Reglas de oro

| Situación | Regla |
|-----------|-------|
| `implements` | Para clases que usan interfaces |
| `extends` | Para interfaces que heredan de interfaces |
| Todos los abstractos | Implementar todos o declarar la clase como `abstract` |
| `@Override` | Siempre al implementar métodos de interfaz |
| Conflicto de `default` | Resolver sobreescribiendo y usando `InterfazX.super.metodo()` |
| Atributos | Las interfaces solo tienen constantes (`public static final`) |
| Lambdas | Solo con interfaces funcionales (`@FunctionalInterface`) |
| Diseño | Prefiere interfaces pequeñas y específicas (Principio de Segregación de Interfaces) |

---

## Próximo tema

**Tema 12: Manejo de Excepciones**

Aprenderás qué son las excepciones en Java, la jerarquía de excepciones (`Throwable`, `Error`, `Exception`, `RuntimeException`), cómo capturarlas con `try-catch-finally`, lanzarlas con `throw` y `throws`, crear excepciones personalizadas, y las buenas prácticas para manejar errores de forma robusta en aplicaciones reales.

---

*Curso de Java — Nivel 2: Programación Orientada a Objetos*
*Tema 11 de 12*
*Creado con fines educativos*
