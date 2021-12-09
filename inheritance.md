---
type: doc
layout: reference
title: "Наследование"
url: https://kotlinlang.ru/docs/inheritance.html
---

<!-- При переводе статьи оригинальная версия была от 08 July 2021 -->

<!-- Inheritance -->
# Наследование

<!-- All classes in Kotlin have a common superclass, `Any`, which is the default superclass for a class with no supertypes declared: -->
Для всех классов в Kotlin родительским суперклассом является класс `Any`. Он также является родительским классом для любого класса,
в котором не указан какой-либо другой родительский класс.

```kotlin
class Example // Неявно наследуется от Any
```

<!-- `Any` has three methods: `equals()`, `hashCode()`, and `toString()`. Thus, these methods are defined for all Kotlin classes. -->
У `Any` есть три метода: `equals()`, `hashCode()` и `toString()`. Эти методы определены для всех классов в Kotlin.

<!-- By default, Kotlin classes are final – they can’t be inherited. To make a class inheritable, mark it with the `open` keyword: -->
По умолчанию все классы в Kotlin имеют статус final, который блокирует возможность наследования.
Чтобы сделать класс наследуемым, его нужно пометить ключевым словом `open`.

```kotlin
open class Base // Класс открыт для наследования
```

<!-- To declare an explicit supertype, place the type after a colon in the class header: -->
Для явного объявления суперкласса мы помещаем его имя за знаком двоеточия в оглавлении класса:

```kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

<!-- If the derived class has a primary constructor, the base class can (and must) be initialized in that primary constructor
according to its parameters. -->
Если у класса есть основной конструктор, базовый тип может (и должен) быть проинициализирован там же, с использованием параметров основного конструктора.

<!-- If the derived class has no primary constructor, then each secondary constructor has to initialize the base type using
the `super` keyword or it has to delegate to another constructor which does. Note that in this case different secondary
constructors can call different constructors of the base type: -->
Если у класса нет основного конструктора, тогда каждый последующий дополнительный конструктор должен включать в себя инициализацию базового типа
с помощью ключевого слова `super` или давать отсылку на другой конструктор, который это делает.
Примечательно, что любые дополнительные конструкторы могут ссылаться на разные конструкторы базового типа.

```kotlin
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```

<a name="overriding-methods"></a>
<!-- ## Overriding methods -->
## Переопределение методов класса

<!--Kotlin requires explicit modifiers for overridable members and overrides:-->
Kotlin требует явно указывать модификаторы и для членов, которые могут быть переопределены, и для самого переопределения.

```kotlin
open class Shape {
    open fun draw() { /*...*/ }
    fun fill() { /*...*/ }
}

class Circle() : Shape() {
    override fun draw() { /*...*/ }
}
```

<!-- The `override` modifier is required for `Circle.draw()`. If it were missing, the compiler would complain. If there is no
`open` modifier on a function, like `Shape.fill()`, declaring a method with the same signature in a subclass is not allowed,
either with `override` or without it. The `open` modifier has no effect when added to members of a final class – a class
without an `open` modifier. -->
Для `Circle.draw()` необходим модификатор `override`. В случае её отсутствия компилятор выдаст ошибку.
Если у функции типа `Shape.fill()` нет модификатора `open`, объявление метода с такой же сигнатурой в производном классе невозможно,
с `override` или без. Модификатор `open` не действует при добавлении к членам final класса (т.е. класса без модификатора `open`).

<!-- A member marked `override` is itself open, so it may be overridden in subclasses. If you want to prohibit re-overriding,
use `final`: -->
Член класса, помеченный `override`, является сам по себе open, т.е. он может быть переопределён в производных классах.
Если вы хотите запретить возможность переопределения такого члена, используйте `final`.

```kotlin
open class Rectangle() : Shape() {
    final override fun draw() { /*...*/ }
}
```

<!-- ## Overriding properties -->
## Переопределение свойств класса

<!-- The overriding mechanism works on properties in the same way that it does on methods. Properties declared on a superclass
that are then redeclared on a derived class must be prefaced with `override`, and they must have a compatible type.
Each declared property can be overridden by a property with an initializer or by a property with a `get` method: -->
Переопределение свойств работает также, как и переопределение методов; все свойства, унаследованные от суперкласса, должны быть помечены ключевым словом `override`,
а также должны иметь совместимый тип.
Каждое объявленное свойство может быть переопределено свойством с инициализацией или свойством с `get`-методом.

```kotlin
open class Shape {
    open val vertexCount: Int = 0
}

class Rectangle : Shape() {
    override val vertexCount = 4
}
```

<!-- You can also override a `val` property with a `var` property, but not vice versa. This is allowed because a `val` property
essentially declares a `get` method, and overriding it as a `var` additionally declares a `set` method in the derived class. -->
Вы также можете переопределить свойство `val` свойством `var`, но не наоборот.
Это разрешено, поскольку свойство `val` объявляет `get`-метод, а при переопределении его как `var` дополнительно объявляется `set`-метод в производном классе.

<!-- Note that you can use the `override` keyword as part of the property declaration in a primary constructor: -->
Обратите внимание, что ключевое слово `override` может быть использовано в основном конструкторе класса как часть объявления свойства.

```kotlin
interface Shape {
    val vertexCount: Int
}

