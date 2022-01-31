---
layout: reference
category: "Syntax"
title: "Классы"
related:
    - functions.md
    - nested-classes.md
    - interfaces.md
url: https://kotlinlang.ru/docs/classes.html
---

<!-- При переводе статьи оригинальная версия была от 29 November 2021 -->

<!-- Classes -->
# Классы

<!-- Classes in Kotlin are declared using the keyword `class`: -->
Классы в Kotlin объявляются с помощью использования ключевого слова `class`.

```kotlin
class Person { /*...*/ }
```

<!-- The class declaration consists of the class name, the class header (specifying its type parameters, the primary constructor, 
and some other things), and the class body surrounded by curly braces. Both the header and the body are optional; if the
class has no body, the curly braces can be omitted. -->
Объявление класса состоит из имени класса, заголовка (указания типов его параметров, основного конструктора и т.п) и тела класса,
заключённого в фигурные скобки. И заголовок, и тело класса являются необязательными составляющими. Если у класса нет тела, фигурные скобки могут быть опущены.

```kotlin
class Empty
```

<a name="constructors"></a>
<!-- ## Constructors -->
## Конструкторы

<!-- A class in Kotlin can have a _primary constructor_ and one or more _secondary constructors_. The primary constructor is a
part of the class header, and it goes after the class name and optional type parameters. -->
Класс в Kotlin может иметь *основной конструктор* (*primary constructor*) и один или более *дополнительных конструкторов* (*secondary constructors*).
Основной конструктор является частью заголовка класса, его объявление идёт сразу после имени класса (и необязательных параметров).

```kotlin
class Person constructor(firstName: String) { /*...*/ }
```

<!-- If the primary constructor does not have any annotations or visibility modifiers, the `constructor` keyword can be omitted: -->
Если у основного конструктора нет аннотаций и модификаторов видимости, ключевое слово `constructor` может быть опущено.

```kotlin
class Person(firstName: String) { /*...*/ }
```

<!-- The primary constructor cannot contain any code. Initialization code can be placed in _initializer blocks_ prefixed with
the `init` keyword. -->
Основной конструктор не может содержать в себе исполняемого кода. Инициализирующий код может быть помещён в соответствующие блоки (*initializers blocks*),
которые помечаются словом `init`.

<!-- During the initialization of an instance, the initializer blocks are executed in the same order as they appear in the
class body, interleaved with the property initializers: -->
При создании экземпляра класса блоки инициализации выполняются в том порядке, в котором они идут в теле класса, чередуясь с инициализацией свойств.

```kotlin
class InitOrderDemo(name: String) {
    val firstProperty = "Первое свойство: $name".also(::println)
    
    init {
        println("Первый блок инициализации: ${name}")
    }
    
    val secondProperty = "Второе свойство: ${name.length}".also(::println)
    
    init {
        println("Второй блок инициализации: ${name.length}")
    }
}
```

<!-- Primary constructor parameters can be used in the initializer blocks. They can also be used in property initializers
declared in the class body: -->
Обратите внимание, что параметры основного конструктора могут быть использованы в инициализирующем блоке.
Они также могут быть использованы при инициализации свойств в теле класса.

```kotlin
class Customer(name: String) {
    val customerKey = name.uppercase()
}
```

<!-- Kotlin has a concise syntax for declaring properties and initializing them from the primary constructor: -->
Для объявления и инициализации свойств основного конструктора в Kotlin есть лаконичное синтаксическое решение:

```kotlin
class Person(val firstName: String, val lastName: String, var age: Int)
```

<!-- Such declarations can also include default values of the class properties: -->
Такие объявления также могут включать в себя значения свойств класса по умолчанию.

```kotlin
class Person(val firstName: String, val lastName: String, var isEmployed: Boolean = true)
```

