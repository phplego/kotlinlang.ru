---
layout: reference
category: "Syntax"
title: "Классы и наследование"
related:
    - functions.md
    - nested-classes.md
    - interfaces.md
url: https://kotlinlang.ru/docs/reference/classes.html
---

<!--# Classes and Inheritance-->
# Классы и наследование

<!--## Classes-->
## Классы

<!--Classes in Kotlin are declared using the keyword <b class="keyword">class</b>:-->
Классы в <b>Kotlin</b> объявляются с помощью использования ключевого слова **class**:

```kotlin
class Invoice {
}
```

<!--The class declaration consists of the class name, the class header (specifying its type parameters, the primary
constructor etc.) and the class body, surrounded by curly braces. Both the header and the body are optional;
if the class has no body, curly braces can be omitted.-->
Объявление класса состоит из имени класса, заголовка (указания типов его параметров, основного конструктора и т.п) и тела класса,
заключённого в фигурные скобки. И заголовок, и тело класса являются необязательными составляющими: если у класса нет тела, фигурные скобки могут быть опущены.

```kotlin
class Empty
```

<a name="constructors"></a>

<!--### Constructors-->
### Конструкторы

<!--A class in Kotlin can have a **primary constructor** and one or more **secondary constructors**. The primary
constructor is part of the class header: it goes after the class name (and optional type parameters).-->
Класс в <b>Kotlin</b> может иметь основной конструктор (**primary constructor**) и один или более дополнительных конструкторов (**secondary constructors**). Основной конструктор является частью заголовка класса, его объявление идёт сразу после имени класса (и необязательных параметров):

```kotlin
class Person constructor(firstName: String)
```

<!--If the primary constructor does not have any annotations or visibility modifiers, the **constructor**
keyword can be omitted:-->
Если у конструктора нет аннотаций и модификаторов видимости, ключевое слово <b class="keyword">constructor</b> может быть опущено:

```kotlin
class Person(firstName: String)
```

<!--The primary constructor cannot contain any code. Initialization code can be placed
in **initializer blocks**, which are prefixed with the **init**keyword:-->
Основной конструктор не может содержать в себе исполняемого кода. Инициализирующий код может быть помещён в соответствующий блок (**initializers blocks**), который помечается словом <b class="keyword">init</b>.

<!--During an instance initialization, the initializer blocks are executed in the same order as they appear in the class body, interleaved with the property initializers:-->
При создании экземпляра класса блоки инициализации выполняются в том порядке, в котором они идут в теле класса, чередуясь с инициализацией свойств:

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

fun main() {
    InitOrderDemo("Привет")
}
```

<!--Note that parameters of the primary constructor can be used in the initializer blocks. They can also be used in
property initializers declared in the class body:-->
Обратите внимание, что параметры основного конструктора могут быть использованы в инициализирующем блоке. Они также могут быть использованы при инициализации свойств в теле класса:

```kotlin
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

<!--In fact, for declaring properties and initializing them from the primary constructor, Kotlin has a concise syntax:-->
В действительности, для объявления и инициализации свойств основного конструктора в <b>Kotlin</b> есть лаконичное синтаксическое решение:

```kotlin
class Person(val firstName: String, val lastName: String, var age: Int) {
  // ...
}
```

<!--Much the same way as regular properties, the properties declared in the primary constructor can be
mutable (**var**) or read-only (**val**).-->
Свойства, объявленные в основном конструкторе, могут быть изменяемые (<b class="keyword">var</b>) и неизменяемые (<b class="keyword">val</b>). 

<!--If the constructor has annotations or visibility modifiers, the **constructor** keyword is required, and
the modifiers go before it:-->
Если у конструктора есть аннотации или модификаторы видимости, ключевое слово <b class="keyword">constructor</b> обязательно, и модификаторы используются перед ним:

```kotlin
class Customer public @Inject constructor(name: String) { ... }
```

