---
type: doc
layout: reference
category: "Basics"
title: "Идиомы"
url: https://kotlinlang.ru/docs/reference/idioms.html
---

# Идиомы

Набор различных часто используемых идиом в языке Kotlin. Если у вас есть любимая идиома, вы можете поделится ею здесь. Для этого нужно создать pull request.

### Создание DTO (он же POJO или POCO)

```kotlin
data class Customer(val name: String, val email: String)
```

создаёт класс `Customer`, обладающий следующими возможностями:

* геттеры (и сеттеры в случае **var**<!--keyword-->ов) для всех свойств
* метод `equals()`
* метод `hashCode()`
* метод `toString()`
* метод `copy()`
* методы `component1()`, `component2()`, и т.п. для всех свойств (см. [Классы данных](data-classes.html))


### Значения по умолчанию для параметров функций

```kotlin
fun foo(a: Int = 0, b: String = "") { ... }
```

### Фильтрация списка

```kotlin
val positives = list.filter { x -> x > 0 }
```

Или короче:

```kotlin
val positives = list.filter { it > 0 }
```

<!--### String Interpolation-->
### Форматирование строк

```kotlin
println("Name $name")
```

<!--### Instance Checks-->
### Проверка объекта на принадлежность к определённому классу

```kotlin
when (x) {
    is Foo -> ...
    is Bar -> ...
    else   -> ...
}
```
<!--### Traversing a map/list of pairs-->
### Итерация по карте/списку пар

```kotlin
for ((k, v) in map) {
    println("$k -> $v")
}
```

<!--`k`, `v` can be called anything.-->
Имена переменных `k` и `v` не имеют значения

<!--### Using ranges-->
### Использование последовательностей чисел 

```kotlin
for (i in 1..100) { ... }
for (x in 2..10) { ... }
```

<!--### Read-only list-->
### Read-only список

```kotlin
val list = listOf("a", "b", "c")
```

<a name="Read-only-ассоциативный-список"></a>

<!--### Read-only map-->
### Read-only ассоциативный список (map)

```kotlin
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```

<!--###Accessing a map-->
### Обращение к ассоциативному списку

```kotlin
println(map["key"])
map["key"] = value
```

<!--### Lazy property-->
### Ленивые свойства

```kotlin
val p: String by lazy {
    // compute the string
}
```

<!--### Extension Functions-->
### Функции-расширения

```kotlin
fun String.spaceToCamelCase() { ... }

"Convert this to camelcase".spaceToCamelCase()
```

<!--### Creating a singleton-->
### Создание синглтона

```kotlin
object Resource {
    val name = "Name"
}
```

<!--### If not null shorthand-->
### Сокращение для "Если не null"

```kotlin
val files = File("Test").listFiles()

println(files?.size)
```

<!--### If not null and else shorthand-->
### Сокращение для "Если не null, иначе" 

```kotlin
val files = File("Test").listFiles()

println(files?.size ?: "empty")
```

<!--### Executing a statement if null-->
### Выброс исключения при равенстве null

```kotlin
val data = ...
val email = data["email"] ?: throw IllegalStateException("Email is missing!")
```

<!--### Execute if not null-->
### Выполнение при неравенстве null

```kotlin
val data = ...

data?.let {
    ... // execute this block if not null
}
```

<!--### Return on when statement-->
### Return с оператором when

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

<!--### 'try/catch' expression-->
### 'try/catch' как выражение

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

<!--### 'if' expression-->
### 'if' как выражение

```kotlin
fun foo(param: Int) {
    val result = if (param == 1) {
        "one"
    } else if (param == 2) {
        "two"
    } else {
        "three"
    }
}
```

<!--### Builder-style usage of methods that return `Unit`-->
### Builder-style использование методов, возвращающих `Unit`

```kotlin
fun arrayOfMinusOnes(size: Int): IntArray {
    return IntArray(size).apply { fill(-1) }
}
```


<!--### Single-expression functions-->
### Функции, состоящие из одного выражения

```kotlin
fun theAnswer() = 42
```

<!--This is equivalent to-->
Что равносильно этому:

```kotlin
fun theAnswer(): Int {
    return 42
}
```

<!--This can be effectively combined with other idioms, leading to shorter code. E.g. with the **when**-expression:-->
Для сокращения кода их можно эффективно совмещать с другими идиомами. Например с **when**<!--keyword-->:

```kotlin
fun transform(color: String): Int = when (color) {
    "Red" -> 0
    "Green" -> 1
    "Blue" -> 2
    else -> throw IllegalArgumentException("Invalid color param value")
}
```

<!--### Calling multiple methods on an object instance ('with')-->
### Вызов нескольких методов объекта ('with')

```kotlin
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()
with(myTurtle) { //draw a 100 pix square
    penDown()
    for(i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
```

<!--### Java 7's try with resources-->
### try with resources из Java 7

```kotlin
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```

<!-- ### Convenient form for a generic function that requires the generic type information -->
### Удобная форма generic-функций, требующих информацию о generic-типе

```kotlin
//  public final class Gson {
//     ...
//     public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException {
//     ...

inline fun <reified T: Any> Gson.fromJson(json: JsonElement): T = this.fromJson(json, T::class.java)
```

<!-- ### Consuming a nullable Boolean -->
### Обработка nullable Boolean

```kotlin
val b: Boolean? = ...
if (b == true) {
    ...
} else {
    // `b` is false or null
}
```

<!-- ### Swapping two variables -->
### Обмен значений переменных

```kotlin
var a = 1
var b = 2
a = b.also { b = a }
```
