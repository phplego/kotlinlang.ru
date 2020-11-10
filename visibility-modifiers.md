---
type: doc
layout: reference
category: "Classes and Objects"
title: "Модификаторы доступа"
url: https://kotlinlang.ru/docs/reference/visibility-modifiers.html
---

<!--# Visibility Modifiers-->
# Модификаторы доступа

<!--Classes, objects, interfaces, constructors, functions, properties and their setters can have _visibility modifiers_.
(Getters always have the same visibility as the property.) 
There are four visibility modifiers in Kotlin: `private`, `protected`, `internal` and `public`.
The default visibility, used if there is no explicit modifier, is `public`.-->
Классы, объекты, интерфейсы, конструкторы, функции, свойства и их сеттеры могут иметь _модификаторы доступа_ (у геттеров всегда такая же видимость, как у свойств, к которым они относятся). В <b>Kotlin</b> предусмотрено четыре модификатора доступа: `private`, `protected`, `internal` и `public`. Если явно не используется никакого модификатора доступа, то по умолчанию применяется `public`.

<!--Below please find explanations of these for different type of declaring scopes.-->
Ниже вы найдёте описание всех возможных способов задавать область видимости.
  
<!--## Packages-->
## Пакеты
  
<!--Functions, properties and classes, objects and interfaces can be declared on the "top-level", i.e. directly inside a package:-->
Функции, свойства, классы, объекты и интерфейсы могут быть объявлены на самом "высоком уровне" прямо внутри пакета:
  
```kotlin
// имя файла: example.kt
package foo

fun baz() {}
class Bar {}
```

<!--* If you do not specify any visibility modifier, `public` is used by default, which means that your declarations will be
visible everywhere;
* If you mark a declaration `private`, it will only be visible inside the file containing the declaration;
* If you mark it `internal`, it is visible everywhere in the same [module](#modules);
* `protected` is not available for top-level declarations.-->

* Если вы не укажете никакого модификатора доступа, будет использован `public`. Это значит, что весь код данного объявления будет виден из космоса;
* Если вы пометите объявление словом `private`, оно будет иметь видимость только внутри файла, где было объявлено;
* Если вы используете `internal`, видимость будет распространяться на весь [модуль](visibility-modifiers.html#modules);
* `protected` запрещено использовать в объявлениях "высокого уровня".

<!--Examples:-->
Примеры:

```kotlin
// имя файла: example.kt
package foo

private fun foo() {} // имеет видимость внутри example.kt

public var bar: Int = 5 // свойство видно со дна Марианской впадины
    private set         // сеттер видно только внутри example.kt
    
internal val baz = 6    // имеет видимость внутри модуля
```

<!--## Classes and Interfaces-->
## Классы и интерфейсы

<!--For members declared inside a class:-->
Для членов, объявленых в классе:

<!--* `private` means visible inside this class only (including all its members);
* `protected` --- same as `private` + visible in subclasses too;
* `internal` --- any client *inside this module* who sees the declaring class sees its `internal` members;
* `public` --- any client who sees the declaring class sees its `public` members.-->

* `private` означает видимость только внутри этого класса (включая его члены);
* `protected` — то же самое, что и `private` + видимость в субклассах;
* `internal` — любой клиент *внутри модуля*, который видит объявленный класс, видит и его `internal` члены;
* `public` — любой клиент, который видит объявленный класс, видит его `public` члены.

<!--*NOTE* for Java users: outer class does not see private members of its inner classes in Kotlin.-->
> *Примечание для Java программистов:* в <b>Kotlin</b> внешний класс не видит `private` члены своих вложенных классов.

<!--If you override a `protected` member and do not specify the visibility explicitly, the overriding member will also have `protected` visibility.-->
Если вы переопределите `protected` член и явно не укажете его видимость, переопределённый элемент также будет иметь модификатор доступа `protected`.
 
<!--Examples:-->
Примеры:

```kotlin
open class Outer {
    private val a = 1
    protected open val b = 2
    internal val c = 3
    val d = 4  // public по умолчанию
    
    protected class Nested {
        public val e: Int = 5
    }
}

class Subclass : Outer() {
    // a не видно
    // b, c и d видно
    // класс Nested и e видно

    override val b = 5   // 'b' - protected
}

class Unrelated(o: Outer) {
    // o.a, o.b не видно
    // o.c и o.d видно (тот же модуль)
    // Outer.Nested не видно, и Nested::e также не видно
}
```

<a name="constructors"></a>

<!--### Constructors-->
### Конструкторы

<!--To specify a visibility of the primary constructor of a class, use the following syntax (note that you need to add an
explicit *constructor*{: .keyword } keyword):-->
Для указания видимости основного конструктора класса используется следующий синтаксис (кстати, надо добавить ключевое слово <b class="keyword">constructor</b>):

```kotlin
class C private constructor(a: Int) { ... }
```

<!--Here the constructor is private. By default, all constructors are `public`, which effectively
amounts to them being visible everywhere where the class is visible (i.e. a constructor of an `internal` class is only 
visible within the same module).-->
В этом примере конструктор является `private`. По умолчанию все конструкторы имеют модификатор доступа `public`, то есть видны везде, где виден сам класс (а вот конструктор `internal` класса видно только в том же модуле).
     
<!--### Local declarations-->
### Локальные объявления
     
<!--Local variables, functions and classes can not have visibility modifiers.-->
Локальные переменные, функции и классы не могут иметь модификаторов доступа. <!--rcd27: неожиданно...-->

<a name="modules"></a>

<!--## Modules-->
## Модули

<!--The `internal` visibility modifier means that the member is visible with the same module. More specifically,
a module is a set of Kotlin files compiled together:-->
Модификатор доступа `internal` означает, что этот член видно в рамках его модуля. Модуль - это набор скомпилированных вместе <b>Kotlin</b> файлов:
  
  * модуль в IntelliJ IDEA;
  * Maven или Gradle проект;
  * набор скомпилированных вместе файлов с одним способом вызова `<kotlinc>` задачи в Ant.
