---
type: doc
layout: reference
category: "Java Interop"
title: "Вызов Kotlin из Java"
---

# Вызов кода Kotlin из Java

Код Kotlin может быть вызван из JAVA просто.

<!--Kotlin code can be called from Java easily.-->

## Свойства (Properties)

Свойство Kotlin компилируется в следующие Java элементы:

 * Метод getter, имя которого вычисляется путем добавления префикса `get`;
 * Метод setter, имя которого вычисляется путем добавления префикса `set` (только для свойств `var`);
 * Приватное поле с тем же именем, что и имя свойства (только для свойств с backing fields).
 
Например, `var firstName: String` компилируется в следующие объявления Java:

``` java
private String firstName;

public String getFirstName () {
     return firstName;
}

public void setFirstName (String firstName) {
     this.firstName = firstName;
}
```

Если имя свойства начинается с `is`, используется другое правило сопоставления имен: имя метода getter будет совпадать с
именем свойства, а имя метода setter будет получено путем замены `is` на `set`. Например, для свойства `isOpen`, getter будет
называться `isOpen()` и setter будет называться `setOpen()`. 
Это правило применяется к свойствам любого типа, а не только к `Boolean`.

<!--
## Properties 
A Kotlin property is compiled to the following Java elements:
 * A getter method, with the name calculated by prepending the `get` prefix;
 * A setter method, with the name calculated by prepending the `set` prefix (only for `var` properties);
 * A private field, with the same name as the property name (only for properties with backing fields).
For example, `var firstName: String` gets compiled to the following Java declarations:
``` java
private String firstName;
public String getFirstName() {
    return firstName;
}
public void setFirstName(String firstName) {
    this.firstName = firstName;
}
```
If the name of the property starts with `is`, a different name mapping rule is used: the name of the getter will be
the same as the property name, and the name of the setter will be obtained by replacing `is` with `set`.
For example, for a property `isOpen`, the getter will be called `isOpen()` and the setter will be called `setOpen()`.
This rule applies for properties of any type, not just `Boolean`.
-->

## Функции уровня пакета (Package-Level Functions)

Все функции и свойства, объявленные в файле `example.kt` внутри пакета `org.foo.bar`, включая функции расширения,
скомпилированы в статические методы класса Java с именем `org.foo.bar.ExampleKt`.

```kotlin
// example.kt
package demo

class Foo

fun bar() { ... }

```


``` java
// Java
new demo.Foo();
demo.ExampleKt.bar();
```


Имя генерируемого JAVA класса может быть выбранно при помощи аннотации `@JvmName`:


```kotlin
@file:JvmName("DemoUtils")

package demo

class Foo

fun bar() { ... }

```



``` java
// Java
new demo.Foo();
demo.DemoUtils.bar();
```


Наличие нескольких файлов, имеющих одинаковое имя Java-класса (тот же пакет и одинаковое имя или аннотацию `@JvmName`),
является ошибкой. Однако компилятор имеет возможность генерировать один Java класс фасада, который имеет указанное имя и
содержит все объявления из всех файлов, которые имеют такое имя. 
Чтобы включить генерацию такого фасада, используйте аннотацию `@JvmMultifileClass` во всех файлах.


```kotlin
// oldutils.kt
@file:JvmName("Utils")
@file:JvmMultifileClass

package demo

fun foo() { ... }
```



```kotlin
// newutils.kt
@file:JvmName("Utils")
@file:JvmMultifileClass

package demo

fun bar() { ... }
```



``` java
// Java
demo.Utils.foo();
demo.Utils.bar();
```

