---
type: doc
layout: reference
category: "Syntax"
title: "Условия и циклы"
url: https://kotlinlang.ru/docs/control-flow.html
---

<!-- При переводе статьи оригинальная версия была от 16 November 2021 -->

<!-- Conditions and loops -->
# Условия и циклы

<a name="if-expression"></a>
<!-- ## If expression -->
## Условное выражение if

<!-- In Kotlin, `if` is an expression: it returns a value.
Therefore, there is no ternary operator (`condition ? then : else`) because ordinary `if` works fine in this role. -->
В языке Kotlin `if` является выражением, т.е. оно возвращает значение.
Это позволяет отказаться от тернарного оператора (`условие ? условие истинно : условие ложно`), потому что обычному `if` вполне по силам его заменить.

```kotlin
// обычное использование
var max = a
if (a < b) max = b

// с блоком else
var max: Int
if (a > b) {
    max = a
} else {
    max = b
}

// в виде выражения
val max = if (a > b) a else b
```

<!-- Branches of `if` branches can be blocks. In this case, the last expression is the value of a block: -->
"Ветви" выражения `if` могут быть блоками, т.е. содержать несколько строк кода, при этом последнее выражение является значением блока:

```kotlin
val max = if (a > b) {
    print("возвращаем a")
    a
} else {
    print("возвращаем b")
    b
}
```

<!-- If you're using `if` as an expression, for example, for returning its value or
assigning it to a variable, the `else` branch is mandatory. -->
Если вы используете `if` в качестве выражения (например, возвращая его значение или присваивая его переменной), то использование ветки `else` является обязательным.

<a name="when-expression"></a>
<!-- ## When expression -->
## Условное выражение when

<!-- `when` defines a conditional expression with multiple branches. It is similar to the `switch` statement in C-like languages.
Its simple form looks like this. -->
`when` определяет условное выражение с несколькими "ветвями". Оно похоже на оператор `switch`, присутствующий в C-подобных языках.

```kotlin
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> { // обратите внимание на блок
        print("x не равен ни 1, ни 2")
    }
}
```

<!-- `when` matches its argument against all branches sequentially until some branch condition is satisfied. -->
`when` последовательно сравнивает свой аргумент со всеми указанными значениями, пока не выполнится какое-либо из условий ветвей.

<!-- `when` can be used either as an expression or as a statement. If it is used as an expression, the value
of the first matching branch becomes the value of the overall expression. If it is used as a statement, the values of
individual branches are ignored. Just like with `if`, each branch can be a block, and its value
is the value of the last expression in the block. -->
`when` можно использовать и как выражение, и как оператор. При использовании его в виде выражения значение первой ветки, удовлетворяющей условию,
становится значением всего выражения. При использовании в виде оператора значения отдельных веток отбрасываются.
В точности как `if`: каждая ветвь может быть блоком и её значением является значение последнего выражения блока.

<!-- The `else` branch is evaluated if none of the other branch conditions are satisfied. -->
Значение ветки `else` вычисляется в том случае, когда ни одно из условий в других ветках не удовлетворено.

<!-- If `when` is used as an _expression_, the `else` branch is mandatory,
unless the compiler can prove that all possible cases are covered with branch conditions,
for example, with [`enum` class](enum-classes.md) entries and [`sealed` class](sealed-classes.md) subtypes). -->
Если `when` используется как *выражение*, то ветка `else` является обязательной, за исключением случаев, в которых компилятор может убедиться,
что ветки покрывают все возможные значения. Так происходит, например с записями [класса `enum`](enum-classes.html)
и с подтипами [`sealed` (изолированных) классов](sealed-classes.html).

```kotlin
enum class Bit {
  ZERO, ONE
}
val numericValue = when (getRandomBit()) {
    Bit.ZERO -> 0
    Bit.ONE -> 1
    // 'else' не требуется, потому что все случаи учтены
}
```

<!-- In `when` _statements_, the `else` branch is mandatory in the following conditions: -->
В *операторах* `when` ветка `else` является обязательной в следующих условиях:

<!-- * `when` has a subject of an `Boolean`, [`enum`](enum-classes.md),
or [`sealed`](sealed-classes.md) type, or their nullable counterparts.
* branches of `when` don't cover all possible cases for this subject. -->
* `when` имеет объект типа `Boolean`, [`enum`](enum-classes.html), [`sealed`](sealed-classes.html)
или их nullable-аналоги;
* ветки `when` не охватывают все возможные случаи для этого объекта.

```kotlin
enum class Color {
    RED, GREEN, BLUE
}

when (getColor()) {
    Color.RED -> println("red")
    Color.GREEN -> println("green")
    Color.BLUE -> println("blue")
    // 'else' не требуется, потому что все случаи учтены
}

when (getColor()) {
    Color.RED -> println("red") // нет веток для GREEN и BLUE
    else -> println("not red") // 'else' обязателен
}
```

<!-- To define a common behavior for multiple cases, combine their conditions in a single line with a comma: -->
Если для нескольких значений выполняется одно и то же действие, то условия можно перечислять в одной ветке через запятую.

```kotlin
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```

<!-- You can use arbitrary expressions (not only constants) as branch conditions -->
Помимо констант в ветках можно использовать произвольные выражения.

