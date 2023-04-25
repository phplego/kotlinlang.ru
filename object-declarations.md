---
type: doc
layout: reference
category: "Syntax"
title: "Объекты"
url: https://kotlinlang.ru/docs/object-declarations.html
---

<!-- При переводе статьи оригинальная версия была от 04 August 2021 -->

<!-- # Object expressions and declarations -->
# Анонимные объекты и объявление объектов

<!-- Sometimes you need to create an object that is a slight modification of some class, without explicitly declaring a new
subclass for it. Kotlin can handle this with _object expressions_ and _object declarations_. -->
Иногда нам необходимо получить экземпляр некоторого класса с незначительной модификацией, желательно без написания
нового подкласса. Kotlin справляется с этим с помощью *объявления объектов* и *анонимных объектов*.

<a name="object-expressions"></a>

<!-- ## Object expressions -->
## Анонимные объекты (ориг.: *Object expressions*)

<!-- _Object expressions_ create objects of anonymous classes, that is, classes that aren't explicitly declared with the `class`
declaration. Such classes are useful for one-time use. You can define them from scratch, inherit from existing classes,
or implement interfaces. Instances of anonymous classes are also called _anonymous objects_ because they are defined by
an expression, not a name. -->
Объекты анонимных классов, т.е. классов, которые явно не объявлены с помощью `class`, полезны для одноразового
использования. Вы можете объявить их с нуля, наследовать от существующих классов или реализовать интерфейсы. Экземпляры
анонимных классов также могут называться *анонимными объектами*, потому что они объявляются выражением, а не именем.

<a name="creating-anonymous-objects-from-scratch"></a>

<!-- ### Creating anonymous objects from scratch -->
### Создание анонимных объектов с нуля

<!-- Object expressions start with the `object` keyword. -->
Анонимный объект начинается с ключевого слова `object`.

<!-- If you just need an object that doesn’t have any nontrivial supertypes, write its members in curly braces after `object`: -->
Если вам просто нужен объект, у которого нет нетривиальных супертипов, перечислите его члены в фигурных скобках после `object`.

```kotlin
val helloWorld = object {
    val hello = "Hello"
    val world = "World"
    // тип анонимных объектов - Any, поэтому `override` необходим в `toString()`
    override fun toString() = "$hello $world"
}
```

<a name="inheriting-anonymous-objects-from-supertypes"></a>

<!-- ### Inheriting anonymous objects from supertypes -->
### Наследование анонимных объектов от супертипов

<!-- To create an object of an anonymous class that inherits from some type (or types), specify this type after `object` and a
colon (`:`). Then implement or override the members of this class as if you were [inheriting](inheritance.md) from it: -->
Для того чтобы создать объект анонимного класса, который наследуется от какого-то типа (типов), укажите этот тип после
`object` и двоеточия (`:`). Затем реализуйте или переопределите члены этого класса, как если бы вы
[наследовали](inheritance.md) от него.

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { /*...*/ }

    override fun mouseEntered(e: MouseEvent) { /*...*/ }
})
```

<!-- If a supertype has a constructor, pass appropriate constructor parameters to it.
Multiple supertypes can be specified as a comma-delimited list after the colon: -->
Если у супертипа есть конструктор, то в него должны быть переданы соответствующие параметры.
Множество супертипов может быть указано после двоеточия в виде списка, заполненного через запятую.

```kotlin
open class A(x: Int) {
    public open val y: Int = x
}

interface B { /*...*/ }

val ab: A = object : A(1), B {
    override val y = 15
}
```

<a name="using-anonymous-objects-as-return-and-value-types"></a>

<!-- ### Using anonymous objects as return and value types -->
### Использование анонимных объектов в качестве возвращаемых типов значений

<!-- When an anonymous object is used as a type of a local or [private](visibility-modifiers.md#packages) but not [inline](inline-functions.md)
declaration (function or property), all its members are accessible via this function or property: -->
Когда анонимный объект используется в качестве типа local или [private](visibility-modifiers.html#packages), но не
[встроенного](inline-functions.html) объявления (функции или свойства), все его члены доступны через эту функцию или
свойство.

```kotlin
class C {
    private fun getObject() = object {
        val x: String = "x"
    }

    fun printX() {
        println(getObject().x)
    }
}
```

<!-- If this function or property is public or private inline, its actual type is: -->
Если эта функция или свойство маркированы как public или встроенный private, то их тип:

<!-- * `Any` if the anonymous object doesn't have a declared supertype
* The declared supertype of the anonymous object, if there is exactly one such type
* The explicitly declared type if there is more than one declared supertype -->

* `Any`, если анонимный объект не имеет объявленного супертипа;
* Объявленный супертип анонимного объекта, если существует ровно один такой тип;
* Явно объявленный тип, если существует более одного объявленного супертипа.

<!-- In all these cases, members added in the anonymous object are not accessible. Overridden members are accessible if they
are declared in the actual type of the function or property: -->
Во всех этих случаях члены, добавленные в анонимный объект, недоступны. Переопределенные члены доступны, если они
объявлены в фактическом типе функции или свойства.

```kotlin
interface A {
    fun funFromA() {}
}
interface B

class C {
    // Возвращаемый тип Any. x недоступен
    fun getObject() = object {
        val x: String = "x"
    }

    // Возвращаемый тип A; x недоступен
    fun getObjectA() = object: A {
        override fun funFromA() {}
        val x: String = "x"
    }

