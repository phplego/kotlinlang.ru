---
type: doc
layout: reference
category: "Basics"
title: "Основной синтаксис"
---
      
# Основной синтаксис

## Определение имени пакета

Имя пакета указывается в начале исходного фала, так же как и в Java:

``` kotlin
package my.demo

import java.util.*

// ...
```

Но в отличие от Java нет необходимости, чтобы стуктура пакетов совпадала со структурой папок:
исходные файлы могут располагаться в произвольном месте на диске.

См. [Пакеты](packages.html).

## Объявление функций

<!-- Function having two `Int` parameters with `Int` return type: -->
Функция принимает два аргумента `Int` и возвращает `Int`: 

``` kotlin
fun sum(a: Int, b: Int): Int {
  return a + b
}
```

<!-- Function with an expression body and inferred return type: -->
Функция с выражением в качестве тела и автоматически выведенным типом возвращаемого значения:

``` kotlin
fun sum(a: Int, b: Int) = a + b
```

<!-- Function returning no meaningful value: -->
Функция, не возвращающая никакого значения (void в Java):

``` kotlin
fun printSum(a: Int, b: Int): Unit {
  print(a + b)
}
```

<!-- `Unit` return type can be omitted: -->
Тип возвращаемого значения `Unit` может быть опущен: 

``` kotlin
fun printSum(a: Int, b: Int) {
  print(a + b)
}
```

См. [Функции](functions.html).

## Определение локальных переменных

<!-- Assign-once (read-only) local variable: -->
Неизменяемая (только для чтения) локальная переменная:

``` kotlin
val a: Int = 1
val b = 1   // Тип `Int` выведен автоматически
val c: Int  // Тип обязателен, когда значение не инициализируется
c = 1       // последующее присвоение
```

<!-- Mutable variable: -->
Изменяемая переменная:

``` kotlin
var x = 5 // Тип `Int` выведен автоматически
x += 1
```

См. [Свойства и поля](properties.html).

## Использование строковых шаблонов

Допустимо использование переменных внутри строк в формате `$name` или `${name}`:

``` kotlin
fun main(args: Array<String>) {
  if (args.size == 0) return

  print("First argument: ${args[0]}")
}
```

См. [Строковые шаблоны](basic-types.html#string-templates).

## Использование условных выражений

``` kotlin
fun max(a: Int, b: Int): Int {
  if (a > b)
    return a
  else
    return b
}
```
Также **if** может быть использовано как выражение (т. е. **if** ... **else** возвращает значение):


``` kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

См. [Выражение **if**<!--keyword-->](control-flow.html#if-expression).

## Nullable-значения и проверка на **null**<!--keyword-->

Ссылка должна быть явно объявлена как nullable (символ `?`) когда она может принимать значение **null**.

Возвращает **null**<!--keyword--> если `str` не содержит числа:

``` kotlin
fun parseInt(str: String): Int? {
  // ...
}
```

Использование функции, возвращающей **null**:

``` kotlin
fun main(args: Array<String>) {
  if (args.size < 2) {
    print("Ожидается два целых числа")
    return
  }

  val x = parseInt(args[0])
  val y = parseInt(args[1])

  // Использование `x * y` приведет к ошибке, потомучто они могут содержать null
  if (x != null && y != null) {
    // x и y автоматически приведены к не-nullable после проверки на null
    print(x * y)
  }
}
```

или

``` kotlin
  // ...
  if (x == null) {
    print("Неверный формат числа у '${args[0]}'")
    return
  }
  if (y == null) {
    print("Неверный формат числа у '${args[1]}'")
    return
  }

  // x и y автоматически приведены к не-nullable после проверки на null
  print(x * y)
```

См. [Null-безопасность](null-safety.html).

<!-- ## Using type checks and automatic casts -->
## Проверка типа и автоматическое приведение типов

Оператор **is**<!--keyword--> проверяет, является ли выражение экземпляром заданного типа.
Если неизменяемя локальная переменная или свойство уже проверены на определенный тип, то в дальнешйем коде нет небходимости
явно приводить к этому типу:

``` kotlin
fun getStringLength(obj: Any): Int? {
  if (obj is String) {
    // `obj` is automatically cast to `String` in this branch
    return obj.length
  }

  // `obj` is still of type `Any` outside of the type-checked branch
  return null
}
```

или

``` kotlin
fun getStringLength(obj: Any): Int? {
  if (obj !is String)
    return null

  // `obj` is automatically cast to `String` in this branch
  return obj.length
}
```

или даже

``` kotlin
fun getStringLength(obj: Any): Int? {
  // `obj` is automatically cast to `String` on the right-hand side of `&&`
  if (obj is String && obj.length > 0)
    return obj.length

  return null
}
```

См. [Классы](classes.html) и [Приведение типов](typecasts.html).

## Использование цикла `for`

``` kotlin
fun main(args: Array<String>) {
  for (arg in args)
    print(arg)
}
```

или

``` kotlin
for (i in args.indices)
  print(args[i])
```

См. [цикл for](control-flow.html#for-loops).

## Использование цикла `while`

``` kotlin
fun main(args: Array<String>) {
  var i = 0
  while (i < args.size)
    print(args[i++])
}
```

См. [цикл while](control-flow.html#while-loops).

## Использование выражения `when`

``` kotlin
fun cases(obj: Any) {
  when (obj) {
    1          -> print("One")
    "Hello"    -> print("Greeting")
    is Long    -> print("Long")
    !is String -> print("Not a string")
    else       -> print("Unknown")
  }
}
```

См. [выражение when](control-flow.html#when-expression).

## Использование диапазонов

Проверка входит ли число в диапазон с помощью оператора **in**:

``` kotlin
if (x in 1..y-1)
  print("OK")
```

Проверка числа на выход из диапазона:

``` kotlin
if (x !in 0..array.lastIndex)
  print("Out")
```

Проход по диапазону:

``` kotlin
for (x in 1..5)
  print(x)
```

См. [Диапазоны](ranges.html).

## Использование коллекций

Итерация по коллекции:

``` kotlin
for (name in names)
  println(name)
```

Проверка содержит ли коллекция данный объект с помощью оператора **in**:

``` kotlin
if (text in names) // names.contains(text) is called
  print("Yes")
```

Используем лямбда выражение для фильтрации и создания карты коллекции:

``` kotlin
names
    .filter { it.startsWith("A") }
    .sortedBy { it }
    .map { it.toUpperCase() }
    .forEach { print(it) }
```

См. [Функции высшего порядка и лямбды](lambdas.html).

