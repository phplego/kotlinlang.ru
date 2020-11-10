---
type: doc
layout: reference
category: "Syntax"
title: "Сопрограммы"
url: "https://kotlinlang.ru/docs/reference/coroutines.html"
---

# Сопрограммы
> Сопрограммы получили статус *стабильные* в Kotlin 1.3. Детали см. [ниже](#experimental-status-of-coroutines) 

Некоторые API инициируют долго протекающие операции (такие как сетевой ввод-вывод, файловый ввод-вывод, интенсивная обработка на CPU или GPU и др.), которые требуют блокировки вызывающего кода в ожидании завершения операций. Сопрограммы обеспечивают возможность избежать блокировки исполняющегося потока путём использования более дешёвой и управляемой операции: *приостановки* (suspend) сопрограммы.

Сопрограммы упрощают асинхронное программирование, оставив все осложнения внутри библиотек. Логика программы может быть выражена *последовательно* в сопрограммах, а базовая библиотека будет её реализовывать асинхронно для нас. Библиотека может обернуть соответствующие части кода пользователя в обратные вызовы (callbacks), подписывающиеся на соответствующие события, и диспетчировать исполнение на различные потоки (или даже на разные машины!). Код при этом останется столь же простой, как если бы исполнялся строго последовательно.

Многие асинхронные механизмы, доступные в других языках программирования, могут быть реализованы в качестве библиотек с помощью сопрограмм Kotlin. Это включает в себя [`async`/`await`](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#composing-suspending-functions) из C# и ECMAScript, [channels](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#channels) и [`select`](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#select-expression) из языка Go, и [generators /`yield`](#generators-api-in-kotlincoroutines) из C# или Python. См. описания [ниже](#standard-apis) о библиотеках, реализующих такие конструкции.

<!--See the description [below](#standard-apis) for libraries providing such constructs.-->

## Блокирование против приостановки 

Главным отличительным признаком сопрограмм является то, что они являются вычислениями, которые могут быть *приостановлены без блокирования потока* (вытеснения средствами операционной системы). Блокирование потоков часто является весьма дорогостоящим, особенно при интенсивных нагрузках: только относительно небольшое число потоков из общего числа является активно выполняющимися, поэтому блокировка одного из них ведет к затягиванию какой-нибудь важной части итоговой работы.

С другой стороны, приостановка сопрограммы обходится практически бесплатно. Не требуется переключения контекста (потоков) или иного вовлечения механизмов операционной системы. И сверх этого, приостановка может гибко контролироваться пользовательской библиотекой во многих аспектах: в качестве авторов библиотеки мы можем решать, что происходит при приостановке, и оптимизировать, журналировать или перехватывать в соответствии со своими потребностями.

Еще одно отличие заключается в том, что сопрограммы не могут быть приостановлены на произвольной инструкции, а только в так называемых *точках остановки* (приостановки), которые вызываются в специально маркируемых функциях.

## Останавливаемые функции 

Приостановка происходит в случае вызова функции, обозначенной специальным модификатором `suspend`:

```kotlin 
suspend fun doSomething(foo: Foo): Bar {
    ...
}
```

Такие функции называются *функциями остановки* (приостановки), поскольку их вызовы могут приостановить выполнение сопрограммы (библиотека может принять решение продолжать работу без приостановки, если результат вызова уже доступен). Функции остановки могут иметь параметры и возвращать значения точно так же, как и все обычные функции, но они могут быть вызваны только из сопрограмм или других функций остановки. В конечном итоге, при старте сопрограммы она должна содержать как минимум одну функцию остановки, и функция эта обычно анонимная (лямбда-функция остановки). Давайте взглянем, для примера, на упрощённую функцию `async()` (из библиотеки [`kotlinx.coroutines`](#generators-api-in-kotlincoroutines)):

<!--
Such functions are called *suspending functions*, because calls to them may suspend a coroutine (the library can decide to proceed without suspension, if the result for the call in question is already available). Suspending functions can take parameters and return values in the same manner as regular functions, but they can only be called from coroutines and other suspending functions. In fact, to start a coroutine, there must be at least one suspending function, and it is usually anonymous (i.e. it is a suspending lambda). Let's look at an example, a simplified `async()` function (from the [`kotlinx.coroutines`](#generators-api-in-kotlincoroutines) library): -->

    
```kotlin
fun <T> async(block: suspend () -> T)
``` 

<!--Here, `async()` is a regular function (not a suspending function), but the `block` parameter has a function type with the `suspend` modifier: `suspend () -> T`. So, when we pass a lambda to `async()`, it is a *suspending lambda*, and we can call a suspending function from it: -->

Здесь `async()` является обычной функцией (не функцией остановки), но параметр `block` имеет функциональный тип с модификатором `suspend`:  `suspend () -> T`. Таким образом, когда мы передаём лямбда-функцию в `async()`, она является анонимной функцией остановки, и мы можем вызывать функцию остановки изнутри её:
   
```kotlin
async {
    doSomething(foo)
    ...
}
```

<!--To continue the analogy, `await()` can be a suspending function (hence also callable from within an `async {}` block) that suspends a coroutine until some computation is done and returns its result: -->

Продолжая аналогию, `await()` может быть функцией остановки (также может вызываться из блока `async {}`), которая приостанавливает сопрограмму до тех пор, пока некоторые вычисления не будут выполнены, и затем возвращает их результат:

```kotlin
async {
    ...
    val result = computation.await()
    ...
}
```

<!-- More information on how actual `async/await` functions work in `kotlinx.coroutines` can be found [here](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#composing-suspending-functions). -->

Больше информации о том, как действительно работают функции `async/await` в  `kotlinx.coroutines`, может быть найдено [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#composing-suspending-functions). 

<!--Note that suspending functions `await()` and `doSomething()` can not be called from a regular function like `main()`: -->
Отметим, что функции приостановки `await()` и `doSomething()` не могут быть вызваны из обыкновенных функций, подобных `main()`:

```kotlin
fun main(args: Array<String>) {
    doSomething() // ERROR: Suspending function called from a non-coroutine context 
}
```

<!--Also note that suspending functions can be virtual, and when overriding them, the `suspend` modifier has to be specified: -->

Заметим, что функции остановки могут быть виртуальными, и при их переопределении модификатор `suspend` также должен быть указан:
 
```kotlin
interface Base {
    suspend fun foo()
}

class Derived: Base {
    override suspend fun foo() { ... }
}
``` 

<a name="restrictssuspension-annotation"></a>

### Aннотация @RestrictsSuspension
<!-- 
Extension functions (and lambdas) can also be marked `suspend`, just like regular ones. This enables creation of [DSLs](type-safe-builders.html) and other APIs that users can extend. In some cases the library author needs to prevent the user from adding *new ways* of suspending a coroutine. 

To achieve this, the [`@RestrictsSuspension`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-restricts-suspension/index.html) annotation may be used. When a receiver class or interface `R` is annotated with it, all suspending extensions are required to delegate to either members of `R` or other extensions to it. Since extensions can't delegate to each other indefinitely (the program would not terminate), this guarantees that all suspensions happen through calling members of `R` that the author of the library can fully control.

This is relevant in the _rare_ cases when every suspension is handled in a special way in the library. For example, when implementing generators through the [`buildSequence()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/build-sequence.html) function described [below](#generators-api-in-kotlincoroutines), we need to make sure that any suspending call in the coroutine ends up calling either `yield()` or `yieldAll()` and not any other function. This is why [`SequenceBuilder`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-sequence-builder/index.html) is annotated with `@RestrictsSuspension`: -->

Расширяющие функции (и анонимные функции) также могут быть маркированы как `suspend`, подобно и всем остальным (регулярным) функциям. Это позволяет создавать [DSL](https://ru.wikipedia.org/wiki/Предметно-ориентированный_язык) и другие API, которые пользователь может расширять. В некоторых случаях автору библиотеки необходимо запретить пользователю добавлять *новые пути* приостановки сопрограммы.

Чтобы осуществить это, можно использовать аннотацию [`@RestrictsSuspension`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-restricts-suspension/index.html). Когда целевой класс или интерфейс `R` аннотируется подобным образом, все расширения приостановки должны делегироваться либо из членов `R`, либо из других его расширений. Поскольку расширения не могут делегировать друг друга до бесконечности (иначе программа никогда не завершится), гарантируется, что все приостановки пройдут посредством вызова члена `R`, так что автор библиотеки может полностью их контролировать.

Это актуально в тех _редких_ случаях, когда каждая приостановка обрабатывается специальным образом в библиотеке. Например, при реализации генераторов через [`buildSequence()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/build-sequence.html) функцию, описанную [ниже](#generators-api-in-kotlincoroutines), мы должны быть уверены, что любой приостанавливаемый вызовов в сопрограмме завершается вызовом либо `yield()`, либо `yieldAll()`, а не какой-либо другой функции. Именно по этой причине [`SequenceBuilder`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-sequence-builder/index.html) аннотирована с @RestrictsSuspension:

```kotlin
@RestrictsSuspension
public abstract class SequenceBuilder<in T> {
    ...
}
```
 
<!-- See the sources [on Github](https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/src/kotlin/coroutines/experimental/SequenceBuilder.kt). -->
См. исходники [на Github](https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/src/kotlin/coroutines/experimental/SequenceBuilder.kt).


## Внутреннее функционирование сопрограмм
<!--We are not trying here to give a complete explanation of how coroutines work under the hood, but a rough sense of what's going on is rather important.

Coroutines are completely implemented through a compilation technique (no support from the VM or OS side is required), and suspension works through code transformation. Basically, every suspending function (optimizations may apply, but we'll not go into this here) is transformed to a state machine where states correspond to suspending calls. Right before a suspension, the next state is stored in a field of a compiler-generated class along with relevant local variables, etc. Upon resumption of that coroutine, local variables are restored and the state machine proceeds from the state right after suspension.
     
A suspended coroutine can be stored and passed around as an object that keeps its suspended state and locals. The type of such objects is `Continuation`, and the overall code transformation described here corresponds to the classical [Continuation-passing style](https://en.wikipedia.org/wiki/Continuation-passing_style). Consequently, suspending functions take an extra parameter of type `Continuation` under the hood.

More details on how coroutines work may be found in [this design document](https://github.com/Kotlin/kotlin-coroutines/blob/master/kotlin-coroutines-informal.md). Similar descriptions of async/await in other languages (such as C# or ECMAScript 2016) are relevant here, although the language features they implement may not be as general as Kotlin coroutines. --> 

Мы не стремимся здесь дать полное объяснение того, как сопрограммы работают под капотом, но примерный смысл того, что происходит, очень важен.

Сопрограммы полностью реализованы с помощью технологии компиляции (поддержка от языковой виртуальной машины, среды исполнения, или операционной системы не требуется), а приостановка работает через преобразование кода. В принципе, каждая функция приостановки (оптимизации могут применяться, но мы не будем вдаваться в эти подробности здесь) преобразуется в конечный автомат, где состояния соответствуют приостановленным вызовам. Прямо перед приостановкой следующее состояние загружается в поле сгенерированного компилятором класса вместе с сопутствующими локальным переменными и т. д. При возобновлении сопрограммы локальные переменные и состояние восстанавливаются, и конечный автомат продолжает свою работу.

Приостановленную сопрограмму можно сохранять и передавать как объект, который хранит её приостановленное состояние и локальные переменные. Типом таких объектов является  Continuation, а преобразование кода, описанное здесь, соответствует классическому [Continuation-passing style](https://en.wikipedia.org/wiki/Continuation-passing_style). Следовательно, приостановливаемые функции принимают дополнительный параметр типа `Continuation` (сохранённое состояние) под капотом. 

Более детально о том, как работают сопрограммы, можно узнать в [этом проектном документе](https://github.com/Kotlin/kotlin-coroutines/blob/master/kotlin-coroutines-informal.md). Похожие описания `async / await` в других языках (таких как C# или ECMAScript 2016) актуальны и здесь, хотя особенности их языковых реализаций могут существенно отличаться от сопрограмм Kotlin.

<a name="experimental-status-of-coroutines"></a>
## Экспериментальный статус сопрограмм сменился на стабильный

<!--The design of coroutines is [experimental](compatibility.html#experimental-features), which means that it may be changed in the upcoming releases. When compiling coroutines in Kotlin 1.1, a warning is reported by default: *The feature "coroutines" is experimental*. To remove the warning, you need to specify an [opt-in flag](/docs/diagnostics/experimental-coroutines.html).

Due to its experimental status, the coroutine-related API in the Standard Library is put under the `kotlin.coroutines.experimental` package. When the design is finalized and the experimental status lifted, the final API will be moved to `kotlin.coroutines`, and the experimental package will be kept around (probably in a separate artifact) for backward compatibility. 
-->
<!--Дизайн сопрограмм носит статус [experimental](compatibility.html#experimental-features), из чего следует возможность его изменения в будущих релизах. При составлении сопрограммы в Kotlin 1.1 по умолчанию выводится предупреждение: *The feature "coroutines" is experimental*. Чтобы убрать предупреждение, необходимо указать опцию [opt-in flag](https://kotlinlang.org/docs/diagnostics/experimental-coroutines.html).-->

Из-за былого экспериментального статуса сопрограмм все связанные API были собраны в стандартной библиотеке как пакет `kotlin.coroutines.experimental`. Дизайн стабилизирован и его экспериментальный статус снят, окончательный API перенесен в пакет `kotlin.coroutines`, а экспериментальный пакет хранится в целях обеспечения обратной совместимости.

<!--
  **IMPORTANT NOTE**: We advise library authors to follow the same convention: add the "experimental" (e.g. `com.example.experimental`) suffix to your packages exposing coroutine-based APIs so that your library remains binary compatible. When the final API is released, follow these steps:
 * copy all the APIs to `com.example` (without the experimental suffix),
 * keep the experimental package around for backward compatibility. 
 
This will minimize migration issues for your users. 
--> 

**Важное замечание**: мы рекомендовали авторам библиотек, начавшим использовать эксперементальные сопрограммы следовать той же конвенции: добавить к названию суффикс «экспериментальный» (например, `com.example.experimental`), указывающий, какой там используется сопрограммно совместимый API. Таким образом ваша библиотека сохранит бинарную совместимость. Сейчас, когда вышел финальный API-интерфейс, выполните следующие действия:
 * скопируйте все API в `com.example` (без experimental суффикса);
 * сохраните экспериментальный вариант пакета для обратной совместимости.

Это позволит минимизировать проблемы миграции для пользователей.

Поддержка экспериментальной версии сопрограмм будет прекращена в Kotlin 1.4

<a name="standard-apis"></a>

<!--## Standard APIs-->
## Стандартные API 

<!--Coroutines come in three main ingredients: 
 - language support (i.s. suspending functions, as described above),
 - low-level core API in the Kotlin Standard Library,
 - high-level APIs that can be used directly in the user code.-->
Сопрограммы представлены в трёх их главных ингредиентах:
- языковая поддержка (функции остановки, как описывалось выше),
- низкоуровневый базовый API в стандартной библиотеке Kotlin,
- API высокого уровня, которые могут быть использованы непосредственно в пользовательском коде.

<!--### Low-level API: `kotlin.coroutines` -->

### Низкий уровень API: **kotlin.coroutines** 

Низкоуровневый API относительно мал и должен использоваться ТОЛЬКО для создания библиотек высокого уровня. Он содержит два главных пакета:  
<!--Low-level API is relatively small and should never be used other than for creating higher-level libraries. It consists of two main packages:--> 
- [`kotlin.coroutines`](/api/latest/jvm/stdlib/kotlin.coroutines/index.html) <!--with main types and primitives such as--> - главные типы и примитивы, такие как:
  - [`createCoroutine()`](/api/latest/jvm/stdlib/kotlin.coroutines/create-coroutine.html)
  - [`startCoroutine()`](/api/latest/jvm/stdlib/kotlin.coroutines/start-coroutine.html)
  - [`suspendCoroutine()`](/api/latest/jvm/stdlib/kotlin.coroutines/suspend-coroutine.html)
- [`kotlin.coroutines.intrinsics`](/api/latest/jvm/stdlib/kotlin.coroutines.intrinsics/index.html) <!--with even lower-level intrinsics such as--> - встроенные функции еще более низкого уровня, такие как [`suspendCoroutineOrReturn`](/api/latest/jvm/stdlib/kotlin.coroutines.intrinsics/suspend-coroutine-or-return.html)
 
Более детальная информация о использовании этих API может быть найдена [здесь](https://github.com/Kotlin/KEEP/blob/master/proposals/coroutines.md). <!-- More details about the usage of these APIs can be found [here]-->

<a name="generators-api-in-kotlincoroutines"></a>
<!--### Generators API in `kotlin.coroutines` -->
### API генераторов в **kotlin.coroutines** 

Это функции исключительно «уровня приложения» в `kotlin.coroutines`: 
<!--The only "application-level" functions in `kotlin.coroutines.experimental` are -->
- [`sequence()`](/api/latest/jvm/stdlib/kotlin.coroutines/sequence.html)
- [`iterator()`](/api/latest/jvm/stdlib/kotlin.coroutines/iterator.html)

<!--These are shipped within `kotlin-stdlib` because they are related to sequences. In fact, these functions (and we can limit ourselves to `buildSequence()` alone here) implement _generators_, i.e. provide a way to cheaply build a lazy sequence:-->
Они перенесены в рамки `kotlin-stdlib`, поскольку они относятся к последовательностям. По сути, эти функции (и мы можем ограничиться здесь рассмотрением только `sequence()`) реализуют генераторы, т. е. предоставляют лёгкую возможность построить ленивые последовательности:

```kotlin
import kotlin.coroutines.*

fun main(args: Array<String>) {
//sampleStart
    val fibonacciSeq = sequence {
        var a = 0
        var b = 1
        
        yield(1)
        
        while (true) {
            yield(a + b)
            
            val tmp = a + b
            a = b
            b = tmp
        }
    }
//sampleEnd

    // Print the first eight Fibonacci numbers
    println(fibonacciSeq.take(8).toList())
}
```

Это сгенерирует ленивую, потенциально бесконечную последовательность Фибоначчи, используя сопрограмму, которая дает последовательные числа Фибоначчи, вызывая функцию yield (). При итерировании такой последовательности на каждом шаге итератор выполняет следующую часть сопрограммы, которая генерирует следующее число. Таким образом, мы можем взять любой конечный список чисел из этой последовательности, например `fibonacciSeq.take(8).toList()`, дающий в результате `[1, 1, 2, 3, 5, 8, 13, 21]`. И сопрограммы достаточно дёшевы, чтобы сделать это практичным.

Чтобы продемонстрировать реальную ленивость такой последовательности, давайте напечатаем некоторые отладочные результаты изнутри вызова sequence():

<!--
This generates a lazy, potentially infinite Fibonacci sequence by creating a coroutine that yields consecutive Fibonacci numbers by calling the `yield()` function. When iterating over such a sequence every step of the iterator executes another portion of the coroutine that generates the next number. So, we can take any finite list of numbers out of this sequence, e.g. `fibonacciSeq.take(8).toList()` results in `[1, 1, 2, 3, 5, 8, 13, 21]`. And coroutines are cheap enough to make this practical. 
   
To demonstrate the real laziness of such a sequence, let's print some debug output inside a call to `buildSequence()`:-->
  
```kotlin
import kotlin.coroutines.*

fun main(args: Array<String>) {
//sampleStart
    val lazySeq = sequence {
        print("START ")
        for (i in 1..5) {
            yield(i)
            print("STEP ")
        }
        print("END")
    }

    // Print the first three elements of the sequence
    lazySeq.take(3).forEach { print("$it ") }
//sampleEnd
}
```
<!--
Run the code above to see that if we print the first three elements, the numbers are interleaved with the `STEP`s the generating loop. This means that the computation is lazy indeed. To print `1` we only execute until the first `yield(i)`, and print `START` along the way. Then, to print `2` we need to proceed to the next `yield(i)`, and this prints `STEP`. Same for `3`. And the next `STEP` never gets printed (as well as `END`), because we never requested further elements of the sequence.   
   
To yield a collection (or sequence) of values at once, the `yieldAll()` function is available:-->

Запустите приведенный выше код, чтобы убедиться, что если мы будем печатать первые три элемента, цифры чередуются со `STEP`-ами по ветвям цикла. Это означает, что вычисления действительно ленивые. Для печати `1` мы выполняем только до первого `yield(i)` и печатаем `START` по ходу дела. Затем, для печати `2`, нам необходимо переходить к следующему `yield(i)`, и здесь печатать `STEP`. То же самое и для `3`. И следующий `STEP` никогда не будет напечатан (точно так же как и `END`), поскольку мы никогда не запрашиваем дополнительных элементов последовательности.

Чтобы сразу породить всю коллекцию (или последовательность) значений, доступна функция `yieldAll()`:

```kotlin
import kotlin.coroutines.*

fun main(args: Array<String>) {
//sampleStart
    val lazySeq = sequence {
        yield(0)
        yieldAll(1..10) 
    }

    lazySeq.forEach { print("$it ") }
//sampleEnd
}
```

Функция `iterator()` во всём подобна sequence(), но только возвращает ленивый итератор.

Вы могли бы добавить собственную логику выполнения функции `sequence()`, написав приостанавливаемое расширение класса `SequenceScope` (что порождается аннотацией `@RestrictsSuspension`, как описывалось [выше](#restrictssuspension-annotation)):

<!--The `buildIterator()` works similarly to `buildSequence()`, but returns a lazy iterator.

One can add custom yielding logic to `buildSequence()` by writing suspending extensions to the `SequenceBuilder` class (that bears the `@RestrictsSuspension` annotation described [above](#restrictssuspension-annotation)): -->

```kotlin
import kotlin.coroutines.*

//sampleStart
suspend fun SequenceScope<Int>.yieldIfOdd(x: Int) {
    if (x % 2 != 0) yield(x)
}

val lazySeq = sequence {
    for (i in 1..10) yieldIfOdd(i)
}
//sampleEnd

fun main(args: Array<String>) {
    lazySeq.forEach { print("$it ") }
}
```

<!--### Other high-level APIs: `kotlinx.coroutines`-->

### Другие API высокого уровня: **kotlinx.coroutines** 

<!--
Only core APIs related to coroutines are available from the Kotlin Standard Library. This mostly consists of core primitives and interfaces that all coroutine-based libraries are likely to use.   

Most application-level APIs based on coroutines are released as a separate library: [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines). This library covers
 * Platform-agnostic asynchronous programming with `kotlinx-coroutines-core`
   * this module includes Go-like channels that support `select` and other convenient primitives
   * a comprehensive guide to this library is available [here](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md).
 * APIs based on `CompletableFuture` from JDK 8: `kotlinx-coroutines-jdk8`
 * Non-blocking IO (NIO) based on APIs from JDK 7 and higher: `kotlinx-coroutines-nio`
 * Support for Swing (`kotlinx-coroutines-swing`) and JavaFx (`kotlinx-coroutines-javafx`)
 * Support for RxJava: `kotlinx-coroutines-rx`
 
These libraries serve as both convenient APIs that make common tasks easy and end-to-end examples of how to build coroutine-based libraries. 
-->

Только базовые API, связанные с сопрограммами, доступны непосредственно из стандартной библиотеки Kotlin. Они преимущественно состоят из основных примитивов и интерфейсов, которые, вероятно, будут использоваться во всех библиотеках на основе сопрограмм.

Большинство API уровня приложений, основанные на сопрограммах, реализованы в отдельной библиотеке [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines). Эта библиотека содержит в себе:
 * Платформенно-зависимое асинхронное программирование с помощью `kotlinx-coroutines-core`:
   * этот модуль включает Go-подобные каналы, которые поддерживают `select` и другие удачные примитивы
   * исчерпывающее руководство по этой библиотеке доступно [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md). 
 * API, основанные на `CompletableFuture` из JDK 8: `kotlinx-coroutines-jdk8`
 * Неблокирующий ввод-вывод (NIO), основанный на API из JDK 7 и выше: `kotlinx-coroutines-nio`
 * Поддержка Swing (`kotlinx-coroutines-swing`) и JavaFx (`kotlinx-coroutines-javafx`)
 * Поддержка RxJava: `kotlinx-coroutines-rx`

Эти библиотеки являются удобными API, которые делают основные задачи простыми. Также они содержат законченные примеры того, как создавать библиотеки, построенные на сопрограммах.
