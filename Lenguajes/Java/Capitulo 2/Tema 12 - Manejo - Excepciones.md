# Curso de Java — Nivel 2: Programación Orientada a Objetos
## Tema 12: Manejo de Excepciones

> **Nivel:** Intermedio | **Prerrequisitos:** Temas 1–11 completados | **Duración estimada:** 5–6 horas

---

## Tabla de contenidos

1. [¿Qué es una Excepción?](#1-qué-es-una-excepción)
2. [Jerarquía de excepciones en Java](#2-jerarquía-de-excepciones-en-java)
3. [Excepciones verificadas vs. no verificadas](#3-excepciones-verificadas-vs-no-verificadas)
4. [Capturar excepciones — try-catch](#4-capturar-excepciones--try-catch)
5. [El bloque finally](#5-el-bloque-finally)
6. [try-with-resources (Java 7+)](#6-try-with-resources-java-7)
7. [Lanzar excepciones — throw](#7-lanzar-excepciones--throw)
8. [Declarar excepciones — throws](#8-declarar-excepciones--throws)
9. [Excepciones personalizadas](#9-excepciones-personalizadas)
10. [Multi-catch y re-lanzamiento](#10-multi-catch-y-re-lanzamiento)
11. [Encadenamiento de excepciones](#11-encadenamiento-de-excepciones)
12. [Excepciones más comunes de Java](#12-excepciones-más-comunes-de-java)
13. [Buenas prácticas en el manejo de excepciones](#13-buenas-prácticas-en-el-manejo-de-excepciones)
14. [Errores comunes con excepciones](#14-errores-comunes-con-excepciones)
15. [Ejercicios prácticos](#15-ejercicios-prácticos)
16. [Resumen del tema y del Nivel 2](#16-resumen-del-tema-y-del-nivel-2)

---

## 1. ¿Qué es una Excepción?

Una **excepción** es un evento anormal que ocurre durante la ejecución de un programa y que interrumpe el flujo normal de instrucciones. No es un error de programación (bug) en sí mismo; es una situación inesperada que el programa debe ser capaz de manejar.

### Ejemplos de situaciones excepcionales

```
División por cero         → ArithmeticException
Acceso a índice inválido  → ArrayIndexOutOfBoundsException
Referencia nula           → NullPointerException
Archivo no encontrado     → FileNotFoundException
Formato de número inválido→ NumberFormatException
Sin memoria disponible    → OutOfMemoryError
Red no disponible         → IOException
```

### El mecanismo de excepciones

Sin excepciones, los programas usaban códigos de error y condicionales para manejar situaciones anómalas:

```java
// ANTES (sin excepciones): código de error mezclado con lógica de negocio
int resultado = dividir(10, 0);
if (resultado == -1) {
    System.out.println("Error: división por cero");
    return;
}
if (resultado == -2) {
    System.out.println("Error: parámetros inválidos");
    return;
}
// La lógica real se mezcla con el manejo de errores → código difícil de leer
```

```java
// CON excepciones: el flujo normal y el manejo de errores están separados
try {
    int resultado = dividir(10, 0);
    System.out.println("Resultado: " + resultado);  // Lógica normal
} catch (ArithmeticException e) {
    System.out.println("Error: " + e.getMessage()); // Manejo de errores
}
// La separación hace el código mucho más limpio y legible
```

### ¿Qué ocurre cuando se lanza una excepción?

```
1. El programa encuentra una situación anormal
2. Se crea un objeto de excepción con información del error
3. El flujo de ejecución se interrumpe en ese punto
4. Java busca hacia arriba en la pila de llamadas un bloque catch apropiado
5. Si lo encuentra: ejecuta el catch y continúa después del bloque try-catch
6. Si NO lo encuentra: el programa termina y se imprime el stack trace
```

```
Pila de llamadas (stack trace):
  main()
    └── metodoA()
          └── metodoB()
                └── metodoC() ← AQUÍ ocurre la excepción

Java busca catch en: metodoC() → metodoB() → metodoA() → main() → termina
```

---

## 2. Jerarquía de excepciones en Java

Todas las excepciones en Java son objetos que heredan de la clase `Throwable`. Entender la jerarquía es fundamental para saber qué capturar y qué lanzar.

```
                    Throwable
                   /          \
               Error         Exception
              /    \         /        \
         OutOf    Stack   IOException  RuntimeException
         Memory   Over    FileNotF     /    |    \
         Error    flow    Found     NPE  AIOOB  ArithE
                  Error   ...       ...  ...    ...
```

### Throwable — la raíz

```java
public class Throwable {
    String message;      // Mensaje descriptivo del error
    Throwable cause;     // La excepción que causó esta (encadenamiento)
    StackTraceElement[]  // Pila de llamadas en el momento del error

    String getMessage()
    String getLocalizedMessage()
    Throwable getCause()
    void printStackTrace()
    StackTraceElement[] getStackTrace()
    String toString()
}
```

### Error — problemas graves de la JVM

Los `Error` representan condiciones tan graves que el programa normalmente no puede recuperarse de ellas. **Nunca deberías capturar un Error** (salvo casos muy especiales).

```java
// Ejemplos de Error (no capturar en código normal):
OutOfMemoryError     // La JVM se quedó sin memoria heap
StackOverflowError   // La pila de llamadas se agotó (recursión infinita)
VirtualMachineError  // Problema interno de la JVM
AssertionError       // Una asertion falló
NoClassDefFoundError // Una clase requerida no se encontró en tiempo de ejecución
```

### Exception — situaciones recuperables

Las `Exception` son situaciones inesperadas de las que el programa puede recuperarse. Se dividen en dos grandes grupos que veremos en la siguiente sección.

---

## 3. Excepciones verificadas vs. no verificadas

### Excepciones verificadas (Checked Exceptions)

Son subclases de `Exception` que **NO** son `RuntimeException`. El compilador **obliga** a manejarlas: debes o capturarlas con `try-catch` o declararlas con `throws` en la firma del método.

Representan situaciones externas al programa que el programador debe anticipar:

```java
// Ejemplos de checked exceptions:
IOException              // Error de entrada/salida
FileNotFoundException    // Archivo no encontrado (subclase de IOException)
SQLException             // Error de base de datos
ClassNotFoundException   // Clase no encontrada en tiempo de ejecución
ParseException           // Error al parsear texto

// El compilador OBLIGA a manejarlas:
public void leerArchivo(String ruta) {
    FileReader fr = new FileReader(ruta);  // ERROR de compilación:
    // Unhandled exception type FileNotFoundException
}

// CORRECTO — opción 1: capturar
public void leerArchivo(String ruta) {
    try {
        FileReader fr = new FileReader(ruta);
    } catch (FileNotFoundException e) {
        System.out.println("Archivo no encontrado: " + e.getMessage());
    }
}

// CORRECTO — opción 2: declarar con throws
public void leerArchivo(String ruta) throws FileNotFoundException {
    FileReader fr = new FileReader(ruta);
    // Quien llame a este método deberá manejarla
}
```

### Excepciones no verificadas (Unchecked Exceptions)

Son subclases de `RuntimeException`. El compilador **no obliga** a manejarlas. Suelen representar errores de programación que deberían corregirse en el código, no manejarse en tiempo de ejecución.

```java
// Ejemplos de unchecked exceptions (RuntimeException):
NullPointerException          // Acceso a referencia null
ArrayIndexOutOfBoundsException// Índice de array fuera de rango
ClassCastException            // Cast inválido
ArithmeticException           // División por cero
NumberFormatException         // String no convertible a número
IllegalArgumentException      // Argumento inválido
IllegalStateException         // Estado inválido del objeto
UnsupportedOperationException // Operación no soportada
ConcurrentModificationException // Modificación concurrente inválida

// El compilador NO obliga a manejarlas (aunque puedes hacerlo):
int[] arr = {1, 2, 3};
arr[10] = 5;  // Compila bien, pero lanza ArrayIndexOutOfBoundsException en ejecución
```

### ¿Cuándo usar cada tipo?

```
CHECKED (verificada):                  UNCHECKED (no verificada):
  El cliente puede hacer algo          El cliente no puede hacer nada
  razonable para recuperarse           razonable (es un bug)
  
  Ejemplos:                            Ejemplos:
  - Archivo no existe → pedirlo        - Índice fuera de rango → bug del programador
  - Red no disponible → reintentar     - Null pointer → bug del programador
  - BD no disponible → usar caché      - Cast inválido → bug del programador
```

---

## 4. Capturar excepciones — try-catch

### Estructura básica

```java
try {
    // Código que puede lanzar una excepción
    // Si ocurre una excepción aquí, el resto del bloque try se omite
} catch (TipoExcepcion nombreVariable) {
    // Código que se ejecuta si ocurre TipoExcepcion
    // nombreVariable contiene información del error
} catch (OtroTipoExcepcion e) {
    // Manejador para otro tipo de excepción
}
// El código continúa aquí después del try-catch
```

### Ejemplo básico

```java
public static int dividir(int a, int b) {
    return a / b;
}

public static void main(String[] args) {
    try {
        int resultado = dividir(10, 2);
        System.out.println("10 / 2 = " + resultado);  // 5

        int error = dividir(10, 0);                    // Lanza ArithmeticException
        System.out.println("Esta línea no se ejecuta"); // Nunca llega aquí
    } catch (ArithmeticException e) {
        System.out.println("Error aritmético: " + e.getMessage()); // / by zero
    }
    System.out.println("El programa continúa normalmente.");
}
```

### Métodos del objeto de excepción

```java
try {
    String s = null;
    s.length();  // NullPointerException
} catch (NullPointerException e) {
    System.out.println(e.getMessage());          // null (o descripción)
    System.out.println(e.getClass().getName());  // java.lang.NullPointerException
    System.out.println(e.toString());            // java.lang.NullPointerException
    e.printStackTrace();                          // Imprime el stack trace completo
}
```

### Múltiples bloques catch

Puedes tener varios catch para distintos tipos de excepciones. Java los evalúa de arriba a abajo y ejecuta el **primer** que coincida:

```java
public static void procesarEntrada(String texto) {
    try {
        int numero = Integer.parseInt(texto);  // Puede lanzar NumberFormatException
        int[] arr  = new int[numero];          // Puede lanzar NegativeArraySizeException
        int resultado = 100 / numero;          // Puede lanzar ArithmeticException
        arr[numero - 1] = resultado;
        System.out.println("Resultado: " + resultado);

    } catch (NumberFormatException e) {
        System.out.println("Error: '" + texto + "' no es un número válido.");
    } catch (NegativeArraySizeException e) {
        System.out.println("Error: El número no puede ser negativo.");
    } catch (ArithmeticException e) {
        System.out.println("Error: División por cero.");
    } catch (ArrayIndexOutOfBoundsException e) {
        System.out.println("Error: Índice fuera del rango.");
    }
}

procesarEntrada("abc");  // NumberFormatException
procesarEntrada("-5");   // NegativeArraySizeException
procesarEntrada("0");    // ArithmeticException
procesarEntrada("5");    // OK: Resultado: 20
```

### Capturar la superclase

Puedes capturar una excepción padre para atrapar todas sus subclases:

```java
try {
    // código
} catch (Exception e) {
    // Captura CUALQUIER Exception (verificada o no verificada)
    System.out.println("Ocurrió un error: " + e.getMessage());
}
```

**Regla importante:** Los catch más específicos deben ir **antes** de los más generales:

```java
// INCORRECTO: Exception es superclase de todas → el segundo catch nunca alcanzaría
try { ... }
catch (Exception e)             { ... }  // Atrapa todo
catch (NumberFormatException e) { ... }  // ERROR: nunca se ejecuta (superclase primero)

// CORRECTO: del más específico al más general
try { ... }
catch (NumberFormatException e) { ... }  // Más específico primero
catch (ArithmeticException e)   { ... }
catch (RuntimeException e)      { ... }  // Más general al final
catch (Exception e)             { ... }  // El más general de todos, al final
```

---

## 5. El bloque finally

El bloque `finally` contiene código que **siempre se ejecuta**, sin importar si ocurrió una excepción o no, y sin importar si fue capturada o no. Es ideal para liberar recursos.

### Sintaxis

```java
try {
    // Código que puede lanzar excepciones
} catch (TipoExcepcion e) {
    // Manejo de la excepción
} finally {
    // SIEMPRE se ejecuta: con o sin excepción, con o sin return
}
```

### Cuándo se ejecuta finally

```java
public static void demostrarFinally(int caso) {
    try {
        System.out.println("Dentro del try: caso " + caso);
        if (caso == 1) throw new RuntimeException("Excepción de prueba");
        if (caso == 2) return;  // Incluso con return, el finally se ejecuta
        System.out.println("Fin del try (solo si caso != 1 y != 2)");
    } catch (RuntimeException e) {
        System.out.println("Catch: " + e.getMessage());
    } finally {
        System.out.println("Finally: SIEMPRE se ejecuta");
    }
    System.out.println("Después del try-catch-finally");
}

demostrarFinally(0);
// Dentro del try: caso 0
// Fin del try (solo si caso != 1 y != 2)
// Finally: SIEMPRE se ejecuta
// Después del try-catch-finally

demostrarFinally(1);
// Dentro del try: caso 1
// Catch: Excepción de prueba
// Finally: SIEMPRE se ejecuta
// Después del try-catch-finally

demostrarFinally(2);
// Dentro del try: caso 2
// Finally: SIEMPRE se ejecuta
// (NO llega a "Después" porque el return ocurrió)
```

### Uso clásico: liberar recursos manualmente

```java
java.io.BufferedReader lector = null;

try {
    lector = new java.io.BufferedReader(new java.io.FileReader("datos.txt"));
    String linea;
    while ((linea = lector.readLine()) != null) {
        System.out.println(linea);
    }
} catch (java.io.IOException e) {
    System.out.println("Error al leer el archivo: " + e.getMessage());
} finally {
    // Garantiza que el archivo se cierra siempre
    if (lector != null) {
        try {
            lector.close();
        } catch (java.io.IOException e) {
            System.out.println("Error al cerrar: " + e.getMessage());
        }
    }
}
```

Este patrón es tan común que Java 7 introdujo el `try-with-resources` para simplificarlo.

### try sin catch (solo con finally)

Es válido tener `try` con `finally` pero sin `catch`. En este caso las excepciones no se capturan pero el `finally` siempre se ejecuta:

```java
public static void metodo() throws IOException {
    BufferedReader br = new BufferedReader(new FileReader("file.txt"));
    try {
        return br.readLine();
    } finally {
        br.close();  // Siempre se cierra, incluso si readLine() lanza excepción
    }
}
```

---

## 6. try-with-resources (Java 7+)

El `try-with-resources` es la forma moderna y elegante de manejar recursos que deben cerrarse. Cualquier objeto que implemente `AutoCloseable` (o `Closeable`) puede usarse.

### Sintaxis

```java
try (TipoRecurso recurso = new TipoRecurso()) {
    // Usa el recurso
} catch (Exception e) {
    // Manejo de excepción
}
// recurso.close() se llama automáticamente al salir del bloque try
// incluso si hay una excepción
```

### Comparación: antes y después

```java
// ANTES de Java 7: verboso y propenso a errores
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("archivo.txt"));
    String linea = br.readLine();
    System.out.println(linea);
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (br != null) {
        try { br.close(); }
        catch (IOException e) { e.printStackTrace(); }
    }
}

// CON try-with-resources: limpio y seguro
try (BufferedReader br = new BufferedReader(new FileReader("archivo.txt"))) {
    String linea = br.readLine();
    System.out.println(linea);
} catch (IOException e) {
    e.printStackTrace();
}
// br.close() se llama automáticamente
```

### Múltiples recursos

```java
// Varios recursos: se cierran en orden INVERSO al de apertura
try (FileInputStream  entrada = new FileInputStream("origen.txt");
     FileOutputStream salida  = new FileOutputStream("destino.txt")) {

    byte[] buffer = new byte[1024];
    int bytesLeidos;
    while ((bytesLeidos = entrada.read(buffer)) != -1) {
        salida.write(buffer, 0, bytesLeidos);
    }
    System.out.println("Archivo copiado exitosamente.");

} catch (IOException e) {
    System.out.println("Error de E/S: " + e.getMessage());
}
// salida.close() se llama primero, luego entrada.close()
```

### Crear tus propios recursos con AutoCloseable

```java
public class ConexionBD implements AutoCloseable {
    private String url;
    private boolean conectado;

    public ConexionBD(String url) {
        this.url = url;
        this.conectado = true;
        System.out.println("Conectado a: " + url);
    }

    public java.util.List<String> ejecutarQuery(String sql) {
        if (!conectado) throw new IllegalStateException("Conexión cerrada.");
        System.out.println("Ejecutando: " + sql);
        return java.util.List.of("Resultado1", "Resultado2", "Resultado3");
    }

    @Override
    public void close() {
        conectado = false;
        System.out.println("Conexión a " + url + " cerrada.");
    }
}

// Uso automático con try-with-resources
try (ConexionBD db = new ConexionBD("jdbc:mysql://localhost/midb")) {
    var resultados = db.ejecutarQuery("SELECT * FROM usuarios");
    resultados.forEach(System.out::println);
} catch (Exception e) {
    System.out.println("Error: " + e.getMessage());
}
// La conexión se cierra automáticamente aquí
```

---

## 7. Lanzar excepciones — throw

La instrucción `throw` lanza una excepción manualmente desde tu código. Se usa para señalar que algo salió mal cuando lo detectas en tu lógica de negocio.

### Sintaxis

```java
throw new TipoExcepcion("Mensaje descriptivo del error");
```

### throw en validaciones

```java
public class CuentaBancaria {
    private String titular;
    private double saldo;

    public CuentaBancaria(String titular, double saldoInicial) {
        if (titular == null || titular.trim().isEmpty()) {
            throw new IllegalArgumentException("El titular no puede ser nulo o vacío.");
        }
        if (saldoInicial < 0) {
            throw new IllegalArgumentException(
                "El saldo inicial no puede ser negativo: " + saldoInicial);
        }
        this.titular = titular.trim();
        this.saldo   = saldoInicial;
    }

    public void depositar(double monto) {
        if (monto <= 0) {
            throw new IllegalArgumentException(
                "El monto a depositar debe ser mayor que cero: " + monto);
        }
        saldo += monto;
    }

    public void retirar(double monto) {
        if (monto <= 0) {
            throw new IllegalArgumentException("El monto debe ser positivo: " + monto);
        }
        if (monto > saldo) {
            throw new IllegalStateException(
                String.format("Saldo insuficiente. Disponible: $%.2f | Solicitado: $%.2f",
                    saldo, monto));
        }
        saldo -= monto;
    }

    public void transferir(CuentaBancaria destino, double monto) {
        if (destino == null) {
            throw new IllegalArgumentException("La cuenta destino no puede ser nula.");
        }
        if (destino == this) {
            throw new IllegalArgumentException("No se puede transferir a la misma cuenta.");
        }
        retirar(monto);
        destino.depositar(monto);
    }

    public double getSaldo()  { return saldo; }
    public String getTitular(){ return titular; }
}

// Uso:
try {
    CuentaBancaria c = new CuentaBancaria("Ana", 1000.0);
    c.retirar(1500.0);  // Lanza IllegalStateException
} catch (IllegalStateException e) {
    System.out.println("Error: " + e.getMessage());
}
```

### throw para salir de métodos con lógica compleja

```java
public static int buscarPrimero(int[] arr, int valor) {
    if (arr == null) {
        throw new IllegalArgumentException("El array no puede ser null.");
    }
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == valor) return i;
    }
    throw new java.util.NoSuchElementException(
        "El valor " + valor + " no existe en el array.");
}
```

---

## 8. Declarar excepciones — throws

La cláusula `throws` en la firma de un método indica que ese método puede lanzar ciertas excepciones checked que no captura internamente. El que llame al método debe manejarlas.

### Sintaxis

```java
public tipoRetorno nombreMetodo(params) throws ExcepcionA, ExcepcionB {
    // El método puede lanzar ExcepcionA o ExcepcionB sin capturarlas
}
```

### throws con excepciones verificadas

```java
import java.io.*;

public class LectorArchivo {

    // Declara que puede lanzar IOException (checked)
    public String leerPrimeraLinea(String ruta) throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader(ruta))) {
            return br.readLine();
        }
        // No captura IOException → la propaga con throws
    }

    // Puede declarar múltiples excepciones
    public void copiarArchivo(String origen, String destino)
            throws FileNotFoundException, IOException {
        try (InputStream  in  = new FileInputStream(origen);
             OutputStream out = new FileOutputStream(destino)) {
            in.transferTo(out);
        }
    }
}

// Quien llama DEBE manejar la excepción checked:
LectorArchivo lector = new LectorArchivo();
try {
    String linea = lector.leerPrimeraLinea("datos.txt");
    System.out.println(linea);
} catch (IOException e) {
    System.out.println("Error de E/S: " + e.getMessage());
}
```

### La cadena de propagación

```java
// La excepción puede propagarse por múltiples niveles
public void nivelC() throws IOException {
    throw new IOException("Error en nivel C");
}

public void nivelB() throws IOException {
    nivelC();  // Propaga la IOException
}

public void nivelA() throws IOException {
    nivelB();  // Propaga la IOException
}

public static void main(String[] args) {
    MiClase obj = new MiClase();
    try {
        obj.nivelA();  // Aquí se captura finalmente
    } catch (IOException e) {
        System.out.println("Capturada en main: " + e.getMessage());
    }
}
```

### throws para unchecked exceptions (documentación)

Puedes usar `throws` para documentar excepciones unchecked aunque no es obligatorio:

```java
/**
 * Divide a entre b.
 * @throws ArithmeticException si b es cero (documentación, no obligatorio)
 */
public static int dividir(int a, int b) throws ArithmeticException {
    return a / b;
}
```

---

## 9. Excepciones personalizadas

Puedes crear tus propias clases de excepción extendiendo `Exception` (checked) o `RuntimeException` (unchecked).

### Cuándo crear excepciones personalizadas

- Cuando ninguna excepción estándar describe bien el problema.
- Cuando quieres agrupar varios tipos de errores bajo un mismo tipo.
- Cuando necesitas añadir información adicional al error (campos extra).
- Cuando defines una API de biblioteca y quieres excepciones propias del dominio.

### Excepción checked personalizada

```java
public class SaldoInsuficienteException extends Exception {

    private double saldoDisponible;
    private double montoSolicitado;

    public SaldoInsuficienteException(double saldoDisponible, double montoSolicitado) {
        super(String.format(
            "Saldo insuficiente. Disponible: $%.2f | Solicitado: $%.2f",
            saldoDisponible, montoSolicitado));
        this.saldoDisponible  = saldoDisponible;
        this.montoSolicitado  = montoSolicitado;
    }

    public SaldoInsuficienteException(String mensaje) {
        super(mensaje);
    }

    public SaldoInsuficienteException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }

    // Información adicional específica del dominio
    public double getSaldoDisponible()  { return saldoDisponible; }
    public double getMontoSolicitado()  { return montoSolicitado; }
    public double getDiferencia()       { return montoSolicitado - saldoDisponible; }
}
```

### Excepción unchecked personalizada

```java
public class ProductoNoEncontradoException extends RuntimeException {

    private int codigoProducto;

    public ProductoNoEncontradoException(int codigo) {
        super("Producto no encontrado con código: " + codigo);
        this.codigoProducto = codigo;
    }

    public ProductoNoEncontradoException(String mensaje) {
        super(mensaje);
    }

    public ProductoNoEncontradoException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }

    public int getCodigoProducto() { return codigoProducto; }
}
```

### Jerarquía de excepciones personalizadas

Para sistemas complejos, puedes crear una jerarquía propia:

```java
// Excepción base de la aplicación
public class AplicacionException extends Exception {
    private String codigoError;

    public AplicacionException(String codigoError, String mensaje) {
        super(mensaje);
        this.codigoError = codigoError;
    }

    public AplicacionException(String codigoError, String mensaje, Throwable causa) {
        super(mensaje, causa);
        this.codigoError = codigoError;
    }

    public String getCodigoError() { return codigoError; }
}

// Excepciones específicas del dominio
public class UsuarioException extends AplicacionException {
    public UsuarioException(String codigo, String msg)          { super(codigo, msg); }
    public UsuarioException(String codigo, String msg, Throwable c) { super(codigo, msg, c); }
}

public class UsuarioNoEncontradoException extends UsuarioException {
    private String username;

    public UsuarioNoEncontradoException(String username) {
        super("USR-001", "Usuario no encontrado: " + username);
        this.username = username;
    }

    public String getUsername() { return username; }
}

public class UsuarioBloqueadoException extends UsuarioException {
    public UsuarioBloqueadoException(String username) {
        super("USR-002", "Usuario bloqueado: " + username);
    }
}

public class ProductoException extends AplicacionException {
    public ProductoException(String codigo, String msg)         { super(codigo, msg); }
}

public class StockInsuficienteException extends ProductoException {
    private int stockDisponible;
    private int cantidadSolicitada;

    public StockInsuficienteException(String producto, int disponible, int solicitado) {
        super("PRD-001",
            String.format("Stock insuficiente para '%s'. Disponible: %d | Solicitado: %d",
                producto, disponible, solicitado));
        this.stockDisponible  = disponible;
        this.cantidadSolicitada = solicitado;
    }

    public int getStockDisponible()  { return stockDisponible; }
    public int getCantidadSolicitada(){ return cantidadSolicitada; }
}
```

### Uso de las excepciones personalizadas

```java
public class ServicioUsuario {

    private java.util.Map<String, String> usuarios = new java.util.HashMap<>();
    private java.util.Set<String> bloqueados = new java.util.HashSet<>();

    public String autenticar(String username, String password)
            throws UsuarioNoEncontradoException, UsuarioBloqueadoException {

        if (!usuarios.containsKey(username)) {
            throw new UsuarioNoEncontradoException(username);
        }
        if (bloqueados.contains(username)) {
            throw new UsuarioBloqueadoException(username);
        }
        if (!usuarios.get(username).equals(password)) {
            throw new IllegalArgumentException("Contraseña incorrecta.");
        }
        return "Token-" + username + "-" + System.currentTimeMillis();
    }
}

// Uso con manejo preciso:
ServicioUsuario servicio = new ServicioUsuario();
try {
    String token = servicio.autenticar("admin", "1234");
    System.out.println("Token: " + token);

} catch (UsuarioNoEncontradoException e) {
    System.out.println("Error [" + e.getCodigoError() + "]: " + e.getMessage());
    System.out.println("Usuario buscado: " + e.getUsername());

} catch (UsuarioBloqueadoException e) {
    System.out.println("Error [" + e.getCodigoError() + "]: " + e.getMessage());
    System.out.println("Contacte al administrador.");

} catch (AplicacionException e) {
    System.out.println("Error general: " + e.getMessage());
}
```

---

## 10. Multi-catch y re-lanzamiento

### Multi-catch (Java 7+) — capturar varios tipos en un solo catch

```java
// ANTES de Java 7: bloques catch duplicados
try {
    // ...
} catch (NumberFormatException e) {
    logger.error("Error: " + e.getMessage());
    notificar(e);
} catch (ArithmeticException e) {
    logger.error("Error: " + e.getMessage());  // Código duplicado
    notificar(e);
}

// CON multi-catch (Java 7+): más limpio
try {
    String texto = obtenerTexto();
    int numero   = Integer.parseInt(texto);     // NumberFormatException
    int resultado = 100 / numero;               // ArithmeticException
    System.out.println("Resultado: " + resultado);

} catch (NumberFormatException | ArithmeticException e) {
    // Maneja ambas excepciones con el mismo código
    System.out.println("Error de cálculo: " + e.getMessage());
    e.printStackTrace();
}
```

**Restricción:** En multi-catch, la variable `e` es implícitamente `final` — no puede reasignarse.

### Re-lanzamiento de excepciones

Puedes capturar una excepción y volver a lanzarla (con o sin modificación):

```java
// Re-lanzar la misma excepción después de loguear
public void procesarDatos(String datos) throws IOException {
    try {
        // ... procesamiento
    } catch (IOException e) {
        logger.error("Error procesando datos: " + e.getMessage());
        throw e;  // Re-lanza la misma excepción
    }
}

// Re-lanzar como un tipo diferente (más descriptivo)
public void cargarConfiguracion(String ruta) throws ConfiguracionException {
    try {
        // ...
    } catch (IOException e) {
        throw new ConfiguracionException(
            "No se pudo cargar la configuración desde: " + ruta, e);
        // El 'e' es la causa (encadenamiento de excepciones)
    }
}
```

### Re-lanzamiento con inferencia de tipo (Java 7+)

```java
// Java puede inferir el tipo exacto re-lanzado cuando usas 'final' o efectivamente final
public <T extends Exception> void ejecutar(Tarea<T> tarea) throws T {
    try {
        tarea.ejecutar();
    } catch (Exception e) {
        throw e;  // Java 7+ puede inferir que se re-lanza T específicamente
    }
}
```

---

## 11. Encadenamiento de excepciones

El **encadenamiento de excepciones** (exception chaining) permite preservar la excepción original cuando la envuelves en otra más descriptiva. Esto mantiene el historial completo de lo que ocurrió.

### Por qué encadenar excepciones

```java
// SIN encadenamiento: pierdes información valiosa para debugging
public void cargarDatos() throws AplicacionException {
    try {
        leerArchivo("datos.csv");
    } catch (IOException e) {
        throw new AplicacionException("DAT-001", "No se pudieron cargar los datos.");
        // ¿Por qué falló exactamente? Perdemos el detalle del IOException
    }
}

// CON encadenamiento: preservas la causa original
public void cargarDatos() throws AplicacionException {
    try {
        leerArchivo("datos.csv");
    } catch (IOException e) {
        throw new AplicacionException("DAT-001", "No se pudieron cargar los datos.", e);
        // e es la causa → el stack trace mostrará AMBAS excepciones
    }
}
```

### Acceder a la cadena de excepciones

```java
try {
    servicio.cargarDatos();
} catch (AplicacionException e) {
    System.out.println("Error principal: " + e.getMessage());

    Throwable causa = e.getCause();
    if (causa != null) {
        System.out.println("Causa raíz: " + causa.getMessage());
        System.out.println("Tipo: " + causa.getClass().getSimpleName());
    }

    // Para ver toda la cadena:
    e.printStackTrace();
    // java.lang.AplicacionException: No se pudieron cargar los datos.
    //   Caused by: java.io.FileNotFoundException: datos.csv (No such file or directory)
}
```

### Obtener la causa raíz

```java
public static Throwable getCausaRaiz(Throwable e) {
    Throwable causa = e;
    while (causa.getCause() != null) {
        causa = causa.getCause();
    }
    return causa;
}

try {
    // ...
} catch (Exception e) {
    Throwable raiz = getCausaRaiz(e);
    System.out.println("Causa raíz: " + raiz.getClass().getSimpleName()
        + ": " + raiz.getMessage());
}
```

---

## 12. Excepciones más comunes de Java

### RuntimeExceptions frecuentes

```java
// NullPointerException — acceso a referencia null
String s = null;
s.length();  // NPE

// Prevención:
if (s != null) { s.length(); }
// O con Optional (Java 8+):
java.util.Optional.ofNullable(s).map(String::length).orElse(0);

// ─────────────────────────────────────────────────────

// ArrayIndexOutOfBoundsException — índice inválido
int[] arr = {1, 2, 3};
arr[5] = 10;  // AIOOBE

// Prevención:
if (indice >= 0 && indice < arr.length) { arr[indice] = 10; }

// ─────────────────────────────────────────────────────

// ClassCastException — cast inválido
Object obj = "Hola";
Integer n = (Integer) obj;  // CCE

// Prevención:
if (obj instanceof Integer) { Integer n2 = (Integer) obj; }

// ─────────────────────────────────────────────────────

// NumberFormatException — String no parseable
int n = Integer.parseInt("abc");  // NFE

// Prevención:
public static boolean esNumero(String s) {
    try { Integer.parseInt(s); return true; }
    catch (NumberFormatException e) { return false; }
}

// ─────────────────────────────────────────────────────

// ArithmeticException — operación aritmética inválida
int resultado = 10 / 0;  // AE: / by zero

// Prevención:
if (divisor != 0) { resultado = dividendo / divisor; }

// ─────────────────────────────────────────────────────

// StackOverflowError — recursión infinita
public int factorial(int n) {
    return n * factorial(n - 1);  // Sin caso base → SOE
}

// ─────────────────────────────────────────────────────

// ConcurrentModificationException — modificar colección al iterar
List<String> lista = new ArrayList<>(List.of("A", "B", "C"));
for (String s : lista) {
    if (s.equals("B")) lista.remove(s);  // CME
}
// Prevención: usar Iterator.remove() o removeIf()
lista.removeIf(s -> s.equals("B"));

// ─────────────────────────────────────────────────────

// IllegalArgumentException — argumento inválido
public void setEdad(int edad) {
    if (edad < 0 || edad > 150)
        throw new IllegalArgumentException("Edad inválida: " + edad);
}

// ─────────────────────────────────────────────────────

// IllegalStateException — estado inválido del objeto
public void iniciar() {
    if (iniciado) throw new IllegalStateException("Ya está iniciado.");
    iniciado = true;
}

// ─────────────────────────────────────────────────────

// UnsupportedOperationException — operación no soportada
List<String> inmutable = List.of("A", "B");
inmutable.add("C");  // UOE: la lista es inmutable
```

### Checked Exceptions frecuentes

```java
// IOException — errores de entrada/salida
try (FileReader fr = new FileReader("archivo.txt")) {
    // ...
} catch (IOException e) {
    System.out.println("Error de E/S: " + e.getMessage());
}

// FileNotFoundException (subclase de IOException)
// ParseException — error de parsing
try {
    java.text.SimpleDateFormat sdf = new java.text.SimpleDateFormat("dd/MM/yyyy");
    java.util.Date fecha = sdf.parse("fecha-invalida");
} catch (java.text.ParseException e) {
    System.out.println("Formato de fecha inválido en posición: " + e.getErrorOffset());
}

// InterruptedException — hilo interrumpido
try {
    Thread.sleep(1000);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();  // Restaurar el estado de interrupción
    System.out.println("El hilo fue interrumpido.");
}
```

---

## 13. Buenas prácticas en el manejo de excepciones

### 13.1 Captura la excepción más específica posible

```java
// MAL: captura todo, pierde información
try { ... }
catch (Exception e) { System.out.println("Algo salió mal."); }

// BIEN: captura lo específico primero
try { ... }
catch (FileNotFoundException e) { System.out.println("Archivo no encontrado."); }
catch (IOException e)           { System.out.println("Error de E/S."); }
catch (Exception e)             { System.out.println("Error inesperado."); }
```

### 13.2 Nunca silencies excepciones (catch vacío)

```java
// ✗ PÉSIMO: el error desaparece silenciosamente
try {
    leerArchivo("datos.txt");
} catch (IOException e) {
    // No hacer nada (catch vacío)
    // El error ocurre pero nadie lo sabe → bug muy difícil de encontrar
}

// ✓ MÍNIMO: al menos loguea
try {
    leerArchivo("datos.txt");
} catch (IOException e) {
    System.err.println("Error al leer archivo: " + e.getMessage());
    e.printStackTrace();
}
```

### 13.3 No uses excepciones para control de flujo normal

```java
// ✗ MAL: excepciones como control de flujo
public boolean existeUsuario(String id) {
    try {
        encontrarUsuario(id);
        return true;
    } catch (UsuarioNoEncontradoException e) {
        return false;  // Usar excepción como flag de existencia es incorrecto
    }
}

// ✓ BIEN: método específico para verificar existencia
public boolean existeUsuario(String id) {
    return repositorio.containsKey(id);
}

public Usuario encontrarUsuario(String id) throws UsuarioNoEncontradoException {
    if (!repositorio.containsKey(id))
        throw new UsuarioNoEncontradoException(id);
    return repositorio.get(id);
}
```

### 13.4 Incluye información útil en el mensaje

```java
// ✗ MAL: mensaje genérico
throw new IllegalArgumentException("Valor inválido");

// ✓ BIEN: mensaje descriptivo con el valor que causó el problema
throw new IllegalArgumentException(
    "La edad debe estar entre 0 y 150. Valor recibido: " + edad);

throw new IllegalArgumentException(
    String.format("El precio no puede ser negativo. Precio recibido: $%.2f", precio));
```

### 13.5 Limpia los recursos siempre (try-with-resources)

```java
// ✓ BIEN: try-with-resources garantiza cierre de recursos
try (Connection conn = dataSource.getConnection();
     PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE id = ?")) {
    ps.setInt(1, userId);
    try (ResultSet rs = ps.executeQuery()) {
        while (rs.next()) {
            System.out.println(rs.getString("nombre"));
        }
    }
} catch (SQLException e) {
    throw new AplicacionException("BD-001", "Error al consultar usuarios", e);
}
```

### 13.6 Propaga las excepciones checked cuando no puedes manejarlas

```java
// ✗ MAL: captura y convierte a RuntimeException sin razón
public String leer() {
    try {
        return Files.readString(Path.of("data.txt"));
    } catch (IOException e) {
        throw new RuntimeException(e);  // Pérdida de información de tipo
    }
}

// ✓ BIEN: propaga la excepción si no puedes manejarla apropiadamente
public String leer() throws IOException {
    return Files.readString(Path.of("data.txt"));
}
```

### 13.7 Loguea en el nivel apropiado

```java
// Solo loguea donde tienes el contexto necesario para un mensaje útil
public class ServicioArchivos {
    private static final java.util.logging.Logger logger =
        java.util.logging.Logger.getLogger(ServicioArchivos.class.getName());

    public String leerContenido(String ruta) throws IOException {
        try {
            return java.nio.file.Files.readString(java.nio.file.Path.of(ruta));
        } catch (IOException e) {
            logger.severe("Error al leer el archivo '" + ruta + "': " + e.getMessage());
            throw e;  // Re-lanza para que el llamador también pueda manejarla
        }
    }
}
```

### 13.8 Documenta las excepciones con @throws en Javadoc

```java
/**
 * Procesa un pedido de compra.
 *
 * @param pedido El pedido a procesar. No puede ser null.
 * @return El número de confirmación del pedido
 * @throws IllegalArgumentException si el pedido es null o inválido
 * @throws StockInsuficienteException si no hay stock suficiente
 * @throws PagoRechazadoException si el método de pago es rechazado
 */
public String procesarPedido(Pedido pedido)
        throws StockInsuficienteException, PagoRechazadoException {
    if (pedido == null)
        throw new IllegalArgumentException("El pedido no puede ser null.");
    // ...
}
```

---

## 14. Errores comunes con excepciones

### Error 1: Catch vacío o demasiado genérico

```java
// ✗ MAL: el error desaparece
try { operacionCritica(); }
catch (Exception e) { }  // ¡Nunca hagas esto!

// ✗ TAMBIÉN MAL: captura errores de JVM que no deberías capturar
try { operacion(); }
catch (Throwable t) { }  // Captura incluso OutOfMemoryError y StackOverflowError
```

### Error 2: Olvidar restaurar el estado en el catch

```java
// ✗ MAL: el objeto queda en estado inconsistente
public void transferir(double monto) throws SaldoInsuficienteException {
    retirar(monto);   // Si esto falla, el dinero no se retira
    try {
        destino.depositar(monto);  // Si esto falla, el dinero ya se retiró
    } catch (Exception e) {
        // ¡El dinero se retiró pero no se depositó!
    }
}

// ✓ BIEN: diseño transaccional
public void transferir(double monto) throws SaldoInsuficienteException {
    retirar(monto);
    try {
        destino.depositar(monto);
    } catch (Exception e) {
        depositar(monto);  // Revertir el retiro
        throw new RuntimeException("Transferencia fallida, operación revertida.", e);
    }
}
```

### Error 3: Perder la causa original al relanzar

```java
// ✗ MAL: se pierde la causa raíz
try { ... }
catch (IOException e) {
    throw new AplicacionException("Error");  // Sin pasar 'e' como causa
}

// ✓ BIEN: preserva la causa
try { ... }
catch (IOException e) {
    throw new AplicacionException("Error al cargar datos", e);  // Con causa
}
```

### Error 4: Declarar throws Exception genérico

```java
// ✗ MAL: demasiado genérico
public void procesar() throws Exception { ... }

// ✓ BIEN: específico
public void procesar() throws IOException, ParseException { ... }
```

### Error 5: Usar excepciones para control de flujo normal

```java
// ✗ MAL: lento y semánticamente incorrecto
for (int i = 0; ; i++) {
    try {
        procesarElemento(array[i]);
    } catch (ArrayIndexOutOfBoundsException e) {
        break;  // Usar excepción para saber cuándo termina el array
    }
}

// ✓ BIEN: usar la condición correcta
for (int i = 0; i < array.length; i++) {
    procesarElemento(array[i]);
}
```

### Error 6: No cerrar recursos en finally (antes de Java 7)

```java
// ✗ MAL: si close() lanza excepción, la excepción original se pierde
try {
    recurso = abrir();
    usar(recurso);
} catch (Exception e) {
    throw e;
} finally {
    recurso.close();  // Si esto falla, puede suprimir la excepción del try
}

// ✓ BIEN: usar try-with-resources (Java 7+)
try (Recurso recurso = abrir()) {
    usar(recurso);
}
```

---

## 15. Ejercicios prácticos

### Ejercicio 1 — Validador con excepciones personalizadas (Nivel: Básico)

Crea un sistema de registro de usuarios con estas excepciones personalizadas (todas unchecked):
- `RegistroException` (base): `codigoError`, `mensaje`
- `NombreInvalidoException extends RegistroException`
- `EmailInvalidoException extends RegistroException`
- `PasswordDebilException extends RegistroException`
- `UsuarioDuplicadoException extends RegistroException`

Crea `RegistroServicio` con `registrar(String nombre, String email, String password)` que:
- Valida nombre: no vacío, 2–50 caracteres, solo letras y espacios
- Valida email: formato correcto con regex
- Valida password: mínimo 8 chars, al menos 1 mayúscula, 1 minúscula, 1 dígito
- Verifica que el email no esté ya registrado

En `Main`, prueba al menos 6 casos distintos (válidos e inválidos) con manejo individual de cada excepción.

---

### Ejercicio 2 — Calculadora robusta (Nivel: Básico-Medio)

Crea `CalculadoraRobusta` con operaciones: `sumar`, `restar`, `multiplicar`, `dividir`, `raizCuadrada`, `logaritmo`, `potencia`.

Cada operación debe:
- Validar sus argumentos (división por cero, raíz de negativo, log de no positivo)
- Lanzar `OperacionInvalidaException` (personalizada checked) con código y mensaje descriptivo
- Mantener un historial de las últimas 10 operaciones exitosas

En `Main`, implementa un menú interactivo que:
- Lee números y operación del usuario
- Maneja las excepciones y muestra mensajes amigables (sin stack traces al usuario)
- Permite ver el historial de operaciones

---

### Ejercicio 3 — Sistema de archivos simulado (Nivel: Medio)

Simula un sistema de archivos en memoria con excepciones checked:

Excepciones: `SistemaArchivosException`, `ArchivoNoEncontradoException`, `ArchivoYaExisteException`, `PermisosDenegadosException`, `EspacioInsuficienteException`

Clase `SistemaArchivos`:
- `crear(String ruta, String contenido)` throws checked exceptions
- `leer(String ruta)` throws checked exceptions
- `escribir(String ruta, String contenido)` throws checked exceptions
- `eliminar(String ruta)` throws checked exceptions
- `mover(String origen, String destino)` throws checked exceptions
- `copiar(String origen, String destino)` throws checked exceptions
- `listar(String directorio)` throws checked exceptions

Simula: capacidad máxima de almacenamiento, archivos de solo lectura, directorios anidados.

En `Main`, demuestra todas las excepciones con casos de prueba.

---

### Ejercicio 4 — Sistema bancario con excepciones (Nivel: Medio)

Extiende el sistema bancario del Tema 7/8 con manejo completo de excepciones:

Excepciones personalizadas:
- `BancoException` (base, checked): código de error, timestamp
- `CuentaNoEncontradaException`
- `SaldoInsuficienteException`: saldoDisponible, montoSolicitado
- `CuentaBloqueadaException`: motivo del bloqueo
- `TransaccionInvalidaException`
- `LimiteDiarioExcedidoException`: limitePermitido, totalHoy

Clase `Banco`:
- `crearCuenta(String titular, double saldoInicial)` throws BancoException
- `retirar(String numeroCuenta, double monto)` throws BancoException
- `depositar(String numeroCuenta, double monto)` throws BancoException
- `transferir(String cuentaOrigen, String cuentaDestino, double monto)` throws BancoException
- `bloquearCuenta(String numeroCuenta, String motivo)`
- Cada cuenta tiene límite diario de retiro de $5,000

Demuestra todos los escenarios de error y éxito con manejo apropiado.

---

### Ejercicio 5 — Parser de datos con encadenamiento (Nivel: Avanzado)

Diseña un sistema de parsing de CSV/JSON simulado con encadenamiento completo de excepciones:

```
ParseException (checked, base)
├── FormatoInvalidoException     ← el archivo no tiene el formato esperado
├── CampoObligatorioException    ← falta un campo requerido
├── TipoDatoInvalidoException    ← el valor no puede convertirse al tipo esperado
└── ValorFueraDeRangoException   ← el valor existe pero está fuera del rango válido
```

Crea `ParserProductos` que lea líneas CSV ("codigo,nombre,precio,stock") y convierta a objetos `Producto`:
- Valida que haya exactamente 4 campos
- Valida que código sea entero positivo
- Valida que precio sea double positivo
- Valida que stock sea entero no negativo
- Valida que nombre no esté vacío

Cada error de validación debe:
- Incluir el número de línea donde ocurrió
- Encadenar la excepción original (NumberFormatException, etc.) como causa
- Proporcionar el valor inválido en el mensaje

En `Main`, parsea un conjunto de líneas (algunas válidas, otras con distintos errores) y muestra un reporte de resultados y errores.

---

### Ejercicio 6 — Framework de reintentos (Nivel: Avanzado)

Implementa un framework de reintentos automáticos usando excepciones e interfaces funcionales:

```java
@FunctionalInterface
interface OperacionReintetable<T> {
    T ejecutar() throws Exception;
}

class PoliticaReintentos {
    int maxIntentos;
    long esperaMs;
    Class<? extends Exception>[] excepcionesReintentables;
    // constructor, getters
}

class ResultadoEjecucion<T> {
    T valor;
    int intentosRealizados;
    List<Exception> errores;
    boolean exitoso;
    // constructor, getters, toString
}
```

Implementa `EjecutorConReintentos`:
- `ejecutar(OperacionReintetable<T> op, PoliticaReintentos politica)` → `ResultadoEjecucion<T>`
- Reintenta solo si la excepción es del tipo configurado como reintentable
- Espera el tiempo configurado entre reintentos
- Encadena todas las excepciones en el resultado
- Lanza una excepción final si se agotaron los reintentos

Simula: conexión a base de datos que falla las primeras 2 veces (IOException) y luego funciona.

---

## 16. Resumen del tema y del Nivel 2

### Manejo de excepciones en una página

```java
// ═══════════════════════════════════════════════
// EXCEPCIÓN PERSONALIZADA
// ═══════════════════════════════════════════════
public class MiException extends Exception {          // checked
    private String codigo;

    public MiException(String codigo, String msg)              { super(msg); this.codigo = codigo; }
    public MiException(String codigo, String msg, Throwable c) { super(msg, c); this.codigo = codigo; }
    public String getCodigo() { return codigo; }
}

// ═══════════════════════════════════════════════
// LANZAR — throw / throws
// ═══════════════════════════════════════════════
public void metodo(int valor) throws MiException {
    if (valor < 0)
        throw new MiException("E001", "Valor negativo: " + valor);
}

// ═══════════════════════════════════════════════
// CAPTURAR — try-catch-finally
// ═══════════════════════════════════════════════
try {
    metodo(-1);                          // Puede lanzar MiException
} catch (MiException e) {               // Específico primero
    System.out.println(e.getCodigo() + ": " + e.getMessage());
} catch (RuntimeException e) {           // Menos específico después
    System.out.println("Error runtime: " + e.getMessage());
} finally {
    System.out.println("Siempre se ejecuta");
}

// ═══════════════════════════════════════════════
// TRY-WITH-RESOURCES — AutoCloseable
// ═══════════════════════════════════════════════
try (MiRecurso r = new MiRecurso()) {
    r.usar();
} catch (Exception e) {
    e.printStackTrace();
}  // r.close() automático

// ═══════════════════════════════════════════════
// MULTI-CATCH (Java 7+)
// ═══════════════════════════════════════════════
try { ... }
catch (TipoA | TipoB | TipoC e) {
    manejar(e);
}
```

### Reglas de oro del manejo de excepciones

| Situación | Regla |
|-----------|-------|
| Checked vs. Unchecked | Checked: situaciones externas recuperables. Unchecked: bugs del programador |
| Catch vacío | NUNCA — al menos loguea la excepción |
| Catch genérico | Último recurso, después de los específicos |
| Recursos | Siempre usar try-with-resources para `AutoCloseable` |
| Mensaje | Incluir siempre el valor que causó el error |
| Causa | Preservar siempre la excepción original al relanzar |
| Control de flujo | NUNCA usar excepciones para lógica normal |
| Documentación | Documentar con `@throws` en Javadoc todos los métodos públicos |
| finally | Solo para limpieza de recursos, no para lógica de negocio |
| Propagación | Si no puedes manejarla apropiadamente, propágala con throws |

---

### Resumen completo del Nivel 2: Programación Orientada a Objetos

¡Felicidades! Has completado el **Nivel 2** completo. Dominas los cuatro pilares de la POO en Java:

| Tema | Conceptos clave dominados |
|------|--------------------------|
| **T7: Clases y Objetos** | Clase, objeto, atributos, constructores, `this`, `new`, `toString()`, `static`, ciclo de vida |
| **T8: Encapsulamiento** | `private`, getters/setters con validación, objetos inmutables, copia defensiva, JavaBeans |
| **T9: Herencia** | `extends`, `super()`, `@Override`, jerarquías, clases abstractas, `final`, upcasting, composición |
| **T10: Polimorfismo** | Early/late binding, despacho dinámico, Strategy pattern, LSP, polimorfismo con colecciones |
| **T11: Interfaces** | `implements`, múltiple implementación, `default`, `static`, lambdas, interfaces funcionales |
| **T12: Excepciones** | `try-catch-finally`, `throw/throws`, excepciones personalizadas, checked/unchecked, encadenamiento |

---

## ¿Qué sigue? — Nivel 3: Java Avanzado

Con los sólidos fundamentos de los Niveles 1 y 2, el **Nivel 3** te lleva al Java de producción real:

| Tema | Contenido |
|------|-----------|
| **T13: Colecciones** | `List`, `Set`, `Map`, `Queue`, `Deque`, `Collections`, rendimiento y cuándo usar cada una |
| **T14: Genéricos** | Clases y métodos genéricos, wildcards (`?`, `extends`, `super`), type erasure |
| **T15: Streams y Lambdas** | API Stream, operaciones intermedias/terminales, `map`, `filter`, `reduce`, collectors |
| **T16: Entrada/Salida (I/O)** | `File`, `Path`, `Files`, NIO.2, lectura/escritura de archivos, serialización |
| **T17: Hilos y Concurrencia** | `Thread`, `Runnable`, `ExecutorService`, `synchronized`, `CompletableFuture` |
| **T18: Patrones de diseño** | Singleton, Factory, Builder, Observer, Decorator, Strategy en proyectos reales |

---

*Curso de Java — Nivel 2: Programación Orientada a Objetos*
*Tema 12 de 12 — ¡Nivel 2 completado!*
*Creado con fines educativos*
