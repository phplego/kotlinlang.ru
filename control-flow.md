---
type: doc
layout: reference
category: "Syntax"
title: "Условия и циклы"
url: https://kotlinlang.ru/docs/control-flow.html
---

<!-- При переводе статьи оригинальная версия была от 05 August 2025 -->

<!-- Conditions and loops -->
# Условия и циклы

<!-- Kotlin gives you flexible tools to control your program's flow. Use `if`, `when`, and loops to define clear, expressive logic for your conditions. -->
Kotlin предоставляет гибкие инструменты для управления ходом выполнения программы. Используйте `if`, `when` и циклы,
чтобы описывать условия ясно и выразительно.

<a name="if-expression"></a>

<!-- ## If expression -->
## Условное выражение if

<!-- To use `if` in Kotlin, add the condition to check within parentheses `()` and the action to take if the result is true within curly braces `{}`. You can use `else` and `else if` for additional branches and checks. -->
Чтобы использовать `if` в Kotlin, укажите проверяемое условие в круглых скобках `()`, а действие, которое нужно
выполнить при истинном результате, — в фигурных скобках `{}`. Для дополнительных веток и проверок можно использовать
`else` и `else if`.

<!-- You can also write `if` as an expression, which lets you assign its returned value directly to a variable. In this form, an `else` branch is required. The `if` expression serves the same purpose as the ternary operator (`condition ? then : else`) found in other languages. -->
Также `if` можно записывать как выражение. Это позволяет сразу присвоить возвращаемое значение переменной. В такой форме
ветка `else` обязательна. Выражение `if` выполняет ту же роль, что и тернарный оператор
(`условие ? значение_если_истина : значение_если_ложь`) в других языках.

<!-- For example: -->
Например:

```kotlin
fun main() {
    val heightAlice = 160
    val heightBob = 175

    var taller = heightAlice
    if (heightAlice < heightBob) taller = heightBob

    // Использует ветку else
    if (heightAlice > heightBob) {
        taller = heightAlice
    } else {
        taller = heightBob
    }

    // Использует if как выражение
    taller = if (heightAlice > heightBob) heightAlice else heightBob

    // Использует else if как выражение
    val heightLimit = 150
    val heightOrLimit = if (heightLimit > heightAlice) heightLimit else if (heightAlice > heightBob) heightAlice else heightBob

    println("Больший рост: $taller")
    // Больший рост: 175
    println("Рост или ограничение: $heightOrLimit")
    // Рост или ограничение: 175
}
```

<!-- Each branch in an `if` expression can be a block, where the value of the last expression becomes the result: -->
Каждая ветка выражения `if` может быть блоком. В этом случае результатом становится значение последнего выражения в
блоке:

```kotlin
fun main() {
    val heightAlice = 160
    val heightBob = 175

    val taller = if (heightAlice > heightBob) {
        print("Выбираем Alice\n")
        heightAlice
    } else {
        print("Выбираем Bob\n")
        heightBob
    }

    println("Больший рост: $taller")
}
```

<a name="when-expression"></a>

<!-- ## When expressions and statements -->
## Выражения и операторы when

<!-- `when` is a conditional expression that runs code based on multiple possible values or conditions. It's similar to the `switch` statement in Java, C, and other languages. `when` evaluates its argument and compares the result against each branch in order until one branch condition is satisfied. For example: -->
`when` — это условная конструкция, которая выполняет код в зависимости от нескольких возможных значений или условий.
Она похожа на оператор `switch` в Java, C и других языках. `when` вычисляет свой аргумент и последовательно сравнивает
результат с каждой веткой, пока не будет выполнено одно из условий. Например:

```kotlin
fun main() {
    val userRole = "Editor"

    when (userRole) {
        "Viewer" -> print("Пользователь может только читать")
        "Editor" -> print("Пользователь может редактировать содержимое")
        else -> print("Роль пользователя не распознана")
    }
    // Пользователь может редактировать содержимое
}
```

<!-- You can use `when` either as an expression or a statement. As an expression, `when` returns a value you can use later in your code. As a statement, `when` completes an action without returning a result: -->
`when` можно использовать как **выражение** или как **оператор**. В виде выражения `when` возвращает значение, которое
можно использовать дальше в коде. В виде оператора `when` выполняет действие и не возвращает результат:

