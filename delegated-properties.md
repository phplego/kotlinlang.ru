---
type: doc
layout: reference
category: "Syntax"
title: "Делегированные свойства"
url: https://kotlinlang.ru/docs/reference/delegated-properties.html
---

<!--# Delegated Properties-->
<!--https://habrahabr.ru/company/JetBrains/blog/183444/-->
# Делегированные свойства
_За помощь в переводе спасибо [официальному блогу JetBrains на Хабрахабре](https://habrahabr.ru/company/JetBrains/blog/183444/)_

<!--There are certain common kinds of properties, that, though we can implement them manually every time we need them, 
would be very nice to implement once and for all, and put into a library. Examples include-->
Существует несколько основных видов свойств, которые мы реализовываем каждый раз вручную в случае их надобности. Однако намного удобнее было бы реализовать их раз и навсегда и положить в какую-нибудь библиотеку. Примеры таких свойств:

<!--* lazy properties: the value gets computed only upon first access,
* observable properties: listeners get notified about changes to this property,
* storing properties in a map, not in separate field each.-->
* ленивые свойства (lazy properties): значение вычисляется один раз, при первом обращении
* свойства, на события об изменении которых можно подписаться (observable properties)
* свойства, хранимые в ассоциативном списке, а не в отдельных полях

<!--To cover these (and other) cases, Kotlin supports _delegated properties_:-->
Для таких случаев, Kotlin поддерживает _делегированные свойства_:

```kotlin
class Example {
    var p: String by Delegate()
}
```

<!--The syntax is: `val/var <property name>: <Type> by <expression>`. The expression after *by*{:.keyword} is the _delegate_, 
because `get()` (and `set()`) corresponding to the property will be delegated to its `getValue()` and `setValue()` methods.
Property delegates don’t have to implement any interface, but they have to provide a `getValue()` function (and `setValue()` - for *var*{:.keyword}'s).
For example: -->

Их синтаксис выглядит следующим образом: `val/var <имя свойства>: <Тип> by <выражение>`. Выражение после *by* — _делегат_: обращения  (`get()`, `set()`) к свойству будут обрабатываться этим выражением. 
Делегат не обязан реализовывать какой-то интерфейс, достаточно, чтобы у него были методы `getValue()` и `setValue()` с определённой сигнатурой: 

```kotlin
class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, спасибо за делегирование мне '${property.name}'!"
    }
 
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value было присвоено значению '${property.name} в $thisRef.'")
    }
}
```

<!--When we read from `p` that delegates to an instance of `Delegate`, the `getValue()` function from `Delegate` is called,
so that its first parameter is the object we read `p` from and the second parameter holds a description of `p` itself 
(e.g. you can take its name). For example: -->

Когда мы читаем значение свойства `p`, вызывается метод `getValue()` класса `Delegate`, причем первым параметром ей передается тот объект, у которого запрашивается свойство `p`, а вторым — объект-описание самого свойства p (у него можно, в частности, узнать имя свойства). Например:

```kotlin
val e = Example()
println(e.p)
```

<!--This prints -->
Этот код выведет

```kotlin
Example@33a17727, спасибо за делегирование мне ‘p’!
```
 
<!--Similarly, when we assign to `p`, the `setValue()` function is called. The first two parameters are the same, and the third holds the value being assigned:-->
Похожим образом, когда мы обращаемся к `p`, вызывается метод `setValue()`. Два первых параметра — такие же, как у get(), а третий — присваиваемое значение свойства:

```kotlin
e.p = "NEW"
```

<!--This prints-->
Этот код выведет
 
```kotlin
NEW было присвоено значению ‘p’ в Example@33a17727.
```

<!--The specification of the requirements to the delegated object can be found [below](delegated-properties.html#property-delegate-requirements).-->
Спецификация требований к делегированным свойствам может быть найдена [ниже](delegated-properties.html#property-delegate-requirements).

<!--Note that since Kotlin 1.1 you can declare a delegated property inside a function or code block, it shouldn't necessarily be a member of a class.
Below you can find [the example](delegated-properties.html#local-delegated-properties-since-11).-->
Заметьте, что начиная с версии Kotlin 1.1, вы можете объявлять делегированные свойства внутри функций или блоков кода, а не только внутри классов. Снизу вы можете найти пример.

<!--## Standard Delegates-->
## Стандартные делегаты

<!--The Kotlin standard library provides factory methods for several useful kinds of delegates.-->
Стандартная библиотека Kotlin предоставляет несколько полезных видов делегатов:

### Ленивые свойства (lazy properties)

<!--`lazy()` is a function that takes a lambda and returns an instance of `Lazy<T>` which can serve as a delegate for implementing a lazy property:
the first call to `get()` executes the lambda passed to `lazy()` and remembers the result, 
subsequent calls to `get()` simply return the remembered result. -->
`lazy()` это функция, которая принимает лямбду и возвращает экземпляр класса `Lazy<T>`, который служит делегатом для реализации ленивого свойства: первый вызов `get()` запускает лямбда-выражение, переданное `lazy()` в качестве аргумента, и запоминает полученное значение, а последующие вызовы просто возвращают вычисленное значение. 


```kotlin
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}

fun main(args: Array<String>) {
    println(lazyValue)
    println(lazyValue)
}
```

Этот код выведет:

```
computed!
Hello
Hello
```

<!--By default, the evaluation of lazy properties is **synchronized**: the value is computed only in one thread, and all threads
will see the same value. If the synchronization of initialization delegate is not required, so that multiple threads
can execute it simultaneously, pass `LazyThreadSafetyMode.PUBLICATION` as a parameter to the `lazy()` function. 
And if you're sure that the initialization will always happen on a single thread, you can use `LazyThreadSafetyMode.NONE` mode, 
which doesn't incur any thread-safety guarantees and the related overhead.-->
По умолчанию вычисление ленивых свойств **синхронизировано**: значение вычисляется только в одном потоке выполнения, и все остальные потоки могут видеть одно и то же значение. Если синхронизация не требуется, передайте `LazyThreadSafetyMode.PUBLICATION` в качестве параметра в функцию `lazy()`, тогда несколько потоков смогут исполнять вычисление одновременно. Или если вы уверены, что инициализация всегда будет происходить в одном потоке исполнения, вы можете использовать режим `LazyThreadSafetyMode.NONE`, который не гарантирует никакой потокобезопасности.


### Observable свойства

<!--`Delegates.observable()` takes two arguments: the initial value and a handler for modifications.
The handler gets called every time we assign to the property (_after_ the assignment has been performed). It has three
parameters: a property being assigned to, the old value and the new one:-->
Функция `Delegates.observable()` принимает два аргумента: начальное значение свойства и обработчик (лямбда), который вызывается при изменении свойства. У обработчика три параметра: описание свойства, которое изменяется, старое значение и новое значение.

```kotlin
import kotlin.properties.Delegates

class User {
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}

fun main(args: Array<String>) {
    val user = User()
    user.name = "first"
    user.name = "second"
}
```

<!--This example prints:-->
Этот код выведет:

```
<no name> -> first
first -> second
```

Если Вам нужно иметь возможность запретить присваивание некоторых значений, используйте функцию `vetoable()` вместо `observable()`.

<!--## Storing Properties in a Map-->
## Хранение свойств в ассоциативном списке

<!--One common use case is storing the values of properties in a map.
This comes up often in applications like parsing JSON or doing other “dynamic” things.
In this case, you can use the map instance itself as the delegate for a delegated property.-->
Один из самых частых сценариев использования делегированных свойств заключается в хранении свойств в ассоциативном списке. Это полезно в "динамическом" коде, например, при работе с JSON:

```kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
```

<!--In this example, the constructor takes a map:-->
В этом примере конструктор принимает ассоциативный список

```kotlin
val user = User(mapOf(
    "name" to "John Doe",
    "age"  to 25
))
```

<!--Delegated properties take values from this map (by the string keys – names of properties):-->
Делегированные свойства берут значения из этого ассоциативного списка (по строковым ключам)


```kotlin
println(user.name) // Prints "John Doe"
println(user.age)  // Prints 25
```

<!--This works also for *var*{:.keyword}’s properties if you use a `MutableMap` instead of read-only `Map`:-->
Также, если вы используете `MutableMap` вместо `Map`, поддерживаются изменяемые свойства (var):

```kotlin
class MutableUser(val map: MutableMap<String, Any?>) {
    var name: String by map
    var age: Int     by map
}
```

<!--## Local Delegated Properties (since 1.1)-->
## Локальные делегированные свойства (с версии 1.1)

<!--You can declare local variables as delegated properties.
For instance, you can make a local variable lazy:-->
Вы можете объявить локальные переменные как делегированные свойства. Например, вы можете сделать локальную переменную ленивой:

```kotlin
fun example(computeFoo: () -> Foo) {
    val memoizedFoo by lazy(computeFoo)

    if (someCondition && memoizedFoo.isValid()) {
        memoizedFoo.doSomething()
    }
}
```

<!--The `memoizedFoo` variable will be computed on the first access only.
If `someCondition` fails, the variable won't be computed at all.-->
Переменная `memoizedFoo` будет вычислена только при первом обращении к ней.
Если условие `someCondition` будет ложно, значение переменной не будет вычислено вовсе.

<a name="property-delegate-requirements"></a>

<!--## Property Delegate Requirements-->
## Требования к делегированным свойствам

<!--Here we summarize requirements to delegate objects. -->
Здесь приведены требования к объектам-делегатам. 

<!--For a **read-only** property (i.e. a *val*{:.keyword}), a delegate has to provide a function named `getValue` that takes the following parameters:-->
Для **read-only** свойства (например *val*), делегат должен предоставлять функцию `getValue`, которая принимает следующие параметры:

<!--* `thisRef` - must be the same or a supertype of the _property owner_ (for extension properties - the type being extended),
* `property` - must be of type `KProperty<*>` or its supertype, this function must return the same type as property (or its subtype).-->

* `thisRef` — должен иметь такой же тип или быть наследником типа _хозяина свойства_ (для [расширений](extensions.html) —  тип, который расширяется)
* `property` — должен быть типа `KProperty<*>` или его родительского типа. Эта функция должна возвращать значение того же типа, что и свойство (или его родительского типа).

<!--For a **mutable** property (a *var*{:.keyword}), a delegate has to _additionally_ provide a function named `setValue` that takes the following parameters:-->
Для **изменяемого** свойства (*var*) делегат должен _дополнительно_ предоставлять функцию `setValue`, которая принимает следующие параметры:
 
<!--* `thisRef` — same as for `getValue()`,
* `property` — same as for `getValue()`,
* new value — must be of the same type as a property or its supertype.-->
* `thisRef` — то же что и у `getValue()`,
* `property` — то же что и у `getValue()`,
* new value — должен быть того же типа, что и свойство (или его родительского типа).
 
<!--`getValue()` and/or `setValue()` functions may be provided either as member functions of the delegate class or extension functions.
The latter is handy when you need to delegate property to an object which doesn't originally provide these functions.
Both of the functions need to be marked with the `operator` keyword.-->
Функции `getValue()` и/или `setValue()` могут быть предоставлены либо как члены класса-делегата, либо как его [расширения](extensions.html). Последнее полезно когда вам нужно делегировать свойство объекту, который изначально не имеет этих функций. Обе эти функции должны быть отмечены с помощью ключевого слова `operator`.

<!--The delegate class may implement one of the interfaces `ReadOnlyProperty` and `ReadWriteProperty` containing the required `operator` methods.
These interfaces are declared in the Kotlin standard library:-->

Эти интерфейсы объявлены в стандартной библиотеке Kotlin:

```kotlin
interface ReadOnlyProperty<in R, out T> {
    operator fun getValue(thisRef: R, property: KProperty<*>): T
}

interface ReadWriteProperty<in R, T> {
    operator fun getValue(thisRef: R, property: KProperty<*>): T
    operator fun setValue(thisRef: R, property: KProperty<*>, value: T)
}
```

<!--### Translation Rules-->
### Translation Rules

<!--Under the hood for every delegated property the Kotlin compiler generates an auxiliary property and delegates to it.
For instance, for the property `prop` the hidden property `prop$delegate` is generated, and the code of the accessors simply delegates to this additional property:-->
Для каждого делегированного свойства компилятор Kotlin "за кулисами" генерирует вспомогательное свойство и делегирует его. Например, для свойства `prop` генерируется скрытое свойство `prop$delegate`, и исполнение геттеров и сеттеров просто делегируется этому дополнительному свойству:

```kotlin
class C {
    var prop: Type by MyDelegate()
}

// этот код генерируется компилятором:
class C {
    private val prop$delegate = MyDelegate()
    var prop: Type
        get() = prop$delegate.getValue(this, this::prop)
        set(value: Type) = prop$delegate.setValue(this, this::prop, value)
}
```
<!--The Kotlin compiler provides all the necessary information about `prop` in the arguments: the first argument `this` refers to an instance of the outer class `C` and `this::prop` is a reflection object of the `KProperty` type describing `prop` itself.-->

Компилятор Kotlin предоставляет всю необходимую информацию о `prop` в аргументах: первый аргумент `this` ссылается на экземпляр внешнего класса `C` и `this::prop` reflection-объект типа `KProperty`, описывающий сам `prop`.

<!--Note that the syntax `this::prop` to refer a [bound callable reference](reflection.html#bound-function-and-property-references-since-11) in the code directly is available only since Kotlin 1.1.-->
Заметьте, что синтаксис `this::prop` для обращения к [bound callable reference](http://kotlinlang.org/docs/reference/reflection.html#bound-function-and-property-references-since-11) напрямую в коде программы доступен только с Kotlin версии 1.1

<!--### Providing a delegate (since 1.1)-->
### Предоставление делегата
_Примечание: Предоставление делегата доступно в Kotlin начиная с версии 1.1_

<!--By defining the `provideDelegate` operator you can extend the logic of creating the object to which the property implementation is delegated.
If the object used on the right hand side of `by` defines `provideDelegate` as a member or extension function, that function will be
called to create the property delegate instance.-->
С помощью определения оператора `provideDelegate` вы можете расширить логику создания объекта, которому будет делегировано свойство. Если объект, который используется справа от `by`, определяет `provideDelegate` как член или как [расширение](extensions.html), эта функция будет вызвана для создания экземпляра делегата.

<!--One of the possible use cases of `provideDelegate` is to check property consistency when the property is created, not only in its getter or setter.-->
Один из возможных юзкейсов `provideDelegate` — это проверка состояния свойства при его создании.

<!--For example, if you want to check the property name before binding, you can write something like this:-->
Например, если вы хотите проверить имя свойства перед связыванием, вы можете написать что-то вроде:

```kotlin
class ResourceLoader<T>(id: ResourceID<T>) {
    operator fun provideDelegate(
            thisRef: MyUI,
            prop: KProperty<*>
    ): ReadOnlyProperty<MyUI, T> {
        checkProperty(thisRef, prop.name)
        // создание делегата
    }

    private fun checkProperty(thisRef: MyUI, name: String) { ... }
}

fun <T> bindResource(id: ResourceID<T>): ResourceLoader<T> { ... }

class MyUI {
    val image by bindResource(ResourceID.image_id)
    val text by bindResource(ResourceID.text_id)
}
```

<!--The parameters of `provideDelegate` are the same as for `getValue`:-->
`provideDelegate` имеет те же параметры, что и `getValue`:

<!--* `thisRef` - must be the same or a supertype of the _property owner_ (for extension properties - the type being extended),
* `property` - must be of type `KProperty<*>` or its supertype.-->
* `thisRef` — должен иметь такой же тип, или быть наследником типа _хозяина свойства_ (для [расширений](extensions.html) —  тип, который расширяется)
* `property` — должен быть типа `KProperty<*>` или его родительского типа. Эта функция должна возвращать значение того же типа, что и свойство (или его родительского типа)

<!--The `provideDelegate` method is called for each property during the creation of the `MyUI` instance, and it performs the necessary validation right away.-->
Метод `provideDelegate` вызывается для каждого свойства во время создания экземпляра `MyUI`, и сразу совершает необходимые проверки.

<!--Without this ability to intercept the binding between the property and its delegate, to achieve the same functionality
you'd have to pass the property name explicitly, which isn't very convenient:-->
Не будь этой возможности внедрения между свойством и делегатом, для достижения той же функциональности вам бы пришлось передавать имя свойства явно, что не очень удобно:

```kotlin
// Проверяем имя свойства без "provideDelegate"
class MyUI {
    val image by bindResource(ResourceID.image_id, "image")
    val text by bindResource(ResourceID.text_id, "text")
}

fun <T> MyUI.bindResource(
        id: ResourceID<T>,
        propertyName: String
): ReadOnlyProperty<MyUI, T> {
   checkProperty(this, propertyName)
   // создание делегата
}
```

<!--In the generated code, the `provideDelegate` method is called to initialize the auxiliary `prop$delegate` property.
Compare the generated code for the property declaration `val prop: Type by MyDelegate()` with the generated code 
[above](delegated-properties.html#translation-rules) (when the `provideDelegate` method is not present):-->
В сгенерированном коде метод `provideDelegate` вызывается для инициализации вспомогательного свойства `prop$delegate`.
Сравните сгенерированный для объявления свойства код `val prop: Type by MyDelegate()` со сгенерированным кодом из Transaction Rules (когда `provideDelegate` не представлен):

```kotlin
class C {
    var prop: Type by MyDelegate()
}

// этот код будет сгенерирован компилятором 
// когда функция 'provideDelegate' доступна:
class C {
    // вызываем "provideDelegate" для создания вспомогательного свойства "delegate"
    private val prop$delegate = MyDelegate().provideDelegate(this, this::prop)
    val prop: Type
        get() = prop$delegate.getValue(this, this::prop)
}
```

<!--Note that the `provideDelegate` method affects only the creation of the auxiliary property and doesn't affect the code generated for getter or setter.-->
Заметьте, что метод `provideDelegate` влияет только на создание вспомогательного свойства и не влияет на код, генерируемый геттером или сеттером.
