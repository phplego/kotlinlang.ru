---
type: doc
layout: reference
category: "Syntax"
title: "Null безопасность"
url: https://kotlinlang.ru/docs/null-safety.html
---

<!-- При переводе статьи оригинальная версия была от 08 July 2021 -->

<!-- # Null safety -->
# Null безопасность

<a name="nullable-types-and-non-null-types"></a>

<!-- ## Nullable types and non-null types -->
## Nullable типы и Non-Null типы

<!-- Kotlin's type system is aimed at eliminating the danger of null references, also known as [The Billion Dollar Mistake](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions). -->
Система типов в Kotlin нацелена на то, чтобы искоренить опасность обращения к `null` значениям, более известную как
["Ошибка на миллиард"](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions).

<!-- One of the most common pitfalls in many programming languages, including Java, is that accessing a member of a null
reference will result in a null reference exception. In Java this would be the equivalent of a `NullPointerException`,
or an _NPE_ for short. -->
Самым распространённым подводным камнем многих языков программирования, в том числе Java, является попытка произвести
доступ к `null` значению. Это приводит к ошибке. В Java такая ошибка называется `NullPointerException` (сокр. *NPE*).

<!-- The only possible causes of an NPE in Kotlin are: -->
В Kotlin NPE могут возникать только в случае:

