---
type: doc
layout: reference
category: Basics
title: Стилистика кода
url: https://kotlinlang.ru/docs/reference/coding-conventions.html
---

# Соглашение о стилистике кода

Данная страница содержит описание текущего стиля написания кода на языке Kotlin.

## Правила наименований
При возникновении сомнений по умолчанию используются следующие правила:

* используйте camelCase в названиях (а также избегайте подчёркиваний)
* названия типов пишутся с заглавной буквы
* названия методов и свойств начинаются со строчной буквы
* используйте отступ из 4 пробелов
* функции, объявленные как public, желательно снабжать документацией в стиле документации языка Kotlin

## Двоеточие

В тех случаях, когда двоеточие разделяет тип и подтип, перед двоеточием ставится пробел. Если же двоеточие ставится между сущностью и типом, то пробел опускается:

```kotlin
interface Foo<out T : Any> : Bar {
    fun foo(a: Int): T
}
```

## Лямбда-выражения

В лямбда-выражениях фигурные скобки, а также стрелка и параметры отделяются пробелами. Желательно передавать лямбду за пределами скобок.

```kotlin
list.filter { it > 10 }.map { element -> element * 2 }
```


В коротких лямбда-выражениях, не являющихся вложенными, рекомендуется использовать соглашение `it` вместо явного объявления параметра. Во вложенных лямбдах с параметрами последние всегда должны быть объявлены.

<!--## Class header formatting-->
## Объявление классов

<!--Classes with a few arguments can be written in a single line:-->
Классы с небольшим количеством аргументов можно писать на одной строчке:

```kotlin 
class Person(id: Int, name: String)
```

<!--Classes with longer headers should be formatted so that each primary constructor argument is in a separate line with indentation.
Also, the closing parenthesis should be on a new line. If we use inheritance, then the superclass constructor call or list of implemented interfaces
should be located on the same line as the parenthesis:-->
Классы с более длинными сигнатурами должны быть отформатированны так, чтобы каждый параметр располагался с новой строки

```kotlin 
class Person(
    id: Int, 
    name: String,
    surname: String
) : Human(id, name) {
    // ...
}
```

<!--For multiple interfaces, the superclass constructor call should be located first and then each interface should be located in a different line:-->
Если класс расширяет несколько интерфейсов, конструктор суперкласса (если он есть) должен располагаться на первой строке, а после него, список расширяемых интерфейсов: каждый интерфейс с новой строки.

```kotlin 
class Person(
    id: Int, 
    name: String,
    surname: String
) : Human(id, name),
    KotlinMaker {
    // ...
}
```

<!--Constructor parameters can use either the regular indent or the continuation indent (double the regular indent).-->
Для параметров конструктора может использоваться как обычный отступ, так и двойной.

## Тип Unit

Если возвращаемым типом является Unit, то его можно явно не указывать:

```kotlin
fun foo() { // здесь пропущено ": Unit"

}
```


<!--## Functions vs Properties-->
## Функции vs Свойства

<!--In some cases functions with no arguments might be interchangeable with read-only properties. 
Although the semantics are similar, there are some stylistic conventions on when to prefer one to another.-->
В некоторых случаях, функции без аргументов могут быть взаимозаменяемы с неизменяемыми (read-only) свойствами.
Несмотря на схожую семантику, есть некоторые стилистические соглашения, указывающие на то, когда лучше использовать одно из этих решений.

<!--Prefer a property over a function when the underlying algorithm:-->
Использование свойства перед функцией предпочтительнее, когда лежащий в основе алгоритм:

<!--does not throw
has a `O(1)` complexity
is cheap to calculate (or caсhed on the first run)
returns the same result over invocations -->
* не выбрасывает исключений
* имеет `O(1)` сложность
* не требует больших затрат на выполнение (или результат вычислений кэшируется при первом вызове)
* возвращает одинаковый результат