class Rectangle(override val vertexCount: Int = 4) : Shape // Всегда имеет 4 вершины

class Polygon : Shape {
    override var vertexCount: Int = 0  // Может быть установлено любое количество
}
```

<!--## Derived class initialization order-->
## Порядок инициализации производного класса

<!-- During the construction of a new instance of a derived class, the base class initialization is done as the first step
(preceded only by evaluation of the arguments for the base class constructor), which means that it happens before the
initialization logic of the derived class is run. -->
При создании нового экземпляра класса в первую очередь выполняется инициализация базового класса (этому шагу предшествует только оценка аргументов,
передаваемых в конструктор базового класса) и, таким образом, происходит до запуска логики инициализации производного класса.

```kotlin
open class Base(val name: String) {

    init { println("Инициализация класса Base") }

    open val size: Int = 
        name.length.also { println("Инициализация свойства size в класса Base: $it") }
}

class Derived(
    name: String,
    val lastName: String,
) : Base(name.replaceFirstChar { it.uppercase() }.also { println("Аргументы, переданные в конструктор класса Base: $it") }) {

    init { println("Инициализация класса Derived") }

    override val size: Int =
        (super.size + lastName.length).also { println("Инициализация свойства size в классе Derived: $it") }
}

fun main() {
    println("Построение класса Derived(\"hello\", \"world\")")
    Derived("hello", "world")
}
```

<!-- This means that when the base class constructor is executed, the properties declared or overridden in the derived class
have not yet been initialized. Using any of those properties in the base class initialization logic (either directly or
indirectly through another overridden `open` member implementation) may lead to incorrect behavior or a runtime failure.
When designing a base class, you should therefore avoid using `open` members in the constructors, property initializers,
or `init` blocks. -->
Это означает, что свойства, объявленные или переопределенные в производном классе, не инициализированы к моменту вызова конструктора базового класса.
Если какое-либо из этих свойств используется в логике инициализации базового класса (прямо или косвенно через другую переопределенную `open` реализацию члена класса),
это может привести к некорректному поведению или сбою во время выполнения. Поэтому при разработке базового класса следует избегать использования членов
с ключевым словом `open` в конструкторах, инициализации свойств и блоков инициализации (`init`).

<!-- ## Calling the superclass implementation -->
### Вызов функций и свойств суперкласса

<!-- Code in a derived class can call its superclass functions and property accessor implementations using the `super` keyword: -->
Производный класс может вызывать реализацию функций и свойств своего суперкласса, используя ключевое слово `super`.

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

<!-- Inside an inner class, accessing the superclass of the outer class is done using the `super` keyword qualified with the
outer class name: `super@Outer`: -->
Во внутреннем классе доступ к суперклассу внешнего класса осуществляется при помощи ключевого слова `super`, за которым следует имя внешнего класса: `super@Outer`.

```kotlin
class FilledRectangle: Rectangle() {
    override fun draw() {
        val filler = Filler()
        filler.drawAndFill()
    }

    inner class Filler {
        fun fill() { println("Filling") }
        fun drawAndFill() {
            super@FilledRectangle.draw() // Вызывает реализацию функции draw() класса Rectangle
            fill()
            println("Нарисованный прямоугольник заполнен ${super@FilledRectangle.borderColor} цветом") // Используется реализация get()-метода свойства borderColor в классе
        }
    }
}
```

<!-- ## Overriding rules -->
## Правила переопределения

<!-- In Kotlin, implementation inheritance is regulated by the following rule: if a class inherits multiple implementations of
the same member from its immediate superclasses, it must override this member and provide its own implementation (perhaps,
using one of the inherited ones). -->
В Kotlin правила наследования реализации определены следующим образом: если класс наследует многочисленные реализации одного и того члена от ближайших родительских классов,
он должен переопределить этот член и обеспечить свою собственную реализацию (возможно, используя одну из унаследованных).

<!-- To denote the supertype from which the inherited implementation is taken, use `super` qualified by the supertype name in
angle brackets, such as `super<Base>`: -->
Для того, чтобы отметить конкретный супертип (родительский класс), от которого мы наследуем данную реализацию,
используйте ключевое слово `super`. Для задания имени родительского супертипа используются треугольные скобки, например `super<Base>`.

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

<!-- It's fine to inherit from both `Rectangle` and `Polygon`,
but both of them have their implementations of `draw()`, so you need to override `draw()` in `Square` and provide a separate
implementation for it to eliminate the ambiguity. -->
Это нормально, наследоваться одновременно от `Rectangle` и `Polygon`,
но так как у каждого из них есть своя реализация функции `draw()`,
мы должны переопределить `draw()` в `Square` и обеспечить нашу собственную реализацию этого метода для устранения получившейся неоднозначности.
