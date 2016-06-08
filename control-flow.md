---
type: doc
layout: reference
category: "Syntax"
title: "Управляющие инструкции"
---

# Управляющие инструкции

## Условное выражение If

В языке Kotlin, ключевое слово **if**<!--keyword--> является выражение, т.е. оно возвращает значение.
Это позволяет отказаться от тернарного оператора (условие ? условие истинно : условие ложно), поскольку выражению **if**<!--keyword--> по силам его заменить.

``` kotlin
// обычное использование 
var max = a 
if (a < b) 
  max = b 
 
// с блоком else 
var max: Int
if (a > b) 
  max = a 
else 
  max = b 
 
// в виде выражения 
val max = if (a > b) a else b
```

"Ветви" выражения **if**<!--keyword--> могут содержать несколько строк кода, при этом при этом последнее выражение является значением блока:

``` kotlin
val max = if (a > b) { 
    print("возвращаем a") 
    a 
  } 
  else { 
    print("возвращаем b") 
    b 
  }
```

Если вы используете конструкцию **if**<!--keyword--> в качестве выражения (например, возвращая его значение или присваивая его переменной), то использование ветки `else` является обязательным.

См. [grammar for **if**<!--keyword-->](grammar.html#if).

## Условное выражение when

Ключевое слово **when**<!--keyword--> призвано заменить оператор switch, присутствующий в C-подобных языках. В простейшем виде его использование выглядит так

``` kotlin
when (x) {
  1 -> print("x == 1")
  2 -> print("x == 2")
  else -> { // обратите внимание на блок
    print("x is neither 1 nor 2")
  }
}
```

Выражение **when**<!--keyword--> последовательно сравнивает аргумент со всеми указанными значениями до удовлетворения одного из условий.
**when**<!--keyword--> можно использовать и как выражение, и как оператор. При использовании в виде выражения значение ветки, удовлетворяющей условию, становится значением всего выражения. При использовании в виде оператора, значения отдельных веток отбрасываются. (В точности как **if**<!--keyword-->: каждая ветвь может быть блоком и её значением является значение последнего выражения блока.)

Значение ветки **else**<!--keyword--> вычисляется в том случае, когда ни одно из условий в других ветках не удовлетворено.
Если **when**<!--keyword--> используется как выражение, то ветка **else**<!--keyword--> является обязательной, за исключением случаев, в которых компилятор может убедиться, что ветки покрывают все возможные значения. 


Если для нескольких значений выполняется одно и тоже действие, то условия можно перечислять в одной ветке через запятую:

``` kotlin
when (x) {
  0, 1 -> print("x == 0 or x == 1")
  else -> print("otherwise")
}
```

Помимо констант в ветках можно использовать произвольные выражения

``` kotlin
when (x) {
  parseInt(s) -> print("s encodes x")
  else -> print("s does not encode x")
}
```

Также можно проверять вхождение аргумента в [промежуток](ranges.html) **in**<!--keyword--> или **!in**<!--keyword--> или его наличие в коллекции:

``` kotlin
when (x) {
  in 1..10 -> print("x is in the range")
  in validNumbers -> print("x is valid")
  !in 10..20 -> print("x is outside the range")
  else -> print("none of the above")
}
```

Помимо этого Кotlin позволяет с момощью **is**<!--keyword--> or **!is**<!--keyword--> проверить тип аргумента. Обратите внимание, что благодаря [smart casts](typecasts.html#smart-casts), вы можете получить доступ к методам и свойствам типа без дополнительной проверки.

```kotlin
val hasPrefix = when(x) {
  is String -> x.startsWith("prefix")
  else -> false
}
```

**when**<!--keyword--> удобно использовать вместо цепочки условий вида **if**<!--keyword-->-**else**<!--keyword--> **if**<!--keyword-->. При отстутствии аргумента, условия работают как простые логические выражения, а тело ветки выполняется при его истинности:

``` kotlin
when {
  x.isOdd() -> print("x is odd")
  x.isEven() -> print("x is even")
  else -> print("x is funny")
}
```

См. [grammar for **when**<!--keyword-->](grammar.html#when).


## Циклы for

Цикл **for**<!--keyword--> обеспечивает перебор всех значений, поставляеміх итератором. The syntax is as follows:

``` kotlin
for (item in collection)
  print(item)
```

The body can be a block.

``` kotlin
for (item: Int in ints) {
  // ...
}
```

As mentioned before, **for**<!--keyword--> iterates through anything that provides an iterator, i.e.

* has a member- or extension-function `iterator()`, whose return type
  * has a member- or extension-function `next()`, and
  * has a member- or extension-function `hasNext()` that returns `Boolean`.

All of these three functions need to be marked as `operator`.

If you want to iterate through an array or a list with an index, you can do it this way:

``` kotlin
for (i in array.indices)
  print(array[i])
```

Note that this "iteration through a range" is compiled down to optimal implementation with no extra objects created.

Alternatively, you can use the `withIndex` library function:

``` kotlin
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```

See the [grammar for **for**<!--keyword-->](grammar.html#for).

## While Loops

**while**<!--keyword--> and **do**<!--keyword-->..**while**<!--keyword--> work as usual

``` kotlin
while (x > 0) {
  x--
}

do {
  val y = retrieveData()
} while (y != null) // y is visible here!
```

See the [grammar for **while**<!--keyword-->](grammar.html#while).

## Break and continue in loops

Kotlin supports traditional **break**<!--keyword--> and **continue**<!--keyword--> operators in loops. See [Returns and jumps](returns.html).


