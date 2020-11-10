---
type: doc
layout: reference
category: "Syntax"
title: "Приведение и проверка типов"
url: https://kotlinlang.ru/docs/reference/typecasts.html
---

<!--# Type Checks and Casts-->
# Приведение и проверка типов

<!--## `is` and `!is` Operators-->
## Операторы `is` и `!is`

<!--We can check whether an object conforms to a given type at runtime by using the `is` operator or its negated form `!is`:-->
Мы можем проверить принадлежит ли объект к какому-либо типу во время исполнения с помощью оператора `is` или его отрицания `!is`:

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

<!--In many cases, one does not need to use explicit cast operators in Kotlin, because the compiler tracks the
`is`-checks for immutable values and inserts (safe) casts automatically when needed:-->
Во многих случаях в Kotlin вам не нужно использовать явные приведения, потому что компилятор следит за `is`-проверками для 
неизменяемых значений и вставляет приведения автоматически, там, где они нужны:

```kotlin
fun demo(x: Any) {
    if (x is String) {
        print(x.length) // x автоматически преобразовывается в String
    }
}
```

<!--The compiler is smart enough to know a cast to be safe if a negative check leads to a return:-->
Компилятор достаточно умён для того, чтобы делать автоматические приведения в случаях, когда проверка на несоответствие типу (`!is`)
приводит к выходу из функции:

```kotlin
    if (x !is String) return
    print(x.length) // x автоматически преобразовывается в String
```

<!--or in the right-hand side of `&&` and `||`:-->
или в случаях, когда приводимая переменная находится справа от оператора `&&` или `||`:

```kotlin
    // x автоматически преобразовывается в String справа от `||`
    if (x !is String || x.length == 0) return

    // x автоматически преобразовывается в String справа от `&&`
    if (x is String && x.length > 0) {
        print(x.length) // x автоматически преобразовывается в String
    }
```


