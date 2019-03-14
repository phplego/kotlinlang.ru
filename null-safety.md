---
type: doc
layout: reference
category: "Syntax"
title: "Null безопасность"
url: https://kotlinlang.ru/docs/reference/null-safety.html
---

<!-- # Null Safety -->
# Null безопасность

<!-- ## Nullable types and Non-Null Types -->
## Nullable типы и Non-Null типы

<!-- Kotlin's type system is aimed at eliminating the danger of null references from code, also known as the [The Billion Dollar Mistake](http://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions). -->
Система типов в языке <b>Kotlin</b> нацелена на то, чтобы искоренить опасность обращения к *null* значениям, более
известную как ["Ошибка на миллион"](http://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions).

<!-- One of the most common pitfalls in many programming languages, including Java is that of accessing a member of a null reference, resulting in a null reference exception. In Java this -->
<!-- would be the equivalent of a `NullPointerException` or NPE for short. -->
Самым распространённым подводным камнем многих языков программирования, в том числе <b>Java</b>, является попытка произвести доступ к *null* значению.
Это приводит к ошибке. В <b>Java</b> такая ошибка называется `NullPointerException` (сокр. "NPE").

<!-- Kotlin's type system is aimed to eliminate `NullPointerException`'s from our code. The only possible causes of NPE's may be -->
<b>Kotlin</b> призван исключить ошибки подобного рода из нашего кода. NPE могу возникать только в случае:

<!-- * An explicit call to `throw NullPointerException()` -->
<!-- * Usage of the `!!` operator that is described below -->
<!-- * External Java code has caused it -->
<!-- * There's some data inconsistency with regard to initialization (an uninitialized *this* available in a constructor is used somewhere) -->

* Явного указания `throw NullPointerException()`
* Использования оператора `!!` (описано ниже)
* Эту ошибку вызвал внешний Java-код
* Есть какое-то несоответствие при инициализации данных (в конструкторе использована ссылка *this* на данные, которые не были ещё проинициализированы)

<!-- In Kotlin, the type system distinguishes between references that can hold *null*{: .keyword } (nullable references) and those that can not (non-null references). -->
<!-- For example, a regular variable of type `String` can not hold *null*{: .keyword }: -->
Система типов <b>Kotlin</b> различает ссылки на те, которые могут иметь значение *null* (nullable ссылки) и те, которые таковыми быть не могут (non-null ссылки).
К примеру, переменная часто используемого типа `String` не может быть *null*:


``` kotlin
 var a: String = "abc"
 a = null // ошибка компиляции
```

<!-- To allow nulls, we can declare a variable as nullable string, written `String?`: -->
Для того, чтобы разрешить *null* значение, мы можем объявить эту строковую переменную как `String?`:

``` kotlin
 var b: String? = "abc"
 b = null // ok
```

<!-- Now, if you call a method or access a property on `a`, it's guaranteed not to cause an NPE, so you can safely say -->
Теперь, при вызове метода с использованием переменной `a`, исключены какие-либо NPE. Вы спокойно можете писать:

``` kotlin
 val l = a.length
```

<!-- But if you want to access the same property on `b`, that would not be safe, and the compiler reports an error: -->
Но в случае, если вы захотите получить доступ к значению `b`, это будет небезопасно. Компилятор предупредит об ошибке:

``` kotlin
 val l = b.length // ошибка: переменная `b` может быть null
```

<!-- But we still need to access that property, right? There are a few ways of doing that. -->
Но нам по-прежнему надо получить доступ к этому свойству/значению, так? Есть несколько способов этого достичь.

<!-- ## Checking for *null*{: .keyword } in conditions -->
## Проверка на *null*

<!-- First, you can explicitly check if `b` is *null*{: .keyword }, and handle the two options separately: -->
Первый способ. Вы можете явно проверить `b` на *null* значение и обработать два варианта по отдельности:

``` kotlin
 val l = if (b != null) b.length else -1
```

<!-- The compiler tracks the information about the check you performed, and allows the call to `length` inside the *if*{: .keyword }. -->
<!-- More complex conditions are supported as well: -->
Компилятор отслеживает информацию о проведённой вами проверке и позволяет вызывать `length` внутри блока *if*.
Также поддерживаются более сложные конструкции:

``` kotlin
 if (b != null && b.length > 0) {
     print("String of length ${b.length}")
 } else {
     print("Empty string")
 }
```

<!-- Note that this only works where `b` is immutable (i.e. a local variable which is not modified between the check and the -->
<!-- usage or a member *val*{: .keyword } which has a backing field and is not overridable), because otherwise it might -->
<!-- happen that `b` changes to *null*{: .keyword } after the check. -->

Обратите внимание: это работает только в том случае, если `b` является неизменной переменной (ориг.: _immutable_). Например, если
это локальная переменная, значение которой не изменяется в период между его проверкой и использованием. Также такой переменной может служить *val*.
В противном случае может так оказаться, что переменная `b` изменила своё значение на *null* после проверки.

<!-- ## Safe Calls -->

## Безопасные вызовы

<!-- Your second option is the safe call operator, written `?.`: -->

Вторым способом является оператор безопасного вызова `?.`:

 ``` kotlin
 b?.length
 ```
<!-- This returns `b.length` if `b` is not null, and *null*{: .keyword } otherwise. The type of this expression is `Int?`. -->

Этот код возвращает `b.length` в том, случае, если `b` не имеет значение *null*. Иначе он возвращает *null*. Типом этого выражения будет `Int?`.

<!-- Safe calls are useful in chains. For example, if Bob, an Employee, may be assigned to a Department (or not), -->
<!-- that in turn may have another Employee as a department head, then to obtain the name of Bob's department head (if any), we write the following: -->

Такие безопасные вызовы полезны в цепочках. К примеру, если Bob, Employee (работник), может быть прикреплён (или нет) к отделу Department, и
у отдела может быть управляющий, другой Employee. Для того, чтобы обратиться к имени этого управляющего (если такой есть), напишем:

``` kotlin
 bob?.department?.head?.name
```

<!-- Such a chain returns *null*{: .keyword } if any of the properties in it is null. -->
Такая цепочка вернёт *null* в случае, если одно из свойств имеет значение *null*.

<!-- To perform a certain operation only for non-null values, you can use the safe call operator together with [`let`](/api/latest/jvm/stdlib/kotlin/let.html): -->
Для проведения каких-либо операций исключительно над non-null значениями вы можете использовать [`let`](/api/latest/jvm/stdlib/kotlin/let.html)
оператор вместе с оператором безопасного вызова:

``` kotlin
 val listWithNulls: List<String?> = listOf("A", null)
 for (item in listWithNulls) {
      item?.let { println(it) } // выводит A и игнорирует null
 }
```

<!-- ## Elvis Operator -->

## Элвис-оператор

<!-- When we have a nullable reference `r`, we can say "if `r` is not null, use it, otherwise use some non-null value `x`": -->
Если у нас есть nullable ссылка `r`, мы можем либо провести проверку этой ссылки и использовать её, либо использовать non-null значение `x`:

``` kotlin
 val l: Int = if (b != null) b.length else -1
```

<!-- Along with the complete *if*{: .keyword }-expression, this can be expressed with the Elvis operator, written `?:`: -->

Аналогом такому *if*-выражению является элвис-оператор `?:`:

``` kotlin
 val l = b?.length ?: -1
```

<!-- If the expression to the left of `?:` is not null, the elvis operator returns it, otherwise it returns the expression to the right. -->
<!-- Note that the right-hand side expression is evaluated only if the left-hand side is null. -->

Если выражение, стоящее слева от Элвис-оператора, не является null, то элвис-оператор его вернёт. В противном случае, в качестве возвращаемого значения
послужит то, что стоит справа.
Обращаем ваше внимание на то, что часть кода, расположенная справа, выполняется ТОЛЬКО в случае, если слева получается null.

<!-- Note that, since *throw*{: .keyword } and *return*{: .keyword } are expressions in Kotlin, they can also be used on -->
<!-- the right hand side of the elvis operator. This can be very handy, for example, for checking function arguments: -->

Так как *throw* и *return* тоже являются выражениями в <b>Kotlin</b>, их также можно использовать справа от Элвис-оператора.
Это может быть крайне полезным для проверки аргументов функции:

``` kotlin
 fun foo(node: Node): String? {
     val parent = node.getParent() ?: return null
     val name = node.getName() ?: throw IllegalArgumentException("name expected")
     // ...
 }
```

<!-- ## The `!!` Operator -->

## Оператор `!!`

<!-- The third option is for NPE-lovers. We can write `b!!`, and this will return a non-null value of `b` -->
<!-- (e.g., a `String` in our example) or throw an NPE if `b` is null: -->

Для любителей NPE существует ещё один способ. Мы можем написать `b!!` и это вернёт нам либо non-null значение `b`
(в нашем примере вернётся `String`), либо выкинет NPE:

``` kotlin
 val l = b!!.length
```

<!-- Thus, if you want an NPE, you can have it, but you have to ask for it explicitly, and it does not appear out of the blue. -->

В случае, если вам нужен NPE, вы можете заполучить её только путём явного указания.

<!-- ## Safe Casts -->

## Безопасные приведения типов (ориг.: _Safe Casts_)

<!-- Regular casts may result into a `ClassCastException` if the object is not of the target type. -->
<!-- Another option is to use safe casts that return *null*{: .keyword } if the attempt was not successful: -->

Обычное приведение типа может вызвать `ClassCastException` в случае, если объект имеет другой тип.
Можно использовать безопасное приведение, которое вернёт *null*, если попытка не удалась:

``` kotlin
 val aInt: Int? = a as? Int
```

<!-- ## Collections of Nullable Type -->
## Коллекции nullable типов

<!-- If you have a collection of elements of a nullable type and want to filter non-null elements, you can do so by using `filterNotNull`. -->

Если у вас есть коллекция nullable элементов и вы хотите отфильтровать все non-null элементы, используйте функцию `filterNotNull`.

``` kotlin
 val nullableList: List<Int?> = listOf(1, 2, null, 4)
 val intList: List<Int> = nullableList.filterNotNull()
```
