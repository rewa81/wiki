# Kotlin Basics

Hier findest du grundlegende Informationen zu Kotlin für das schnelle Nachschlagen.

### Einstiegspunkt (main-Funktion)
Jedes Kotlin-Programm benötigt eine `main`-Funktion als Einstiegspunkt. Ab Kotlin 1.3 kann diese ohne Parameter definiert werden.

```kotlin
fun main() {
    println("Hallo Welt!")
}

// Mit Argumenten (Kommandozeile)
fun main(args: Array<String>) {
    println(args.contentToString())
}
```

### Variablen
In Kotlin gibt es zwei Arten von Variablen:
- `val`: Eine schreibgeschützte (read-only) Variable. Einmal zugewiesen, kann der Wert nicht mehr geändert werden (entspricht `final` in Java).
- `var`: Eine veränderbare Variable.

```kotlin
val name = "Kotlin" // Kann nicht geändert werden
var version = 1.9    // Kann später geändert werden
version = 2.0
```

### Datentypen und deren Zuweisung
Kotlin besitzt eine statische Typisierung, kann Typen aber oft automatisch ableiten (Type Inference).

- **Ganzzahlen**: `Byte`, `Short`, `Int`, `Long`
- **Gleitkommazahlen**: `Float`, `Double`
- **Boolean**: `Boolean` (true/false)
- **Zeichen/Strings**: `Char`, `String`

Explizite Typzuweisung:
```kotlin
val count: Int = 10
val price: Double = 9.99
val message: String = "Hallo"
```

### Functions (Funktionen)
Funktionen werden mit dem Schlüsselwort `fun` deklariert.

```kotlin
fun greet(name: String): String {
    return "Hallo, $name!"
}

// Einzeilige Funktionen (Expression Body)
fun add(a: Int, b: Int) = a + b
```

### Default and Named Arguments
Kotlin erlaubt es, Standardwerte für Parameter zu definieren und Argumente beim Aufruf explizit zu benennen.

```kotlin
fun formatMessage(text: String, prefix: String = "Log", uppercase: Boolean = false) {
    // ...
}

// Aufruf mit Default-Werten
formatMessage("Fehler aufgetreten") 

// Aufruf mit Named Arguments (Reihenfolge egal)
formatMessage(uppercase = true, text = "Warnung")
```

### if Expression
In Kotlin ist `if` ein Ausdruck (Expression), das heißt, es gibt einen Wert zurück. Es ersetzt den ternären Operator (`? :`).

```kotlin
val max = if (a > b) a else b

val status = if (score > 90) {
    "Sehr gut"
} else {
    "Gut"
}
```

### While Loop
Die `while` und `do-while` Schleifen funktionieren wie in den meisten Sprachen.

```kotlin
var x = 5
while (x > 0) {
    println(x)
    x--
}
```

### for Loop and Ranges
Die `for`-Schleife in Kotlin nutzt Ranges, um über Bereiche oder Collections zu iterieren.

```kotlin
// Inklusive 1 bis 5
for (i in 1..5) {
    println(i)
}

// Bis 5 (exklusive 5)
for (i in 1 until 5) {
    println(i)
}

// Mit Schrittweite
for (i in 1..10 step 2) {
    println(i)
}

// Rückwärts
for (i in 5 downTo 1) {
    println(i)
}
```

### in Keyword
Das `in`-Schlüsselwort wird verwendet, um zu prüfen, ob ein Wert in einer Range oder Collection enthalten ist.

```kotlin
val x = 10
if (x in 1..20) {
    println("x ist im Bereich")
}

val names = listOf("Anna", "Bob", "Charly")
if ("Bob" in names) {
    println("Bob ist dabei")
}
```

### Expressions and Statements
- **Statement**: Führt eine Aktion aus, gibt aber keinen Wert zurück (z. B. eine Variablen-Deklaration oder eine Schleife).
- **Expression**: Ergibt einen Wert. In Kotlin sind viele Konstrukte (wie `if`, `when` oder `try`), die in anderen Sprachen Statements sind, Expressions.

```kotlin
// Statement
val a = 10 

// Expression (if liefert einen Wert)
val result = if (a > 5) "Groß" else "Klein"
```