```kotlin
when (x) {
    s.toInt() -> print("s encodes x")
    else -> print("s does not encode x")
}
```

<!-- You can also check a value for being `in` or `!in` a [range](ranges.md) or a collection: -->
Также можно проверять вхождение аргумента в [интервал](ranges.html) `in` или `!in` или его наличие в коллекции:

```kotlin
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
```

<!-- Another option is checking that a value `is` or `!is` of a particular type. Note that,
due to [smart casts](typecasts.md#smart-casts), you can access the methods and properties of the type without
any extra checks. -->
Помимо этого Кotlin позволяет с помощью `is` или `!is` проверить тип аргумента. Обратите внимание,
что благодаря [умным приведениям](typecasts.html#smart-casts) вы можете получить доступ к методам и свойствам типа без дополнительной проверки.

```kotlin
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}
```

<!-- `when` can also be used as a replacement for an `if`-`else` `if` chain.
If no argument is supplied, the branch conditions are simply boolean expressions, and a branch is executed when its condition is true: -->
`when` удобно использовать вместо цепочки условий вида `if`-`else` `if`.
При отсутствии  аргумента условия работают как простые логические выражения, а тело ветки выполняется при его истинности.

```kotlin
when {
    x.isOdd() -> print("x is odd")
    y.isEven() -> print("y is even")
    else -> print("x+y is odd")
}
```

<!-- You can capture *when* subject in a variable using following syntax: -->
Можно получать переменную внутри `when` условия по следующему синтаксису:

```kotlin
fun Request.getBody() =
        when (val response = executeRequest()) {
            is Success -> response.body
            is HttpError -> throw HttpException(response.status)
        }
```

<!-- The scope of variable introduced in *when* subject is restricted to the body of this *when*. -->
Такая переменная, объявленная внутри условия `when` может быть видна только внутри тела этого `when`.

<a name="for-loops"></a>
<!-- ## For loops -->
## Цикл for

<!-- The `for` loop iterates through anything that provides an iterator. This is equivalent to the `foreach` loop in languages like C#.
The syntax of `for` is the following: -->
Цикл `for` обеспечивает перебор всех значений, поставляемых итератором. Он эквивалентен циклу `foreach` в таких языках, как C#.

```kotlin
for (item in collection) print(item)
```

<!-- The body of `for` can be a block. -->
Телом цикла может быть блок кода.

```kotlin
for (item: Int in ints) {
    // ...
}
```

<!-- As mentioned before, `for` iterates through anything that provides an iterator. This means that it: -->
Как отмечено выше, цикл `for` позволяет проходить по всем элементам объекта, имеющего итератор, например:

<!-- * has a member or an extension function `iterator()` that returns `Iterator<>`:
  * has a member or an extension function `next()`
  * has a member or an extension function `hasNext()` that returns `Boolean`. -->
* обладающего внутренней или внешней функцией `iterator()`, возвращаемый тип которой `Iterator<>`:
  * обладает внутренней или внешней функцией `next()`
  * обладает внутренней или внешней функцией `hasNext()`, возвращающей `Boolean`.

<!-- All of these three functions need to be marked as `operator`. -->
Все три указанные функции должны быть объявлены как `operator`.

<!-- To iterate over a range of numbers, use a [range expression](ranges.md): -->
Чтобы перебрать диапазон чисел, используйте выражение диапазона:

```kotlin
for (i in 1..3) {
    println(i)
}
for (i in 6 downTo 0 step 2) {
    println(i)
}
```

<!-- A `for` loop over a range or an array is compiled to an index-based loop that does not create an iterator object. -->
Цикл `for` по диапазону или массиву компилируется в основанный на индексе цикл, который не создает объект итератора.

<!-- If you want to iterate through an array or a list with an index, you can do it this way: -->
Если при проходе по массиву или списку необходим порядковый номер элемента, используйте следующий подход:

```kotlin
for (i in array.indices) {
    println(array[i])
}
```

<!-- Alternatively, you can use the `withIndex` library function: -->
Также вы можете использовать библиотечную функцию `withIndex`.

```kotlin
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```

<a name="while-loops"></a>
<!-- ## While loops -->
## Цикл while

<!-- `while` and `do-while` loops execute their body continuously while their condition is satisfied.
The difference between them is the condition checking time: -->
Тело циклов `while` и `do-while` выполняется до тех пор, пока их условие выполняется.
Разница между ними заключается во времени проверки условия:

<!-- * `while` checks the condition and, if it's satisfied, executes the body and then returns to the condition check.
* `do-while` executes the body and then checks the condition. If it's satisfied, the loop repeats. So, the body of 
executes at least once regardless of the condition. -->
* `while` проверяет условие и, если оно истинно, выполняет тело, а затем возвращается к проверке условия;
* `do-while` выполняет тело и только затем проверяет условие. Если оно выполняется, цикл повторяется.
Таким образом, тело `do-while` выполняется по крайней мере один раз независимо от условия.

```kotlin
while (x > 0) {
    x--
}

do {
    val y = retrieveData()
} while (y != null) // y здесь доступно!
```

## Break и continue в циклах

<!-- Kotlin supports traditional `break` and `continue` operators in loops. See [Returns and jumps](returns.md). -->
Kotlin поддерживает привычные операторы `break` и `continue` в циклах. См. [Операторы перехода](returns.html).
