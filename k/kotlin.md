# Kotlin

## Language Overview

### Class

Similar to Java class

```kotlin
class Person(val name: String, val age: Int) // concise constructor

// Full primary constructor
class Person(name: String) {
    init {
        this.name = name
    }
}

// Changing visibility
class InternalComponent
internal constructor(name: String) {
    ...
}

// Secondary constructor
class Rectangle(val height: Int, val width: Int) {
    constructor(side: Int) : this(side, side) { ... }
}
```

### Class modifiers

#### Class Delegation

```kotlin
interface Repository {
    fun getById(id: Int): Customer
    fun getAll(): List<Customer>
}

interface Logger {
    fun logAll()
}

class Controller(
    repository: Repository,
    logger: Logger
) : Repository by repository, Logger by logger

fun use(controller: Controller) {
    controller.logAll() // calls controller.logger.logAll()
}
```

#### Data class

Similar to class, but also includes definition for `copy`, `equals`, `hashCode` and `toString` methods

```kotlin
data class Person(val name: String, val address: String)

contact.copy(address = "new address")
```

#### Enum class

```kotlin
enum class Color {
    BLUE, ORANGE, RED
}

enum class Color(
    val r: Int,
    val g: Int,
    val b: Int
) {
    BLUE(0, 0, 255), ORANGE(255, 165, 0), RED(255, 0, 0);
    
    fun rgb() = (r * 256 + g) * 256 + b
}
```

#### Sealed class

Restricts the class hierarchy: all subclasses must be located in the same file

```kotlin
sealed class Expr
class Num(val value: Int) : Expr()
class Sum(val left: Expr, val right: Expr) : Expr()

fun eval(e: Expr): Int = when (e) {
    is Num -> e.value
    is Sum -> eval(e.left) + eval(e.right)
}
// if Expr was not sealed, when would need an else branch
```

### Conditionals

#### If

If is an _expression_ in Kotlin - it returns a value you can assign to a variable

```kotlin
val max = if (a > b) a else b
```

#### Ternary operator

There is no ternary operator in Kotlin

#### When

Similar to Java's `switch` statement, useful with many conditions

```kotlin
// Checking conditions
val (description, colour) = when {
    degrees < 10 -> "cold" to BLUE
    degrees < 25 -> "mild" to ORANGE
    else -> "hot" to RED
}
```

```kotlin
// Checking values
enum class Color {
    BLUE, ORANGE, RED
}

fun getDescription(color: Color): String =
    when (color) {
        Color.BLUE -> "cold"
        Color.ORANGE -> "mild"
        Color.RED -> "hot"
    }
}
```

```kotlin
// Multiple values
fun respondToInput(input: String) = when (input) {
    "y", "yes" -> "I'm glad you agree"
    "n", "no" -> "Sorry to hear that"
    else -> "I don't understand you"
}
```

```kotlin
// Match on any object (using equals)
fun mix(c1: Color, c2: Color) =
    when (setOf(c1, c2)) {
        setOf(RED, YELLOW) -> ORANGE
        setOf(YELLOW, BLUE) -> GREEN
        setOf(BLUE, VIOLET) -> INDIGO
        else -> throw Exception("Dirty color")
    }
```

```kotlin
// Checking types
when (pet) {
    is Cat -> pet.meow() // equivalent to instanceof
    is Dog -> pet.woof() // smart cast to the right type
}

when (val pet = getMyFavouritePet()) {
    is Cat -> pet.meow()
    is Dog -> pet.woof()
}
```

### Constants

#### Compile-time constants

The value will be inlined everywhere in the resulting bytecode. It's possible only for primitive types and String.

```kotlin
const val answer = 42
```

#### @JvmField

Exposes a Kotlin property as a field in Java, so no getter and setter are generated.

```kotlin
@JvmField
val prop = MyClass()

// same as following in Java
public static final MyClass prop = new MyClass();
```

### Exceptions

There is no difference between `checked` and `unchecked` exceptions

```kotlin
val percentage = 
    if (number in 0..100)
        number
    else
        throw IllegalArgumentException("Error!")
```

`Try` is an expression

```kotlin
val number = try {
    Integer.parseInt(string)
} catch {e: NumberFormatException) {
    return
}
```

### Functions

#### Default arguments

```kotlin
fun displaySeparator(character: Char = '*', size: Int = 10) {
    repeat(size) {
        print(character)
    }
}

displaySeparator(size = 5) // equivalent to displaySeparator('*', 5)
displaySeparator(character = '?') // equivalent to displaySeparator('?', 10)
displaySeparator(size = 3, character = '5') // valid, prints 555
```

#### Extension functions

They are defined outside of the class, but they can be called as a regular member of the class. They have to be imported in all the locations where the function is used. As they are `static` Java methods under the hood, no `override` is possible for extension functions.

