---
type: doc
layout: reference
category: "Basics"
title: "Основной синтаксис"
url: https://kotlinlang.ru/docs/reference/basic-syntax.html
---
      
# Основной синтаксис

## Определение имени пакета

Имя пакета указывается в начале исходного файла, так же как и в Java:

```kotlin
package my.demo

import java.util.*

// ...
```

Но в отличие от Java, нет необходимости, чтобы структура пакетов совпадала со структурой папок:
исходные файлы могут располагаться в произвольном месте на диске.

См. [Пакеты](packages.html).

## Объявление функции

<!-- Function having two `Int` parameters with `Int` return type: -->
Функция принимает два аргумента `Int` и возвращает `Int`: 

```kotlin
fun sum(a: Int, b: Int): Int {
  return a + b
}
```

<!-- Function with an expression body and inferred return type: -->
Функция с выражением в качестве тела и автоматически определенным типом возвращаемого значения:

```kotlin
fun sum(a: Int, b: Int) = a + b
```

<!-- Function returning no meaningful value: -->
Функция, не возвращающая никакого значения (void в Java):

```kotlin
fun printSum(a: Int, b: Int): Unit {
  print(a + b)
}
```

<!-- `Unit` return type can be omitted: -->
Тип возвращаемого значения `Unit` может быть опущен: 

```kotlin
fun printSum(a: Int, b: Int) {
  print(a + b)
}
```

См. [Функции](functions.html).

## Определение внутренних переменных

<!-- Assign-once (read-only) local variable: -->
Неизменяемая (только для чтения) внутренняя переменная:

```kotlin
val a: Int = 1
val b = 1   // Тип `Int` выведен автоматически
val c: Int  // Тип обязателен, когда значение не инициализируется
c = 1       // последующее присвоение
```

<!-- Mutable variable: -->
Изменяемая переменная:

```kotlin
var x = 5 // Тип `Int` выведен автоматически
x += 1
```

<!-- Top-level vaiables: -->
Глобальные переменные:

```kotlin
val PI = 3.14
var x = 0

fun incrementX() { 
    x += 1 
}
```


См. [Свойства и поля](properties.html).

## Комментарии
<!--Just like Java and JavaScript, Kotlin supports end-of-line and block comments.-->
Также, как Java и JavaScript, Kotlin поддерживает однострочные комментарии.

```kotlin
// однострочный комментарий

/* Блочный комментарий
   из нескольких строк. */
```

<!--Unlike Java, block comments in Kotlin can be nested.-->
В отличие от Java, блочные комментарии могут быть вложенными.

