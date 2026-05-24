---
type: doc
layout: reference
category: "Syntax"
title: "Обобщения (Generics): in, out, where"
menuLabel: "Обобщения: in, out, where"
url: https://kotlinlang.ru/docs/generics.html
---

<!-- При обновлении перевода использована оригинальная версия от 17 December 2024 -->

<!-- # Generics: in, out, where -->
# Обобщения (Generics): in, out, where

<!-- Classes in Kotlin can have type parameters, just like in Java: -->
Как и в Java, в Kotlin классы могут иметь типовые параметры.

```kotlin
class Box<T>(t: T) {
    var value = t
}
```

<!-- To create an instance of such a class, simply provide the type arguments: -->
Для того чтобы создать объект такого класса, необходимо предоставить тип в качестве аргумента.

```kotlin
val box: Box<Int> = Box<Int>(1)
```

<!-- But if the parameters can be inferred, for example, from the constructor arguments,
you can omit the type arguments: -->
Но если параметры могут быть выведены из контекста (в аргументах конструктора или в некоторых других случаях),
можно опустить указание типа.

```kotlin
val box = Box(1) // 1 имеет тип Int, поэтому компилятор отмечает для себя, что тип переменной box — Box<Int>
```

<a name="variance"></a>
<!-- ## Variance -->
## Вариативность

<!-- One of the trickiest aspects of Java's type system is the wildcard types (see [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)).
Kotlin doesn't have these. Instead, Kotlin has declaration-site variance and type projections. -->
Одним из самых сложных мест в системе типов Java являются маски (ориг. *wildcards*)
(см. [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)). А в Kotlin их нет.
Вместо этого, в Kotlin есть вариативность на уровне объявления и проекции типов.

<a name="variance-and-wildcards-in-java"></a>
<!-- ### Variance and wildcards in Java -->
### Вариантность и маски в Java

<!-- Let's think about why Java needs these mysterious wildcards. First, generic types in Java are _invariant_,
meaning that `List<String>` is _not_ a subtype of `List<Object>`. If `List` were not _invariant_, it would
have been no better than Java's arrays, as the following code would have compiled but caused an exception at runtime: -->
Давайте подумаем, зачем Java нужны эти загадочные маски. Прежде всего, обобщённые типы в Java являются
*инвариантными* (ориг. *invariant*). Это означает, что `List<String>` *не является* подтипом `List<Object>`.
Если бы `List` не был *инвариантным*, он был бы ничем не лучше массивов Java: следующий код компилировался бы,
но вызывал исключение во время выполнения.

```java
// Java
List<String> strs = new ArrayList<String>();

// Java сообщает о несоответствии типов здесь, во время компиляции.
List<Object> objs = strs;

// Что было бы, если бы Java этого не делала?
// Мы могли бы положить Integer в список строк.
objs.add(1);

// А затем во время выполнения Java выбросила бы
// ClassCastException: Integer cannot be cast to String
String s = strs.get(0);
```

<!-- Java prohibits such things to guarantee runtime safety. But this has implications. For example,
consider the `addAll()` method from the `Collection` interface. What's the signature of this method? Intuitively,
you'd write it this way: -->
Java запрещает подобные вещи, гарантируя тем самым безопасность выполнения кода.
Но у такого подхода есть свои последствия. Рассмотрим, например, метод `addAll()` интерфейса `Collection`.
Какова сигнатура данного метода? Интуитивно мы бы указали её таким образом:

```java
// Java
interface Collection<E> ... {
    void addAll(Collection<E> items);
}
```

<!-- But then, you would not be able to do the following (which is perfectly safe): -->
Но тогда мы бы не могли выполнять следующую простую операцию (которая является абсолютно безопасной):

```java
// Java

// Следующий код не скомпилировался бы с наивным объявлением addAll:
// Collection<String> не является подтипом Collection<Object>
void copyAll(Collection<Object> to, Collection<String> from) {
    to.addAll(from);
}
```

<!-- That's why the actual signature of `addAll()` is the following: -->
Вот почему сигнатура `addAll()` на самом деле такая:

```java
// Java
interface Collection<E> ... {
    void addAll(Collection<? extends E> items);
}
```

