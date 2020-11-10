---
type: doc
layout: reference
category: "Other"
title: "Мульти-декларации"
url: https://kotlinlang.ru/docs/reference/multi-declarations.html
---

<!--# Destructuring Declarations-->
<!--Переведено с опорой на статью JetBrains: https://habrahabr.ru/company/JetBrains/blog/152126/-->

# Мульти-декларации
<!--Sometimes it is convenient to _destructure_ an object into a number of variables, for example:-->
Иногда удобно _деструктуризировать_ объект на несколько переменных, например:

```kotlin
val (name, age) = person 
```

<!--This syntax is called a _destructuring declaration_. A destructuring declaration creates multiple variables at once.
We have declared two new variables: `name` and `age`, and can use them independently:-->
Этот синтаксис называется _деструктуризирующее присваивание_. Он позволяет присвоить объект сразу нескольким 
переменным, разбив его на части. Мы объявили две переменные: `name` и `age`, и теперь можем использовать их по отдельности:
 
```kotlin
println(name)
println(age)
```

<!--A destructuring declaration is compiled down to the following code:-->
Эта декларация транслируется в такой код:

```kotlin
val name = person.component1()
val age = person.component2()
```

<!--The `component1()` and `component2()` functions are another example of the _principle of conventions_ widely used in Kotlin 
(see operators like `+` and `*`, *for*{: .keyword }-loops etc.). 
Anything can be on the right-hand side of a destructuring declaration, as long as the required number of component functions can be called on it.
And, of course, there can be `component3()` and `component4()` and so on.-->
Как и многое другое в Kotlin, мульти-декларации опираются на конвенцию: функции `componentN()` вызываются по имени, 
то есть могут быть объявлены как в классе person, так и вне его — в качестве [расширений](extensions.html). 

<!--Note that the `componentN()` functions need to be marked with the `operator` 
keyword to allow using them in a destructuring declaration.-->
Заметьте, что функции `componentN()` нужно отмечать ключевым словом `operator`, чтобы позволить их использование в деструктуризирующем присваивании.

<!--Destructuring declarations also work in *for*{: .keyword }-loops: when you say-->
Деструктуризирующие присваивания также работают в циклах `for`:

```kotlin
for ((a, b) in collection) { ... }
```

<!--Variables `a` and `b` get the values returned by `component1()` and `component2()` called on elements of the collection.-->
В данном примере значения переменных `a` и `b` возращены методами `component1()` и `component2()`, вызванными неявно у элементов коллекции. 

<!--## Example: Returning Two Values from a Function-->
## Например: возврат двух значений из функции 
 
<!--Let's say we need to return two things from a function. For example, a result object and a status of some sort.
A compact way of doing this in Kotlin is to declare a [_data class_](data-classes.html) and return its instance:-->
Предположим, нам нужно вернуть два значения из функции. Например, результат вычисления и какой-нибудь статус.
Компактный способ достичь этого — объявление `data`-класса и возвращение его экземпляра:

```kotlin
data class Result(val result: Int, val status: Status)
fun function(...): Result {
    // вычисления
    
    return Result(result, status)
}

// Теперь мы можем использовать деструктуризирующее присваивание:
val (result, status) = function(...)
```

<!--Since data classes automatically declare `componentN()` functions, destructuring declarations work here.-->
Так как `data`-классы автоматически объявляют `componentN()`-функции, мульти-декларации будут работать с ними "из коробки".

<!--**NOTE**: we could also use the standard class `Pair` and have `function()` return `Pair<Int, Status>`, 
but it's often better to have your data named properly.-->
**ПРИМЕЧАНИЕ**: мы также могли использовать стандартный класс `Pair`, чтобы заставить функцию вернуть `Pair<Int, Status>`,
но правильнее будет именовать ваши данные должным образом.

<!--## Example: Destructuring Declarations and Maps-->
## Пример: мульти-декларации и ассоциативные списки

<!--Probably the nicest way to traverse a map is this:-->
Пожалуй, самый хороший способ итерации по ассоциативному списку:

```kotlin
for ((key, value) in map) {
   // do something with the key and the value
}
```

<!--To make this work, we should -->
Чтобы это работало, мы должны:

<!--* present the map as a sequence of values by providing an `iterator()` function,
* present each of the elements as a pair by providing functions `component1()` and `component2()`.-->
* представить ассоциативный список как последовательность значений, предоставив функцию `iterator()`,
* представить каждый элемент как пару с помощью функций `component1()` и `component2()`.
  
<!--And indeed, the standard library provides such extensions:-->
И да, стандартная библиотека предоставляет такие расширения:

```kotlin
operator fun <K, V> Map<K, V>.iterator(): Iterator<Map.Entry<K, V>> = entrySet().iterator()
operator fun <K, V> Map.Entry<K, V>.component1() = getKey()
operator fun <K, V> Map.Entry<K, V>.component2() = getValue()
```  
  
<!--So you can freely use destructuring declarations in *for*{: .keyword }-loops with maps (as well as collections of data class instances etc).-->
Так что вы можете свободно использовать мульти-декларации в циклах `for` с ассоциативными списками (так же как и с коллекциями экземпляров `data`-классов).