<!--See [Documenting Kotlin Code](kotlin-doc.html) for information on the documentation comment syntax.-->
См. [Документация Kotlin кода](https://kotlinlang.ru/docs/reference/kotlin-doc.html) для информации о документации в комментариях.

<a name="using-string-templates"></a>
## Использование строковых шаблонов

Допустимо использование переменных внутри строк в формате `$name` или `${name}`:

```kotlin
fun main(args: Array<String>) {
  if (args.size == 0) return

  print("Первый аргумент: ${args[0]}")
}
```

```
var a = 1
// просто имя переменной в шаблоне:
val s1 = "a равно $a" 

a = 2
// произвольное выражение в шаблоне:
val s2 = "${s1.replace("равно", "было равно")}, но теперь равно $a"

/*
  Результат работы программы:
  a было равно 1, но теперь равно 2
*/
```


См. [Строковые шаблоны](basic-types.html#string-templates).

## Использование условных выражений

```kotlin
fun max(a: Int, b: Int): Int {
  if (a > b)
    return a
  else
    return b
}
```
Также <b class="keyword">if</b> может быть использовано как выражение (т. е. <b class="keyword">if</b> ... <b class="keyword">else</b> возвращает значение):


```kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

См. [Выражение <b class="keyword">if</b>](control-flow.html#if-expression).

## Nullable-значения и проверка на <b class="keyword">null</b>

Ссылка должна быть явно объявлена как nullable (символ `?`) когда она может принимать значение <b class="keyword">null</b>.

Возвращает <b class="keyword">null</b> если `str` не содержит числа:

```kotlin
fun parseInt(str: String): Int? {
  // ...
}
```

Использование функции, возвращающей <b class="keyword">null</b>:

```kotlin
fun main(args: Array<String>) {
  if (args.size < 2) {
    print("Ожидается два целых числа")
    return
  }

  val x = parseInt(args[0])
  val y = parseInt(args[1])

  // Использование `x * y` приведет к ошибке, потому что они могут содержать null
  if (x != null && y != null) {
    // x и y автоматически приведены к не-nullable после проверки на null
    print(x * y)
  }
}
```

или

```kotlin
  // ...
  if (x == null) {
    print("Неверный формат числа x '${args[0]}'")
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

Оператор <b class="keyword">is</b> проверяет, является ли выражение экземпляром заданного типа.
Если неизменяемая внутренняя переменная или свойство уже проверены на определенный тип, то в дальнейшем нет необходимости
явно приводить к этому типу:

```kotlin
fun getStringLength(obj: Any): Int? {
  if (obj is String) {
    // в этом блоке `obj` автоматически преобразован в `String`
    return obj.length
  }

  // `obj` имеет тип `Any` вне блока проверки типа
  return null
}
```

или

```kotlin
fun getStringLength(obj: Any): Int? {
  if (obj !is String)
    return null

  // в этом блоке `obj` автоматически преобразован в `String`
  return obj.length
}
```

или даже

```kotlin
fun getStringLength(obj: Any): Int? {
  // `obj` автоматически преобразован в `String` справа от оператора `&&`
  if (obj is String && obj.length > 0)
    return obj.length

  return null
}
```

См. [Классы](classes.html) и [Приведение типов](typecasts.html).

## Использование цикла <b class="keyword">for</b>

```kotlin
fun main(args: Array<String>) {
  for (arg in args)
    print(arg)
}
```

или

```kotlin
for (i in args.indices)
  print(args[i])
```

См. [цикл for](control-flow.html#for-loops).

## Использование цикла <b class="keyword">while</b>

```kotlin
fun main(args: Array<String>) {
  var i = 0
  while (i < args.size)
    print(args[i++])
}
```

См. [цикл while](control-flow.html#while-loops).

## Использование выражения <b class="keyword">when</b>

```kotlin
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

## Использование интервалов

Проверка на вхождение числа в интервал с помощью оператора <b class="keyword">in</b>:

```kotlin
if (x in 1..y-1)
  print("OK")
```

Проверка значения на выход за пределы интервала:

```kotlin
if (x !in 0..array.lastIndex)
  print("Out")
```

Перебор значений в заданном интервале:

```kotlin
for (x in 1..5)
  print(x)
```
<!--or over a progression:-->
Или по арифметической прогрессии:
```kotlin
for (x in 1..10 step 2) {
  print(x)
}
for (x in 9 downTo 0 step 3) {
  print(x)
}
```

См. [Интервалы](ranges.html).

## Использование коллекций

Итерация по коллекции:

```kotlin
for (name in names)
  println(name)
```

Проверка, содержит ли коллекция данный объект, с помощью оператора <b class="keyword">in</b>:

```kotlin
val items = setOf("apple", "banana", "kiwi")
when {
  "orange" in items -> println("juicy")
  "apple" in items -> println("apple is fine too")
}
```

<!--Using lambda expressions to filter and map collections:-->
Использование лямбда-выражения для фильтрации и модификации коллекции:

```kotlin
names
    .filter { it.startsWith("A") }
    .sortedBy { it }
    .map { it.toUpperCase() }
    .forEach { print(it) }
```

См. [Функции высшего порядка и лямбды](lambdas.html).


<!--Creating basic classes and their instances:-->
Создание базовых классов и их экземпляров

```kotlin
val rectangle = Rectangle(5.0, 2.0) //не требуется ключевое слово 'new'
val triangle = Triangle(3.0, 4.0, 5.0)
```

См. [Классы](classes.html) и [Объявление экземпляров класса](object-declarations.html).


