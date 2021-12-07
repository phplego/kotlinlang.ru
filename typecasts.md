---
type: doc
layout: reference
category: "Syntax"
title: "Приведение и проверка типов"
url: https://kotlinlang.ru/docs/typecasts.html
---

<!-- При переводе статьи оригинальная версия была от 02 September 2021 -->

<!-- Type Checks and Casts -->
# Приведение и проверка типов

<!-- ## `is` and `!is` Operators -->
## Операторы `is` и `!is`

<!-- Use the `is` operator or its negated form `!is` to perform a runtime check that identifies whether an object conforms to a given type: -->
Используйте оператор `is` или его орицание `!is`, чтобы проверить соотвествует ли объект заданному типу
во время исполнения.

```kotlin
if (obj is String) {
    print(obj.length)
}

if (obj !is String) { // то же самое, что и !(obj is String)
    print("Not a String")
}
else {
    print(obj.length)
}
```

<a name="smart-casts"></a>
<!--## Smart Casts-->
## Умные приведения

<!-- In most cases, you don't need to use explicit cast operators in Kotlin because the compiler tracks the
`is`-checks and [explicit casts](#unsafe-cast-operator) for immutable values and inserts (safe) casts automatically when necessary: -->
В большенстве случаев вам не нужно использовать явные приведения в Kotlin,
потому что компилятор отслеживает `is`-проверки и [явные преобразования](#unsafe-cast-operator)
для неизменяемых значений и вставляет (безопасно) приведения автоматически, там, где они нужны.

```kotlin
fun demo(x: Any) {
    if (x is String) {
        print(x.length) // x автоматически преобразовывается в String
    }
}
```

<!-- The compiler is smart enough to know that a cast is safe if a negative check leads to a return: -->
Компилятор достаточно умён для того, чтобы понимать, что приведения безопасны в случаях,
когда проверка на несоответствие типу (`!is`) приводит к выходу из функции:

```kotlin
if (x !is String) return

print(x.length) // x автоматически преобразовывается в String
```

<!-- or if it is on the right-hand side of `&&` or `||`: -->
или в случаях, когда приводимая переменная находится справа от оператора `&&` или `||`:

```kotlin
// x автоматически преобразовывается в String справа от `||`
if (x !is String || x.length == 0) return

// x автоматически преобразовывается в String справа от `&&`
if (x is String && x.length > 0) {
    print(x.length) // x автоматически преобразовывается в String
}
```


<!-- Smart casts work for [`when` expressions](control-flow.md#when-expression)
and [`while` loops](control-flow.md#while-loops) as well: -->
Умные приведения работают вместе с [`when`-выражениями](control-flow.html#when-expression)
и [циклами `while`](control-flow.html#while-loops):

```kotlin
when (x) {
    is Int -> print(x + 1)
    is String -> print(x.length + 1)
    is IntArray -> print(x.sum())
}
```

<!-- Note that smart casts work only when the compiler can guarantee that the variable won't change between the check and the usage.
More specifically, smart casts can be used under the following conditions: -->
Заметьте, что умные приведения работают только тогда, когда компилятор может гарантировать,
что переменная не изменится между проверкой и использованием.
Точнее говоря, умные приведения будут работать:

<!-- * `val` local variables - always, with the exception of [local delegated properties](delegated-properties.md).
* `val` properties - if the property is private or internal or if the check is performed in the same [module](visibility-modifiers.md#modules) where the property is declared. Smart casts cannot be used on open properties or properties that have custom getters.
* `var` local variables - if the variable is not modified between the check and the usage, is not captured in a lambda that modifies it, and is not a local delegated property.
* `var` properties - never, because the variable can be modified at any time by other code. -->

* с локальными `val` переменными - всегда за исключением [локальных делегированных свойств](delegated-properties.md).;
* с `val` свойствами - если поле имеет модификатор доступа `private` или `internal`, или проверка происходит в том же [модуле](visibility-modifiers.html#modules), в котором объявлено это свойство.
Умные приведения неприменимы к публичным свойствам или свойствам, которые имеют переопределённые getter'ы;
* с локальными `var` переменными - если переменная не изменяется между проверкой и использованием,
не захватывается лямбдой, которая её модифицирует и не является локальным делегированным свойством;
* с `var` свойствами - никогда, потому что переменная может быть изменена в любое время другим кодом.

<a name="unsafe-cast-operator"></a>
<!-- ## "Unsafe" cast operator -->
## Оператор "небезопасного" приведения

<!-- Usually, the cast operator throws an exception if the cast isn't possible. And so, it's called *unsafe*.
The unsafe cast in Kotlin is done by the infix operator `as`. -->
Обычно оператор приведения выбрасывает исключение, если приведение невозможно, поэтому мы называем его *небезопасным*.
Небезопасное приведение в Kotlin выполняется с помощью инфиксного оператора `as`:

```kotlin
val x: String = y as String
```

<!-- Note that `null` cannot be cast to `String`, as this type is not [nullable](null-safety.md).
If `y` is null, the code above throws an exception.
To make code like this correct for null values, use the nullable type on the right-hand side of the cast: -->
Заметьте, что `null` не может быть приведен к `String`, так как `String` не является [nullable](null-safety.html),
т.е. если `y` - null, код выше выбросит исключение. Чтобы сделать этот код корректным для null-значений,
используйте nullable-тип в правой части приведения.

```kotlin
val x: String? = y as String?
```

<a name="safe-nullable-cast-operator"></a>
<!-- ## "Safe" (nullable) cast operator -->
## Оператор "безопасного" (nullable) приведения 

<!-- To avoid exceptions, use the *safe* cast operator `as?`, which returns `null` on failure. -->
Чтобы избежать исключения, вы можете использовать оператор *безопасного* приведения `as?`, который возвращает `null` в случае неудачи.

```kotlin
val x: String? = y as? String
```

<!-- Note that despite the fact that the right-hand side of `as?` is a non-null type `String`, the result of the cast is nullable. -->
Заметьте, что несмотря на то, что справа от `as?` стоит non-null тип `String`, результат приведения является nullable.

<a name="type-erasure-and-generic-type-checks"></a>
<!-- ## Type erasure and generic type checks -->
## Стирание и проверка типов у Обобщений (Generics)

<!-- Kotlin ensures type safety for operations involving [generics](generics.md) at compile time,
while, at runtime, instances of generic types don't hold information about their actual type arguments. For example,
`List<Foo>` is erased to just `List<*>`. In general, there is no way to check whether an instance belongs to a generic
type with certain type arguments at runtime. -->
Котлин обеспечивает типобезопасность операций, связанных с [обобщениями](generics.html) на этапе компиляции,
в то время как информация о типе аргумента обобщения недоступна во время выполнения программы.
Например для `List<Foo>` происходит стирание типа, что превращает его в `List<*>`. В связи с чем,
нет способа проверить, принадлежит ли объект конкретному типу во время выполнения программы.

<!-- Because of that, the compiler prohibits `is`-checks that cannot be performed at runtime due to type erasure, such as
`ints is List<Int>` or `list is T` (type parameter). You can, however, check an instance against a [star-projected type](generics.md#star-projections): -->
Учитывая это, компилятор запрещает `is`-проверки, которые не могут быть выполнены во время выполнения программы
из-за стирания типов, например `ints is List<Int>` или `list is T` (параметризированный тип).
Однако у вас есть возможность произвести проверку со ["звёздными" проекциями](generics.html#star-projections).

```kotlin
if (something is List<*>) {
    something.forEach { println(it) } // Элементы типа `Any?`
}
```

<!-- Similarly, when you already have the type arguments of an instance checked statically (at compile time),
you can make an `is`-check or a cast that involves the non-generic part of the type. Note that
angle brackets are omitted in this case: -->
Таким же образом, когда у вас есть статически определенный тип аргумента, вы можете произвести `is`-проверку
или приведение с необобщенной частью типа. Заметьте, что в данном случае угловые скобки пропущены:

```kotlin
fun handleStrings(list: List<String>) {
    if (list is ArrayList) {
        // `list` приводится к `ArrayList<String>` засчет "умного приведения"
    }
}
```

<!-- The same syntax but with the type arguments omitted can be used for casts that do not take type arguments into account: `list as ArrayList`.  -->
Аналогичный синтаксис, но с пропущенным типом аргумента может использоваться для приведений,
которые не принимают типы аргументы: `list as ArrayList`.

<!-- Inline functions with [reified type parameters](inline-functions.md#reified-type-parameters) have their actual type arguments
inlined at each call site. This enables `arg is T` checks for the type parameters, but if `arg` is an instance of a
generic type itself, *its* type arguments are still erased. -->
Встроенные (inline) функции с [параметрами вещественного типа](inline-functions.html#reified-type-parameters) имеют свои аргументы типа,
встроенные на каждый момент вызова, что позволяет `arg is T` проверять параметризованный тип,
но если `arg` является объектом обобщенного типа, его аргумент типа по-прежнему стираются.

```kotlin
inline fun <reified A, reified B> Pair<*, *>.asPairOf(): Pair<A, B>? {
    if (first !is A || second !is B) return null
    return first as A to second as B
}

val somePair: Pair<Any?, Any?> = "items" to listOf(1, 2, 3)

val stringToSomething = somePair.asPairOf<String, Any>()
val stringToInt = somePair.asPairOf<String, Int>()
val stringToList = somePair.asPairOf<String, List<*>>()
val stringToStringList = somePair.asPairOf<String, List<String>>() // Нарушает типобезопасность!

fun main() {
    println("stringToSomething = " + stringToSomething)
    println("stringToInt = " + stringToInt)
    println("stringToList = " + stringToList)
    println("stringToStringList = " + stringToStringList)
    //println(stringToStringList?.second?.forEach() {it.length}) // Это вызовет исключение ClassCastException, так как элементы списка не являются строками
}
```

<a name="unchecked-casts"></a>
<!-- ## Unchecked casts -->
## Непроверяемые (Unchecked) приведения

<!-- As established above, type erasure makes checking the actual type arguments of a generic type instance impossible at runtime.
Additionally, generic types in the code might not be connected to each other closely enough for the compiler to ensure
type safety. --> 
Как упоминалось выше, стирание типов делает невозможным проверку типа аргумента обобщения на этапе выполнения, и обобщенные типы в коде могут быть недостаточно связаны друг с другом, чтобы компилятор обеспечил типобезопасность.

<!-- Even so, sometimes we have high-level program logic that implies type safety instead. For example: -->
Тем не менее, иногда мы имеем программную логику высокого уровня, которая подразумевает типобезопасность.

```kotlin 
fun readDictionary(file: File): Map<String, *> = file.inputStream().use {
    TODO("Прочитать сопоставление строк с произвольными элементами.")
}

// Мы сохранили словарь (map) `Int`ов в файл
val intsFile = File("ints.dictionary")

// Warning: Unchecked cast: `Map<String, *>` to `Map<String, Int>`
val intsDictionary: Map<String, Int> = readDictionary(intsFile) as Map<String, Int>
```

<!-- A warning appears for the cast in the last line. The compiler can't fully check it at runtime and provides
no guarantee that the values in the map are `Int`. -->
Компилятор выдает предупреждение для приведения в последней строке. Приведение не может быть полностью проверено во время выполнения и нет дает гарантии, что значения в словаре (map) являются `Int`.

<!-- To avoid unchecked casts, you can redesign the program structure. In the example above, you could use the
`DictionaryReader<T>` and `DictionaryWriter<T>` interfaces with type-safe implementations for different types.
You can introduce reasonable abstractions to move unchecked casts from the call site to the implementation details.
Proper use of [generic variance](generics.md#variance) can also help. -->
Чтобы избежать непроверяемые приведения, вы можете изменить структуру программы: в примере выше
возможно объявить интерфейсы `DictionaryReader<T>` и `DictionaryWriter<T>`
с типобезопасными имплементациями для различных типов. Правильное использование
[вариативности обобщений](generics.html#variance) также может помочь.
 
<!-- For generic functions, using [reified type parameters](inline-functions.md#reified-type-parameters) makes casts
like `arg as T` checked, unless `arg`'s type has *its own* type arguments that are erased. -->
Для обобщенных функций, используемых встроенные (inline) функции с
[параметрами вещественного типа](inline-functions.html#reified-type-parameters) приведение типа `arg as T` является проверяемым,
до тех пор, пока тип `arg` не имеет *свои* аргументы типа, которые были стерты.

<!-- An unchecked cast warning can be suppressed by [annotating](annotations.md) the statement or the
declaration where it occurs with `@Suppress("UNCHECKED_CAST")`: -->
Предупреждение о непроверяемом приведении можно убрать используя [аннотации](annotations.html) `@Suppress("UNCHECKED_CAST")`.

```kotlin
inline fun <reified T> List<*>.asListOfType(): List<T>? =
    if (all { it is T })
        @Suppress("UNCHECKED_CAST")
        this as List<T> else
        null
```

<!-- >**On the JVM**: [array types](basic-types.md#arrays) (`Array<Foo>`) retain information about the erased type of
>their elements, and type casts to an array type are partially checked: the
>nullability and actual type arguments of the element type are still erased. For example,
>the cast `foo as Array<List<String>?>` will succeed if `foo` is an array holding any `List<*>`, whether it is nullable or not. -->
В JVM, [массивы](basic-types.html#arrays) (`Array<Foo>`) сохраняют информацию о стираемом типе их элементов,
и приведение типов к массиву частично проверяется: nullability и фактические аргументы
для параметризированных элементов массива все еще стираются.
Например, приведение `foo as Array <List <String>?>` будет успешным, если `foo` является массивом, содержащим какой-либо `List <*>`, null или нет.
