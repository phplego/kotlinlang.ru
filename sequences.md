---
type: doc
layout: reference
category: Collections
title: "Последовательности (Sequences)"
url: https://kotlinlang.ru/docs/sequences.html
---

<a name="sequences"></a>
<!-- # Sequences -->
# Последовательности (Sequences)

<!-- Along with collections, the Kotlin standard library contains another container type – _sequences_ ([`Sequence<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/-sequence/index.html)).
Sequences offer the same functions as [`Iterable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/index.html)
but implement another approach to multi-step collection processing. -->
Стандартная библиотека Kotlin помимо коллекций содержит еще один тип контейнера - _последовательности_ ([`Sequence<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/-sequence/index.html)).
Последовательности предлагают те же функции, что и [`Iterable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/index.html), но реализуют другой подход к многоэтапной обработке коллекции.

<!-- When the processing of an `Iterable` includes multiple steps, they are executed eagerly: each processing step completes
and returns its result – an intermediate collection. The following step executes on this collection. In turn, multi-step
processing of sequences is executed lazily when possible: actual computing happens only when the result of the whole
processing chain is requested.  -->
Если обработка `Iterable` состоит из нескольких шагов, то они выполняются немедленно: при завершении обработки каждый шаг возвращает свой результат - промежуточную коллекцию. Следующий шаг выполняется для этой промежуточной коллекции.
В свою очередь, многоступенчатая обработка последовательностей по возможности выполняется "лениво": фактически вычисления происходят только тогда, когда запрашивается результат выполнения всех шагов.

<!-- The order of operations execution is different as well: `Sequence` performs all the processing steps one-by-one for every
single element. In turn, `Iterable` completes each step for the whole collection and then proceeds to the next step.  -->
Порядок выполнения операций также различается: `Sequence` выполняет все шаги один за другим для каждого отдельного элемента. Тогда как `Iterable` завершает каждый шаг для всей коллекции, а затем переходит к следующему шагу.

<!-- So, the sequences let you avoid building results of intermediate steps, therefore improving the performance of the whole
collection processing chain. However, the lazy nature of sequences adds some overhead which may be significant when
processing smaller collections or doing simpler computations. Hence, you should consider both `Sequence` and `Iterable`
and decide which one is better for your case. -->
Таким образом, последовательности позволяют избежать создания промежуточных результатов для каждого шага, тем самым повышая производительность всей цепочки вызовов. Однако "ленивый" характер последовательностей добавляет некоторые накладные расходы, которые могут быть значительными при обработке небольших коллекций или при выполнении более простых вычислений. Следовательно, вы должны рассмотреть, а затем самостоятельно решить, что вам подходит больше - `Sequence` или `Iterable`.


<a name="construct"></a>
<!-- ## Construct -->
## Создание последовательностей

<a name="from-elements"></a>
<!-- ### From elements -->
### С помощью элементов

<!-- To create a sequence, call the [`sequenceOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/sequence-of.html)
function listing the elements as its arguments. -->
Чтобы создать последовательность, вызовите функцию [`sequenceOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/sequence-of.html) и передайте ей элементы в качестве аргументов.

```kotlin
val numbersSequence = sequenceOf("four", "three", "two", "one")
```


<a name="from-an-iterable"></a>
<!-- ### From an Iterable -->
### С помощью `Iterable`

<!-- If you already have an `Iterable` object (such as a `List` or a `Set`), you can create a sequence from it by calling
[`asSequence()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/as-sequence.html). -->
Если у вас уже есть объект `Iterable` (например, `List` или `Set`), то вы можете создать из него последовательность, вызвав функцию [`asSequence()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/as-sequence.html).

```kotlin
val numbers = listOf("one", "two", "three", "four")
val numbersSequence = numbers.asSequence()
```


<a name="from-a-function"></a>
<!-- ### From a function -->
### С помощью функции

<!-- One more way to create a sequence is by building it with a function that calculates its elements.
To build a sequence based on a function, call [`generateSequence()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/generate-sequence.html)
with this function as an argument. Optionally, you can specify the first element as an explicit value or a result of a function call.
The sequence generation stops when the provided function returns `null`. So, the sequence in the example below is infinite. -->
Ещё один способ создать последовательность - использовать функцию, которая вычисляет элементы последовательности. Для этого вызовите [`generateSequence()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/generate-sequence.html) и в качестве аргумента передайте ей эту функцию. При желании вы можете указать первый элемент как явное значение или результат вызова функции.
Генерация последовательности останавливается, когда предоставленная функция возвращает `null`. Последовательность в приведённом ниже примере бесконечна.

```kotlin
fun main() {
    val oddNumbers = generateSequence(1) { it + 2 } // `it` - это предыдущее значение
    println(oddNumbers.take(5).toList()) // [1, 3, 5, 7, 9]
    //println(oddNumbers.count())     // ошибка: последовательность бесконечна
}
```

<!-- To create a finite sequence with `generateSequence()`, provide a function that returns `null` after the last element you need. -->
Для создания конечной последовательности передайте в `generateSequence()` такую функцию, которая после последнего нужного вам элемента вернёт `null`.

```kotlin
fun main() {
    val oddNumbersLessThan10 = generateSequence(1) { if (it < 8) it + 2 else null }
    println(oddNumbersLessThan10.count()) // 5
}
```


<a name="from-chunks"></a>
<!-- ### From chunks -->
### С помощью чанков (chunks)

<!-- Finally, there is a function that lets you produce sequence elements one by one or by chunks of arbitrary sizes – the
[`sequence()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/sequence.html) function.
This function takes a lambda expression containing calls of [`yield()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/-sequence-scope/yield.html)
and [`yieldAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/-sequence-scope/yield-all.html) functions.
They return an element to the sequence consumer and suspend the execution of `sequence()` until the next element is
requested by the consumer. `yield()` takes a single element as an argument; `yieldAll()` can take an `Iterable` object,
an `Iterator`, or another `Sequence`. A `Sequence` argument of `yieldAll()` can be infinite. However, such a call must be
the last: all subsequent calls will never be executed. -->
Наконец, есть функция [`sequence()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/sequence.html), которая позволяет создавать элементы последовательности один за другим или кусками (chunks) произвольного размера. Эта функция принимает лямбда-выражение, содержащее вызовы функций
[`yield()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/-sequence-scope/yield.html) и
[`yieldAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/-sequence-scope/yield-all.html).
Они возвращают элемент потребителю последовательности и приостанавливают выполнение `sequence()` до тех пор, пока потребитель не запросит следующий элемент. Функция `yield()` принимает в качестве аргумента один элемент; `yieldAll()` может принимать объект `Iterable`, `Iterator` или другую `Sequence`. Аргумент `Sequence`, переданный в `yieldAll()`, может быть бесконечным. Однако такой вызов должен быть последним, иначе все последующие вызовы никогда не будут выполнены.

```kotlin
fun main() {
    val oddNumbers = sequence {
        yield(1)
        yieldAll(listOf(3, 5))
        yieldAll(generateSequence(7) { it + 2 })
    }
    println(oddNumbers.take(5).toList()) // [1, 3, 5, 7, 9]
}
```


<a name="sequence-operations"></a>
<!-- ## Sequence operations -->
## Операции с последовательностями

<!-- The sequence operations can be classified into the following groups regarding their state requirements:
* _Stateless_ operations require no state and process each element independently, for example, [`map()`](collection-transformations.md#map) or [`filter()`](collection-filtering.md).
   Stateless operations can also require a small constant amount of state to process an element, for example, [`take()` or `drop()`](collection-parts.md).
* _Stateful_ operations require a significant amount of state, usually proportional to the number of elements in a sequence. -->
Операции с последовательностями можно разделить на следующие группы:
* _Stateless_ (без сохранения состояния) - операции, которым не требуется создавать промежуточное состояние. Они обрабатывают каждый элемент независимо, например, функции [`map()`](collection-transformations.html#map) и [`filter()`](collection-filtering.html). К этой же группе относятся операции, которым требуется создавать небольшое константное количество промежуточных состояний, например, [`take()` или `drop()`](collection-parts.html).
* _Stateful_ (с отслеживанием состояния) - данным операциям требуется создавать большое количество промежуточных состояний, которое, как правило, пропорционально количеству элементов в последовательности.

<!-- If a sequence operation returns another sequence, which is produced lazily, it's called _intermediate_.
Otherwise, the operation is _terminal_. Examples of terminal operations are [`toList()`](constructing-collections.md#copy)
or [`sum()`](collection-aggregate.md). Sequence elements can be retrieved only with terminal operations. -->
Если операция возвращает другую последовательность, которая создаётся "лениво", то такая операция называется _промежуточной_ (_intermediate_). В противном случае эта операция будет _терминальной_ (_terminal_). Примеры терминальных операций: [`toList()`](constructing-collections.md#copy) или [`sum()`](collection-aggregate.md). Элементы последовательности можно получить только с помощью терминальных операций.

<!-- Sequences can be iterated multiple times; however some sequence implementations might constrain themselves to be iterated
only once. That is mentioned specifically in their documentation. -->
Последовательности можно итерировать несколько раз; однако некоторые реализации последовательностей могут ограничивать итерацию до одного раза. Это специально упоминается в их документации.


<a name="sequence-processing-example"></a>
<!-- ## Sequence processing example -->
## Примеры работы с последовательностью

<!-- Let's take a look at the difference between `Iterable` and `Sequence` with an example. -->
Давайте на примере разберём разницу между `Iterable` и `Sequence`.


<a name="iterable"></a>
<!-- ### Iterable -->
### Iterable

<!-- Assume that you have a list of words. The code below filters the words longer than three characters and prints the lengths
of first four such words. -->
Предположим, у вас есть список слов. Отфильтруем слова длиной более трёх символов и выведем на печать длину первых четырёх таких слов.

```kotlin
fun main() {    
    val words = "The quick brown fox jumps over the lazy dog".split(" ")
    val lengthsList = words.filter { println("filter: $it"); it.length > 3 }
        .map { println("length: ${it.length}"); it.length }
        .take(4)

    println("Lengths of first 4 words longer than 3 chars:")
    println(lengthsList) // [5, 5, 5, 4]
}
```

<!-- When you run this code, you'll see that the `filter()` and `map()` functions are executed in the same order as they appear
in the code. First, you see `filter:` for all elements, then `length:` for the elements left after filtering, and then
the output of the two last lines.  -->
Попробуйте запустить этот код и увидите, что функции `filter()` и `map()` выполняются в том же порядке, в котором они появляются в коде. Сначала все слова фильтруются с помощью `filter()`, а затем для оставшихся слов вычисляется их длина с помощью `map()`.

<!-- This is how the list processing goes: -->
Визуально это выглядит следующим образом:

<img src="https://kotlinlang.org/docs/images/list-processing.png" width="700"/>


<a name="sequence"></a>
<!-- ### Sequence -->
### Sequence

<!-- Now let's write the same with sequences: -->
Теперь напишем такой же код, но с использованием последовательности:

```kotlin
fun main() {
    val words = "The quick brown fox jumps over the lazy dog".split(" ")
    //convert the List to a Sequence
    val wordsSequence = words.asSequence()

    val lengthsSequence = wordsSequence.filter { println("filter: $it"); it.length > 3 }
        .map { println("length: ${it.length}"); it.length }
        .take(4)

    println("Lengths of first 4 words longer than 3 chars")
    // terminal operation: obtaining the result as a List
    println(lengthsSequence.toList()) // [5, 5, 5, 4]
}
```

<!-- The output of this code shows that the `filter()` and `map()` functions are called only when building the result list.
So, you first see the line of text `“Lengths of..”` and then the sequence processing starts.
Note that for elements left after filtering, the map executes before filtering the next element.
When the result size reaches 4, the processing stops because it's the largest possible size that `take(4)` can return. -->
Если вы запустите этот код, то увидите, что функции `filter()` и `map()` вызываются в момент обращения к списку.
Сначала в лог будет выведена строка `“Lengths of..”` и только после неё начинается вычисление результата.
Обратите внимание и на порядок вызова функций. Если элемент соответствует условию фильтра, то функция `map()`, не дожидаясь окончания фильтрации, вычисляет длину слова.
Когда размер последовательности достигает 4, вычисление останавливается, потому что это максимально возможный размер, который может вернуть `take(4)`.

<!-- The sequence processing goes like this: -->
Визуально это выглядит следующим образом:

<img src="https://kotlinlang.org/docs/images/sequence-processing.png" width="700"/>

<!-- In this example, the sequence processing takes 18 steps instead of 23 steps for doing the same with lists. -->
В этом примере вычисление результата занимает 18 шагов, а в аналогичном примере с `Iterable` - 23 шага.
