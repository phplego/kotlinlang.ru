---
type: doc
layout: reference
category: "Other"
title: "Мульти-декларации"
url: https://kotlinlang.ru/docs/destructuring-declarations.html
---

<!-- При переводе статьи оригинальная версия была от 11 February 2021 -->
<!-- Переведено с опорой на статью JetBrains: https://habrahabr.ru/company/JetBrains/blog/152126/ -->

<!-- # Destructuring declarations -->
# Мульти-декларации

<!-- Sometimes it is convenient to *destructure* an object into a number of variables, for example: -->
Иногда удобно *деструктуризировать* объект на несколько переменных, например:

```kotlin
val (name, age) = person 
```

<!-- This syntax is called a *destructuring declaration*. A destructuring declaration creates multiple variables at once.
You have declared two new variables: `name` and `age`, and can use them independently: -->
Этот синтаксис называется *деструктуризирующее присваивание*. Он позволяет присвоить объект сразу нескольким переменным,
разбив его на части. Вы объявили две переменные: `name` и `age`, и теперь можете использовать их по отдельности.

```kotlin
println(name)
println(age)
```

<!-- A destructuring declaration is compiled down to the following code: -->
Эта декларация транслируется в такой код:

```kotlin
val name = person.component1()
val age = person.component2()
```

<!-- The `component1()` and `component2()` functions are another example of the *principle of conventions* widely used in Kotlin 
(see operators like `+` and `*`, `for`-loops as an example). 
Anything can be on the right-hand side of a destructuring declaration, as long as the required number of component 
functions can be called on it. And, of course, there can be `component3()` and `component4()` and so on. -->
Как и многое другое в Kotlin, мульти-декларации опираются на конвенцию: функции `componentN()` вызываются по имени,
то есть могут быть объявлены как в классе person, так и вне его — в качестве [расширений](extensions.html).

<!-- > The `componentN()` functions need to be marked with the `operator` keyword to allow using them in a destructuring 
>declaration. -->
> Функции `componentN()` нужно отмечать ключевым словом `operator`, чтобы позволить их использование в
> деструктуризирующем присваивании.

<!-- Destructuring declarations also work in `for`-loops: -->
Деструктуризирующие присваивания также работают в циклах `for`:

```kotlin
for ((a, b) in collection) { /* ... */ }
```

<!-- Variables `a` and `b` get the values returned by `component1()` and `component2()` called on elements of the collection. -->
В данном примере значения переменных `a` и `b` возращены методами `component1()` и `component2()`, вызванными неявно у
элементов коллекции.

<a name="example-returning-two-values-from-a-function"></a>

<!-- ## Example: returning two values from a function -->
## Пример: возврат двух значений из функции

<!-- Assume that you need to return two things from a function - for example, a result object and a status of some sort.
A compact way of doing this in Kotlin is to declare a [data class](data-classes.md) and return its instance: -->
Предположим, вам нужно вернуть два значения из функции. Например, результат вычисления и какой-нибудь статус.
Компактный способ достичь этого — объявление [`data`-класса](data-classes.html) и возвращение его экземпляра.

```kotlin
data class Result(val result: Int, val status: Status)
fun function(...): Result {
    // вычисления
    
    return Result(result, status)
}

// Теперь вы можете использовать деструктуризирующее присваивание:
val (result, status) = function(...)
```

<!-- Since data classes automatically declare `componentN()` functions, destructuring declarations work here. -->
Так как `data`-классы автоматически объявляют `componentN()`-функции, мульти-декларации будут работать с ними "из
коробки".

<!-- > You could also use the standard class `Pair` and have `function()` return `Pair<Int, Status>`, 
> but it's often better to have your data named properly. -->
> Вы также могли использовать стандартный класс `Pair`, чтобы заставить функцию вернуть `Pair<Int, Status>`, но
> правильнее будет именовать ваши данные должным образом.

<a name="example-destructuring-declarations-and-maps"></a>

