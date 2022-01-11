---
type: doc
layout: reference
category: "Basics"
title: "Идиомы"
url: https://kotlinlang.ru/docs/idioms.html
---

<!-- При переводе статьи оригинальная версия была от 30 November 2021 -->

<!-- # Idioms -->
# Идиомы

<!-- A collection of random and frequently used idioms in Kotlin. If you have a favorite idiom, contribute it by sending a pull request. -->
Набор различных часто используемых идиом в языке Kotlin. Если у вас есть любимая идиома, вы можете поделится ею здесь.
Для этого нужно создать pull request.

<a name="create-dtos-pojos-pocos"></a>

<!-- ## Create DTOs (POJOs/POCOs) -->
## Создание DTO (он же POJO или POCO)

```kotlin
data class Customer(val name: String, val email: String)
```

<!-- provides a `Customer` class with the following functionality: -->
создаёт класс `Customer`, обладающий следующими возможностями:

<!-- * getters (and setters in case of `var`s) for all properties
* `equals()`
* `hashCode()`
* `toString()`
* `copy()`
* `component1()`, `component2()`, ..., for all properties (see [Data classes](data-classes.md)) -->

* геттеры (и сеттеры в случае `var`) для всех свойств,
* метод `equals()`,
* метод `hashCode()`,
* метод `toString()`,
* метод `copy()`,
* методы `component1()`, `component2()`, и т.д. для всех свойств (см. [Классы данных](data-classes.html))

<a name="default-values-for-function-parameters"></a>

<!-- ## Default values for function parameters -->
## Значения по умолчанию для параметров функций

```kotlin
fun foo(a: Int = 0, b: String = "") { ... }
```

<a name="filter-a-list"></a>

<!-- ## Filter a list -->
## Фильтрация списка

```kotlin
val positives = list.filter { x -> x > 0 }
```

<!-- Or alternatively, even shorter: -->
Или короче:

```kotlin
val positives = list.filter { it > 0 }
```

