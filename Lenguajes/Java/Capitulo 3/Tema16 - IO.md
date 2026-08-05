# Curso de Java — Nivel 3: Java Avanzado
## Tema 16: Entrada/Salida (I/O)

> **Nivel:** Intermedio-Avanzado | **Prerrequisitos:** Temas 1–15 completados | **Duración estimada:** 5–6 horas

---

## Tabla de contenidos

1. [¿Qué es la E/S en Java?](#1-qué-es-la-es-en-java)
2. [La API clásica — java.io](#2-la-api-clásica--javaio)
3. [Leer archivos de texto](#3-leer-archivos-de-texto)
4. [Escribir archivos de texto](#4-escribir-archivos-de-texto)
5. [Leer y escribir archivos binarios](#5-leer-y-escribir-archivos-binarios)
6. [La API moderna — java.nio.2](#6-la-api-moderna--javanio2)
7. [La clase Files — operaciones de alto nivel](#7-la-clase-files--operaciones-de-alto-nivel)
8. [Trabajar con directorios](#8-trabajar-con-directorios)
9. [Serialización de objetos](#9-serialización-de-objetos)
10. [Procesamiento de archivos con Streams](#10-procesamiento-de-archivos-con-streams)
11. [Archivos de propiedades](#11-archivos-de-propiedades)
12. [Entrada estándar — Scanner y Console](#12-entrada-estándar--scanner-y-console)
13. [Errores comunes con I/O](#13-errores-comunes-con-io)
14. [Ejercicios prácticos](#14-ejercicios-prácticos)
15. [Resumen del tema](#15-resumen-del-tema)

---

## 1. ¿Qué es la E/S en Java?

La **Entrada/Salida** (I/O — Input/Output) es el mecanismo mediante el cual un programa lee datos desde fuentes externas (archivos, teclado, red) y escribe datos hacia destinos externos (archivos, pantalla, red).

### Dos APIs de I/O en Java

```
API CLÁSICA (java.io)           API MODERNA NIO.2 (java.nio.2)
──────────────────────          ──────────────────────────────
Desde Java 1.0                  Desde Java 7
Basada en streams               Basada en canales y buffers
Síncrona y bloqueante           Puede ser no bloqueante
File, FileReader, BufferedReader Path, Files, Paths
Verbosa pero familiar           Concisa y más poderosa
```

### El modelo de streams de I/O

Java modela la E/S como un flujo (stream) de datos. Hay dos tipos fundamentales:

```
BYTE STREAMS (datos binarios):     CHARACTER STREAMS (texto):
────────────────────────────────   ────────────────────────
InputStream / OutputStream         Reader / Writer
FileInputStream                    FileReader / FileWriter
BufferedInputStream                BufferedReader / BufferedWriter
DataInputStream                    PrintWriter
ObjectInputStream                  InputStreamReader (puente)
```

### Jerarquía simplificada

```
                InputStream              OutputStream
               /     |      \           /     |      \
     FileInput  Buffered  Object    FileOut  Buffered  Object
     Stream     Input     Input     Stream   Output    Output
                Stream    Stream             Stream    Stream

                Reader                  Writer
               /      \                /      \
        FileReader  BufferedReader  FileWriter  PrintWriter
                BufferedWriter
```

---

## 2. La API clásica — java.io

### La clase File

`File` representa una ruta en el sistema de archivos (puede ser un archivo o directorio). No necesariamente existe en disco.

```java
import java.io.File;

// Crear objetos File
File archivo = new File("datos.txt");
File rutaAbsoluta = new File("/home/usuario/documentos/datos.txt");
File enSubdir = new File("carpeta/subcarpeta/archivo.txt");
File combinado = new File("carpeta", "archivo.txt"); // Concatena ruta

// Información sobre el archivo
System.out.println(archivo.getName());          // "datos.txt"
System.out.println(archivo.getPath());          // "datos.txt"
System.out.println(archivo.getAbsolutePath());  // Ruta completa
System.out.println(archivo.getCanonicalPath()); // Ruta sin ../. etc.
System.out.println(archivo.getParent());        // Directorio padre
System.out.println(archivo.length());           // Tamaño en bytes
System.out.println(archivo.lastModified());     // Timestamp de modificación

// Verificaciones
archivo.exists();        // ¿Existe en disco?
archivo.isFile();        // ¿Es un archivo (no directorio)?
archivo.isDirectory();   // ¿Es un directorio?
archivo.canRead();       // ¿Se puede leer?
archivo.canWrite();      // ¿Se puede escribir?
archivo.isHidden();      // ¿Está oculto?

// Operaciones
archivo.createNewFile();        // Crea el archivo si no existe (retorna boolean)
archivo.delete();               // Elimina el archivo/directorio vacío
archivo.renameTo(new File("nuevo.txt")); // Renombra/mueve
archivo.mkdirs();               // Crea el directorio y sus padres
archivo.setReadOnly();          // Hace el archivo de solo lectura

// Listar contenido de directorio
File dir = new File(".");
String[] nombres = dir.list();           // Array de nombres
File[] archivos  = dir.listFiles();      // Array de objetos File
File[] soloTxt   = dir.listFiles(
    f -> f.getName().endsWith(".txt")); // Con filtro
```

---

## 3. Leer archivos de texto

### 3.1 FileReader y BufferedReader — lectura básica

```java
import java.io.*;

// Siempre usar try-with-resources para garantizar el cierre
try (BufferedReader br = new BufferedReader(new FileReader("datos.txt"))) {
    String linea;
    while ((linea = br.readLine()) != null) {
        System.out.println(linea);
    }
} catch (FileNotFoundException e) {
    System.out.println("Archivo no encontrado: " + e.getMessage());
} catch (IOException e) {
    System.out.println("Error de lectura: " + e.getMessage());
}
```

### 3.2 Especificar codificación (importante para acentos)

```java
import java.nio.charset.StandardCharsets;

// Con codificación explícita UTF-8 (recomendado siempre)
try (BufferedReader br = new BufferedReader(
        new InputStreamReader(
            new FileInputStream("datos.txt"),
            StandardCharsets.UTF_8))) {

    String linea;
    int numeroLinea = 0;
    while ((linea = br.readLine()) != null) {
        numeroLinea++;
        System.out.printf("%3d: %s%n", numeroLinea, linea);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

### 3.3 Leer todo el archivo de una vez

```java
// Leer todas las líneas en una List<String>
try (BufferedReader br = new BufferedReader(new FileReader("datos.txt"))) {
    List<String> lineas = br.lines().collect(Collectors.toList());
    System.out.println("Total líneas: " + lineas.size());
    lineas.forEach(System.out::println);
}

// Leer carácter por carácter (menos eficiente, pero útil para parsing)
try (FileReader fr = new FileReader("datos.txt")) {
    int caracter;
    StringBuilder sb = new StringBuilder();
    while ((caracter = fr.read()) != -1) {
        sb.append((char) caracter);
    }
    System.out.println(sb.toString());
}
```

### 3.4 StreamTokenizer — lectura con tokens

```java
// Útil para archivos con tokens separados por espacios
try (BufferedReader br = new BufferedReader(new FileReader("numeros.txt"))) {
    StreamTokenizer st = new StreamTokenizer(br);
    double suma = 0;
    int contador = 0;
    while (st.nextToken() != StreamTokenizer.TT_EOF) {
        if (st.ttype == StreamTokenizer.TT_NUMBER) {
            suma += st.nval;
            contador++;
        }
    }
    System.out.printf("Suma: %.2f | Cantidad: %d | Promedio: %.2f%n",
        suma, contador, suma / contador);
}
```

---

## 4. Escribir archivos de texto

### 4.1 FileWriter y BufferedWriter

```java
// Escribir un archivo (sobreescribe si existe)
try (BufferedWriter bw = new BufferedWriter(new FileWriter("salida.txt"))) {
    bw.write("Primera línea");
    bw.newLine();  // Salto de línea compatible con el SO
    bw.write("Segunda línea");
    bw.newLine();
    bw.write("Tercera línea");
} catch (IOException e) {
    e.printStackTrace();
}

// Agregar al final (append mode)
try (BufferedWriter bw = new BufferedWriter(
        new FileWriter("salida.txt", true))) {  // true = append
    bw.newLine();
    bw.write("Línea agregada al final");
}
```

### 4.2 PrintWriter — escritura con formato

```java
// PrintWriter: como System.out pero para archivos
try (PrintWriter pw = new PrintWriter(
        new BufferedWriter(new FileWriter("reporte.txt")))) {

    pw.println("=== REPORTE DE VENTAS ===");
    pw.println();

    // printf con formato
    pw.printf("%-20s %10s %10s%n", "Producto", "Cantidad", "Total");
    pw.println("-".repeat(42));

    String[][] datos = {
        {"Laptop",    "5",  "$4,999.95"},
        {"Mouse",     "15", "$449.85"},
        {"Teclado",   "8",  "$399.92"}
    };

    for (String[] fila : datos) {
        pw.printf("%-20s %10s %10s%n", fila[0], fila[1], fila[2]);
    }

    pw.println("-".repeat(42));
    pw.printf("%-20s %10s %10s%n", "TOTAL", "28", "$5,849.72");

    // Verificar errores (PrintWriter no lanza IOExceptions)
    if (pw.checkError()) {
        System.out.println("Hubo un error al escribir.");
    }
}
```

### 4.3 Escribir con codificación explícita

```java
try (PrintWriter pw = new PrintWriter(
        new OutputStreamWriter(
            new FileOutputStream("utf8.txt"),
            StandardCharsets.UTF_8))) {

    pw.println("Texto con acentos: ñoño, áéíóú, ¿Cómo estás?");
    pw.println("Símbolos: €, ©, ®, ™");
}
```

---

## 5. Leer y escribir archivos binarios

Los archivos binarios almacenan datos en formato nativo (no texto legible), lo que es más eficiente en espacio y velocidad.

### 5.1 FileInputStream y FileOutputStream

```java
// Copiar un archivo binario (imagen, PDF, etc.)
try (FileInputStream fis = new FileInputStream("imagen.png");
     FileOutputStream fos = new FileOutputStream("copia.png")) {

    byte[] buffer = new byte[8192];  // Buffer de 8 KB
    int bytesLeidos;
    long totalCopiado = 0;

    while ((bytesLeidos = fis.read(buffer)) != -1) {
        fos.write(buffer, 0, bytesLeidos);
        totalCopiado += bytesLeidos;
    }

    System.out.printf("Copiados %.2f KB%n", totalCopiado / 1024.0);
} catch (IOException e) {
    e.printStackTrace();
}
```

### 5.2 DataInputStream y DataOutputStream — tipos primitivos

```java
// Escribir datos tipados en binario
try (DataOutputStream dos = new DataOutputStream(
        new BufferedOutputStream(new FileOutputStream("datos.bin")))) {

    dos.writeInt(42);
    dos.writeDouble(3.14159);
    dos.writeBoolean(true);
    dos.writeUTF("Hola Java");  // String en formato UTF-8 con longitud
    dos.writeLong(System.currentTimeMillis());
}

// Leer en el mismo orden que se escribieron
try (DataInputStream dis = new DataInputStream(
        new BufferedInputStream(new FileInputStream("datos.bin")))) {

    int entero     = dis.readInt();
    double decimal = dis.readDouble();
    boolean bool   = dis.readBoolean();
    String texto   = dis.readUTF();
    long tiempo    = dis.readLong();

    System.out.println(entero + " | " + decimal + " | " + bool
        + " | " + texto + " | " + tiempo);
}
```

### 5.3 RandomAccessFile — acceso aleatorio

```java
// RandomAccessFile permite leer y escribir en cualquier posición del archivo
try (RandomAccessFile raf = new RandomAccessFile("datos.bin", "rw")) {

    // Escribir en posición 0
    raf.seek(0);
    raf.writeInt(100);
    raf.writeInt(200);
    raf.writeInt(300);

    // Leer desde posición 4 (segundo int)
    raf.seek(4);
    int segundo = raf.readInt();  // 200

    // Ir al final
    raf.seek(raf.length());
    raf.writeInt(400);

    // Tamaño del archivo
    System.out.println("Tamaño: " + raf.length() + " bytes");
}
```

---

## 6. La API moderna — java.nio.2

Java 7 introdujo `java.nio.file` con una API más limpia y poderosa para trabajar con el sistema de archivos.

### Path — la alternativa moderna a File

```java
import java.nio.file.*;

// Crear paths
Path p1 = Path.of("datos.txt");                           // Java 11+
Path p2 = Paths.get("datos.txt");                         // Java 7+
Path p3 = Paths.get("/home", "usuario", "docs", "archivo.txt");
Path p4 = Paths.get("carpeta/subcarpeta/archivo.txt");

// Información del path
System.out.println(p3.getFileName());    // archivo.txt
System.out.println(p3.getParent());      // /home/usuario/docs
System.out.println(p3.getRoot());        // /
System.out.println(p3.toString());       // /home/usuario/docs/archivo.txt
System.out.println(p3.isAbsolute());     // true
System.out.println(p3.getNameCount());   // 4 componentes

// Navegar el path
System.out.println(p3.getName(0));       // home
System.out.println(p3.getName(1));       // usuario
System.out.println(p3.subpath(1, 3));   // usuario/docs

// Resolver rutas (concatenar)
Path base = Paths.get("/home/usuario");
Path completo = base.resolve("docs/archivo.txt");
// /home/usuario/docs/archivo.txt

// Relativizar
Path relativo = base.relativize(completo);  // docs/archivo.txt

// Normalizar (eliminar . y ..)
Path sucio = Paths.get("/home/usuario/../usuario/./docs");
Path limpio = sucio.normalize();  // /home/usuario/docs

// Convertir a URI o File
URI uri = p3.toUri();
File file = p3.toFile();
```

---

## 7. La clase Files — operaciones de alto nivel

`Files` es una clase utilitaria con métodos estáticos que simplifican enormemente las operaciones de I/O.

### 7.1 Leer archivos

```java
import java.nio.file.*;
import java.nio.charset.StandardCharsets;

Path path = Path.of("datos.txt");

// Leer todo el contenido como String (Java 11+)
String contenido = Files.readString(path);
String contenidoUTF8 = Files.readString(path, StandardCharsets.UTF_8);

// Leer todas las líneas como List<String>
List<String> lineas = Files.readAllLines(path);
List<String> lineasUTF8 = Files.readAllLines(path, StandardCharsets.UTF_8);

// Leer todos los bytes
byte[] bytes = Files.readAllBytes(path);

// Stream perezoso de líneas (ideal para archivos grandes)
try (Stream<String> stream = Files.lines(path)) {
    long lineasLargas = stream
        .filter(l -> l.length() > 80)
        .count();
    System.out.println("Líneas largas: " + lineasLargas);
}
```

### 7.2 Escribir archivos

```java
// Escribir String completo (sobreescribe)
Files.writeString(path, "Contenido del archivo");
Files.writeString(path, "Contenido UTF-8", StandardCharsets.UTF_8);

// Agregar al final
Files.writeString(path, "\nNueva línea al final",
    StandardOpenOption.APPEND);

// Escribir lista de líneas
List<String> lineas = List.of("Línea 1", "Línea 2", "Línea 3");
Files.write(path, lineas);
Files.write(path, lineas, StandardCharsets.UTF_8,
    StandardOpenOption.CREATE, StandardOpenOption.APPEND);

// Escribir bytes
byte[] datos = {72, 101, 108, 108, 111};
Files.write(path, datos);

// Opciones de apertura (StandardOpenOption)
// CREATE        — crea si no existe
// CREATE_NEW    — crea, falla si ya existe
// APPEND        — agrega al final
// TRUNCATE_EXISTING — sobreescribe (por defecto con write)
// READ          — abre para lectura
// WRITE         — abre para escritura
// DELETE_ON_CLOSE — elimina al cerrar
```

### 7.3 Operaciones de archivos

```java
// Copiar
Files.copy(origen, destino);
Files.copy(origen, destino, StandardCopyOption.REPLACE_EXISTING);
Files.copy(origen, destino, StandardCopyOption.COPY_ATTRIBUTES);

// Mover / Renombrar
Files.move(origen, destino);
Files.move(origen, destino, StandardCopyOption.REPLACE_EXISTING,
    StandardCopyOption.ATOMIC_MOVE);

// Eliminar
Files.delete(path);                    // Lanza excepción si no existe
Files.deleteIfExists(path);            // No lanza si no existe (retorna boolean)

// Crear archivos y directorios
Files.createFile(path);                // Archivo nuevo
Files.createDirectory(path);          // Un directorio
Files.createDirectories(path);        // Directorios anidados
Path temp = Files.createTempFile("prefijo", ".txt");  // Archivo temporal
Path tempDir = Files.createTempDirectory("tmpdir");    // Directorio temporal

// Información
Files.exists(path);
Files.notExists(path);
Files.isRegularFile(path);
Files.isDirectory(path);
Files.isReadable(path);
Files.isWritable(path);
Files.isExecutable(path);
Files.isHidden(path);
Files.size(path);                      // Tamaño en bytes
Files.getLastModifiedTime(path);       // FileTime

// Tipo MIME
String tipo = Files.probeContentType(path);  // "text/plain", "image/png", etc.
```

### 7.4 BufferedReader y BufferedWriter con NIO.2

```java
// Leer con BufferedReader moderno
try (BufferedReader br = Files.newBufferedReader(path, StandardCharsets.UTF_8)) {
    String linea;
    while ((linea = br.readLine()) != null) {
        System.out.println(linea);
    }
}

// Escribir con BufferedWriter moderno
try (BufferedWriter bw = Files.newBufferedWriter(path, StandardCharsets.UTF_8)) {
    bw.write("Línea 1");
    bw.newLine();
    bw.write("Línea 2");
}

// Con opciones
try (BufferedWriter bw = Files.newBufferedWriter(path,
        StandardCharsets.UTF_8,
        StandardOpenOption.CREATE,
        StandardOpenOption.APPEND)) {
    bw.write("Línea nueva");
}
```

---

## 8. Trabajar con directorios

### 8.1 Listar el contenido de un directorio

```java
Path directorio = Path.of(".");

// Listar directamente (no recursivo)
try (Stream<Path> entries = Files.list(directorio)) {
    entries
        .filter(Files::isRegularFile)
        .filter(p -> p.toString().endsWith(".java"))
        .forEach(System.out::println);
}

// Listar recursivamente
try (Stream<Path> all = Files.walk(directorio)) {
    all.filter(Files::isRegularFile)
       .filter(p -> p.toString().endsWith(".java"))
       .forEach(System.out::println);
}

// Con profundidad máxima
try (Stream<Path> limited = Files.walk(directorio, 2)) {  // Máximo 2 niveles
    limited.forEach(System.out::println);
}

// find: con condición BiPredicate
try (Stream<Path> found = Files.find(directorio, Integer.MAX_VALUE,
        (path, attrs) -> attrs.isRegularFile()
            && attrs.size() > 1024  // Archivos mayores a 1 KB
            && path.toString().endsWith(".txt"))) {
    found.forEach(System.out::println);
}
```

### 8.2 DirectoryStream — iteración controlada

```java
try (DirectoryStream<Path> stream = Files.newDirectoryStream(
        Path.of("."), "*.{java,class}")) {

    for (Path entry : stream) {
        System.out.printf("%-30s %8d bytes%n",
            entry.getFileName(),
            Files.size(entry));
    }
}
```

### 8.3 FileVisitor — visitante de árbol de directorios

```java
// Implementar FileVisitor para recorrer árbol completo
Files.walkFileTree(Path.of("."), new SimpleFileVisitor<Path>() {

    @Override
    public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
        System.out.println("Archivo: " + file);
        return FileVisitResult.CONTINUE;
    }

    @Override
    public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) {
        System.out.println("Directorio: " + dir);
        return FileVisitResult.CONTINUE;
    }

    @Override
    public FileVisitResult visitFileFailed(Path file, IOException exc) {
        System.err.println("Error: " + file + " — " + exc.getMessage());
        return FileVisitResult.CONTINUE;  // Continúa a pesar del error
    }

    @Override
    public FileVisitResult postVisitDirectory(Path dir, IOException exc) {
        return FileVisitResult.CONTINUE;
    }
});

// FileVisitResult opciones:
// CONTINUE      — continúa el recorrido
// TERMINATE     — para inmediatamente
// SKIP_SUBTREE  — no entra al subdirectorio
// SKIP_SIBLINGS — omite hermanos en el mismo nivel
```

### 8.4 Eliminar directorio y su contenido recursivamente

```java
// Eliminar un directorio y todo su contenido
public static void eliminarDirectorio(Path directorio) throws IOException {
    if (!Files.exists(directorio)) return;

    Files.walkFileTree(directorio, new SimpleFileVisitor<Path>() {
        @Override
        public FileVisitResult visitFile(Path file, BasicFileAttributes attrs)
                throws IOException {
            Files.delete(file);
            return FileVisitResult.CONTINUE;
        }

        @Override
        public FileVisitResult postVisitDirectory(Path dir, IOException exc)
                throws IOException {
            Files.delete(dir);
            return FileVisitResult.CONTINUE;
        }
    });
}
```

### 8.5 WatchService — vigilar cambios en directorios

```java
// Detectar cuando se crea, modifica o elimina un archivo
WatchService watcher = FileSystems.getDefault().newWatchService();
Path dir = Path.of(".");

dir.register(watcher,
    StandardWatchEventKinds.ENTRY_CREATE,
    StandardWatchEventKinds.ENTRY_MODIFY,
    StandardWatchEventKinds.ENTRY_DELETE);

System.out.println("Vigilando cambios en: " + dir);

// Loop de vigilancia (bloquea hasta que hay eventos)
WatchKey key;
while ((key = watcher.take()) != null) {
    for (WatchEvent<?> event : key.pollEvents()) {
        WatchEvent.Kind<?> kind = event.kind();
        Path filename = (Path) event.context();

        System.out.printf("[%s] %s%n", kind.name(), filename);
    }
    key.reset();  // Importante: reiniciar la clave para seguir recibiendo eventos
}
```

---

## 9. Serialización de objetos

La **serialización** es el proceso de convertir un objeto Java en una secuencia de bytes que puede guardarse en disco o enviarse por red, y posteriormente reconstruirlo (deserialización).

### 9.1 Hacer una clase Serializable

```java
import java.io.Serializable;

public class Empleado implements Serializable {
    // serialVersionUID: versión de la clase (muy importante)
    private static final long serialVersionUID = 1L;

    private String nombre;
    private int edad;
    private double salario;
    private transient String password; // transient: NO se serializa

    public Empleado(String nombre, int edad, double salario, String password) {
        this.nombre   = nombre;
        this.edad     = edad;
        this.salario  = salario;
        this.password = password;
    }

    // getters y toString...
    @Override
    public String toString() {
        return String.format("Empleado{nombre='%s', edad=%d, salario=%.2f, password=%s}",
            nombre, edad, salario, password);
    }
}
```

### 9.2 Serializar (escribir objeto a archivo)

```java
Empleado emp = new Empleado("Ana García", 28, 3500.0, "secreto123");

try (ObjectOutputStream oos = new ObjectOutputStream(
        new BufferedOutputStream(new FileOutputStream("empleado.ser")))) {

    oos.writeObject(emp);
    System.out.println("Empleado serializado: " + emp);
}
```

### 9.3 Deserializar (leer objeto desde archivo)

```java
try (ObjectInputStream ois = new ObjectInputStream(
        new BufferedInputStream(new FileInputStream("empleado.ser")))) {

    Empleado recuperado = (Empleado) ois.readObject();
    System.out.println("Empleado recuperado: " + recuperado);
    // password será null (era transient)
} catch (ClassNotFoundException e) {
    System.out.println("Clase no encontrada: " + e.getMessage());
}
```

### 9.4 Serializar múltiples objetos y colecciones

```java
List<Empleado> empleados = List.of(
    new Empleado("Ana",   28, 3500.0, "pass1"),
    new Empleado("Luis",  35, 4200.0, "pass2"),
    new Empleado("María", 42, 5100.0, "pass3")
);

// Serializar la lista completa (List<Empleado> también es Serializable)
try (ObjectOutputStream oos = new ObjectOutputStream(
        new FileOutputStream("empleados.ser"))) {
    oos.writeObject(empleados);
}

// Deserializar
try (ObjectInputStream ois = new ObjectInputStream(
        new FileInputStream("empleados.ser"))) {
    @SuppressWarnings("unchecked")
    List<Empleado> recuperados = (List<Empleado>) ois.readObject();
    recuperados.forEach(System.out::println);
} catch (ClassNotFoundException e) {
    e.printStackTrace();
}
```

### 9.5 El serialVersionUID — importancia

```java
// Si cambias la clase sin mantener el serialVersionUID,
// la deserialización fallará con InvalidClassException

// VERSIÓN 1 (serializada)
public class Empleado implements Serializable {
    private static final long serialVersionUID = 1L;
    private String nombre;
    private int edad;
}

// VERSIÓN 2 (nueva, con campo adicional — compatible si serialVersionUID es el mismo)
public class Empleado implements Serializable {
    private static final long serialVersionUID = 1L; // Mismo UID → compatible
    private String nombre;
    private int edad;
    private String departamento; // Campo nuevo → será null al deserializar objetos viejos
}

// VERSIÓN INCOMPATIBLE (cambió el UID)
public class Empleado implements Serializable {
    private static final long serialVersionUID = 2L; // UID diferente → InvalidClassException
    // ...
}
```

### 9.6 Serialización personalizada con readObject/writeObject

```java
public class ContraseñaSegura implements Serializable {
    private static final long serialVersionUID = 1L;
    private String usuario;
    private transient String password;  // No serializable directamente

    // Serialización personalizada
    private void writeObject(ObjectOutputStream oos) throws IOException {
        oos.defaultWriteObject();  // Serializa campos normales (usuario)
        // Guardamos el password cifrado
        oos.writeObject(cifrar(password));
    }

    private void readObject(ObjectInputStream ois)
            throws IOException, ClassNotFoundException {
        ois.defaultReadObject();  // Deserializa campos normales
        // Recuperamos y desciframos el password
        String passwordCifrado = (String) ois.readObject();
        this.password = descifrar(passwordCifrado);
    }

    private String cifrar(String texto) {
        // Implementación de cifrado (simplificado)
        return new StringBuilder(texto).reverse().toString();
    }

    private String descifrar(String texto) {
        return new StringBuilder(texto).reverse().toString();
    }
}
```

---

## 10. Procesamiento de archivos con Streams

Combinar I/O con la API Stream de Java 8 permite procesar archivos grandes de forma eficiente y elegante.

### 10.1 Procesar líneas de un archivo con Stream

```java
Path archivo = Path.of("ventas.csv");

// Contar líneas con datos válidos
long lineasValidas = Files.lines(archivo)
    .skip(1)  // Saltar encabezado
    .filter(l -> !l.trim().isEmpty())
    .filter(l -> !l.startsWith("#"))
    .count();

// Parsear CSV con streams
record Venta(String producto, int cantidad, double precio) {
    double total() { return cantidad * precio; }
}

List<Venta> ventas = Files.lines(Path.of("ventas.csv"))
    .skip(1)  // Saltar encabezado
    .filter(l -> !l.isBlank())
    .map(linea -> {
        String[] partes = linea.split(",");
        return new Venta(
            partes[0].trim(),
            Integer.parseInt(partes[1].trim()),
            Double.parseDouble(partes[2].trim())
        );
    })
    .collect(Collectors.toList());

// Estadísticas de ventas
double totalIngresos = ventas.stream()
    .mapToDouble(Venta::total)
    .sum();

Map<String, Double> ingresoPorProducto = ventas.stream()
    .collect(Collectors.groupingBy(
        Venta::producto,
        Collectors.summingDouble(Venta::total)));

System.out.printf("Total ingresos: $%.2f%n", totalIngresos);
ingresoPorProducto.forEach((p, t) ->
    System.out.printf("  %s: $%.2f%n", p, t));
```

### 10.2 Transformar archivos con streams

```java
// Leer, transformar y escribir
Path entrada = Path.of("entrada.txt");
Path salida  = Path.of("salida.txt");

// Filtrar y transformar líneas
List<String> lineasTransformadas = Files.lines(entrada)
    .filter(l -> !l.isBlank())
    .map(String::trim)
    .map(String::toUpperCase)
    .distinct()
    .sorted()
    .collect(Collectors.toList());

Files.write(salida, lineasTransformadas, StandardCharsets.UTF_8);

// Procesar múltiples archivos
Path directorio = Path.of("logs");
long erroresTotal = Files.list(directorio)
    .filter(p -> p.toString().endsWith(".log"))
    .flatMap(p -> {
        try {
            return Files.lines(p);
        } catch (IOException e) {
            return Stream.empty();
        }
    })
    .filter(l -> l.contains("[ERROR]"))
    .count();

System.out.println("Total errores en logs: " + erroresTotal);
```

### 10.3 Copiar y fusionar archivos

```java
// Fusionar múltiples archivos en uno
Path directorio = Path.of("partes");
Path archivofinal = Path.of("completo.txt");

try (BufferedWriter bw = Files.newBufferedWriter(archivofinal)) {
    Files.list(directorio)
        .filter(p -> p.toString().endsWith(".txt"))
        .sorted()
        .forEach(parte -> {
            try {
                Files.lines(parte).forEach(linea -> {
                    try {
                        bw.write(linea);
                        bw.newLine();
                    } catch (IOException e) {
                        throw new RuntimeException(e);
                    }
                });
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        });
}
```

---

## 11. Archivos de propiedades

Los archivos `.properties` son pares clave=valor muy usados para configuración de aplicaciones.

### 11.1 Leer archivos de propiedades

```java
import java.util.Properties;

// Leer desde archivo
Properties props = new Properties();

try (InputStream is = new FileInputStream("config.properties")) {
    props.load(is);                    // Para archivos .properties (texto)
} catch (IOException e) {
    e.printStackTrace();
}

// Obtener valores
String host     = props.getProperty("db.host");
String puerto   = props.getProperty("db.puerto", "5432");  // Con valor por defecto
String usuario  = props.getProperty("db.usuario");
int    timeout  = Integer.parseInt(props.getProperty("timeout", "30"));

System.out.println("Conectando a " + host + ":" + puerto);

// Iterar todas las propiedades
props.forEach((clave, valor) ->
    System.out.println(clave + " = " + valor));

// Como Map (entrySet)
for (Map.Entry<Object, Object> entry : props.entrySet()) {
    System.out.println(entry.getKey() + " → " + entry.getValue());
}
```

**Ejemplo de archivo `config.properties`:**

```
# Configuración de base de datos
db.host=localhost
db.puerto=5432
db.nombre=miapp
db.usuario=admin
db.password=secreto

# Configuración general
app.nombre=MiAplicacion
app.version=2.1.0
timeout=30
debug=false
```

### 11.2 Escribir archivos de propiedades

```java
Properties propsNuevas = new Properties();
propsNuevas.setProperty("app.nombre", "MiApp");
propsNuevas.setProperty("app.version", "1.0.0");
propsNuevas.setProperty("debug", "false");
propsNuevas.setProperty("max.conexiones", "10");

try (OutputStream os = new FileOutputStream("nueva_config.properties")) {
    propsNuevas.store(os, "Configuración generada automáticamente");
}
```

### 11.3 Cargar desde classpath (en proyectos)

```java
// Cargar desde resources del classpath (dentro del JAR)
try (InputStream is = MiClase.class.getResourceAsStream("/config.properties")) {
    if (is != null) {
        Properties props = new Properties();
        props.load(is);
        System.out.println(props.getProperty("app.nombre"));
    }
}

// Usando ClassLoader
ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
try (InputStream is = classLoader.getResourceAsStream("config.properties")) {
    Properties props = new Properties();
    props.load(is);
}
```

---

## 12. Entrada estándar — Scanner y Console

### 12.1 Scanner — leer desde el teclado

```java
import java.util.Scanner;

Scanner scanner = new Scanner(System.in);

// Leer distintos tipos
System.out.print("Nombre: ");
String nombre = scanner.nextLine();

System.out.print("Edad: ");
int edad = scanner.nextInt();

System.out.print("Salario: ");
double salario = scanner.nextDouble();

scanner.nextLine();  // Limpiar el buffer después de nextInt/nextDouble

System.out.print("Dirección: ");
String direccion = scanner.nextLine();

System.out.printf("Hola %s, %d años, $%.2f, %s%n",
    nombre, edad, salario, direccion);

scanner.close();
```

### 12.2 Validación de entrada con Scanner

```java
Scanner sc = new Scanner(System.in);

// Leer entero con validación
public static int leerEntero(Scanner sc, String mensaje, int min, int max) {
    int valor;
    while (true) {
        System.out.print(mensaje);
        if (sc.hasNextInt()) {
            valor = sc.nextInt();
            sc.nextLine();  // Limpiar buffer
            if (valor >= min && valor <= max) {
                return valor;
            }
            System.out.printf("El valor debe estar entre %d y %d.%n", min, max);
        } else {
            System.out.println("Por favor ingresa un número entero.");
            sc.nextLine();  // Descartar entrada inválida
        }
    }
}

int edad = leerEntero(sc, "Ingresa tu edad (1-120): ", 1, 120);
System.out.println("Edad válida: " + edad);
```

### 12.3 Scanner para leer archivos

```java
// Scanner también puede leer archivos
try (Scanner fileSc = new Scanner(new File("datos.txt"))) {
    while (fileSc.hasNextLine()) {
        System.out.println(fileSc.nextLine());
    }
}

// Con delimitadores personalizados
try (Scanner sc = new Scanner(new File("datos.csv"))) {
    sc.useDelimiter(",|\\n");  // Coma o salto de línea como separador
    while (sc.hasNext()) {
        System.out.println(sc.next().trim());
    }
}
```

### 12.4 Console — entrada segura para contraseñas

```java
// Console: lee contraseñas sin mostrarlas en pantalla
Console console = System.console();

if (console != null) {
    String usuario = console.readLine("Usuario: ");
    char[] password = console.readPassword("Contraseña: ");  // No se muestra

    // Usar la contraseña
    autenticar(usuario, new String(password));

    // Limpiar la contraseña de la memoria (buena práctica de seguridad)
    java.util.Arrays.fill(password, ' ');
} else {
    System.out.println("No hay consola disponible (ej: ejecutando desde IDE)");
    Scanner sc = new Scanner(System.in);
    String usuario = sc.nextLine();
    // En IDE no hay Console, usa Scanner como alternativa
}
```

---

## 13. Errores comunes con I/O

### Error 1: No cerrar los recursos (olvidar try-with-resources)

```java
// ✗ MAL: el archivo puede quedar abierto si hay excepción
FileWriter fw = new FileWriter("salida.txt");
fw.write("datos");
fw.close();  // No se llama si write() lanza excepción

// ✓ BIEN: try-with-resources garantiza el cierre
try (FileWriter fw = new FileWriter("salida.txt")) {
    fw.write("datos");
}  // fw.close() se llama siempre, incluso con excepción
```

### Error 2: Rutas absolutas hardcodeadas

```java
// ✗ MAL: solo funciona en una máquina específica
String ruta = "C:\\Users\\Juan\\documentos\\datos.txt";

// ✓ BIEN: ruta relativa o construida dinámicamente
Path datos = Path.of("datos.txt");
Path enHome = Path.of(System.getProperty("user.home"), "datos.txt");
Path temporal = Files.createTempFile("datos", ".txt");
```

### Error 3: No especificar la codificación

```java
// ✗ MAL: usa la codificación del sistema (varía entre plataformas)
new FileReader("texto.txt")
new FileWriter("texto.txt")

// ✓ BIEN: siempre especificar UTF-8 explícitamente
new InputStreamReader(new FileInputStream("texto.txt"), StandardCharsets.UTF_8)
Files.readString(path, StandardCharsets.UTF_8)
Files.newBufferedReader(path, StandardCharsets.UTF_8)
```

### Error 4: Leer en orden incorrecto en DataInputStream

```java
// Si escribes: int → double → String
dos.writeInt(42);
dos.writeDouble(3.14);
dos.writeUTF("Hola");

// Debes leer EXACTAMENTE en el mismo orden:
int n = dis.readInt();      // 42
// ✗ MAL: leer double cuando el primero fue int
String s = dis.readUTF();   // EOFException o datos corruptos

// ✓ BIEN: mismo orden
int n     = dis.readInt();    // 42
double d  = dis.readDouble(); // 3.14
String st = dis.readUTF();    // "Hola"
```

### Error 5: Path vs. String en la API moderna

```java
// ✗ MAL: mezclar String con API de Path
File f = new File("datos.txt");
Files.readString(f);  // ERROR: Files espera Path, no File

// ✓ BIEN: convertir
Files.readString(f.toPath());   // File → Path
// O usar Path desde el inicio:
Path p = Path.of("datos.txt");
Files.readString(p);
```

### Error 6: No manejar FileNotFoundException antes de IOException

```java
// ✗ MAL: orden incorrecto (FileNotFoundException es subclase de IOException)
try {
    new FileReader("archivo.txt");
} catch (IOException e) {
    System.out.println("Error genérico");
} catch (FileNotFoundException e) {    // ¡Nunca se alcanza!
    System.out.println("No encontrado");
}

// ✓ BIEN: específico antes que general
try {
    new FileReader("archivo.txt");
} catch (FileNotFoundException e) {
    System.out.println("Archivo no encontrado: " + e.getMessage());
} catch (IOException e) {
    System.out.println("Error de I/O: " + e.getMessage());
}
```

---

## 14. Ejercicios prácticos

### Ejercicio 1 — Lector y escritor de CSV (Nivel: Básico)

Crea `GestorCSV.java` con métodos:
1. `escribirCSV(String archivo, List<String[]> datos, String[] encabezado)` — escribe con encabezado
2. `leerCSV(String archivo)` — retorna `List<String[]>` (primera fila es encabezado)
3. `filtrarCSV(String archivo, int columna, String valor)` — retorna filas donde columna=valor
4. `ordenarCSV(String archivo, int columna)` — reescribe el CSV ordenado por la columna dada

Prueba con un CSV de productos: código, nombre, categoría, precio, stock.

---

### Ejercicio 2 — Gestor de archivos de texto (Nivel: Básico-Medio)

Crea `GestorTexto.java` con:
1. `contarPalabras(Path archivo)` — total de palabras
2. `frecuenciaPalabras(Path archivo)` — `Map<String, Long>` de frecuencias
3. `buscarLineas(Path archivo, String patron)` — líneas que contienen el patrón
4. `reemplazar(Path archivo, String buscar, String reemplazar)` — reemplaza en todo el archivo
5. `fusionarArchivos(List<Path> archivos, Path destino)` — une todos en uno
6. `dividirArchivo(Path archivo, int lineasPorParte)` — divide en partes numeradas

---

### Ejercicio 3 — Sistema de configuración (Nivel: Medio)

Crea `ConfiguracionApp.java` que:
- Cargue la configuración desde `config.properties` al iniciar
- Si el archivo no existe, cree uno con valores por defecto
- Permita leer y escribir propiedades en tiempo de ejecución
- Guarde los cambios automáticamente al modificar
- Soporte tipos: String, int, double, boolean, List<String> (separados por coma)
- Tenga un método `recargar()` para leer los cambios del disco

Demuestra: cargar la config, modificar valores, guardar, recargar y verificar.

---

### Ejercicio 4 — Explorador de directorios (Nivel: Medio)

Crea `ExploradoDir.java` que reciba una ruta y genere un reporte completo:

```
/home/usuario/proyecto
├── src/
│   ├── Main.java              (2.3 KB)
│   ├── modelo/
│   │   ├── Empleado.java      (4.1 KB)
│   │   └── Producto.java      (3.2 KB)
│   └── servicio/
│       └── Servicio.java      (5.8 KB)
├── test/
│   └── MainTest.java          (1.9 KB)
└── pom.xml                    (0.8 KB)

Estadísticas:
  Total archivos: 6
  Total directorios: 3
  Tamaño total: 18.1 KB
  Extensiones: .java (5), .xml (1)
  Archivo más grande: Servicio.java (5.8 KB)
```

---

### Ejercicio 5 — Sistema de log con serialización (Nivel: Avanzado)

Diseña un sistema de logging con persistencia:

```java
record EntradaLog(
    LocalDateTime timestamp,
    String nivel,    // DEBUG, INFO, WARN, ERROR
    String clase,
    String mensaje,
    String excepcion // null si no hay excepción
) implements Serializable { }
```

`GestorLog`:
- `registrar(String nivel, String clase, String mensaje)`
- `registrar(String nivel, String clase, String mensaje, Exception e)`
- Persiste los logs en archivo binario con `ObjectOutputStream` (append)
- `leerTodos()` — deserializa todos los logs
- `filtrarPorNivel(String nivel)` — retorna solo los del nivel indicado
- `filtrarPorFecha(LocalDate fecha)` — retorna los de esa fecha
- `exportarTexto(Path destino)` — escribe los logs en formato legible
- `limpiarAntiguos(int diasMaximos)` — elimina logs de más de N días

---

### Ejercicio 6 — Procesador de logs de servidor (Nivel: Avanzado)

Dado un directorio con archivos de log de servidor (formato Apache/Nginx):

```
192.168.1.1 - - [25/Jul/2024:10:15:30 +0000] "GET /api/users HTTP/1.1" 200 1024
192.168.1.2 - - [25/Jul/2024:10:15:31 +0000] "POST /api/login HTTP/1.1" 401 256
```

Implementa `AnalizadorLogs` usando streams de archivos:
1. Total de peticiones por código de respuesta (200, 404, 500, etc.)
2. Las 10 IPs con más peticiones
3. Los 10 endpoints más solicitados
4. Peticiones por hora del día (distribución)
5. Tamaño promedio de respuesta por tipo de petición (GET, POST, etc.)
6. Errores 5xx con sus IPs de origen
7. Guardar el reporte completo en un archivo de texto formateado

Procesa eficientemente archivos de varios GB usando `Files.lines()` (lazy stream).

---

## 15. Resumen del tema

### Las opciones de I/O más usadas

```java
// ════════════════════════════════════════════
// LEER ARCHIVO DE TEXTO (recomendado: NIO.2)
// ════════════════════════════════════════════
// Todo de una vez (archivos pequeños)
String contenido = Files.readString(Path.of("archivo.txt"));
List<String> lineas = Files.readAllLines(Path.of("archivo.txt"));

// Línea por línea (archivos grandes — lazy)
try (Stream<String> stream = Files.lines(Path.of("archivo.txt"))) {
    stream.forEach(System.out::println);
}

// Con BufferedReader
try (BufferedReader br = Files.newBufferedReader(Path.of("archivo.txt"))) {
    String linea;
    while ((linea = br.readLine()) != null) { ... }
}

// ════════════════════════════════════════════
// ESCRIBIR ARCHIVO DE TEXTO
// ════════════════════════════════════════════
Files.writeString(Path.of("salida.txt"), "contenido");
Files.write(Path.of("salida.txt"), List.of("línea1", "línea2"));
Files.writeString(path, "más", StandardOpenOption.APPEND);

try (PrintWriter pw = new PrintWriter(Files.newBufferedWriter(Path.of("rpt.txt")))) {
    pw.printf("Datos: %s%n", dato);
}

// ════════════════════════════════════════════
// OPERACIONES DE ARCHIVO
// ════════════════════════════════════════════
Files.copy(origen, destino, StandardCopyOption.REPLACE_EXISTING);
Files.move(origen, destino);
Files.delete(path);
Files.deleteIfExists(path);
Files.exists(path);
Files.size(path);

// ════════════════════════════════════════════
// DIRECTORIOS
// ════════════════════════════════════════════
Files.createDirectories(path);
try (Stream<Path> entries = Files.list(dir)) { ... }
try (Stream<Path> all = Files.walk(dir)) { ... }

// ════════════════════════════════════════════
// SERIALIZACIÓN
// ════════════════════════════════════════════
// Escribir
try (ObjectOutputStream oos = new ObjectOutputStream(
        new FileOutputStream("objeto.ser"))) {
    oos.writeObject(miObjeto);
}
// Leer
try (ObjectInputStream ois = new ObjectInputStream(
        new FileInputStream("objeto.ser"))) {
    MiClase obj = (MiClase) ois.readObject();
}

// ════════════════════════════════════════════
// PROPIEDADES
// ════════════════════════════════════════════
Properties p = new Properties();
try (InputStream is = new FileInputStream("config.properties")) {
    p.load(is);
}
String valor = p.getProperty("clave", "default");
```

### Reglas de oro

| Situación | Regla |
|-----------|-------|
| Recursos | Siempre usar `try-with-resources` con cualquier stream de I/O |
| Codificación | Siempre especificar `StandardCharsets.UTF_8` explícitamente |
| API preferida | Usar NIO.2 (`Files`, `Path`) sobre la API clásica cuando sea posible |
| Archivos grandes | Usar `Files.lines()` (lazy stream) en lugar de `readAllLines()` |
| Rutas | Construir con `Path.of()` o `Paths.get()`, evitar Strings hardcodeados |
| Serialización | Siempre definir `serialVersionUID`; usar `transient` para datos sensibles |
| Properties | Cargar desde classpath con `getResourceAsStream()` en proyectos con JAR |
| Excepciones | Capturar `FileNotFoundException` antes de `IOException` |
| Binarios | Usar `BufferedInputStream/OutputStream` siempre para rendimiento |
| Directorios | `Files.walk()` para recorrido recursivo; `Files.list()` para un solo nivel |

---

## Próximo tema

**Tema 17: Hilos y Concurrencia**

Aprenderás a crear y gestionar hilos con `Thread` y `Runnable`, la sincronización con `synchronized` y `Lock`, el problema de race conditions y deadlocks, el `ExecutorService` para pools de hilos, `Future` y `CompletableFuture` para programación asíncrona, y las colecciones thread-safe del paquete `java.util.concurrent`.

---

*Curso de Java — Nivel 3: Java Avanzado*
*Tema 16 de 24*
*Creado con fines educativos*
