---
url: https://kotlinlang.ru/docs/reference/generics.html
type: doc
layout: reference
category: "Syntax"
title: "Обобщения (Generics)"
url: https://kotlinlang.ru/docs/reference/generics.html
---

<!-- # Generics -->
# Обобщения (Generics)

<!-- As in Java, classes in Kotlin may have type parameters: -->
Как и в <b>Java</b>, в <b>Kotlin</b> классы могут иметь параметризированные типы:

``` kotlin
class Box<T>(t: T) {
    var value = t
}
```

<!-- In general, to create an instance of such a class, we need to provide the type arguments: -->
Для того, чтобы создать объект такого класса, нам надо обеспечить соответствие аргумента этому типу:

``` kotlin
val box: Box<Int> = Box<Int>(1)
```

<!-- But if the parameters may be inferred, e.g. from the constructor arguments or by some other means, one is allowed to omit the type arguments: -->
Но в случае, если необходимо опустить указание параметра (в аргументах конструктора или для каких-либо других целей), можно опустить указание типа данного аргумента:

``` kotlin
val box = Box(1) // 1 имеет тип Int, поэтому компилятор отмечает для себя, что у переменной box - тоже Int
```

## Variance

<!-- One of the most tricky parts of Java's type system is wildcard types (see [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)). -->
Одним из самых сложных мест в системе типов <b>Java</b> является использование специальных символов для различных типов (см. [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)).
<!-- And Kotlin doesn't have any. Instead, it has two other things: declaration-site variance and type projections. -->
А в <b>Kotlin</b> этого нет. Вместо этого, у нас есть две другие вещи: _declaration-site variance_ и _type projections_.

