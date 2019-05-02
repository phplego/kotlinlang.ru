---
type: doc
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

<!--Classes in Kotlin are declared using the keyword **class**:-->
Классы в <b>Kotlin</b> объявляются с помощью использования ключевого слова **class**:

``` kotlin
class Invoice {
}
```

<!--The class declaration consists of the class name, the class header (specifying its type parameters, the primary
constructor etc.) and the class body, surrounded by curly braces. Both the header and the body are optional;
if the class has no body, curly braces can be omitted.-->
Объявление класса состоит из имени класса, заголовка (указания типов его параметров, первичного конструктора и т.п) и тела класса,
заключённого в фигурные скобки. И заголовок, и тело класса являются необязательными составляющими: если у класса нет тела, фигурные скобки могут быть опущены.

``` kotlin
class Empty
```

<a name="constructors"></a>

<!--### Constructors-->
### Конструкторы

<!--A class in Kotlin can have a **primary constructor** and one or more **secondary constructors**. The primary
constructor is part of the class header: it goes after the class name (and optional type parameters).-->
Класс в <b>Kotlin</b> может иметь первичный конструктор (**primary constructor**) и один или более вторичных конструкторов (**secondary constructors**). Первичный конструктор является частью заголовка класса, его объявление идёт сразу после имени класса (и необязательных параметров):

``` kotlin
class Person constructor(firstName: String)
```

<!--If the primary constructor does not have any annotations or visibility modifiers, the **constructor**
keyword can be omitted:-->
Если у конструктора нет аннотаций и модификаторов видимости, ключевое слово **constructor** может быть опущено:

``` kotlin
class Person(firstName: String)
```

<!--The primary constructor cannot contain any code. Initialization code can be placed
in **initializer blocks**, which are prefixed with the **init**keyword:-->
Первичный конструктор не может содержать в себе исполняемого кода. Инициализирующий код может быть помещён в соответствующий блок (**initializers blocks**), который помечается словом **init**:


``` kotlin
class Customer(name: String) {
    init {
        logger.info("Customer initialized with value ${name}")
    }
}
```

<!--Note that parameters of the primary constructor can be used in the initializer blocks. They can also be used in
property initializers declared in the class body:-->
Обратите внимание, что параметры первичного конструктора могут быть использованы в инициализирующем блоке. Они также могут быть использованы при инициализации свойств в теле класса:

