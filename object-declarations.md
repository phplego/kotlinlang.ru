---
type: doc
layout: reference
category: "Syntax"
title: "Объекты"
url: https://kotlinlang.ru/docs/reference/object-declarations.html
---

<!-- # Object Expressions and Declarations -->
# Анонимные объекты и объявление объектов

<!-- Sometimes we need to create an object of a slight modification of some class, without explicitly declaring a new subclass for it. -->
<!-- Java handles this case with *anonymous inner classes*. -->
<!-- Kotlin slightly generalizes this concept with *object expressions* and *object declarations*. -->
Иногда нам необходимо получить экземпляр некоторого класса с незначительной модификацией, желательно без написания нового подкласса. <b>Java</b> справляется с этим с помощью _вложенных анонимных классов_.
<b>Kotlin</b> несколько улучшает данный подход.

<a name="object-expressions"></a>

<!-- ## Object expressions -->
## Анонимные объекты (ориг.:_Object expressions_)

<!-- To create an object of an anonymous class that inherits from some type (or types), we write: -->
Для того, чтобы создать объект анонимного класса, который наследуется от какого-то типа (типов), используется конструкция:

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
```

<!-- If a supertype has a constructor, appropriate constructor parameters must be passed to it. -->
<!-- Many supertypes may be specified as a comma-separated list after the colon: -->
Если у супертипа есть конструктор, то в него должны быть переданы соответсвующие параметры.
Множество супертипов может быть указано после двоеточия в виде списка, заполненного через запятую:


```kotlin
open class A(x: Int) {
    public open val y: Int = x
}

interface B {...}

val ab: A = object : A(1), B {
    override val y = 15
}
```

<!-- If, by any chance, we need "just an object", with no nontrivial supertypes, we can simply say: -->
Если всё-таки нам нужен *просто объект* без всяких там родительских классов, то можем указать:

```kotlin
val adHoc = object {
    var x: Int = 0
    var y: Int = 0
}
print(adHoc.x + adHoc.y)
```

<!-- Just like Java's anonymous inner classes, code in object expressions can access variables from the enclosing scope. -->
<!-- (Unlike Java, this is not restricted to final variables.) -->
Код внутри объявленного объекта может обращаться к переменным за скобками так же, как вложенные анонимные классы в <b>Java</b>

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

<a name="object-declarations"></a>

<!-- ## Object declarations -->
## Объявления объектов (ориг.:_Object declarations_)

<!-- [Singleton](http://en.wikipedia.org/wiki/Singleton_pattern) is a very useful pattern, and Kotlin (after Scala) makes it easy to declare singletons: -->
[Синглтон](https://ru.wikipedia.org/wiki/%D0%9E%D0%B4%D0%B8%D0%BD%D0%BE%D1%87%D0%BA%D0%B0_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)) - очень полезный паттерн программирования, и <b>Kotlin</b> (переняв у <b>Scala</b>) позволяет объявлять его довольно простым способом :

```kotlin
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ...
    }

    val allDataProviders: Collection<DataProvider>
        get() = // ...
}
```

<!-- This is called an *object declaration*, and it always has a name following the *object*{: .keyword } keyword. -->
<!-- Just like a variable declaration, an object declaration is not an expression, and cannot be used on the right hand side of an assignment statement. -->
Это называется *объявлением объекта* и всегда имеет приставку в виде ключевого слова **object**.
Аналогично объявлению переменной, объявление объекта не является выражением и не может быть использовано в правой части оператора присваивания.

<!-- To refer to the object, we use its name directly: -->
Для непосредственной ссылки на объект используется его имя:

```kotlin
DataProviderManager.registerDataProvider(...)
```

<!-- Such objects can have supertypes: -->
Подобные объекты могут иметь супертипы:

```kotlin
object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
}
```

<!-- **NOTE**: object declarations can't be local (i.e. be nested directly inside a function), but they can be nested into other object declarations or non-inner classes. -->

**ПРИМЕЧАНИЕ**: объявление объекта не может иметь локальный характер (т.е. быть вложенным непосредственно в функцию), но может быть вложено в объявление другого объекта или какого-либо невложенного класса.

<a name="companion-objects"></a>

<!-- ### Companion Objects -->
### Вспомогательные объекты

<!-- An object declaration inside a class can be marked with the *companion*{: .keyword } keyword: -->
Объявление объекта внутри класса может быть отмечено ключевым словом **companion**:

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```

<!-- Members of the companion object can be called by using simply the class name as the qualifier: -->
Для вызова членов такого `companion` объекта используется имя класса:

```kotlin
val instance = MyClass.create()
```

<!-- The name of the companion object can be omitted, in which case the name `Companion` will be used: -->
Не обязательно указывать имя вспомогательного объекта. В таком случае он будет назван `Companion`:

```kotlin
class MyClass {
    companion object {
    }
}

val x = MyClass.Companion
```

<!-- Note that, even though the members of companion objects look like static members in other languages, at runtime those -->
<!-- are still instance members of real objects, and can, for example, implement interfaces: -->
<!-- Несмотря на то, что члены таких вспомогательных объектов выглядят, как статические члены в других языках программирования, на самом деле они являются членами реальных объектов и могут реализовывать, к примеру, интерфейсы: -->
Такие члены вспомогательных объектов выглядят, как статические члены в других языках программирования. На самом же деле, они являются членами реальных объектов и могут реализовывать, к примеру, интерфейсы:

```kotlin
interface Factory<T> {
    fun create(): T
}


class MyClass {
    companion object : Factory<MyClass> {
        override fun create(): MyClass = MyClass()
    }
}
```

<!-- However, on the JVM you can have members of companion objects generated as real static methods and fields, if you use -->
<!-- the `@JvmStatic` annotation. See the [Java interoperability](java-to-kotlin-interop.html#static-fields) section -->
<!-- for more details. -->
Однако в <b>JVM</b> вы можете статически генерировать методы вспомогательных объектов и полей, используя аннотацию `@JvmStatic@`.
См. [Совместимость с Java](http://kotlinlang.org/docs/reference/java-to-kotlin-interop.html#static-fields).


<!-- ### Semantic difference between object expressions and declarations -->
### Семантическое различие между анонимным объектом и декларируемым объектом.

<!-- There is one important semantic difference between object expressions and object declarations: -->
Существует только одно смысловое различие между этими двумя понятиями:

<!-- * object expressions are executed (and initialized) **immediately**, where they are used -->
<!-- * object declarations are initialized **lazily**, when accessed for the first time -->
<!-- * a companion object is initialized when the corresponding class is loaded (resolved), matching the semantics of a Java static initializer -->

* анонимный объект инициализируется **сразу после того**, как был использован
* декларированный объект инициализируется **лениво**, в момент первого к нему доступа
* вспомогательный объект инициализируется в момент, когда класс, к которому он относится, загружен и семантически совпадает со статическим инициализатором <b>Java</b>

