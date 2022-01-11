---
type: doc
layout: reference
category: "Syntax"
title: "Делегированные свойства"
url: https://kotlinlang.ru/docs/delegated-properties.html
---

<!-- При переводе статьи оригинальная версия была от 16 November 2021 -->
<!-- Статья на хабре устарела, но всё равно помогает https://habrahabr.ru/company/JetBrains/blog/183444/-->

<!--# Delegated Properties-->
# Делегированные свойства

*За помощь в переводе спасибо [официальному блогу JetBrains на Хабре](https://habrahabr.ru/company/JetBrains/blog/183444/)*

<!-- With some common kinds of properties, even though you can implement them manually every time you need them,
it is more helpful to implement them once, add them to a library, and reuse them later. For example: -->
Существует несколько основных видов свойств, которые вы реализовываете каждый раз вручную в случае их надобности, однако
намного удобнее реализовать их один раз и положить в какую-нибудь библиотеку. Примеры таких свойств:

<!-- * _Lazy_ properties: the value is computed only on first access.
* _Observable_ properties: listeners are notified about changes to this property.
* Storing properties in a _map_ instead of a separate field for each property. -->

* *ленивые* свойства (ориг.: *lazy properties*): значение вычисляется один раз, при первом обращении;
* свойства, на события об изменении которых можно подписаться (ориг.: *observable properties*);
* свойства, хранимые в *ассоциативном списке*, а не в отдельных полях.

<!-- To cover these (and other) cases, Kotlin supports _delegated properties_: -->
Для этих (и остальных) случаев, Kotlin поддерживает *делегированные свойства*.

```kotlin
class Example {
    var p: String by Delegate()
}
```

<!--The syntax is: `val/var <property name>: <Type> by <expression>`. The expression after `by` is a _delegate_,
because the `get()` (and `set()`) that correspond to the property will be delegated to its `getValue()` and `setValue()` methods.
Property delegates don’t have to implement an interface, but they have to provide a `getValue()` function (and `setValue()` for `var`s). -->
Их синтаксис выглядит следующим образом: `val/var <имя свойства>: <Тип> by <выражение>`. Выражение после `by` — *делегат*,
потому что обращения (`get()`, `set()`) к свойству будут делегированы его методам `getValue()` и `setValue()`. Делегат
не обязан реализовывать какой-то интерфейс, достаточно, чтобы у него были метод `getValue()` (и `setValue()` для
`var`'ов) с определённой сигнатурой.

<!-- For example: -->
Например:

```kotlin
import kotlin.reflect.KProperty

class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }

    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name}' in $thisRef.")
    }
}
```

<!-- When you read from `p`, which delegates to an instance of `Delegate`, the `getValue()` function from `Delegate` is called.
Its first parameter is the object you read `p` from, and the second parameter holds a description of `p` itself
(for example, you can take its name). -->
Когда вы читаете значение свойства `p`, вызывается метод `getValue()` класса `Delegate`, причем первым параметром ей
передается тот объект, у которого запрашивается свойство `p`, а вторым — объект-описание самого свойства `p` (у него
можно, в частности, узнать имя свойства).

```kotlin
val e = Example()
println(e.p)
```

<!-- This prints: -->
Этот код выведет:

```
Example@33a17727, спасибо за делегирование мне 'p'!
```

<!-- Similarly, when you assign to `p`, the `setValue()` function is called. The first two parameters are the same, and
the third holds the value being assigned: -->
Похожим образом, когда мы обращаемся к `p`, вызывается метод `setValue()`. Два первых параметра — такие же, а третий —
присваиваемое значение свойства.

```kotlin
e.p = "NEW"
```

<!-- This prints: -->
Этот код выведет:

```
NEW было присвоено значению 'p' в Example@33a17727.
```

<!-- The specification of the requirements to the delegated object can be found [below](#property-delegate-requirements). -->
Спецификация требований к делегированным свойствам может быть найдена [ниже](#property-delegate-requirements).

<!-- You can declare a delegated property inside a function or code block; it doesn’t have to be a member of a class.
Below you can find [an example](#local-delegated-properties). -->
Вы можете объявлять делегированные свойства внутри функций или блоков кода, а не только внутри членов классов. Ниже вы
сможете найти [пример](#local-delegated-properties).

<a name="standard-delegates"></a>

<!-- ## Standard delegates -->
## Стандартные делегаты

<!-- The Kotlin standard library provides factory methods for several useful kinds of delegates. -->
Стандартная библиотека Kotlin предоставляет несколько полезных видов делегатов.

<a name="lazy-properties"></a>

<!-- ### Lazy properties -->
### Ленивые свойства

<!-- [`lazy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/lazy.html) is a function that takes a lambda and returns an instance of `Lazy<T>`, which can serve as a delegate for implementing a lazy property.
The first call to `get()` executes the lambda passed to `lazy()` and remembers the result.
Subsequent calls to `get()` simply return the remembered result. -->
[`lazy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/lazy.html) — это функция, которая принимает лямбду и
возвращает экземпляр класса `Lazy<T>`, который служит делегатом для реализации ленивого свойства: первый вызов `get()`
запускает лямбда-выражение, переданное `lazy()` в качестве аргумента, и запоминает полученное значение, а последующие
вызовы просто возвращают вычисленное значение.

```kotlin
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}

fun main() {
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

<!-- By default, the evaluation of lazy properties is *synchronized*: the value is computed only in one thread, but all threads
will see the same value. If the synchronization of the initialization delegate is not required to allow multiple threads
to execute it simultaneously, pass `LazyThreadSafetyMode.PUBLICATION` as a parameter to `lazy()`. -->
По умолчанию вычисление ленивых свойств *синхронизировано*: значение вычисляется только в одном потоке выполнения, и все
остальные потоки могут видеть одно и то же значение. Если синхронизация не требуется, передайте
`LazyThreadSafetyMode.PUBLICATION` в качестве параметра в функцию `lazy()`, тогда несколько потоков смогут исполнять
вычисление одновременно.

<!-- If you're sure that the initialization will always happen in the same thread as the one where you use the property,
you can use `LazyThreadSafetyMode.NONE`. It doesn't incur any thread-safety guarantees and related overhead. -->
Или если вы уверены, что инициализация всегда будет происходить в одном потоке исполнения, вы можете использовать режим
`LazyThreadSafetyMode.NONE`, который не гарантирует никакой потокобезопасности и связанных с этим дополнительных затрат.

<a name="observable-properties"></a>

<!-- ### Observable properties -->
### Observable свойства

<!-- [`Delegates.observable()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/observable.html)
takes two arguments: the initial value and a handler for modifications. -->
[Функция `Delegates.observable()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/observable.html)
принимает два аргумента: начальное значение свойства и обработчик (лямбда).

<!-- The handler is called every time you assign to the property (*after* the assignment has been performed). It has three
parameters: the property being assigned to, the old value, and the new value: -->
Обработчик вызывается каждый раз при изменении свойства (*после* выполнения задания). У обработчика три параметра:
описание свойства, которое изменяется, старое значение и новое значение.

```kotlin
import kotlin.properties.Delegates

class User {
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}

fun main() {
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

<a name="delegating-to-another-property"></a>

<!-- ## Delegating to another property -->
## Делегирование другому  свойству

<!-- If you want to intercept assignments and *veto* them, use [`vetoable()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/vetoable.html) instead of `observable()`.
The handler passed to `vetoable` will be called *before* the assignment of a new property value. -->
Если Вам нужно иметь возможность запретить присваивание некоторых значений, используйте функцию
[`vetoable()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/vetoable.html) вместо `observable()`.
Обработчик, переданный `vetoable`, будет вызван перед присвоением нового значения свойства.

<!-- A property can delegate its getter and setter to another property. Such delegation is available for
both top-level and class properties (member and extension). The delegate property can be: -->
Свойство может делегировать свои геттеры и сеттеры другому свойству. Такое делегирование доступно как для свойств
верхнего уровня, так и для свойств класса (членам и расширениям). Свойство делегата может быть:

<!-- * A top-level property
* A member or an extension property of the same class
* A member or an extension property of another class -->

* свойством высшего уровня,
* членом или свойством расширения того же класса,
* член или свойством расширения другого класса.

<!-- To delegate a property to another property, use the `::` qualifier in the delegate name, for example, `this::delegate` or
`MyClass::delegate`. -->
Чтобы делегировать свойство другому свойству, используйте квалификатор `::` в имени делегата, например, `this::delegate`
или `MyClass::delegate`.

```kotlin
var topLevelInt: Int = 0
class ClassWithDelegate(val anotherClassInt: Int)

class MyClass(var memberInt: Int, val anotherClassInstance: ClassWithDelegate) {
    var delegatedToMember: Int by this::memberInt
    var delegatedToTopLevel: Int by ::topLevelInt

    val delegatedToAnotherClass: Int by anotherClassInstance::anotherClassInt
}
var MyClass.extDelegated: Int by ::topLevelInt
```

<!-- This may be useful, for example, when you want to rename a property in a backward-compatible way: introduce a new property,
annotate the old one with the `@Deprecated` annotation, and delegate its implementation. -->
Это может быть полезно, например, когда вы хотите переименовать свойство обратно совместимым способом: введите новое
свойство, пометьте старое аннотацией  `@Deprecated` и делегируйте его реализацию.

```kotlin
class MyClass {
   var newName: Int = 0
   @Deprecated("Use 'newName' instead", ReplaceWith("newName"))
   var oldName: Int by this::newName
}
fun main() {
   val myClass = MyClass()
   // Уведомление: 'oldName: Int' устарело.
   // Используйте 'newName'
   myClass.oldName = 42
   println(myClass.newName) // 42
}
```

<a name="storing-properties-in-a-map"></a>

<!-- ## Storing properties in a map -->
## Хранение свойств в ассоциативном списке

<!-- One common use case is storing the values of properties in a map.
This comes up often in applications for things like parsing JSON or performing other dynamic tasks.
In this case, you can use the map instance itself as the delegate for a delegated property. -->
Один из самых частых сценариев использования делегированных свойств заключается в хранении свойств в ассоциативном
списке (map). Это полезно в "динамическом" коде, например, при работе с JSON. В этом случае вы можете использовать сам
экземпляр ассоциативного списка в качестве делегата для делегированного свойства.

```kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
```

<!-- In this example, the constructor takes a map: -->
В этом примере конструктор принимает ассоциативный список:

```kotlin
val user = User(mapOf(
    "name" to "John Doe",
    "age"  to 25
))
```

<!-- Delegated properties take values from this map through string keys, which are associated with the names of properties: -->
Делегированные свойства берут значения из этого ассоциативного списка с помощью строковых ключей, которые связаны с
именами свойств.

```kotlin
println(user.name) // Prints "John Doe"
println(user.age)  // Prints 25
```

<!-- This also works for `var`’s properties if you use a `MutableMap` instead of a read-only `Map`: -->
Также, если вы используете `MutableMap` вместо read-only `Map`, поддерживаются изменяемые свойства `var`.

```kotlin
class MutableUser(val map: MutableMap<String, Any?>) {
    var name: String by map
    var age: Int     by map
}
```

<a name="local-delegated-properties"></a>

<!-- ## Local delegated properties -->
## Локальные делегированные свойства

<!-- You can declare local variables as delegated properties.
For example, you can make a local variable lazy: -->
Вы можете объявить локальные переменные как делегированные свойства. Например, вы можете сделать локальную переменную
ленивой.

```kotlin
fun example(computeFoo: () -> Foo) {
    val memoizedFoo by lazy(computeFoo)

    if (someCondition && memoizedFoo.isValid()) {
        memoizedFoo.doSomething()
    }
}
```

<!-- The `memoizedFoo` variable will be computed on first access only.
If `someCondition` fails, the variable won't be computed at all. -->
Переменная `memoizedFoo` будет вычислена только при первом обращении к ней. Если условие `someCondition` будет ложно,
значение переменной не будет вычислено вовсе.

<a name="property-delegate-requirements"></a>

<!-- ## Property delegate requirements -->
## Требования к делегированным свойствам

<!-- For a *read-only* property (`val`), a delegate should provide an operator function `getValue()` with the following parameters: -->
Для *read-only* свойства (например `val`), делегат должен предоставлять функцию `getValue`, которая принимает следующие
параметры:

<!-- * `thisRef` must be the same type as, or a supertype of, the *property owner* (for extension properties, it should be the type being extended).
* `property`  must be of type `KProperty<*>` or its supertype. -->

* `thisRef` — должен иметь такой же тип или быть наследником типа *хозяина свойства* (для расширений — тип, который
расширяется);
* `property` — должен быть типа `KProperty<*>` или его родительского типа.

<!-- `getValue()` must return the same type as the property (or its subtype). -->
`getValue()` должна возвращать значение того же типа, что и свойство (или его родительского типа).

```kotlin
class Resource

class Owner {
    val valResource: Resource by ResourceDelegate()
}

class ResourceDelegate {
    operator fun getValue(thisRef: Owner, property: KProperty<*>): Resource {
        return Resource()
    }
}
```

<!-- For a *mutable* property (`var`), a delegate has to additionally provide an operator function `setValue()`
with the following parameters: -->
Для *изменяемого* свойства (`var`) делегат должен дополнительно предоставлять функцию `setValue`, которая принимает
следующие параметры:

<!-- * `thisRef` must be the same type as, or a supertype of, the *property owner* (for extension properties, it should be the type being extended).
* `property` must be of type `KProperty<*>` or its supertype.
* `value` must be of the same type as the property (or its supertype). -->

* `thisRef` — должен иметь такой же тип или быть наследником типа *хозяина свойства* (для расширений — тип, который
расширяется);
* `property` — должен быть типа `KProperty<*>` или его родительского типа;
* `value` — должен быть того же типа, что и свойство (или его родительской тип).

```kotlin
class Resource

class Owner {
    var varResource: Resource by ResourceDelegate()
}

class ResourceDelegate(private var resource: Resource = Resource()) {
    operator fun getValue(thisRef: Owner, property: KProperty<*>): Resource {
        return resource
    }
    operator fun setValue(thisRef: Owner, property: KProperty<*>, value: Any?) {
        if (value is Resource) {
            resource = value
        }
    }
}
```

<!-- `getValue()` and/or `setValue()` functions can be provided either as member functions of the delegate class or as extension functions.
The latter is handy when you need to delegate a property to an object that doesn't originally provide these functions.
Both of the functions need to be marked with the `operator` keyword. -->
Функции `getValue()` и/или `setValue()` могут быть предоставлены либо как члены класса-делегата, либо как его расширения.
Последнее полезно когда вам нужно делегировать свойство объекту, который изначально не имеет этих функций. Обе эти
функции должны быть отмечены с помощью ключевого слова `operator`.

<!-- You can create delegates as anonymous objects without creating new classes, by using the interfaces `ReadOnlyProperty` and `ReadWriteProperty` from the Kotlin standard library.
They provide the required methods: `getValue()` is declared in `ReadOnlyProperty`; `ReadWriteProperty`
extends it and adds `setValue()`. This means you can pass a `ReadWriteProperty` whenever a `ReadOnlyProperty` is expected. -->
Вы можете создавать делегатов как анонимные объекты без создания новых классов, используя интерфейсы `ReadOnlyProperty`
и `ReadWriteProperty` из стандартной библиотеки Kotlin. Они предоставляют необходимые методы: `getValue()` объявлен в
`ReadOnlyProperty`; `ReadWriteProperty` расширяет его и добавляет `setValue()`. Это значает, что вы можете передавать
`ReadWriteProperty` всякий раз, когда ожидается `ReadOnlyProperty`.

```kotlin
fun resourceDelegate(): ReadWriteProperty<Any?, Int> =
    object : ReadWriteProperty<Any?, Int> {
        var curValue = 0 
        override fun getValue(thisRef: Any?, property: KProperty<*>): Int = curValue
        override fun setValue(thisRef: Any?, property: KProperty<*>, value: Int) {
            curValue = value
        }
    }

val readOnly: Int by resourceDelegate()  // ReadWriteProperty неизменяемое
var readWrite: Int by resourceDelegate()
```

<a name="translation-rules-for-delegated-properties"></a>

<!-- ### Translation rules for delegated properties -->
### Правила преобразования для делегированных свойств

<!-- Прим. пер.: TODO: рецензировать заголовок у более опытных программистов -->
Под *правилами преобразования* (ориг.: *translation rules*) здесь понимаются правила, по которым компилируются
делегированные свойства.

<!-- Under the hood, the Kotlin compiler generates an auxiliary property for every delegated property and then delegates to it.
For example, for the property `prop` it generates the hidden property `prop$delegate`, and the code of the accessors
simply delegates to this additional property: -->
Для каждого делегированного свойства компилятор Kotlin "за кулисами" генерирует вспомогательное свойство и делегирует
его. Например, для свойства `prop` генерируется скрытое свойство `prop$delegate`, и исполнение геттеров и сеттеров
просто делегируется этому дополнительному свойству.

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

<!-- The Kotlin compiler provides all the necessary information about `prop` in the arguments: the first argument `this`
refers to an instance of the outer class `C`, and `this::prop` is a reflection object of the `KProperty` type describing `prop` itself. -->
Компилятор Kotlin предоставляет всю необходимую информацию о `prop` в аргументах: первый аргумент `this` ссылается на
экземпляр внешнего класса `C` и `this::prop` reflection-объект типа `KProperty`, описывающий сам `prop`.

<a name="translation-rules-when-delegating-to-another-property"></a>

<!-- ### Translation rules when delegating to another property -->
### Правила преобразования при делегировании другому свойству

<!-- When delegating to another property, the Kotlin compiler generates immediate access to the referenced property.
This means that the compiler doesn't generate the field `prop$delegate`. This optimization helps save memory. -->
При делегировании другому свойству компилятор Kotlin создает непосредственный доступ к указанному свойству. Это
означает, что компилятор не генерирует поле `prop$delegate`. Такая оптимизация помогает экономить память.

<!-- Take the following code, for example: -->
Возьмем, к примеру, следующий код:

```kotlin
class C<Type> {
    private var impl: Type = ...
    var prop: Type by ::impl
}
```

<!-- Property accessors of the `prop` variable invoke the `impl` variable directly, skipping the delegated property's `getValue`and `setValue` operators, 
and thus the `KProperty` reference object is not needed. -->
Геттеры и сеттеры свойств переменной `prop` напрямую вызывают переменную `impl`, пропуская операторы делегированного
свойства `getValue` и `setValue`, и, следовательно, ссылочный объект `KProperty` не требуется.

<!-- For the code above, the compiler generates the following code: -->
Для кода выше компилятор генерирует следующий код:

```kotlin
class C<Type> {
    private var impl: Type = ...

    var prop: Type
        get() = impl
        set(value) {
            impl = value
        }
    
    fun getProp$delegate(): Type = impl // Этот метод нужен только для рефлексии
}
```

<a name="providing-a-delegate"></a>

<!-- ### Providing a delegate -->
### Предоставление делегата

<!-- By defining the `provideDelegate` operator, you can extend the logic for creating the object to which the property implementation
is delegated. If the object used on the right-hand side of `by` defines `provideDelegate` as a member or extension function,
that function will be called to create the property delegate instance. -->
С помощью определения оператора `provideDelegate` вы можете расширить логику создания объекта, которому будет
делегировано свойство. Если объект, который используется справа от `by`, определяет `provideDelegate` как член или как
расширение, эта функция будет вызвана для создания экземпляра делегата.

<!-- One of the possible use cases of `provideDelegate` is to check the consistency of the property upon its initialization. -->
Один из возможных случаев использования `provideDelegate` — это проверка состояния свойства при его инициализации.

<!-- For example, to check the property name before binding, you can write something like this: -->
Например, если вы хотите проверить имя свойства перед связыванием, вы можете написать что-то вроде следующего:

```kotlin
class ResourceDelegate<T> : ReadOnlyProperty<MyUI, T> {
    override fun getValue(thisRef: MyUI, property: KProperty<*>): T { ... }
}

class ResourceLoader<T>(id: ResourceID<T>) {
    operator fun provideDelegate(
            thisRef: MyUI,
            prop: KProperty<*>
    ): ReadOnlyProperty<MyUI, T> {
        checkProperty(thisRef, prop.name)
        // создание делегата
        return ResourceDelegate()
    }

    private fun checkProperty(thisRef: MyUI, name: String) { ... }
}

class MyUI {
    fun <T> bindResource(id: ResourceID<T>): ResourceLoader<T> { ... }

    val image by bindResource(ResourceID.image_id)
    val text by bindResource(ResourceID.text_id)
}
```

<!-- The parameters of `provideDelegate` are the same as those of `getValue`: -->
`provideDelegate` имеет те же параметры, что и `getValue`:

<!-- * `thisRef` must be the same type as, or a supertype of, the _property owner_ (for extension properties, it should be the type being extended);
* `property` must be of type `KProperty<*>` or its supertype. -->

* `thisRef` — должен иметь такой же тип, или быть наследником типа *хозяина свойства* (для расширений —  тип, который
расширяется)
* `property` — должен быть типа `KProperty<*>` или его родительского типа.

<!-- The `provideDelegate` method is called for each property during the creation of the `MyUI` instance, and it performs
the necessary validation right away. -->
Метод `provideDelegate` вызывается для каждого свойства во время создания экземпляра `MyUI`, и сразу совершает
необходимые проверки.

<!-- Without this ability to intercept the binding between the property and its delegate, to achieve the same functionality
you'd have to pass the property name explicitly, which isn't very convenient: -->
Не будь этой возможности внедрения между свойством и делегатом, для достижения той же функциональности вам бы пришлось
передавать имя свойства явно, что не очень удобно.

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

<!-- In the generated code, the `provideDelegate` method is called to initialize the auxiliary `prop$delegate` property.
Compare the generated code for the property declaration `val prop: Type by MyDelegate()` with the generated code
[above](#translation-rules-for-delegated-properties) (when the `provideDelegate` method is not present): -->
В сгенерированном коде метод `provideDelegate` вызывается для инициализации вспомогательного свойства `prop$delegate`.
Сравните сгенерированный для объявления свойства код `val prop: Type by MyDelegate()` со сгенерированным кодом
[выше](#translation-rules-for-delegated-properties) (когда `provideDelegate` не представлен):

```kotlin
class C {
    var prop: Type by MyDelegate()
}

// этот код будет сгенерирован компилятором 
// когда функция 'provideDelegate' доступна:
class C {
    // вызываем "provideDelegate" для создания вспомогательного свойства "delegate"
    private val prop$delegate = MyDelegate().provideDelegate(this, this::prop)
    var prop: Type
        get() = prop$delegate.getValue(this, this::prop)
        set(value: Type) = prop$delegate.setValue(this, this::prop, value)
}
```

<!-- Note that the `provideDelegate` method affects only the creation of the auxiliary property and doesn't affect the code
generated for the getter or the setter. -->
Заметьте, что метод `provideDelegate` влияет только на создание вспомогательного свойства и не влияет на код,
генерируемый геттером или сеттером.

<!-- With the `PropertyDelegateProvider` interface from the standard library, you can create delegate providers without creating new classes. -->
С интерфейсом `PropertyDelegateProvider` из стандартной библиотеки, вы можете создавать делегатов поставщиков делегатов
без создания новых классов.

```kotlin
val provider = PropertyDelegateProvider { thisRef: Any?, property ->
    ReadOnlyProperty<Any?, Int> {_, property -> 42 }
}
val delegate: Int by provider
```
