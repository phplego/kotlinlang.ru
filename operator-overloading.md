---
type: doc
layout: reference
title: "Перегрузка операторов"
category: "Syntax"
url: https://kotlinlang.ru/docs/operator-overloading.html
---

<!-- При переводе статьи оригинальная версия была от 25 August 2021 -->

<!-- # Operator overloading -->
# Перегрузка операторов

<!-- Kotlin allows you to provide custom implementations for the predefined set of operators on types. These operators have
predefined symbolic representation (like `+` or `*`) and precedence. To implement an operator, provide a [member function](functions.md#member-functions)
or an [extension function](extensions.md) with a specific name for the corresponding type. This type becomes the left-hand side type
for binary operations and the argument type for the unary ones. -->
Kotlin позволяет реализовывать предопределённый набор операторов для ваших типов. Эти операторы имеют фиксированное
символическое представление (вроде `+` или `*`) и фиксированные приоритеты. Для реализации оператора предоставте
[функцию-член](functions.html#member-functions) или [функцию-расширение](extensions.html) с фиксированным именем и с
соответствующим типом, т. е. левосторонним типом для бинарных операций или типом аргумента для унарных оперций.

<!-- To overload an operator, mark the corresponding function with the `operator` modifier: -->
Функции, которые перегружают операторы, должны быть отмечены модификатором `operator`.

```kotlin
interface IndexedContainer {
    operator fun get(index: Int)
}
```

<!-- When [overriding](inheritance.md#overriding-methods) your operator overloads, you can omit `operator`: -->
При [переопределении](inheritance.html#overriding-methods) перегрузок оператора вы можете опускать `operator`.

```kotlin
class OrdersList: IndexedContainer {
    override fun get(index: Int) { /*...*/ }   
}
```

<a name="unary-operations"></a>

<!-- ## Unary operations -->
## Унарные операторы

<a name="unary-prefix-operators"></a>

<!-- ### Unary prefix operators -->
### Унарные префиксные операторы

<!-- ### Unary prefix operators -->

| Выражение | Транслируется в  |
|-----------|------------------|
| `+a`      | `a.unaryPlus()`  |
| `-a`      | `a.unaryMinus()` |
| `!a`      | `a.not()`        |

Под транслированием, понимается представление вашего кода после компиляции.

<!-- This table says that when the compiler processes, for example, an expression `+a`, it performs the following steps: -->
Эта таблица демонстрирует, что компилятор при обрабатывании, к примеру, выражения `+a`, осуществляет следующие действия:

<!-- * Determines the type of `a`, let it be `T`.
* Looks up a function `unaryPlus()` with the `operator` modifier and no parameters for the receiver `T`, that means a member 
function or an extension function.
* If the function is absent or ambiguous, it is a compilation error.
* If the function is present and its return type is `R`, the expression `+a` has type `R`. -->

* Определяет тип выражения `a`, пусть это будет `T`;
* Ищет функцию `unaryPlus()` с модификатором `operator` без параметров для приёмника типа `Т`, т. е. функцию-член или функцию-расширение;
* Если функция отсутствует или неоднозначная, возвращается ошибка компиляции;
* Если функция присутствует и `R` - её возвращаемый тип, выражение `+a` имеет Тип `R`.

<!-- > These operations, as well as all the others, are optimized for [basic types](basic-types.md) and do not introduce 
> overhead of function calls for them. -->
> Эти операции, как и все остальные, оптимизированы для [основных типов](basic-types.html) и не требуют дополнительных
> затрат на вызовы этих функций для них.

<!-- As an example, here's how you can overload the unary minus operator: -->
Например, вы можете перегрузить оператор унарного минуса:

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.unaryMinus() = Point(-x, -y)

fun main() {
   println(-point)  // выведет "Point(x=-10, y=-20)"
}
```

<a name="increments-and-decrements"></a>

<!-- ### Increments and decrements -->
### Инкремент и декремент

| Выражение | Транслируется в      |
|-----------|----------------------|
| `a++`     | `a.inc()` + см. ниже |
| `a--`     | `a.dec()` + см. ниже |

<!-- The `inc()` and `dec()` functions must return a value, which will be assigned to the variable on which the
`++` or `--` operation was used. They shouldn't mutate the object on which the `inc` or `dec` was invoked. -->
Функции `inc()` и `dec()` должны возвращать значение, которое будет присвоено переменной, к которой была применёна
операция `++` или `--` . Они не должны изменять сам объект, для которого были вызваны эти функции.

<!-- The compiler performs the following steps for resolution of an operator in the *postfix* form, for example `a++`: -->
Компилятор осуществляет следующие шаги, обрабатывая операторы в *постфиксной* форме, например `a++`:

<!-- * Determines the type of `a`, let it be `T`.
* Looks up a function `inc()` with the `operator` modifier and no parameters, applicable to the receiver of type `T`.
* Checks that the return type of the function is a subtype of `T`. -->

* Определяет тип переменной `a`, пусть это будет `T`;
* Ищет функцию `inc()` с модификатором `operator` без параметров, применимую для приёмника типа `Т`.
* Проверяет, что возвращаемый тип такой функции является подтипом `T`.

<!-- The effect of computing the expression is: -->
Результатами вычисления выражения является:

<!-- * Store the initial value of `a` to a temporary storage `a0`.
* Assign the result of `a0.inc()` to `a`.
* Return `a0` as the result of the expression. -->

* Сохранение инициализирующего значения `a` во временную переменную `a0`,
* Сохранение результата выполнения `a0.inc()` в `a`,
* Возвращение `a0` как результата вычисления выражения (т.е. значения до инкремента).

<!-- For `a--` the steps are completely analogous. -->
Для `a--` шаги выполнения полностью аналогичные.

<!-- For the *prefix* forms `++a` and `--a` resolution works the same way, and the effect is: -->
Для *префиксной* формы `++a` или `--a` действует также, но результатом будет:

<!-- * Assign the result of `a.inc()` to `a`.
* Return the new value of `a` as a result of the expression. -->

* Присвоение результата вычисления `a.inc()` непосредственно `a`,
* Возвращение нового значения `a` как результата вычисления выражения.

<a name="binary-operations"></a>

<!-- ## Binary operations -->
## Бинарные операции

<a name="arithmetic-operators"></a>

<!-- ### Arithmetic operators -->
### Арифметические операции

| Выражение | Транслируется в |
|-----------|-----------------|
| `a + b`   | `a.plus(b)`     |
| `a - b`   | `a.minus(b)`    |
| `a * b`   | `a.times(b)`    |
| `a / b`   | `a.div(b)`      |
| `a % b`   | `a.rem(b)`      |
| `a..b`    | `a.rangeTo(b)`  |

<!-- For the operations in this table, the compiler just resolves the expression in the *Translated to* column. -->
Для перечисленных в таблице операций компилятор всего лишь решает выражение из колонки *Транслируется в*.

<!-- Below is an example `Counter` class that starts at a given value and can be incremented using the overloaded `+` operator: -->
Ниже приведен пример класса `Counter` (счетчик), начинающего счёт с заданного значения, которое может быть увеличено
с помощью перегруженного оператора `+`:

```kotlin
data class Counter(val dayIndex: Int) {
    operator fun plus(increment: Int): Counter {
        return Counter(dayIndex + increment)
    }
}
```

<a name="in-operator"></a>

<!-- ### in operator -->
### Оператор in

| Выражение | Транслируется в  |
|-----------|------------------|
| `a in b`  | `b.contains(a)`  |
| `a !in b` | `!b.contains(a)` |

<!-- For `in` and `!in` the procedure is the same, but the order of arguments is reversed. -->
Для `in` и `!in` используется одна и та же процедура, только возвращаемый результат инвертируется.

<a name="indexed-access-operator"></a>

<!-- ### Indexed access operator -->
### Оператор доступа по индексу

| Выражение               | Транслируется в           |
|-------------------------|---------------------------|
| `a[i]`                  | `a.get(i)`                |
| `a[i, j]`               | `a.get(i, j)`             |
| `a[i_1, ...,  i_n]`     | `a.get(i_1, ...,  i_n)`   |
| `a[i] = b`              | `a.set(i, b)`             |
| `a[i, j] = b`           | `a.set(i, j, b)`          |
| `a[i_1, ...,  i_n] = b` | `a.set(i_1, ..., i_n, b)` |

<!-- Square brackets are translated to calls to `get` and `set` with appropriate numbers of arguments. -->
Квадратные скобки транслируется в вызов `get` или `set` с соответствующим числом аргументов.

<a name="invoke-operator"></a>

<!-- ### invoke operator -->
### Оператор вызова

| Выражение           | Транслируется в            |
|---------------------|----------------------------|
| `a()`               | `a.invoke()`               |
| `a(i)`              | `a.invoke(i)`              |
| `a(i, j)`           | `a.invoke(i, j)`           |
| `a(i_1, ...,  i_n)` | `a.invoke(i_1, ...,  i_n)` |

<!-- Parentheses are translated to calls to `invoke` with appropriate number of arguments. -->
Оператор вызова (функции, метода) в круглых скобках транслируется в `invoke` с соответствующим числом аргументов.

<a name="augmented-assignments"></a>

<!-- ### Augmented assignments -->
### Присвоения с накоплением

| Выражение  | Транслируется в    |
|------------|--------------------|
| `a += b`   | `a.plusAssign(b)`  |
| `a -= b`   | `a.minusAssign(b)` |
| `a *= b`   | `a.timesAssign(b)` |
| `a /= b`   | `a.divAssign(b)`   |
| `a %= b`   | `a.modAssign(b)`   |

<!-- For the assignment operations, for example `a += b`, the compiler performs the following steps: -->
Для присваивающих операций, таких как `a += b`, компилятор осуществляет следующие шаги:

<!-- * If the function from the right column is available:
  * If the corresponding binary function (that means `plus()` for `plusAssign()`) is available too, `a` is a mutable variable, and the return type of `plus` is a subtype of the type of `a`, report an error (ambiguity).
  * Make sure its return type is `Unit`, and report an error otherwise.
  * Generate code for `a.plusAssign(b)`.
* Otherwise, try to generate code for `a = a + b` (this includes a type check: the type of `a + b` must be a subtype of `a`). -->

* Если функция из правой колонки таблицы доступна:
  * Если соответствующая бинарная функция (например `plus()` для `plusAssign()`) также доступна, `a` - изменяемая переменная и возвращаемый тип `plus` является подтипом типа `a`, то фиксируется ошибка (неоднозначность);
  * Проверяется, что возвращаемое значение функции `Unit`, в противном случае фиксируется ошибка;
  * Генерируется код для `a.plusAssign(b)`.
* В противном случае делается попытка сгенерировать код для `a = a + b` (при этом включается проверка типов: тип выражения `a + b` должен быть подтипом `a`).

<!-- > Assignments are *NOT* expressions in Kotlin. -->
> Присвоение *НЕ ЯВЛЯЕТСЯ* выражением в Kotlin.

<a name="equality-and-inequality-operators"></a>

<!-- ### Equality and inequality operators -->
### Операторы равенства и неравенства

| Выражение | Транслируется в                   |
|-----------|-----------------------------------|
| `a == b`  | `a?.equals(b) ?: (b === null)`    |
| `a != b`  | `!(a?.equals(b) ?: (b === null))` |

<!-- These operators only work with the function [`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html), 
which can be overridden to provide custom equality check implementation. Any other function with the same name (like `equals(other: Foo)`) will not be called. -->
Эти операторы работают только с функцией [`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html),
которая может быть переопределена для обеспечения пользовательской реализации проверки равенства. Любая другая функция с
тем же именем (например, `equals(other: Foo)`) вызываться не будет.

<!-- > `===` and `!==` (identity checks) are not overloadable, so no conventions exist for them. -->
> Операции `===` и `!==` (проверка идентичности) являются неперегружаемыми, поэтому никакие соглашения для них не приводятся.

<!-- The `==` operation is special: it is translated to a complex expression that screens for `null`'s.
`null == null` is always true, and `x == null` for a non-null `x` is always false and won't invoke `x.equals()`. -->
Операция `==` имеет специальный смысл: она транслируется в составное выражение, в котором экранируются значения `null`.
`null == null` - это всегда истина, а `x == null` для non-null значений `x` - всегда ложь, и не будет расширяться в `x.equals()`.

<a name="comparison-operators"></a>

<!-- ### Comparison operators -->
### Операторы сравнений

| Выражение | Транслируется в       |
|-----------|-----------------------|
| `a > b`   | `a.compareTo(b) > 0`  |
| `a < b`   | `a.compareTo(b) < 0`  |
| `a >= b`  | `a.compareTo(b) >= 0` |
| `a <= b`  | `a.compareTo(b) <= 0` |

<!-- All comparisons are translated into calls to `compareTo`, that is required to return `Int`. -->
Все сравнения транслируются в вызовы `compareTo`, от которых требуется возврат значения типа `Int`.

<a name="property-delegation-operators"></a>

<!-- ### Property delegation operators -->
### Операторы делегирования свойств

<!-- `provideDelegate`, `getValue` and `setValue` operator functions are described
in [Delegated properties](delegated-properties.md). -->
Операторы `provideDelegate`, `getValue` и `setValue` описаны в [Делегированные свойства](delegated-properties.md).

<a name="infix-calls-for-named-functions"></a>

<!-- ## Infix calls for named functions -->
## Инфиксные вызовы именованных функций

<!-- You can simulate custom infix operations by using [infix function calls](functions.md#infix-notation). -->
Вы можете имитировать инфиксные операции, используя [инфиксную запись](functions.html#infix-notation).
