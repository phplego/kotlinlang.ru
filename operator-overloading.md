---
type: doc
layout: reference
title: "Перегрузка операторов"
category: "Syntax"
url: https://kotlinlang.ru/docs/reference/operator-overloading.html
---

<!-- # Operator overloading -->
# Перегрузка операторов
<!--
Kotlin allows us to provide implementations for a predefined set of operators on our types. These operators have fixed symbolic representation
(like `+` or `*`) and fixed [precedence](grammar.html#precedence). To implement an operator, we provide a [member function](functions.html#member-functions)
or an [extension function](extensions.html) with a fixed name, for the corresponding type, i.e. left-hand side type for binary operations and argument type for unary ones.
Functions that overload operators need to be marked with the `operator` modifier.

Further we describe the conventions that regulate operator overloading for different operators. -->

Язык Kotlin позволяет нам реализовывать предопределённый набор операторов для наших типов. Эти операторы имеют фиксированное символическое представление (вроде `+` или `*`) и фиксированные [приоритеты](grammar.html#precedence). Для реализации оператора мы предоставляем [функцию-член](functions.html#member-functions) или [функцию-расширение](extensions.html) с фиксированным именем и с соответствующим типом, т. е. левосторонним типом для бинарных операций или типом аргумента для унарных оперций. Функции, которые перегружают операторы, должны быть отмечены модификатором `operator`.

Далее мы опишем соглашения, которые регламентируют перегрузку операторов для разных типов операторов.

<!-- ## Unary operations -->
## Унарные операторы
### Унарные префиксные операторы 

<!-- ### Unary prefix operators -->

| Выражение | Транслируется в |
|------------|---------------|
| `+a` | `a.unaryPlus()` |
| `-a` | `a.unaryMinus()` |
| `!a` | `a.not()` |

<!--This table says that when the compiler processes, for example, an expression `+a`, it performs the following steps:-->

Эта таблица демонстрирует, что когда компилятор обрабатывает, к примеру, выражение `+a`, он оcуществляет следующие действия:
<!--
 Determines the type of `a`, let it be `T`.
 Looks up a function `unaryPlus()` with the `operator` modifier and no parameters for the receiver `T`, i.e. a member function or an extension function.
 If the function is absent or ambiguous, it is a compilation error.
 If the function is present and its return type is `R`, the expression `+a` has type `R`. 
 -->
 
* Определяется тип выражения `a`, пусть это будет `T`.
* Смотрится функция `unaryPlus()` с модификатором `operator` без параметров для приёмника типа `Т`, т. е. функция-член или функция расширения.
* Если функция отсутствует или неоднозначная, то это ошибка компиляции.
* Если функция присутствует и её возвращаемый тип есть `R`, выражение `+a` имеет Тип `R`.

<!--*Note* that these operations, as well as all the others, are optimized for [Basic types](basic-types.html) and do not introduce overhead of function calls for them. -->
*Примечание*: эти операции, как и все остальные, оптимизированы для [основных типов](basic-types.html) и не вносят накладных расходов на вызовы этих функций для них.

<!--As an example, here's how you can overload the unary minus operator:-->
Например, вы можете перегрузить оператор унарного минуса:

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.unaryMinus() = Point(-x, -y)

val point = Point(10, 20)
println(-point)  // выведет "(-10, -20)"
```

<!--### Increments and decrements -->
### Инкремент и декремент

| Выражение | Транслируется в |
|------------|---------------|
| `a++` | `a.inc()` + see below |
| `a--` | `a.dec()` + see below |

<!-- The `inc()` and `dec()` functions must return a value, which will be assigned to the variable on which the
`++` or `--` operation was used. They shouldn't mutate the object on which the `inc` or `dec` was invoked. -->
Функции `inc()` и `dec()` должны возвращать значение, которое будет присвоено переменной, к которой была применёна
операция `++` или `--` . Они не должны пытаться изменять сам объект, для которого `inc` или `dec` были вызваны.

<!--The compiler performs the following steps for resolution of an operator in the *postfix* form, e.g. `a++`:

* Determines the type of `a`, let it be `T`.
* Looks up a function `inc()` with the `operator` modifier and no parameters, applicable to the receiver of type `T`.
* Checks that the return type of the function is a subtype of `T`. -->
Компилятор осуществляет следующие шаги для разрешения операторов в *постфиксной* форме, например для `a++`:

* Определяется тип переменной `a`, пусть это будет `T`.
* Смотрится функция `inc()` с модификатором `operator` без параметров, применимая для приёмника типа `Т`.
* Проверяется, что возвращаемый тип такой функции является подтипом `T`. 

<!--The effect of computing the expression is:

* Store the initial value of `a` to a temporary storage `a0`,
* Assign the result of `a.inc()` to `a`,
* Return `a0` as a result of the expression. -->
Эффектом вычисления будет:

* Загружается инициализирующее значение `a` во временную переменную `a0`,
* Результат выполнения `a.inc()` сохраняется в `a`,
* Возвращается `a0` как результат вычисления выражения (т.е. значение до инкремента).

<!-- For `a--` the steps are completely analogous. -->
Для `a--` шаги выполнения полностью аналогичные.

<!--For the *prefix* forms `++a` and `--a` resolution works the same way, and the effect is:

* Assign the result of `a.inc()` to `a`,
* Return the new value of `a` as a result of the expression. -->
Для *префиксной* формы `++a` или `--a` разрешение работает подобно, но результатом будет:

* Присвоение результата вычисления `a.inc()` непосредственно `a`,
* Возвращается новое значение `a` как общий результат вычисления выражения.

<!-- ## Binary operations -->
## Бинарные операции
<a name="arithmetic"></a>

<!--### Arithmetic operators  -->
### Арифметические операции

| Выражение | Транслируется в |
| -----------|-------------- |
| `a + b` | `a.plus(b)` |
| `a - b` | `a.minus(b)` |
| `a * b` | `a.times(b)` |
| `a / b` | `a.div(b)` |
| `a % b` | `a.rem(b)`, `a.mod(b)` (устаревшее) |
| `a..b ` | `a.rangeTo(b)` |


<!--For the operations in this table, the compiler just resolves the expression in the *Translated to* column.-->
Для перечисленных в таблице операций компилятор всего лишь разрешает выражение из колонки *Транслируется в*.

<!--Note that the `rem` operator is supported since Kotlin 1.1. Kotlin 1.0 uses the `mod` operator, which is deprecated
in Kotlin 1.1.-->
Отметим, что операция `rem` поддерживается только начиная с Kotlin 1.1. Kotlin 1.0 использует только операцию `mod`, которая отмечена как устаревшая в Kotlin 1.1.

<!--#### Example-->
#### Пример

<!--Below is an example Counter class that starts at a given value and can be incremented using the overloaded `+` operator.-->
Ниже приведен пример класса Counter, начинающего счёт с заданного значения, которое может быть увеличено с помощью перегруженного оператора `+`.

```kotlin
data class Counter(val dayIndex: Int) {
    operator fun plus(increment: Int): Counter {
        return Counter(dayIndex + increment)
    }
}
```


<a name="in"></a>

<!--### 'In' operator -->
### Оператор in

| Выражение | Транслируется в |
| -----------|-------------- |
| `a in b` | `b.contains(a)` |
| `a !in b` | `!b.contains(a)` |

<!--For `in` and `!in` the procedure is the same, but the order of arguments is reversed.-->
Для операций `in` и `!in` используется одна и та же процедура, только возвращаемый результат инвертируется.

<a name="indexed"></a>

<!--### Indexed access operator-->
### Оператор доступа по индексу

| Выражение | Транслируется в |
| -------|-------------- |
| `a[i]`  | `a.get(i)` |
| `a[i, j]`  | `a.get(i, j)` |
| `a[i_1, ...,  i_n]`  | `a.get(i_1, ...,  i_n)` |
| `a[i] = b` | `a.set(i, b)` |
| `a[i, j] = b` | `a.set(i, j, b)` |
| `a[i_1, ...,  i_n] = b` | `a.set(i_1, ..., i_n, b)` |

<!--Square brackets are translated to calls to `get` and `set` with appropriate numbers of arguments.-->
Квадратные скобки транслируются в вызов `get` или `set` с соответствующим числом аргументов. 

<a name="invoke"></a>

<!--### Invoke operator-->
### Оператор вызова

| Выражение | Транслируется в |
|--------|---------------|
| `a()`  | `a.invoke()` |
| `a(i)`  | `a.invoke(i)` |
| `a(i, j)`  | `a.invoke(i, j)` |
| `a(i_1, ...,  i_n)`  | `a.invoke(i_1, ...,  i_n)` |

<!--Parentheses are translated to calls to `invoke` with appropriate number of arguments.-->
Оператор вызова (функции, метода) в круглых скобках транслируется в `invoke` с соответствующим числом аргументов. 

<a name="assignments"></a>

<!--### Augmented assignments-->
### Присвоения с накоплением

| Выражение | Транслируется в |
|------------|---------------|
| `a += b` | `a.plusAssign(b)` |
| `a -= b` | `a.minusAssign(b)` |
| `a *= b` | `a.timesAssign(b)` |
| `a /= b` | `a.divAssign(b)` |
| `a %= b` | `a.modAssign(b)` |

<!--For the assignment operations, e.g. `a += b`, the compiler performs the following steps:-->
Для присваивающих операций, таких как `a += b`, компилятор осуществляет следующие шаги:

<!--
 If the function from the right column is available
   If the corresponding binary function (i.e. `plus()` for `plusAssign()`) is available too, report error (ambiguity).
   Make sure its return type is `Unit`, and report an error otherwise.
   Generate code for `a.plusAssign(b)`
 Otherwise, try to generate code for `a = a + b` (this includes a type check: the type of `a + b` must be a subtype of `a`). 
-->

* Если функция из правой колонки таблицы доступна
  * Если соответствующая бинарная функция (т.е. `plus()` для `plusAssign()`) также доступна, то фиксируется ошибка  (неоднозначность).
  * Проверяется, что возвращаемое значение функции `Unit`, в противном случае фиксируется ошибка.
  * Генерируется код для `a.plusAssign(b)`
* В противном случае делается попытка сгенерировать код для `a = a + b` (при этом включается проверка типов: тип выражения `a + b` должен быть подтипом `a`).

<!--*Note*: assignments are *NOT* expressions in Kotlin.-->
*Отметим*: присвоение *НЕ ЯВЛЯЕТСЯ* выражением в Kotlin.

<a name="equals"></a>

<!--### Equality and inequality operators-->
### Операторы равенства и неравенства

| Выражение | Транслируется в |
|------------|---------------|
| `a == b` | `a?.equals(b) ?: (b === null)` |
| `a != b` | `!(a?.equals(b) ?: (b === null))` |

<!--*Note*: `===` and `!==` (identity checks) are not overloadable, so no conventions exist for them-->
*Отметим*: операции `===` и `!==` (проверка идентичности) являются неперегружаемыми, поэтому не приводятся никакие соглашения для них.

<!--The `==` operation is special: it is translated to a complex expression that screens for `null`'s.
`null == null` is always true, and `x == null` for a non-null `x` is always false and won't invoke `x.equals()`.-->
Операция `==` имеет специальный смысл: она транслируется в составное выражение, в котором экранируются значения `null`.
`null == null` - это всегда истина, а `x == null` для ненулевых `x` - всегда ложь, и не должно расширяться в `x.equals()`.

<a name="comparison"></a>

<!--### Comparison operators-->
### Операторы сравнений

| Выражение | Транслируется в |
|--------|---------------|
| `a > b`  | `a.compareTo(b) > 0` |
| `a < b`  | `a.compareTo(b) < 0` |
| `a >= b` | `a.compareTo(b) >= 0` |
| `a <= b` | `a.compareTo(b) <= 0` |

<!--All comparisons are translated into calls to `compareTo`, that is required to return `Int`.-->
Все сравнения транслируются в вызовы `compareTo`, от которых требуется возврат значения типа `Int`.

<!--## Infix calls for named functions-->
## Инфиксные вызовы именованных функций

<!--We can simulate custom infix operations by using [infix function calls](functions.html#infix-notation).-->
Мы можем моделировать вручную инфиксные операции использованием [infix function calls](functions.html#infix-notation).

