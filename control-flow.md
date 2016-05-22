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

The **else**<!--keyword--> branch is evaluated if none of the other branch conditions are satisfied.
If **when**<!--keyword--> is used as an expression, the **else**<!--keyword--> branch is mandatory,
unless the compiler can prove that all possible cases are covered with branch conditions.

If many cases should be handled in the same way, the branch conditions may be combined with a comma:

``` kotlin
when (x) {
  0, 1 -> print("x == 0 or x == 1")
  else -> print("otherwise")
}
```

We can use arbitrary expressions (not only constants) as branch conditions

``` kotlin
when (x) {
  parseInt(s) -> print("s encodes x")
  else -> print("s does not encode x")
}
```

We can also check a value for being **in**<!--keyword--> or **!in**<!--keyword--> a [range](ranges.html) or a collection:

``` kotlin
when (x) {
  in 1..10 -> print("x is in the range")
  in validNumbers -> print("x is valid")
  !in 10..20 -> print("x is outside the range")
  else -> print("none of the above")
}
```

Another possibility is to check that a value **is**<!--keyword--> or **!is**<!--keyword--> of a particular type. Note that,
due to [smart casts](typecasts.html#smart-casts), you can access the methods and properties of the type without
any extra checks.

```kotlin
val hasPrefix = when(x) {
  is String -> x.startsWith("prefix")
  else -> false
}
```

**when**<!--keyword--> can also be used as a replacement for an **if**<!--keyword-->-**else**<!--keyword--> **if**<!--keyword--> chain.
If no argument is supplied, the branch conditions are simply boolean expressions, and a branch is executed when its condition is true:

``` kotlin
when {
  x.isOdd() -> print("x is odd")
  x.isEven() -> print("x is even")
  else -> print("x is funny")
}
```

See the [grammar for **when**<!--keyword-->](grammar.html#when).


## For Loops

**for**<!--keyword--> loop iterates through anything that provides an iterator. The syntax is as follows:

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


