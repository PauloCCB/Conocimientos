# Curso de Java — Nivel 2: Programación Orientada a Objetos
## Tema 8: Encapsulamiento

> **Nivel:** Intermedio | **Prerrequisitos:** Temas 1–7 completados | **Duración estimada:** 4–5 horas

---

## Tabla de contenidos

1. [¿Qué es el Encapsulamiento?](#1-qué-es-el-encapsulamiento)
2. [Modificadores de acceso](#2-modificadores-de-acceso)
3. [Atributos privados](#3-atributos-privados)
4. [Getters — métodos de consulta](#4-getters--métodos-de-consulta)
5. [Setters — métodos de modificación](#5-setters--métodos-de-modificación)
6. [Validación dentro de setters](#6-validación-dentro-de-setters)
7. [Encapsulamiento en constructores](#7-encapsulamiento-en-constructores)
8. [Objetos inmutables](#8-objetos-inmutables)
9. [Encapsulamiento de colecciones y arrays](#9-encapsulamiento-de-colecciones-y-arrays)
10. [El patrón JavaBeans](#10-el-patrón-javabeans)
11. [Buenas prácticas de encapsulamiento](#11-buenas-prácticas-de-encapsulamiento)
12. [Errores comunes con encapsulamiento](#12-errores-comunes-con-encapsulamiento)
13. [Ejercicios prácticos](#13-ejercicios-prácticos)
14. [Resumen del tema](#14-resumen-del-tema)

---

## 1. ¿Qué es el Encapsulamiento?

El **encapsulamiento** es el primer pilar de la Programación Orientada a Objetos. Consiste en **ocultar los detalles internos** de un objeto y **exponer solo lo necesario** a través de una interfaz pública controlada.

La idea central es que un objeto debe ser el único responsable de su propio estado. Nadie debería poder modificar sus datos directamente desde afuera sin pasar por las reglas que el propio objeto define.

### La analogía del cajero automático

Piensa en un cajero automático (ATM):

```
SIN encapsulamiento:                CON encapsulamiento:
──────────────────────              ──────────────────────────────
Cualquiera puede:                   Solo puedes interactuar a través de:
  - Abrir la caja y                   - Ingresar tu tarjeta
    tomar el dinero                   - Ingresar tu PIN
  - Cambiar el saldo                  - Seleccionar operación
    directamente                      - Recibir dinero o estado
  - Modificar los                   
    registros                       El mecanismo interno está oculto.
                                    Las reglas se aplican siempre.
```

El cajero controla exactamente qué operaciones están permitidas y aplica validaciones (¿tienes saldo? ¿el PIN es correcto?). El usuario no puede "saltarse" esas reglas porque no tiene acceso directo a los internos.

### ¿Por qué es tan importante?

```java
// SIN encapsulamiento: estado inválido posible
public class Persona {
    public int edad;  // Cualquiera puede escribir cualquier cosa
}

Persona p = new Persona();
p.edad = -50;       // ¡Edad negativa! Estado inválido.
p.edad = 999;       // ¡Edad imposible! Sin control.
p.edad = 0;         // ¿Un recién nacido? ¿O un error?
```

```java
// CON encapsulamiento: el objeto garantiza su propio estado válido
public class Persona {
    private int edad;  // Nadie puede acceder directamente

    public void setEdad(int edad) {
        if (edad >= 0 && edad <= 150) {
            this.edad = edad;
        } else {
            throw new IllegalArgumentException("Edad inválida: " + edad);
        }
    }

    public int getEdad() { return edad; }
}

Persona p = new Persona();
p.setEdad(-50);  // IllegalArgumentException: Edad inválida: -50
p.setEdad(999);  // IllegalArgumentException: Edad inválida: 999
p.setEdad(28);   // OK: estado válido garantizado
```

### Beneficios concretos del encapsulamiento

| Beneficio | Descripción |
|-----------|-------------|
| **Integridad de datos** | Los objetos garantizan que siempre están en un estado válido |
| **Flexibilidad** | Puedes cambiar la implementación interna sin afectar el código externo |
| **Mantenimiento** | Un solo lugar para aplicar reglas de negocio |
| **Seguridad** | Control total sobre qué puede modificarse y cómo |
| **Abstracción** | El usuario de la clase no necesita saber cómo funciona internamente |

---

## 2. Modificadores de acceso

Java tiene cuatro niveles de acceso que controlan desde dónde puede accederse a una clase, atributo o método.

### La tabla completa de modificadores

| Modificador | Misma clase | Mismo paquete | Subclase (otro paquete) | Cualquier lugar |
|-------------|:-----------:|:-------------:|:-----------------------:|:---------------:|
| `private`   | ✓           | ✗             | ✗                       | ✗               |
| *(ninguno)* | ✓           | ✓             | ✗                       | ✗               |
| `protected` | ✓           | ✓             | ✓                       | ✗               |
| `public`    | ✓           | ✓             | ✓                       | ✓               |

### Representación visual

```
UNIVERSO DEL PROGRAMA
┌──────────────────────────────────────────────────────────────┐
│  Cualquier lugar (public)                                    │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Mismo paquete (package-private / sin modificador)     │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │  Subclases (protected)                           │  │  │
│  │  │  ┌────────────────────────────────────────────┐  │  │  │
│  │  │  │  Misma clase (private)                     │  │  │  │
│  │  │  └────────────────────────────────────────────┘  │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

### 2.1 private — el más restrictivo

Solo accesible dentro de la **misma clase**. Es el nivel de acceso correcto para casi todos los atributos.

```java
public class CuentaBancaria {
    private double saldo;        // Solo accesible dentro de CuentaBancaria
    private String numeroCuenta;

    public void depositar(double monto) {
        // OK: estamos dentro de la misma clase
        if (monto > 0) saldo += monto;
    }
}

// Desde fuera de la clase:
CuentaBancaria c = new CuentaBancaria("Ana", 1000);
c.saldo = 99999;  // ERROR de compilación: saldo tiene acceso private
```

### 2.2 Sin modificador — acceso de paquete (package-private)

Accesible dentro de la **misma clase y cualquier clase del mismo paquete**. Raramente se usa intencionalmente para atributos; es más común en clases auxiliares internas de un paquete.

```java
// Paquete: com.miempresa.util
package com.miempresa.util;

class Helper {
    int valorInterno;  // Sin modificador: accesible en todo el paquete
    
    void metodoHelper() { ... }
}
```

### 2.3 protected — acceso para herencia

Accesible en la **misma clase, mismo paquete y subclases** (aunque estén en otro paquete). Se usa principalmente en el contexto de herencia (Tema 9).

```java
public class Animal {
    protected String nombre;    // Las subclases pueden acceder directamente
    protected int energia;

    protected void respirar() {
        energia--;
        System.out.println(nombre + " respira.");
    }
}

public class Perro extends Animal {
    public void ladrar() {
        respirar();            // OK: acceso a método protected del padre
        System.out.println(nombre + " ladra: ¡Guau!");  // OK: atributo protected
    }
}
```

### 2.4 public — acceso total

Accesible desde **cualquier clase, en cualquier paquete**. Correcto para la interfaz pública de la clase: métodos que otros necesitan usar.

```java
public class Calculadora {
    // Atributo privado: implementación interna
    private double ultimoResultado;

    // Métodos públicos: interfaz que otros usan
    public double sumar(double a, double b) {
        ultimoResultado = a + b;
        return ultimoResultado;
    }

    public double getUltimoResultado() {
        return ultimoResultado;
    }
}
```

### Modificadores para clases

Las clases de nivel superior (no internas) solo pueden ser `public` o sin modificador:

```java
public class ClasePublica { }     // Accesible desde cualquier paquete
class ClaseDesPaquete { }         // Accesible solo en el mismo paquete
// private class Error { }        // ERROR: no válido para clase de nivel superior
// protected class Error { }      // ERROR: no válido para clase de nivel superior
```

---

## 3. Atributos privados

La regla de oro del encapsulamiento es:

> **Todos los atributos de una clase deben ser `private`.**

Esto garantiza que ningún código externo pueda modificar directamente el estado del objeto. El único acceso es a través de los métodos que la clase provee.

### Antes y después del encapsulamiento

```java
// ✗ MAL: atributos públicos, sin control
public class Empleado {
    public String nombre;
    public double salario;
    public int horasTrabajadas;
    public boolean activo;
}

// Cualquiera puede hacer esto sin ninguna validación:
Empleado e = new Empleado();
e.salario = -5000;       // Salario negativo: imposible en la realidad
e.horasTrabajadas = -1;  // Horas negativas: sin sentido
e.nombre = null;         // Nombre nulo: causa NPE después
```

```java
// ✓ BIEN: atributos privados, control garantizado
public class Empleado {
    private String nombre;
    private double salario;
    private int horasTrabajadas;
    private boolean activo;

    // Solo se puede interactuar mediante métodos controlados
    public void setSalario(double salario) {
        if (salario >= 0) this.salario = salario;
        else throw new IllegalArgumentException("El salario no puede ser negativo.");
    }
    // ... más getters y setters con validación
}
```

### Por qué private en atributos es tan importante

**1. Puedes cambiar la implementación sin romper el código que usa la clase:**

```java
// Versión 1: almacena la edad como int
public class Persona {
    private int edad;
    public int getEdad() { return edad; }
}

// Versión 2: más adelante decides almacenar fecha de nacimiento en vez de edad
// El código externo que llama a getEdad() NO necesita cambiar
public class Persona {
    private LocalDate fechaNacimiento;   // Cambio interno
    public int getEdad() {
        return Period.between(fechaNacimiento, LocalDate.now()).getYears();
    }
    // Los que llamaban getEdad() siguen funcionando igual
}
```

**2. Un solo lugar para las reglas:**

```java
public class Producto {
    private double precio;

    public void setPrecio(double precio) {
        if (precio < 0) throw new IllegalArgumentException("Precio no puede ser negativo.");
        if (precio > 1_000_000) throw new IllegalArgumentException("Precio excede el máximo.");
        this.precio = precio;
        // Si mañana cambia la regla, solo cambias aquí
    }
}
```

**3. Facilita la depuración:**

Con atributos privados, si el precio tiene un valor inválido, sabes que SOLO pudo haber pasado a través de `setPrecio()`. Puedes agregar un breakpoint en ese método y encontrar el problema. Con atributos públicos, cualquier línea de código en cualquier lugar del programa pudo haberlo cambiado.

---

## 4. Getters — métodos de consulta

Un **getter** (también llamado accessor) es un método público que devuelve el valor de un atributo privado. Sigue la convención de nomenclatura `getNombreAtributo()`.

### Convención de nomenclatura

```java
// Para atributos no booleanos: get + NombreAtributo (PascalCase)
private String nombre;       → public String getNombre()
private double precio;       → public double getPrecio()
private int cantidadStock;   → public int getCantidadStock()
private LocalDate fechaAlta; → public LocalDate getFechaAlta()

// Para atributos boolean: is + NombreAtributo
private boolean activo;      → public boolean isActivo()
private boolean disponible;  → public boolean isDisponible()
private boolean esMayor;     → public boolean isEsMayor()
```

### Getters simples

```java
public class Producto {
    private int codigo;
    private String nombre;
    private double precio;
    private int stock;
    private boolean disponible;

    // Getters simples — retornan el valor directamente
    public int getCodigo()       { return codigo; }
    public String getNombre()    { return nombre; }
    public double getPrecio()    { return precio; }
    public int getStock()        { return stock; }
    public boolean isDisponible(){ return disponible; }
}
```

### Getters calculados — más que simples lecturas

Un getter no está obligado a devolver exactamente el valor de un atributo. Puede calcular y devolver información derivada:

```java
public class Empleado {
    private String nombre;
    private String apellido;
    private double salarioMensual;
    private int horasExtra;

    // Getter simple
    public String getNombre()     { return nombre; }
    public String getApellido()   { return apellido; }
    public double getSalario()    { return salarioMensual; }

    // Getters calculados — información derivada
    public String getNombreCompleto() {
        return nombre + " " + apellido;
    }

    public double getSalarioAnual() {
        return salarioMensual * 12;
    }

    public double getSalarioConExtras() {
        double pagoExtra = horasExtra * (salarioMensual / 160.0) * 1.5;
        return salarioMensual + pagoExtra;
    }

    public String getCategoria() {
        if (salarioMensual >= 5000) return "Senior";
        if (salarioMensual >= 3000) return "Mid";
        return "Junior";
    }
}
```

### No todos los atributos necesitan getter

Proporciona getters solo para los atributos que el mundo exterior necesita conocer. Los atributos que son puramente internos (contadores, flags de estado interno) pueden no tener getter:

```java
public class ConexionBD {
    private String url;
    private String usuario;
    private String password;     // Sin getter: nunca expongas contraseñas
    private int reintentos;      // Sin getter: detalle interno
    private boolean conectado;

    public String getUrl()       { return url; }
    public String getUsuario()   { return usuario; }
    public boolean isConectado() { return conectado; }
    // password y reintentos son privados y sin getter
}
```

---

## 5. Setters — métodos de modificación

Un **setter** (también llamado mutator) es un método público que modifica el valor de un atributo privado. Sigue la convención `setNombreAtributo(tipo valor)`.

### Convención de nomenclatura

```java
private String nombre;       → public void setNombre(String nombre)
private double precio;       → public void setPrecio(double precio)
private boolean activo;      → public void setActivo(boolean activo)
```

### Setters simples

```java
public class Configuracion {
    private String idioma;
    private int timeout;
    private boolean modoDebug;

    public void setIdioma(String idioma)       { this.idioma = idioma; }
    public void setTimeout(int timeout)        { this.timeout = timeout; }
    public void setModoDebug(boolean debug)    { this.modoDebug = debug; }
}
```

### No todos los atributos necesitan setter

Muchos atributos NO deben tener setter porque no deben cambiar después de la creación del objeto:

```java
public class Persona {
    private final String dni;        // El DNI no cambia → sin setter, final
    private String nombre;           // El nombre puede cambiar (matrimonio)
    private LocalDate fechaNacimiento; // No cambia → sin setter
    private String email;            // Puede cambiar → con setter

    public Persona(String dni, String nombre, LocalDate fechaNacimiento) {
        this.dni = dni;
        this.nombre = nombre;
        this.fechaNacimiento = fechaNacimiento;
    }

    // Solo nombre y email tienen setters
    public void setNombre(String nombre) { this.nombre = nombre; }
    public void setEmail(String email) { this.email = email; }

    // Getters para todo
    public String getDni()                   { return dni; }
    public String getNombre()                { return nombre; }
    public LocalDate getFechaNacimiento()    { return fechaNacimiento; }
    public String getEmail()                 { return email; }
}
```

---

## 6. Validación dentro de setters

El verdadero poder del encapsulamiento está en la **validación** dentro de los setters. Cada setter es la única puerta de entrada para modificar un atributo, por lo que es el lugar ideal para aplicar las reglas de negocio.

### Patrón de validación con excepción

```java
public class Empleado {
    private String nombre;
    private double salario;
    private int edad;
    private String email;

    public void setNombre(String nombre) {
        if (nombre == null || nombre.trim().isEmpty()) {
            throw new IllegalArgumentException("El nombre no puede ser nulo o vacío.");
        }
        this.nombre = nombre.trim();  // Limpia espacios extra
    }

    public void setSalario(double salario) {
        if (salario < 0) {
            throw new IllegalArgumentException("El salario no puede ser negativo: " + salario);
        }
        if (salario > 500_000) {
            throw new IllegalArgumentException("El salario supera el máximo permitido.");
        }
        this.salario = salario;
    }

    public void setEdad(int edad) {
        if (edad < 18) {
            throw new IllegalArgumentException("El empleado debe ser mayor de edad.");
        }
        if (edad > 70) {
            throw new IllegalArgumentException("Edad fuera del rango laboral.");
        }
        this.edad = edad;
    }

    public void setEmail(String email) {
        if (email == null || !email.matches("[a-zA-Z0-9+_.-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}")) {
            throw new IllegalArgumentException("Email inválido: " + email);
        }
        this.email = email.toLowerCase();  // Normaliza a minúsculas
    }
}
```

### Patrón de validación silenciosa (ignorar valores inválidos)

A veces es preferible simplemente ignorar el valor inválido en vez de lanzar una excepción:

```java
public class Configuracion {
    private int intentosMaximos = 3;
    private int timeout = 30;

    public void setIntentosMaximos(int intentos) {
        if (intentos >= 1 && intentos <= 10) {
            this.intentosMaximos = intentos;
        }
        // Si es inválido, simplemente no cambia — mantiene el valor actual
    }

    public void setTimeout(int segundos) {
        if (segundos > 0) this.timeout = segundos;
        // Si es <= 0, se ignora silenciosamente
    }
}
```

### Validación con valor corregido automáticamente

En algunos casos conviene corregir el valor en vez de rechazarlo:

```java
public class Volumen {
    private int nivel;  // 0 a 100

    public void setNivel(int nivel) {
        // Clamp: ajusta al rango válido en vez de rechazar
        this.nivel = Math.max(0, Math.min(100, nivel));
        // Si nivel=-5 → 0; si nivel=150 → 100; si nivel=75 → 75
    }

    public void subir(int cantidad) {
        setNivel(this.nivel + cantidad);  // Usa el setter para aprovechar la validación
    }

    public void bajar(int cantidad) {
        setNivel(this.nivel - cantidad);
    }
}
```

### Validaciones de consistencia entre atributos

A veces la validación de un atributo depende del valor de otro:

```java
public class Rango {
    private double minimo;
    private double maximo;

    public void setMinimo(double minimo) {
        if (minimo > this.maximo && this.maximo != 0) {
            throw new IllegalArgumentException(
                "El mínimo no puede ser mayor que el máximo actual (" + this.maximo + ").");
        }
        this.minimo = minimo;
    }

    public void setMaximo(double maximo) {
        if (maximo < this.minimo) {
            throw new IllegalArgumentException(
                "El máximo no puede ser menor que el mínimo actual (" + this.minimo + ").");
        }
        this.maximo = maximo;
    }

    public boolean contiene(double valor) {
        return valor >= minimo && valor <= maximo;
    }
}
```

---

## 7. Encapsulamiento en constructores

El constructor también es parte de la interfaz pública del objeto y debe aplicar las mismas validaciones que los setters. Una forma elegante de hacerlo es **llamar a los setters desde el constructor**, de modo que la lógica de validación no se duplique.

### Reutilizar la validación del setter en el constructor

```java
public class Producto {
    private String nombre;
    private double precio;
    private int stock;

    // Constructor que reutiliza los setters
    public Producto(String nombre, double precio, int stock) {
        setNombre(nombre);   // Usa el setter → aplica la validación
        setPrecio(precio);
        setStock(stock);
    }

    public void setNombre(String nombre) {
        if (nombre == null || nombre.trim().isEmpty())
            throw new IllegalArgumentException("El nombre no puede estar vacío.");
        this.nombre = nombre.trim();
    }

    public void setPrecio(double precio) {
        if (precio < 0)
            throw new IllegalArgumentException("El precio no puede ser negativo.");
        this.precio = precio;
    }

    public void setStock(int stock) {
        if (stock < 0)
            throw new IllegalArgumentException("El stock no puede ser negativo.");
        this.stock = stock;
    }

    public String getNombre() { return nombre; }
    public double getPrecio() { return precio; }
    public int getStock()     { return stock; }
}

// Uso:
Producto p1 = new Producto("Laptop", 999.99, 10);  // OK
Producto p2 = new Producto("", 50.0, 5);            // IllegalArgumentException
Producto p3 = new Producto("Mouse", -10.0, 3);      // IllegalArgumentException
```

### Constructor con valores por defecto y setters opcionales

Un patrón muy útil es tener un constructor mínimo y dejar que el usuario personalice con setters:

```java
public class Conexion {
    private String host;
    private int puerto;
    private int timeout;
    private int reintentos;

    // Constructor mínimo obligatorio
    public Conexion(String host, int puerto) {
        setHost(host);
        setPuerto(puerto);
        this.timeout = 30;    // valor por defecto
        this.reintentos = 3;  // valor por defecto
    }

    // Setters para configuración opcional
    public void setHost(String host) {
        if (host == null || host.trim().isEmpty())
            throw new IllegalArgumentException("Host no puede ser vacío.");
        this.host = host.trim();
    }

    public void setPuerto(int puerto) {
        if (puerto < 1 || puerto > 65535)
            throw new IllegalArgumentException("Puerto debe estar entre 1 y 65535.");
        this.puerto = puerto;
    }

    public void setTimeout(int segundos) {
        if (segundos > 0) this.timeout = segundos;
    }

    public void setReintentos(int reintentos) {
        if (reintentos >= 0 && reintentos <= 10) this.reintentos = reintentos;
    }

    public String getHost()     { return host; }
    public int getPuerto()      { return puerto; }
    public int getTimeout()     { return timeout; }
    public int getReintentos()  { return reintentos; }

    @Override
    public String toString() {
        return String.format("Conexion{host='%s', puerto=%d, timeout=%ds, reintentos=%d}",
            host, puerto, timeout, reintentos);
    }
}

// Uso
Conexion c = new Conexion("localhost", 8080);
c.setTimeout(60);
c.setReintentos(5);
System.out.println(c);
// Conexion{host='localhost', puerto=8080, timeout=60s, reintentos=5}
```

---

## 8. Objetos inmutables

Un **objeto inmutable** es aquel cuyo estado no puede cambiar después de ser creado. Es la forma más fuerte de encapsulamiento: si nadie puede cambiar el estado, no hay necesidad de validaciones en setters (porque no hay setters).

### Características de un objeto inmutable

1. Todos los atributos son `private` y `final`.
2. No hay setters.
3. Los getters devuelven copias de los objetos mutables (no referencias directas).
4. La clase puede declararse `final` para evitar subclases que rompan la inmutabilidad.

### Ejemplo: clase Punto inmutable

```java
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Solo getters, sin setters
    public double getX() { return x; }
    public double getY() { return y; }

    // En vez de modificar, crean un NUEVO objeto con el cambio
    public Punto moverX(double delta) {
        return new Punto(this.x + delta, this.y);
    }

    public Punto moverY(double delta) {
        return new Punto(this.x, this.y + delta);
    }

    public double distanciaA(Punto otro) {
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }

    @Override
    public String toString() {
        return String.format("Punto(%.2f, %.2f)", x, y);
    }
}

// Uso:
Punto p1 = new Punto(3.0, 4.0);
Punto p2 = p1.moverX(2.0);  // Crea un nuevo punto; p1 no cambia
System.out.println(p1);  // Punto(3.00, 4.00) — sin cambios
System.out.println(p2);  // Punto(5.00, 4.00) — nuevo objeto
```

### Ejemplo: clase Dinero inmutable

```java
public final class Dinero {
    private final double monto;
    private final String moneda;

    public Dinero(double monto, String moneda) {
        if (monto < 0) throw new IllegalArgumentException("El monto no puede ser negativo.");
        if (moneda == null || moneda.isEmpty())
            throw new IllegalArgumentException("La moneda no puede estar vacía.");
        this.monto = monto;
        this.moneda = moneda.toUpperCase();
    }

    public double getMonto()   { return monto; }
    public String getMoneda()  { return moneda; }

    public Dinero sumar(Dinero otro) {
        if (!this.moneda.equals(otro.moneda))
            throw new IllegalArgumentException("No se pueden sumar monedas distintas.");
        return new Dinero(this.monto + otro.monto, this.moneda);
    }

    public Dinero restar(Dinero otro) {
        if (!this.moneda.equals(otro.moneda))
            throw new IllegalArgumentException("No se pueden restar monedas distintas.");
        if (this.monto < otro.monto)
            throw new IllegalArgumentException("Saldo insuficiente.");
        return new Dinero(this.monto - otro.monto, this.moneda);
    }

    public Dinero aplicarPorcentaje(double porcentaje) {
        return new Dinero(this.monto * (porcentaje / 100.0), this.moneda);
    }

    @Override
    public String toString() {
        return String.format("%.2f %s", monto, moneda);
    }
}

// Uso:
Dinero precio = new Dinero(100.0, "EUR");
Dinero iva = precio.aplicarPorcentaje(21);
Dinero total = precio.sumar(iva);
System.out.println("Precio: " + precio);    // 100.00 EUR
System.out.println("IVA:    " + iva);       // 21.00 EUR
System.out.println("Total:  " + total);     // 121.00 EUR
```

### Ventajas de los objetos inmutables

- **Thread-safe por naturaleza:** pueden compartirse entre hilos sin sincronización.
- **Sin efectos secundarios inesperados:** nadie puede cambiar tu objeto desde afuera.
- **Seguros como claves en mapas:** su hash no cambia.
- **Fáciles de razonar:** el estado es siempre el mismo desde la creación.

`String`, `Integer`, `Double`, `LocalDate`, `BigDecimal` son todos inmutables en Java.

---

## 9. Encapsulamiento de colecciones y arrays

Un error sutil pero importante: si tienes un atributo que es un array u objeto mutable (como `ArrayList`), un getter que devuelve la referencia directa rompe el encapsulamiento porque quien recibe la referencia puede modificar el objeto interno.

### El problema: la referencia directa expone el interior

```java
public class Equipo {
    private List<String> miembros;

    public Equipo() {
        miembros = new ArrayList<>();
    }

    public void agregarMiembro(String nombre) {
        if (nombre != null && !nombre.isEmpty()) {
            miembros.add(nombre);
        }
    }

    // ✗ MAL: devuelve la referencia directa
    public List<String> getMiembros() {
        return miembros;  // Quien recibe esto puede modificar la lista interna
    }
}

// Problema:
Equipo e = new Equipo();
e.agregarMiembro("Ana");
e.agregarMiembro("Luis");

List<String> lista = e.getMiembros();
lista.clear();  // ¡Vacía la lista INTERNA del equipo sin pasar por ningún método!
lista.add("Intruso");

System.out.println(e.getMiembros());  // [Intruso] — estado comprometido
```

### La solución: devolver una copia defensiva

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Equipo {
    private List<String> miembros;

    public Equipo() {
        miembros = new ArrayList<>();
    }

    public void agregarMiembro(String nombre) {
        if (nombre != null && !nombre.isEmpty()) {
            miembros.add(nombre.trim());
        }
    }

    public boolean eliminarMiembro(String nombre) {
        return miembros.remove(nombre);
    }

    // ✓ BIEN opción 1: copia defensiva
    public List<String> getMiembros() {
        return new ArrayList<>(miembros);  // Copia independiente
    }

    // ✓ BIEN opción 2: lista inmutable (no se puede modificar)
    public List<String> getMiembrosInmutables() {
        return Collections.unmodifiableList(miembros);
    }

    public int getCantidadMiembros() { return miembros.size(); }
}

// Ahora es seguro:
Equipo e = new Equipo();
e.agregarMiembro("Ana");

List<String> copia = e.getMiembros();
copia.clear();  // Solo borra la COPIA, no el interior del equipo

System.out.println(e.getCantidadMiembros());  // 1 — el equipo está intacto
```

### Lo mismo aplica para arrays

```java
public class CalendarioExamenes {
    private int[] fechas;  // Días del mes con exámenes

    public CalendarioExamenes(int[] fechas) {
        // ✓ Copia defensiva en el constructor también
        this.fechas = Arrays.copyOf(fechas, fechas.length);
    }

    // ✗ MAL: expone el array interno
    // public int[] getFechas() { return fechas; }

    // ✓ BIEN: devuelve una copia
    public int[] getFechas() {
        return Arrays.copyOf(fechas, fechas.length);
    }

    public int getCantidadExamenes() { return fechas.length; }
}
```

---

## 10. El patrón JavaBeans

**JavaBeans** es una convención estándar de Java para crear clases reutilizables. Muchos frameworks (Spring, Hibernate, Jackson) dependen de esta convención para funcionar automáticamente.

### Reglas del estándar JavaBeans

1. La clase debe ser `public`.
2. Debe tener un **constructor sin parámetros público**.
3. Todos los atributos deben ser **`private`**.
4. Cada atributo debe tener **getter y setter públicos** con la nomenclatura estándar.
5. La clase debe implementar `Serializable` (en muchos contextos).

```java
import java.io.Serializable;

public class PersonaBean implements Serializable {
    // 1. Atributos privados
    private String nombre;
    private String apellido;
    private int edad;
    private String email;

    // 2. Constructor sin parámetros (obligatorio en JavaBeans)
    public PersonaBean() { }

    // 3. Constructor de conveniencia (opcional)
    public PersonaBean(String nombre, String apellido, int edad, String email) {
        this.nombre   = nombre;
        this.apellido = apellido;
        this.edad     = edad;
        this.email    = email;
    }

    // 4. Getters
    public String getNombre()   { return nombre; }
    public String getApellido() { return apellido; }
    public int getEdad()        { return edad; }
    public String getEmail()    { return email; }

    // 5. Setters
    public void setNombre(String nombre)     { this.nombre = nombre; }
    public void setApellido(String apellido) { this.apellido = apellido; }
    public void setEdad(int edad)            { this.edad = edad; }
    public void setEmail(String email)       { this.email = email; }

    @Override
    public String toString() {
        return String.format("Persona{nombre='%s %s', edad=%d, email='%s'}",
            nombre, apellido, edad, email);
    }
}
```

### Por qué lo usan los frameworks

Frameworks como Spring Boot pueden automáticamente:
- Crear objetos usando el constructor vacío.
- Leer JSON y asignar valores usando los setters.
- Serializar objetos a JSON leyendo los getters.

```java
// Spring/Jackson puede convertir automáticamente este JSON:
// {"nombre":"Ana","apellido":"García","edad":28,"email":"ana@email.com"}
// en un objeto PersonaBean usando reflexión + getters/setters
```

---

## 11. Buenas prácticas de encapsulamiento

### 11.1 Diseña la interfaz antes que la implementación

Antes de escribir código, pregúntate:
- ¿Qué operaciones necesita poder hacer quien use esta clase?
- ¿Qué información necesita poder consultar?
- ¿Qué NO debe poder modificar directamente?

```java
// Diseño de la interfaz pública de CuentaBancaria:
// ✓ depositar(monto)
// ✓ retirar(monto)
// ✓ transferir(destino, monto)
// ✓ getSaldo() — consultar
// ✓ getTitular() — consultar
// ✓ getHistorial() — consultar (lista inmutable)
// ✗ setSaldo() — no debe existir: el saldo solo cambia por operaciones controladas
```

### 11.2 Principio de mínimo acceso

Dale al código el nivel de acceso más restrictivo posible que le permita funcionar:

```java
public class Procesador {
    // Solo pública la interfaz principal
    public ResultadoProcesado procesar(DatosEntrada datos) {
        DatosValidados validados = validar(datos);    // private
        DatosTrans transformados = transformar(validados); // private
        return formatear(transformados);              // private
    }

    private DatosValidados validar(DatosEntrada datos) { ... }
    private DatosTrans transformar(DatosValidados datos) { ... }
    private ResultadoProcesado formatear(DatosTrans datos) { ... }
}
```

### 11.3 Usar métodos en vez de acceso directo entre atributos

Incluso dentro de la misma clase, es buena práctica usar los setters para aprovechar la validación:

```java
public class Temperatura {
    private double celsius;

    public void setCelsius(double celsius) {
        if (celsius < -273.15)
            throw new IllegalArgumentException("Temperatura bajo el cero absoluto.");
        this.celsius = celsius;
    }

    public void setFahrenheit(double fahrenheit) {
        setCelsius((fahrenheit - 32) * 5.0 / 9.0);  // Usa el setter con validación
    }

    public void setKelvin(double kelvin) {
        setCelsius(kelvin - 273.15);  // Usa el setter con validación
    }
}
```

### 11.4 Documenta la interfaz pública con Javadoc

```java
/**
 * Representa una cuenta de ahorro con control de saldo y validaciones.
 */
public class CuentaAhorro {

    /**
     * Deposita dinero en la cuenta.
     *
     * @param monto Cantidad a depositar. Debe ser mayor que cero.
     * @throws IllegalArgumentException si el monto es cero o negativo.
     */
    public void depositar(double monto) {
        if (monto <= 0)
            throw new IllegalArgumentException("El monto debe ser mayor que cero.");
        this.saldo += monto;
    }

    /**
     * Intenta retirar dinero de la cuenta.
     *
     * @param monto Cantidad a retirar. Debe ser mayor que cero y no exceder el saldo.
     * @return true si el retiro fue exitoso, false si el saldo es insuficiente.
     */
    public boolean retirar(double monto) {
        if (monto <= 0 || monto > saldo) return false;
        saldo -= monto;
        return true;
    }
}
```

### 11.5 Encapsulamiento ≠ getters y setters para todo

Un error común es pensar que encapsular significa agregar getter y setter para cada atributo. Eso no es encapsulamiento: es simplemente hacer los atributos públicos con más código.

```java
// ✗ MAL: getters y setters sin pensar, encapsulamiento falso
public class Punto {
    private double x;
    private double y;

    public double getX() { return x; }
    public void setX(double x) { this.x = x; }  // Sin validación: ¿para qué private?
    public double getY() { return y; }
    public void setY(double y) { this.y = y; }   // Sin validación: ¿para qué private?
}

// ✓ BIEN: piensa qué operaciones son significativas
public class Punto {
    private double x;
    private double y;

    public Punto(double x, double y) { this.x = x; this.y = y; }

    public double getX() { return x; }
    public double getY() { return y; }
    public double distanciaAlOrigen() { return Math.sqrt(x*x + y*y); }
    public Punto trasladar(double dx, double dy) { return new Punto(x+dx, y+dy); }
    // Sin setX y setY: un punto matemático no "cambia" su coordenada
}
```

---

## 12. Errores comunes con encapsulamiento

### Error 1: Atributos públicos sin control

```java
// ✗ El código externo puede poner cualquier valor sin validación
public class Temperatura {
    public double celsius;  // Sin private: sin control
}

Temperatura t = new Temperatura();
t.celsius = -999999;  // Estado imposible, sin error
```

### Error 2: Getter que devuelve referencia a objeto mutable interno

```java
public class Pedido {
    private List<String> articulos = new ArrayList<>();

    // ✗ MAL: expone la lista interna
    public List<String> getArticulos() { return articulos; }
}

Pedido p = new Pedido();
p.getArticulos().add("Artículo trampa");  // Modifica el interior directamente
```

**Solución:**

```java
public List<String> getArticulos() {
    return Collections.unmodifiableList(articulos);
}
```

### Error 3: Setter sin validación

```java
public void setEdad(int edad) {
    this.edad = edad;  // ✗ Sin validación: ¿de qué sirve el private entonces?
}

// Con esto puedo hacer: persona.setEdad(-100) sin ningún error
```

### Error 4: Modificar el atributo directamente en vez del setter dentro de la misma clase

```java
public class Producto {
    private double precio;

    public void setPrecio(double precio) {
        if (precio >= 0) this.precio = precio;
    }

    public void aplicarDescuento(double porcentaje) {
        // ✗ Bypassa la validación del setter
        this.precio = this.precio - (this.precio * porcentaje / 100);
        // ¿Y si el resultado es negativo?

        // ✓ Usa el setter para aprovechar la validación
        setPrecio(this.precio * (1 - porcentaje / 100));
    }
}
```

### Error 5: Constructor que no valida (confiar en el setter solo para updates)

```java
public class Empleado {
    private int edad;

    // ✗ El constructor no valida, aunque el setter sí lo hace
    public Empleado(int edad) {
        this.edad = edad;  // Se salta la validación del setter
    }

    public void setEdad(int edad) {
        if (edad < 18 || edad > 70)
            throw new IllegalArgumentException("Edad laboral inválida.");
        this.edad = edad;
    }

    // ✓ El constructor llama al setter para reusar la validación
    public Empleado(int edad) {
        setEdad(edad);  // La validación se aplica también al crear el objeto
    }
}
```

### Error 6: Copia defensiva olvidada en el constructor

```java
public class Horario {
    private int[] horas;

    // ✗ MAL: guarda la referencia al array original
    public Horario(int[] horas) {
        this.horas = horas;  // Si alguien modifica el array original, cambia el horario
    }

    // ✓ BIEN: copia defensiva
    public Horario(int[] horas) {
        this.horas = Arrays.copyOf(horas, horas.length);
    }
}
```

---

## 13. Ejercicios prácticos

### Ejercicio 1 — Refactorizar clase sin encapsulamiento (Nivel: Básico)

Se te da la siguiente clase mal diseñada:

```java
public class Vehiculo {
    public String marca;
    public String modelo;
    public int año;
    public double velocidadActual;
    public double velocidadMaxima;
    public boolean encendido;
    public double combustible;  // 0.0 a 100.0 (porcentaje)
}
```

Refactorízala aplicando encapsulamiento completo:
- Todos los atributos deben ser `private`
- Agrega un constructor completo con validaciones
- Getter para todos los atributos
- Setter solo para los que tienen sentido (piensa cuáles no deben cambiarse)
- Métodos de acción: `encender()`, `apagar()`, `acelerar(double kmh)`, `frenar(double kmh)`, `recargarCombustible(double litros)`
- Todas las validaciones necesarias (no puedes acelerar si está apagado, no más de la velocidad máxima, etc.)
- `toString()` completo

---

### Ejercicio 2 — Clase Temperatura con conversiones (Nivel: Básico-Medio)

Crea `Temperatura.java` donde el estado interno se almacena siempre en Celsius, pero se puede establecer y consultar en cualquier escala.

- Atributo privado: `double celsius`
- Límite inferior: -273.15°C (cero absoluto)
- Constructor con valor en Celsius
- Métodos: `getCelsius()`, `getFahrenheit()`, `getKelvin()`
- Setters: `setCelsius(double)`, `setFahrenheit(double)`, `setKelvin(double)` (todos con validación del cero absoluto)
- `esCriogenica()` → boolean (menor a -150°C)
- `esFusion()` → boolean (mayor a 100°C a nivel del mar)
- Fábrica estática: `static Temperatura desdeFahrenheit(double f)` y `static Temperatura desdeKelvin(double k)`
- `toString()` que muestre las tres escalas

---

### Ejercicio 3 — Sistema de inventario (Nivel: Medio)

Crea `Inventario.java` que encapsule una lista de `Producto`s.

`Producto` tiene: `codigo` (auto-generado), `nombre`, `precio`, `cantidad`.

`Inventario` tiene:
- `private List<Producto> productos`
- `agregarProducto(Producto p)` — verifica que el código no esté repetido
- `eliminarProducto(int codigo)` — retorna boolean
- `buscarPorCodigo(int codigo)` — retorna Producto o null
- `buscarPorNombre(String nombre)` — retorna lista (copia defensiva)
- `actualizarPrecio(int codigo, double nuevoPrecio)` — con validación
- `actualizarCantidad(int codigo, int cantidad)` — con validación
- `getProductos()` — retorna lista inmutable
- `getTotalProductos()`, `getValorTotalInventario()`
- `getProductosConStockBajo(int umbral)` — retorna lista de productos con cantidad <= umbral
- `toString()` — resumen del inventario

---

### Ejercicio 4 — Clase Persona inmutable (Nivel: Medio)

Crea una versión inmutable de `Persona` que tenga: `nombre`, `apellido`, `fechaNacimiento` (LocalDate), `dni` (String).

- Todos los atributos `private final`
- Un solo constructor que valide todos los campos
- Solo getters, sin setters
- Método calculado: `getEdad()` (calcula la edad actual)
- Método calculado: `getNombreCompleto()`
- Método calculado: `esMayorDeEdad()`
- Método que "simula" cambio retornando un nuevo objeto: `conNombre(String nuevoNombre)` → retorna nueva Persona con el nuevo nombre manteniendo el resto
- `equals()` basado en DNI
- `hashCode()` basado en DNI
- `toString()`

---

### Ejercicio 5 — CuentaBancaria con historial (Nivel: Avanzado)

Crea `CuentaBancaria.java` completamente encapsulada con historial de transacciones:

```
Atributos privados:
- numeroCuenta (String, auto-generado, formato "CTA-XXXXXX")
- titular (String)
- saldo (double)
- List<String> historial (registro de todas las operaciones)
- boolean bloqueada (si hay 3 retiros fallidos consecutivos)
- int intentosFallidos (contador de retiros fallidos)
- static int contadorCuentas
```

Métodos:
- `depositar(double monto)` — valida, actualiza saldo, agrega al historial
- `retirar(double monto)` — valida, controla bloqueo, agrega al historial
- `transferir(CuentaBancaria destino, double monto)` — usa retirar/depositar
- `desbloquear(String codigoAdmin)` — solo si el código es "ADMIN2024"
- `getHistorial()` — retorna lista inmutable
- `imprimirEstadoCuenta()` — muestra todas las transacciones con formato
- `isBloqueada()`, `getSaldo()`, `getTitular()`, `getNumeroCuenta()`
- `static int getTotalCuentas()`

---

### Ejercicio 6 — Diseño propio (Nivel: Avanzado)

Diseña y desarrolla una clase `MatrizNumerica` que encapsule una matriz de doubles (bidimensional). La clase debe:

- Almacenar internamente un `double[][] datos` privado
- Constructor: `MatrizNumerica(int filas, int columnas)` — inicializa en ceros
- Constructor: `MatrizNumerica(double[][] datos)` — copia defensiva
- `setValor(int fila, int col, double valor)` — con validación de índices
- `getValor(int fila, int col)` — con validación de índices
- `getFilas()`, `getColumnas()`
- `getDatos()` — retorna copia defensiva profunda (deep copy)
- `sumar(MatrizNumerica otra)` — retorna nueva matriz suma
- `escalar(double factor)` — retorna nueva matriz multiplicada por factor
- `transponer()` — retorna nueva matriz transpuesta
- `getMaximo()`, `getMinimo()`, `getPromedio()`
- `toString()` — imprime la matriz con formato de tabla

---

## 14. Resumen del tema

### El patrón completo de encapsulamiento

```java
public class MiClase {

    // 1. Atributos siempre PRIVATE
    private TipoA atributo1;
    private TipoB atributo2;
    private boolean flag;

    // 2. Constructor que valida usando setters
    public MiClase(TipoA a, TipoB b) {
        setAtributo1(a);  // Reutiliza la validación del setter
        setAtributo2(b);
    }

    // 3. Getters: get + NombreAtributo (is + Nombre para boolean)
    public TipoA getAtributo1() { return atributo1; }
    public TipoB getAtributo2() { return atributo2; }
    public boolean isFlag()     { return flag; }

    // 4. Setters: con VALIDACIÓN SIEMPRE
    public void setAtributo1(TipoA valor) {
        if (/* validación */) throw new IllegalArgumentException("Mensaje claro");
        this.atributo1 = valor;
    }

    public void setAtributo2(TipoB valor) {
        // validación
        this.atributo2 = valor;
    }

    // 5. toString para depuración
    @Override
    public String toString() {
        return "MiClase{atributo1=" + atributo1 + ", atributo2=" + atributo2 + "}";
    }
}
```

### Modificadores de acceso en resumen

| Modificador | ¿Cuándo usarlo? |
|-------------|-----------------|
| `private` | Atributos (siempre) y métodos auxiliares internos |
| `protected` | Atributos/métodos que las subclases necesitan acceder |
| *(sin modificador)* | Clases auxiliares dentro del mismo paquete |
| `public` | Métodos de la interfaz pública (getters, setters, acciones principales) |

### Reglas de oro del encapsulamiento

| Situación | Regla |
|-----------|-------|
| Atributos | Siempre `private`; sin excepción para buenas clases |
| Getters | Proporcionar solo los que el exterior necesita conocer |
| Setters | Proporcionar solo para los atributos que deben cambiar tras la creación |
| Validación | Siempre en el setter; el constructor llama al setter para reutilizarla |
| Listas/arrays | Devolver copia defensiva o `Collections.unmodifiableList()` |
| Objetos inmutables | Usar `final` en atributos y eliminar setters cuando el estado no debe cambiar |
| `this.atributo` | Usar `this.` cuando el parámetro tiene el mismo nombre que el atributo |
| Documentación | Javadoc en todos los métodos públicos, especialmente getters y setters |

---

## Próximo tema

**Tema 9: Herencia**

Aprenderás cómo una clase puede heredar atributos y métodos de otra usando `extends`, cómo funciona la jerarquía de clases, qué es el constructor de la clase padre (`super()`), cómo sobreescribir métodos con `@Override`, el concepto de método final y la clase `Object` como raíz de toda la jerarquía Java.

---

*Curso de Java — Nivel 2: Programación Orientada a Objetos*
*Tema 8 de 12*
*Creado con fines educativos*
