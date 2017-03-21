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
Ниболее базовый пример использования enum — это реализация типобезопасных перечислений

``` kotlin
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

``` kotlin
enum class Color(val rgb: Int) {
        RED(0xFF0000),
        GREEN(0x00FF00),
        BLUE(0x0000FF)
}
```

<!--## Anonymous Classes-->
## Анонимные классы

<!--Enum constants can also declare their own anonymous classes-->
Enum-константы также могут объявлять свои собственные анонимные классы

``` kotlin
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

<!--with their corresponding methods, as well as overriding base methods. Note that if the enum class defines any
members, you need to separate the enum constant definitions from the member definitions with a semicolon, just like
in Java.-->
как с их собственными методами, так и с перегруженными методами базового класса. Следует заметить, что при объявлении 
в enum-классе каких-либо членов, необходимо отделять их от списка констант точкой с запятой, так же как и в Java.

<!--## Working with Enum Constants-->
## Работа с enum-константами

<!--Just like in Java, enum classes in Kotlin have synthetic methods allowing to list
the defined enum constants and to get an enum constant by its name. The signatures
of these methods are as follows (assuming the name of the enum class is `EnumClass`):-->
Так же как и в Java, enum-классы в Kotlin имеют стандартные методы для вывода списка объявленных констант и для получения enum-константы по её имени.
Ниже приведены сигнатуры этих методов: 

``` kotlin
EnumClass.valueOf(value: String): EnumClass
EnumClass.values(): Array<EnumClass>
```

<!--The `valueOf()` method throws an `IllegalArgumentException` if the specified name does
not match any of the enum constants defined in the class.-->
Метод `valueOf()` выбрасывает исключение `IllegalArgumentException`, если указанное имя не соответствует ни одной константе, объявленной в классе.

<!--Every enum constant has properties to obtain its name and position in the enum class declaration:-->
Каждая enum-константа имеет поля, в которых содержатся её имя и порядковый номер в enum-классе:

``` kotlin
val name: String
val ordinal: Int
```

<!--The enum constants also implement the [Comparable](/api/latest/jvm/stdlib/kotlin/-comparable/index.html) interface, with the natural order being the order in which they are defined in the enum class.-->
Также, enum-константы реализуют интерфейс [Comparable](http://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html). Порядок сортировки соответствует порядку объявления.
