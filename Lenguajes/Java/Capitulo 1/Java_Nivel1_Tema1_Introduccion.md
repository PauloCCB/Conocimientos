# Curso de Java — Nivel 1: Fundamentos
## Tema 1: Introducción a Java

> **Nivel:** Principiante | **Prerrequisitos:** Ninguno | **Duración estimada:** 2–3 horas

---

## Tabla de contenidos

1. [¿Qué es Java?](#1-qué-es-java)
2. [Historia y evolución de Java](#2-historia-y-evolución-de-java)
3. [Características principales del lenguaje](#3-características-principales-del-lenguaje)
4. [Arquitectura de Java: JDK, JRE y JVM](#4-arquitectura-de-java-jdk-jre-y-jvm)
5. [¿Cómo funciona Java internamente?](#5-cómo-funciona-java-internamente)
6. [Instalación del entorno de desarrollo](#6-instalación-del-entorno-de-desarrollo)
7. [Estructura de un programa Java](#7-estructura-de-un-programa-java)
8. [Tu primer programa: Hola Mundo](#8-tu-primer-programa-hola-mundo)
9. [Compilar y ejecutar desde la terminal](#9-compilar-y-ejecutar-desde-la-terminal)
10. [Variaciones de impresión en pantalla](#10-variaciones-de-impresión-en-pantalla)
11. [Comentarios en Java](#11-comentarios-en-java)
12. [Reglas fundamentales del lenguaje](#12-reglas-fundamentales-del-lenguaje)
13. [Errores comunes del principiante](#13-errores-comunes-del-principiante)
14. [Ejercicios prácticos](#14-ejercicios-prácticos)
15. [Resumen del tema](#15-resumen-del-tema)

---

## 1. ¿Qué es Java?

Java es un **lenguaje de programación de propósito general**, orientado a objetos, fuertemente tipado y diseñado para ser portátil entre plataformas. Fue creado con la filosofía:

> *"Write Once, Run Anywhere"* — Escribe una vez, ejecuta en cualquier lugar.

Esto significa que el código que escribes en tu computadora con Windows puede ejecutarse sin cambios en una Mac, en Linux, en un servidor, en un teléfono Android o incluso en una tarjeta inteligente.

Java no es solo un lenguaje: es también un **ecosistema completo** que incluye un conjunto de herramientas de desarrollo, una máquina virtual, miles de librerías y un enorme mercado laboral.

### ¿Dónde se usa Java hoy en día?

- Aplicaciones empresariales y bancarias (la mayoría de los bancos del mundo usan Java)
- Desarrollo de aplicaciones Android (históricamente, hoy comparte protagonismo con Kotlin)
- Aplicaciones web del lado del servidor (con frameworks como Spring)
- Sistemas de comercio electrónico a gran escala (Amazon, eBay en sus inicios)
- Herramientas científicas y de análisis de datos
- Sistemas embebidos y tarjetas inteligentes (Java Card)
- Videojuegos (Minecraft fue desarrollado en Java)
- Aplicaciones de la NASA y agencias espaciales

---

## 2. Historia y evolución de Java

Conocer la historia de Java te ayuda a entender por qué el lenguaje tiene ciertas características y decisiones de diseño.

### Línea de tiempo

| Año  | Hito |
|------|------|
| 1991 | James Gosling y el equipo "Green" de Sun Microsystems comienzan a trabajar en un lenguaje para dispositivos electrónicos del hogar. El proyecto se llamaba originalmente "Oak". |
| 1995 | Sun Microsystems lanza Java 1.0 al público. El lenguaje cambia de nombre de "Oak" a "Java" (por el café de Java, Indonesia). El lema es "Write Once, Run Anywhere". |
| 1996 | Se lanza el JDK 1.0 con 212 clases y interfaces. Netscape Navigator incluye soporte para Java Applets, lo que populariza el lenguaje en la web. |
| 1997 | Java 1.1 introduce los inner classes, la API de reflexión y mejoras en JDBC. |
| 1998 | Java 2 (J2SE 1.2) es un salto enorme: introduce el framework de colecciones, Swing para interfaces gráficas y el compilador JIT (Just-In-Time). |
| 2004 | Java 5 (J2SE 5.0) trae genéricos, enumeraciones, anotaciones, autoboxing y el bucle for-each. Es considerado uno de los mayores saltos del lenguaje. |
| 2006 | Sun Microsystems libera Java como software de código abierto bajo la licencia GPL. Nace OpenJDK. |
| 2009 | Oracle Corporation adquiere Sun Microsystems y con ello el control de Java. |
| 2014 | Java 8 introduce las expresiones lambda, la API Stream y la nueva API de fechas. Es una revolución en la forma de escribir Java. |
| 2017 | A partir de Java 9, Oracle adopta un ciclo de lanzamiento cada 6 meses. Java 9 introduce el sistema de módulos (Project Jigsaw). |
| 2018 | Java 11 se convierte en la primera versión LTS (Long-Term Support) del nuevo ciclo. |
| 2021 | Java 17 se convierte en la versión LTS más adoptada en producción. |
| 2023 | Java 21 trae Virtual Threads (Project Loom), mejorando masivamente la concurrencia. |

### ¿Qué es una versión LTS?

LTS significa *Long-Term Support* (Soporte a largo plazo). Oracle garantiza actualizaciones de seguridad y correcciones para estas versiones durante años. Para aprender y para proyectos de producción, se recomienda usar versiones LTS.

Las versiones LTS actuales son: **Java 8**, **Java 11**, **Java 17** y **Java 21**.

Para este curso usaremos **Java 21** (o Java 17 si tu sistema no soporta la versión más nueva).

---

## 3. Características principales del lenguaje

### 3.1 Orientado a Objetos

Java está diseñado desde cero bajo el paradigma de la Programación Orientada a Objetos (POO). Todo en Java existe dentro de una clase. Los cuatro pilares de la POO que Java implementa son:

- **Encapsulamiento:** Los datos y el comportamiento se agrupan en unidades llamadas objetos, y se controla el acceso a sus partes internas.
- **Herencia:** Una clase puede heredar características de otra, evitando duplicar código.
- **Polimorfismo:** Un mismo método puede comportarse de manera diferente según el objeto que lo ejecute.
- **Abstracción:** Se trabaja con conceptos de alto nivel sin preocuparse por los detalles de implementación interna.

### 3.2 Fuertemente tipado

En Java, cada variable debe tener un tipo de dato declarado y ese tipo no cambia. Esto evita muchos errores en tiempo de ejecución porque el compilador los detecta antes de que el programa corra.

```java
int edad = 25;       // Correcto
edad = "hola";       // Error de compilación: no puedes asignar texto a un int
```

### 3.3 Portabilidad (independiente de la plataforma)

Gracias a la JVM, el bytecode compilado de Java se ejecuta en cualquier sistema que tenga una JVM instalada. No es necesario recompilar el código para cada sistema operativo.

### 3.4 Robusto y seguro

Java fue diseñado para minimizar errores:
- La gestión de memoria es automática (Garbage Collector)
- No existe la aritmética de punteros directa (lo que elimina toda una categoría de errores)
- El sistema de tipos es estricto
- Hay verificación del bytecode antes de la ejecución
- El modelo de seguridad de la JVM actúa como una caja de arena (sandbox)

### 3.5 Multihilo (Multithreading)

Java tiene soporte nativo para la programación concurrente, permitiendo que múltiples tareas se ejecuten simultáneamente dentro de un mismo programa.

### 3.6 Rendimiento

Aunque Java no es tan rápido como C o C++ (porque pasa por la JVM), el compilador JIT (Just-In-Time) optimiza el bytecode en tiempo de ejecución, lo que hace que el rendimiento de Java sea muy competitivo en aplicaciones empresariales.

---

## 4. Arquitectura de Java: JDK, JRE y JVM

Este es el concepto más importante para entender cómo funciona Java. Mucha gente instala Java sin entender qué está instalando. Vamos a desmitificarlo completamente.

La arquitectura de Java se organiza como tres capas concéntricas: la JVM es la más interna, el JRE la envuelve, y el JDK es la más externa y completa.

```
┌─────────────────────────────────────────────────────┐
│                      JDK                            │
│  (Java Development Kit — Kit de Desarrollo)         │
│                                                     │
│   javac   jdb   javadoc   jar   jshell   ...        │
│                                                     │
│   ┌───────────────────────────────────────────┐     │
│   │                  JRE                      │     │
│   │  (Java Runtime Environment)               │     │
│   │                                           │     │
│   │   Librerías estándar (java.lang, etc.)    │     │
│   │   APIs de Java                            │     │
│   │                                           │     │
│   │   ┌───────────────────────────────────┐   │     │
│   │   │              JVM                  │   │     │
│   │   │  (Java Virtual Machine)           │   │     │
│   │   │                                   │   │     │
│   │   │  Class Loader                     │   │     │
│   │   │  Bytecode Verifier                │   │     │
│   │   │  Interpreter                      │   │     │
│   │   │  JIT Compiler                     │   │     │
│   │   │  Garbage Collector                │   │     │
│   │   │  Runtime Data Areas               │   │     │
│   │   └───────────────────────────────────┘   │     │
│   └───────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────┘
```

---

### 4.1 JVM — Java Virtual Machine (Máquina Virtual de Java)

La JVM es el **corazón de Java** y la razón por la que el lenguaje es portátil. Es un programa que simula ser una computadora dentro de tu computadora real. Su trabajo es tomar el bytecode compilado de Java y traducirlo a instrucciones nativas que tu procesador pueda entender.

**¿Por qué es importante la JVM?**

Sin la JVM, el mismo programa de Java no podría ejecutarse en distintos sistemas operativos sin ser recompilado. Con la JVM, el ciclo es:

1. Tú escribes código Java (`.java`)
2. El compilador lo convierte a bytecode (`.class`)
3. La JVM de cada sistema operativo traduce ese bytecode a instrucciones nativas

Existen JVMs para Windows, macOS, Linux, Solaris, ARM, etc. El bytecode es **el mismo** en todos los casos; solo la JVM es diferente.

#### Componentes internos de la JVM

**a) Class Loader (Cargador de clases)**

El Class Loader es el responsable de cargar los archivos `.class` en la memoria de la JVM. Se encarga de tres pasos:
- *Loading:* Encuentra y carga el archivo `.class` en memoria.
- *Linking:* Verifica que el bytecode sea válido, reserva espacio para variables estáticas y resuelve las referencias entre clases.
- *Initialization:* Ejecuta los bloques estáticos de inicialización y asigna valores iniciales a las variables estáticas.

**b) Bytecode Verifier (Verificador de bytecode)**

Antes de ejecutar el código, la JVM lo verifica para asegurarse de que:
- No accede a zonas de memoria no autorizadas
- No manipula tipos de datos de manera incorrecta
- No viola las reglas de acceso (private, public, etc.)
- El código no puede dañar el sistema

Esta es la razón por la que Java se considera un lenguaje seguro.

**c) Interpreter (Intérprete)**

El intérprete lee el bytecode instrucción por instrucción y lo ejecuta. Es el método más simple pero también el más lento, ya que cada instrucción se traduce cada vez que se necesita.

**d) JIT Compiler (Compilador Just-In-Time)**

El compilador JIT es la optimización más importante para el rendimiento de Java. En lugar de interpretar el bytecode línea a línea cada vez, el JIT observa qué partes del código se ejecutan frecuentemente ("hotspots") y las compila directamente a código nativo de la máquina. La próxima vez que esas partes se ejecuten, ya no necesitan interpretación: se ejecutan directamente como código nativo, a velocidad casi igual a C++.

Por eso los programas Java tienden a hacerse más rápidos con el tiempo de ejecución: el JIT aprende qué optimizar.

**e) Garbage Collector (Recolector de basura)**

En lenguajes como C o C++, el programador debe reservar y liberar memoria manualmente. Olvidar liberar memoria causa fugas de memoria (*memory leaks*) que pueden colapsar un sistema.

Java elimina este problema con el Garbage Collector: un proceso que se ejecuta automáticamente en segundo plano, detecta los objetos que ya no están siendo usados por el programa y libera su memoria automáticamente.

Ventajas:
- El programador se enfoca en la lógica del negocio, no en la gestión de memoria
- Se eliminan los errores de acceso a memoria inválida
- Previene fugas de memoria

Desventaja:
- El Garbage Collector puede causar pequeñas pausas en la ejecución (aunque las JVMs modernas los minimizan enormemente con técnicas como G1GC y ZGC).

**f) Runtime Data Areas (Áreas de datos en tiempo de ejecución)**

La JVM organiza la memoria en varias zonas:

- **Heap (Montículo):** Aquí se almacenan todos los objetos creados con `new`. Es la zona más grande y donde actúa el Garbage Collector.
- **Stack (Pila):** Cada hilo tiene su propio stack. Aquí se almacenan las variables locales y las llamadas a métodos. Cuando un método termina, su marco se elimina del stack automáticamente.
- **Method Area:** Almacena la información de las clases cargadas (nombres, métodos, variables estáticas, bytecode).
- **Program Counter Register:** Lleva el registro de qué instrucción se está ejecutando en cada hilo.
- **Native Method Stack:** Para métodos escritos en otros lenguajes (como C) que Java puede llamar mediante JNI.

---

### 4.2 JRE — Java Runtime Environment (Entorno de Ejecución de Java)

El JRE es la capa que envuelve a la JVM. Contiene todo lo necesario para **ejecutar** programas Java, pero no para **desarrollarlos**.

El JRE incluye:
- La JVM (descrita arriba)
- Las **librerías estándar de Java** (también llamadas Java Class Library o Java API)
- Archivos de configuración y recursos necesarios para la ejecución

#### Las librerías estándar de Java

Son miles de clases ya escritas que puedes usar en tus programas sin reinventar la rueda. Se organizan en paquetes (packages):

| Paquete | Contenido |
|---------|-----------|
| `java.lang` | Clases fundamentales: String, Math, System, Object, etc. Se importa automáticamente en todo programa. |
| `java.util` | Colecciones (List, Map, Set), utilidades de fecha, generadores de números aleatorios, etc. |
| `java.io` | Lectura y escritura de archivos y flujos de datos. |
| `java.net` | Comunicación por red: sockets, URLs, HTTP. |
| `java.math` | Operaciones matemáticas de alta precisión. |
| `java.time` | API moderna de fechas y tiempos (desde Java 8). |
| `java.sql` | Comunicación con bases de datos. |

**¿Quién instala el JRE?**

Históricamente, los usuarios finales instalaban el JRE para ejecutar aplicaciones Java en sus computadoras. Hoy en día, con la popularización de los runtimes embebidos, muchas aplicaciones Java incluyen su propio JRE, por lo que el usuario ya no necesita instalarlo manualmente.

---

### 4.3 JDK — Java Development Kit (Kit de Desarrollo de Java)

El JDK es la capa más externa y completa. Contiene todo lo del JRE, más las **herramientas necesarias para desarrollar** programas Java.

**El JDK incluye:**

| Herramienta | Nombre del ejecutable | Función |
|-------------|-----------------------|---------|
| Compilador | `javac` | Convierte código fuente `.java` en bytecode `.class` |
| Ejecutador | `java` | Lanza la JVM y ejecuta programas |
| Depurador | `jdb` | Permite ejecutar el programa paso a paso para encontrar errores |
| Generador de docs | `javadoc` | Genera documentación HTML a partir de los comentarios del código |
| Empaquetador | `jar` | Crea archivos JAR (Java ARchive) que empaquetan múltiples clases |
| Visor de clases | `javap` | Muestra el bytecode descompilado de un archivo `.class` |
| Consola interactiva | `jshell` | REPL de Java, permite ejecutar código Java de forma interactiva (desde Java 9) |
| Analizador de rendimiento | `jvisualvm` / `jcmd` | Monitorea y analiza el rendimiento de aplicaciones en ejecución |

**¿Quién instala el JDK?**

Todo programador que quiera escribir, compilar y ejecutar código Java. Si eres desarrollador, necesitas el JDK, no solo el JRE.

---

## 5. ¿Cómo funciona Java internamente?

Veamos el recorrido completo del código desde que lo escribes hasta que aparece en pantalla:

```
PASO 1: Escribes el código fuente
─────────────────────────────────
  HolaMundo.java
  (texto legible por humanos)


PASO 2: Compilación con javac
─────────────────────────────────
  $ javac HolaMundo.java
  
  El compilador javac:
  - Analiza la sintaxis del código
  - Verifica tipos de datos
  - Detecta errores de compilación
  - Genera el archivo .class


PASO 3: Se genera el bytecode
─────────────────────────────────
  HolaMundo.class
  (instrucciones intermedias, no es código de máquina)
  
  El bytecode es independiente del sistema operativo.
  El mismo .class funciona en Windows, Mac y Linux.


PASO 4: Ejecución con java
─────────────────────────────────
  $ java HolaMundo
  
  La JVM:
  1. Class Loader carga HolaMundo.class en memoria
  2. Bytecode Verifier verifica que sea seguro
  3. Interpreter/JIT ejecuta las instrucciones
  4. Garbage Collector gestiona la memoria


PASO 5: Resultado en pantalla
─────────────────────────────────
  ¡Hola Mundo!
```

### La diferencia entre compilación e interpretación

- **Lenguajes compilados** (C, C++): El código fuente se convierte directamente a código de máquina para un sistema operativo específico. Es muy rápido pero no es portable: debes compilar una versión para Windows, otra para Mac, otra para Linux.

- **Lenguajes interpretados** (Python, JavaScript en el navegador): El intérprete lee y ejecuta el código fuente línea por línea en tiempo real. Es portable pero más lento.

- **Java: el mejor de ambos mundos:** El código se compila a bytecode (compilación) y la JVM interpreta/JIT-compila ese bytecode (interpretación optimizada). Obtienes portabilidad sin sacrificar demasiado rendimiento.

---

## 6. Instalación del entorno de desarrollo

### 6.1 Instalar el JDK

**Opción recomendada: Eclipse Temurin (Adoptium)**

Es la distribución gratuita, de código abierto y más usada de OpenJDK.

1. Ve a: https://adoptium.net
2. Selecciona Java 21 (LTS)
3. Descarga el instalador para tu sistema operativo
4. Sigue el asistente de instalación

**Otras distribuciones gratuitas del JDK:**

| Distribución | Mantenida por | URL |
|--------------|---------------|-----|
| Eclipse Temurin | Eclipse Foundation | adoptium.net |
| Oracle JDK | Oracle | oracle.com/java |
| Amazon Corretto | Amazon | aws.amazon.com/corretto |
| Microsoft Build of OpenJDK | Microsoft | microsoft.com/openjdk |
| Azul Zulu | Azul Systems | azul.com/downloads |

Todas son básicamente equivalentes para aprender Java.

### 6.2 Verificar la instalación

Abre una terminal (Command Prompt en Windows, Terminal en Mac/Linux) y escribe:

```bash
java --version
```

Deberías ver algo similar a:

```
openjdk 21.0.2 2024-01-16
OpenJDK Runtime Environment Temurin-21.0.2+13 (build 21.0.2+13)
OpenJDK 64-Bit Server VM Temurin-21.0.2+13 (build 21.0.2+13, mixed mode, sharing)
```

Verifica también el compilador:

```bash
javac --version
```

Salida esperada:

```
javac 21.0.2
```

Si alguno de estos comandos falla, puede ser que el JDK no esté en el PATH del sistema. En ese caso, debes agregar la carpeta `bin` del JDK a las variables de entorno del sistema.

### 6.3 Elegir un entorno de desarrollo (IDE)

Un IDE (Integrated Development Environment) es un editor de código especializado que facilita enormemente el desarrollo. Incluye autocompletado, detección de errores en tiempo real, depurador integrado y muchas otras funciones.

#### IntelliJ IDEA Community Edition (Recomendado para principiantes)

**URL:** https://www.jetbrains.com/idea/download (elegir Community, que es gratuita)

Es el IDE más inteligente para Java. Tiene:
- Autocompletado muy potente
- Detección de errores antes de compilar
- Refactoring automático
- Integración con Git y otras herramientas
- Interfaz moderna e intuitiva

Es la herramienta preferida por la mayoría de desarrolladores Java profesionales.

#### Visual Studio Code (Ligero y flexible)

**URL:** https://code.visualstudio.com

Después de instalarlo, instala la extensión "Extension Pack for Java" desde el marketplace. Es una opción más ligera que IntelliJ.

#### Eclipse IDE (Clásico y robusto)

**URL:** https://www.eclipse.org/downloads

El IDE clásico de Java. Es muy usado en entornos empresariales y académicos. Tiene una curva de aprendizaje un poco más pronunciada pero es muy completo.

---

## 7. Estructura de un programa Java

Todo programa Java sigue una estructura básica. Es importante entenderla desde el principio, porque aunque no comprendas cada palabra al 100%, reconocerás el patrón en todos los programas que veas.

```java
// 1. Declaración del paquete (opcional al inicio)
package com.miempresa.miproyecto;

// 2. Importaciones de librerías externas (opcional)
import java.util.Scanner;

// 3. Declaración de la clase (OBLIGATORIO)
public class NombreDeLaClase {

    // 4. Variables de clase (atributos) — opcionales
    // Se declaran aquí, fuera de los métodos

    // 5. Método main — PUNTO DE ENTRADA OBLIGATORIO
    public static void main(String[] args) {

        // 6. Cuerpo del programa: aquí va la lógica
        System.out.println("Este es el cuerpo del programa");

    } // fin del método main

} // fin de la clase
```

### Explicación de cada parte

**`package com.miempresa.miproyecto;`**

Un paquete (package) es una forma de organizar las clases en carpetas lógicas, similar a un sistema de carpetas. Cuando tu proyecto crece y tiene decenas de clases, los paquetes evitan que todo esté mezclado. La convención es usar el nombre de dominio de tu empresa en orden inverso, seguido del nombre del proyecto. Para proyectos pequeños de práctica, puedes omitir el package.

**`import java.util.Scanner;`**

La instrucción `import` permite usar clases de otros paquetes en tu código. `java.lang` se importa automáticamente (por eso puedes usar `String` y `System` sin importar nada), pero para usar otras clases como `Scanner`, debes importarlas explícitamente. Aprenderás más sobre esto en temas siguientes.

**`public class NombreDeLaClase`**

Esta es la declaración de la clase. En Java:
- `public` significa que la clase es accesible desde cualquier lugar.
- `class` es la palabra clave que indica que estamos declarando una clase.
- `NombreDeLaClase` es el identificador. Debe comenzar con mayúscula (convención PascalCase) y el nombre del archivo `.java` debe ser exactamente igual.

**`public static void main(String[] args)`**

Este es el método principal. Es el punto de entrada de cualquier aplicación Java. Cuando ejecutas `java NombreDeLaClase`, la JVM busca exactamente este método para comenzar la ejecución.

- `public`: El método es visible para la JVM desde afuera de la clase.
- `static`: El método pertenece a la clase, no a un objeto. Esto permite que la JVM lo llame sin necesidad de crear un objeto primero.
- `void`: El método no devuelve ningún valor.
- `main`: El nombre especial que reconoce la JVM.
- `String[] args`: Un arreglo de cadenas de texto que permite recibir argumentos desde la línea de comandos. Por ejemplo: `java MiPrograma argumento1 argumento2`. Aunque al principio no lo usarás, debe estar siempre declarado.

Las llaves `{ }` delimitan el bloque de código. Todo lo que esté entre las llaves de `main` se ejecutará cuando corras el programa.

---

## 8. Tu primer programa: Hola Mundo

Por tradición, el primer programa que se escribe en cualquier lenguaje de programación es uno que muestra el texto "Hola Mundo" en pantalla. Es simple, pero te permite verificar que todo el entorno está correctamente instalado y funcionando.

### El código completo

```java
public class HolaMundo {
    public static void main(String[] args) {
        System.out.println("¡Hola Mundo!");
    }
}
```

### Análisis línea por línea

**Línea 1:** `public class HolaMundo {`

Declara una clase pública llamada `HolaMundo`. El archivo debe guardarse con el nombre `HolaMundo.java` (sensible a mayúsculas). La llave `{` abre el cuerpo de la clase.

**Línea 2:** `public static void main(String[] args) {`

Declara el método principal. Esta línea debe escribirse exactamente así, sin cambiar ninguna palabra. Es el punto donde comienza la ejecución del programa. La llave `{` abre el cuerpo del método.

**Línea 3:** `System.out.println("¡Hola Mundo!");`

Esta es la instrucción que hace el trabajo visible: imprime el texto en la pantalla.

- `System`: Es una clase de la librería estándar `java.lang` (siempre disponible).
- `out`: Es un atributo estático de `System` que representa la salida estándar (la pantalla).
- `println`: Es un método que imprime el texto y luego añade un salto de línea.
- `"¡Hola Mundo!"`: Es el texto a imprimir, encerrado entre comillas dobles.
- `;`: El punto y coma al final indica el fin de la instrucción. Es obligatorio.

**Línea 4:** `}`

Cierra el cuerpo del método `main`.

**Línea 5:** `}`

Cierra el cuerpo de la clase `HolaMundo`.

### ¿Por qué es tan verboso?

Si comparas con otros lenguajes, Java parece muy largo para hacer algo tan simple:

```python
# Python
print("¡Hola Mundo!")
```

```java
// Java
public class HolaMundo {
    public static void main(String[] args) {
        System.out.println("¡Hola Mundo!");
    }
}
```

La razón es que Java es un lenguaje orientado a objetos puro y fuertemente estructurado. Toda esa "verbosidad" tiene sentido en programas grandes: la estructura explícita hace que el código sea más fácil de mantener, entender y escalar. Con el tiempo, esa estructura se vuelve natural.

---

## 9. Compilar y ejecutar desde la terminal

### Paso 1: Crear el archivo

Crea un archivo de texto llamado `HolaMundo.java` (cuidado con las mayúsculas) y escribe el código del programa dentro.

### Paso 2: Abrir la terminal en esa carpeta

- **Windows:** Click derecho en la carpeta → "Abrir en terminal" (o usa Command Prompt y navega con `cd`)
- **Mac:** Click derecho en la carpeta → "Nueva Terminal en la carpeta"
- **Linux:** Click derecho → "Abrir terminal aquí"

### Paso 3: Compilar

```bash
javac HolaMundo.java
```

Si no hay errores, el compilador genera el archivo `HolaMundo.class` en la misma carpeta. Si hay errores, los muestra en la terminal con el número de línea donde ocurrieron.

### Paso 4: Ejecutar

```bash
java HolaMundo
```

Nota importante: para compilar usas `javac HolaMundo.java` (con la extensión `.java`), pero para ejecutar usas `java HolaMundo` (sin extensión). Estás ejecutando la clase, no el archivo.

### Salida esperada

```
¡Hola Mundo!
```

### Usando JShell (forma interactiva, Java 9+)

JShell es una herramienta que permite ejecutar código Java directamente, sin necesidad de crear archivos ni clases. Ideal para probar cosas rápidas.

```bash
jshell
```

Dentro de jshell:

```java
jshell> System.out.println("¡Hola desde JShell!")
¡Hola desde JShell!

jshell> int x = 5 + 3
x ==> 8

jshell> /exit
```

---

## 10. Variaciones de impresión en pantalla

Java ofrece varias formas de mostrar información en pantalla. Es importante conocerlas todas desde el principio.

### `System.out.println()` — Imprime con salto de línea

```java
System.out.println("Primera línea");
System.out.println("Segunda línea");
```

Salida:
```
Primera línea
Segunda línea
```

El cursor queda al inicio de la siguiente línea después de imprimir.

### `System.out.print()` — Imprime sin salto de línea

```java
System.out.print("Hola ");
System.out.print("Mundo");
System.out.print("!");
```

Salida:
```
Hola Mundo!
```

Todo queda en la misma línea porque no hay salto de línea.

### `System.out.printf()` — Imprime con formato

Permite insertar valores dentro de una cadena de texto usando especificadores de formato:

```java
String nombre = "María";
int edad = 28;
double salario = 2500.50;

System.out.printf("Nombre: %s%n", nombre);
System.out.printf("Edad: %d años%n", edad);
System.out.printf("Salario: $%.2f%n", salario);
```

Salida:
```
Nombre: María
Edad: 28 años
Salario: $2500.50
```

#### Tabla de especificadores de formato más usados

| Especificador | Tipo de dato | Ejemplo |
|---------------|--------------|---------|
| `%s` | String (texto) | `"Hola"` |
| `%d` | Entero (int, long) | `42` |
| `%f` | Decimal (float, double) | `3.14` |
| `%.2f` | Decimal con 2 cifras | `3.14` |
| `%c` | Carácter (char) | `'A'` |
| `%b` | Booleano | `true` |
| `%n` | Salto de línea (portable) | — |
| `%10s` | Texto con ancho 10 | `"    Hola"` |

### Secuencias de escape en Strings

Dentro de un String, ciertos caracteres tienen significado especial y se escriben con una barra invertida `\`:

| Secuencia | Significado | Ejemplo |
|-----------|-------------|---------|
| `\n` | Salto de línea | `"Línea 1\nLínea 2"` |
| `\t` | Tabulación horizontal | `"Nombre:\tJuan"` |
| `\\` | Barra invertida literal | `"C:\\Windows"` |
| `\"` | Comilla doble literal | `"Dijo: \"hola\""` |
| `\'` | Comilla simple literal | `"It\'s Java"` |
| `\r` | Retorno de carro | (menos común) |

Ejemplo:

```java
System.out.println("Nombre:\tCarlos");
System.out.println("Ciudad:\tMadrid");
System.out.println("País:\tEspaña");
```

Salida:
```
Nombre:	Carlos
Ciudad:	Madrid
País:	España
```

---

## 11. Comentarios en Java

Los comentarios son texto dentro del código que el compilador ignora completamente. Sirven para:
- Explicar qué hace el código
- Dejar notas para otros desarrolladores (o para ti mismo en el futuro)
- Deshabilitar temporalmente líneas de código durante pruebas

### Comentario de una línea

```java
// Esto es un comentario de una sola línea
int x = 5; // También puedes poner comentarios al final de una línea de código
```

### Comentario de múltiples líneas

```java
/*
   Esto es un comentario
   que puede abarcar
   varias líneas.
   Útil para explicaciones largas.
*/
int resultado = calcularTotal();
```

### Comentario Javadoc

Es un tipo especial de comentario que la herramienta `javadoc` usa para generar documentación oficial en formato HTML. Se coloca justo antes de la declaración de una clase o método.

```java
/**
 * Esta clase representa a un estudiante del curso de Java.
 * 
 * @author Tu Nombre
 * @version 1.0
 * @since 2024
 */
public class Estudiante {

    /**
     * Calcula el promedio de calificaciones del estudiante.
     * 
     * @param calificaciones Array con las calificaciones
     * @return El promedio calculado como número decimal
     */
    public double calcularPromedio(int[] calificaciones) {
        // implementación...
        return 0.0;
    }
}
```

### Buenas prácticas con comentarios

- **Comenta el "por qué", no el "qué":** El código ya dice qué hace; el comentario debe explicar por qué se hace así.
- **Evita comentarios obvios:** `int x = 5; // asigna 5 a x` — esto no añade valor.
- **Mantén los comentarios actualizados:** Un comentario desactualizado es peor que no tener comentario.
- **Usa Javadoc en clases y métodos públicos:** Es la forma estándar de documentar APIs en Java.

---

## 12. Reglas fundamentales del lenguaje

Estas reglas son absolutas en Java. Violarlas produce errores de compilación.

### Regla 1: Java es sensible a mayúsculas y minúsculas (case-sensitive)

```java
int edad = 25;
int Edad = 30;    // Esta es una variable DIFERENTE a "edad"
// edad y Edad son dos variables distintas

System.out.println(EDAD); // Error: EDAD no existe
```

### Regla 2: El nombre del archivo debe coincidir con la clase pública

```java
// Si tu clase se llama:
public class MiPrograma { ... }

// El archivo debe llamarse:
// MiPrograma.java   ← CORRECTO
// miprograma.java   ← ERROR
// MiPrograma.Java   ← ERROR (la extensión en minúscula)
// mi_programa.java  ← ERROR
```

### Regla 3: Toda instrucción termina con punto y coma

```java
System.out.println("Hola");  // Correcto
System.out.println("Hola")   // Error de compilación: falta el ;
```

### Regla 4: Los bloques de código se delimitan con llaves { }

Cada llave de apertura `{` debe tener su correspondiente llave de cierre `}`. Si falta alguna, el compilador reportará un error.

```java
public class Ejemplo {
    public static void main(String[] args) {
        System.out.println("Hola");
    }  // cierra main
}      // cierra la clase
```

### Regla 5: Todo código ejecutable debe estar dentro de un método

```java
public class Ejemplo {
    System.out.println("Hola"); // ERROR: esto está fuera de un método

    public static void main(String[] args) {
        System.out.println("Hola"); // CORRECTO: dentro del método main
    }
}
```

### Regla 6: Convenciones de nomenclatura (no son obligatorias, pero sí esperadas)

Aunque no son reglas del compilador, toda la comunidad Java sigue estas convenciones. No seguirlas no causa errores, pero hace que tu código parezca no profesional.

| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Clases | PascalCase | `MiClase`, `HolaMundo`, `GestorUsuarios` |
| Variables | camelCase | `nombreUsuario`, `edadEmpleado`, `totalVentas` |
| Métodos | camelCase | `calcularTotal()`, `obtenerNombre()` |
| Constantes | UPPER_SNAKE_CASE | `MAX_INTENTOS`, `PI`, `TASA_IVA` |
| Paquetes | minúsculas con puntos | `com.empresa.proyecto` |

---

## 13. Errores comunes del principiante

### Error 1: Olvidar el punto y coma

```java
// ERROR
System.out.println("Hola")

// Mensaje del compilador:
// error: ';' expected
```

**Solución:** Revisa que cada instrucción termine con `;`.

### Error 2: Nombre del archivo diferente al de la clase

```java
// Archivo: holaMundo.java (minúscula al inicio)
public class HolaMundo { ... }

// Mensaje del compilador:
// error: class HolaMundo is public, should be declared in a file named HolaMundo.java
```

**Solución:** El nombre del archivo y de la clase pública deben ser idénticos, incluyendo mayúsculas y minúsculas.

### Error 3: Llaves desbalanceadas

```java
public class Ejemplo {
    public static void main(String[] args) {
        System.out.println("Hola");
    // Falta la llave de cierre de la clase

// Mensaje:
// error: reached end of file while parsing
```

**Solución:** Usa la función de tu IDE para verificar el balance de llaves, o indenta correctamente el código para visualizarlas.

### Error 4: Confundir `print` con `println`

```java
System.out.print("Hola");
System.out.print(" Mundo");
// Salida: Hola Mundo  (todo en la misma línea)

System.out.println("Hola");
System.out.println(" Mundo");
// Salida:
// Hola
//  Mundo  (cada uno en su línea)
```

### Error 5: Olvidar las comillas en un String

```java
System.out.println(Hola Mundo);   // ERROR: Hola Mundo no es una variable

System.out.println("Hola Mundo"); // CORRECTO
```

### Error 6: Usar el nombre incorrecto del método main

```java
// ERROR: cualquier variación que no sea exactamente esta línea causará que la JVM no encuentre el punto de entrada
public static void Main(String[] args) { }  // Error: M mayúscula
public void main(String[] args) { }          // Error: falta static
public static int main(String[] args) { }    // Error: no debe retornar int
```

---

## 14. Ejercicios prácticos

Resuelve estos ejercicios en orden. Cada uno construye sobre el anterior.

### Ejercicio 1 — Verificar el entorno (Nivel: Básico)

Escribe, compila y ejecuta el programa "Hola Mundo" exactamente como se muestra en este documento. Si funciona correctamente, tu entorno está configurado.

**Resultado esperado:**
```
¡Hola Mundo!
```

---

### Ejercicio 2 — Tu presentación (Nivel: Básico)

Crea un programa llamado `MiPresentacion.java` que imprima en pantalla tu nombre, tu país y por qué quieres aprender Java. Usa tres llamadas a `System.out.println()`.

**Ejemplo de resultado:**
```
Nombre: Carlos González
País: México
Razón: Quiero desarrollar aplicaciones Android
```

---

### Ejercicio 3 — Arte con caracteres (Nivel: Básico)

Crea un programa llamado `FiguraEstrella.java` que dibuje un triángulo de asteriscos usando `println`:

**Resultado esperado:**
```
*
**
***
****
*****
```

---

### Ejercicio 4 — Usar printf (Nivel: Medio)

Crea un programa llamado `TablaProductos.java` que muestre una tabla de productos usando `System.out.printf()` para alinear las columnas:

**Resultado esperado:**
```
Producto          Precio    Stock
----------------------------------
Laptop            $999.99   15
Teclado           $49.99    50
Monitor           $299.99   8
```

*Pista: Usa `%-18s` para texto alineado a la izquierda con 18 caracteres de ancho, `$%-9.2f` para precios.*

---

### Ejercicio 5 — Secuencias de escape (Nivel: Medio)

Crea un programa llamado `DialogoPersonaje.java` que imprima el siguiente texto exactamente, incluyendo las comillas y la tabulación:

**Resultado esperado:**
```
El profesor dijo: "La programación es como cocinar."
	- Primero aprendes los ingredientes (variables)
	- Luego las recetas (algoritmos)
	- Finalmente abres tu restaurante (tus proyectos)
```

*Pista: Para las comillas dentro del String usa `\"`, para la tabulación usa `\t`.*

---

### Ejercicio 6 — Investigación (Nivel: Reflexión)

Responde en un comentario dentro de tu código:

1. ¿Cuál es la diferencia entre JDK y JRE en tus propias palabras?
2. ¿Qué hace el Garbage Collector y por qué es útil?
3. ¿Qué es el bytecode y por qué Java lo usa en lugar de compilar directamente a código de máquina?

---

## 15. Resumen del tema

Después de completar este tema, deberías ser capaz de:

| Habilidad | Estado |
|-----------|--------|
| Explicar qué es Java y para qué sirve | ✓ |
| Describir la historia y evolución de Java | ✓ |
| Diferenciar JVM, JRE y JDK y sus funciones | ✓ |
| Explicar cómo funciona la JVM internamente | ✓ |
| Instalar el JDK y un IDE en tu computadora | ✓ |
| Identificar las partes de un programa Java | ✓ |
| Escribir, compilar y ejecutar "Hola Mundo" | ✓ |
| Usar `println`, `print` y `printf` | ✓ |
| Escribir comentarios de línea, bloque y Javadoc | ✓ |
| Conocer las reglas y convenciones del lenguaje | ✓ |
| Identificar y corregir errores comunes | ✓ |

---

## Próximo tema

**Tema 2: Variables y tipos de datos**

Aprenderás a declarar variables, los tipos de datos primitivos (`int`, `double`, `boolean`, `char`, etc.), tipos de referencia, conversión de tipos (casting), y las reglas para nombrar variables correctamente.

---

*Curso de Java — Nivel 1: Fundamentos*  
*Tema 1 de 6*  
*Creado con fines educativos*