<!-- ## Example: destructuring declarations and maps -->
## Пример: мульти-декларации и ассоциативные списки

<!-- Probably the nicest way to traverse a map is this: -->
Пожалуй, самый хороший способ итерации по ассоциативному списку:

```kotlin
for ((key, value) in map) {
   // do something with the key and the value
}
```

<!-- To make this work, you should -->
Чтобы это работало, вы должны:

<!-- * Present the map as a sequence of values by providing an `iterator()` function.
* Present each of the elements as a pair by providing functions `component1()` and `component2()`. -->

* представить ассоциативный список как последовательность значений, предоставив функцию `iterator()`,
* представить каждый элемент как пару с помощью функций `component1()` и `component2()`.
  
<!-- And indeed, the standard library provides such extensions: -->
И да, стандартная библиотека предоставляет такие расширения:

```kotlin
operator fun <K, V> Map<K, V>.iterator(): Iterator<Map.Entry<K, V>> = entrySet().iterator()
operator fun <K, V> Map.Entry<K, V>.component1() = getKey()
operator fun <K, V> Map.Entry<K, V>.component2() = getValue()
```
  
<!-- So you can freely use destructuring declarations in `for`-loops with maps (as well as collections of data class instances or similar). -->
Так что вы можете свободно использовать мульти-декларации в циклах `for` с ассоциативными списками (так же как и с
коллекциями экземпляров `data`-классов).

<a name="underscore-for-unused-variables"></a>

<!-- ## Underscore for unused variables -->
## Подчеркивание для неиспользуемых переменных

<!-- If you don't need a variable in the destructuring declaration, you can place an underscore instead of its name: -->
Если вам не нужна переменная в объявлении деструктурирования, вы можете поместить символ подчеркивания вместо ее имени.

```kotlin
val (_, status) = getResult()
```

<!-- The `componentN()` operator functions are not called for the components that are skipped in this way. -->
Функции оператора `component()` не вызываются для компонентов, которые пропускаются таким образом.

<a name="destructuring-in-lambdas"></a>

<!-- ## Destructuring in lambdas -->
## Деструктурирование в лямбдах

<!-- You can use the destructuring declarations syntax for lambda parameters.
If a lambda has a parameter of the `Pair` type (or `Map.Entry`, or any other type that has the appropriate `componentN`
functions), you can introduce several new parameters instead of one by putting them in parentheses: -->
Вы можете использовать синтаксис объявлений деструктурирования для лямбда-параметров. Если у лямбды есть параметр типа
`Pair` (или `Map.Entry`, или любого другого типа, который имеет соответствующие функции `componentN`), вы можете ввести
несколько новых параметров вместо одного, заключив их в круглые скобки.

```kotlin
map.mapValues { entry -> "${entry.value}!" }
map.mapValues { (key, value) -> "$value!" }
```

<!-- Note the difference between declaring two parameters and declaring a destructuring pair instead of a parameter: -->
Обратите внимание на разницу между объявлением двух параметров и объявлением пары деструктурирования вместо параметра.

```kotlin
{ a -> ... } // один параметр
{ a, b -> ... } // два параметра
{ (a, b) -> ... } // пара деструктурирования
{ (a, b), c -> ... } // пара деструктурирования и параметр
```

<!-- If a component of the destructured parameter is unused, you can replace it with the underscore to avoid inventing its name: -->
Если компонент деструктурированного параметра не используется, вы можете заменить его символом подчеркивания, чтобы
избежать выдумывания его имени.

```kotlin
map.mapValues { (_, value) -> "$value!" }
```

<!-- You can specify the type for the whole destructured parameter or for a specific component separately: -->
Вы можете указать тип для всего деструктурированного параметра или отдельно для конкретного компонента.

```kotlin
map.mapValues { (_, value): Map.Entry<Int, String> -> "$value!" }

map.mapValues { (_, value: String) -> "$value!" }
```