<!--
## Package-Level Functions
All the functions and properties declared in a file `example.kt` inside a package `org.foo.bar`, including extension functions,
are compiled into static methods of a Java class named `org.foo.bar.ExampleKt`.
```kotlin
// example.kt
package demo
class Foo
fun bar() { ... }
```
``` java
// Java
new demo.Foo();
demo.ExampleKt.bar();
```
The name of the generated Java class can be changed using the `@JvmName` annotation:
```kotlin
@file:JvmName("DemoUtils")
package demo
class Foo
fun bar() { ... }
```
``` java
// Java
new demo.Foo();
demo.DemoUtils.bar();
```
Having multiple files which have the same generated Java class name (the same package and the same name or the same
@JvmName annotation) is normally an error. However, the compiler has the ability to generate a single Java facade
class which has the specified name and contains all the declarations from all the files which have that name.
To enable the generation of such a facade, use the @JvmMultifileClass annotation in all of the files.
```kotlin
// oldutils.kt
@file:JvmName("Utils")
@file:JvmMultifileClass
package demo
fun foo() { ... }
```
```kotlin
// newutils.kt
@file:JvmName("Utils")
@file:JvmMultifileClass
package demo
fun bar() { ... }
```
``` java
// Java
demo.Utils.foo();
demo.Utils.bar();
```
-->

## Поля экземпляра (Instance Fields)

Если вам нужно представить свойство Котлина в качестве поля в Java, вам нужно добавить к нему аннотацию `@JvmField`.
Поле будет иметь такую же видимость, что и базовое свойство.Вы можете добавить свойству аннотацию `@JvmField`,
если оно имеет backing field , не является приватным, не имеет `open`, `override` или `const` модификаторов и не является делегированным свойством.


```kotlin
class C(id: String) {
    @JvmField val ID = id
}
```



``` java
// Java
class JavaClient {
    public String getID(C c) {
        return c.ID;
    }
}
```