<!--For more details, see [Visibility Modifiers](visibility-modifiers.html#constructors).-->
Для более подробной информации по данному вопросу см. "[Модификаторы доступа](visibility-modifiers.html#constructors)".

<!--#### Secondary Constructors-->
#### Дополнительные конструкторы 

<!--The class can also declare **secondary constructors**, which are prefixed with **constructor**:-->
В классах также могут быть объявлены дополнительные конструкторы (**secondary constructors**), перед которыми используется ключевое слово <b class="keyword">constructor</b>:

```kotlin
class Person {
    var children: MutableList<Person> = mutableListOf<>()
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

<!--If the class has a primary constructor, each secondary constructor needs to delegate to the primary constructor, either
directly or indirectly through another secondary constructor(s). Delegation to another constructor of the same class
is done using the **this** keyword:-->
Если у класса есть основной конструктор, каждый дополнительный конструктор должен прямо или косвенно ссылаться (через другой(_ие_) конструктор(_ы_)) на основной. Осуществляется это при помощи ключевого слова <b class="keyword">this</b>:

```kotlin
class Person(val name: String) {
    var children: MutableList<Person> = mutableListOf<>()
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

<!--Note that code in initializer blocks effectively becomes part of the primary constructor. Delegation to the primary constructor happens as the first statement of a secondary constructor, so the code in all initializer blocks and property initializers is executed before the secondary constructor body. Even if the class has no primary constructor, the delegation still happens implicitly, and the initializer blocks are still executed:-->
Обратите внимание, что код в блоках инициализации фактически становится частью основного конструктора. Дополнительный конструктор ссылается на основной при помощи своего первого оператора, поэтому код во всех блоках инициализации, а также инициализация свойств выполняется перед выполнением кода в теле дополнительного конструктора. Даже если у класса нет основного конструктора на него все равно происходит неявная ссылка и блоки инициализации выполняются также:

```kotlin
class Constructors {
    init {
        println("Блок инициализации")
    }

    constructor(i: Int) {
        println("Конструктор")
    }
}

fun main() {
    Constructors(1)
}
```

<!--If a non-abstract class does not declare any constructors (primary or secondary), it will have a generated primary
constructor with no arguments. The visibility of the constructor will be public. If you do not want your class
to have a public constructor, you need to declare an empty primary constructor with non-default visibility:-->
Если в абстрактном классе не объявлено никаких конструкторов (основного или дополнительных), у этого класса автоматически сгенерируется пустой конструктор без параметров. Видимость этого конструктора будет **public**. Если вы не желаете иметь класс с открытым **public** конструктором, вам необходимо объявить пустой конструктор с соответствующим модификатором видимости:

```kotlin
class DontCreateMe private constructor () {
}
```

<!--NOTE: On the JVM, if all of the parameters of the primary constructor have default values, the compiler will generate an additional parameterless constructor which will use the default values. This makes it easier to use Kotlin with libraries such as Jackson or JPA that create class instances through parameterless constructors.-->
>**Примечание**: В виртуальной машине JVM компилятор генерирует дополнительный конструктор без параметров в случае, если все параметры основного конструктора имеют значения по умолчанию. Это делает использование таких библиотек, как <b>Jackson</b> и <b>JPA</b>, более простым в языке <b>Kotlin</b>, так как они используют пустые конструкторы при создании экземпляров классов.
>
>```kotlin
>class Customer(val customerName: String = "")
>```


<!--### Creating instances of classes-->
### Создание экземпляров классов

<!--To create an instance of a class, we call the constructor as if it were a regular function:-->
Для создания экземпляра класса конструктор вызывается так, как если бы он был обычной функцией:

```kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

<!--Note that Kotlin does not have a **new** keyword.-->
Обращаем ваше внимание на то, что в <b>Kotlin</b> не используется ключевое слово <b class="keyword">new</b>.

<!--Creating instances of nested, inner and anonymous inner classes is described in [Nested classes](nested-classes.html).-->
Создание экземпляров вложенных, внутренних и анонимных внутренних классов описано в разделе [Вложенные классы](nested-classes.html).

<!--### Class Members-->
### Члены класса

<!--Classes can contain-->
Классы могут содержать в себе:

<!--* Constructors and initializer blocks
* [Functions](functions.html)
* [Properties](properties.html)
* [Nested and Inner Classes](nested-classes.html)
* [Object Declarations](object-declarations.html)-->
* Конструкторы и инициализирующие блоки
* [Функции](functions.html)
* [Свойства](properties.html)
* [Вложенные классы](nested-classes.html)
* [Объявления объектов](object-declarations.html)

<a name="inheritance"></a>

<!--## Inheritance-->
## Наследование

<!--All classes in Kotlin have a common superclass `Any`, that is a default super for a class with no supertypes declared:-->
Для всех классов в языке <b>Kotlin</b> родительским суперклассом является класс `Any`. Он также является родительским классом для любого класса, в котором не указан какой-либо другой родительский класс:

```kotlin
class Example // Неявно наследуется от Any
```

<!--`Any` has three methods: `equals()`, `hashCode()` and `toString()`. Thus, they are defined for all Kotlin classes.-->
У `Any` есть три метода: `equals()`, `hashCode()` и `toString()`. Эти методы определены для всех классов в Kotlin.

<!--By default, Kotlin classes are final: they can’t be inherited.
To make a class inheritable, mark it with the `open` keyword.-->
По умолчанию все классы в Kotlin имеют статус **final**, который блокирует возможность наследования.  
Чтобы сделать класс наследуемым, его нужно пометить ключевым словом `open`.

```kotlin
open class Base // Класс открыт для наследования
```

<!--To declare an explicit supertype, we place the type after a colon in the class header:-->
Для явного объявления суперкласса мы помещаем его имя за знаком двоеточия в оглавлении класса:

```kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

<!--If the class has a primary constructor, the base type can (and must) be initialized right there,
using the parameters of the primary constructor.-->
Если у класса есть основной конструктор, базовый тип может (и должен) быть проинициализирован там же, с использованием параметров основного конструктора.

<!--If the derived class has no primary constructor, then each secondary constructor has to initialize the base type
using the **super** keyword, or to delegate to another constructor which does that.
Note that in this case different secondary constructors can call different constructors of the base type:-->
Если у класса нет основного конструктора, тогда каждый последующий дополнительный конструктор должен включать в себя инициализацию базового типа с помощью ключевого слова <b class="keyword">super</b> или давать отсылку на другой конструктор, который это делает.
Примечательно, что любые дополнительные конструкторы могут ссылаться на разные конструкторы базового типа:

```kotlin
class MyView : View {
    constructor(ctx: Context) : super(ctx) {
    }

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs) {
    }
}
```

<!--### Overriding methods-->
<a name="overriding-methods"></a>
### Переопределение методов класса

<!--As we mentioned before, we stick to making things explicit in Kotlin. So, Kotlin requires explicit
modifiers for overridable members (we call them *open*) and for overrides:-->
Как упоминалось ранее, мы придерживаемся идеи определённости и ясности в языке <b>Kotlin</b>. Поэтому <b>Kotlin</b>
требует явно указывать модификаторы и для членов, которые могут быть переопределены, и для самого переопределения:

```kotlin
open class Shape {
    open fun draw() { /*...*/ }
    fun fill() { /*...*/ }
}

class Circle() : Shape() {
    override fun draw() { /*...*/ }
}
```

<!--The **override** annotation is required for `Shape.fill()`. If it were missing, the compiler would complain.
If there is no **open** modifier on a function, like `Shape.fill()`, declaring a method with the same signature in a subclass is illegal,
either with **override** or without it. The *open*{: .keyword } modifier has no effect when added on members of a final class (i.e.. a class with no *open*{: .keyword } modifier).-->
Для `Circle.draw()` необходима аннотация  <b class="keyword">override</b>. В случае её отсутствия компилятор выдаст ошибку. Если у функции типа `Shape.fill()` нет аннотации <b class="keyword">open</b>, объявление метода с такой же сигнатурой в производном классе невозможно, с <b class="keyword">override</b> или без. Модификатор <b class="keyword">open</b> не действует при добавлении к членам **final** класса (т.е. класса без модификатора <b class="keyword">open</b>).

<!--A member marked **override** is itself open, i.e. it may be overridden in subclasses. If you want to prohibit re-overriding, use **final**:-->
Член класса, помеченный <b class="keyword">override</b>, является сам по себе **open**, т.е. он может быть переопределён в производных классах. Если вы хотите запретить возможность переопределения такого члена, используйте <b class="keyword">final</b>:

```kotlin
open class Rectangle() : Shape() {
    final override fun draw() { /*...*/ }
}
```

<!--### Overriding properties-->
### Переопределение свойств класса

<!--Overriding properties works in a similar way to overriding methods; properties declared on a superclass 
that are then redeclared on a derived class must be prefaced with *override*{: .keyword }, and they must have a compatible type.
Each declared property can be overridden by a property with an initializer or by a property with a `get` method.-->
Переопределение свойств работает также, как и переопределение методов; все свойства, унаследованные от суперкласса, должны быть помечены ключевым словом <b class="keyword">override</b>, а также должны иметь совместимый тип.
Каждое объявленное свойство может быть переопределено свойством с инициализацией или свойством с `get`-методом.

```kotlin
open class Shape {
    open val vertexCount: Int = 0
}

class Rectangle : Shape() {
    override val vertexCount = 4
}
```

<!--You can also override a `val` property with a `var` property, but not vice versa.
This is allowed because a `val` property essentially declares a `get` method,
and overriding it as a `var` additionally declares a `set` method in the derived class.-->
Вы также можете переопределить свойство `val` свойством `var`, но не наоборот.
Это разрешено, поскольку свойство `val` объявляет `get`-метод, а при переопределении его как `var` дополнительно объявляется `set`-метод в производном классе.

<!--Note that you can use the *override*{: .keyword } keyword as part of the property declaration in a primary constructor.-->
Обратите внимание, что ключевое слово <b class="keyword">override</b> может быть использовано в основном конструкторе класса как часть объявления свойства.

```kotlin
interface Shape {
    val vertexCount: Int
}

class Rectangle(override val vertexCount: Int = 4) : Shape // Всегда имеет 4 вершины

class Polygon : Shape {
    override var vertexCount: Int = 0  // Может быть установлено любое количество
}
```

<!--### Derived class initialization order-->
### Порядок инициализации производного класса

<!--During construction of a new instance of a derived class, the base class initialization is done as the first step (preceded only by evaluation of the arguments for the base class constructor) and thus happens before the initialization logic of the derived class is run.-->
При создании нового экземпляра класса в первую очередь выполняется инициализация базового класса (этому шагу предшествует только оценка аргументов, передаваемых в конструктор базового класса) и, таким образом, происходит до запуска логики инициализации производного класса.

```kotlin
open class Base(val name: String) {

    init { println("Инициализация класса Base") }

    open val size: Int = 
        name.length.also { println("Инициализация свойства size в класса Base: $it") }
}

class Derived(
    name: String,
    val lastName: String
) : Base(name.capitalize().also { println("Аргументы, переданные в конструктор класса Base: $it") }) {

    init { println("Инициализация класса Derived") }

    override val size: Int =
        (super.size + lastName.length).also { println("Инициализация свойства size в классе Derived: $it") }
}

fun main() {
    println("Построение класса Derived(\"hello\", \"world\")")
    val d = Derived("hello", "world")
}
```

<!--It means that, by the time of the base class constructor execution, the properties declared or overridden in the derived class are not yet initialized. If any of those properties are used in the base class initialization logic (either directly or indirectly, through another overridden *open*{: .keyword } member implementation), it may lead to incorrect behavior or a runtime failure. When designing a base class, you should therefore avoid using *open*{: .keyword } members in the constructors, property initializers, and *init*{: .keyword } blocks.-->
Это означает, что свойства, объявленные или переопределенные в производном классе, не инициализированы к моменту вызова конструктора базового класса. Если какое-либо из этих свойств используется в логике инициализации базового класса (прямо или косвенно через другую переопределенную реализацию члена класса), это может привести к некорректному поведению или сбою во время выполнения. Поэтому при разработке базового класса следует избегать использования членов с ключевым словом <b class="keyword">open</b> в конструкторах, инициализации свойств и блоках инициализации (<b class="keyword">init</b>).

<!--### Calling the superclass implementation-->
### Вызов функций и свойств суперкласса

<!--Code in a derived class can call its superclass functions and property accessors implementations using the *super*{: .keyword } keyword:-->
Производный класс может вызывать реализацию функций и свойств своего суперкласса, используя ключевой слово <b class="keyword">super</b>:

```kotlin
open class Rectangle {
    open fun draw() { println("Рисование прямоугольника") }
    val borderColor: String get() = "black"
}

class FilledRectangle : Rectangle() {
    override fun draw() {
        super.draw()
        println("Заполнение прямоугольника")
    }

    val fillColor: String get() = super.borderColor
}
```

<!--Inside an inner class, accessing the superclass of the outer class is done with the *super*{: .keyword } keyword qualified with the outer class name: `super@Outer`:-->
Во внутреннем классе доступ к суперклассу внешнего класса осуществляется при помощи ключевого слова <b class="keyword">super</b>, за которым следует имя внешнего класса: `super@Outer`:

```kotlin
class FilledRectangle: Rectangle() {
    fun draw() { /* ... */ }
    val borderColor: String get() = "black"
    
    inner class Filler {
        fun fill() { /* ... */ }
        fun drawAndFill() {
            super@FilledRectangle.draw() // Вызывает реализацию функции draw() класса Rectangle
            fill()
            println("Нарисованный прямоугольник заполнен ${super@FilledRectangle.borderColor} цветом.") // Используется реализация get()-метода свойства borderColor в классе Rectangle
        }
    }
}
```

<!--### Overriding Rules-->
### Правила переопределения

<!--In Kotlin, implementation inheritance is regulated by the following rule: if a class inherits many implementations of the same member from its immediate superclasses,
it must override this member and provide its own implementation (perhaps, using one of the inherited ones).
To denote the supertype from which the inherited implementation is taken, we use **super** qualified by the supertype name in angle brackets, e.g. `super<Base>`:-->
В <b>Kotlin</b> правила наследования реализации определены следующим образом: если класс наследует многочисленные реализации одного и того члена от ближайших родительских классов, он должен переопределить этот член и обеспечить свою собственную реализацию (возможно, используя одну из унаследованных). Для того, чтобы отметить конкретный супертип (родительский класс), от которого мы наследуем данную реализацию, мы используем ключевое слово <b class="keyword">super</b>. Для задания имени родительского супертипа используются треугольные скобки, например `super<Base>`:

```kotlin
open class Rectangle {
    open fun draw() { /* ... */ }
}

interface Polygon {
    fun draw() { /* ... */ } // члены интерфейса открыты ('open') по умолчанию
}

class Square() : Rectangle(), Polygon {
    // Компилятор требует, чтобы функция draw() была переопределена:
    override fun draw() {
        super<Rectangle>.draw() // вызов Rectangle.draw()
        super<Polygon>.draw() // вызов Polygon.draw()
    }
}
```

<!---It's fine to inherit from both `Rectangle` and `Polygon`,
but both of them have their implementations of `draw()`, so we have to override `draw()` in `Square`
and provide its own implementation that eliminates the ambiguity.-->
Это нормально, наследоваться одновременно от `Rectangle` и `Polygon`, но так как у каждого из них есть своя реализация функции `draw()`, мы должны переопределить `draw()` в `Square` и обеспечить нашу собственную реализацию этого метода для устранения получившейся неоднозначности.

<!--## Abstract Classes-->
## Абстрактные классы

<!--A class and some of its members may be declared **abstract**
An abstract member does not have an implementation in its class.
Note that we do not need to annotate an abstract class or function with open – it goes without saying.-->
Класс и некоторые его члены могут быть объявлены как <b class="keyword">abstract</b>. Абстрактный член не имеет реализации в своём классе.
Обратите внимание, что нам не надо аннотировать абстрактный класс или функцию словом **open** - это подразумевается и так.

<!--We can override a non-abstract open member with an abstract one-->
Можно переопределить неабстрактный **open** член абстрактным

```kotlin
open class Polygon {
    open fun draw() {}
}

abstract class Rectangle : Polygon() {
    abstract override fun draw()
}
```

<!--## Companion Objects-->
## Объекты-помощники

<!--If you need to write a function that can be called without having a class instance but needs access to the internals
of a class (for example, a factory method), you can write it as a member of an [object declaration](object-declarations.html)
inside that class.-->
Если вам нужно написать функцию, которая может быть использована без создания экземпляра класса, которая имела бы доступ к данным внутри этого класса (к примеру, фабричный метод), вы можете написать её как член [объявления объекта](object-declarations.html) внутри этого класса.

<!--Even more specifically, if you declare a [companion object](object-declarations.html#companion-objects) inside your class,
you'll be able to call its members using only the class name as a qualifier.-->
В частности, если вы объявляете [объект-помощник](object-declarations.html#companion-objects) в своём классе, у вас появляется возможность обращаться к членам класса, используя название класса в качестве классификатора.


## Прочие классы
Также обратите внимание на:
* [Изолированные классы (sealed classes)](sealed-classes.html)
* [Классы данных (data classes)](data-classes.html)
* [Вложенные классы (nested classes)](nested-classes.html)
* [Классы-перечисления (enum classes)](enum-classes.html)
