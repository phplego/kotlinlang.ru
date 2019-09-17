---
type: doc
layout: reference
category: "Syntax"
title: "Управляющие инструкции"
url: https://kotlinlang.ru/docs/reference/control-flow.html
---

# Управляющие инструкции

<a name="if-expression"></a>

## Условное выражение <b class="keyword">if</b>

В языке Kotlin ключевое слово <b class="keyword">if</b> является выражением, т.е. оно возвращает значение.
Это позволяет отказаться от тернарного оператора (условие ? условие истинно : условие ложно), поскольку выражению <b class="keyword">if</b> вполне по силам его заменить.

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

"Ветви" выражения <b class="keyword">if</b> могут содержать несколько строк кода, при этом последнее выражение является значением блока:

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

Если вы используете конструкцию <b class="keyword">if</b> в качестве выражения (например, возвращая его значение или присваивая его переменной), то использование ветки `else` является обязательным.

См. [использование <b class="keyword">if</b>](grammar.html#if).

<a name="when-expression"></a>

## Условное выражение <b class="keyword">when</b>

Ключевое слово <b class="keyword">when</b> призвано заменить оператор switch, присутствующий в C-подобных языках. В простейшем виде его использование выглядит так:

``` kotlin
when (x) {
  1 -> print("x == 1")
  2 -> print("x == 2")
  else -> { // обратите внимание на блок
    print("x is neither 1 nor 2")
  }
}
```

Выражение <b class="keyword">when</b> последовательно сравнивает аргумент со всеми указанными значениями до удовлетворения одного из условий.
<b class="keyword">when</b> можно использовать и как выражение, и как оператор. При использовании в виде выражения значение ветки, удовлетворяющей условию, становится значением всего выражения. При использовании в виде оператора значения отдельных веток отбрасываются. (В точности как <b class="keyword">if</b>: каждая ветвь может быть блоком и её значением является значение последнего выражения блока.)

Значение ветки <b class="keyword">else</b> вычисляется в том случае, когда ни одно из условий в других ветках не удовлетворено.
Если <b class="keyword">when</b> используется как выражение, то ветка <b class="keyword">else</b> является обязательной, за исключением случаев, в которых компилятор может убедиться, что ветки покрывают все возможные значения. 


Если для нескольких значений выполняется одно и то же действие, то условия можно перечислять в одной ветке через запятую:

``` kotlin
when (x) {
  0, 1 -> print("x == 0 or x == 1")
  else -> print("otherwise")
}
```

Помимо констант в ветках можно использовать произвольные выражения:

``` kotlin
when (x) {
  parseInt(s) -> print("s encodes x")
  else -> print("s does not encode x")
}
```

Также можно проверять вхождение аргумента в [интервал](ranges.html) <b class="keyword">in</b> или <b class="keyword">!in</b> или его наличие в коллекции:

``` kotlin
when (x) {
  in 1..10 -> print("x is in the range")
  in validNumbers -> print("x is valid")
  !in 10..20 -> print("x is outside the range")
  else -> print("none of the above")
}
```

Помимо этого Кotlin позволяет с помощью <b class="keyword">is</b> или <b class="keyword">!is</b> проверить тип аргумента. Обратите внимание, что благодаря [умным приведениям](typecasts.html#smart-casts) вы можете получить доступ к методам и свойствам типа без дополнительной проверки:

```kotlin
val hasPrefix = when(x) {
  is String -> x.startsWith("prefix")
  else -> false
}
```

<b class="keyword">when</b> удобно использовать вместо цепочки условий вида <b class="keyword">if</b>-<b class="keyword">else</b> <b class="keyword">if</b>. При отстутствии аргумента, условия работают как простые логические выражения, а тело ветки выполняется при его истинности:

``` kotlin
when {
  x.isOdd() -> print("x is odd")
  x.isEven() -> print("x is even")
  else -> print("x is funny")
}
```

См. [использование <b class="keyword">when</b>](grammar.html#when).

<a name="for-loops"></a>

## Циклы <b class="keyword">for</b>

Цикл <b class="keyword">for</b> обеспечивает перебор всех значений, поставляемых итератором. Для этого используется следующий синтаксис:

``` kotlin
for (item in collection)
  print(item)
```

Телом цикла может быть блок кода:

``` kotlin
for (item: Int in ints) {
  // ...
}
```

Как отмечено выше, цикл <b class="keyword">for</b> позволяет проходить по всем элементам объекта, имеющего итератор, например:

* обладающего внутренней или внешней функцией `iterator()`, возвращаемый тип которой
  * обладает внутренней или внешней функцией `next()`, и
  * обладает внутренней или внешней функцией `hasNext()`, возвращающей `Boolean`.

Все три указанные функции должны быть объявлены как `operator`.

Если при проходе по массиву или списку необходим порядковый номер элемента, используйте следующий подход:

``` kotlin
for (i in array.indices)
  print(array[i])
```

Обратите внимание, что данная "итерация по ряду" компилируется в более производительный код без создания дополнительных объектов.

Также вы можете использовать библиотечную функцию `withIndex`:

``` kotlin
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```

См. [использование <b class="keyword">for</b>](grammar.html#for).

<a name="while-loops"></a>

## Циклы <b class="keyword">while</b>

Ключевые слова <b class="keyword">while</b> и <b class="keyword">do</b>..<b class="keyword">while</b> работают как обычно:

``` kotlin
while (x > 0) {
  x--
}

do {
  val y = retrieveData()
} while (y != null) // y здесь доступно!
```

См. [использование <b class="keyword">while</b>](grammar.html#while).

## Ключевые слова <b class="keyword">break</b> и <b class="keyword">continue</b> в циклах

Kotlin поддерживает обычные операторы <b class="keyword">break</b> и <b class="keyword">continue</b> в циклах. См. [Операторы перехода](returns.html).