<!-- * An explicit call to `throw NullPointerException()`.
* Usage of the `!!` operator that is described below.
* Data inconsistency with regard to initialization, such as when:
  * An uninitialized `this` available in a constructor is passed and used somewhere (a "leaking `this`").
  * A [superclass constructor calls an open member](inheritance.md#derived-class-initialization-order) whose implementation
  in the derived class uses an uninitialized state.
* Java interoperation:
  * Attempts to access a member of a `null` reference of a [platform type](java-interop.md#null-safety-and-platform-types);
  * Nullability issues with generic types being used for Java interoperation. For example, a piece of Java code might add
  `null` into a Kotlin `MutableList<String>`, therefore requiring a `MutableList<String?>` for working with it.
  * Other issues caused by external Java code. -->

* Явного указания `throw NullPointerException()`;
* Использования оператора `!!` (описано ниже);
* Несоответствие данных в отношении инициализации, например, когда:
  * Неинициализированное `this`, доступное в конструкторе, передается и где-то используется ("утечка `this`");
  * [Конструктор суперкласса вызывает open элемент](inheritance.html#derived-class-initialization-order), реализация которого в производном классе использует неинициализированное состояние.
* Эту ошибку вызвал внешний Java-код:
  * Попытка получить доступ к элементу `null` значения [платформенного типа](java-interop.html#null-safety-and-platform-types);
  * Проблемы с обнуляемостью при использовании обобщённых типов для взаимодействия с Java. Например, фрагмент кода Java может добавить `null` в Kotlin `MutableList<String>`, поэтому для работы с ним требуется `MutableList<String?>`;
  * Другие проблемы, вызванные внешним Java-кодом.

<!-- In Kotlin, the type system distinguishes between references that can hold `null` (nullable references) and those that
cannot (non-null references).
For example, a regular variable of type `String` cannot hold `null`: -->
Система типов Kotlin различает ссылки на те, которые могут иметь значение `null` (nullable ссылки) и те, которые
таковыми быть не могут (non-null ссылки). К примеру, переменная часто используемого типа `String` не может быть `null`.

```kotlin
var a: String = "abc" // Обычная инициализация означает non-null значение по умолчанию
a = null // ошибка компиляции
```

<!-- To allow nulls, you can declare a variable as a nullable string by writing `String?`: -->
Для того чтобы разрешить `null` значение, вы можете объявить эту строковую переменную как `String?`.

```kotlin
var b: String? = "abc" // null-значения возможны
b = null // ok
```

<!-- Now, if you call a method or access a property on `a`, it's guaranteed not to cause an NPE, so you can safely say: -->
Теперь, при вызове метода с использованием переменной `a`, исключены какие-либо NPE. Вы спокойно можете писать:

```kotlin
val l = a.length
```

<!-- But if you want to access the same property on `b`, that would not be safe, and the compiler reports an error: -->
Но в случае, если вы захотите получить доступ к значению `b`, это будет небезопасно. Компилятор предупредит об ошибке:

```kotlin
val l = b.length // ошибка: переменная `b` может быть null
```

<!-- But you still need to access that property, right? There are a few ways to do so. -->
Но вам по-прежнему нужен доступ к этому свойству/значению, верно? Есть несколько способов этого достичь.

<a name="checking-for-null-in-conditions"></a>

<!-- ## Checking for `null` in conditions -->
## Проверка на null

<!-- First, you can explicitly check whether `b` is `null`, and handle the two options separately: -->
Первый способ: вы можете явно проверить `b` на `null` значение и обработать два варианта по отдельности.

```kotlin
val l = if (b != null) b.length else -1
```

<!-- The compiler tracks the information about the check you performed, and allows the call to `length` inside the `if`.
More complex conditions are supported as well: -->
Компилятор отслеживает информацию о проведённой вами проверке и позволяет вызывать `length` внутри блока `if`.
Также поддерживаются более сложные конструкции:

```kotlin
if (b != null && b.length > 0) {
    print("Строка длиной ${b.length}")
} else {
    print("Пустая строка")
}
```

<!-- Note that this only works where `b` is immutable (meaning it is a local variable that is not modified between the check and its
usage or it is a member `val` that has a backing field and is not overridable), because otherwise it could be the case
that `b` changes to `null` after the check. -->
Обратите внимание: это работает только в том случае, если `b` является неизменной переменной (ориг.: _immutable_).
Например, если это локальная переменная, значение которой не изменяется в период между его проверкой и использованием,
или переменная `val`, которая имеет теневое поле и не может быть переопределено. В противном случае может так оказаться,
что переменная `b` изменила своё значение на `null` после проверки.

<a name="safe-calls"></a>

<!-- ## Safe calls -->
## Безопасные вызовы

<!-- Your second option for accessing a property on a nullable variable is using the safe call operator `?.`: -->
Вторым способом доступа к свойству nullable переменной - это использование оператор безопасного вызова `?.`.

```kotlin
val a = "Kotlin"
val b: String? = null
println(b?.length)
println(a?.length) // Ненужный безопасный вызов
```

<!-- This returns `b.length` if `b` is not null, and `null` otherwise. The type of this expression is `Int?`. -->
Этот код возвращает `b.length` в том, случае, если `b` не имеет значение `null`. Иначе он возвращает `null`. Типом этого
выражения будет `Int?`.

<!-- Safe calls are useful in chains. For example, Bob is an employee who may be assigned to a department (or not). That department
may in turn have another employee as a department head. To obtain the name of Bob's department head (if there is one),
you write the following: -->
Такие безопасные вызовы полезны в цепочках. К примеру, если Bob (Боб), Employee (работник), может быть прикреплён (или
нет) к отделу Department, и у отдела может быть управляющий, другой Employee. Для того чтобы обратиться к имени этого
управляющего (если такой есть), напишем:

```kotlin
bob?.department?.head?.name
```

<!-- Such a chain returns `null` if any of the properties in it is `null`. -->
Такая цепочка вернёт `null` в случае, если одно из свойств имеет значение `null`.

<!-- To perform a certain operation only for non-null values, you can use the safe call operator together with
[`let`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html): -->
Для проведения каких-либо операций исключительно над non-null значениями вы можете использовать
[`let`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html) оператор вместе с оператором безопасного вызова.

```kotlin
val listWithNulls: List<String?> = listOf("Kotlin", null)
for (item in listWithNulls) {
    item?.let { println(it) } // выводит Kotlin и игнорирует null
}
```

<!-- A safe call can also be placed on the left side of an assignment. Then, if one of the receivers in the safe calls chain
is `null`, the assignment is skipped and the expression on the right is not evaluated at all: -->
Безопасный вызов также может быть размещен в левой части присвоения. Затем, если один из получателей в цепочке
безопасных вызовов равен `null`, присвоение пропускается, а выражение справа вообще не вычисляется.

```kotlin
// Если значение `person` или `person.department` равно null, функция не вызывается
person?.department?.head = managersPool.getManager()
```

<a name="elvis-operator"></a>

<!-- ## Elvis Operator -->
## Элвис-оператор

<!-- When you have a nullable reference, `b`, you can say "if `b` is not `null`, use it, otherwise use some non-null value": -->
Если у вас есть nullable ссылка `b`, вы можете либо провести проверку этой ссылки и использовать её, либо использовать
non-null значение:

```kotlin
val l: Int = if (b != null) b.length else -1
```

<!-- Instead of writing the complete `if` expression, you can also express this with the Elvis operator `?:`: -->
Вместо того чтобы писать полное `if`-выражение, вы можете использовать элвис-оператор `?:`.

```kotlin
val l = b?.length ?: -1
```

<!-- If the expression to the left of `?:` is not `null`, the Elvis operator returns it, otherwise it returns the expression
to the right.
Note that the expression on the right-hand side is evaluated only if the left-hand side is `null`. -->
Если выражение, стоящее слева от Элвис-оператора, не является `null`, то элвис-оператор его вернёт. В противном случае
в качестве возвращаемого значения послужит то, что стоит справа. Обращаем ваше внимание на то, что часть кода,
расположенная справа, выполняется ТОЛЬКО в случае, если слева получается `null`.

<!-- Since `throw` and `return` are expressions in Kotlin, they can also be used on
the right-hand side of the Elvis operator. This can be handy, for example, when checking function arguments: -->
Так как `throw` и `return` тоже являются выражениями в Kotlin, их также можно использовать справа от Элвис-оператора.
Это может быть крайне полезным для проверки аргументов функции.

```kotlin
fun foo(node: Node): String? {
    val parent = node.getParent() ?: return null
    val name = node.getName() ?: throw IllegalArgumentException("name expected")
    // ...
}
```

<a name="the-operator"></a>

<!-- ## The `!!` operator -->
## Оператор !!

<!-- The third option is for NPE-lovers: the not-null assertion operator (`!!`) converts any value to a non-null
type and throws an exception if the value is `null`. You can write `b!!`, and this will return a non-null value of `b`
(for example, a `String` in our example) or throw an NPE if `b` is `null`: -->
Для любителей NPE существует третий способ: оператор not-null (`!!`) преобразует любое значение в non-null тип и выдает
исключение, если значение равно `null`. Вы можете написать `b!!` и это вернёт нам либо non-null значение `b`
(в нашем примере вернётся `String`), либо выкинет NPE, если `b` равно `null`.

```kotlin
val l = b!!.length
```

<!-- Thus, if you want an NPE, you can have it, but you have to ask for it explicitly and it won’t appear out of the blue. -->
В случае, если вам нужен NPE, вы можете заполучить её только путём явного указания.

<a name="safe-casts"></a>

<!-- ## Safe casts -->
## Безопасные приведения типов

<!-- Regular casts may result in a `ClassCastException` if the object is not of the target type.
Another option is to use safe casts that return `null` if the attempt was not successful: -->
Обычное приведение типа может вызвать `ClassCastException` в случае, если объект имеет другой тип.
Можно использовать безопасное приведение, которое вернёт `null`, если попытка не удалась.

```kotlin
val aInt: Int? = a as? Int
```

<a name="collections-of-a-nullable-type"></a>

<!-- ## Collections of a nullable type -->
## Коллекции nullable типов

<!-- If you have a collection of elements of a nullable type and want to filter non-null elements, you can do so by using
`filterNotNull`: -->

Если у вас есть коллекция nullable элементов и вы хотите отфильтровать все non-null элементы, используйте функцию `filterNotNull`.

```kotlin
val nullableList: List<Int?> = listOf(1, 2, null, 4)
val intList: List<Int> = nullableList.filterNotNull()
```
