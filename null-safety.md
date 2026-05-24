---
type: doc
layout: reference
category: "Syntax"
title: "Null безопасность"
url: https://kotlinlang.ru/docs/null-safety.html
---

<!-- При переводе статьи оригинальная версия была актуализирована по официальной документации от 28 August 2025 -->

<!-- # Null safety -->
# Null безопасность

<!-- Null safety is a Kotlin feature designed to significantly reduce the risk of null references, also known as [The Billion-Dollar Mistake](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions). -->
Null безопасность - это возможность Kotlin, которая существенно снижает риск обращения к `null`-ссылкам, также известный
как ["ошибка на миллиард долларов"](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions).

<!-- One of the most common pitfalls in many programming languages, including Java, is that accessing a member of a null reference results in a null reference exception. In Java, this would be the equivalent of a `NullPointerException`, or an NPE for short. -->
Один из самых распространённых подводных камней во многих языках программирования, включая Java, - это доступ к члену
`null`-ссылки, который приводит к исключению null reference. В Java этому соответствует `NullPointerException`,
или сокращённо *NPE*.

<!-- Kotlin explicitly supports nullability as part of its type system, meaning you can explicitly declare which variables or properties are allowed to be `null`. Also, when you declare non-null variables, the compiler enforces that these variables cannot hold a `null` value, preventing an NPE. -->
Kotlin явно поддерживает допустимость `null` как часть системы типов. Это значит, что вы можете явно объявить,
каким переменным или свойствам разрешено принимать значение `null`. Кроме того, когда вы объявляете non-null переменные,
компилятор следит, чтобы такие переменные не могли хранить `null`, и тем самым предотвращает NPE.

<!-- Kotlin's null safety ensures safer code by catching potential null-related issues at compile time rather than runtime. This feature improves code robustness, readability, and maintainability by explicitly expressing `null` values, making the code easier to understand and manage. -->
Null безопасность Kotlin делает код безопаснее, потому что потенциальные проблемы, связанные с `null`, обнаруживаются
во время компиляции, а не во время выполнения. Явное указание `null`-значений повышает надёжность, читаемость и
сопровождаемость кода: такой код проще понимать и поддерживать.

<!-- The only possible causes of an NPE in Kotlin are: -->
В Kotlin NPE может возникнуть только в следующих случаях:

<!-- * An explicit call to `throw NullPointerException()`.
* Usage of the not-null assertion operator `!!`.
* Data inconsistency during initialization, such as when:
  * An uninitialized `this` available in a constructor is used somewhere else (a "leaking `this`").
  * A [superclass constructor calling an open member](inheritance.html#derived-class-initialization-order) whose implementation in the derived class uses an uninitialized state.
* Java interoperation:
  * Attempts to access a member of a `null` reference of a [platform type](java-interop.html#null-safety-and-platform-types).
  * Nullability issues with generic types. For example, a piece of Java code adding `null` into a Kotlin `MutableList<String>`, which would require `MutableList<String?>` to handle it properly.
  * Other issues caused by external Java code. -->

* Явный вызов `throw NullPointerException()`;
* Использование оператора утверждения not-null `!!`;
* Несогласованность данных во время инициализации, например:
  * Неинициализированный `this`, доступный в конструкторе, используется где-то ещё ("утечка `this`");
  * [Конструктор суперкласса вызывает open-член](inheritance.html#derived-class-initialization-order), реализация которого в производном классе использует неинициализированное состояние.
* Взаимодействие с Java:
  * Попытка получить доступ к члену `null`-ссылки [платформенного типа](java-interop.html#null-safety-and-platform-types);
  * Проблемы с null-допустимостью у обобщённых типов. Например, фрагмент Java-кода может добавить `null` в Kotlin `MutableList<String>`, и для корректной обработки такого значения потребуется `MutableList<String?>`;
  * Другие проблемы, вызванные внешним Java-кодом.

<!-- Besides NPE, another exception related to null safety is `UninitializedPropertyAccessException`. Kotlin throws this exception when you try to access a property that has not been initialized, ensuring that non-nullable properties are not used until they are ready. This typically happens with `lateinit` properties. -->
> Кроме NPE, с null безопасностью связано исключение
> [`UninitializedPropertyAccessException`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/-uninitialized-property-access-exception/).
> Kotlin выбрасывает его при попытке обратиться к свойству, которое ещё не было инициализировано. Так Kotlin гарантирует,
> что non-null свойства не используются до готовности. Обычно это происходит со
> [`lateinit` свойствами](properties.html#late-initialized-properties-and-variables).

<a name="nullable-types-and-non-null-types"></a>
<a name="nullable-types-and-non-nullable-types"></a>

<!-- ## Nullable types and non-nullable types -->
## Nullable типы и non-null типы

<!-- In Kotlin, the type system distinguishes between types that can hold `null` (nullable types) and those that cannot (non-nullable types). For example, a regular variable of type `String` cannot hold `null`: -->
В Kotlin система типов различает типы, которые могут хранить `null` (nullable типы), и типы, которые не могут этого
делать (non-null типы). Например, обычная переменная типа `String` не может хранить `null`.

```kotlin
fun main() {
    // Присваивает переменной non-null строку
    var a: String = "abc"
    // Пытается повторно присвоить null переменной non-null типа
    a = null
    print(a)
    // Null can not be a value of a non-null type String
}
```

<!-- You can safely call a method or access a property on `a`. It's guaranteed not to cause an NPE because `a` is a non-nullable variable. The compiler ensures that `a` always holds a valid `String` value, so there's no risk of accessing its properties or methods when it's `null`: -->
Вы можете безопасно вызвать метод или обратиться к свойству через `a`. Это гарантированно не вызовет NPE, потому что
`a` является non-null переменной. Компилятор следит, чтобы `a` всегда содержала корректное значение `String`,
поэтому риска обратиться к свойствам или методам `a`, когда она равна `null`, нет.

```kotlin
fun main() {
    // Присваивает переменной non-null строку
    val a: String = "abc"
    // Возвращает длину non-null переменной
    val l = a.length
    print(l)
    // 3
}
```

<!-- To allow `null` values, declare a variable with a `?` sign right after the variable type. For example, you can declare a nullable string by writing `String?`. This expression makes `String` a type that can accept `null`: -->
Чтобы разрешить `null`-значения, объявите переменную со знаком `?` сразу после типа. Например, nullable строку можно
объявить как `String?`. Такая запись делает `String` типом, который может принимать `null`.

```kotlin
fun main() {
    // Присваивает переменной nullable строку
    var b: String? = "abc"
    // Успешно присваивает null nullable переменной
    b = null
    print(b)
    // null
}
```

<!-- If you try accessing `length` directly on `b`, the compiler reports an error. This is because `b` is declared as a nullable variable and can hold `null` values. Attempting to access properties on nullables directly leads to an NPE: -->
Если попытаться обратиться к `length` напрямую через `b`, компилятор сообщит об ошибке. Это происходит потому, что
`b` объявлена как nullable переменная и может хранить `null`. Прямой доступ к свойствам nullable значений потенциально
привёл бы к NPE.

```kotlin
fun main() {
    // Присваивает переменной nullable строку
    var b: String? = "abc"
    // Присваивает null nullable переменной
    b = null
    // Пытается напрямую вернуть длину nullable переменной
    val l = b.length
    print(l)
    // Only safe (?.) or non-null asserted (!!.) calls are allowed on a nullable receiver of type String?
}
```

<!-- In the example above, the compiler requires you to use safe calls to check for nullability before accessing properties or performing operations. There are several ways to handle nullables: -->
В примере выше компилятор требует использовать безопасные вызовы, чтобы проверить null-допустимость перед доступом
к свойствам или выполнением операций. Есть несколько способов работать с nullable значениями:

<!-- * Check for null with the if conditional
* Safe call operator ?.
* Elvis operator ?:
* Not-null assertion operator !!
* Nullable receiver
* let function
* Safe casts as?
* Collections of a nullable type -->

* [Проверка на `null` с помощью условного выражения `if`](#check-for-null-with-the-if-conditional);
* [Оператор безопасного вызова `?.`](#safe-call-operator);
* [Элвис-оператор `?:`](#elvis-operator);
* [Оператор утверждения not-null `!!`](#not-null-assertion-operator);
* [Nullable получатель](#nullable-receiver);
* [Функция `let`](#let-function);
* [Безопасные приведения `as?`](#safe-casts);
* [Коллекции nullable типа](#collections-of-a-nullable-type).

<!-- Read the next sections for details and examples of `null` handling tools and techniques. -->
Читайте следующие разделы, чтобы подробнее узнать об инструментах и приёмах обработки `null`.

<a name="checking-for-null-in-conditions"></a>
<a name="check-for-null-with-the-if-conditional"></a>

<!-- ## Check for null with the if conditional -->
## Проверка на null с помощью if

<!-- When working with nullable types, you need to handle nullability safely to avoid an NPE. One way to handle this is checking for nullability explicitly with the `if` conditional expression. -->
При работе с nullable типами нужно безопасно обрабатывать null-допустимость, чтобы избежать NPE. Один из способов -
явно проверять значение на `null` с помощью условного выражения `if`.

<!-- For example, check whether `b` is `null` and then access `b.length`: -->
Например, проверьте, равна ли `b` значению `null`, и только затем обратитесь к `b.length`.

```kotlin
fun main() {
    // Присваивает nullable переменной null
    val b: String? = null
    // Сначала проверяет null-допустимость, затем обращается к length
    val l = if (b != null) b.length else -1
    print(l)
    // -1
}
```

<!-- In the example above, the compiler performs a smart cast to change the type from nullable `String?` to non-nullable `String`. It also tracks the information about the check you performed and allows the call to `length` inside the `if` conditional. -->
В примере выше компилятор выполняет [умное приведение](typecasts.html#smart-casts), меняя тип с nullable `String?`
на non-null `String`. Он также отслеживает информацию о выполненной проверке и разрешает вызов `length` внутри
условного выражения `if`.

<!-- More complex conditions are supported as well: -->
Более сложные условия тоже поддерживаются.

```kotlin
fun main() {
    // Присваивает переменной nullable строку
    val b: String? = "Kotlin"
    // Сначала проверяет null-допустимость, затем обращается к length
    if (b != null && b.length > 0) {
        print("Строка длиной ${b.length}")
        // Строка длиной 6
    } else {
        // Запасной вариант, если условие не выполнено
        print("Пустая строка")
    }
}
```

<!-- Note that the example above only works when the compiler can guarantee that `b` doesn't change between the check and its usage, same as the smart cast prerequisites. -->
Обратите внимание: пример выше работает только тогда, когда компилятор может гарантировать, что `b` не изменится
между проверкой и использованием. Это то же требование, что и для [умных приведений](typecasts.html#smart-casts).

<a name="safe-calls"></a>
<a name="safe-call-operator"></a>

<!-- ## Safe call operator -->
## Оператор безопасного вызова

<!-- The safe call operator `?.` allows you to handle nullability safely in a shorter form. Instead of throwing an NPE, if the object is `null`, the `?.` operator simply returns `null`: -->
Оператор безопасного вызова `?.` позволяет безопасно и кратко обрабатывать null-допустимость. Если объект равен `null`,
оператор `?.` не выбрасывает NPE, а просто возвращает `null`.

```kotlin
fun main() {
    // Присваивает переменной nullable строку
    val a: String? = "Kotlin"
    // Присваивает nullable переменной null
    val b: String? = null
    // Проверяет null-допустимость и возвращает длину или null
    println(a?.length)
    // 6
    println(b?.length)
    // null
}
```

<!-- The `b?.length` expression checks for nullability and returns `b.length` if `b` is non-null, or `null` otherwise. The type of this expression is `Int?`. -->
Выражение `b?.length` проверяет null-допустимость и возвращает `b.length`, если `b` не равна `null`; иначе оно
возвращает `null`. Тип такого выражения - `Int?`.

<!-- You can use the `?.` operator with both var and val variables in Kotlin: -->
В Kotlin оператор `?.` можно использовать и с [`var`, и с `val` переменными](basic-syntax.html#variables):

<!-- * A nullable `var` can hold a `null` (for example, `var nullableValue: String? = null`) or a non-null value (for example, `var nullableValue: String? = "Kotlin"`). If it's a non-null value, you can change it to `null` at any point.
* A nullable `val` can hold a `null` (for example, `val nullableValue: String? = null`) or a non-null value (for example, `val nullableValue: String? = "Kotlin"`). If it's a non-null value, you cannot change it to `null` subsequently. -->

* Nullable `var` может хранить `null` (например, `var nullableValue: String? = null`) или non-null значение (например, `var nullableValue: String? = "Kotlin"`). Если в ней хранится non-null значение, вы можете в любой момент заменить его на `null`;
* Nullable `val` может хранить `null` (например, `val nullableValue: String? = null`) или non-null значение (например, `val nullableValue: String? = "Kotlin"`). Если в ней хранится non-null значение, позже заменить его на `null` нельзя.

<!-- Safe calls are useful in chains. For example, Bob is an employee who may be assigned to a department (or not). That department may, in turn, have another employee as a department head. To obtain the name of Bob's department head (if there is one), you write the following: -->
Безопасные вызовы полезны в цепочках. Например, Bob - это сотрудник, который может быть прикреплён к отделу или не
прикреплён. У этого отдела, в свою очередь, может быть другой сотрудник в роли руководителя отдела. Чтобы получить имя
руководителя отдела Bob, если такой руководитель есть, напишите:

```kotlin
bob?.department?.head?.name
```

<!-- This chain returns `null` if any of its properties are `null`. -->
Такая цепочка вернёт `null`, если любое из её свойств равно `null`.

<!-- You can also place a safe call on the left side of an assignment: -->
Также безопасный вызов можно поместить в левую часть присваивания.

```kotlin
person?.department?.head = managersPool.getManager()
```

<!-- In the example above, if one of the receivers in the safe call chain is `null`, the assignment is skipped, and the expression on the right is not evaluated at all. For example, if either `person` or `person.department` is `null`, the function is not called. Here's the equivalent of the same safe call but with the `if` conditional: -->
В примере выше, если один из получателей в цепочке безопасных вызовов равен `null`, присваивание пропускается, а
выражение справа вообще не вычисляется. Например, если `person` или `person.department` равны `null`, функция не
вызывается. Ниже приведён эквивалент того же безопасного вызова через условное выражение `if`.

```kotlin
if (person != null && person.department != null) {
    person.department.head = managersPool.getManager()
}
```

<a name="elvis-operator"></a>

<!-- ## Elvis operator -->
## Элвис-оператор

<!-- When working with nullable types, you can check for `null` and provide an alternative value. For example, if `b` is not `null`, access `b.length`. Otherwise, return an alternative value: -->
При работе с nullable типами можно проверить значение на `null` и предоставить альтернативу. Например, если `b`
не равна `null`, обратитесь к `b.length`; иначе верните другое значение.

```kotlin
fun main() {
    // Присваивает nullable переменной null
    val b: String? = null
    // Проверяет null-допустимость. Если значение не null, возвращает length. Если null, возвращает 0
    val l: Int = if (b != null) b.length else 0
    println(l)
    // 0
}
```

<!-- Instead of writing the complete `if` expression, you can handle this in a more concise way with the Elvis operator `?:`: -->
Вместо полного `if`-выражения это можно записать короче с помощью элвис-оператора `?:`.

```kotlin
fun main() {
    // Присваивает nullable переменной null
    val b: String? = null
    // Проверяет null-допустимость. Если значение не null, возвращает length. Если null, возвращает non-null значение
    val l = b?.length ?: 0
    println(l)
    // 0
}
```

<!-- If the expression to the left of `?:` is not `null`, the Elvis operator returns it. Otherwise, the Elvis operator returns the expression to the right. The expression on the right-hand side is evaluated only if the left-hand side is `null`. -->
Если выражение слева от `?:` не равно `null`, элвис-оператор возвращает его. Иначе элвис-оператор возвращает выражение
справа. Выражение справа вычисляется только в том случае, если выражение слева равно `null`.

<!-- Since `throw` and `return` are expressions in Kotlin, you can also use them on the right-hand side of the Elvis operator. This can be handy, for example, when checking function arguments: -->
Поскольку `throw` и `return` в Kotlin являются выражениями, их тоже можно использовать справа от элвис-оператора.
Это удобно, например, при проверке аргументов функции.

```kotlin
fun foo(node: Node): String? {
    // Проверяет getParent(). Если значение не null, оно присваивается parent. Если null, возвращается null
    val parent = node.getParent() ?: return null
    // Проверяет getName(). Если значение не null, оно присваивается name. Если null, выбрасывается исключение
    val name = node.getName() ?: throw IllegalArgumentException("name expected")
    // ...
}
```

<a name="the-operator"></a>
<a name="not-null-assertion-operator"></a>

<!-- ## Not-null assertion operator -->
## Оператор утверждения not-null

<!-- The not-null assertion operator `!!` converts any value to a non-nullable type. -->
Оператор утверждения not-null `!!` преобразует любое значение в non-null тип.

<!-- When you apply the `!!` operator to a variable whose value is not `null`, it's safely handled as a non-nullable type, and the code executes normally. However, if the value is `null`, the `!!` operator forces it to be treated as non-nullable, which results in an NPE. -->
Когда оператор `!!` применяется к переменной, значение которой не равно `null`, эта переменная безопасно обрабатывается
как значение non-null типа, и код выполняется как обычно. Однако если значение равно `null`, оператор `!!` принудительно
интерпретирует его как non-null значение, что приводит к NPE.

<!-- When `b` is not `null` and the `!!` operator makes it return its non-null value (which is a `String` in this example), it accesses `length` correctly: -->
Когда `b` не равна `null`, оператор `!!` возвращает её non-null значение (в этом примере - `String`) и корректно
обращается к `length`.

```kotlin
fun main() {
    // Присваивает переменной nullable строку
    val b: String? = "Kotlin"
    // Обрабатывает b как non-null значение и обращается к его длине
    val l = b!!.length
    println(l)
    // 6
}
```

<!-- When `b` is `null` and the `!!` operator makes it return its non-null value, and an NPE occurs: -->
Когда `b` равна `null`, оператор `!!` всё равно пытается вернуть её как non-null значение, и возникает NPE.

```kotlin
fun main() {
    // Присваивает nullable переменной null
    val b: String? = null
    // Обрабатывает b как non-null значение и пытается обратиться к его длине
    val l = b!!.length
    println(l)
    // Exception in thread "main" java.lang.NullPointerException
}
```

<!-- The `!!` operator is particularly useful when you are confident that a value is not `null` and there's no chance of getting an NPE, but the compiler cannot guarantee this due to certain rules. In such cases, you can use the `!!` operator to explicitly tell the compiler that the value is not `null`. -->
Оператор `!!` особенно полезен, когда вы уверены, что значение не равно `null` и NPE невозможен, но компилятор не может
гарантировать это из-за определённых правил. В таких случаях `!!` позволяет явно сообщить компилятору, что значение
не является `null`.

<a name="nullable-receiver"></a>

<!-- ## Nullable receiver -->
## Nullable получатель

<!-- You can use extension functions with a nullable receiver type, allowing these functions to be called on variables that might be `null`. -->
Вы можете использовать [функции-расширения](extensions.html#extension-functions) с nullable типом получателя. Такие
функции можно вызывать для переменных, которые могут быть равны `null`.

<!-- By defining an extension function on a nullable receiver type, you can handle `null` values within the function itself instead of checking for `null` at every place where you call the function. -->
Если определить функцию-расширение для nullable типа получателя, `null`-значения можно обрабатывать внутри самой
функции, а не проверять на `null` в каждом месте вызова.

<!-- For example, the `.toString()` extension function can be called on a nullable receiver. When invoked on a `null` value, it safely returns the string `"null"` without throwing an exception: -->
Например, функцию-расширение
[`toString()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/to-string.html) можно вызвать для nullable
получателя. При вызове на значении `null` она безопасно возвращает строку `"null"` и не выбрасывает исключение.

```kotlin
fun main() {
    // Присваивает null nullable объекту Person, который хранится в переменной person
    val person: Person? = null

    // Применяет .toString к nullable переменной person и печатает строку
    println(person.toString())
    // null
}

// Объявляет простой класс Person
data class Person(val name: String)
```

<!-- In the example above, even though `person` is `null`, the `.toString()` function safely returns the string `"null"`. This can be helpful for debugging and logging. -->
В примере выше, несмотря на то что `person` равна `null`, функция `.toString()` безопасно возвращает строку `"null"`.
Это может быть полезно при отладке и логировании.

<!-- If you expect the `.toString()` function to return a nullable string (either a string representation or `null`), use the safe-call operator `?.`. The `?.` operator calls `.toString()` only if the object is not `null`, otherwise it returns `null`: -->
Если вы ожидаете, что функция `.toString()` вернёт nullable строку - строковое представление или `null`, - используйте
оператор безопасного вызова `?.`. Оператор `?.` вызывает `.toString()` только если объект не равен `null`; иначе он
возвращает `null`.

```kotlin
fun main() {
    // Присваивает nullable объект Person переменной
    val person1: Person? = null
    val person2: Person? = Person("Alice")

    // Печатает "null", если person равен null; иначе печатает результат person.toString()
    println(person1?.toString())
    // null
    println(person2?.toString())
    // Person(name=Alice)
}

// Объявляет класс Person
data class Person(val name: String)
```

<!-- The `?.` operator allows you to safely handle potential `null` values while still accessing properties or functions of objects that might be `null`. -->
Оператор `?.` позволяет безопасно обрабатывать потенциальные `null`-значения и при этом обращаться к свойствам или
функциям объектов, которые могут быть равны `null`.

<a name="let-function"></a>

<!-- ## Let function -->
## Функция let

<!-- To handle `null` values and perform operations only on non-null types, you can use the safe call operator `?.` together with the let function. -->
Чтобы обрабатывать `null`-значения и выполнять операции только над non-null типами, можно использовать оператор
безопасного вызова `?.` вместе с функцией
[`let`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/let.html).

<!-- This combination is useful for evaluating an expression, check the result for `null`, and execute code only if it's not `null`, avoiding manual null checks: -->
Такая комбинация полезна, когда нужно вычислить выражение, проверить результат на `null` и выполнить код только в том
случае, если результат не равен `null`, без ручных проверок.

```kotlin
fun main() {
    // Объявляет список nullable строк
    val listWithNulls: List<String?> = listOf("Kotlin", null)
    // Проходит по каждому элементу списка
    for (item in listWithNulls) {
        // Проверяет элемент на null и печатает только non-null значения
        item?.let { println(it) }
        // Kotlin
    }
}
```

<a name="safe-casts"></a>

<!-- ## Safe casts -->
## Безопасные приведения типов

<!-- The regular Kotlin operator for type casts is the `as` operator. However, regular casts can result in an exception if the object is not of the target type. -->
Обычный оператор Kotlin для [приведения типов](typecasts.html) - это оператор `as`. Однако обычное приведение может
завершиться исключением, если объект не относится к целевому типу.

<!-- You can use the `as?` operator for safe casts. It tries to cast a value to the specified type and returns `null` if the value is not of that type: -->
Для безопасных приведений можно использовать оператор `as?`. Он пытается привести значение к указанному типу и
возвращает `null`, если значение к этому типу не относится.

```kotlin
fun main() {
    // Объявляет переменную типа Any, которая может хранить значение любого типа
    val a: Any = "Hello, Kotlin!"
    // Безопасно приводит к Int с помощью оператора 'as?'
    val aInt: Int? = a as? Int
    // Безопасно приводит к String с помощью оператора 'as?'
    val aString: String? = a as? String

    println(aInt)
    // null
    println(aString)
    // "Hello, Kotlin!"
}
```

<!-- The code above prints `null` because `a` is not an `Int`, so the cast fails safely. It also prints `"Hello, Kotlin!"` because it matches the `String?` type, so the safe cast succeeds. -->
Код выше печатает `null`, потому что `a` не является `Int`, поэтому приведение безопасно завершается неудачей. Он также
печатает `"Hello, Kotlin!"`, потому что значение соответствует типу `String?`, и безопасное приведение успешно
выполняется.

<a name="collections-of-a-nullable-type"></a>

<!-- ## Collections of a nullable type -->
## Коллекции nullable типа

<!-- If you have a collection of nullable elements and want to keep only the non-null ones, use the `filterNotNull()` function: -->
Если у вас есть коллекция nullable элементов и вы хотите оставить только non-null элементы, используйте функцию
`filterNotNull()`.

```kotlin
fun main() {
    // Объявляет список с null и non-null целочисленными значениями
    val nullableList: List<Int?> = listOf(1, 2, null, 4)

    // Отфильтровывает null-значения, получая список non-null целых чисел
    val intList: List<Int> = nullableList.filterNotNull()
    println(intList)
    // [1, 2, 4]
}
```

<a name="what-s-next"></a>

<!-- ## What's next? -->
## Что дальше?

<!-- * Learn how to handle nullability in Java and Kotlin.
* Learn about generic types that are definitely non-nullable. -->

* Узнайте, как [обрабатывать null-допустимость при взаимодействии Java и Kotlin](java-interop.html#null-safety-and-platform-types);
* Узнайте про [обобщённые типы](generics.html), которые точно не допускают `null`.