```kotlin
fun String.lastChar() = this.get(this.length - 1)
// fun String.lastChar() = get(length - 1) is also valid

val c: Char = "abc".lastChar() // returns 'c'
```

#### Infix functions

```kotlin
infix fun Int.until(to: Int): IntRange

1.until(10)
1 until 10

infix fun <A, B> A.to(that: B) = Pair(this, that)

"ANSWER".to(42)
"hot" to RED
```

#### Inline functions

The compiler will substitute the body of an inline function instead of calling it, which makes the performance overhead for inline functions to zero.

```kotlin
inline fun max(a: Int, b: Int) = if (a > b) a else b

@kotlin.internal.InlineOnly
public inline fun <R> run(block: () -> R): R = block()

// the InlineOnly annotation specifies that the function should
// not be called directly but always inlined
```

#### Named arguments

```kotlin
println(listOf('a', 'b', 'c').joinToString(
    separator="",
    prefix="(",
    postfix=")"
)) // will print (abc)
```

### Hello, world!

```kotlin
package intro

fun main() {
    val name = "Kotlin"
    println("Hello, $name!") // will print Hello, Kotlin!
}
```

### Lambdas

They replace anonymous classes in Java, and allow functional programming

```kotlin
// Basic syntax
{ x: Int, y: Int -> x + y }

// The following statements are all equivalent
list.any({ i: Int -> i > 0})
list.any() { i: Int -> i > 0 } // valid because lambda is the last argument
list.any { i: Int -> i > 0 } // empty parentheses can be omitted
list.any { i -> i > 0 } // type can be omitted if it can be inferred
list.any { it > 0 } // if lambda has a single argument

// Descructuring declarations
map.mapValues { (key, value) -> "$key -> $value!" }
map.mapValues { (_, value) -> "$value!" } // if one variable is not needed
```

```kotlin
{ println("hey!") }() // possible, but strange
run { println("hey!") } // usual way to run lambda
```

```kotlin
fun duplicateNonZero(list: List<Int>): List<Int> {
    return list.flatMap {
        if (it == 0) return listOf()
        listOf(it, it)
    }
}

println(duplicateNonZero(listOf(3, 0, 5)) // prints [] because the return inside
                                          // the lambda makes the fun return
 
// Correct versions                                         
list.flatMap {
    if (it == 0) return@flatMap listOf<Int>()
    listOf(it, it)
}

list.flatMap l@{
    if (it == 0) return@l listOf<Int>()
    listOf(it, it)
}
```

#### Lambda with receiver

Similar to an extensions function, it has an implicit `this` set to the first argument of the `with` extension function

```kotlin
val sb = StringBuilder()
with (sb) {
    appendln("Alphabeth: ")
    for (c in 'a'..'z') {
        append(c)
    }
    toString()
}
```

```kotlin
inline fun buildString(
    builderAction: StringBuilder.() -> Unit
) {
    val stringBuilder = StringBuilder()
    stringBuilder.builderAction()
    return stringBuilder.toString()
}

val s = buildString {
    this.append("...")
}
```

### Lateinit

`lateinit` allows non-nullable properties to be initialized outside the constructor method. It cannot be a `val`, because that would violate immutability, and cannot be nullable or be a primitive type, as it's initialized to `null` under the hood. We can check if the property has been inialized by using the `isinitialized` property.

```kotlin
class KotlinActivity: Activity() {
    lateinit var myData: MyData = null
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        myData = intent.getParcelableExtra("MY_DATA")
    }
}
```

### Loops

#### For

```kotlin
val list = listOf("a", "b", "c")
for (s: String in list) {
    print(s)
}

val map = mapOf(1 to "one", 2 to "two", 3 to "three")
for ((key, value) in map) {
    println("$key = $value")
}

val list = listOf("a", "b", "c")
for ((index, element) in list.withIndex()) {
    println("$index: $element")
}
```

```kotlin
// includes the upper bound - prints 123456789
for (i in 1..9) {
    print(i)
}

// excludes the upper bound - prints 12345678
for (i in 1 until 9) {
    print(i)
}

// iterating with a step - prints 97531
for (i in 9 downTo 1 step 2) {
    print(i)
}

// iterating over a string - prints bcd
for (ch in "abc") {
    print(ch + 1)
}
```

#### While

```kotlin
while (condition) {
 /*...*/
}
 
do {
 /*...*/
} while (condition)
```

### Main with arguments

```kotlin
package intro

fun main(args: Array<String>) {
    val name = if (args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!")
}
```

### Multiline strings