<!-- The _wildcard type argument_ `? extends E` indicates that this method accepts a collection of objects of `E`
_or a subtype of_ `E`, not just `E` itself. This means that you can safely _read_ `E`'s from items
(elements of this collection are instances of a subclass of E), but _cannot write_ to
it as you don't know what objects comply with that unknown subtype of `E`.
In return for this limitation, you get the desired behavior: `Collection<String>` _is_ a subtype of `Collection<? extends Object>`.
In other words, the wildcard with an _extends_\-bound (_upper_ bound) makes the type _covariant_. -->
*Маска для аргумента* `? extends E` указывает на то, что этот метод принимает коллекцию объектов `E`
*или некоего типа унаследованного от* `E`, а не только сам `E`. Это значит, что вы можете безопасно *читать*
объекты типа `E` из содержимого (элементы коллекции являются экземплярами подкласса E), но *не можете их присваивать*,
потому что не знаете, какие объекты соответствуют этому неизвестному подтипу `E`. Минуя это ограничение,
желаемый результат может быть достигнут: `Collection<String>` *является* подтипом `Collection<? extends Object>`.
Другими словами, маска с *extends*-связкой (*верхнее* связывание) делает тип *ковариантным* (ориг. *covariant*).

<!-- The key to understanding why this works is rather simple: if you can only _take_ items from a collection,
then using a collection of `String`s and reading `Object`s from it is fine. Conversely, if you can only _put_ items
into the collection, it's okay to take a collection of `Object`s and put `String`s into it: in Java there is
`List<? super String>`, which accepts `String`s or any of its supertypes. -->
Ключом к пониманию, почему этот трюк работает, является довольно простая мысль: использование коллекции `String`'ов и
чтение из неё `Object` нормально только в случае, если вы только *берёте* элементы из коллекции. Наоборот, если вы
только *вносите* элементы в коллекцию, то нормально брать коллекцию `Object`'ов и помещать в неё `String`'и:
в Java есть `List<? super String>`, который принимает `String` или любой его супертип.

<!-- The latter is called _contravariance_, and you can only call methods that take `String` as an argument on `List<? super String>`
(for example, you can call `add(String)` or `set(int, String)`).  If you call something that returns `T` in `List<T>`,
you don't get a `String`, but rather an `Object`. -->
Это называется *контрвариантностью* (ориг.: *contravariance*). Вы можете вызвать только те методы, которые принимают
`String` в качестве аргумента в `List<? super String>` (например, вы можете вызвать `add(String)` или `set(int, String)`).
В случае, если вы вызываете из `List<T>` что-то с возвращаемым значением `T`, вы получаете не `String`, а `Object`.