Свойства с [поздней инициализацией](properties.html#late-initialized-properties-and-variables) также отображаются как поля.
Видимость поля будет такой же, как видимость сеттера свойства с поздней инициализацией.

<!--
## Instance Fields
If you need to expose a Kotlin property as a field in Java, you need to annotate it with the `@JvmField` annotation.
The field will have the same visibility as the underlying property. You can annotate a property with `@JvmField`
if it has a backing field, is not private, does not have `open`, `override` or `const` modifiers, and is not a delegated property.
```kotlin
class C(id: String) {
    @JvmField val ID = id
}
```
``` java
// Java
class JavaClient {
    public String getID(C c) {
        return c.ID;
    }
}
```
[Late-Initialized](properties.html#late-initialized-properties-and-variables) properties are also exposed as fields. 
The visibility of the field will be the same as the visibility of `lateinit` property setter.
-->

## Статические поля (Static Fields)

Свойства Kotlin, объявленные в именованном объекте или объекте-помощнике, будут иметь статические backing fields в этом именованном объекте или в классе, содержащем объект-помощник.

Обычно эти поля являются приватными, но они могут быть представлены одним из следующих способов:

 - `@JvmField` аннотацией;
 - `lateinit` модификатором;
 - `const` модификатором.
 
 Аннотирование такого свойства с помощью `@JvmField` делает его статическим полем с той же видимостью, что и само свойство.


```kotlin
class Key(val value: Int) {
    companion object {
        @JvmField
        val COMPARATOR: Comparator<Key> = compareBy<Key> { it.value }
    }
}
```



``` java
// Java
Key.COMPARATOR.compare(key1, key2);
// public static final field in Key class
```


Свойство с [поздней инициализацией](properties.html#late-initialized-properties-and-variables) в объекте или объекте-помощнике имеет статическое backing field с той же видимостью, что и сеттер свойства.


```kotlin
object Singleton {
    lateinit var provider: Provider
}
```



``` java
// Java
Singleton.provider = new Provider();
// public static non-final field in Singleton class
```


Свойства, аннотированные `const` (как в классах, так и на верхнем уровне), превращаются в статические поля в Java:



```kotlin
// file example.kt

object Obj {
    const val CONST = 1
}

class C {
    companion object {
        const val VERSION = 9
    }
}

const val MAX = 239
```


В Java:


``` java
int c = Obj.CONST;
int d = ExampleKt.MAX;
int v = C.VERSION;
```
<!--
## Static Fields
Kotlin properties declared in a named object or a companion object will have static backing fields
either in that named object or in the class containing the companion object.
Usually these fields are private but they can be exposed in one of the following ways:
 - `@JvmField` annotation;
 - `lateinit` modifier;
 - `const` modifier.
Annotating such a property with `@JvmField` makes it a static field with the same visibility as the property itself.

```kotlin
class Key(val value: Int) {
    companion object {
        @JvmField
        val COMPARATOR: Comparator<Key> = compareBy<Key> { it.value }
    }
}
```

``` java
// Java
Key.COMPARATOR.compare(key1, key2);
// public static final field in Key class
```

A [late-initialized](properties.html#late-initialized-properties-and-variables) property in an object or a companion object
has a static backing field with the same visibility as the property setter.

```kotlin
object Singleton {
    lateinit var provider: Provider
}
```

``` java
// Java
Singleton.provider = new Provider();
// public static non-final field in Singleton class
```
Properties annotated with `const` (in classes as well as at the top level) are turned into static fields in Java:

```kotlin
// file example.kt
object Obj {
    const val CONST = 1
}
class C {
    companion object {
        const val VERSION = 9
    }
}
const val MAX = 239
```

In Java:

``` java
int c = Obj.CONST;
int d = ExampleKt.MAX;
int v = C.VERSION;
```
-->

## Статические методы (Static Methods)

Как упоминалось выше, Kotlin представляет функции уровня пакета как статические методы.
Kotlin также может генерировать статические методы для функций, определенных в именованных объектах или объектах-помощниках, если вы добавите аннотацию `@JvmStatic` к функции. 
Если вы используете эту аннотацию, компилятор создаст как статический метод во включающем классе объекта, так и метод экземпляра в самом объекте. 
Например:


```kotlin
class C {
    companion object {
        @JvmStatic fun foo() {}
        fun bar() {}
    }
}
```


Теперь `foo()` является статическим методом в Java, в то время как `bar()` нет:


``` java
C.foo(); // works fine
C.bar(); // error: not a static method
C.Companion.foo(); // instance method remains
C.Companion.bar(); // the only way it works
```


То же самое для именованных объектов:


```kotlin
object Obj {
    @JvmStatic fun foo() {}
    fun bar() {}
}
```


В Java:


``` java
Obj.foo(); // works fine
Obj.bar(); // error
Obj.INSTANCE.bar(); // works, a call through the singleton instance
Obj.INSTANCE.foo(); // works too
```


Аннотацию `@JvmStatic` можно также применить к свойству объекта или объекта-помощника, сделав его методы getter и setter статическими элементами в этом объекте или классе, содержащем объект-помощник.

<!--
## Static Methods
As mentioned above, Kotlin represents package-level functions as static methods.
Kotlin can also generate static methods for functions defined in named objects or companion objects if you annotate those functions as `@JvmStatic`.
If you use this annotation, the compiler will generate both a static method in the enclosing class of the object and an instance method in the object itself.
For example:
```kotlin
class C {
    companion object {
        @JvmStatic fun foo() {}
        fun bar() {}
    }
}
```
Now, `foo()` is static in Java, while `bar()` is not:
``` java
C.foo(); // works fine
C.bar(); // error: not a static method
C.Companion.foo(); // instance method remains
C.Companion.bar(); // the only way it works
```
Same for named objects:
```kotlin
object Obj {
    @JvmStatic fun foo() {}
    fun bar() {}
}
```
In Java:
``` java
Obj.foo(); // works fine
Obj.bar(); // error
Obj.INSTANCE.bar(); // works, a call through the singleton instance
Obj.INSTANCE.foo(); // works too
```
`@JvmStatic` annotation can also be applied on a property of an object or a companion object
making its getter and setter methods be static members in that object or the class containing the companion object.
-->

## Видимость (Visibility)

Видимость в Kotlin представляется в Java следующим образом:

* `private` элементы компилируются в `private` элементы;
* `private` объявления верхнего уровня компилируются в локальные объявления пакетов;
* `protected` остаются `protected` (обратите внимание, что java разрешает доступ  к защищенным членам из других классов в том же пакете, а Kotlin-нет, поэтому классы Java будут иметь более широкий доступ к коду);
* `internal`  объявления становятся `public` в JAVA. Члены `internal` классов проходят через искажение имен,  чтобы усложнить случайное использование их из Java и позволить перегрузку для членов с одинаковыми сигнатурами, которые не видят друг друга в соответствии с правилами Kotlin;
* `public` остаются `public`.

<!--
## Visibility
The Kotlin visibilities are mapped to Java in the following way:
* `private` members are compiled to `private` members;
* `private` top-level declarations are compiled to package-local declarations;
* `protected` remains `protected` (note that Java allows accessing protected members from other classes in the same package
and Kotlin doesn't, so Java classes will have broader access to the code);
* `internal` declarations become `public` in Java. Members of `internal` classes go through name mangling, to make
it harder to accidentally use them from Java and to allow overloading for members with the same signature that don't see
each other according to Kotlin rules;
* `public` remains `public`.
-->

## KClass

Иногда вам нужно вызвать метод Kotlin с параметром типа `KClass`. 
Автоматического преобразования из `Class` в `KClass` нет, поэтому вам нужно сделать это вручную, вызывая эквивалент свойства расширения `Class<T>.kotlin`:


```kotlin
kotlin.jvm.JvmClassMappingKt.getKotlinClass(MainView.class)
```

<!--
## KClass
Sometimes you need to call a Kotlin method with a parameter of type `KClass`.
There is no automatic conversion from `Class` to `KClass`, so you have to do it manually by invoking the equivalent of
the `Class<T>.kotlin` extension property:
```kotlin
kotlin.jvm.JvmClassMappingKt.getKotlinClass(MainView.class)
```
-->


## Обработка столкновений сигнатур с @JvmName 

Иногда у нас есть именованная функция в Kotlin, для которой нам нужно другое JVM-имя байтового кода. 
Самый яркий пример происходит вследствии *стирания типа*:


```kotlin
fun List<String>.filterValid(): List<String>
fun List<Int>.filterValid(): List<Int>
```


Эти две функции не могут быть определены вместе, потому что их подписи JVM одинаковы: `filterValid(Ljava/util/List;)Ljava/util/List;`. 
Если мы действительно хотим, чтобы они имели одно и то же имя в Kotlin, мы можем добавить одному (или обоим) аннотацию `@JvmName` и указать другое имя в качестве аргумента:


```kotlin
fun List<String>.filterValid(): List<String>

@JvmName("filterValidInt")
fun List<Int>.filterValid(): List<Int>
```


Из Kotlin они будут доступны с одинаковым именем `filterValid`, но из Java это будет `filterValid` и `filterValidInt`.

Этот же трюк применяется, когда нам нужно иметь свойство `x` вместе с функцией `getX()`:


```kotlin
val x: Int
    @JvmName("getX_prop")
    get() = 15

fun getX() = 10
```


Чтобы изменить имена созданных методов доступа для свойств без явно введенных геттеров и сеттеров, вы можете использовать `@get:JvmName` и `@set:JvmName`:


```kotlin
@get:JvmName("x")
@set:JvmName("changeX")
var x: Int = 23
```

<!--
## Handling signature clashes with @JvmName
Sometimes we have a named function in Kotlin, for which we need a different JVM name the byte code.
The most prominent example happens due to *type erasure*:
```kotlin
fun List<String>.filterValid(): List<String>
fun List<Int>.filterValid(): List<Int>
```
These two functions can not be defined side-by-side, because their JVM signatures are the same: `filterValid(Ljava/util/List;)Ljava/util/List;`.
If we really want them to have the same name in Kotlin, we can annotate one (or both) of them with `@JvmName` and specify a different name as an argument:
```kotlin
fun List<String>.filterValid(): List<String>
@JvmName("filterValidInt")
fun List<Int>.filterValid(): List<Int>
```
From Kotlin they will be accessible by the same name `filterValid`, but from Java it will be `filterValid` and `filterValidInt`.
The same trick applies when we need to have a property `x` alongside with a function `getX()`:
```kotlin
val x: Int
    @JvmName("getX_prop")
    get() = 15
fun getX() = 10
```
To change the names of generated accessor methods for properties without explicitly implemented getters and setters, you can use `@get:JvmName` and `@set:JvmName`:
```kotlin
@get:JvmName("x")
@set:JvmName("changeX")
var x: Int = 23
```
-->


## Генерация перегрузок 

Обычно, если вы пишете функцию Kotlin со значениями параметров по умолчанию, она будет видна в Java только как полная
сигнатура со всеми параметрами. Если вы хотите предоставить многократные перегрузки вызовам Java, можно использовать аннотацию `@JvmOverloads`.

Аннотации также работают для конструкторов, статических методов и т.д. Их нельзя использовать для абстрактных методов, включая методы, определенные в интерфейсах.


```kotlin
class Foo @JvmOverloads constructor(x: Int, y: Double = 0.0) {
    @JvmOverloads fun f(a: String, b: Int = 0, c: String = "abc") { ... }
}
```


Для каждого параметра со значением по умолчанию это создаст одну дополнительную перегрузку, которая имеет этот параметр и все параметры справа от него в удаленном списке параметров. В этом примере будет создано следующее:


``` java
// Constructors:
Foo(int x, double y)
Foo(int x)

// Methods
void f(String a, int b, String c) { }
void f(String a, int b) { }
void f(String a) { }
```


Обратите внимание, как описаны [вторичные конструкторы](classes.html#secondary-constructors), если класс имеет значения по
умолчанию для всех параметров конструктора, то для него будет создан открытый конструктор без аргументов. Это работает, 
даже если не указана аннотация `@JvmOverloads`.

<!--
## Overloads Generation
Normally, if you write a Kotlin function with default parameter values, it will be visible in Java only as a full
signature, with all parameters present. If you wish to expose multiple overloads to Java callers, you can use the
`@JvmOverloads` annotation.
The annotation also works for constructors, static methods etc. It can't be used on abstract methods, including methods
defined in interfaces.
```kotlin
class Foo @JvmOverloads constructor(x: Int, y: Double = 0.0) {
    @JvmOverloads fun f(a: String, b: Int = 0, c: String = "abc") { ... }
}
```
For every parameter with a default value, this will generate one additional overload, which has this parameter and
all parameters to the right of it in the parameter list removed. In this example, the following will be
generated:
``` java
// Constructors:
Foo(int x, double y)
Foo(int x)
// Methods
void f(String a, int b, String c) { }
void f(String a, int b) { }
void f(String a) { }
```
Note that, as described in [Secondary Constructors](classes.html#secondary-constructors), if a class has default
values for all constructor parameters, a public no-argument constructor will be generated for it. This works even
if the `@JvmOverloads` annotation is not specified.
-->

## Проверенные исключения (Checked Exceptions)

Как мы уже упоминали выше, Котлин не имеет проверенных исключений.
Таким образом, как правило, сигнатуры Java функций Kotlin не объявляют исключения. 
Поэтому если мы имеем такую функцию в Котлине:


```kotlin
// example.kt
package demo

fun foo() {
    throw IOException()
}
```


И мы хотим вызвать её из Java и поймать исключение:


``` java
// Java
try {
  demo.Example.foo();
}
catch (IOException e) { // error: foo() does not declare IOException in the throws list
  // ...
}
```


мы получаем сообщение об ошибке из компилятора Java, потому что `foo()` не объявляет `IOException`. 
Чтобы обойти эту проблему, используйте аннотацию `@Throws` в Kotlin:


```kotlin
@Throws(IOException::class)
fun foo() {
    throw IOException()
}
```

<!--
## Checked Exceptions
As we mentioned above, Kotlin does not have checked exceptions.
So, normally, the Java signatures of Kotlin functions do not declare exceptions thrown.
Thus if we have a function in Kotlin like this:
```kotlin
// example.kt
package demo
fun foo() {
    throw IOException()
}
```
And we want to call it from Java and catch the exception:
``` java
// Java
try {
  demo.Example.foo();
}
catch (IOException e) { // error: foo() does not declare IOException in the throws list
  // ...
}
```
we get an error message from the Java compiler, because `foo()` does not declare `IOException`.
To work around this problem, use the `@Throws` annotation in Kotlin:
```kotlin
@Throws(IOException::class)
fun foo() {
    throw IOException()
}
```
-->


## Null-безопасность (Null-safety)

При вызове Kotlin функций из Java никто не мешает нам передавать *null*{: .keyword} в качестве ненулевого параметра. 
Вот почему Kotlin генерирует проверки времени выполнения для всех публичных функций, которые ожидают непустые значения. 
Таким образом, мы немедленно получаем исключение `NullPointerException` в Java-коде.

<!--
## Null-safety
When calling Kotlin functions from Java, nobody prevents us from passing *null*{: .keyword } as a non-null parameter.
That's why Kotlin generates runtime checks for all public functions that expect non-nulls.
This way we get a `NullPointerException` in the Java code immediately.
-->

## Variant generics

Когда классы Kotlin используют [вариативность на уровне объявления](generics.html#declaration-site-variance), есть два варианта того, как их использование видно из кода Java. Допустим, у нас есть следующий класс и две функции, которые его используют:


```kotlin
class Box<out T>(val value: T)

interface Base
class Derived : Base

fun boxDerived(value: Derived): Box<Derived> = Box(value)
fun unboxBase(box: Box<Base>): Base = box.value
```


Наивный способ перевести эти функции в Java будет


``` java
Box<Derived> boxDerived(Derived value) { ... }
Base unboxBase(Box<Base> box) { ... }
```


Проблема в том, что в Kotlin мы можем сказать `unboxBase(boxDerived ("s"))`, но в Java это было бы невозможно, потому что в
Java класс `Box` является *инвариантным* по своему параметру `T` и, следовательно, `Box <Derived>` не является подтипом 
`Box <Base>`. 
Чтобы заставить его работать на Java, нам нужно определить `unboxBase` следующим образом:
    
    
``` java
Base unboxBase(Box<? extends Base> box) { ... }  
```


Здесь мы используем *типы подстановок* Java (`? extends Base`), чтобы эмулировать вариативность на уровне объявления 
с помощью вариативности на уровне употребления, потому что она имеется в Java.

Чтобы заставить API Kotlin работать в Java, мы генерируем `Box<Super>` как `Box<? extends Super>` для ковариантно определенных
`Box`(или `Foo <? super Bar>` для контравариантно определенных `Foo`), когда он появляется *как параметр*. Когда это
возвращаемое значение, мы не создаем подстановочные знаки, потому что в противном случае клиенты Java будут иметь дело с ними
(а это противоречит общему стилю кодирования Java). Поэтому функции из нашего примера фактически переводятся следующим образом:


``` java
// return type - no wildcards
Box<Derived> boxDerived(Derived value) { ... }
 
// parameter - wildcards 
Base unboxBase(Box<? extends Base> box) { ... }
```


ПРИМЕЧАНИЕ. Когда тип аргумента является окончательным, обычно нет смысла создавать подстановочный знак, поэтому `Box<String>` всегда `Box<String>`, независимо от того, какую позицию он занимает.

Если нам нужны подстановочные знаки, где они не генерируются по умолчанию, мы можем использовать аннотацию `@JvmWildcard`:


```kotlin
fun boxDerived(value: Derived): Box<@JvmWildcard Derived> = Box(value)
// is translated to 
// Box<? extends Derived> boxDerived(Derived value) { ... }
```


С другой стороны, если нам не нужны подстановочные знаки, где они созданы, мы можем использовать `@JvmSuppressWildcards`:


```kotlin
fun unboxBase(box: Box<@JvmSuppressWildcards Base>): Base = box.value
// is translated to 
// Base unboxBase(Box<Base> box) { ... }
```


ПРИМЕЧАНИЕ. `@JvmSuppressWildcards` можно использовать не только для индивидуальных аргументов типа, но и для всех объявлений, таких как функции или классы, что приводит к подавлению всех подстановочных знаков внутри них.

<!--
## Variant generics
When Kotlin classes make use of [declaration-site variance](generics.html#declaration-site-variance), there are two 
options of how their usages are seen from the Java code. Let's say we have the following class and two functions that use it:
```kotlin
class Box<out T>(val value: T)
interface Base
class Derived : Base
fun boxDerived(value: Derived): Box<Derived> = Box(value)
fun unboxBase(box: Box<Base>): Base = box.value
```
A naive way of translating these functions into Java would be this:
``` java
Box<Derived> boxDerived(Derived value) { ... }
Base unboxBase(Box<Base> box) { ... }
```
The problem is that in Kotlin we can say `unboxBase(boxDerived("s"))`, but in Java that would be impossible, because in Java 
  the class `Box` is *invariant* in its parameter `T`, and thus `Box<Derived>` is not a subtype of `Box<Base>`. 
  To make it work in Java we'd have to define `unboxBase` as follows:
``` java
Base unboxBase(Box<? extends Base> box) { ... }  
```
Here we make use of Java's *wildcards types* (`? extends Base`) to emulate declaration-site variance through use-site 
variance, because it is all Java has.
To make Kotlin APIs work in Java we generate `Box<Super>` as `Box<? extends Super>` for covariantly defined `Box` 
(or `Foo<? super Bar>` for contravariantly defined `Foo`) when it appears *as a parameter*. When it's a return value,
we don't generate wildcards, because otherwise Java clients will have to deal with them (and it's against the common 
Java coding style). Therefore, the functions from our example are actually translated as follows:
``` java
// return type - no wildcards
Box<Derived> boxDerived(Derived value) { ... }
// parameter - wildcards 
Base unboxBase(Box<? extends Base> box) { ... }
```
NOTE: when the argument type is final, there's usually no point in generating the wildcard, so `Box<String>` is always
  `Box<String>`, no matter what position it takes.
If we need wildcards where they are not generated by default, we can use the `@JvmWildcard` annotation:
```kotlin
fun boxDerived(value: Derived): Box<@JvmWildcard Derived> = Box(value)
// is translated to 
// Box<? extends Derived> boxDerived(Derived value) { ... }
```
On the other hand, if we don't need wildcards where they are generated, we can use `@JvmSuppressWildcards`:
```kotlin
fun unboxBase(box: Box<@JvmSuppressWildcards Base>): Base = box.value
// is translated to 
// Base unboxBase(Box<Base> box) { ... }
```
NOTE: `@JvmSuppressWildcards` can be used not only on individual type arguments, but on entire declarations, such as 
functions or classes, causing all wildcards inside them to be suppressed.
-->

## Перевод типа Nothing (Translation of type Nothing)

Тип [`Nothing`](exceptions.html#the-nothing-type) является особым, потому что он не имеет естественного аналога на Java.
Действительно, каждый ссылочный тип Java, включая `java.lang.Void`, принимает значение `null` как значение, а Nothing не
принимает даже этого. Таким образом, этот тип не может быть точно представлен в Java-мире. Вот почему Kotlin генерирует
необработанный тип, где аргумент типа `Nothing` используется:


```kotlin
fun emptyList(): List<Nothing> = listOf()
// is translated to
// List emptyList() { ... }
```

<!--
### Translation of type Nothing
The type [`Nothing`](exceptions.html#the-nothing-type) is special, because it has no natural counterpart in Java. Indeed, every Java reference type, including
`java.lang.Void`, accepts `null` as a value, and `Nothing` doesn't accept even that. So, this type cannot be accurately
represented in the Java world. This is why Kotlin generates a raw type where an argument of type `Nothing` is used:
```kotlin
fun emptyList(): List<Nothing> = listOf()
// is translated to
// List emptyList() { ... }
```
-->
