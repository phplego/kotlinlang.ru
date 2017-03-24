---
type: doc
layout: reference
category: "Syntax"
title: "Сопрограммы (coroutines)"
menuLabel: "Сопрограммы"
url: https://kotlinlang.ru/docs/reference/coroutines.html
---

<!-- # Coroutines -->
# Сопрограммы

<!-- > Coroutines are *experimental* in Kotlin 1.1. See details [below](#experimental-status-of-coroutines) 
 {:.note} -->

> Сопрограммы *экспериментальная* возможность Kotlin 1.1. [Подробнее](#экспериментальный-статус-сопрограмм) 
{:.note}

<!-- Some APIs initiate long-running operations (such as network IO, file IO, CPU- or GPU-intensive work, etc) and require the caller to block until they complete. Coroutines provide a way to avoid blocking a thread and replace it with a cheaper and more controllable operation: *suspension* of a coroutine.
 
 Coroutines simplify asynchronous programming by putting the complications into libraries. The logic of the program can be expressed *sequentially* in a coroutine, and the underlying library will figure out the asynchrony for us. The library can wrap relevant parts of the user code into callbacks, subscribe to relevant events, schedule execution on different threads (or even different machines!), and the code remains as simple as if it was sequentially executed.   
 
 Many asynchronous mechanisms available in other languages can be implemented as libraries using Kotlin coroutines. This includes [`async`/`await`](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#composing-suspending-functions) from C# and ECMAScript, [channels](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#channels) and [`select`](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#select-expression) from Go, and [generators/`yield`](#generators-api-in-kotlincoroutines) from C# and Python. See the description [below](#standard-apis) for libraries providing such constructs. -->

Некоторые API запускают долгие операции (такие как взаимодействие по сети, операции с файлом, операции нагружающие CPU или GPU, и т.д.) и обязывают заблокировать вызывающий поток, пока они не будут завершены. Сопрограммы предоставляют способ избежать блокирование потока и заменить блокировку на более дешёвую и управляемую операцию: *приостановка (suspension)* сопрограммы.

Сопрограммы упрощают асинхронное программирование скрывая все сложности в библиотеки. Логика в сопрограмме может быть изложена *последовательно*, а нижележащая библиотека решит для нас проблему с асинхронностью. Библиотека оборачивает соответствующие части пользовательского кода в коллбэки, подписывается на соответствующие события, планирует выполнение в разных потоках (или даже на разных машинах!), и код становится таким же простым как если бы он был выполнен последовательно.   

Используя сопрограммы kotlin, многие механизмы асинхронности, доступные в других языках, могут быть реализованы как отдельные библиотеки. Такие механизмы как [`async`/`await`](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#composing-suspending-functions) из C# и ECMAScript, [`channels`](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#channels) и [`select`](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#select-expression) из языка Go, и [`generators/yield`](#api-генераторов-в-kotlincoroutines) из C# и Python. [Подробнее](#стандартный-api) о библиотеках предоставляющих эти механизмы.

<!-- ## Blocking vs Suspending -->

## Блокирование против Приостановки

<!-- Basically, coroutines are computations that can be *suspended* without *blocking a thread*. Blocking threads is often expensive, especially under high load, because only a relatively small number of threads is practical to keep around, so blocking one of them leads to some important work being delayed.
  
 Coroutine suspension is almost free, on the other hand. No context switch or any other involvement of the OS is required. And on top of that, suspension can be controlled by a user library to a large extent: as library authors, we can decide what happens upon a suspension and optimize/log/intercept according to our needs.     
 
 Another difference is that coroutines can not be suspended at random instructions, but rather only at so called *suspension points*, which are calls to specially marked functions. -->

По своей сути, сопрограммы это вычисления которые могут быть *приостановлены* без выполнения *блокирования потока*. Блокирование потока, зачастую очень дорогое, особенно при высокой нагрузке, потому что практичнее держать только относительно небольшое кол-во потоков, в таком случае, блокирование одного из них приводит к тому, что какая ни будь важная работа будет отложена.
 
С другой стороны, приостановка сопрограммы почти бесплатная. Для приостановки не нужно переключения контекста или другого участия ОС. В добавок ко всему, приостановка, в значительной степени может управляться пользовательской библиотекой: как авторы библиотек, мы можем предпринять какие-либо действия при приостановке и оптимизировать/вывести в лог/перехватить в соответствии с нашими требованиями.     

Другое отличие это то, что сопрограммы не могут быть приостановлены в любом месте кода, а только в так называемых *точках приостановки*, которые требуют специально помеченных функций.  

<!-- ## Suspending functions -->

## Приостанавливающие функции

<!-- A suspension happens when we call a function marked with the special modifier, `suspend`: -->

Приостановка происходит когда мы вызываем функцию, помеченную специальным модификатором, `suspend`:

``` kotlin 
suspend fun doSomething(foo: Foo): Bar {
    ...
}
```

<!-- Such functions are called *suspending functions*, because calls to them may suspend a coroutine (the library can decide to proceed without suspension, if the result for the call in question is already available). Suspending functions can take parameters and return values in the same manner as regular functions, but they can only be called from coroutines and other suspending functions. In fact, to start a coroutine, there must be at least one suspending function, and it is usually anonymous (i.e. it is a suspending lambda). Let's look at an example, a simplified `async()` function (from the [`kotlinx.coroutines`](#generators-api-in-kotlincoroutines) library): -->

Такие функции называются *приостанавливающие функции*, потому что их вызов может приостановить сопрограмму (библиотека может решить действовать без приостановки, если результат вызова уже доступен). Приостанавливающие функции могут принимать параметры и возвращать значения так же как и обычные функции, но они могут быть вызваны только из сопрограмм и других приостанавливающих функций. На самом деле, для работы сопрограммы, нужна хотя бы одна приостанавливающая функция, и обычно это анонимная функция (т.е. приостанавливающая лямбда). Посмотрим на пример упрощённой функции `async()` (из библиотеки [`kotlinx.coroutines`](#api-генераторов-в-kotlincoroutines)):
    
``` kotlin
fun <T> async(block: suspend () -> T)
``` 

<!-- Here, `async()` is a regular function (not a suspending function), but the `block` parameter has a function type with the `suspend` modifier: `suspend () -> T`. So, when we pass a lambda to `async()`, it is a *suspending lambda*, and we can call a suspending function from it: -->

Здесь мы видим что `async()` это обычная функция (не приостанавливающая), но у параметра `block` указан функциональный тип с модификатором `suspend`: `suspend () -> T`. Таким образом, когда мы передаём лямбду в `async()`, она становится *приостанавливающей лямбдой*, и из неё мы можем вызвать приостанавливающую функцию:
   
``` kotlin
async {
    doSomething(foo)
    ...
}
```

<!-- To continue the analogy, `await()` can be a suspending function (hence also callable from within an `async {}` block) that suspends a coroutine until some computation is done and returns its result: -->

По аналогии, функция `await()` может быть приостанавливающей функцией (следовательно она также может быть вызвана из блока `async {}`) которая приостанавливает сопрограмму пока не будет выполнено вычисление, а после - возвращает результат:

``` kotlin
async {
    ...
    val result = computation.await()
    ...
}
```

<!-- More information on how actual `async/await` functions work in `kotlinx.coroutines` can be found [here](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#composing-suspending-functions). -->

Более подробная информация о работе функции `async/await` из `kotlinx.coroutines` смотрите [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#composing-suspending-functions).

<!-- Note that suspending functions `await()` and `doSomething()` can not be called from a regular function like `main()`: -->

Следует отметить, что приостанавливающие функции `await()` и `doSomething()` не могут быть вызваны из обычной функции, такой как `main()`:

``` kotlin
fun main(args: Array<String>) {
    doSomething() // ERROR: Suspending function called from a non-coroutine context 
}
```

<!-- Also note that suspending functions can be virtual, and when overriding them, the `suspend` modifier has to be specified: -->

Так же следует отметить, что приостанавливающие функции могут быть виртуальными, и мы можем переопределить их, но в этом случае модификатор `suspend` всё равно должен быть указан:
 
``` kotlin
interface Base {
    suspend fun foo()
}

class Derived: Base {
    override suspend fun foo() { ... }
}
``` 

<!-- ### `@RestrictsSuspension` annotation -->

### Аннотация `@RestrictsSuspension`
 
<!-- Extension functions (and lambdas) can also be marked `suspend`, just like regular ones. This enables creation of [DSLs](type-safe-builders.html) and other APIs that users can extend. In some cases the library author needs to prevent the user from adding *new ways* of suspending a coroutine. -->

Функции расширения (и лямбды), так же как и обычные функции, могут быть помечены модификатором `suspend`. Это позволяет создавать [DSLs](type-safe-builders.html) и другие API которые пользователь может расширить. В некоторых случаях автору библиотеки нужно запретить пользователю добавлять *другие способы* приостановки сопрограммы. 

<!-- To achieve this, the [`@RestrictsSuspension`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-restricts-suspension/index.html) annotation may be used. When a receiver class or interface `R` is annotated with it, all suspending extensions are required to delegate to either members of `R` or other extensions to it. Since extensions can't delegate to each other indefinitely (the program would not terminate), this guarantees that all suspensions happen through calling members of `R` that the author of the library can fully control. -->

Для достижения этой цели, может быть использована аннотация [`@RestrictsSuspension`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-restricts-suspension/index.html). Когда некоторый класс или интерфейс `R` помечен этой аннотацией, все приостанавливающие функции расширения, расширяющие `R` могут делегировать вызовы только к любому члену `R` или другим функциям расширения `R` *того же объекта*. Так как функции расширения не могут делегировать вызовы друг другу бесконечно (программа не будет завершена), это гарантирует что все приостановки произошли из членов `R`, автор которого, имеет полный контроль над ним.

<!-- This is relevant in the _rare_ cases when every suspension is handled in a special way in the library. For example, when implementing generators through the [`buildSequence()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/build-sequence.html) function described [below](#generators-api-in-kotlincoroutines), we need to make sure that any suspending call in the coroutine ends up calling either `yield()` or `yieldAll()` and not any other function. This is why [`SequenceBuilder`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-sequence-builder/index.html) is annotated with `@RestrictsSuspension`: -->

Это актуально в _редких_ случаях когда каждая приостановка в библиотеке обрабатывается специальным образом. Например, когда вы реализуете генератор через функцию [`buildSequence()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/build-sequence.html) описанную [ниже](#api-генераторов-в-kotlincoroutines), нам нужно быть уверенным в том, что любой приостанавливающий вызов в сопрограмме заканчивается *только* вызовом либо `yield()` либо `yieldAll()`. Вот почему [`SequenceBuilder`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-sequence-builder/index.html) помечен аннотацией `@RestrictsSuspension`:

``` kotlin
@RestrictsSuspension
public abstract class SequenceBuilder<in T> {
    ...
}
```
 
Смотри исходники [Github](https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/src/kotlin/coroutines/experimental/SequenceBuilder.kt).   

<!-- ## The inner workings of coroutines -->

## Объяснение работы сопрограмм

<!-- We are not trying here to give a complete explanation of how coroutines work under the hood, but a rough sense of what's going on is rather important. -->

Мы не будем давать полное объяснение как работают сопрограммы под капотом, но довольно важно иметь примерное понимание того, что там происходит.

<!-- Coroutines are completely implemented through a compilation technique (no support from the VM or OS side is required), and suspension works through code transformation. Basically, every suspending function (optimizations may apply, but we'll not go into this here) is transformed to a state machine where states correspond to suspending calls. Right before a suspension, the next state is stored in a field of a compiler-generated class along with relevant local variables, etc. Upon resumption of that coroutine, local variables are restored and the state machine proceeds from the state right after suspension. -->

Сопрограммы полностью реализованы через компиляцию (поддержка со стороны виртуальной машины или ОС не требуется), а сопрограммы работают через трансформацию кода. Фактически, каждая приостанавливающая функция (могут применяться оптимизации, но это выходит за рамки краткого описания) преобразуется в машину состояний (конечный автомат) где состояние соответствует приостановленным вызовам. Прямо перед приостановкой, следующее состояние сохраняется в поле сгенерированного компилятором класса вместе с соответствующими локальными переменными, и другой информацией. После возобновления сопрограммы, локальные переменные восстанавливаются и выполнение продолжается сразу после места приостановки.
     
<!-- A suspended coroutine can be stored and passed around as an object that keeps its suspended state and locals. The type of such objects is `Continuation`, and the overall code transformation described here corresponds to the classical [Continuation-passing style](https://en.wikipedia.org/wiki/Continuation-passing_style). Consequently, suspending functions take an extra parameter of type `Continuation` under the hood. -->

Приостановленная сопрограмма может быть сохранена и передана как объект которых хранит приостановленное состояние и локальные переменные. Тип таких объектов `Continuation`, и все трансформации кода, описанные здесь, соответствуют классическому [стилю передачи продолжения](https://en.wikipedia.org/wiki/Continuation-passing_style). Следовательно, внутри, приостанавливающие функции принимают дополнительный параметр типа `Continuation`.

<!-- More details on how coroutines work may be found in [this design document](https://github.com/Kotlin/kotlin-coroutines/blob/master/kotlin-coroutines-informal.md). Similar descriptions of async/await in other languages (such as C# or ECMAScript 2016) are relevant here, although the language features they implement may not be as general as Kotlin coroutines. -->

Более подробные детали работы сопрограмм можно найти [в этом документе](https://github.com/Kotlin/kotlin-coroutines/blob/master/kotlin-coroutines-informal.md). Аналогичное описание работы async/await есть и в других языках (C# или ECMAScript 2016), хотя реализации этих языков могут отличаться от реализации сопрограмм Kotlin. 

<!-- ## Experimental status of coroutines -->

## Экспериментальный статус сопрограмм

Дизайн сопрограмм [экспериментальный](compatibility.html#experimental-features), что означает что он может меняться в последующих релизах. В случае компиляции сопрограмм в Kotlin 1.1, компилятор выдаёт предупреждение: *The feature "coroutines" is experimental*. To remove the warning, you need to specify an [opt-in flag](/docs/diagnostics/experimental-coroutines.html).

<!-- Due to its experimental status, the coroutine-related API in the Standard Library is put under the `kotlin.coroutines.experimental` package. When the design is finalized and the experimental status lifted, the final API will be moved to `kotlin.coroutines`, and the experimental package will be kept around (probably in a separate artifact) for backward compatibility. -->

В связи с экспериментальным статусом сопрограмм, API Стандартной Библиотеки использующий сопрограммы лежит в пакете `kotlin.coroutines.experimental`. Когда дизайн будет окончательно одобрен и статус `экспериментальный` будет отменён, утвержденное API будет перемещено в пакет `kotlin.coroutines`, а пакет с экспериментальным API будет сохранён (скорее всего в отдельном артефакте) для обеспечения обратней совместимости. 

<!-- **IMPORTANT NOTE**: We advise library authors to follow the same convention: add the "experimental" (e.g. `com.example.experimental`) suffix to your packages exposing coroutine-based APIs so that your library remains binary compatible. When the final API is released, follow these steps:
  * copy all the APIs to `com.example` (without the experimental suffix),
  * keep the experimental package around for backward compatibility. -->

**ВАЖНОЕ ЗАМЕЧЕНИЕ**: Мы рекомендуем авторам библиотек следовать такому же соглашению: добавить суффикс "experimental" (e.g. `com.example.experimental`) библиотекам, предоставляющим API основанное на сопрограммах, таким образом ваша библиотека сохранит бинарную совместимость. Когда утвержденное API будет выпущено, выполните следующие шаги:
 * скопируйте всё API в пакет `com.example` (без суффикса "experimental"),
 * оставьте пакет "experimental" для обеспечения обратней совместимости.
 
<!-- This will minimize migration issues for your users. -->

Это минимизирует ошибки миграции для ваших пользователей. 

<!-- ## Standard APIs -->

## Стандартный API
 
<!-- Coroutines come in three main ingredients: 
  - language support (i.s. suspending functions, as described above),
  - low-level core API in the Kotlin Standard Library,
  - high-level APIs that can be used directly in the user code. -->

Сопрограммы состоят из трёх компонентов: 
 - уровень языка (приостанавливающие функции),
 - низко-уровневое API ядра в стандартной библиотеке Kotlin,
 - высоко-уровневые API которые могут быть использованы в пользовательском коде.
 
<!-- ### Low-level API: `kotlin.coroutines`  -->

### Низко-уровневое API: `kotlin.coroutines` 

<!-- Low-level API is relatively small and should never be used other than for creating higher-level libraries. It consists of two main packages: 
 - [`kotlin.coroutines.experimental`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/index.html) with main types and primitives such as
   - [`createCoroutine()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/create-coroutine.html)
   - [`startCoroutine()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/start-coroutine.html)
   - [`suspendCoroutine()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/suspend-coroutine.html)
 - [`kotlin.coroutines.experimental.intrinsics`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental.intrinsics/index.html) with even lower-level intrinsics such as [`suspendCoroutineOrReturn`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental.intrinsics/suspend-coroutine-or-return.html) -->

Низкоуровневый API относительно маленький и не должен быть использован иначе чем при создании высокоуровневых библиотек. Он состоит из двух главных пакетов: 
- [`kotlin.coroutines.experimental`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/index.html) с основными типами и примитивами, такими как
  - [`createCoroutine()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/create-coroutine.html)
  - [`startCoroutine()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/start-coroutine.html)
  - [`suspendCoroutine()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/suspend-coroutine.html)
- [`kotlin.coroutines.experimental.intrinsics`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental.intrinsics/index.html) с ещё более низко-уровневыми внутреннестями такими как [`suspendCoroutineOrReturn`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental.intrinsics/suspend-coroutine-or-return.html)
 
 <!-- More details about the usage of these APIs can be found [here](https://github.com/Kotlin/kotlin-coroutines/blob/master/kotlin-coroutines-informal.md). -->
 
 Подробные детали использования этого API [здесь](https://github.com/Kotlin/kotlin-coroutines/blob/master/kotlin-coroutines-informal.md).

<!-- ### Generators API in `kotlin.coroutines` -->

### API генераторов в `kotlin.coroutines`
  
<!-- The only "application-level" functions in `kotlin.coroutines.experimental` are
 - [`buildSequence()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/build-sequence.html)
 - [`buildIterator()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/build-iterator.html) -->

Единственные функции "уровня приложения" в `kotlin.coroutines.experimental` это
- [`buildSequence()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/build-sequence.html)
- [`buildIterator()`](/api/latest/jvm/stdlib/kotlin.coroutines.experimental/build-iterator.html)

<!-- These are shipped within `kotlin-stdlib` because they are related to sequences. In fact, these functions (and we can limit ourselves to `buildSequence()` alone here) implement _generators_, i.e. provide a way to cheaply build a lazy sequence: -->

Они поставляются без `kotlin-stdlib` потому что они относятся к последовательностям. На самом деле эти функции (можем ограничиться только `buildSequence()`) реализуют _генераторы_, т.е. предоставляют способ быстро построить ленивую последовательность:
 
<div class="sample" markdown="1" data-min-compiler-version="1.1"> 

``` kotlin
import kotlin.coroutines.experimental.*

fun main(args: Array<String>) {
//sampleStart
    val fibonacciSeq = buildSequence {
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

    // Print the first five Fibonacci numbers
    println(fibonacciSeq.take(8).toList())
}
```

</div>
  
<!-- This generates a lazy, potentially infinite Fibonacci sequence by creating a coroutine that yields consecutive Fibonacci numbers by calling the `yield()` function. When iterating over such a sequence every step of the iterator executes another portion of the coroutine that generates the next number. So, we can take any finite list of numbers out of this sequence, e.g. `fibonacciSeq.take(8).toList()` results in `[1, 1, 2, 3, 5, 8, 13, 21]`. And coroutines are cheap enough to make this practical. -->

Этот код генерирует ленивую, потенциально бесконечную последовательность Фибоначчи с помощью сопрограммы, которая отдаёт число из последовательности Фибоначчи вызовом функции `yield()`. Когда мы проходим по такого рода последовательности, для каждого шага итератор выполняет следующую порцию сопрограммы которая генерирует следующее число. Таким образом, мы можем получить любой конечный список чисел из этой последовательности, пример: `fibonacciSeq.take(8).toList()` results in `[1, 1, 2, 3, 5, 8, 13, 21]`. Сопрограммы достаточно дешёвые, что делает их практичными. 
   
<!-- To demonstrate the real laziness of such a sequence, let's print some debug output inside a call to `buildSequence()`: -->

Что бы продемонстрировать факт "ленивости" такой последовательности, давайте выведем в консоль какую ни будь информацию внутри `buildSequence()`:
  
<div class="sample" markdown="1" data-min-compiler-version="1.1"> 

``` kotlin
import kotlin.coroutines.experimental.*

fun main(args: Array<String>) {
//sampleStart
    val lazySeq = buildSequence {
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

</div>  
   
<!-- Run the code above to see that if we print the first three elements, the numbers are interleaved with the `STEP`s the generating loop. This means that the computation is lazy indeed. To print `1` we only execute until the first `yield(i)`, and print `START` along the way. Then, to print `2` we need to proceed to the next `yield(i)`, and this prints `STEP`. Same for `3`. And the next `STEP` never gets printed (as well as `END`), because we never requested further elements of the sequence. -->

Запустите код выше и убедитесь, что если мы выводим на консоль первые 3 элемента, номера чередуются с надписью `STEP` из цикла генерации. Это значит что вычисление действительно выполняется лениво. Что бы вывести `1` мы выполняем код до первого вызова `yield(i)`, предварительно выведя в консоль `START`. Далее, что бы вывести `2` нам нужно продолжить выполнение до следующего вызова `yield(i)`, и тут мы выводим `STEP`. Тоже самое для `3`. А следующий `STEP` никогда не будет выведен (так же как и `END`), потому что мы не запрашивали других элементов последовательности.   
   
<!-- To yield a collection (or sequence) of values at once, the `yieldAll()` function is available: -->

Что бы отдать коллекцию (или последовательность) значений за раз доступна функция `yieldAll()`:

<div class="sample" markdown="1" data-min-compiler-version="1.1"> 

``` kotlin
import kotlin.coroutines.experimental.*

fun main(args: Array<String>) {
//sampleStart
    val lazySeq = buildSequence {
        yield(0)
        yieldAll(1..10) 
    }

    lazySeq.forEach { print("$it ") }
//sampleEnd
}
```

</div>  

<!-- The `buildIterator()` works similarly to `buildSequence()`, but returns a lazy iterator. -->

Функция `buildIterator()` работает аналогично с `buildSequence()`, но возвращает ленивый итератор.

<!-- One can add custom yielding logic to `buildSequence()` by writing suspending extensions to the `SequenceBuilder` class (that bears the `@RestrictsSuspension` annotation described [above](#restrictssuspension-annotation)): -->

Вы можете добавить свою логику для функции `buildSequence()` путём написания приостанавливающих функций расширения для класса `SequenceBuilder` (Он помечен аннотацией `@RestrictsSuspension` описанной [выше](#aннотация-restrictssuspension)):

<div class="sample" markdown="1" data-min-compiler-version="1.1"> 

``` kotlin
import kotlin.coroutines.experimental.*

//sampleStart
suspend fun SequenceBuilder<Int>.yieldIfOdd(x: Int) {
    if (x % 2 != 0) yield(x)
}

val lazySeq = buildSequence {
    for (i in 1..10) yieldIfOdd(i)
}
//sampleEnd

fun main(args: Array<String>) {
    lazySeq.forEach { print("$it ") }
}
```

</div>  
  
<!-- ### Other high-level APIs: `kotlinx.coroutines` -->

### Другие высоко-уровневые API: `kotlinx.coroutines`

<!-- Only core APIs related to coroutines are available from the Kotlin Standard Library. This mostly consists of core primitives and interfaces that all coroutine-based libraries are likely to use. -->

Из Стандартной Библиотеки Kotlin доступны только ключевые API связанные с сопрограммами. Это API состоит в основном из примитивов и интерфейсов, и его используют все основанные на сопрограммах библиотеки.   

<!-- Most application-level APIs based on coroutines are released as a separate library: [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines). This library covers
  * Platform-agnostic asynchronous programming with `kotlinx-coroutines-core`
    * this module includes Go-like channels that support `select` and other convenient primitives
    * a comprehensive guide to this library is available [here](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md).
  * APIs based on `CompletableFuture` from JDK 8: `kotlinx-coroutines-jdk8`
  * Non-blocking IO (NIO) based on APIs from JDK 7 and higher: `kotlinx-coroutines-nio`
  * Support for Swing (`kotlinx-coroutines-swing`) and JavaFx (`kotlinx-coroutines-javafx`)
  * Support for RxJava: `kotlinx-coroutines-rx` -->

Большинство API уровня приложений основаны на сопрограммах выпущенных как отдельные библиотеки: [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines). Эта библиотека включает
 * Платформо-независимое асинхронное программирование с `kotlinx-coroutines-core`
   * этот модуль включает Go-подобные каналы (channels) которые поддерживают `select` и другие удобные примитивы.
   * Полное руководство к этой библиотеке доступно [здесь](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md).
 * API основанные на `CompletableFuture` из JDK 8: `kotlinx-coroutines-jdk8`
 * Неблокирующие IO (NIO) основанные на API из JDK 7 и выше: `kotlinx-coroutines-nio`
 * Поддержка Swing (`kotlinx-coroutines-swing`) и JavaFx (`kotlinx-coroutines-javafx`)
 * Поддержка RxJava: `kotlinx-coroutines-rx`
 
<!-- These libraries serve as both convenient APIs that make common tasks easy and end-to-end examples of how to build coroutine-based libraries.  -->

Эти библиотеки служат двум целям: удобным API который делает распространённые задачи простыми и как полноценные примеры построения библиотек, основанных на сопрограммах.