``` kotlin
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

<!--In fact, for declaring properties and initializing them from the primary constructor, Kotlin has a concise syntax:-->
В действительности, для объявления и инициализации свойств первичного конструктора в <b>Kotlin</b> есть лаконичное синтаксическое решение:

``` kotlin
class Person(val firstName: String, val lastName: String, var age: Int) {
  // ...
}
```

<!--Much the same way as regular properties, the properties declared in the primary constructor can be
mutable (**var**) or read-only (**val**).-->
Свойства, объявленные в первичном конструкторе, могут быть изменяемые (**var**) и неизменяемые (**val**). 

<!--If the constructor has annotations or visibility modifiers, the **constructor** keyword is required, and
the modifiers go before it:-->
Если у конструктора есть аннотации или модификаторы видимости, ключевое слово **constructor** обязательно, и модификаторы используются перед ним:

``` kotlin
class Customer public @Inject constructor(name: String) { ... }
```

<!--For more details, see [Visibility Modifiers](visibility-modifiers.html#constructors).-->
Для более подробной информации по данному вопросу см. "[Модификаторы доступа](visibility-modifiers.html#constructors)".

<!--#### Secondary Constructors-->
#### Второстепенные конструкторы 

<!--The class can also declare **secondary constructors**, which are prefixed with **constructor**:-->
В классах также могут быть объявлены дополнительные конструкторы (**secondary constructors**), перед которыми используется ключевое слово **constructor**:

``` kotlin
class Person {
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

<!--If the class has a primary constructor, each secondary constructor needs to delegate to the primary constructor, either
directly or indirectly through another secondary constructor(s). Delegation to another constructor of the same class
is done using the **this** keyword:-->
Если у класса есть главный (первичный) конструктор, каждый последующий конструктор должен прямо или косвенно ссылаться (через другой(_ие_) конструктор(_ы_)) на первичный:

``` kotlin
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

<!--If a non-abstract class does not declare any constructors (primary or secondary), it will have a generated primary
constructor with no arguments. The visibility of the constructor will be public. If you do not want your class
to have a public constructor, you need to declare an empty primary constructor with non-default visibility:-->
Если в абстрактном классе не объявлено никаких конструкторов (первичных или второстепенных), у этого класса автоматически сгенерируется пустой конструктор без параметров. Видимость этого конструктора будет **public**. Если вы не желаете иметь класс с открытым **public** конструктором, вам необходимо объявить пустой конструктор с соответствующим модификатором видимости:

``` kotlin
class DontCreateMe private constructor () {
}
```
>
 **Примечание**: В виртуальной машине JVM компилятор генерирует дополнительный конструктор без параметров в случае, если все параметры первичного конструктора имеют значения по умолчанию. Это делает использование таких библиотек, как <b>Jackson</b> и <b>JPA</b>, более простым в языке <b>Kotlin</b>, так как они используют пустые конструкторы при создании экземпляров классов.

``` kotlin
class Customer(val customerName: String = "")
```


<!--### Creating instances of classes-->
### Создание экземпляров классов

<!--To create an instance of a class, we call the constructor as if it were a regular function:-->
Для создания экземпляра класса конструктор вызывается так, как если бы он был обычной функцией:

``` kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

<!--Note that Kotlin does not have a **new** keyword.-->
Обращаем ваше внимание на то, что в <b>Kotlin</b> не используется ключевое слово **new**.


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
Для всех классов в языке <b>Koltin</b> родительским суперклассом является класс `Any`. Он также является родительским классом для любого класса, в котором не указан какой-либо другой родительский класс:

``` kotlin
class Example // Implicitly inherits from Any
```

<!--`Any` is not `java.lang.Object`; in particular, it does not have any members other than `equals()`, `hashCode()` and `toString()`.
Please consult the [Java interoperability](java-interop.html#object-methods) section for more details.-->
Класс `Any` не является аналогом `java.lang.Object`. В частности, у него нет никаких членов кроме методов: `equals()`, `hashCode()`, и `toString()`. Пожалуйста, ознакомьтесь с [совместимостью c Java](http://kotlinlang.org/docs/reference/java-interop.html#object-methods) для более подробной информации.

<!--To declare an explicit supertype, we place the type after a colon in the class header:-->
Для явного объявления суперкласса мы помещаем его имя за знаком двоеточия в оглавлении класса:

``` kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

<!--If the class has a primary constructor, the base type can (and must) be initialized right there,
using the parameters of the primary constructor.-->
Если у класса есть основной конструктор, базовый тип может (и должен) быть проинициализирован там же, с использованием параметров первичного конструктора.

<!--If the class has no primary constructor, then each secondary constructor has to initialize the base type
using the **super** keyword, or to delegate to another constructor which does that.
Note that in this case different secondary constructors can call different constructors of the base type:-->
Если у класса нет первичного конструктора, тогда каждый последующий второстепенный конструктор должен включать в себя инициализацию базового типа с помощью ключевого слова **super** или давать отсылку на другой конструктор, который это делает.
Примечательно, что любые вторичные конструкторы могут ссылаться на разные конструкторы базового типа:

``` kotlin
class MyView : View {
    constructor(ctx: Context) : super(ctx) {
    }

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs) {
    }
}
```

<!--The **open** annotation on a class is the opposite of Java's **final**: it allows others
to inherit from this class. By default, all classes in Kotlin are final, which
corresponds to [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html),
Item 17: *Design and document for inheritance or else prohibit it*.-->
Ключевое слово **open** является противоположностью слову **final** в <b>Java</b>: оно позволяет другим классам наследоваться от данного. По умолчанию, все классы в <b>Kotlin</b> имеют статус **final**, что отвечает [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 17: *Design and document for inheritance or else prohibit it*.

<!--### Overriding Members-->
<a name="overriding-properties"></a>
### Переопределение членов класса

<!--As we mentioned before, we stick to making things explicit in Kotlin. And unlike Java, Kotlin requires explicit
annotations for overridable members (we call them *open*) and for overrides:-->
Как упоминалось ранее, мы придерживаемся идеи определённости и ясности в языке <b>Kotlin</b>. И, в отличие от <b>Java</b>, <b>Kotlin</b>
требует чёткой аннотации и для членов, которые могут быть переопределены, и для самого переопределения:

``` kotlin
open class Base {
  open fun v() {}
  fun nv() {}
}
class Derived() : Base() {
  override fun v() {}
}
```

<!--The **override** annotation is required for `Derived.v()`. If it were missing, the compiler would complain.
If there is no **open** annotation on a function, like `Base.nv()`, declaring a method with the same signature in a subclass is illegal,
either with **override** or without it. In a final class (e.g. a class with no **open** annotation), open members are prohibited.-->
Для `Derived.v()` необходима аннотация  **override**. В случае её отсутствия компилятор выдаст ошибку. Если у функции типа `Base.nv()` нет аннотации **open**, объявление метода с такой же сигнатурой в производном классе невозможно, с **override** или без. В **final** классе (классе без аннотации **open**),  запрещено использование аннотации **open** для его членов. 

<!--A member marked **override** is itself open, i.e. it may be overridden in subclasses. If you want to prohibit re-overriding, use **final**:-->
Член класса, помеченный **override**, является сам по себе **open**, т.е. он может быть переопределён в производных классах. Если вы хотите запретить возможность переопределения такого члена, используйте **final**:

``` kotlin
open class AnotherDerived() : Base() {
  final override fun v() {}
}
```

<!--#### Wait! How will I hack my libraries now?!-->
#### Стойте! Как мне теперь хакнуть свои библиотеки?

<!--One issue with our approach to overriding (classes and members final by default) is that it would be difficult to subclass something inside the libraries you use to override some method that was not intended for overriding by the library designer, and introduce some nasty hack there.-->
При нашем подходе к переопределению классов и их членов (которые по дефолту **final**) будет сложно унаследоваться от чего-нибудь внутри используемых вами библиотек для того, чтобы переопределить не предназначенный для этого метод и внедрить туда свой гнусный хак.

<!--We think that this is not a disadvantage, for the following reasons:-->
Мы думаем, что это не является недостатком по следующим причинам:

<!--* Best practices say that you should not allow these hacks anyway
* People successfully use other languages (C++, C#) that have similar approach
* If people really want to hack, there still are ways: you can always write your hack in Java and call it from Kotlin (*see [Java Interop](java-interop.html)*), and Aspect frameworks always work for these purposes-->
* Опыт поколений говорит о том, что, в любом случае, лучше не позволять внедрять такие хаки
* Люди успешно используют другие языки (<b>C++</b>, <b>C#</b>), которые имеют аналогичный подход к этому вопросу
* Если кто-то действительно хочет хакнуть, пусть напишет свой код на <b>Java</b> и вызовет его в <b>Kotlin</b> _(см. [Java-совместимость](http://kotlinlang.org/docs/reference/java-interop.html))_

<!--### Overriding Rules-->
### Правила переопределения

<!--In Kotlin, implementation inheritance is regulated by the following rule: if a class inherits many implementations of the same member from its immediate superclasses,
it must override this member and provide its own implementation (perhaps, using one of the inherited ones).
To denote the supertype from which the inherited implementation is taken, we use **super** qualified by the supertype name in angle brackets, e.g. `super<Base>`:-->
В <b>Kotlin</b> правила наследования имплементации определены следующим образом: если класс перенимает большое количество имплементаций одного и того члена от ближайших родительских классов, он должен переопределить этот член и обеспечить свою собственную имплементацию (возможно, используя одну из унаследованных). Для того, чтобы отметить супертип (родительский класс), от которого мы унаследовали данную имплементацию, мы используем ключевое слово **super**. Для уточнения имя родительского супертипа используются треугольные скобки, например `super<Base>`:

``` kotlin
open class A {
  open fun f() { print("A") }
  fun a() { print("a") }
}

interface B {
  fun f() { print("B") } // interface members are 'open' by default
  fun b() { print("b") }
}

class C() : A(), B {
  // The compiler requires f() to be overridden:
  override fun f() {
    super<A>.f() // call to A.f()
    super<B>.f() // call to B.f()
  }
}
```

<!---It's fine to inherit from both `A` and `B`, and we have no problems with `a()` and `b()` since `C` inherits only one implementation of each of these functions.
But for `f()` we have two implementations inherited by `C`, and thus we have to override `f()` in `C`
and provide our own implementation that eliminates the ambiguity.-->
Нормально наследоваться одновременно от `A` и `B`. У нас не возникнет никаких проблем с `a()` и `b()` в том случае, если `C` унаследует только одну имплементацию этих функций.
Но для `f()` у нас есть две имплементации, унаследованные классом `C`, поэтому необходимо переопределить `f()` в `C` и обеспечить нашу собственную реализацию этого метода для устранения получившейся неоднозначности.

<!--## Abstract Classes-->
## Абстрактные классы

<!--A class and some of its members may be declared **abstract**
An abstract member does not have an implementation in its class.
Note that we do not need to annotate an abstract class or function with open – it goes without saying.-->
Класс и некоторые его члены могут быть объявлены как **abstract**. Абстрактный член не имеет реализации в его классе.
Обратите внимание, что нам не надо аннотировать абстрактный класс или функцию словом **open** - это подразумевается и так.

<!--We can override a non-abstract open member with an abstract one-->
Можно переопределить не-абстрактный **open** член абстрактным

``` kotlin
open class Base {
  open fun f() {}
}

abstract class Derived : Base() {
  override abstract fun f()
}
```

<!--## Companion Objects-->
## Объекты-помощники

<!--In Kotlin, unlike Java or C#, classes do not have static methods. In most cases, it's recommended to simply use
package-level functions instead.-->
В <b>Kotlin</b>, в отличие от <b>Java</b> или <b>C#</b>, в классах не бывает статических методов. В большинстве случаев рекомендуется использовать функции на уровне пакета (ориг.: _"package-level functions"_).

<!--If you need to write a function that can be called without having a class instance but needs access to the internals
of a class (for example, a factory method), you can write it as a member of an [object declaration](object-declarations.html)
inside that class.-->
Если вам нужно написать функцию, которая может быть использована без создания экземпляра класса, но имела бы доступ к данным внутри этого класса (к примеру, фабричный метод), вы можете написать её как член [объявления объекта](object-declarations.html) внутри этого класса.

<!--Even more specifically, if you declare a [companion object](object-declarations.html#companion-objects) inside your class,
you'll be able to call its members with the same syntax as calling static methods in Java/C#, using only the class name
as a qualifier.-->
В частности, если вы объявляете [объект-помощник](object-declarations.html#companion-objects) в своём классе, у вас появляется возможность обращаться к его членам, используя тот же синтаксис, как при использовании статических методов в <b>Java</b>/<b>C#</b> (указав название класса для доступа).


## Прочие классы
Также обратите внимание на:
* [Изолированные классы (sealed classes)](sealed-classes.html)
* [Классы данных (data classes)](data-classes.html)
* [Вложенные классы (nested classes)](nested-classes.html)
* [Классы-перечисления (enum classes)](enum-classes.html)
