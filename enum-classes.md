---
type: doc
layout: reference
category: "Syntax"
title: "Enum классы"
url: https://kotlinlang.ru/docs/enum-classes.html
---

<!-- При переводе статьи оригинальная версия была от 09 July 2021 -->

<!-- # Enum classes -->
# Enum-классы

<!-- The most basic use case for enum classes is the implementation of type-safe enums: -->
Наиболее базовый пример использования enum (перечисления) — это реализация типобезопасных перечислений.

```kotlin
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```

<!-- Each enum constant is an object. Enum constants are separated by commas. -->
Каждая enum-константа является объектом. При объявлении константы разделяются запятыми.

<!-- Since each enum is an instance of the enum class, it can be initialized as: -->
Так как константы являются экземплярами enum-класса, они могут быть инициализированы.

```kotlin
enum class Color(val rgb: Int) {
    RED(0xFF0000),
    GREEN(0x00FF00),
    BLUE(0x0000FF)
}
```

<a name="anonymous-classes"></a>
<!-- ## Anonymous classes -->
## Анонимные классы

<!-- Enum constants can declare their own anonymous classes with their corresponding methods, as well as with overriding base
methods. -->
Enum-константы также могут объявлять свои собственные анонимные классы как с их собственными методами, так и с
перегруженными методами базового класса.

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

<!-- If the enum class defines any members, separate the constant definitions from the member definitions with a semicolon. -->
Следует заметить, что при объявлении в enum-классе каких-либо членов, необходимо отделять их от объявления констант
точкой с запятой.

<a name="implementing-interfaces-in-enum-classes"></a>
<!-- ## Implementing interfaces in enum classes -->
## Реализация интерфейсов в классах enum

<!-- An enum class can implement an interface (but it cannot derive from a class), providing either a common implementation of
interface members for all of the entries, or separate implementations for each entry within its anonymous class.
This is done by adding the interfaces you want to implement to the enum class declaration as follows: -->
Enum-класс может реализовывать интерфейс (но не наследоваться от класса), предоставляя либо единственную реализацию
членов интерфейса для всех элементов enum, либо отдельные для каждого элемента в своем анонимном классе. Это достигается
путем добавления интерфейсов, которые вы хотите реализовать, к объявлению класса enum следующим образом:

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

<a name="working-with-enum-constants"></a>
<!-- ## Working with enum constants -->
## Работа с enum-константами

<!-- Enum classes in Kotlin have synthetic methods for listing
the defined enum constants and getting an enum constant by its name. The signatures
of these methods are as follows (assuming the name of the enum class is `EnumClass`): -->
Enum-классы в Kotlin имеют синтетические методы для вывода списка объявленных enum-констант и для получения
enum-константы по её имени. Ниже приведены сигнатуры этих методов (при условии, что имя enum-класса - `EnumClass`):

```kotlin
EnumClass.valueOf(value: String): EnumClass
EnumClass.values(): Array<EnumClass>
```

<!-- The `valueOf()` method throws an `IllegalArgumentException` if the specified name does
not match any of the enum constants defined in the class. -->
Метод `valueOf()` выбрасывает исключение `IllegalArgumentException`, если указанное имя не соответствует ни одной
enum-константе, объявленной в классе.

<!-- You can access the constants in an enum class in a generic way using
the `enumValues<T>()` and `enumValueOf<T>()` functions: -->
К константам в enum-классе можно получить доступ универсальным способом, используя функции `enumValues<T>()` и
`enumValueOf<T>()`:

```kotlin
enum class RGB { RED, GREEN, BLUE }

inline fun <reified T : Enum<T>> printAllValues() {
    print(enumValues<T>().joinToString { it.name })
}

printAllValues<RGB>() // выведет RED, GREEN, BLUE
```

<!-- Every enum constant has properties for obtaining its name and position in the enum class declaration: -->
Каждая enum-константа имеет поля, в которых содержатся её имя и порядковый номер в объявлении enum-класса.

```kotlin
val name: String
val ordinal: Int
```

<!-- The enum constants also implement the [Comparable](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html) interface,
with the natural order being the order in which they are defined in the enum class. -->
Также enum-константы реализуют интерфейс [Comparable](http://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html).
Порядок сортировки соответствует порядку объявления.