Выражение:

```kotlin
// Возвращает строку, присвоенную
// переменной text
val text = when (x) {
    1 -> "x == 1"
    2 -> "x == 2"
    else -> "x не равен ни 1, ни 2"
}
```

Оператор:

```kotlin
// Не возвращает результат, а вызывает
// функцию печати
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> print("x не равен ни 1, ни 2")
}
```

<!-- Secondly, you can use `when` with or without a subject. The behavior stays the same either way. Using a subject usually makes your code more readable and maintainable because it clearly shows what you're checking. -->
Кроме того, `when` можно использовать с объектом проверки или без него. Поведение остаётся тем же, но объект проверки
обычно делает код понятнее и проще в сопровождении, потому что явно показывает, что именно проверяется.

С объектом проверки `x`:

```kotlin
when (x) { ... }
```

Без объекта проверки:

```kotlin
when { ... }
```

<!-- How you use `when` determines whether you need to cover all possible cases in your branches. Covering all possible cases is called being exhaustive. -->
От способа использования `when` зависит, нужно ли покрывать ветками все возможные случаи. Такое полное покрытие
называется исчерпывающим.

<!-- ### Statements -->
### Операторы

<!-- If you use `when` as a statement, you don't need to cover all possible cases. In this example, some cases aren't covered, so no branch is triggered. However, no error occurs: -->
Если `when` используется как оператор, покрывать все возможные случаи не нужно. В следующем примере некоторые случаи не
покрыты, поэтому ни одна ветка не выполняется, но ошибки при этом не возникает:

```kotlin
fun main() {
    val deliveryStatus = "OutForDelivery"

    when (deliveryStatus) {
        // Покрыты не все случаи
        "Pending" -> print("Ваш заказ готовится")
        "Shipped" -> print("Ваш заказ уже в пути")
    }
}
```

<!-- Just like with `if`, each branch can be a block, and its value is the value of the last expression in the block. -->
Как и в `if`, каждая ветка может быть блоком, а её значением является значение последнего выражения в этом блоке.

<!-- ### Expressions -->
### Выражения

<!-- If you use `when` as an expression, you must cover all possible cases. The value of the first matching branch becomes the value of the overall expression. If you don't cover all cases, the compiler throws an error. -->
Если `when` используется как выражение, необходимо покрыть все возможные случаи. Значение первой подходящей ветки
становится значением всего выражения. Если покрыты не все случаи, компилятор выдаёт ошибку.

<!-- If your `when` expression has a subject, you can use an `else` branch to make sure that all possible cases are covered, but it isn't mandatory. For example, if your subject is a `Boolean`, `enum` class, `sealed` class, or one of their nullable counterparts, you can cover all cases without an `else` branch: -->
Если у выражения `when` есть объект проверки, можно использовать ветку `else`, чтобы гарантировать покрытие всех
возможных случаев, но это не обязательно. Например, если объект проверки имеет тип `Boolean`, тип
[класса `enum`](enum-classes.html), тип [`sealed`-класса](sealed-classes.html) или один из их nullable-аналогов, можно
покрыть все случаи без ветки `else`:

```kotlin
import kotlin.random.Random

enum class Bit {
    ZERO, ONE
}

fun getRandomBit(): Bit {
    return if (Random.nextBoolean()) Bit.ONE else Bit.ZERO
}

fun main() {
    val numericValue = when (getRandomBit()) {
        // Ветка else не нужна, потому что покрыты все случаи
        Bit.ZERO -> 0
        Bit.ONE -> 1
    }

    println("Случайный бит как число: $numericValue")
    // Случайный бит как число: 0
}
```

