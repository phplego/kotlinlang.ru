---
type: doc
layout: reference
category: "Higher-Order Functions and Lambdas
title: "Функции высокого уровня и Лямбды."
---

# Функции высокого уровня и Лямбды.

## Функции высокого уровня.
Функция высокого уровня - это функция, которая принимает другую функцию в качестве входного аргумента,
либо имеет функцию в качестве возвращаемого результата. Хорошим примером такой функции является `lock()`,
которая берёт залоченный объект и функцию, применяет лок, выполняет фкнуцию и отпускает `lock`:

``` kotlin
fun <T> lock(lock: Lock, body: () -> T): T{
  lock.lock()
  try{
      return body()
  }
  finally {
      lock.unlock()
  }
}
```

Давайте проанализируем этот блок. Параметр `body` имеет функциональный тип: `() -> T`, то есть предполагается,
что это функция, которая не имеет никаких входных аргументов и возвращает значение типа T. Она вызывается
внутри блока `try`, защищена `lock`, и её результат возвращается `lock()` функцией.

Если мы хотим вызвать метод lock(), мы можем подать другую функцию в качестве входящего аргумента 
(более подробно читайте [function references](http://kotlinlang.org/docs/reference/reflection.html#function-references)

``` kotlin
fun toBeSynchronized() = sharedResource.operation()

val result = lock (lock, ::toBeSynchronized)
```

Другой, наиболее удобный способ применения [лямбда-выражения](http://kotlinlang.org/docs/reference/lambdas.html#lambda-expressions-and-anonymous-functions):
```kotlin
val result = lock(lock, { sharedResource.operation() })
```

Лямбда-выражения более потробно описаны [здесь](http://kotlinlang.org/docs/reference/lambdas.html#lambda-expressions-and-anonymous-functions), но в целях продолжить этот раздел, давайте произведём краткий обзор:
- Лямбда-выражения всегда заключены в круглые скобки.
- Параметры этого выражения (если такие есть) объявлены до знака `->` (параметры могут быть опущены)
- Тело выражения идёт после знака `->`

В Kotlin существует конвенция, по которой, если последний параметр функции является функцией, и вы применяете лямбда-
выражение в качестве аргумнента, вы можете указать её вне скобок:
``` kotlin
lock (lock) {
    sharedResource.operation()
}
```

Другим примером функции высокого уровня будет `map()`:
``` kotlin
fun <T, R> List<T>.map(transform: (T) -> R): List<R> {
    val result = arrayListOf<R>()
    for (item in this)
        result.add(transform(item))
    return result
}
```

Эта функция может быть вызвана следующим образом:
``` kotlin
val doubled = ints.map { it -> it * 2 }
```

Обратите внимание, что параметры могут быть проигнорированы при вызове, если лямбда является единственным аргументом
для вызова функции.

<!--it: implicit name of a single parameter>
<!--http://kotlinlang.org/docs/reference/lambdas.html#it-implicit-name-of-a-single-parameter>
## it: неявное имя единичного параметра

Ещё одной полезной особенностью синтаксиса является возможность опустить объявление параметра функции в случае, если он
единственный (вместе с `->`) и её имя будет `it`:
``` kotlin
ints.map { it * 2 }
```

Это соглашение позволяет писать код в [LINQ](http://msdn.microsoft.com/en-us/library/bb308959.aspx) стиле:
``` kotlin
strings.filter { it.lenght == 5 }.sortBy { it }.map { it.toUpperCase() }
```

##Инлайн Функции
Иногда необходима улучшить производительность высокоуровневых функций, исползуя [инлайн функции](http://kotlinlang.org/docs/reference/inline-functions.html)

##Лямбда-выражения и Анонимные Функции.
<!--http://kotlinlang.org/docs/reference/inline-functions.html>




