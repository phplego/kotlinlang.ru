---
type: doc
layout: reference
category: "Syntax"
title: "Обобщения (Generics)"
url: https://kotlinlang.ru/docs/reference/generics.html
---

<!-- # Generics -->
# Обобщения (Generics)

<!-- As in Java, classes in Kotlin may have type parameters: -->
Как и в <b>Java</b>, в <b>Kotlin</b> классы тоже могут иметь generic типы:

``` kotlin
class Box<T>(t: T) {
    var value = t
}
```

<!-- In general, to create an instance of such a class, we need to provide the type arguments: -->
Для того, чтобы создать объект такого класса, необходимо предоставить тип в качестве аргумента:

``` kotlin
val box: Box<Int> = Box<Int>(1)
```

<!-- But if the parameters may be inferred, e.g. from the constructor arguments or by some other means, one is allowed to omit the type arguments: -->
Но если параметры могут быть выведены из контекста (в аргументах конструктора или в некоторых других случаях), можно опустить указание типа:

``` kotlin
val box = Box(1) // 1 имеет тип Int, поэтому компилятор отмечает для себя, что у переменной box тип — Box<Int>
```

<a name="variance"></a>

<!--## Variance-->
## Вариативность

<!-- One of the most tricky parts of Java's type system is wildcard types (see [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)). -->
Одним из самых сложных мест в системе типов <b>Java</b> являются маски (ориг. wildcards) (см. [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)).
<!-- And Kotlin doesn't have any. Instead, it has two other things: declaration-site variance and type projections. -->
А в <b>Kotlin</b> этого нет. Вместо этого, у нас есть две другие вещи: _вариативность на уровне объявления_ и _проекции типов_.

