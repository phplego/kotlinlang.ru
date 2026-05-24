---
type: doc
layout: reference
category: "Syntax"
title: "Классы"
related:
    - functions.md
    - nested-classes.md
    - interfaces.md
url: https://kotlinlang.ru/docs/classes.html
---

<!-- При переводе статьи оригинальная версия была от 12 February 2026 -->

<!-- Classes -->
# Классы

<!-- Before creating classes, consider using a data class if the purpose is to store data.
Alternatively, think about extending an existing class with an extension, rather than creating a new one from scratch. -->
> Прежде чем создавать класс, подумайте, не подойдёт ли [класс данных](data-classes.html), если задача состоит в хранении данных.
> Также можно рассмотреть вариант с [расширением](extensions.html) существующего класса вместо создания нового с нуля.

<!-- Like other object-oriented languages, Kotlin uses classes to encapsulate data (properties) and behavior (functions)
for reusable, structured code. -->
Как и другие объектно-ориентированные языки, Kotlin использует *классы* для инкапсуляции данных (свойств) и поведения (функций)
в переиспользуемый структурированный код.

<!-- Classes are blueprints or templates for objects, which you create via constructors.
When you create an instance of a class, you are creating a concrete object based on that blueprint. -->
Классы — это схемы или шаблоны для объектов, которые создаются с помощью [конструкторов](classes.html#constructors-and-initializer-blocks).
Когда вы [создаёте экземпляр класса](classes.html#creating-instances), вы создаёте конкретный объект на основе такой схемы.

<!-- Kotlin offers concise syntax for declaring classes. To declare a class, use the `class` keyword followed by the class name: -->
Kotlin предоставляет лаконичный синтаксис объявления классов. Чтобы объявить класс, используйте ключевое слово `class`,
после которого указывается имя класса:

```kotlin
class Person { /*...*/ }
```

<a name="class-members"></a>
<!-- The class declaration consists of a class header and class body. -->
Объявление класса состоит из:

<!-- Class header, including but not limited to: -->
* **заголовка класса**, который включает, в частности:
    * ключевое слово `class`;
    * имя класса;
    * параметры типа, если они есть;
    * [основной конструктор](classes.html#primary-constructor), если он объявлен;
<!-- Class body (optional), surrounded by curly braces, and including class members such as: -->
* **тела класса** (необязательно), заключённого в фигурные скобки `{}` и включающего **члены класса**, например:
    * [дополнительные конструкторы](classes.html#secondary-constructors);
    * [блоки инициализации](classes.html#initializer-blocks);
    * [функции](functions.html);
    * [свойства](properties.html);
    * [вложенные и внутренние классы](nested-classes.html);
    * [объявления объектов](object-declarations.html).

<!-- You can keep both the class header and body to a bare minimum.
If the class doesn't have a body, you can omit the curly braces: -->
Заголовок и тело класса могут быть минимальными. Если у класса нет тела, фигурные скобки `{}` можно опустить:

```kotlin
// Класс с основным конструктором, но без тела
class Person(val name: String, var age: Int)
```

<!-- Here's an example that declares a class with a header and body, then creates an instance from it: -->
Ниже пример класса с заголовком и телом, после чего создаётся его [экземпляр](classes.html#creating-instances):

```kotlin
// Класс Person с основным конструктором,
// который инициализирует свойство name
class Person(val name: String) {
    // Тело класса со свойством age
    var age: Int = 0
}

fun main() {
    // Создаёт экземпляр класса Person вызовом конструктора
    val person = Person("Alice")

    // Обращается к свойствам экземпляра
    println(person.name)
    // Alice
    println(person.age)
    // 0
}
```

<a name="creating-instances-of-classes"></a>
<a name="creating-instances"></a>
<!-- ## Creating instances -->
## Создание экземпляров

<!-- An instance is created when you use the class as a blueprint to build a real object to work with in your program. -->
Экземпляр создаётся, когда вы используете класс как схему для построения реального объекта, с которым будет работать программа.

<!-- To create an instance of a class, use the class name followed by parentheses `()`, similar to calling a function: -->
Чтобы создать экземпляр класса, укажите имя класса и круглые скобки `()` — почти как при вызове [функции](functions.html):

```kotlin
// Создаёт экземпляр класса Person
val anonymousUser = Person()
```

<!-- In Kotlin, you can create instances: -->
В Kotlin экземпляры можно создавать:

<!-- Without arguments (`Person()`): creates an instance using the default values, if they are declared in the class.
With arguments (`Person(value)`): creates an instance by passing specific values. -->
* **без аргументов** (`Person()`): создаётся экземпляр со значениями по умолчанию, если они объявлены в классе;
* **с аргументами** (`Person(value)`): создаётся экземпляр с переданными конкретными значениями.

<!-- You can assign the created instance to a mutable (`var`) or read-only (`val`) variable: -->
Созданный экземпляр можно присвоить изменяемой (`var`) или неизменяемой (`val`) [переменной](basic-syntax.html#variables):

```kotlin
// Создаёт экземпляр со значением по умолчанию
// и присваивает его изменяемой переменной
var anonymousUser = Person()

// Создаёт экземпляр с конкретным значением
// и присваивает его неизменяемой переменной
val namedUser = Person("Joe")
```

<!-- It's possible to create instances wherever you need them, inside the main() function, within other functions, or inside another class.
Additionally, you can create instances inside another function and call that function from main(). -->
Экземпляры можно создавать там, где они нужны: внутри функции [`main()`](basic-syntax.html#program-entry-point),
внутри других функций или внутри другого класса. Также можно создать экземпляр внутри другой функции и вызвать эту функцию из `main()`.

<!-- The following code declares a Person class with a property for storing a name.
It also demonstrates how to create an instance with both the default constructor's value and a specific value: -->
Следующий код объявляет класс `Person` со свойством для хранения имени. В нём также показано, как создать экземпляр
со значением конструктора по умолчанию и с конкретным значением:

```kotlin
// Заголовок класса с основным конструктором,
// который инициализирует name значением по умолчанию
class Person(val name: String = "Sebastian")

fun main() {
    // Создаёт экземпляр со значением конструктора по умолчанию
    val anonymousUser = Person()

    // Создаёт экземпляр с конкретным значением
    val namedUser = Person("Joe")

    // Обращается к свойству name у экземпляров
    println(anonymousUser.name)
    // Sebastian
    println(namedUser.name)
    // Joe
}
```

<!-- In Kotlin, unlike other object-oriented programming languages, there is no need for the `new` keyword when creating class instances. -->
> В Kotlin, в отличие от некоторых других объектно-ориентированных языков программирования, при создании экземпляров классов
> не нужно ключевое слово `new`.

<!-- For information about creating instances of nested, inner, and anonymous inner classes, see the Nested classes section. -->
Информацию о создании экземпляров вложенных, внутренних и анонимных внутренних классов см. в разделе
[Вложенные классы](nested-classes.html).

<a name="constructors"></a>
<a name="constructors-and-initializer-blocks"></a>
<!-- ## Constructors and initializer blocks -->
## Конструкторы и блоки инициализации

<!-- When you create a class instance, you call one of its constructors. A class in Kotlin can have a primary constructor
and one or more secondary constructors. -->
При создании экземпляра класса вызывается один из его конструкторов. Класс в Kotlin может иметь
[*основной конструктор*](classes.html#primary-constructor) и один или несколько [*дополнительных конструкторов*](classes.html#secondary-constructors).

<!-- The primary constructor is the main way to initialize a class. You declare it in the class header.
A secondary constructor provides additional initialization logic. You declare it in the class body. -->
Основной конструктор — главный способ инициализировать класс. Он объявляется в заголовке класса.
Дополнительный конструктор предоставляет дополнительную логику инициализации и объявляется в теле класса.

<!-- Both primary and secondary constructors are optional, but a class must have at least one constructor. -->
И основной, и дополнительные конструкторы необязательны, но у класса должен быть хотя бы один конструктор.

<a name="primary-constructor"></a>
<!-- ### Primary constructor -->
### Основной конструктор

<!-- The primary constructor sets up the initial state of an instance when it's created. -->
Основной конструктор задаёт начальное состояние экземпляра при его [создании](classes.html#creating-instances).

<!-- To declare a primary constructor, place it in the class header after the class name: -->
Чтобы объявить основной конструктор, поместите его в заголовок класса после имени класса:

```kotlin
class Person constructor(name: String) { /*...*/ }
```

<!-- If the primary constructor doesn't have any annotations or visibility modifiers, you can omit the `constructor` keyword: -->
Если у основного конструктора нет [аннотаций](annotations.html) или [модификаторов видимости](visibility-modifiers.html#constructors),
ключевое слово `constructor` можно опустить:

```kotlin
class Person(name: String) { /*...*/ }
```

<!-- The primary constructor can declare parameters as properties. Use the `val` keyword before the argument name
to declare a read-only property and the `var` keyword for a mutable property: -->
Основной конструктор может объявлять параметры как свойства. Используйте ключевое слово `val` перед именем аргумента,
чтобы объявить свойство только для чтения, и `var`, чтобы объявить изменяемое свойство:

```kotlin
class Person(val name: String, var age: Int) { /*...*/ }
```

<!-- These constructor parameter properties are stored as part of the instance and are accessible from outside the class. -->
Такие свойства-параметры конструктора хранятся как часть экземпляра и доступны извне класса.

<!-- It's also possible to declare primary constructor parameters that are not properties.
These parameters don't have `val` or `var` in front of them, so they are not stored in the instance and are available only within the class body: -->
Также можно объявлять параметры основного конструктора, которые не являются свойствами. Перед ними нет `val` или `var`,
поэтому они не сохраняются в экземпляре и доступны только внутри тела класса:

```kotlin
// Параметр основного конструктора, который также является свойством
class PersonWithProperty(val name: String) {
    fun greet() {
        println("Hello, $name")
    }
}

// Только параметр основного конструктора: он не сохраняется как свойство
class PersonWithAssignment(name: String) {
    // Его нужно присвоить свойству, чтобы использовать позже
    val displayName: String = name

    fun greet() {
        println("Hello, $displayName")
    }
}
```

<!-- Properties declared in the primary constructor are accessible by member functions of the class: -->
Свойства, объявленные в основном конструкторе, доступны [функциям-членам](functions.html) класса:

```kotlin
// Класс с основным конструктором, который объявляет свойства
class Person(val name: String, var age: Int) {
    // Функция-член обращается к свойствам класса
    fun introduce(): String {
        return "Hi, I'm $name and I'm $age years old."
    }
}
```

<!-- You can also assign default values to properties in the primary constructor: -->
Свойствам в основном конструкторе также можно назначать значения по умолчанию:

```kotlin
class Person(val name: String = "John", var age: Int = 30) { /*...*/ }
```

<!-- If no value is passed to the constructor during instance creation, properties use their default value: -->
Если при [создании экземпляра](classes.html#creating-instances) в конструктор не передано значение, свойства используют значения по умолчанию:

```kotlin
// Класс с основным конструктором,
// включающим значения по умолчанию для name и age
class Person(val name: String = "John", var age: Int = 30)

fun main() {
    // Создаёт экземпляр со значениями по умолчанию
    val person = Person()
    println("Name: ${person.name}, Age: ${person.age}")
    // Name: John, Age: 30
}
```

<!-- You can use the primary constructor parameters to initialize additional class properties directly in the class body: -->
Параметры основного конструктора можно использовать для инициализации дополнительных свойств класса прямо в его теле:

```kotlin
// Класс с основным конструктором,
// включающим значения по умолчанию для name и age
class Person(
    val name: String = "John",
    var age: Int = 30
) {
    // Инициализирует свойство description
    // из параметров основного конструктора
    val description: String = "Name: $name, Age: $age"
}

fun main() {
    // Создаёт экземпляр класса Person
    val person = Person()
    // Обращается к свойству description
    println(person.description)
    // Name: John, Age: 30
}
```

<!-- As with functions, you can use trailing commas in constructor declarations: -->
Как и в функциях, в объявлениях конструкторов можно использовать [завершающие запятые](coding-conventions.html#trailing-commas):

```kotlin
class Person(
    val name: String,
    val lastName: String,
    var age: Int,
) { /*...*/ }
```

<a name="initializer-blocks"></a>
<!-- ### Initializer blocks -->
### Блоки инициализации

<!-- The primary constructor initializes the class and sets its properties. In most cases, you can handle this with simple code. -->
Основной конструктор инициализирует класс и задаёт его свойства. В большинстве случаев для этого достаточно простого кода.

<!-- If you need to perform more complex operations during instance creation, place that logic in initializer blocks inside the class body.
These blocks run when the primary constructor executes. -->
Если при [создании экземпляра](classes.html#creating-instances) нужно выполнить более сложные операции, поместите эту логику
в *блоки инициализации* внутри тела класса. Такие блоки выполняются при выполнении основного конструктора.

<!-- Declare initializer blocks with the `init` keyword followed by curly braces. Write within the curly braces any code that you want to run during initialization: -->
Блоки инициализации объявляются с помощью ключевого слова `init`, после которого идут фигурные скобки `{}`.
Внутри фигурных скобок напишите любой код, который должен выполняться при инициализации:

```kotlin
// Класс с основным конструктором, который инициализирует name и age
class Person(val name: String, var age: Int) {
    init {
        // Блок инициализации выполняется при создании экземпляра
        println("Person created: $name, age $age.")
    }
}

fun main() {
    // Создаёт экземпляр класса Person
    Person("John", 30)
    // Person created: John, age 30.
}
```

<!-- Add as many initializer blocks (`init {}`) as you need. They run in the order in which they appear in the class body,
along with property initializers: -->
Можно добавить столько блоков инициализации (`init {}`), сколько нужно. Они выполняются в том порядке,
в котором указаны в теле класса, вместе с инициализаторами свойств:

```kotlin
// Класс с основным конструктором, который инициализирует name и age
class Person(val name: String, var age: Int) {
    // Первый блок инициализации
    init {
        // Выполняется первым при создании экземпляра
        println("Person created: $name, age $age.")
    }

    // Второй блок инициализации
    init {
        // Выполняется после первого блока инициализации
        if (age < 18) {
            println("$name is a minor.")
        } else {
            println("$name is an adult.")
        }
    }
}

fun main() {
    // Создаёт экземпляр класса Person
    Person("John", 30)
    // Person created: John, age 30.
    // John is an adult.
}
```

<!-- You can use primary constructor parameters in initializer blocks.
For example, in the code above, the first and second initializers use the `name` and `age` parameters from the primary constructor. -->
Параметры основного конструктора можно использовать в блоках инициализации. Например, в коде выше первый и второй
инициализаторы используют параметры `name` и `age` из основного конструктора.

<!-- A common use case for `init` blocks is data validation. For example, by calling the `require` function: -->
Частый сценарий использования блоков `init` — проверка данных. Например, с помощью вызова функции
[`require`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/require.html):

```kotlin
class Person(val age: Int) {
    init {
        require(age > 0) { "age must be positive" }
    }
}
```

<a name="secondary-constructors"></a>
<!-- ### Secondary constructors -->
### Дополнительные конструкторы

<!-- In Kotlin, secondary constructors are additional constructors that a class can have beyond its primary constructor.
Secondary constructors are useful when you need multiple ways to initialize a class or for Java interoperability. -->
В Kotlin дополнительные конструкторы — это конструкторы, которые класс может иметь помимо основного конструктора.
Они полезны, когда нужно несколько способов инициализировать класс или обеспечить [совместимость с Java](java-to-kotlin-interop.html).

<!-- To declare a secondary constructor, use the `constructor` keyword inside the class body with the constructor parameters within parentheses.
Add the constructor logic within curly braces: -->
Чтобы объявить дополнительный конструктор, используйте ключевое слово `constructor` внутри тела класса,
указав параметры конструктора в круглых скобках `()`. Логику конструктора добавьте в фигурных скобках `{}`:

```kotlin
// Заголовок класса с основным конструктором, который инициализирует name и age
class Person(val name: String, var age: Int) {

    // Дополнительный конструктор принимает age как String
    // и преобразует его в Int
    constructor(name: String, age: String) : this(name, age.toIntOrNull() ?: 0) {
        println("$name created with converted age: ${this.age}")
    }
}

fun main() {
    // Использует дополнительный конструктор с age как String
    Person("Bob", "8")
    // Bob created with converted age: 8
}
```

<!-- The expression `age.toIntOrNull() ?: 0` uses the Elvis operator. For more information, see Null safety. -->
> Выражение `age.toIntOrNull() ?: 0` использует элвис-оператор. Дополнительную информацию см. в разделе
> [Null-безопасность](null-safety.html#elvis-operator).

<!-- In the code above, the secondary constructor delegates to the primary constructor via the `this` keyword,
passing `name` and the `age` value converted to an integer. -->
В коде выше дополнительный конструктор делегирует вызов основному конструктору через ключевое слово `this`,
передавая `name` и значение `age`, преобразованное в целое число.

<!-- In Kotlin, secondary constructors must delegate to the primary constructor.
This delegation ensures that all primary constructor initialization logic is executed before any secondary constructor logic runs. -->
В Kotlin дополнительные конструкторы должны делегировать вызов основному конструктору. Такая делегация гарантирует,
что вся логика инициализации основного конструктора выполнится до логики любого дополнительного конструктора.

<!-- Constructor delegation can be direct or indirect. -->
Делегация конструктора может быть:

<!-- Direct, where the secondary constructor calls the primary constructor immediately.
Indirect, where one secondary constructor calls another, which in turn delegates to the primary constructor. -->
* **прямой**, когда дополнительный конструктор сразу вызывает основной конструктор;
* **косвенной**, когда один дополнительный конструктор вызывает другой, а тот, в свою очередь, делегирует вызов основному конструктору.

<!-- Here's an example demonstrating how direct and indirect delegation works: -->
Ниже пример, показывающий, как работает прямая и косвенная делегация:

```kotlin
// Заголовок класса с основным конструктором, который инициализирует name и age
class Person(
    val name: String,
    var age: Int
) {
    // Дополнительный конструктор с прямой делегацией
    // основному конструктору
    constructor(name: String) : this(name, 0) {
        println("Person created with default age: $age and name: $name.")
    }

    // Дополнительный конструктор с косвенной делегацией:
    // this("Bob") -> constructor(name: String) -> основной конструктор
    constructor() : this("Bob") {
        println("New person created with default age: $age and name: $name.")
    }
}

fun main() {
    // Создаёт экземпляр на основе прямой делегации
    Person("Alice")
    // Person created with default age: 0 and name: Alice.

    // Создаёт экземпляр на основе косвенной делегации
    Person()
    // Person created with default age: 0 and name: Bob.
    // New person created with default age: 0 and name: Bob.
}
```

<!-- In classes with initializer blocks (`init {}`), the code within these blocks becomes part of the primary constructor.
Given that secondary constructors delegate to the primary constructor first, all initializer blocks and property initializers
run before the body of the secondary constructor. Even if the class has no primary constructor, the delegation still happens implicitly: -->
В классах с блоками инициализации (`init {}`) код внутри этих блоков становится частью основного конструктора.
Поскольку дополнительные конструкторы сначала делегируют вызов основному конструктору, все блоки инициализации
и инициализаторы свойств выполняются до тела дополнительного конструктора. Даже если у класса нет основного конструктора,
делегация всё равно происходит неявно:

```kotlin
// Заголовок класса без основного конструктора
class Person {
    // Блок инициализации выполняется при создании экземпляра
    init {
        // Выполняется до дополнительного конструктора
        println("1. First initializer block runs")
    }

    // Дополнительный конструктор, принимающий целочисленный параметр
    constructor(i: Int) {
        // Выполняется после блока инициализации
        println("2. Person $i is created")
    }
}

fun main() {
    // Создаёт экземпляр класса Person
    Person(1)
    // 1. First initializer block runs
    // 2. Person 1 created
}
```

<a name="classes-without-constructors"></a>
<!-- ### Classes without constructors -->
### Классы без конструкторов

<!-- Classes that don't declare any constructors (primary or secondary) have an implicit primary constructor with no parameters: -->
Классы, в которых не объявлены конструкторы (ни основной, ни дополнительные), имеют неявный основной конструктор без параметров:

```kotlin
// Класс без явно объявленных конструкторов
class Person {
    // Основной и дополнительные конструкторы не объявлены
}

fun main() {
    // Создаёт экземпляр класса Person
    // с помощью неявного основного конструктора
    val person = Person()
}
```

<!-- The visibility of this implicit primary constructor is public, meaning it can be accessed from anywhere.
If you don't want your class to have a public constructor, declare an empty primary constructor with non-default visibility: -->
Видимость такого неявного основного конструктора — `public`, то есть он доступен отовсюду. Если вы не хотите,
чтобы у класса был открытый конструктор, объявите пустой основной конструктор с нестандартной видимостью:

```kotlin
class Person private constructor() { /*...*/ }
```

<!-- On the JVM, if all primary constructor parameters have default values, the compiler implicitly provides
a parameterless constructor that uses those default values.
This makes it easier to use Kotlin with libraries such as Jackson or Spring Data JPA, which create class instances
through parameterless constructors.
In the following example, Kotlin implicitly provides a parameterless constructor `Person()` that uses the default value `""`: -->
> На JVM, если все параметры основного конструктора имеют значения по умолчанию, компилятор неявно предоставляет
> конструктор без параметров, который использует эти значения.
>
> Это упрощает использование Kotlin с библиотеками вроде [Jackson](https://github.com/FasterXML/jackson)
> или [Spring Data JPA](https://spring.io/projects/spring-data-jpa), которые создают экземпляры классов
> через конструкторы без параметров.
>
> В следующем примере Kotlin неявно предоставляет конструктор без параметров `Person()`, который использует значение
> по умолчанию `""`:
>
> ```kotlin
> class Person(val personName: String = "")
> ```

<a name="inheritance"></a>
<!-- ## Inheritance -->
## Наследование

<!-- Class inheritance in Kotlin allows you to create a new class (derived class) from an existing class (base class),
inheriting its properties and functions while adding or modifying behavior. -->
Наследование классов в Kotlin позволяет создавать новый класс (производный класс) на основе существующего класса (базового класса),
наследуя его свойства и функции, а также добавляя или изменяя поведение.

<!-- For detailed information about inheritance hierarchies and how to use of the `open` keyword, see the Inheritance section. -->
Подробную информацию об иерархиях наследования и использовании ключевого слова `open` см. в разделе [Наследование](inheritance.html).

<a name="abstract-classes"></a>
<!-- ## Abstract classes -->
## Абстрактные классы

<!-- In Kotlin, abstract classes are classes that can't be instantiated directly.
They are designed to be inherited by other classes which define their actual behavior. This behavior is called an implementation. -->
В Kotlin абстрактные классы — это классы, экземпляры которых нельзя создать напрямую. Они предназначены для наследования
другими классами, которые определяют их фактическое поведение. Такое поведение называется *реализацией*.

<!-- An abstract class can declare abstract properties and functions, which must be implemented by subclasses. -->
Абстрактный класс может объявлять абстрактные свойства и функции, которые должны быть реализованы в подклассах.

<!-- Abstract classes can also have constructors.
These constructors initialize class properties and enforce required parameters for subclasses.
Declare an abstract class using the `abstract` keyword: -->
У абстрактных классов также могут быть конструкторы. Эти конструкторы инициализируют свойства класса и задают обязательные
параметры для подклассов. Абстрактный класс объявляется с помощью ключевого слова `abstract`:

```kotlin
abstract class Person(val name: String, val age: Int)
```

<!-- An abstract class can have both abstract and non-abstract members (properties and functions).
To declare a member as abstract, you must use the `abstract` keyword explicitly. -->
У абстрактного класса могут быть как абстрактные, так и неабстрактные члены (свойства и функции).
Чтобы объявить член абстрактным, нужно явно использовать ключевое слово `abstract`.

<!-- You don't need to annotate abstract classes or functions with the `open` keyword because they are implicitly inheritable by default.
For more details about the `open` keyword, see Inheritance. -->
Абстрактные классы и функции не нужно помечать ключевым словом `open`, потому что они по умолчанию доступны для наследования.
Подробнее о ключевом слове `open` см. в разделе [Наследование](inheritance.html).

<!-- Abstract members don't have an implementation in the abstract class.
You define the implementation in a subclass or inheriting class with an `override` function or property: -->
Абстрактные члены не имеют реализации в абстрактном классе. Реализация определяется в подклассе или наследующем классе
с помощью функции или свойства с модификатором `override`:

```kotlin
// Абстрактный класс с основным конструктором,
// который объявляет name и age
abstract class Person(
    val name: String,
    val age: Int
) {
    // Абстрактный член:
    // не предоставляет реализацию
    // и должен быть реализован в подклассах
    abstract fun introduce()

    // Неабстрактный член: имеет реализацию
    fun greet() {
        println("Hello, my name is $name.")
    }
}

// Подкласс, который предоставляет реализацию абстрактного члена
class Student(
    name: String,
    age: Int,
    val school: String
) : Person(name, age) {
    override fun introduce() {
        println("I am $name, $age years old, and I study at $school.")
    }
}

fun main() {
    // Создаёт экземпляр класса Student
    val student = Student("Alice", 20, "Engineering University")

    // Вызывает неабстрактный член
    student.greet()
    // Hello, my name is Alice.

    // Вызывает переопределённый абстрактный член
    student.introduce()
    // I am Alice, 20 years old, and I study at Engineering University.
}
```

<a name="companion-objects"></a>
<!-- ## Companion objects -->
## Вспомогательные объекты

<!-- In Kotlin, each class can have a companion object.
Companion objects are a type of object declaration that allows you to access its members using the class name without creating a class instance. -->
В Kotlin у каждого класса может быть [вспомогательный объект](object-declarations.html#companion-objects).
Вспомогательные объекты — это разновидность объявления объекта, которая позволяет обращаться к его членам по имени класса
без создания экземпляра класса.

<!-- Suppose you need to write a function that can be called without creating an instance of a class, but it is still logically
connected to the class (such as a factory function). In that case, you can declare it inside a companion object declaration within the class: -->
Предположим, вам нужно написать функцию, которую можно вызвать без создания экземпляра класса, но которая логически связана
с классом, например фабричную функцию. В таком случае её можно объявить внутри вспомогательного [объявления объекта](object-declarations.html)
в классе:

```kotlin
// Класс с основным конструктором, который объявляет свойство name
class Person(
    val name: String
) {
    // Тело класса со вспомогательным объектом
    companion object {
        fun createAnonymous() = Person("Anonymous")
    }
}

fun main() {
    // Вызывает функцию без создания экземпляра класса
    val anonymous = Person.createAnonymous()
    println(anonymous.name)
    // Anonymous
}
```

<!-- If you declare a companion object inside your class, you can access its members using only the class name as a qualifier. -->
Если вы объявляете вспомогательный объект внутри класса, к его членам можно обращаться, используя только имя класса как квалификатор.

<!-- For more information, see Companion objects. -->
Дополнительную информацию см. в разделе [Вспомогательные объекты](object-declarations.html#companion-objects).