<!-- Learn the difference between [Java and Kotlin filtering](java-to-kotlin-idioms-strings.md#create-a-string-from-collection-items). -->
Узнайте разницу между [фильтрацией в Java и Kotlin](java-to-kotlin-idioms-strings.html#create-a-string-from-collection-items).

<a name="check-the-presence-of-an-element-in-a-collection"></a>

<!-- ## Check the presence of an element in a collection -->
## Проверка наличия элемента в коллекции

```kotlin
if ("john@example.com" in emailsList) { ... }

if ("jane@example.com" !in emailsList) { ... }
```

<a name="string-interpolation"></a>

<!-- ## String interpolation -->
## Форматирование строк

```kotlin
println("Name $name")
```

<!-- Learn the difference between [Java and Kotlin string concatenation](java-to-kotlin-idioms-strings.md#concatenate-strings). -->
Узнайте разницу между [конкатенация строк в Java и Kotlin](java-to-kotlin-idioms-strings.html#concatenate-strings).

<a name="instance-checks"></a>

<!-- ## Instance checks -->
## Проверка объекта на принадлежность к определённому классу

```kotlin
when (x) {
    is Foo -> ...
    is Bar -> ...
    else   -> ...
}
```

<a name="read-only-list"></a>

<!-- ## Read-only list -->
## Read-only список

```kotlin
val list = listOf("a", "b", "c")
```

<a name="read-only-map"></a>

<!--## Read-only map-->
## Read-only ассоциативный список (map)

```kotlin
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```

<a name="access-a-map-entry"></a>

<!-- ## Access a map entry -->
## Обращение к ассоциативному списку

```kotlin
println(map["key"])
map["key"] = value
```

<a name="traverse-a-map-or-a-list-of-pairs"></a>

<!-- ## Traverse a map or a list of pairs -->
## Итерация по ассоциативному списку или списку пар

```kotlin
for ((k, v) in map) {
    println("$k -> $v")
}
```

<!-- `k` and `v` can be any convenient names, such as `name` and `age`. -->
`k` и `v` могут быть любыми удобными именами, такими как `name` и `age`.

<a name="iterate-over-a-range"></a>

<!-- ## Iterate over a range -->
## Итерация по по диапазону

```kotlin
for (i in 1..100) { ... }  // закрытый диапазон: включает 100
for (i in 1 until 100) { ... } // полуоткрытый диапазон: не включает 100
for (x in 2..10 step 2) { ... }
for (x in 10 downTo 1) { ... }
(1..10).forEach { ... }
```

<a name="lazy-property"></a>

<!-- ## Lazy property -->
## Ленивые свойства

```kotlin
val p: String by lazy {
    // compute the string
}
```

<a name="extension-functions"></a>

<!-- ## Extension functions -->
## Функции-расширения

```kotlin
fun String.spaceToCamelCase() { ... }

"Convert this to camelcase".spaceToCamelCase()
```

<a name="create-a-singleton"></a>

<!-- ## Create a singleton -->
## Создание синглтона

```kotlin
object Resource {
    val name = "Name"
}
```

<a name="instantiate-an-abstract-class"></a>

<!-- ## Instantiate an abstract class -->
## Создание экземпляра абстрактного класса

```kotlin
abstract class MyAbstractClass {
    abstract fun doSomething()
    abstract fun sleep()
}

fun main() {
    val myObject = object : MyAbstractClass() {
        override fun doSomething() {
            // ...
        }

        override fun sleep() { // ...
        }
    }
    myObject.doSomething()
}
```

<a name="if-not-null-shorthand"></a>

<!-- ## If-not-null shorthand -->
## Сокращение для "Если не null"

```kotlin
val files = File("Test").listFiles()

println(files?.size) // размер выводится, если размер файлов не равен null
```

<a name="if-not-null-else-shorthand"></a>

<!-- ## If-not-null-else shorthand -->
## Сокращение для "Если не null, иначе"

```kotlin
val files = File("Test").listFiles()

println(files?.size ?: "empty") // если файл равен null, выводится "empty"
```

<a name="execute-a-statement-if-null"></a>

<!-- ## Execute a statement if null -->
## Выброс исключения при равенстве null

```kotlin
val values = ...
val email = values["email"] ?: throw IllegalStateException("Email is missing!")
```

<a name="get-first-item-of-a-possibly-empty-collection"></a>

<!-- ## Get first item of a possibly empty collection -->
## Получение первого элемента, возможно, пустой коллекции

```kotlin
val emails = ... // может быть пустой
val mainEmail = emails.firstOrNull() ?: ""
```

<!-- Learn the difference between [Java and Kotlin first item getting](java-to-kotlin-collections-guide.md#get-the-first-and-the-last-items-of-a-possibly-empty-collection). -->
Узнайте разницу между [получения первого элемента в Java и Kotlin](java-to-kotlin-collections-guide.html#get-the-first-and-the-last-items-of-a-possibly-empty-collection).

<a name="execute-if-not-null"></a>

<!-- ## Execute if not null -->
## Выполнение при неравенстве null

```kotlin
val value = ...

value?.let {
    ... // этот блок выполняется, если value не равен null
}
```

<a name="map-nullable-value-if-not-null"></a>

<!-- ## Map nullable value if not null -->
## Маппинг nullable значение при неравенстве null

```kotlin
val value = ...

val mapped = value?.let { transformValue(it) } ?: defaultValue 
// возвращается defaultValue, если значение или результат преобразования равны null
```

<a name="return-on-when-statement"></a>

<!-- ## Return on when statement -->
## Return с оператором when

```kotlin
fun transform(color: String): Int {
    return when (color) {
        "Red" -> 0
        "Green" -> 1
        "Blue" -> 2
        else -> throw IllegalArgumentException("Invalid color param value")
    }
}
```

<a name="try-catch-expression"></a>

<!-- ## try-catch expression -->
## Выражение try-catch

```kotlin
fun test() {
    val result = try {
        count()
    } catch (e: ArithmeticException) {
        throw IllegalStateException(e)
    }

    // Working with result
}
```

<a name="if-expression"></a>

<!-- ## if expression -->
## Выражение if

```kotlin
val y = if (x == 1) {
    "one"
} else if (x == 2) {
    "two"
} else {
    "other"
}
```

<a name="builder-style-usage-of-methods-that-return-unit"></a>

<!-- ## Builder-style usage of methods that return Unit -->
## Builder-style использование методов, возвращающих Unit

```kotlin
fun arrayOfMinusOnes(size: Int): IntArray {
    return IntArray(size).apply { fill(-1) }
}
```

<a name="single-expression-functions"></a>

<!-- ## Single-expression functions -->
## Функции, состоящие из одного выражения

```kotlin
fun theAnswer() = 42
```

<!-- This is equivalent to -->
Что равносильно этому:

```kotlin
fun theAnswer(): Int {
    return 42
}
```

<!-- This can be effectively combined with other idioms, leading to shorter code. For example, with the `when` expression: -->
Для сокращения кода их можно эффективно совмещать с другими идиомами. Например, с `when`:

```kotlin
fun transform(color: String): Int = when (color) {
    "Red" -> 0
    "Green" -> 1
    "Blue" -> 2
    else -> throw IllegalArgumentException("Invalid color param value")
}
```

<a name="call-multiple-methods-on-an-object-instance-with"></a>

<!-- ## Call multiple methods on an object instance (with) -->
## Вызов нескольких методов объекта (with)

```kotlin
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()
with(myTurtle) { // нарисует квадрат размером 100 pix
    penDown()
    for (i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
```

<a name="configure-properties-of-an-object-apply"></a>

<!-- ## Configure properties of an object (apply) -->
## Конфигурация свойств объекта (apply)

```kotlin
val myRectangle = Rectangle().apply {
    length = 4
    breadth = 5
    color = 0xFAFAFA
}
```

<!-- This is useful for configuring properties that aren't present in the object constructor. -->
Это полезно для конфигурации свойств, которых нет в конструкторе объектов.

<a name="java-7-s-try-with-resources"></a>

<!-- ## Java 7's try-with-resources -->
## try-with-resources из Java 7

```kotlin
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```

<a name="generic-function-that-requires-the-generic-type-information"></a>

<!-- ## Generic function that requires the generic type information -->
## Обобщённая функция, требующая информацию об обобщённом типе

```kotlin
//  public final class Gson {
//     ...
//     public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException {
//     ...

inline fun <reified T: Any> Gson.fromJson(json: JsonElement): T = this.fromJson(json, T::class.java)
```

<a name="nullable-boolean"></a>

<!-- ## Nullable Boolean -->
## Nullable Boolean

```kotlin
val b: Boolean? = ...
if (b == true) {
    ...
} else {
    // `b` is false or null
}
```

<a name="swap-two-variables"></a>

<!-- ## Swap two variables -->
## Обмен значений переменных

```kotlin
var a = 1
var b = 2
a = b.also { b = a }
```

<a name="mark-code-as-incomplete-todo"></a>

<!-- ## Mark code as incomplete (TODO) -->
## Обозначение кода как незаконченного (TODO)

<!-- Kotlin's standard library has a `TODO()` function that will always throw a `NotImplementedError`.
Its return type is `Nothing` so it can be used regardless of expected type.
There's also an overload that accepts a reason parameter: -->
В стандартной библиотеке Kotlin есть функция `TODO()`, которая всегда выдает ошибку `NotImplementedError`. Её
возвращаемый тип - `Nothing`, поэтому её можно использовать независимо от ожидаемого типа. Существует также перегрузка
этой функции, которая принимает параметр причины.

```kotlin
fun calcTaxes(): BigDecimal = TODO("Waiting for feedback from accounting")
```

<!-- IntelliJ IDEA's kotlin plugin understands the semantics of `TODO()` and automatically adds a code pointer in the TODO tool window. -->
Плагин от IntelliJ IDEA понимает семантику `TODO()` и автоматически добавляет указатель кода в окно инструмента TODO.

<a name="what-s-next"></a>

<!-- ## What’s next? -->
## Что дальше?

<!-- * Solve [Advent of Code puzzles](advent-of-code.md) using the idiomatic Kotlin style
* Learn how to perform [typical tasks with strings in Java and Kotlin](java-to-kotlin-idioms-strings.md) -->

* Решайте [Advent of Code puzzles](advent-of-code.html), используя идиоматический стиль;
* Узнайте, как решать [типичные задачи со строками в Java и Kotlin](java-to-kotlin-idioms-strings.html).