    // Возвращаемый тип B; funFromA() и x недоступны
    fun getObjectB(): B = object: A, B { // требуется явный тип возвращаемого значения
        override fun funFromA() {}
        val x: String = "x"
    }
}
```

<a name="accessing-variables-from-anonymous-objects"></a>

<!-- ### Accessing variables from anonymous objects -->
### Доступ к переменным из анонимных объектов

<!-- The code in object expressions can access variables from the enclosing scope: -->
Код внутри объявленного объекта может обращаться к переменным из окружающей области видимости.

```kotlin
fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
    // ...
}
```

<a name="object-declarations-overview"></a>

<!-- ## Object declarations -->
## Объявления объектов (ориг.: *Object declarations*)

<!-- The [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern) pattern can be useful in several cases,
and Kotlin makes it easy to declare singletons: -->
[Синглтон](https://ru.wikipedia.org/wiki/%D0%9E%D0%B4%D0%B8%D0%BD%D0%BE%D1%87%D0%BA%D0%B0_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F))
\- очень полезный паттерн программирования, и Kotlin позволяет объявлять его довольно простым способом:

```kotlin
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ...
    }

    val allDataProviders: Collection<DataProvider>
        get() = // ...
}
```

<!-- This is called an _object declaration_, and it always has a name following the `object` keyword.
Just like a variable declaration, an object declaration is not an expression, and it cannot be used on the right-hand side
of an assignment statement. -->
Это называется *объявлением объекта* и всегда имеет приставку в виде ключевого слова `object`. Аналогично объявлению
переменной, объявление объекта не является выражением и не может быть использовано в правой части оператора присваивания.

<!-- The initialization of an object declaration is thread-safe and done on first access. -->
Инициализация объявления объекта потокобезопасна и выполняется при первом доступе.

<!-- To refer to the object, use its name directly: -->
Для непосредственной ссылки на объект используется его имя.

```kotlin
DataProviderManager.registerDataProvider( /*...*/ )
```

<!-- Such objects can have supertypes: -->
Подобные объекты могут иметь супертипы:

```kotlin
object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { /*...*/ }

    override fun mouseEntered(e: MouseEvent) { /*...*/ }
}
```

<!-- > Object declarations can't be local (that is, they can't be nested directly inside a function), but they can be nested
> into other object declarations or non-inner classes. -->

> Объявление объекта не может иметь локальный характер (т.е. быть вложенным непосредственно в функцию), но может быть
> вложено в объявление другого объекта или какого-либо невложенного класса.

<a name="companion-objects"></a>

<!-- ### Companion objects -->
### Вспомогательные объекты

<!-- An object declaration inside a class can be marked with the `companion` keyword: -->
Объявление объекта внутри класса может быть отмечено ключевым словом `companion`.

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```

<!-- Members of the companion object can be called simply by using the class name as the qualifier: -->
Для вызова членов такого `companion` объекта используется имя класса.

```kotlin
val instance = MyClass.create()
```

<!-- The name of the companion object can be omitted, in which case the name `Companion` will be used: -->
Необязательно указывать имя вспомогательного объекта. Тогда он будет назван `Companion`.

```kotlin
class MyClass {
    companion object { }
}

val x = MyClass.Companion
```

<!-- Class members can access the private members of the corresponding companion object. -->
Члены класса могут получить доступ к `private` членам соответствующего вспомогательного объекта.

<!-- The name of a class used by itself (not as a qualifier to another name) acts as a reference to the companion
object of the class (whether named or not): -->
Имя класса, используемого не в качестве определителя другого имени, действует как ссылка на вспомогательный объект
класса (независимо от того, именован он или нет).

```kotlin
class MyClass1 {
    companion object Named { }
}

val x = MyClass1

class MyClass2 {
    companion object { }
}

val y = MyClass2
```

<!-- Note that even though the members of companion objects look like static members in other languages, at runtime those
are still instance members of real objects, and can, for example, implement interfaces: -->
Хотя такие члены вспомогательных объектов и выглядят, как статические члены в других языках программирования, во время
выполнения они являются членами реальных объектов и могут реализовывать, к примеру, интерфейсы.

```kotlin
interface Factory<T> {
    fun create(): T
}

class MyClass {
    companion object : Factory<MyClass> {
        override fun create(): MyClass = MyClass()
    }
}

val f: Factory<MyClass> = MyClass
```

<!-- However, on the JVM you can have members of companion objects generated as real static methods and fields if you use
the `@JvmStatic` annotation. See the [Java interoperability](java-to-kotlin-interop.md#static-fields) section
for more detail. -->
Однако в JVM вы можете статически генерировать методы вспомогательных объектов и полей, используя аннотацию `@JvmStatic`.
См. [Совместимость с Java](http://kotlinlang.org/docs/reference/java-to-kotlin-interop.html#static-fields).

<a name="semantic-difference-between-object-expressions-and-declarations"></a>

<!-- ### Semantic difference between object expressions and declarations -->
### Семантическое различие между анонимным объектом и декларируемым объектом

<!-- There is one important semantic difference between object expressions and object declarations: -->
Существует только одно смысловое различие между этими двумя понятиями:

<!-- * Object expressions are executed (and initialized) _immediately_, where they are used.
* Object declarations are initialized _lazily_, when accessed for the first time.
* A companion object is initialized when the corresponding class is loaded (resolved) that matches the semantics of a Java
  static initializer. -->

* анонимный объект инициализируется *непосредственно* при использовании;
* декларированный объект инициализируется *лениво*, в момент первого к нему доступа;
* вспомогательный объект инициализируется в момент, когда класс, к которому он относится, загружен и семантически
совпадает со статическим инициализатором Java.