<!-- To simplify `when` expressions and reduce repetition, try out context-sensitive resolution (currently in preview). This feature allows you to omit the type name when using enum entries or sealed class members in `when` expressions if the expected type is known. -->
> **Совет.** Чтобы упростить выражения `when` и сократить повторения, попробуйте контекстно-зависимое разрешение
> (сейчас доступно в предварительном режиме). Эта возможность позволяет опускать имя типа при использовании записей
> `enum` или членов `sealed`-класса в выражениях `when`, если ожидаемый тип известен.
>
> Подробнее см. в разделе [Preview of context-sensitive resolution](https://kotlinlang.org/docs/whatsnew22.html#preview-of-context-sensitive-resolution)
> и в соответствующем [KEEP-предложении](https://github.com/Kotlin/KEEP/blob/improved-resolution-expected-type/proposals/context-sensitive-resolution.md).

<!-- If your `when` expression doesn't have a subject, you must have an `else` branch or the compiler throws an error. The `else` branch is evaluated when none of the other branch conditions are satisfied: -->
Если у выражения `when` **нет** объекта проверки, ветка `else` обязательна, иначе компилятор выдаст ошибку. Ветка
`else` вычисляется, когда ни одно из условий других веток не выполнено:

```kotlin
fun main() {
    val localFileSize = 1200
    val remoteFileSize = 1200

    val message = when {
        localFileSize > remoteFileSize -> "Локальный файл больше удалённого"
        localFileSize < remoteFileSize -> "Локальный файл меньше удалённого"
        else -> "Локальный и удалённый файлы имеют одинаковый размер"
    }

    println(message)
    // Локальный и удалённый файлы имеют одинаковый размер
}
```

<!-- ### Other ways to use when -->
### Другие способы использования when

<!-- `when` expressions and statements offer different ways to simplify your code, handle multiple conditions, and perform type checks. -->
Выражения и операторы `when` предлагают разные способы упростить код, обработать несколько условий и выполнить проверки
типов.

<!-- Group multiple conditions into a single branch using commas: -->
Объединяйте несколько условий в одной ветке с помощью запятых:

```kotlin
fun main() {
    val ticketPriority = "High"

    when (ticketPriority) {
        "Low", "Medium" -> print("Стандартное время ответа")
        else -> print("Высокий приоритет обработки")
    }
}
```

<!-- Use expressions that evaluate to `true` or `false` as branch conditions: -->
Используйте в условиях веток выражения, которые вычисляются в `true` или `false`:

```kotlin
fun main() {
    val storedPin = "1234"
    val enteredPin = 1234

    when (enteredPin) {
        // Выражение
        storedPin.toInt() -> print("PIN-код верный")
        else -> print("Неверный PIN-код")
    }
}
```

<!-- Check whether a value is or isn't contained in a range or collection using the `in` or `!in` keywords: -->
Проверяйте, входит ли значение в [диапазон](ranges.html) или коллекцию, с помощью ключевых слов `in` и `!in`:

```kotlin
fun main() {
    val x = 7
    val validNumbers = setOf(15, 16, 17)

    when (x) {
        in 1..10 -> print("x входит в диапазон")
        in validNumbers -> print("x является допустимым")
        !in 10..20 -> print("x находится вне диапазона")
        else -> print("ни одно из условий выше не выполнено")
    }
}
```

<!-- Check a value's type using the `is` or `!is` keywords. Due to smart casts, you can access the member functions and properties of the type directly: -->
Проверяйте тип значения с помощью ключевых слов `is` и `!is`. Благодаря
[умным приведениям](typecasts.html#smart-casts) можно обращаться к функциям и свойствам этого типа напрямую:

```kotlin
fun hasPrefix(input: Any): Boolean = when (input) {
    is String -> input.startsWith("ID-")
    else -> false
}

fun main() {
    val testInput = "ID-98345"
    println(hasPrefix(testInput))
    // true
}
```

<!-- Use `when` instead of a traditional `if`-`else` `if` chain. Without a subject, the branch conditions are simply boolean expressions. The first branch with a `true` condition runs: -->
Используйте `when` вместо традиционной цепочки `if`-`else` `if`. Без объекта проверки условия веток являются простыми
логическими выражениями. Выполняется первая ветка, условие которой равно `true`:

```kotlin
fun Int.isOdd() = this % 2 != 0
fun Int.isEven() = this % 2 == 0

fun main() {
    val x = 5
    val y = 8

    when {
        x.isOdd() -> print("x нечётный")
        y.isEven() -> print("y чётный")
        else -> print("x + y нечётно")
    }
    // x нечётный
}
```

<!-- Finally, capture the subject in a variable by using the following syntax: -->
Наконец, объект проверки можно захватить в переменную с помощью следующего синтаксиса:

```kotlin
fun main() {
    val message = when (val input = "yes") {
        "yes" -> "Вы сказали yes"
        "no" -> "Вы сказали no"
        else -> "Нераспознанный ввод: $input"
    }

    println(message)
    // Вы сказали yes
}
```

<!-- The scope of a variable introduced as the subject is restricted to the body of the `when` expression or statement. -->
Область видимости переменной, объявленной как объект проверки, ограничена телом выражения или оператора `when`.

<!-- ### Guard conditions -->
### Guard conditions в when {id="guard-conditions-in-when-expressions"}

<!-- Guard conditions allow you to include more than one condition to the branches of a `when` expression or statement, making complex control flow more explicit and concise. You can use guard conditions with `when` as long as it has a subject. -->
Guard conditions позволяют включать в ветки выражения или оператора `when` более одного условия, делая сложный поток
управления более явным и кратким. Их можно использовать с `when`, если у него есть объект проверки.

<!-- Place a guard condition after the primary condition in the same branch, separated by `if`: -->
Размещайте guard condition после основного условия в той же ветке, отделяя его с помощью `if`:

```kotlin
sealed interface Animal {
    data class Cat(val mouseHunter: Boolean) : Animal
    data class Dog(val breed: String) : Animal
}

fun feedDog() = println("Кормим собаку")
fun feedCat() = println("Кормим кошку")

fun feedAnimal(animal: Animal) {
    when (animal) {
        // Ветка только с основным условием
        // Вызывает feedDog(), если animal — Dog
        is Animal.Dog -> feedDog()
        // Ветка с основным и дополнительным условием
        // Вызывает feedCat(), если animal — Cat и не ловит мышей
        is Animal.Cat if !animal.mouseHunter -> feedCat()
        // Печатает "Неизвестное животное", если ни одно из условий выше не подошло
        else -> println("Неизвестное животное")
    }
}

fun main() {
    val animals = listOf(
        Animal.Dog("Beagle"),
        Animal.Cat(mouseHunter = false),
        Animal.Cat(mouseHunter = true)
    )

    animals.forEach { feedAnimal(it) }
    // Кормим собаку
    // Кормим кошку
    // Неизвестное животное
}
```

<!-- You can't use guard conditions when you have multiple conditions separated by a comma. For example: -->
Guard conditions нельзя использовать, если в ветке несколько условий, разделённых запятой. Например:

```kotlin
0, 1 -> print("x == 0 или x == 1")
```

<!-- In a single `when` expression or statement, you can combine branches with and without guard conditions. The code in a branch with a guard condition runs only if both the primary condition and the guard condition evaluate to `true`. If the primary condition doesn't match, the guard condition isn't evaluated. -->
В одном выражении или операторе `when` можно сочетать ветки с guard conditions и без них. Код в ветке с guard condition
выполняется только в том случае, если и основное условие, и guard condition вычисляются в `true`. Если основное условие
не подходит, guard condition не вычисляется.

<!-- Since `when` statements don't need to cover all cases, using guard conditions in `when` statements without an `else` branch means that if no conditions match, no code is run. -->
Так как операторы `when` не обязаны покрывать все случаи, использование guard conditions в операторах `when` без ветки
`else` означает, что при отсутствии подходящих условий код не выполнится.

<!-- Unlike statements, `when` expressions must cover all cases. If you use guard conditions in `when` expressions without an `else` branch, the compiler requires you to handle every possible case to avoid runtime errors. -->
В отличие от операторов, выражения `when` должны покрывать все случаи. Если вы используете guard conditions в выражениях
`when` без ветки `else`, компилятор потребует обработать все возможные случаи, чтобы избежать ошибок во время выполнения.

<!-- Combine multiple guard conditions within a single branch using the boolean operators `&&` (AND) or `||` (OR). Use parentheses around the boolean expressions to avoid confusion: -->
Объединяйте несколько guard conditions в одной ветке с помощью логических операторов `&&` (AND) или `||` (OR).
Используйте скобки вокруг логических выражений, чтобы [избежать путаницы](coding-conventions.html#guard-conditions-in-when-expression):

```kotlin
when (animal) {
    is Animal.Cat if (!animal.mouseHunter && animal.hungry) -> feedCat()
}
```

<!-- Guard conditions also support `else if`: -->
Guard conditions также поддерживают `else if`:

```kotlin
when (animal) {
    // Проверяет, является ли animal собакой
    is Animal.Dog -> feedDog()
    // Guard condition, который проверяет, является ли animal кошкой, не ловящей мышей
    is Animal.Cat if !animal.mouseHunter -> feedCat()
    // Вызывает giveLettuce(), если ни одно из условий выше не подошло и animal.eatsPlants равно true
    else if animal.eatsPlants -> giveLettuce()
    // Печатает "Неизвестное животное", если ни одно из условий выше не подошло
    else -> println("Неизвестное животное")
}
```

<a name="for-loops"></a>

<!-- ## For loops -->
## Цикл for

<!-- Use the `for` loop to iterate through a collection, array, or range: -->
Используйте цикл `for`, чтобы проходить по [коллекции](collections-overview.html), [массиву](https://kotlinlang.org/docs/arrays.html) или
[диапазону](ranges.html):

```kotlin
for (item in collection) print(item)
```

<!-- The body of a `for` loop can be a block with curly braces `{}`. -->
Тело цикла `for` может быть блоком в фигурных скобках `{}`.

```kotlin
fun main() {
    val shoppingList = listOf("Молоко", "Бананы", "Хлеб")

    println("Купить:")
    for (item in shoppingList) {
        println("- $item")
    }
    // Купить:
    // - Молоко
    // - Бананы
    // - Хлеб
}
```

<!-- ### Ranges -->
### Диапазоны

<!-- To iterate over a range of numbers, use a range expression with `..` and `..<` operators: -->
Чтобы перебрать диапазон чисел, используйте выражение диапазона с операторами `..` и `..<`:

```kotlin
fun main() {
    println("Закрытый диапазон:")
    for (i in 1..6) {
        print(i)
    }
    // Закрытый диапазон:
    // 123456

    println("\nОткрытый диапазон:")
    for (i in 1..<6) {
        print(i)
    }
    // Открытый диапазон:
    // 12345

    println("\nОбратный порядок с шагом 2:")
    for (i in 6 downTo 0 step 2) {
        print(i)
    }
    // Обратный порядок с шагом 2:
    // 6420
}
```

<!-- ### Arrays -->
### Массивы

<!-- If you want to iterate through an array or a list with an index, you can use the `indices` property: -->
Если нужно пройти по массиву или списку с индексом, используйте свойство `indices`:

```kotlin
fun main() {
    val routineSteps = arrayOf("Проснуться", "Почистить зубы", "Сварить кофе")

    for (i in routineSteps.indices) {
        println(routineSteps[i])
    }
    // Проснуться
    // Почистить зубы
    // Сварить кофе
}
```

<!-- Alternatively, you can use the `.withIndex()` function from the standard library: -->
Также можно использовать функцию [`.withIndex()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/with-index.html)
из стандартной библиотеки:

```kotlin
fun main() {
    val routineSteps = arrayOf("Проснуться", "Почистить зубы", "Сварить кофе")

    for ((index, value) in routineSteps.withIndex()) {
        println("Шаг с индексом $index: \"$value\"")
    }
    // Шаг с индексом 0: "Проснуться"
    // Шаг с индексом 1: "Почистить зубы"
    // Шаг с индексом 2: "Сварить кофе"
}
```

<!-- ### Iterators -->
### Итераторы

<!-- The `for` loop iterates through anything that provides an iterator. Collections provide iterators by default, whereas ranges and arrays are compiled into index-based loops. -->
Цикл `for` перебирает всё, что предоставляет [итератор](iterators.html). Коллекции предоставляют итераторы по
умолчанию, а диапазоны и массивы компилируются в циклы, основанные на индексах.

<!-- You can create your own iterators by providing a member or extension function called `iterator()` that returns an `Iterator<>`. The `iterator()` function must have a `next()` function and a `hasNext()` function that returns a `Boolean`. -->
Можно создавать собственные итераторы, предоставляя функцию-член или функцию-расширение `iterator()`, которая возвращает
`Iterator<>`. У итератора, который возвращает `iterator()`, должны быть функция `next()` и функция `hasNext()`,
возвращающая `Boolean`.

<!-- The easiest way to create your own iterator for a class is to inherit from the `Iterable<T>` interface and override the `iterator()`, `next()`, and `hasNext()` functions that are already there. For example: -->
Самый простой способ создать собственный итератор для класса — унаследоваться от интерфейса
[`Iterable<T>`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/-iterable/) и переопределить уже
имеющиеся функции `iterator()`, `next()` и `hasNext()`. Например:

```kotlin
class Booklet(val totalPages: Int) : Iterable<Int> {
    override fun iterator(): Iterator<Int> {
        return object : Iterator<Int> {
            var current = 1

            override fun hasNext() = current <= totalPages
            override fun next() = current++
        }
    }
}

fun main() {
    val booklet = Booklet(3)
    for (page in booklet) {
        println("Читаем страницу $page")
    }
    // Читаем страницу 1
    // Читаем страницу 2
    // Читаем страницу 3
}
```

<!-- Learn more about interfaces and inheritance. -->
> Подробнее см. в разделах [Интерфейсы](interfaces.html) и [Наследование](inheritance.html).

<!-- Alternatively, you can create the functions from scratch. In this case, add the `operator` keyword to the functions: -->
Также можно создать функции с нуля. В этом случае добавьте к функциям ключевое слово `operator`:

```kotlin
class Booklet(val totalPages: Int) {
    operator fun iterator(): Iterator<Int> {
        return object {
            var current = 1

            operator fun hasNext() = current <= totalPages
            operator fun next() = current++
        }.let {
            object : Iterator<Int> {
                override fun hasNext() = it.hasNext()
                override fun next() = it.next()
            }
        }
    }
}

fun main() {
    val booklet = Booklet(3)
    for (page in booklet) {
        println("Читаем страницу $page")
    }
    // Читаем страницу 1
    // Читаем страницу 2
    // Читаем страницу 3
}
```

<a name="while-loops"></a>

<!-- ## While loops -->
## Цикл while

<!-- `while` and `do-while` loops run the code in their body continuously while the condition is satisfied. The difference between them is the condition checking time: -->
Циклы `while` и `do-while` непрерывно выполняют код в своём теле, пока условие истинно. Разница между ними заключается
во времени проверки условия:

<!-- * `while` checks the condition and, if it's satisfied, runs the code in its body and then returns to the condition check.
* `do-while` runs the code in its body and then checks the condition. If it's satisfied, the loop repeats. So, the body of `do-while` runs at least once, regardless of the condition. -->
* `while` проверяет условие и, если оно истинно, выполняет код в своём теле, а затем возвращается к проверке условия;
* `do-while` выполняет код в своём теле и только затем проверяет условие. Если оно истинно, цикл повторяется. Поэтому
  тело `do-while` выполняется как минимум один раз независимо от условия.

<!-- For a `while` loop, place the condition to check in parentheses `()` and the body within curly braces `{}`: -->
Для цикла `while` поместите проверяемое условие в круглые скобки `()`, а тело — в фигурные скобки `{}`:

```kotlin
fun main() {
    var carsInGarage = 0
    val maxCapacity = 3

    while (carsInGarage < maxCapacity) {
        println("Машина заехала. Машин в гараже: ${++carsInGarage}")
    }
    // Машина заехала. Машин в гараже: 1
    // Машина заехала. Машин в гараже: 2
    // Машина заехала. Машин в гараже: 3

    println("Гараж заполнен!")
    // Гараж заполнен!
}
```

<!-- For a `do-while` loop, place the body within curly braces `{}` first before the condition to check in parentheses `()`: -->
Для цикла `do-while` сначала поместите тело в фигурные скобки `{}`, а затем укажите проверяемое условие в круглых
скобках `()`:

```kotlin
import kotlin.random.Random

fun main() {
    var roll: Int

    do {
        roll = Random.nextInt(1, 7)
        println("Выпало $roll")
    } while (roll != 6)
    // Выпало 2
    // Выпало 6

    println("Выпала 6! Игра окончена.")
    // Выпала 6! Игра окончена.
}
```

<a name="break-and-continue-in-loops"></a>

<!-- ## Break and continue in loops -->
## Break и continue в циклах

<!-- Kotlin supports traditional `break` and `continue` operators in loops. See [Returns and jumps](returns.html). -->
Kotlin поддерживает привычные операторы `break` и `continue` в циклах. См. [Операторы перехода](returns.html).
