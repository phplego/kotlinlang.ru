---
type: doc
layout: reference
category: "Higher-Order Functions and Lambdas
title: "Функции высокого порядка и Лямбды."
---

# Функции высокого порядка и Лямбды.

## Функции высокого порядка.
Функция высокого порядка - это функция, которая принимает другую функцию в качестве входного аргумента,
либо имеет функцию в качестве возвращаемого результата. Хорошим примером такой функции является lock(),
которая берёт залоченный объект и функцию, применяет лок, выполняет фкнуцию и отпускает lock:

``` kotlin
fun <T> lock(lock: Lock, body: ()-> T): T{
  lock.lock()
  try{
      return body()
  }
  finally {
      lock.unlock()
  }
}
```

Давайте проанализируем этот блок. Параметр body имеет функциональный тип: () -> T, то есть предполагается,
что это функция, которая не имеет никаких входных аргументов и возвращает значение типа T. Она вызывается
внутри блока try, защищена lock, и её результат возвращается lock() функцией.

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
- Параметры этого выражения (если такие есть) объявлены до знака "->" (параметры могут быть опущены)
- Тело выражения идёт после знака "->"



