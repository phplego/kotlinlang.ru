---
type: doc
layout: reference
category: "Syntax"
title: "Перечисления (enum)"
url: https://kotlinlang.ru/docs/reference/enum-classes.html
---

<!--# Enum Classes-->
# Перечисляемые типы

<!--The most basic usage of enum classes is implementing type-safe enums-->
Наиболее базовый пример использования enum — это реализация типобезопасных перечислений

```kotlin
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```

<!--Each enum constant is an object. Enum constants are separated with commas.-->
Каждая enum-константа является объектом. При объявлении константы разделяются запятыми.

<!--## Initialization-->
## Инициализация

<!--Since each enum is an instance of the enum class, they can be initialized-->
Так как константы являются экземплярами enum-класса, они могут быть инициализированы

```kotlin
enum class Color(val rgb: Int) {
        RED(0xFF0000),
        GREEN(0x00FF00),
        BLUE(0x0000FF)
}
```

<!--## Anonymous Classes-->
## Анонимные классы

<!--Enum constants can also declare their own anonymous classes with their corresponding methods, as well as overriding base methods.-->
Enum-константы также могут объявлять свои собственные анонимные классы как с их собственными методами, так и с перегруженными методами базового класса.

```kotlin
enum class ProtocolState {
    WAITING {
        override fun signal() = TALKING
    },

    TALKING {
        override fun signal() = WAITING
    };

    abstract fun signal(): ProtocolState
}
```

<!-- If the enum class defines any members, separate the enum constant definitions from the member definitions with a semicolon.-->
Следует заметить, что при объявлении в enum-классе каких-либо членов, необходимо отделять их от списка констант точкой с запятой.

<!-- Enum entries cannot contain nested types other than inner classes (deprecated in Kotlin 1.2). -->
Элементы enum не могут содержать вложенные типы, за исключением внутренних (inner) классов (не рекомендуется с версии Kotlin 1.2).

<!-- ## Implementing Interfaces in Enum Classes -->
## Реализация интерфейсов в классах enum

<!-- An enum class may implement an interface (but not derive from a class), providing either a single interface members implementation for all of the entries, or separate ones for each entry within its anonymous class. This is done by adding the interfaces to the enum class declaration as follows: -->
Класс enum может реализовывать интерфейс (но не наследоваться от класса), предоставляя либо единственную реализацию членов интерфейса для всех элементов enum, либо отдельные для каждого элемента в своем анонимном классе. Это делается путем добавления интерфейсов к объявлению класса enum следующим образом:

```kotlin
import java.util.function.BinaryOperator
import java.util.function.IntBinaryOperator

enum class IntArithmetics : BinaryOperator<Int>, IntBinaryOperator {
    PLUS {
        override fun apply(t: Int, u: Int): Int = t + u
    },
    TIMES {
        override fun apply(t: Int, u: Int): Int = t * u
    };
    
    override fun applyAsInt(t: Int, u: Int) = apply(t, u)
}

fun main() {
    val a = 13
    val b = 31
    for (f in IntArithmetics.values()) {
        println("$f($a, $b) = ${f.apply(a, b)}")
    }
}
```

<!--## Working with Enum Constants-->
## Работа с enum-константами

<!--Just like in Java, enum classes in Kotlin have synthetic methods allowing to list
the defined enum constants and to get an enum constant by its name. The signatures
of these methods are as follows (assuming the name of the enum class is `EnumClass`):-->
Так же как и в Java, enum-классы в Kotlin имеют стандартные методы для вывода списка объявленных констант и для получения enum-константы по её имени.
Ниже приведены сигнатуры этих методов: 

```kotlin
EnumClass.valueOf(value: String): EnumClass
EnumClass.values(): Array<EnumClass>
```

<!--The `valueOf()` method throws an `IllegalArgumentException` if the specified name does
not match any of the enum constants defined in the class.-->
Метод `valueOf()` выбрасывает исключение `IllegalArgumentException`, если указанное имя не соответствует ни одной константе, объявленной в классе.

<!--Since Kotlin 1.1, it's possible to access the constants in an enum class in a generic way, using
the `enumValues<T>()` and `enumValueOf<T>()` functions:-->
Начиная с версии Kotlin 1.1 к константам в классе enum можно получить доступ универсальным способом, используя функции `enumValues<T>()` и `enumValueOf<T>()`:

```kotlin
enum class RGB { RED, GREEN, BLUE }

inline fun <reified T : Enum<T>> printAllValues() {
    print(enumValues<T>().joinToString { it.name })
}

printAllValues<RGB>() // выведет RED, GREEN, BLUE
```

<!--Every enum constant has properties to obtain its name and position in the enum class declaration:-->
Каждая enum-константа имеет поля, в которых содержатся её имя и порядковый номер в enum-классе:

```kotlin
val name: String
val ordinal: Int
```

<!--The enum constants also implement the [Comparable](/api/latest/jvm/stdlib/kotlin/-comparable/index.html) interface, with the natural order being the order in which they are defined in the enum class.-->
Также enum-константы реализуют интерфейс [Comparable](http://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html). Порядок сортировки соответствует порядку объявления.