<!-- Joshua Bloch, in his book [Effective Java, 3rd Edition](http://www.oracle.com/technetwork/java/effectivejava-136174.html), explains the problem well
(Item 31: "Use bounded wildcards to increase API flexibility"). He gives the name _Producers_ to objects you only
_read from_ and _Consumers_ to those you only _write to_. He recommends: -->
Джошуа Блох (Joshua Bloch) хорошо объясняет эту проблему в книге
[Effective Java, 3rd Edition](http://www.oracle.com/technetwork/java/effectivejava-136174.html)
(Item 31: "Use bounded wildcards to increase API flexibility"). Он называет объекты:
- *Производителями* (ориг.: *producers*), если вы только *читаете* из них,
- *Потребителями* (ориг.: *consumers*), если вы только *записываете* в них.

Он рекомендует:

<!-- >"For maximum flexibility, use wildcard types on input parameters that represent producers or consumers." -->
> "Для максимальной гибкости используйте типы-маски во входных параметрах, которые представляют производителей
> или потребителей."

<!-- He then proposes the following mnemonic: _PECS_ stands for _Producer-Extends, Consumer-Super._ -->
Затем он предлагает следующую мнемонику: *PECS* означает *Producer-Extends, Consumer-Super*.

<!-- > If you use a producer-object, say, `List<? extends Foo>`, you are not allowed to call `add()` or `set()` on this object,
> but this does not mean that it is _immutable_: for example, nothing prevents you from calling `clear()`
> to remove all the items from the list, since `clear()` does not take any parameters at all.
>
>The only thing guaranteed by wildcards (or other types of variance) is _type safety_. Immutability is a completely different story. -->
> Если вы используете объект-производитель, предположим, `List<? extends Foo>`, вы не можете вызвать методы `add()` или
> `set()` этого объекта. Но это не значит, что объект является *неизменяемым*: например, ничто не мешает вам вызвать
> метод `clear()` для того, чтобы очистить список, так как `clear()` не имеет аргументов.
> 
> Единственное, что гарантируют маски (или другие типы вариантности) — *безопасность типов*.
> Неизменяемость — совершенно другая история.

<a name="declaration-site-variance"></a>
<!-- ### Declaration-site variance -->
### Вариантность на уровне объявления

<!-- Let's suppose that there is a generic interface `Source<T>` that does not have any methods that take `T` as a parameter, only methods that return `T`: -->
Допустим, у вас есть обобщённый интерфейс `Source<T>`, у которого нет методов, принимающих `T` в качестве аргумента.
Только методы, возвращающие `T`:

```java
// Java
interface Source<T> {
    T nextT();
}
```

<!-- Then, it would be perfectly safe to store a reference to an instance of `Source<String>` in a variable of
type `Source<Object>` - there are no consumer-methods to call. But Java does not know this, and still prohibits it: -->
Тогда было бы вполне безопасно хранить ссылки на экземпляр `Source<String>` в переменной типа `Source<Object>` —
не нужно вызывать никакие методы-потребители. Но Java не знает этого и не воспринимает такой код.

```java
// Java
void demo(Source<String> strs) {
    Source<Object> objects = strs; // !!! Запрещено в Java
    // ...
}
```

<!-- To fix this, you should declare objects of type `Source<? extends Object>`. Doing so is meaningless,
because you can call all the same methods on such a variable as before, so there's no value added by the more complex type.
But the compiler does not know that. -->
Чтобы исправить это, вам нужно объявить объекты типа `Source<? extends Object>`, что в каком-то роде бессмысленно,
потому что у переменной такого типа вы можете вызывать только те методы, что и ранее, стало быть,
более сложный тип не добавляет смысла. Но компилятор этого не понимает.

<!-- In Kotlin, there is a way to explain this sort of thing to the compiler. This is called _declaration-site variance_:
you can annotate the _type parameter_ `T` of `Source` to make sure that it is only _returned_ (produced) from members
of `Source<T>`, and never consumed.
To do this, use the `out` modifier: -->
В Kotlin существует способ объяснить вещь такого рода компилятору. Он называется *вариантность на уровне объявления*:
вы можете объявить *типовой параметр* `T` класса `Source` таким образом, чтобы удостовериться, что он только
*возвращается* (производится) членами `Source<T>`, и никогда не потребляется. Чтобы сделать это,
вам необходимо использовать модификатор `out`.

```kotlin
interface Source<out T> {
    fun nextT(): T
}

fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs // Всё в порядке, т.к. T — out-параметр
    // ...
}
```

<!-- The general rule is this: when a type parameter `T` of a class `C` is declared `out`, it may occur only in the _out_-position
in the members of `C`, but in return `C<Base>` can safely be a supertype of `C<Derived>`. -->
Общее правило таково: когда параметр `T` класса `С` объявлен как `out`, он может использоваться только в *out*-местах
в членах `C`. Но зато `C<Base>` может быть родителем `C<Derived>`, и это будет безопасно.

<!-- In other words, you can say that the class `C` is _covariant_ in the parameter `T`, or that `T` is a _covariant_ type parameter.
You can think of `C` as being a _producer_ of `T`'s, and NOT a _consumer_ of `T`'s. -->
Другими словами, класс `C` *ковариантен* в параметре `T`; или `T` является *ковариантным* типовым параметром.
`C` *производит* экземпляры типа `T`, но не *потребляет* их.

<!-- The `out` modifier is called a _variance annotation_, and  since it is provided at the type parameter declaration site,
it provides _declaration-site variance_.
This is in contrast with Java's _use-site variance_ where wildcards in the type usages make the types covariant. -->
Модификатор `out` определяет *вариантность*, и так как он указывается на месте объявления типового параметра,
речь идёт о *вариативности на месте объявления*. Эта концепция противопоставлена *вариативности на месте использования*
из Java, где маски при использовании типа делают типы ковариантными.

<!-- In addition to `out`, Kotlin provides a complementary variance annotation: `in`. It makes a type parameter _contravariant_, meaning
it can only be consumed and never produced. A good example of a contravariant type is `Comparable`: -->
В дополнении к `out`, Kotlin предоставляет дополнительный модификатор `in`. Он делает параметризованный тип *контравариантным*:
он может только потребляться, но не может производиться. `Comparable` является хорошим примером такого класса:

```kotlin
interface Comparable<in T> {
    operator fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    x.compareTo(1.0) // 1.0 имеет тип Double, расширяющий Number
    // Таким образом, мы можем присвоить значение x переменной типа Comparable<Double>
    val y: Comparable<Double> = x // OK!
}
```

<!-- The words _in_ and _out_ seem to be self-explanatory (as they’ve already been used successfully in C# for quite some time),
and so the mnemonic mentioned above is not really needed. It can in fact be rephrased at a higher level of abstraction: -->
Слова `in` и `out` говорят сами за себя (так как они довольно успешно используются в C# уже долгое время), таким образом,
мнемоника, приведённая выше, не так уж и нужна, и её можно перефразировать следующим образом:

<!-- **[The Existential](https://en.wikipedia.org/wiki/Existentialism) Transformation: Consumer in, Producer out\!** :-) -->
**[Экзистенциальная](https://ru.wikipedia.org/wiki/%D0%AD%D0%BA%D0%B7%D0%B8%D1%81%D1%82%D0%B5%D0%BD%D1%86%D0%B8%D0%B0%D0%BB%D0%B8%D0%B7%D0%BC) Трансформация: Consumer in, Producer out\!** :-)

<a name="type-projections"></a>
<!-- ## Type projections -->
## Проекции типов

<a name="use-site-variance-type-projections"></a>
<!-- ### Use-site variance: type projections -->
### Вариативность на месте использования

<!-- It is very easy to declare a type parameter `T` as `out` and avoid trouble with subtyping on the use site,
but some classes _can't_ actually be restricted to only return `T`'s!
A good example of this is `Array`: -->
Объявлять параметризованный тип `T` как `out` очень удобно: при его использовании не будет никаких проблем с подтипами.
И это действительно так в случае с классами, которые могут быть ограничены на только возвращение `T`.
А как быть с теми классами, которые ещё и принимают `T`? Хороший пример этого: класс `Array`:

```kotlin
class Array<T>(val size: Int) {
    operator fun get(index: Int): T { ... }
    operator fun set(index: Int, value: T) { ... }
}
```

<!-- This class can be neither co- nor contravariant in `T`. And this imposes certain inflexibilities. Consider the following function: -->
Этот класс не может быть ни ко-, ни контравариантным по `T`, что ведёт к некоторому снижению гибкости.
Рассмотрим следующую функцию:

```kotlin
fun copy(from: Array<Any>, to: Array<Any>) {
    assert(from.size == to.size)
    for (i in from.indices)
        to[i] = from[i]
}
```

<!-- This function is supposed to copy items from one array to another. Let's try to apply it in practice: -->
По задумке, это функция должна копировать значения из одного массива в другой. Давайте попробуем сделать это на практике:

```kotlin
val ints: Array<Int> = arrayOf(1, 2, 3)
val any = Array<Any>(3) { "" } 
copy(ints, any)
//   ^ Ошибка: тип Array<Int>, а ожидалось Array<Any>
```

<!-- Here you run into the same familiar problem: `Array<T>` is _invariant_ in `T`, and so neither `Array<Int>` nor `Array<Any>`
is a subtype of the other. Why not? Again, this is because `copy` could have an unexpected behavior, for example, it may attempt to
write a `String` to `from`, and if you actually pass an array of `Int` there, a `ClassCastException` will be thrown later. -->
Здесь вы встречаете уже знакомую вам проблему: `Array<T>` *инвариантен* по `T`, таким образом `Array<Int>` не является
подтипом `Array<Any>`. Почему? Опять же, потому что копирование потенциально опасно, например может произойти попытка
*записать*, скажем, значение типа `String` в `from`. И если мы на самом деле передадим туда массив `Int`,
будет выброшен `ClassCastException`.

<!-- To prohibit the `copy` function from _writing_ to `from`, you can do the following: -->
Чтобы запретить функции `copy` *записывать* в `from`, вы можете сделать следующее:

```kotlin
fun copy(from: Array<out Any>, to: Array<Any>) { ... }
```

<!-- This is _type projection_, which means that `from` is not a simple array, but is rather a restricted (_projected_) one.
You can only call methods that return the type parameter `T`, which in this case means that you can only call `get()`.
This is our approach to _use-site variance_, and it corresponds to Java's `Array<? extends Object>` while being slightly simpler. -->
Произошедшее здесь называется *проекция типов*: мы сказали, что `from` — не просто массив, а ограниченный (*спроецированный*):
мы можем вызывать только те методы, которые возвращают параметризованный тип `T`, что в этом случае означает,
что мы можем вызывать только `get()`. Таков наш подход к *вариативности на месте использования*,
и он соответствует `Array<? extends Object>` из Java, но в более простом виде.

<!-- You can project a type with `in` as well: -->
Вы также можете проецировать тип с помощью `in`.

```kotlin
fun fill(dest: Array<in String>, value: String) { ... }
```

<!-- `Array<in String>` corresponds to Java's `Array<? super String>`. This means that you can pass an array of `String`, `CharSequence`,
or `Object` to the `fill()` function. -->
`Array<in String>` соответствует `Array<? super String>` из Java, то есть мы можем передать массив `String`, `CharSequence`
или `Object` в функцию `fill()`.

<a name="star-projections"></a>
<!-- ### Star-projections -->
### "Звёздные" проекции

<!-- Sometimes you want to say that you know nothing about the type argument, but you still want to use it in a safe way.
The safe way here is to define such a projection of the generic type, that every concrete instantiation of that generic
type will be a subtype of that projection. -->
Иногда возникает ситуация, когда вы ничего не знаете о типе аргумента, но всё равно хотите использовать его безопасным образом.
Этой безопасности можно добиться путём определения такой проекции параметризованного типа,
при которой его экземпляр будет подтипом этой проекции.

<!-- Kotlin provides so-called _star-projection_ syntax for this: -->
Для этого в Kotlin есть так называемый синтаксис *star-projection*:

<!-- - For `Foo<out T : TUpper>`, where `T` is a covariant type parameter with the upper bound `TUpper`, `Foo<*>` is
  equivalent to `Foo<out TUpper>`. This means that when the `T` is unknown you can safely _read_ values of `TUpper` from `Foo<*>`.
- For `Foo<in T>`, where `T` is a contravariant type parameter, `Foo<*>` is equivalent to `Foo<in Nothing>`. This means
  there is nothing you can _write_ to `Foo<*>` in a safe way when `T` is unknown.
- For `Foo<T : TUpper>`, where `T` is an invariant type parameter with the upper bound `TUpper`, `Foo<*>` is equivalent
  to `Foo<out TUpper>` for reading values and to `Foo<in Nothing>` for writing values. -->
* Для `Foo<out T : TUpper>`, где `T` — ковариантный параметризованный тип с верхней границей `TUpper`, `Foo<*>` является
эквивалентом `Foo<out TUpper>`. Это значит, что когда `T` неизвестен, вы можете безопасно *читать* значения типа
`TUpper` из `Foo<*>`;
* Для `Foo<in T>`, где `T` — контравариантный параметризованный тип, `Foo<*>` является эквивалентом `Foo<in Nothing>`.
Это значит, что вы не можете безопасно *писать* в `Foo<*>` при неизвестном `T`;
* Для `Foo<T : TUpper>`, где `T` — инвариантный параметризованный тип с верхней границей `TUpper`, `Foo<*>` является
эквивалентом `Foo<out TUpper>` при чтении значений и `Foo<in Nothing>` при записи значений.

<!-- If a generic type has several type parameters, each of them can be projected independently.
For example, if the type is declared as `interface Function<in T, out U>` you could use the following star-projections: -->
Если параметризованный тип имеет несколько параметров, каждый из них проецируется независимо.
Например, если тип объявлен как `interface Function<in T, out U>`, вы можете представить следующую "звёздную" проекцию:

<!-- * `Function<*, String>` means `Function<in Nothing, String>`.
* `Function<Int, *>` means `Function<Int, out Any?>`.
* `Function<*, *>` means `Function<in Nothing, out Any?>`. -->
* `Function<*, String>` означает `Function<in Nothing, String>`;
* `Function<Int, *>` означает `Function<Int, out Any?>`;
* `Function<*, *>` означает `Function<in Nothing, out Any?>`.

<!-- > Star-projections are very much like Java's raw types, but safe. -->
> "Звёздные" проекции очень похожи на сырые (raw) типы из Java, за тем исключением того, что они безопасны.

<a name="generic-functions"></a>
<!-- ## Generic functions -->
## Обобщённые функции

<!-- Classes aren’t the only declarations that can have type parameters. Functions can, too. Type parameters are placed _before_ the name of the function: -->
Функции, как и классы, могут иметь типовые параметры. Типовые параметры помещаются *перед* именем функции.

```kotlin
fun <T> singletonList(item: T): List<T> {
    // ...
}

fun <T> T.basicToString(): String {  // функция-расширение
    // ...
}
```

<!-- To call a generic function, specify the type arguments at the call site _after_ the name of the function: -->
Для вызова обобщённой функции, укажите тип аргументов на месте вызова *после* имени функции.

```kotlin
val l = singletonList<Int>(1)
```

<!-- Type arguments can be omitted if they can be inferred from the context, so the following example works as well: -->
Аргументы типа можно опустить, если их можно вывести из контекста, поэтому следующий пример также работает:

```kotlin
val l = singletonList(1)
```

<a name="generic-constraints"></a>
<!-- ## Generic constraints -->
## Обобщённые ограничения

<!-- The set of all possible types that can be substituted for a given type parameter may be restricted by _generic constraints_. -->
Набор всех возможных типов, которые могут быть переданы в качестве параметра, может быть ограничен с помощью *обобщённых ограничений*. 

<a name="upper-bounds"></a>
<!-- ### Upper bounds -->
### Верхние границы

<!-- The most common type of constraint is an _upper bound_, which corresponds to Java's `extends` keyword: -->
Самый распространённый тип ограничений - *верхняя граница*, которая соответствует ключевому слову `extends` из Java.

```kotlin
fun <T : Comparable<T>> sort(list: List<T>) { ... }
```

<!-- The type specified after a colon is the _upper bound_, indicating that only a subtype of `Comparable<T>` can be substituted for `T`. For example: -->
Тип, указанный после двоеточия, является *верхней границей*: только подтип `Comparable<T>` может быть передан в `T`. Например:

```kotlin
sort(listOf(1, 2, 3)) // Всё в порядке. Int — подтип Comparable<Int>
sort(listOf(HashMap<Int, String>())) // Ошибка: HashMap<Int, String> не является подтипом Comparable<HashMap<Int, String>>
```

<!-- The default upper bound (if there was none specified) is `Any?`. Only one upper bound can be specified inside the angle brackets.
If the same type parameter needs more than one upper bound, you need a separate _where_\-clause: -->
По умолчанию (если не указана явно) верхняя граница — `Any?`. В угловых скобках может быть указана только одна верхняя граница.
Для указания нескольких верхних границ нужно использовать отдельное условие `where`.

```kotlin
fun <T> copyWhenGreater(list: List<T>, threshold: T): List<String>
    where T : CharSequence,
          T : Comparable<T> {
    return list.filter { it > threshold }.map { it.toString() }
}
```

<!-- The passed type must satisfy all conditions of the `where` clause simultaneously. In the above example, the `T` type
must implement _both_ `CharSequence` and `Comparable`. -->
Переданный тип должен одновременно удовлетворять всем условиям `where`. В приведенном выше примере тип `T` должен
реализовывать и `CharSequence`, и `Comparable`.

<a name="definitely-non-nullable-types"></a>
<!-- ## Definitely non-nullable types -->
## Определённо ненулевые типы

<!-- To make interoperability with generic Java classes and interfaces easier, Kotlin supports declaring a generic type parameter
as **definitely non-nullable**. -->
Чтобы упростить взаимодействие с обобщёнными Java-классами и интерфейсами, Kotlin поддерживает объявление обобщённого
параметра типа как **определённо ненулевого**.

<!-- To declare a generic type `T` as definitely non-nullable, declare the type with `& Any`. For example: `T & Any`. -->
Чтобы объявить обобщённый тип `T` определённо ненулевым, укажите его с `& Any`. Например: `T & Any`.

<!-- A definitely non-nullable type must have a nullable [upper bound](#upper-bounds). -->
У определённо ненулевого типа должна быть [верхняя граница](#upper-bounds), допускающая `null`.

<!-- The most common use case for declaring definitely non-nullable types is when you want to override a Java method that
contains `@NotNull` as an argument. For example, consider the `load()` method: -->
Чаще всего определённо ненулевые типы объявляют, когда нужно переопределить Java-метод, аргумент которого содержит
`@NotNull`. Например, рассмотрим метод `load()`:

```java
import org.jetbrains.annotations.*;

public interface Game<T> {
    public T save(T x) {}
    @NotNull
    public T load(@NotNull T x) {}
}
```

<!-- To override the `load()` method in Kotlin successfully, you need `T1` to be declared as definitely non-nullable: -->
Чтобы успешно переопределить метод `load()` в Kotlin, нужно объявить `T1` как определённо ненулевой:

```kotlin
interface ArcadeGame<T1> : Game<T1> {
    override fun save(x: T1): T1
    // T1 является определённо ненулевым
    override fun load(x: T1 & Any): T1 & Any
}
```

<!-- When working only with Kotlin, it's unlikely that you will need to declare definitely non-nullable types explicitly because
Kotlin's type inference takes care of this for you. -->
Если вы работаете только с Kotlin, вам вряд ли потребуется объявлять определённо ненулевые типы явно: за это отвечает
вывод типов Kotlin.

<a name="type-erasure"></a>
<!-- ## Type erasure -->
## Стирание типов

<!-- The type safety checks that Kotlin performs for generic declaration usages are done at compile time.
At runtime, the instances of generic types do not hold any information about their actual type arguments.
The type information is said to be _erased_. For example, the instances of `Foo<Bar>` and `Foo<Baz?>` are erased to
just `Foo<*>`. -->
Проверки безопасности типов, которые Kotlin выполняет при использовании обобщённых объявлений, происходят во время компиляции.
Во время выполнения экземпляры обобщённых типов не содержат информации о своих фактических аргументах типа.
Говорят, что информация о типе *стирается*. Например, экземпляры `Foo<Bar>` и `Foo<Baz?>` стираются просто до `Foo<*>`.

<a name="generics-type-checks-and-casts"></a>
<!-- ### Generics type checks and casts -->
### Проверки и приведения обобщённых типов

<!-- Due to the type erasure, there is no general way to check whether an instance of a generic type was created with certain type
arguments at runtime, and the compiler prohibits such `is`-checks such as
`ints is List<Int>` or `list is T` (type parameter). However, you can check an instance against a star-projected type: -->
Из-за стирания типов нет общего способа во время выполнения проверить, был ли экземпляр обобщённого типа создан
с определёнными аргументами типа. Поэтому компилятор запрещает такие `is`-проверки, как `ints is List<Int>`
или `list is T` (параметр типа). Однако можно проверить экземпляр на соответствие типу со звёздной проекцией:

```kotlin
if (something is List<*>) {
    something.forEach { println(it) } // Элементы имеют тип `Any?`
}
```

<!-- Similarly, when you already have the type arguments of an instance checked statically (at compile time),
you can make an `is`-check or a cast that involves the non-generic part of the type. Note that
angle brackets are omitted in this case: -->
Аналогично, если аргументы типа экземпляра уже проверены статически (во время компиляции), можно выполнить `is`-проверку
или приведение, которое затрагивает необобщённую часть типа. Обратите внимание, что угловые скобки в этом случае опускаются:

```kotlin
fun handleStrings(list: MutableList<String>) {
    if (list is ArrayList) {
        // `list` умно приведён к `ArrayList<String>`
    }
}
```

<!-- The same syntax but with the type arguments omitted can be used for casts that do not take type arguments into account: `list as ArrayList`. -->
Тот же синтаксис с опущенными аргументами типа можно использовать для приведений, которые не учитывают аргументы типа:
`list as ArrayList`.

<!-- The type arguments of generic function calls are also only checked at compile time. Inside the function bodies,
the type parameters cannot be used for type checks, and type casts to type parameters (`foo as T`) are unchecked.
The only exclusion is inline functions with [reified type parameters](inline-functions.html#reified-type-parameters),
which have their actual type arguments inlined at each call site. This enables type checks and casts for the type parameters.
However, the restrictions described above still apply for instances of generic types used inside checks or casts.
For example, in the type check `arg is T`, if `arg` is an instance of a generic type itself, its type arguments are still erased. -->
Аргументы типа при вызове обобщённых функций также проверяются только во время компиляции. Внутри тел функций параметры
типа нельзя использовать для проверок типов, а приведения к параметрам типа (`foo as T`) являются непроверенными.
Единственное исключение — inline-функции с [овеществлёнными параметрами типа](inline-functions.html#reified-type-parameters):
их фактические аргументы типа встраиваются в каждом месте вызова. Это позволяет выполнять проверки типов и приведения
для параметров типа. Однако для экземпляров обобщённых типов, используемых в проверках или приведениях, всё равно действуют
описанные выше ограничения. Например, в проверке типа `arg is T`, если `arg` сам является экземпляром обобщённого типа,
его аргументы типа всё равно стираются.

```kotlin
//sampleStart
inline fun <reified A, reified B> Pair<*, *>.asPairOf(): Pair<A, B>? {
    if (first !is A || second !is B) return null
    return first as A to second as B
}

val somePair: Pair<Any?, Any?> = "items" to listOf(1, 2, 3)

val stringToSomething = somePair.asPairOf<String, Any>()
val stringToInt = somePair.asPairOf<String, Int>()
val stringToList = somePair.asPairOf<String, List<*>>()
val stringToStringList = somePair.asPairOf<String, List<String>>() // Компилируется, но нарушает безопасность типов!
// Подробности см. в main ниже

//sampleEnd

fun main() {
    println("stringToSomething = " + stringToSomething)
    println("stringToInt = " + stringToInt)
    println("stringToList = " + stringToList)
    println("stringToStringList = " + stringToStringList)
    //println(stringToStringList?.second?.forEach() {it.length}) // Выбросит ClassCastException, так как элементы списка не String
}
```

<a name="unchecked-casts"></a>
<!-- ### Unchecked casts -->
### Непроверенные приведения

<!-- Type casts to generic types with concrete type arguments such as `foo as List<String>` cannot be checked at runtime.
These unchecked casts can be used when type safety is implied by the high-level program logic but cannot be inferred
directly by the compiler. See the example below. -->
Приведения к обобщённым типам с конкретными аргументами типа, например `foo as List<String>`, нельзя проверить во время
выполнения. Такие непроверенные приведения можно использовать, когда безопасность типов следует из высокоуровневой логики
программы, но компилятор не может вывести её напрямую. См. пример ниже.

```kotlin
fun readDictionary(file: File): Map<String, *> = file.inputStream().use {
    TODO("Read a mapping of strings to arbitrary elements.")
}

// В этот файл мы сохранили отображение с `Int`
val intsFile = File("ints.dictionary")

// Предупреждение: Unchecked cast: `Map<String, *>` to `Map<String, Int>`
val intsDictionary: Map<String, Int> = readDictionary(intsFile) as Map<String, Int>
```

<!-- A warning appears for the cast in the last line. The compiler can't fully check it at runtime and provides
no guarantee that the values in the map are `Int`. -->
Для приведения в последней строке появляется предупреждение. Компилятор не может полностью проверить его во время
выполнения и не гарантирует, что значения в map имеют тип `Int`.

<!-- To avoid unchecked casts, you can redesign the program structure. In the example above, you could use the
`DictionaryReader<T>` and `DictionaryWriter<T>` interfaces with type-safe implementations for different types.
You can introduce reasonable abstractions to move unchecked casts from the call site to the implementation details.
Proper use of [generic variance](#variance) can also help. -->
Чтобы избежать непроверенных приведений, можно пересмотреть структуру программы. В примере выше можно было бы использовать
интерфейсы `DictionaryReader<T>` и `DictionaryWriter<T>` с типобезопасными реализациями для разных типов. Можно ввести
разумные абстракции и перенести непроверенные приведения из места вызова в детали реализации. Правильное использование
[вариантности обобщений](#variance) также может помочь.

<!-- For generic functions, using [reified type parameters](inline-functions.html#reified-type-parameters) makes casts
like `arg as T` checked, unless `arg`'s type has *its own* type arguments that are erased. -->
Для обобщённых функций использование [овеществлённых параметров типа](inline-functions.html#reified-type-parameters)
делает приведения вроде `arg as T` проверяемыми, если только тип `arg` не имеет *собственных* аргументов типа,
которые стираются.

<!-- An unchecked cast warning can be suppressed by [annotating](annotations.html) the statement or the
declaration where it occurs with `@Suppress("UNCHECKED_CAST")`: -->
Предупреждение о непроверенном приведении можно подавить, [аннотировав](annotations.html) инструкцию или объявление,
где оно возникает, с помощью `@Suppress("UNCHECKED_CAST")`:

```kotlin
inline fun <reified T> List<*>.asListOfType(): List<T>? =
    if (all { it is T })
        @Suppress("UNCHECKED_CAST")
        this as List<T>
    else
        null
```

<!-- >**On the JVM**: [array types](arrays.html) (`Array<Foo>`) retain information about the erased type of
>their elements, and type casts to an array type are partially checked: the
>nullability and actual type arguments of the element type are still erased. For example,
>the cast `foo as Array<List<String>?>` will succeed if `foo` is an array holding any `List<*>`, whether it is nullable or not. -->
> **На JVM**: [типы массивов](https://kotlinlang.org/docs/arrays.html) (`Array<Foo>`) сохраняют информацию о стёртом типе своих элементов,
> и приведения к типу массива проверяются частично: допустимость `null` и фактические аргументы типа элемента
> всё равно стираются. Например, приведение `foo as Array<List<String>?>` завершится успешно, если `foo` —
> массив, содержащий любой `List<*>`, независимо от того, допускает он `null` или нет.

<a name="underscore-operator-for-type-arguments"></a>
<!-- ## Underscore operator for type arguments -->
## Оператор подчёркивания для аргументов типа

<!-- The underscore operator `_` can be used for type arguments. Use it to automatically infer a type of the argument when other types are explicitly specified: -->
Оператор подчёркивания `_` можно использовать для аргументов типа. Он позволяет автоматически вывести тип аргумента,
когда остальные типы указаны явно:

```kotlin
abstract class SomeClass<T> {
    abstract fun execute() : T
}

class SomeImplementation : SomeClass<String>() {
    override fun execute(): String = "Test"
}

class OtherImplementation : SomeClass<Int>() {
    override fun execute(): Int = 42
}

object Runner {
    inline fun <reified S: SomeClass<T>, T> run() : T {
        return S::class.java.getDeclaredConstructor().newInstance().execute()
    }
}

fun main() {
    // T выводится как String, потому что SomeImplementation наследуется от SomeClass<String>
    val s = Runner.run<SomeImplementation, _>()
    assert(s == "Test")

    // T выводится как Int, потому что OtherImplementation наследуется от SomeClass<Int>
    val n = Runner.run<OtherImplementation, _>()
    assert(n == 42)
}
```