<!-- First, let's think about why Java needs those mysterious wildcards. The problem is explained in [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 28: *Use bounded wildcards to increase API flexibility*. -->
Для начала давайте подумаем на тему, зачем <b>Java</b> нужны эти странные спец. символы. Пробема описана в книге [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 28: *Use bounded wildcards to increase API flexibility*.
<!-- First, generic types in Java are **invariant**, meaning that `List<String>` is **not** a subtype of `List<Object>`. -->
Обобщающие типы в <b>Java</b>, прежде всего, **неизменны**. Подразумевается, что `List<String>` *не является* подтипом `List<Object>`.
<!-- Why so? If List was not **invariant**, it would have been no -->
<!-- better than Java's arrays, since the following code would have compiled and caused an exception at runtime: -->
Почему так? Если бы List был изменяемым, единственно лучшим решением для следующей задачи был бы массив, потому что после компиляции данный код вызвал бы ошибку в рантайме:

``` java
// Java
List<String> strs = new ArrayList<String>();
List<Object> objs = strs; // !!! Причина вышеуказанной проблемы заключена здесь, Java запрещает так делать
objs.add(1); // Тут мы помещаем Integer в список String'ов
String s = strs.get(0); // !!! ClassCastException: не можем кастовать Integer к String
```
<!-- So, Java prohibits such things in order to guarantee run-time safety. But this has some implications. For example, consider the `addAll()` method from `Collection` -->
<!-- interface. What's the signature of this method? Intuitively, we'd put it this way: -->
Таким образом, <b>Java</b> запрешает подобные вещи, гаранитируя тем самым безопасность в период выполнения кода. Но у такого подхода есть свои последствия. Рассмотрим, например, метод `addAll` интерфейса `Collection`. Какова сигнатура данного метода? Интуитивно мы бы указали её таким образом:

``` java
// Java
interface Collection<E> ... {
  void addAll(Collection<E> items);
}
```

<!-- But then, we would not be able to do the following simple thing (which is perfectly safe): -->
Но тогда мы бы не могли выполнять следующую простую операция (которая является абсолютно безопасной):

``` java
// Java
void copyAll(Collection<Object> to, Collection<String> from) {
  to.addAll(from); // !!! Не скомпилируется с нативным объявлением метода addAll:
                   //       Collection<String> не является подтипом Collection<Object>
}
```

<!-- (In Java, we learned this lesson the hard way, see [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 25: *Prefer lists to arrays*) -->
В <b>Java</b> нам этот урок дорого стоил, см. [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 25: *Prefer lists to arrays*.

<!-- That's why the actual signature of `addAll()` is the following: -->
Вот почему сигнатура `addAll()` на самом деле такая:

``` java
// Java
interface Collection<E> ... {
  void addAll(Collection<? extends E> items);
}
```

<!-- The **wildcard type argument** `? extends T` indicates that this method accepts a collection of objects of *some subtype of* `T`, not `T` itself. -->
**Специальный символ для аргумента** `? extends T` указвает на то, что это метод принимает коллекцию объектов *некого типа* `T`, а не сам по себе `T`.
<!-- This means that we can safely **read** `T`'s from items (elements of this collection are instances of a subclass of T), but **cannot write** to -->
<!-- it since we do not know what objects comply to that unknown subtype of `T`. -->
<!-- In return for this limitation, we have the desired behaviour: `Collection<String>` *is* a subtype of `Collection<? extends Object>`. -->
Это значит, что мы можем безопасно **читать** `T` из содержимого (элементы коллекции являются экземплярами подкласса T), но **не можем их изменять**, потому что не знаем, какие объекты соответствуют этому неизвестному типу `T`.
Минуя это ограничение, мы достигаем желаемого результата: `Collection<String>`
**является** подтипом `Collection<? extends Object>`.
 <!-- In "clever words", the wildcard with an **extends**\-bound (**upper** bound) makes the type **covariant**. -->
 Выражаясь более "умными словами", спец.символ с **extends**-связкой (**верхнее** связывание) делает тип ковариантным (ориг.: _covariant_).

<!-- The key to understanding why this trick works is rather simple: if you can only **take** items from a collection, then using a collection of `String`s -->
<!-- and reading `Object`s from it is fine. Conversely, if you can only _put_ items into the collection, it's OK to take a collection of -->
<!-- `Object`s and put `String`s into it: in Java we have `List<? super String>` a **supertype** of `List<Object>`. -->
Ключом к пониманию, почему этот трюк работает, является довольно простая мысль: использование  `String`'ов и чтение из них `Object`ов - нормально только в случае, если  вы **берёте** элементы из коллекции. Наоборот, если вы только _вносите_ элементы в коллекцию, то нормально брать коллекцию `Object`'ов и помещать в неё `String`и: в <b>Java</b> есть `List<? super String>`, **супертип** `List<Object>`'a.

<!-- The latter is called **contravariance**, and you can only call methods that take String as an argument on `List<? super String>` -->
<!-- (e.g., you can call `add(String)` or `set(int, String)`), while -->
<!-- if you call something that returns `T` in `List<T>`, you don't get a `String`, but an `Object`. -->
Это назвается **контрвариантностью**. В `List<? super String>` вы можете вызвать только те методы, которые принимают String в качестве аргумента (например, `add(String)` или `set(int, String)`). В случае, если вы вызываете из `List<T>` что-то c возвращаемым значением `T`, вы получаете не `String`, а `Object`.

<!-- Joshua Bloch calls those objects you only **read** from **Producers**, and those you only **write** to **Consumers**. He recommends: "*For maximum flexibility, use wildcard types on input parameters that represent producers or consumers*", and proposes the following mnemonic: -->
Джоуа Блок (Joshua Block) выделяет такие объекты:
- **Производители** (ориг.:_producers_) из которых вы только **читаете**
- **Потребители** (ориг.: _consumers_),в которые вы только **записываете**

<!-- *PECS stands for Producer-Extends, Consumer-Super.* -->
PECS настаивает на _Producer-Extends_, _Consumer-Super_.

<!-- *NOTE*: if you use a producer-object, say, `List<? extends Foo>`, you are not allowed to call `add()` or `set()` on this object, but this does not mean -->
<!-- that this object is **immutable**: for example, nothing prevents you from calling `clear()` to remove all items from the list, since `clear()` -->
<!-- does not take any parameters at all. The only thing guaranteed by wildcards (or other types of variance) is **type safety**. Immutability is a completely different story. -->

>*Примечание*: если вы используете объект-производитель, предположим, `List<? extends Foo>`, вы не можете вызвать методы `add()` или `set()`, применительно к этому объекту. Но это не значит, что объект **неизменный** (immutable): ничто не мешает вам вызвать метод `clear()` для того, чтобы очистить список, так как `clear()` не имеет аргументов. Единственное, что гарантировано при использовании спец.символов - **безопасность типов**. Неизменяемость (ориг.: _immutability_) - совершенно другая история.


<!-- ### Declaration-site variance -->
### Вариантность на уровне объявления

<!-- Suppose we have a generic interface `Source<T>` that does not have any methods that take `T` as a parameter, only methods that return `T`: -->
Допустим, у нас есть дженерик интерфейс `Source<T>`, у которого нет методов, принимающий `T` в качестве аргумента. Только методы, возвращающие `T`:

``` java
// Java
interface Source<T> {
  T nextT();
}
```

<!-- Then, it would be perfectly safe to store a reference to an instance of `Source<String>` in a variable of type `Source<Object>` -- there are no consumer-methods to call. But Java does not know this, and still prohibits it: -->
Тогда было бы наиболее безопасно хранить ссылки на экземляр `Source<String>` с вариантным типом `Source<Object>` -- для вызова нет никаких методов-потребителей.

``` java
// Java
void demo(Source<String> strs) {
  Source<Object> objects = strs; // !!! Not allowed in Java
  // ...
}
```

To fix this, we have to declare objects of type `Source<? extends Object>`, which is sort of meaningless, because we can call all the same methods on such a variable as before, so there's no value added by the more complex type. But the compiler does not know that.

In Kotlin, there is a way to explain this sort of thing to the compiler. This is called **declaration-site variance**: we can annotate the **type parameter** `T` of Source to make sure that it is only **returned** (produced) from members of `Source<T>`, and never consumed.
To do this we provide the **out** modifier:

``` kotlin
abstract class Source<out T> {
    abstract fun nextT(): T
}

fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs // This is OK, since T is an out-parameter
    // ...
}
```

The general rule is: when a type parameter `T` of a class `C` is declared **out**, it may occur only in **out**\-position in the members of `C`, but in return `C<Base>` can safely be a supertype
of `C<Derived>`.

In "clever words" they say that the class `C` is **covariant** in the parameter `T`, or that `T` is a **covariant** type parameter.
You can think of `C` as being a **producer** of `T`'s, and NOT a **consumer** of `T`'s.

The **out** modifier is called a **variance annotation**, and  since it is provided at the type parameter declaration site, we talk about **declaration-site variance**.
This is in contrast with Java's **use-site variance** where wildcards in the type usages make the types covariant.

In addition to **out**, Kotlin provides a complementary variance annotation: **in**. It makes a type parameter **contravariant**: it can only be consumed and never
produced. A good example of a contravariant class is `Comparable`:

``` kotlin
abstract class Comparable<in T> {
    abstract fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    x.compareTo(1.0) // 1.0 has type Double, which is a subtype of Number
    // Thus, we can assign x to a variable of type Comparable<Double>
    val y: Comparable<Double> = x // OK!
}
```

We believe that the words **in** and **out** are self-explaining (as they were successfully used in C# for quite some time already),
thus the mnemonic mentioned above is not really needed, and one can rephrase it for a higher purpose:

**[The Existential](http://en.wikipedia.org/wiki/Existentialism) Transformation: Consumer in, Producer out\!** :-)

## Type projections

### Use-site variance: Type projections

It is very convenient to declare a type parameter T as *out* and have no trouble with subtyping on the use site. Yes, it is, when the class in question **can** actually be restricted to only return `T`'s, but what if it can't?
A good example of this is Array:

``` kotlin
class Array<T>(val size: Int) {
    fun get(index: Int): T { /* ... */ }
    fun set(index: Int, value: T) { /* ... */ }
}
```

This class cannot be either co\- or contravariant in `T`. And this imposes certain inflexibilities. Consider the following function:

``` kotlin
fun copy(from: Array<Any>, to: Array<Any>) {
    assert(from.size == to.size)
    for (i in from.indices)
        to[i] = from[i]
}
```

This function is supposed to copy items from one array to another. Let's try to apply it in practice:

``` kotlin
val ints: Array<Int> = arrayOf(1, 2, 3)
val any = Array<Any>(3)
copy(ints, any) // Error: expects (Array<Any>, Array<Any>)
```

Here we run into the same familiar problem: `Array<T>` is **invariant** in `T`, thus neither of `Array<Int>` and `Array<Any>`
is a subtype of the other. Why? Again, because copy **might** be doing bad things, i.e. it might attempt to **write**, say, a String to `from`,
and if we actually passed an array of `Int` there, a `ClassCastException` would have been thrown sometime later.

Then, the only thing we want to ensure is that `copy()` does not do any bad things. We want to prohibit it from **writing** to `from`, and we can:

``` kotlin
fun copy(from: Array<out Any>, to: Array<Any>) {
 // ...
}
```

What has happened here is called **type projection**: we said that `from` is not simply an array, but a restricted (**projected**) one: we can only call those methods that return the type parameter
`T`, in this case it means that we can only call `get()`. This is our approach to **use-site variance**, and corresponds to Java's `Array<? extends Object>`,
but in a slightly simpler way.

You can project a type with **in** as well:

``` kotlin
fun fill(dest: Array<in String>, value: String) {
    // ...
}
```

`Array<in String>` corresponds to Java's `Array<? super String>`, i.e. you can pass an array of `CharSequence` or an array of `Object` to the `fill()` function.

### Star-projections

Sometimes you want to say that you know nothing about the type argument, but still want to use it in a safe way.
The safe way here is to define such a projection of the generic type, that every concrete instantiation of that generic type would be a subtype of that projection.

Kotlin provides so called **star-projection** syntax for this:

 - For `Foo<out T>`, where `T` is a covariant type parameter with the upper bound `TUpper`, `Foo<*>` is equivalent to `Foo<out TUpper>`. It means that when the `T` is unknown you can safely *read* values of `TUpper` from `Foo<*>`.
 - For `Foo<in T>`, where `T` is a contravariant type parameter, `Foo<*>` is equivalent to `Foo<in Nothing>`. It means there is nothing you can *write* to `Foo<*>` in a safe way when `T` is unknown.
 - For `Foo<T>`, where `T` is an invariant type parameter with the upper bound `TUpper`, `Foo<*>` is equivalent to `Foo<out TUpper>` for reading values and to `Foo<in Nothing>` for writing values.

If a generic type has several type parameters each of them can be projected independently.
For example, if the type is declared as `interface Function<in T, out U>` we can imagine the following star-projections:

 - `Function<*, String>` means `Function<in Nothing, String>`;
 - `Function<Int, *>` means `Function<Int, out Any?>`;
 - `Function<*, *>` means `Function<in Nothing, out Any?>`.

*Note*: star-projections are very much like Java's raw types, but safe.

## Generic functions

Not only classes can have type parameters. Functions can, too. Type parameters are placed before the name of the function:

``` kotlin
fun <T> singletonList(item: T): List<T> {
    // ...
}

fun <T> T.basicToString() : String {  // extension function
    // ...
}
```

To call a generic function, specify the type arguments at the call site **after** the name of the function:

``` kotlin
val l = singletonList<Int>(1)
```

## Generic constraints

The set of all possible types that can be substituted for a given type parameter may be restricted by **generic constraints**.

### Upper bounds

The most common type of constraint is an **upper bound** that corresponds to Java's *extends* keyword:

``` kotlin
fun <T : Comparable<T>> sort(list: List<T>) {
    // ...
}
```

The type specified after a colon is the **upper bound**: only a subtype of `Comparable<T>` may be substituted for `T`. For example

``` kotlin
sort(listOf(1, 2, 3)) // OK. Int is a subtype of Comparable<Int>
sort(listOf(HashMap<Int, String>())) // Error: HashMap<Int, String> is not a subtype of Comparable<HashMap<Int, String>>
```

The default upper bound (if none specified) is `Any?`. Only one upper bound can be specified inside the angle brackets.
If the same type parameter needs more than one upper bound, we need a separate **where**\-clause:

``` kotlin
fun <T> cloneWhenGreater(list: List<T>, threshold: T): List<T>
    where T : Comparable,
          T : Cloneable {
  return list.filter { it > threshold }.map { it.clone() }
}
```