<!-- You can use a [trailing comma](coding-conventions.md#trailing-commas) when you declare class properties: -->
Вы можете использовать [завершающую запятую](coding-conventions.md#trailing-commas) при объявлении свойств класса.

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    var age: Int, // завершающая запятая
) { /*...*/ }
```

<!-- Much like regular properties, properties declared in the primary constructor can be mutable (`var`) or read-only (`val`). -->
Свойства, объявленные в основном конструкторе, могут быть изменяемые (`var`) и неизменяемые (`val`).

<!-- If the constructor has annotations or visibility modifiers, the `constructor` keyword is required and the modifiers go before it: -->
Если у конструктора есть аннотации или модификаторы видимости, ключевое слово `constructor` обязательно, и модификаторы используются перед ним.

```kotlin
class Customer public @Inject constructor(name: String) { /*...*/ }
```

<!-- Learn more about [visibility modifiers](visibility-modifiers.md#constructors). -->
Для более подробной информации см. "[Модификаторы доступа](visibility-modifiers.html#constructors)".

<a name="secondary-constructors"></a>
<!-- ### Secondary constructors -->
### Дополнительные конструкторы 

<!-- A class can also declare _secondary constructors_, which are prefixed with `constructor`: -->
В классах также могут быть объявлены *дополнительные конструкторы* (*secondary constructors*), перед которыми используется ключевое слово `constructor`.

```kotlin
class Person(val pets: MutableList<Pet> = mutableListOf())
class Pet {
    constructor(owner: Person) {
        owner.pets.add(this) // добавляет этого питомца в список домашних животных своего владельца
    }
}
```

<!-- If the class has a primary constructor, each secondary constructor needs to delegate to the primary constructor, either
directly or indirectly through another secondary constructor(s). Delegation to another constructor of the same class
is done using the **this** keyword: -->
Если у класса есть основной конструктор, каждый дополнительный конструктор должен прямо или косвенно ссылаться (через другой(*ие*) конструктор(*ы*)) на основной.
Осуществляется это при помощи ключевого слова `this`.

```kotlin
class Person(val name: String) {
    val children: MutableList<Person> = mutableListOf()
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

<!-- Code in initializer blocks effectively becomes part of the primary constructor. Delegation to the primary constructor
happens as the first statement of a secondary constructor, so the code in all initializer blocks and property initializers
is executed before the body of the secondary constructor. -->
Обратите внимание, что код в блоках инициализации фактически становится частью основного конструктора.
Дополнительный конструктор ссылается на основной при помощи своего первого оператора, поэтому код во всех блоках инициализации,
а также инициализация свойств выполняется перед выполнением кода в теле дополнительного конструктора.

<!-- Even if the class has no primary constructor, the delegation still happens implicitly, and the initializer blocks are
still executed: -->
Даже если у класса нет основного конструктора на него все равно происходит неявная ссылка и блоки инициализации выполняются также.

```kotlin
class Constructors {
    init {
        println("Блок инициализации")
    }
    constructor(i: Int) {
        println("Constructor $i")
    }
}
```

<!-- If a non-abstract class does not declare any constructors (primary or secondary), it will have a generated primary constructor
with no arguments. The visibility of the constructor will be public. -->
Если в абстрактном классе не объявлено никаких конструкторов (основного или дополнительных), у этого класса автоматически сгенерируется пустой конструктор без параметров.
Видимость этого конструктора будет public.

<!-- If you don't want your class to have a public constructor, declare an empty primary constructor with non-default visibility: -->
Если вы не желаете иметь класс с открытым *public* конструктором, вам необходимо объявить пустой конструктор с соответствующим модификатором видимости.

```kotlin
class DontCreateMe private constructor () { /*...*/ }
```

<!-- > On the JVM, if all of the primary constructor parameters have default values, the compiler will generate an additional parameterless constructor which will use the default values. This makes it easier to use Kotlin with libraries such as Jackson or JPA that create class instances through parameterless constructors.
>
> ```kotlin
> class Customer(val customerName: String = "")
> ``` -->
> В JVM компилятор генерирует дополнительный конструктор без параметров в случае, если все параметры основного конструктора имеют значения по умолчанию. Это делает использование таких библиотек, как Jackson и JPA, более простым с Kotlin, так как они используют пустые конструкторы при создании экземпляров классов.
>
> ```kotlin
> class Customer(val customerName: String = "")
> ```

<a name="creating-instances-of-classes"></a>
<!-- ## Creating instances of classes -->
## Создание экземпляров классов

<!-- To create an instance of a class, call the constructor as if it were a regular function: -->
Для создания экземпляра класса конструктор вызывается так, как если бы он был обычной функцией.

```kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

<!-- > Kotlin does not have a `new` keyword. -->
> В Kotlin нет ключевого слова `new`.

<!-- The process of creating instances of nested, inner, and anonymous inner classes is described in [Nested classes](nested-classes.md). -->
Создание экземпляров вложенных, внутренних и анонимных внутренних классов описано в разделе [Вложенные классы](nested-classes.html).

<a name="class-members"></a>
<!-- ## Class members -->
## Члены класса

<!-- Classes can contain: -->
Классы могут содержать в себе:

<!-- * [Constructors and initializer blocks](classes.md#constructors)
* [Functions](functions.md)
* [Properties](properties.md)
* [Nested and inner classes](nested-classes.md)
* [Object declarations](object-declarations.md) -->
* [Конструкторы и инициализирующие блоки](classes.html#constructors)
* [Функции](functions.html)
* [Свойства](properties.html)
* [Вложенные классы](nested-classes.html)
* [Объявления объектов](object-declarations.html)

<a name="inheritance"></a>
<!-- ## Inheritance -->
## Наследование

<!-- Classes can be derived from each other and form inheritance hierarchies.
[Learn more about inheritance in Kotlin](inheritance.md). -->
Классы могут быть производными друг от друга и формировать иерархии наследования.
[Узнайте больше о наследовании в Котлине](inheritance.md).

<a name="abstract-classes"></a>
<!-- ## Abstract classes -->
## Абстрактные классы

<!-- A class may be declared `abstract`, along with some or all of its members.
An abstract member does not have an implementation in its class.
You don't need to annotate abstract classes or functions with `open`. -->
Класс может быть объявлен как  `abstract` со всеми или некоторыми его членами. Абстрактный член не имеет реализации в своём классе.
Обратите внимание, что нам не надо аннотировать абстрактный класс или функцию словом `open` - это и так подразумевается.

```kotlin
abstract class Polygon {
    abstract fun draw()
}
class Rectangle : Polygon() {
    override fun draw() {
        // рисование прямоугольника
    }
}
```

<!-- You can override a non-abstract `open` member with an abstract one. -->
Можно переопределить неабстрактный `open` член абстрактным.

```kotlin
open class Polygon {
    open fun draw() {
        // некоторый метод рисования полигонов по умолчанию
    }
}
abstract class WildShape : Polygon() {
    // Классы, которые наследуют WildShape, должны предоставлять свой собственный
    // метод рисования вместо использования по умолчанию для полигона
    abstract override fun draw()
}
```

<a name="companion-objects"></a>
<!-- ## Companion objects -->
## Вспомогательные объекты

<!-- If you need to write a function that can be called without having a class instance but that needs access to the internals
of a class (such as a factory method), you can write it as a member of an [object declaration](object-declarations.md) inside that class. -->
Если вам нужно написать функцию, которая может быть использована без создания экземпляра класса, которая имела бы доступ к данным внутри этого класса
(к примеру, фабричный метод), вы можете написать её как член [объявления объекта](object-declarations.html) внутри этого класса.

<!-- Even more specifically, if you declare a [companion object](object-declarations.md#companion-objects) inside your class,
you can access its members using only the class name as a qualifier. -->
В частности, если вы объявляете [вспомогательный объект](object-declarations.html#companion-objects) в своём классе,
у вас появляется возможность обращаться к членам класса, используя только название класса в качестве классификатора.