<!--Such _smart casts_ work for [*when*{: .keyword }-expressions](control-flow.html#when-expression)
and [*while*{: .keyword }-loops](control-flow.html#while-loops) as well:-->
Такие _умные приведения_  работают вместе с [*when*-выражениями](control-flow.html#when-expression) и [циклами *while*](control-flow.html#while-loops):

```kotlin
when (x) {
    is Int -> print(x + 1)
    is String -> print(x.length + 1)
    is IntArray -> print(x.sum())
}
```

<!--Note that smart casts do not work when the compiler cannot guarantee that the variable cannot change between the check and the usage.
More specifically, smart casts are applicable according to the following rules:-->
Заметьте, что умные приведения не работают, когда компилятор не может гарантировать, что переменная не изменится между проверкой и использованием.
Более конкретно, умные приведения будут работать:

<!--  * *val*{: .keyword } local variables - always;
  * *val*{: .keyword } properties - if the property is private or internal or the check is performed in the same module where the property is declared. Smart casts aren't applicable to open properties or properties that have custom getters;
  * *var*{: .keyword } local variables - if the variable is not modified between the check and the usage and is not captured in a lambda that modifies it;
  * *var*{: .keyword } properties - never (because the variable can be modified at any time by other code).-->
  
* с локальными *val* переменными - всегда;
* с *val* свойствами - если поле имеет модификатор доступа `private` или `internal`, или проверка происходит в том же [модуле](visibility-modifiers.html#modules), в котором объявлено это свойство. 
Умные приведения неприменимы к публичным свойствам или свойствам, которые имеют переопределённые getter'ы;
* с локальными *var* переменными - если переменная не изменяется между проверкой и использованием и не захватывается лямбдой, которая её модифицирует;
* с *var* свойствами - никогда (потому что переменная может быть изменена в любое время другим кодом).

<a name="unsafe-cast"></a>

<!--## "Unsafe" cast operator-->
## Оператор "небезопасного" приведения

<!--Usually, the cast operator throws an exception if the cast is not possible. Thus, we call it *unsafe*.
The unsafe cast in Kotlin is done by the infix operator *as*{: .keyword } (see [operator precedence](grammar.html#precedence)):-->
Обычно оператор приведения выбрасывает исключение, если приведение невозможно, поэтому мы называем его *небезопасным*.
Небезопасное приведение в Kotlin выполняется с помощью инфиксного оператора *as* (см. [приоритеты операторов](grammar.html#precedence)):

```kotlin
val x: String = y as String
```

<!--Note that *null*{: .keyword } cannot be cast to `String` as this type is not [nullable](null-safety.html),
i.e. if `y` is null, the code above throws an exception.
In order to match Java cast semantics we have to have nullable type at cast right hand side, like-->
Заметьте, что *null* не может быть приведен к `String`, так как `String` не является [nullable](null-safety.html),
т.е. если `y` - null, код выше выбросит исключение. Чтобы соответствовать семантике приведений в Java, нам нужно указать nullable тип в правой части приведения:

```kotlin
val x: String? = y as String?
```

<a name="nullable-cast"></a>

<!--## "Safe" (nullable) cast operator-->
## Оператор "безопасного" (nullable) приведения 

<!--To avoid an exception being thrown, one can use a *safe* cast operator *as?*{: .keyword } that returns *null*{: .keyword } on failure:-->
Чтобы избежать исключения, вы можете использовать оператор *безопасного* приведения *as?*, который возвращает *null* в случае неудачи:

```kotlin
val x: String? = y as? String
```

<!--Note that despite the fact that the right-hand side of *as?*{: .keyword } is a non-null type `String` the result of the cast is nullable.-->
Заметьте, что несмотря на то, что справа от *as?* стоит non-null тип `String`, результат приведения является nullable.

<a name="type-erasure"></a>

<!--## Type erasure and generic type checks-->
## Стирание и проверка типов у Обобщений (Generics)

<!--Kotlin ensures type safety of operations involving [generics](generics.html) at compile time,
while, at runtime, instances of generic types hold no information about their actual type arguments. For example, 
`List<Foo>` is erased to just `List<*>`. In general, there is no way to check whether an instance belongs to a generic 
type with certain type arguments at runtime. -->
Котлин обеспечивает типобезопасность операций, связанных с [обобщениями](generics.html) на этапе компиляции(compile time), в то время как информация о типе аргумента обобщения недоступна во время выполнения программы. Например для `List<Foo>` происходит стирание типа, что превращает его в `List<*>`. В связи с чем, нет способа проверить, принадлежит ли объект конкретному типу во время выполнения программы.

<!--Given that, the compiler prohibits *is*{: .keyword }-checks that cannot be performed at runtime due to type erasure, such as 
`ints is List<Int>` or `list is T` (type parameter). You can, however, check an instance against a [star-projected type](generics.html#star-projections):-->
Учитывая это, компилятор запрещает **is**-проверки, которые не могут быть выполнены во время выполнения программы из-за стирания типов, например `ints is List<Int>` или `list is T` (параметризированный тип). Однако у вас есть возможность произвести проверку со ["Звёздными" проекциями](generics.html#star-projections):

```kotlin
if (something is List<*>) {
    something.forEach { println(it) } // Элементы типа `Any?`
}
```

<!--Similarly, when you already have the type arguments of an instance checked statically (at compile time),
you can make an *is*{: .keyword }-check or a cast that involves the non-generic part of the type. Note that 
angle brackets are omitted in this case:-->
Таким же образом, когда у вас есть статически определенный тип аргумента, вы можете произвести **is**-проверку или приведение с не-обобщенной частью типа. Заметье, что в данном случае угловые скобки пропущены:

```kotlin
fun handleStrings(list: List<String>) {
    if (list is ArrayList) {
        // `list` приводится к `ArrayList<String>` засчет "умного приведения"
    }
}
```

<!--The same syntax with omitted type arguments can be used for casts that do not take type arguments into account: `list as ArrayList`. -->
Аналогичный синтаксис, с пропущенным типом аргумента может использоваться для приведений, которые не принимают типы аргументы: `list as ArrayList`

<!--Inline functions with [reified type parameters](inline-functions.html#reified-type-parameters) have their actual type arguments
 inlined at each call site, which enables `arg is T` checks for the type parameters, but if `arg` is an instance of a 
generic type itself, *its* type arguments are still erased. Example:-->
Встроенные (inline) функции с [параметрами вещественного типа](inline-functions.html#параметры-вещественного-типа) имеют свои аргументы типа, встроенные на каждый момент вызова, что позволяет `arg is T` проверку параметризованного типа, но если `arg` является объектом обобщенного типа, его аргумент типа по-прежнему стирается. Пример:

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
```

<a name="unchecked-casts"></a>

<!--## Unchecked casts-->
## Непроверяемые (Unchecked) приведения

<!--As said above, type erasure makes checking actual type arguments of a generic type instance impossible at runtime, and 
generic types in the code might be connected to each other not closely enough for the compiler to ensure 
type safety. -->
Как упоминалось выше, стирание типов делает невозможным проверку типа аргумента обобщения на этапе выполнения, и обобщенные типы в коде могут быть недостаточно связаны друг с другом, чтобы компилятор обеспечил типобезопасность.

<!--Even so, sometimes we have high-level program logic that implies type safety instead. For example:-->
Тем не менее, иногда мы имеем программную логику высокого уровня, которая вместо этого подразумевает типобезопасность. Например:

```kotlin 
fun readDictionary(file: File): Map<String, *> = file.inputStream().use { 
    TODO("Прочитать сопоставление строк с произвольными элементами.")
}

// Мы сохранили словарь(map) `Int`ов в файл
val intsFile = File("ints.dictionary")

// Warning: Unchecked cast: `Map<String, *>` to `Map<String, Int>`
val intsDictionary: Map<String, Int> = readDictionary(intsFile) as Map<String, Int>
```

<!--The compiler produces a warning for the cast in the last line. The cast cannot be fully checked at runtime and provides 
no guarantee that the values in the map are `Int`.-->
Компилятор выдает предупреждение для приведения в последней строке. Приведение не может быть полностью проверено во время выполнения и нет дает гарантии, что значения в словаре (map) являются `Int`.

<!--To avoid unchecked casts, you can redesign the program structure: in the example above, there could be interfaces
 `DictionaryReader<T>` and `DictionaryWriter<T>` with type-safe implementations for different types. 
 You can introduce reasonable abstractions to move unchecked casts from calling code to the implementation details.
 Proper use of [generic variance](generics.html#variance) can also help. -->
 Чтобы избежать непроверяемые приведения, вы можете изменить структуру программы: в примере выше, возможно объявить интерфейсы `DictionaryReader<T>` и `DictionaryWriter<T>` с типобезопасными имплементациями для типизированного типа. Правильно использование [вариативности обобщений](generics.html#Вариативность) также может помочь.
 
<!--For generic functions, using [reified type parameters](inline-functions.html#reified-type-parameters) makes the casts 
such as `arg as T` checked, unless `arg`'s type has *its own* type arguments that are erased.-->
Для обобщенных функций, используемых встроенные (inline) функции с [параметрами вещественного типа](inline-functions.html#Параметры-вещественного-типа) приведение типа `arg as T` является проверяемым, до тех пор, если тип` arg` не имеет **свои** аргументы типа, которые были стерты.

<!--An unchecked cast warning can be suppressed by [annotating](annotations.html#annotations) the statement or the 
declaration where it occurs with `@Suppress("UNCHECKED_CAST")`:-->
Предупреждение о непроверяемом приведении можно убрать используя [аннотации](annotations.html)

```kotlin
inline fun <reified T> List<*>.asListOfType(): List<T>? =
    if (all { it is T })
        @Suppress("UNCHECKED_CAST")
        this as List<T> else
        null
```

<!--On the JVM, the [array types](basic-types.html#arrays) (`Array<Foo>`) retain the information about the erased type of 
their elements, and the type casts to an array type are partially checked: the 
nullability and actual type arguments of the elements type are still erased. For example, 
the cast `foo as Array<List<String>?>` will succeed if `foo` is an array holding any `List<*>`, nullable or not.-->
В JVM, [массивы](basic-types.html#arrays) сохраняют информацию о стираемом типе их элементов, а также приведение типов к массиву частично проверяется: nullability и фактические аргументы для параметризированных элементов массива все еще стираются. Например, приведение `foo as Array <List <String>?>` будет успешным, если `foo` является массивом, содержащим какой-либо` List <*> `, null или нет.