<!-- First, let's think about why Java needs those mysterious wildcards. The problem is explained in [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 28: *Use bounded wildcards to increase API flexibility*. -->
Для начала давайте подумаем на тему, зачем <b>Java</b> нужны эти странные маски. Проблема описана в книге [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 28: *Use bounded wildcards to increase API flexibility*.
<!-- First, generic types in Java are **invariant**, meaning that `List<String>` is **not** a subtype of `List<Object>`. -->
Прежде всего, обобщающие типы в <b>Java</b> являются **инвариантными** (ориг. invariant). Это означает, что `List<String>` **не является** подтипом `List<Object>`.
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
Таким образом, <b>Java</b> запрещает подобные вещи, гаранитируя тем самым безопасность в период выполнения кода. Но у такого подхода есть свои последствия. Рассмотрим, например, метод `addAll` интерфейса `Collection`. Какова сигнатура данного метода? Интуитивно мы бы указали её таким образом:

``` java
// Java
interface Collection<E> ... {
  void addAll(Collection<E> items);
}
```

<!-- But then, we would not be able to do the following simple thing (which is perfectly safe): -->
Но тогда мы бы не могли выполнять следующую простую операцию (которая является абсолютно безопасной):

``` java
// Java
void copyAll(Collection<Object> to, Collection<String> from) {
  to.addAll(from); // !!! Не скомпилируется с нативным объявлением метода addAll:
                   //       Collection<String> не является подтипом Collection<Object>
}
```

<!-- (In Java, we learned this lesson the hard way, see [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 25: *Prefer lists to arrays*) -->
(В <b>Java</b> нам этот урок дорого стоил, см. [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 25: *Prefer lists to arrays*)

<!-- That's why the actual signature of `addAll()` is the following: -->
Вот почему сигнатура `addAll()` на самом деле такая:

``` java
// Java
interface Collection<E> ... {
  void addAll(Collection<? extends E> items);
}
```

<!-- The **wildcard type argument** `? extends E` indicates that this method accepts a collection of objects of `E` *or some subtype of* `E`, not just `E` itself. -->
**Маска для аргумента** `? extends E` указывает на то, что этот метод принимает коллекцию объектов `E` *или некоего типа унаследованного от* `E`, а не сам `E`.
<!-- This means that we can safely **read** `E`'s from items (elements of this collection are instances of a subclass of E), but **cannot write** to -->
<!-- it since we do not know what objects comply to that unknown subtype of `E`. -->
<!-- In return for this limitation, we have the desired behaviour: `Collection<String>` *is* a subtype of `Collection<? extends Object>`. -->
Это значит, что мы можем безопасно **читать** объекты типа `E` из содержимого (элементы коллекции являются экземплярами подкласса E), но **не можем их изменять**, потому что не знаем, какие объекты соответствуют этому неизвестному подтипу от `E`.
Минуя это ограничение, мы достигаем желаемого результата: `Collection<String>`
**является** подтипом `Collection<? extends Object>`.
 <!-- In "clever words", the wildcard with an **extends**\-bound (**upper** bound) makes the type **covariant**. -->
 Выражаясь более "умными словами", маска с **extends**-связкой (**верхнее** связывание) делает тип **ковариантным** (ориг. covariant).

<!-- The key to understanding why this trick works is rather simple: if you can only **take** items from a collection, then using a collection of `String`s -->
<!-- and reading `Object`s from it is fine. Conversely, if you can only _put_ items into the collection, it's OK to take a collection of -->
<!-- `Object`s and put `String`s into it: in Java we have `List<? super String>` a **supertype** of `List<Object>`. -->
Ключом к пониманию, почему этот трюк работает, является довольно простая мысль: использование коллекции `String`'ов и чтение из неё `Object`'ов нормально только в случае, если  вы **берёте** элементы из коллекции. Наоборот, если вы только _вносите_ элементы в коллекцию, то нормально брать коллекцию `Object`'ов и помещать в неё `String`'и: в <b>Java</b> есть `List<? super String>`, **супертип** `List<Object>`'a.

<!-- The latter is called **contravariance**, and you can only call methods that take String as an argument on `List<? super String>` -->
<!-- (e.g., you can call `add(String)` or `set(int, String)`), while -->
<!-- if you call something that returns `T` in `List<T>`, you don't get a `String`, but an `Object`. -->
Это назвается **контрвариантностью**. В `List<? super String>` вы можете вызвать только те методы, которые принимают String в качестве аргумента (например, `add(String)` или `set(int, String)`). В случае, если вы вызываете из `List<T>` что-то c возвращаемым значением `T`, вы получаете не `String`, а `Object`.

<!-- Joshua Bloch calls those objects you only **read** from **Producers**, and those you only **write** to **Consumers**. He recommends: "*For maximum flexibility, use wildcard types on input parameters that represent producers or consumers*", and proposes the following mnemonic: -->
Джошуа Блок (Joshua Block) называет объекты:
- **Производителями** (ориг.:_producers_), если вы только **читаете** из них
- **Потребителями** (ориг.: _consumers_), если вы только **записываете** в них

Его рекомендация: "*Для максимальной гибкости используйте маски (ориг. wildcards) на входных параметрах, которые представляют производителей или потребителей*"

<!-- *PECS stands for Producer-Extends, Consumer-Super.* -->
*PECS настаивает на Producer-Extends, Consumer-Super.*

<!-- *NOTE*: if you use a producer-object, say, `List<? extends Foo>`, you are not allowed to call `add()` or `set()` on this object, but this does not mean -->
<!-- that this object is **immutable**: for example, nothing prevents you from calling `clear()` to remove all items from the list, since `clear()` -->
<!-- does not take any parameters at all. The only thing guaranteed by wildcards (or other types of variance) is **type safety**. Immutability is a completely different story. -->

>*Примечание*: если вы используете объект-производитель, предположим, `List<? extends Foo>`, вы не можете вызвать методы `add()` или `set()` этого объекта. Но это не значит, что объект является **неизменяемым** (immutable): ничто не мешает вам вызвать метод `clear()` для того, чтобы очистить список, так как `clear()` не имеет аргументов. Единственное, что гарантируют маски — **безопасность типов**. Неизменяемость (ориг.: _immutability_) — совершенно другая история.


<!-- ### Declaration-site variance -->
### Вариантность на уровне объявления

<!-- Suppose we have a generic interface `Source<T>` that does not have any methods that take `T` as a parameter, only methods that return `T`: -->
Допустим, у нас есть generic интерфейс `Source<T>`, у которого нет методов, которые принимают `T` в качестве аргумента. Только методы, возвращающие `T`:

``` java
// Java
interface Source<T> {
  T nextT();
}
```

<!-- Then, it would be perfectly safe to store a reference to an instance of `Source<String>` in a variable of type `Source<Object>` -- there are no consumer-methods to call. But Java does not know this, and still prohibits it: -->
Тогда было бы вполне безопасно хранить ссылки на экземляр `Source<String>` в переменной типа `Source<Object>` — не нужно вызывать никакие методы-потребители. Но Java не знает этого и не воспринимает такой код:

``` java
// Java
void demo(Source<String> strs) {
  Source<Object> objects = strs; // !!! Запрещено в Java
  // ...
}
```

<!--To fix this, we have to declare objects of type `Source<? extends Object>`, which is sort of meaningless, because we can call all the same methods on such a variable as before, so there's no value added by the more complex type. But the compiler does not know that.-->
Чтобы исправить это, нам нужно объявить объекты типа `Source<? extends Object>`, что в каком-то роде бессмысленно, потому что мы можем вызывать у переменных только те методы, что и ранее, стало быть более сложный тип не добавляет значения. Но компилятор не знает этого.

<!--In Kotlin, there is a way to explain this sort of thing to the compiler. This is called **declaration-site variance**: we can annotate the **type parameter** `T` of Source to make sure that it is only **returned** (produced) from members of `Source<T>`, and never consumed.
To do this we provide the **out** modifier:-->
В Kotlin существует способ объяснить вещь такого рода компилятору. Он называется **вариантность на уровне объявления**: мы можем пометить аннотацией **параметризованный тип** `T` класса `Source`, чтобы удостовериться, что он только **возвращается** (производится) членами `Source<T>`, и никогда не потребляется. Чтобы сделать это, нам необходимо использовать модификатор **out**

``` kotlin
abstract class Source<out T> {
    abstract fun nextT(): T
}

fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs // Всё в порядке, т.к. T — out-параметр
    // ...
}
```

<!--The general rule is: when a type parameter `T` of a class `C` is declared **out**, it may occur only in **out**\-position in the members of `C`, but in return `C<Base>` can safely be a supertype of `C<Derived>`.-->
Общее правило таково: когда параметр `T` класса `С` объявлен как **out**, он может использоваться только в **out**-местах в членах `C`. Но зато `C<Base>` может быть родителем `C<Derived>`, и это будет безопасно.

<!--In "clever words" they say that the class `C` is **covariant** in the parameter `T`, or that `T` is a **covariant** type parameter.
You can think of `C` as being a **producer** of `T`'s, and NOT a **consumer** of `T`'s.-->
Говоря "умными словами", класс `C` **ковариантен** в параметре `T`; или: `T` является **ковариантным** параметризованным типом.

<!--The **out** modifier is called a **variance annotation**, and  since it is provided at the type parameter declaration site, we talk about **declaration-site variance**.
This is in contrast with Java's **use-site variance** where wildcards in the type usages make the types covariant.-->
Модификатор **out** называют **вариативной аннотацией**, и так как он указывается на месте объявления типа параметра, речь идёт о **вариативности на месте объявления**. Эта концепция противопоставлена **вариативности на месте использования** из Java, где маски при использовании типа делают типы ковариантными.

<!--In addition to **out**, Kotlin provides a complementary variance annotation: **in**. It makes a type parameter **contravariant**: it can only be consumed and never
produced. A good example of a contravariant class is `Comparable`:-->
В дополнении к **out**, Kotlin предоставляет дополнительную вариативную аннотацию **in**. Она делает параметризованный тип **контравариантным**: он может только потребляться, но не может производиться. `Comparable` является хорошим примером такого класса:

``` kotlin
abstract class Comparable<in T> {
    abstract fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    x.compareTo(1.0) // 1.0 имеет тип Double, расширяющий Number
    // Таким образом, мы можем присвоить значение x переменной типа Comparable<Double>
    val y: Comparable<Double> = x // OK!
}
```

<!--We believe that the words **in** and **out** are self-explaining (as they were successfully used in C# for quite some time already),
thus the mnemonic mentioned above is not really needed, and one can rephrase it for a higher purpose:-->
Мы верим, что слова **in** и **out** говорят сами за себя (так как они довольно успешно используются в C# уже долгое время), таким образом, мнемоника, приведённая выше, не так уж и нужна, и её можно перефразировать следущим образом:

**[Экзистенцианальная](https://ru.wikipedia.org/wiki/%D0%AD%D0%BA%D0%B7%D0%B8%D1%81%D1%82%D0%B5%D0%BD%D1%86%D0%B8%D0%B0%D0%BB%D0%B8%D0%B7%D0%BC) Трансформация: Consumer in, Producer out\!** :-)

<a name="type-projections"></a>

<!--## Type projections-->
## Проекции типов

<!--### Use-site variance: Type projections-->
### Вариативность на месте использования

<!--It is very convenient to declare a type parameter T as *out* and have no trouble with subtyping on the use site. Yes, it is, when the class in question **can** actually be restricted to only return `T`'s, but what if it can't?
A good example of this is Array:-->
Объявлять параметризованный тип `T` как **out** очень удобно: при его использовании не будет никаких проблем с подтипами. И это действительно так в случае с классами, которые могут быть ограничены на только возвращение `T`. А как быть с теми классами, которые ещё и принимают `T`? Пример: класс `Array`
``` kotlin
class Array<T>(val size: Int) {
    fun get(index: Int): T { /* ... */ }
    fun set(index: Int, value: T) { /* ... */ }
}
```

<!--This class cannot be either co\- or contravariant in `T`. And this imposes certain inflexibilities. Consider the following function:-->
Этот класс не может быть ни ко-, ни контравариантным в `T`, что ведёт к некоторому снижению гибкости. Рассмотрим следующую функцию:

``` kotlin
fun copy(from: Array<Any>, to: Array<Any>) {
    assert(from.size == to.size)
    for (i in from.indices)
        to[i] = from[i]
}
```

<!--This function is supposed to copy items from one array to another. Let's try to apply it in practice:-->
По задумке, это функция должна копировать значения из одного массива в другой. Давате попробуем сделать это на практике:

``` kotlin
val ints: Array<Int> = arrayOf(1, 2, 3)
val any = Array<Any>(3)
copy(ints, any) // Ошибка: ожидалось (Array<Any>, Array<Any>)
```

<!--Here we run into the same familiar problem: `Array<T>` is **invariant** in `T`, thus neither of `Array<Int>` and `Array<Any>`
is a subtype of the other. Why? Again, because copy **might** be doing bad things, i.e. it might attempt to **write**, say, a String to `from`,
and if we actually passed an array of `Int` there, a `ClassCastException` would have been thrown sometime later.-->
Здесь мы попадаем в уже знакомую нам проблему: `Array<T>` **инвариантен** в `T`, таким образом `Array<Int>` не является подтипом `Array<Any>`. Почему? Опять же, потому что копирование **может** сотворить плохие вещи, например может произойти попытка **записать**, скажем, значение типа `String` в `from`. И если мы на самом деле передадим туда массив `Int`, через некоторое время будет выборошен `ClassCastException`.

<!--Then, the only thing we want to ensure is that `copy()` does not do any bad things. We want to prohibit it from **writing** to `from`, and we can:-->
Тогда единственная вещь, в которой мы хотим удостовериться, это то, что `copy()` не сделает ничего плохого. Мы хотим запретить методу **записывать** в `from`, и мы можем это сделать:

``` kotlin
fun copy(from: Array<out Any>, to: Array<Any>) {
 // ...
}
```

<!--What has happened here is called **type projection**: we said that `from` is not simply an array, but a restricted (**projected**) one: we can only call those methods that return the type parameter
`T`, in this case it means that we can only call `get()`. This is our approach to **use-site variance**, and corresponds to Java's `Array<? extends Object>`, but in a slightly simpler way.-->
Произошедшее здесь называется **проекция типов**: мы сказали, что `from` — не просто массив, а ограниченный (**спроецированный**): мы можем вызывать только те методы, которые возвращают параметризованный тип `T`, что в этом случае означает, что мы можем вызывать только `get()`. Таков наш подход к **вариативности на месте использования**, и он соответствует `Array<? extends Object>` из Java, но в более простом виде.

<!--You can project a type with **in** as well:-->
Вы так же можете проецировать тип с **in**:

``` kotlin
fun fill(dest: Array<in String>, value: String) {
    // ...
}
```

<!--`Array<in String>` corresponds to Java's `Array<? super String>`, i.e. you can pass an array of `CharSequence` or an array of `Object` to the `fill()` function.-->
`Array<in String>` соответствует `Array<? super String>` из Java, то есть мы можем передать массив `CharSequence` или массив `Object` в функцию `fill()`.


<!--### Star-projections-->
### "Звёздные" проекции 

<!--Sometimes you want to say that you know nothing about the type argument, but still want to use it in a safe way.
The safe way here is to define such a projection of the generic type, that every concrete instantiation of that generic type would be a subtype of that projection.-->
Иногда возникает ситуация, когда вы ничего не знаете о типе аргумента, но всё равно хотите использовать его безопасным образом.
Этой безопасности можно добиться путём определения такой проекции параметризованного типа, при которой его экземпляр будет подтипом этой проекции.

<!--Kotlin provides so called **star-projection** syntax for this:-->
Kotlin предоставляет так называемый **star-projection** синтаксис для этого:

<!--
- For `Foo<out T : TUpper>`, where `T` is a covariant type parameter with the upper bound `TUpper`, `Foo<*>` is equivalent to `Foo<out TUpper>`. It means that when the `T` is unknown you can safely *read* values of `TUpper` from `Foo<*>`.
 - For `Foo<in T>`, where `T` is a contravariant type parameter, `Foo<*>` is equivalent to `Foo<in Nothing>`. It means there is nothing you can *write* to `Foo<*>` in a safe way when `T` is unknown.
 - For `Foo<T : TUpper>`, where `T` is an invariant type parameter with the upper bound `TUpper`, `Foo<*>` is equivalent to `Foo<out TUpper>` for reading values and to `Foo<in Nothing>` for writing values.
 -->
 - Для `Foo<out T>`, где `T` — ковариантный параметризованный тип с верхней границей `TUpper`, `Foo<*>` является эквивалентом `Foo<out TUpper>`. Это значит, что когда `T` неизвестен, вы можете безопасно *читать* значения типа `TUpper` из `Foo<*>`.
 - Для `Foo<in T>`, где `T` — контравариантный параметризованный тип, `Foo<*>` является эквивалентом `Foo<in Nothing>`. Это значит, что вы не можете безопасно *писать* в `Foo<*>` при неизвестном `T`.
 - Для `Foo<T>`, где `T` — инвариантный параметризованный тип с верхней границей `TUpper`, `Foo<*>` является эквивалентом `Foo<out TUpper>` при чтении значений и `Foo<in Nothing>` при записи значений.

<!--If a generic type has several type parameters each of them can be projected independently.
For example, if the type is declared as `interface Function<in T, out U>` we can imagine the following star-projections:-->
Если параметризованный тип имеет несколько параметров, каждый из них проецируется независимо.
Например, если тип объявлен как `interface Function<in T, out U>`, мы можем представить следующую "звёздную" проекцию:

<!--
 - `Function<*, String>` means `Function<in Nothing, String>`;
 - `Function<Int, *>` means `Function<Int, out Any?>`;
 - `Function<*, *>` means `Function<in Nothing, out Any?>`.
 -->
 - `Function<*, String>` означает `Function<in Nothing, String>`;
 - `Function<Int, *>` означает `Function<Int, out Any?>`;
 - `Function<*, *>` означает `Function<in Nothing, out Any?>`.
<!--*Note*: star-projections are very much like Java's raw types, but safe.-->
*Примечаение*: "звёздные" проекции очень похожи на сырые (raw) типы из Java, за тем исключением, что являются безопасными.

<!--## Generic functions-->
## Обобщённые функции

<!--Not only classes can have type parameters. Functions can, too. Type parameters are placed before the name of the function:-->
Функции, как и классы, могут иметь типовые параметры. Типовые параметры помещаются перед именем функции:

``` kotlin
fun <T> singletonList(item: T): List<T> {
    // ...
}

fun <T> T.basicToString() : String {  // функция-расширение
    // ...
}
```

<!--To call a generic function, specify the type arguments at the call site **after** the name of the function:-->
Для вызова обобщённой функции, укажите тип аргументов на месте вызова **после** имени функции:

``` kotlin
val l = singletonList<Int>(1)
```

<!--## Generic constraints-->
## Обобщённые ограничения

<!--The set of all possible types that can be substituted for a given type parameter may be restricted by **generic constraints**.-->
Набор всех возможных типов, которые могут быть переданы в качестве параметра, может быть ограничен с помощью **обобщённых ограничений**. 

<!--### Upper bounds-->
### Верхние границы

<!--The most common type of constraint is an **upper bound** that corresponds to Java's *extends* keyword:-->
Самый распространённый тип ограничений - **верхняя граница**, которая соответствует ключевому слову *extends* из Java:

``` kotlin
fun <T : Comparable<T>> sort(list: List<T>) {
    // ...
}
```

<!--The type specified after a colon is the **upper bound**: only a subtype of `Comparable<T>` may be substituted for `T`. For example-->
Тип, указанный после двоеточия, является **верхней границей**: только подтип `Comparable<T>` может быть передан в `T`. Например:

``` kotlin
sort(listOf(1, 2, 3)) // Всё в порядке. Int — подтип Comparable<Int>
sort(listOf(HashMap<Int, String>())) // Ошибка: HashMap<Int, String> не является подтипом Comparable<HashMap<Int, String>>
```

<!--The default upper bound (if none specified) is `Any?`. Only one upper bound can be specified inside the angle brackets.
If the same type parameter needs more than one upper bound, we need a separate **where**\-clause:-->
По умолчанию (если не указана явно) верхняя граница — `Any?`. Только одна верхняя граница может быть указана в угловых скобках.
В случае, если один параметризованный тип требует больше чем одной верхней границы, нам нужно использовать разделяющее **where**-условие:

``` kotlin
fun <T> cloneWhenGreater(list: List<T>, threshold: T): List<T>
    where T : Comparable,
          T : Cloneable {
  return list.filter { it > threshold }.map { it.clone() }
}
```