```kotlin
val q = """To code,
    or not to code?.."""
println(q) // will print To code,
           //                or not to code?..
           
val r = """To code,
    or not to code?..""".trimMargin()
println(q) // will print To code,
           //            or not to code?..
```

### Nullability

The modern approach is to make a NPE a _compile-time_ error rather than a _run-time_ error

```kotlin
val s1: String = "always not null"
val s2: String? = null

s1.length // OK
s2.length // Compiler error, s2 might be null
val length: Int? = s2?.length // equal to if (s2 != null) s2.length else null
val length: Int = s2?.length ?: 0 // ?: is called the Elvis operator
```

```kotlin
val s: String?
if (s == null) return
s.length // smart cast, it it passed the check above we know it's not null
s!! // if (s != null) s else null
```

```kotlin
if (any is String) {
    any.toUpperCase() // smart cast, any is casted to String in this block
}

(any as? String)?.toUpperCase() // safe cast, equivalent to
                                // if (a is String) a else null
```

### Object

It's the Kotlin way to express a Singleton

```kotlin
object KSingleton {
    fun foo() {}
}

KSingleton.foo() // from Kotlin
KSingleton.INSTANCE.foo() // from Java
```

#### Companion object

Special object inside a class, useful to implement static methods in Kotlin. They can also implement a interface.

```kotlin
class A {
    companion object {
        fun foo() = 1
    }
}

fun main(args: Array<String>) {
    A.foo()
}
```

```kotlin
class C {
    companion object {
        @JvmStatic fun foo() {}
        fun bar() {}
    }
}

// from Java
C.foo(); // works because of the @JvmStatic annotation
C.Companion.foo(); // accessing the companion object from Java
C.Companion.bar(); // accessing the companion object from Java
```

#### Object expression

Replaces anonymous classes with multiple methods \(if the anonymous class has a single method, it can be replaced by a lambda expresssion\). This is **not** a singleton.

```kotlin
window.addMouseListener(
    object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            // ...
        }
        
        override fun mouseEntered(e: MouseEvent) {
            // ...
        }
    }
)
```

### Properties

```kotlin
class Contact (
    val name: String, // read-only, property + getter
    var address: String // mutable, property + getter + setter
)

println(contact.address)
contact.address = "..."
```

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() {
            return height == width
        }
}

val rectangle = Rectangle(2, 3)
println(rectangle.isSquare) // false
```

```kotlin
class StateLogger {
    var state = false
        set(value) {
            println("state has changed: $field -> $value")
            field = value
        }
}
```

```kotlin
class LengthCounter {
    var counter: Int = 0
        private set
        
    fun addWord(word: String) {
        counter += word.length
    }
}
```

```kotlin
// Extension properties
val String.lastIndex: Int
    get() = this.length - 1
    
val String.indices: IntRange
    get() = 0..lastIndex
    
"abc".lastIndex // 2
"abc".indices // 0..2
```

```kotlin
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}
```

### Ranges

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
isLetter('q') // true
isLetter('*') // false

fun isNotDigist(c: Char) = c !in '0'..'9'
isNotDigit('x') // true

fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
    else -> "I don't know..."
}
recognize('$') // I don't know...
```

### Sequences

Allow lazy operations on collections without creating intermediate collections. They will apply "vertical" evaluation \(the first element goes through the whole chain, then the second, etc.\) rather than the "horizontal" evaluation collections will perform. Nothing happens until there is a terminal operation.

```kotlin
listOf(1, 2, 3, 4)    // [1, 2, 3, 4]
    .map { it * it }  // [1, 4, 9, 16]
    .find { it > 3 }  // 4
    
listOf(1, 2, 3, 4)
    .asSequence()    // asSequence will apply map and find
    .map { it * it } // to 1 and 2, stopping after 2 because
    .find { it > 3 } // a result is found
```

```kotlin
val numbers = generateSequence(3) { n ->
    (n + 1).takeIf { n < 7 }
}

println(numbers.first())  // does not run the lambda
println(numbers.toList()) // runs the lambda 4 times
```

```kotlin
val numbers = sequence {
    var x = 0
    while (true) {
        yield(x++)
    }
}
numbers.take(5).toList() // [0, 1, 2, 3, 4]
```

### String templates

```kotlin
package intro

fun main(args: Array<String>) {
    val name = if (args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!")
}

fun main(args: Array<String>) {
    println("Hello, ${args.getOrNull(0)!") // prints Hello, null! if args is empty
}
```

### Variables

```kotlin
val variable1 // read-only, corresponds to final value in Java
var variable2 // mutable
```

## Resources

### Articles

* [Learn Kotlin in Y minutes](https://learnxinyminutes.com/docs/kotlin/)

### Online Courses

* [Kotlin for Java developers](https://www.coursera.org/learn/kotlin-for-java-developers) - Coursera

