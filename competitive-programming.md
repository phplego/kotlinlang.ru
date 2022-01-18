---
type: doc
layout: reference
title: "Kotlin для спортивного программирования"
url: https://kotlinlang.ru/docs/competitive-programming.html
---

<!-- При переводе статьи оригинальная версия была от 14 December 2021 -->

<!-- # Kotlin for competitive programming -->
# Kotlin для спортивного программирования

<!-- This tutorial is designed both for competitive programmers that did not use Kotlin before and 
for Kotlin developers that did not participate in any competitive programming events before.
It assumes the corresponding programming skills. -->
Это руководство предназначено как для Kotlin-разработчиков, которые ранее не участвовали в соревнованиях, так и для
людей, имеющих опыт в спортивном программировании, но не использовавших ранее Kotlin. Так же это руководство
предполагает, что вы имеете соответствующие навыки программирования.

<!-- [Competitive programming](https://en.wikipedia.org/wiki/Competitive_programming)
is a mind sport where contestants write programs to solve precisely specified 
algorithmic problems within strict constraints. Problems can range from simple ones that can be solved by 
any software developer and require little code to get a correct solution, to complex ones that require knowledge of 
special algorithms, data structures, and a lot of practice. While not being specifically designed for competitive 
programming, Kotlin incidentally fits well in this domain, reducing the typical amount of boilerplate that a 
programmer needs to write and read while working with the code almost to the level offered by dynamically-typed 
scripting languages, while having tooling and performance of a statically-typed language. -->
[Спортивного программирование](https://en.wikipedia.org/wiki/Competitive_programming) - это интеллектуальный спорт, в
котором участники пишут программы для решения точно заданных алгоритмических задач в рамках строгих ограничений. Задачи
могут варьироваться от простых, которые может решить любой разработчик программного обеспечения и которые требуют
небольшого количества кода, до сложных, требующих знания специальных алгоритмов, структур данных и много опыта. Несмотря
на то, что  Kotlin специально не разрабатывался для спортивного программирования, он хорошо вписывается в эту область,
сокращая типичный объем шаблонов, которые программисту необходимо писать и читать при работе с кодом, почти до уровня,
предлагаемого динамически типизированными языками, при этом имея инструментарий и производительность статически
типизированного языка.

<!-- See [Get started with Kotlin/JVM](jvm-get-started.md) on how to set up development
environment for Kotlin. In competitive programming, a single project is usually created and each problem's solution is 
written in a single source file. -->
Подробнее о том, как настроить среду разработки для Kotlin, смотрите в разделе [Начало работы с Kotlin/JVM](jvm-get-started.html).
В спортивном программировании обычно создается один проект, и решение каждой задачи записывается в одном исходном файле.

<a name="simple-example-reachable-numbers-problem"></a>

<!-- ## Simple example: Reachable Numbers problem -->
## Простой пример: задача достижимых чисел

<!-- Let's take a look at a concrete example. -->
Давайте рассмотрим конкретный пример.

<!-- [Codeforces](https://codeforces.com/) 
Round 555 was held on April 26th for 3rd Division, which means it had problems fit for any developer to try. 
You can use [this link](https://codeforces.com/contest/1157) to read the problems. 
The simplest problem in the set is the 
[Problem A: Reachable Numbers](https://codeforces.com/contest/1157/problem/A).
It asks to implement a straightforward algorithm described in the problem statement. -->
[Codeforces](https://codeforces.com/) Round 555 состоялся 26 апреля для 3-го дивизиона, это значит, что в нем были
задачи, которые мог бы решить любой разработчик. Вы можете воспользоваться [этой ссылкой](https://codeforces.com/contest/1157),
чтобы ознакомиться с ними. Самая простая задача в наборе - это задача A. Достижимые числа. В ней предлагается
реализовать простой алгоритм, описанный в условии.

<!-- We'd start solving it by creating a Kotlin source file with an arbitrary name. `A.kt` will do well.
First, we need to implement a function specified in the problem statement as: -->
Решение этой задачи началось бы с создания исходного файла Kotlin с произвольным именем. `A.kt` подойдет. Во-первых, нам
нужно реализовать функцию, указанную в условии задачи:

<!-- Let's denote a function f(x) in such a way: we add 1 to x, then, while there is at least one trailing zero 
in the resulting number, we remove that zero. -->
Давайте объявим функцию f(x) следующим образом: добавим 1 к x, затем, пока у результата последняя цифра является нулем,
будем удалять этот ноль.

<!-- Kotlin is a pragmatic and unopinionated language, supporting both imperative and function programming styles without 
pushing the developer towards either one. We can implement the function `f` in functional style, using such Kotlin 
features as [tail recursion](functions.md#tail-recursive-functions): -->
Kotlin - прагматичный и непредвзятый язык, поддерживающий как императивный, так и функциональный стили программирования,
не подталкивает разработчика ни к одному из них. Вы можете реализовать функцию `f` в функциональном стиле, используя
одну из функций Kotlin - [хвостовую рекурсию](functions.html#tail-recursive-functions).

```kotlin
tailrec fun removeZeroes(x: Int): Int =
    if (x % 10 == 0) removeZeroes(x / 10) else x
    
fun f(x: Int) = removeZeroes(x + 1)
```

<!-- Alternatively, we can write an imperative implementation of the function `f` using the traditional 
[while loop](control-flow.md) and mutable variables that are denoted in Kotlin with 
[var](basic-syntax.md#variables): -->
Как вариант, вы можете написать императивную реализацию функции `f`, используя традиционный [цикл `while`](control-flow.html)
и изменяемые переменные, обознаемые с помощью [`var`](basic-syntax.html#variables).

```kotlin
fun f(x: Int): Int {
    var cur = x + 1
    while (cur % 10 == 0) cur /= 10
    return cur
}
```

<!-- Types in Kotlin are optional in many places due to pervasive use of type-inference, but every declaration still has 
a well-defined static type that is known at compilation. -->
Типы в Kotlin во многих местах необязательны из-за повсеместного использования вывода типов, но каждое объявление всё
равно имеет четко определенный статический тип, который известен при компиляции.

<!-- Now, all is left is to write the main function that reads the input and implements the rest of the algorithm that the problem 
statement asks for &mdash; to compute the number of different integers that are produced while repeatedly applying 
function `f` to the initial number `n` that is given in the standard input. -->
Теперь осталось написать основную функцию, которая считывает входные данные и реализует остальную часть алгоритма, о
котором говорится в условии задачи - вычислить количество различных целых чисел, которые получаются при многократном
применении функции `f` к исходному числу `n`.

<!-- By default, Kotlin runs on JVM and gives direct access to a rich and efficient collections library with 
general-purpose collections and data-structures like dynamically-sized arrays (`ArrayList`), 
hash-based maps and sets (`HashMap`/`HashSet`), tree-based ordered maps and sets (`TreeMap`/`TreeSet`), etc. 
Using a hash-set of integers to track values that were already reached while applying function `f`, 
the straightforward imperative version of a solution to the problem can be written as shown below: -->
По умолчанию Kotlin работает на JVM и предоставляет прямой доступ к богатой и эффективной библиотеке коллекций с коллекциями общего назначения и структурами данных, такими как массивы динамического размера (ArrayList), карты и наборы на основе хэша (HashMap /HashSet), упорядоченные карты и наборы на основе дерева (TreeMap/TreeSet) и т. Д. Используя хэш-набор целых чисел для отслеживания значений, которые уже были достигнуты при применении функции f, простая императивная версия решения проблемы может быть записана, как показано ниже:
По умолчанию Kotlin работает на JVM и предоставляет прямой доступ к богатой и эффективной библиотеке коллекций с
коллекциями и структурами данных общего назначения, такими как динамически изменяемые массивы (`ArrayList`),
ассоциативные списоки и наборы на основе хэшей (`HashMap`/`HashSet`), упорядоченные ассоциативные списоки и наборы на
основе деревьев (`TreeMap`/`TreeSet`) и тому подобное. Используя хэш-набор целых чисел для отслеживания значений,
которые уже были достигнуты при применении функции `f`, прямая императивная версия решения задачи может быть записана
так, как показано ниже.

```kotlin
fun main() {
    var n = readln().toInt() // считывание целого числа с входных данных
    val reached = HashSet<Int>() // изменяемый хэш-набор
    while (reached.add(n)) n = f(n) // повторяемая функция f
    println(reached.size) // вывод ответа
}
```

<!-- > The readln() function is available since [Kotlin 1.6.0](whatsnew16.md#new-readline-functions). -->
> Функция `readln()` доступна начиная с [версии Kotlin 1.6.0](whatsnew16.html#new-readline-functions).

<!-- There is no need to handle the case of misformatted input in competitive programming. An input format is always precisely
specified in competitive programming, and the actual input cannot deviate from the input specification in the problem
statement. That's why we're using Kotlin's [`readln()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/readln.html) function. It asserts that the input string is present and throws
an exception otherwise. Likewise, the [`String.toInt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-int.html)
function throws an exception if the input string is not an integer. -->
В спортивном программировании нет необходимости обрабатывать случай неправильно отформатированного ввода. Формат ввода
всегда точно задан и не может отклоняться от спецификации ввода в условии задачи. Поэтому можно использовать функцию
[`readln()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/readln.html). Она выбрасывает исключение, если
входная строка отсутствует. Аналогично, функция [`String.toInt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-int.html)
выбрасывает исключение, если входная строка не является целым числом.

<!-- All online competitive programming events allow the use of pre-written code, so you can define your own library of 
utility functions that are geared towards competitive programming to make your actual solution code somewhat easier 
to read and write. You would then use this code as a template for your solutions. For example, you can define 
the following helper functions for reading inputs in competitive programming: -->
Все онлайн-соревнования по спортивному программированию позволяют использовать готовый код, поэтому вы можете создать
собственную библиотеку полезных функций, предназначенных для соревновательного программирования, чтобы облегчить чтение
и написание кода решения. После чего вы сможете использовать этот код в качестве шаблона для своих решений. Например, вы
можете определить следующие вспомогательные функции для чтения входных данных в спортивном программировании:

```kotlin
private fun readInt() = readln().toInt()
private fun readStr() = readln().toString()
// и так далее для других типов, которые вы будете использовать в своих решениях
```

<!-- Note the use of `private` [visibility modifier](visibility-modifiers.md) here.
While the concept of visibility modifier is not relevant for competitive programming at all, 
it allows you to place multiple solution files based on the
same template without getting an error for conflicting public declarations in the same package. -->
Обратите внимание на использование здесь [модификатора видимости](visibility-modifiers.html) `private`. Хотя концепция
модификатора видимости вообще не имеет отношения к спортивному программированию, она позволяет разместить несколько
файлов решений на основе одного и того же шаблона, не получая ошибки из-за конфликтующих `public` объявлений в одном и
том же пакете.

<a name="functional-operators-example-long-number-problem"></a>

<!-- ## Functional operators example: Long Number problem -->
## Пример функциональных операторов: Задача длинного числа

<!-- For more complicated problems, Kotlin's extensive library of functional operations on collections comes in handy to 
minimize the boilerplate and turn the code into a linear top-to-bottom and left-to-right fluent data transformation 
pipeline. For example, the 
[Problem B: Long Number](https://codeforces.com/contest/1157/problem/B) problem 
takes a simple greedy algorithm to implement and it can be written using this style without a single mutable variable: -->
Для решения более сложных задач пригодится обширная Kotlin-библиотека функциональных операций над коллекциями,
позволяющая свести к минимуму шаблонность и превратить код в линейный конвейер преобразования данных сверху вниз и слева
направо. Например, для решения [задачи B. Длинное число](https://codeforces.com/contest/1157/problem/B) требуется
простой жадный алгоритм, и он может быть написан в этом стиле без единой изменяемой переменной.

```kotlin
fun main() {
    // чтение входных данных
    val n = readln().toInt()
    val s = readln()
    val fl = readln().split(" ").map { it.toInt() }
    // определение локальной функции f
    fun f(c: Char) = '0' + fl[c - '1']
    // жадное нахождние первого и последнего индекса
    val i = s.indexOfFirst { c -> f(c) > c }
        .takeIf { it >= 0 } ?: s.length
    val j = s.withIndex().indexOfFirst { (j, c) -> j > i && f(c) < c }
        .takeIf { it >= 0 } ?: s.length
    // компоновка и вывод ответа
    val ans =
        s.substring(0, i) +
        s.substring(i, j).map { c -> f(c) }.joinToString("") +
        s.substring(j)
    println(ans)
}
```

<!-- In this dense code, in addition to collection transformations, you can see such handy Kotlin features as local functions
and the [elvis operator](null-safety.md#elvis-operator) `?:`
that allow to express 
[idioms](idioms.md) like "take the value if it is positive or else use length" with a concise and readable 
expressions like `.takeIf { it >= 0 } ?: s.length`, yet it is perfectly fine with Kotlin to create additional mutable
variables and express the same code in imperative style, too. -->
В этом насыщенном коде, помимо преобразований коллекций, можно увидеть такие удобные возможности Kotlin, как локальные
функции и [элвис-оператор `?:`](null-safety.html#elvis-operator), которые позволяют выражать [идиомы](idioms.html) типа
"взять значение, если оно положительное, иначе использовать длину" с помощью лаконичных и читабельных выражений типа
`.takeIf { it >= 0 } ?: s.length`, однако в Kotlin вполне можно создать дополнительные изменяемые переменные и выразить
тот же код в императивном стиле.

<!-- To make reading the input in competitive programming tasks like this more concise, 
you can have the following list of helper input-reading functions: -->
Чтобы сделать чтение ввода в задачах спортивного программирования, подобных этой, более лаконичным, у вас может быть
следующий список вспомогательных функций чтения ввода:

```kotlin
private fun readInt() = readln().toInt() // одиночный int
private fun readStrings() = readln().split(" ") // список string
private fun readInts() = readStrings().map { it.toInt() } // список int
```

<!-- With these helpers, the part of code for reading input becomes simpler, closely following the input 
specification in the problem statement line by line: -->
С их помощью часть кода, предназначенная для чтения входных данных, становится проще, точно следуя спецификации входных
данных в условии задачи.

```kotlin
    // чтение входных данных
    val n = readInt()
    val s = readln()
    val fl = readInts()
```

<!-- Note that in competitive programming it is customary to give variables shorter names than it is 
typical in industrial programming practice, since the code is to be written just once and not supported thereafter. 
However, these names are usually still mnemonic &mdash; `a` for arrays,
`i`, `j`, etc for indices, `r`, and `c` for row and column numbers in tables, `x` and `y` for coordinates, etc.
It is easier to keep the same names for input data as it is given in the problem statement. 
However, more complex problems require more code which leads to using longer self-explanatory
variable and function names. -->
Обратите внимание, что в спортивном программировании принято давать переменным более короткие имена, по сравнению с
практикой промышленного программирования, поскольку код должен быть написан только один раз и впоследствии не
поддерживаться. Однако эти имена обычно все еще являются мнемоническими — `a` для массивов, `i`, `j` и т.д. для
индексов, `r` и `c` для номеров строк и столбцов в таблицах, `x` и `y` для координат и т.д. Проще сохранить те же имена
для входных данных, что и в условии задачи. Однако более сложные задачи требуют большего количества кода, что приводит к
использованию более длинных понятных имен переменных и функций.

<a name="more-tips-and-tricks"></a>

## More tips and tricks

<!-- Competitive programming problems often have input like this: -->
Проблемы конкурентного программирования часто имеют следующий ввод:

<!-- The first line of the input contains two integers `n` and `k` -->
В первой строке входных данных записано два целых числа `n` и `k`.

<!-- In Kotlin this line can be concisely parsed with the following statement using
[destructuring declaration](destructuring-declarations.md) 
from a list of integers: -->
В Kotlin эту строку можно кратко разобрать с помощью следующего оператора, используя [объявление деструктурирования](destructuring-declarations.html)
списка целых чисел.

```kotlin
val (n, k) = readInts() 
```

<!-- It might be temping to use JVM's `java.util.Scanner` class to parse less structured 
input formats. Kotlin is designed to interoperate well with JVM libraries, so that their use feels quite
natural in Kotlin. However, beware that `java.util.Scanner` is extremely slow. So slow, in fact, that parsing
10<sup>5</sup> or more integers with it might not fit into a typical 2 second time-limit, which a simple Kotlin's 
`split(" ").map { it.toInt() }` would handle.  -->
Может быть утомительно использовать класс JVM `java.util.Scanner` для разбора менее структурированных форматов ввода.
Kotlin спроектирован так, чтобы хорошо взаимодействовать с библиотеками JVM, поэтому их использование в Kotlin кажется
вполне естественным. Однако имейте в виду, что `java.util.Scanner` работает крайне медленно. Настолько медленно, что
парсинг 10<sup>5</sup> или более целых чисел с его помощью может не уложиться в типичный двухсекундный лимит времени, с
которым справится простой Kotlin конструкция `split(" ").map { it.toInt() }`.

<!-- Writing output in Kotlin is usually straightforward with 
[println(...)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/println.html) 
calls and using Kotlin's 
[string templates](basic-types.md#string-templates). However, care must be taken when output 
contains on order of 10<sup>5</sup> lines or more. Issuing so many `println` calls is too slow, since the output 
in Kotlin is automatically flushed after each line. 
A faster way to write many lines from an array or a list is using
[joinToString()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html) function
with `"\n"` as the separator, like this: -->
Написание вывода в Kotlin обычно не вызывает затруднений, если использовать вызовы [`println(...)`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/println.html)
и [строковые шаблоны](basic-types.html#string-templates) Kotlin. Однако следует быть осторожным, когда вывод содержит
порядка 10<sup>5</sup> строк или более. Выполнение такого количества вызовов `println` будет слишком медленным,
поскольку вывод в Kotlin автоматически очищается после каждой строки. Более быстрый способ записать много строк из
массива или списка - использовать функцию [`joinToString()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html)
с `"\n"` в качестве разделителя, например, так:

```kotlin
println(a.joinToString("\n")) // каждый элемент массива/списка отдельной строкой
```

<a name="learning-kotlin"></a>

<!-- ## Learning Kotlin -->
## Изучение Kotlin

<!-- Kotlin is easy to learn, especially for those who already know Java.
A short introduction to the basic syntax of Kotlin for software developers can be found directly in the
reference section of the web site starting from [basic syntax](basic-syntax.md). -->
Kotlin прост в изучении, особенно для тех, кто уже знает Java. Краткое введение в базовый синтаксис Kotlin для
разработчиков программного обеспечения можно найти непосредственно в статье [Основной синтаксис](basic-syntax.html).

<!-- IDEA has built-in 
[Java-to-Kotlin converter](https://www.jetbrains.com/help/idea/converting-a-java-file-to-kotlin-file.html). 
It can be used by people familiar with Java to learn the corresponding Kotlin syntactic constructions, but it
is not perfect and it is still worth familiarizing yourself with Kotlin and learning the 
[Kotlin idioms](idioms.md). -->
В IDEA есть встроенный [конвертер Java в Kotlin](https://www.jetbrains.com/help/idea/converting-a-java-file-to-kotlin-file.html).
Он может быть использован людьми, знакомыми с Java, для изучения соответствующих синтаксических конструкций Kotlin, но
он не совершенен, и все же вам самим стоит ознакомиться с Kotlin и изучить [идиомы Kotlin](idioms.html).

<!-- A great resource to study Kotlin syntax and API of the Kotlin standard library are
[Kotlin Koans](koans.md). -->
Отличным ресурсом для изучения синтаксиса Kotlin и API стандартной библиотеки Kotlin являются [Kotlin Koans](koans.html).
