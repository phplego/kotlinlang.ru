---
type: doc
layout: reference
category: "Syntax"
title: "Функции области видимости"
url: https://kotlinlang.ru/docs/reference/scope-functions.html
---

<!-- # Scope Functions -->
# Функции области видимости

<!-- The Kotlin standard library contains several functions whose sole purpose is to execute a block of code within the context of an object. When you call such a function on an object with a [lambda expression](lambdas.html) provided, it forms a temporary scope. In this scope, you can access the object without its name. Such functions are called _scope functions_. There are five of them: `let`, `run`, `with`, `apply`, and `also`. -->
Стандартная библиотека Kotlin содержит несколько функций, единственной целью которых является выполнение блока кода в контексте объекта. Эти функции формируют временную область видимости для объекта, к которому были применены, и вызывают код, указанный в переданном [лямбда-выражении](lambdas.md). В этой области видимости можно получить доступ к объекту без явного к нему обращения по имени. Такие функции называются _функциями области видимости_ (англ. _scope functions_). Всего их пять: `let`, `run`, `with`, `apply`, и `also`.

<!-- Basically, these functions do the same: execute a block of code on an object. What's different is how this object becomes available inside the block and what is the result of the whole expression. -->
По сути, все эти функции делают одно и то же: выполняют блок кода для объекта. Отличие состоит в том, как этот объект становится доступным внутри блока и каков результат всего выражения.

<!-- Here's a typical usage of a scope function: -->
Пример обычного использования функции области видимости:

```kotlin
data class Person(var name: String, var age: Int, var city: String) {
    fun moveTo(newCity: String) { city = newCity }
    fun incrementAge() { age++ }
}

fun main() {
    Person("Alice", 20, "Amsterdam").let {
        println(it)
        it.moveTo("London")
        it.incrementAge()
        println(it)
    }
}
```

<!-- If you write the same without `let`, you'll have to introduce a new variable and repeat its name whenever you use it.  -->
Если вы захотите написать то же самое без функции `let`, то вам придется объявить новую переменную и обращаться к ней всякий раз, когда она используется.

```kotlin
data class Person(var name: String, var age: Int, var city: String) {
    fun moveTo(newCity: String) { city = newCity }
    fun incrementAge() { age++ }
}

fun main() {
    val alice = Person("Alice", 20, "Amsterdam")
    println(alice)
    alice.moveTo("London")
    alice.incrementAge()
    println(alice)
}
```

<!-- The scope functions do not introduce any new technical capabilities, but they can make your code more concise and readable. -->
Функции области видимости не предоставляют никаких новых технических возможностей, но они могут сделать ваш код более кратким и читабельным.

<!-- Due to the similar nature of scope functions, choosing the right one for your case can be a bit tricky. The choice mainly depends on your intent and the consistency of use in your project. Below we'll provide detailed descriptions of the distinctions between scope functions and the conventions on their usage. -->
Из-за того, что все функции области видимости имеют схожий характер, выбрать правильную для вашего конкретного случая может быть затруднительно. В основном выбор зависит от ваших намерений и от структуры вашего проекта. Ниже приведено подробное описание того, чем функции области видимости отличаются между собой, а также соглашение об их использовании.

<!-- ## Distinctions -->
## Отличительные особенности

<!-- Because the scope functions are all quite similar in nature, it's important to understand the differences between them. There are two main differences between each scope function:
* The way to refer to the context object
* The return value. -->
Поскольку функции области видимости очень похожи друг на друга, важно понимать чем они различаются. Между ними есть два основных различия:
* Способ ссылки на контекстный объект
* Возвращаемое значение.

<!-- ### Context object: `this` or `it` -->
### Контекстный объект: `this` или `it`

<!-- Inside the lambda of a scope function, the context object is available by a short reference instead of its actual name. Each scope function uses one of two ways to access the context object: as a lambda [receiver](lambdas.html#function-literals-with-receiver) (`this`) or as a lambda argument (`it`). Both provide the same capabilities, so we'll describe the pros and cons of each for different cases and provide recommendations on their use. -->
Внутри лямбда-выражения, которое передается функции области видимости, объект контекста доступен по краткой ссылке, а не по его фактическому имени. Каждая функция области видимости использует один из двух способов доступа к объекту контекста: как [лямбда-получатель](lambdas.md#function-literals-with-receive) (`this`) или как лямбда-аргумент (`it`). Оба предоставляют одинаковые возможности, поэтому опишем плюсы и минусы каждого для разных случаев и дадим рекомендации по их использованию.

```kotlin
fun main() {
    val str = "Hello"
    // this
    str.run {
        println("Длина строки: $length")
        //println("Длина строки: ${this.length}") // делает то же самое
    }

    // it
    str.let {
        println("Длина строки: ${it.length}")
    }
}
```

#### this

<!-- `run`, `with`, and `apply` refer to the context object as a lambda receiver - by keyword `this`. Hence, in their lambdas, the object is available as it would be in ordinary class functions. In most cases, you can omit `this` when accessing the members of the receiver object, making the code shorter. On the other hand, if `this` is omitted, it can be hard to distinguish between the receiver members and external objects or functions. So, having the context object as a receiver (`this`) is recommended for lambdas that mainly operate on the object members: call its functions or assign properties. -->
`run`,` with` и `apply` ссылаются на объект контекста как лямбда-получатель - по ключевому слову `this`. Следовательно, в их лямбдах объект доступен, как это было бы в обычных функциях класса. В большинстве случаев `this` можно опустить при доступе к элементам объекта-получателя, что сделает код короче. С другой стороны, если `this` опущено, то будет сложнее различить элементы-получатели с внешними объектами или функциями. Таким образом, наличие `this` рекомендуется для лямбд, которые в основном работают с членами объекта: вызывают его функции или присваивают свойства.

```kotlin
data class Person(var name: String, var age: Int = 0, var city: String = "")

fun main() {
    val adam = Person("Adam").apply {
        age = 20        // то же самое, что и this.age = 20 или adam.age = 20
        city = "London"
    }
    println(adam)
}
```

#### it

<!-- In turn, `let` and `also` have the context object as a lambda argument. If the argument name is not specified, the object is accessed by the implicit default name `it`. `it` is shorter than `this` and expressions with `it` are usually easier for reading. However, when calling the object functions or properties you don't have the object available implicitly like `this`. Hence, having the context object as `it` is better when the object is mostly used as an argument in function calls. `it` is also better if you use multiple variables in the code block. -->
В свою очередь, `let` и `also` передают контекстный объект как аргумент в лямбду.  Если имя аргумента не указано, то к объекту обращаются неявным способом при помощи ключевого слова `it`. `it` короче `this` и выражения с `it` более читабельны. Однако при вызове функций или свойств объекта у вас не будет доступа к такому неявному объекту как `this`. Следовательно, использовать контекстный объект `it` лучше, когда объект в основном используется в качестве аргумента для вызова функций. `it` также лучше, если вы используете несколько переменных в блоке кода.

```kotlin
import kotlin.random.Random

fun writeToLog(message: String) {
    println("INFO: $message")
}

fun main() {
    fun getRandomInt(): Int {
        return Random.nextInt(100).also {
            writeToLog("Метод getRandomInt() сгенерировал значение $it")
        }
    }

    val i = getRandomInt()
}
```


<!-- Additionally, when you pass the context object as an argument, you can provide a custom name for the context object inside the scope. -->
Кроме того, когда вы передаете объект в качестве аргумента, вы можете указать пользовательское имя для этого объекта внутри области видимости.

```kotlin
import kotlin.random.Random

fun writeToLog(message: String) {
    println("INFO: $message")
}

fun main() {
    fun getRandomInt(): Int {
        return Random.nextInt(100).also { value ->
            writeToLog("Метод getRandomInt() сгенерировал значение $value")
        }
    }

    val i = getRandomInt()
}
```


<!-- ### Return value -->
### Возвращаемое значение

<!-- The scope functions differ by the result they return:
* `apply` and `also` return the context object.
* `let`, `run`, and `with` return the lambda result. -->
Функции области видимости также отличаются по значению, которое они возвращают:
* `apply` и `also` возвращают объект контекста.
* `let`, `run` и `with` возвращают результат лямбды.

<!-- These two options let you choose the proper function depending on what you do next in your code. -->
Это отличие позволит выбрать правильную функцию в зависимости от того, что вы будете делать дальше в своем коде.

<!-- #### Context object -->
#### Контекстный объект

<!-- The return value of `apply` and `also` is the context object itself. Hence, they can be included into call chains as _side steps_: you can continue chaining function calls on the same object after them.   -->
Функции `apply` и `also` возвращают объект контекста. Следовательно, с их помощью можно вызвать длинную цепочку функций относительно оригинального контекстного объекта. Такая цепочка функций известна как _side steps_.

```kotlin
fun main() {
    val numberList = mutableListOf<Double>()
    numberList.also { println("Заполнение списка") }
        .apply {
            add(2.71)
            add(3.14)
            add(1.0)
        }
        .also { println("Сортировка списка") }
        .sort()
    println(numberList)
}
```

<!-- They also can be used in return statements of functions returning the context object. -->
Они также могут быть использованы совместно с ключевым словом `return`.

```kotlin
import kotlin.random.Random

fun writeToLog(message: String) {
    println("Информация: $message")
}

fun main() {
    fun getRandomInt(): Int {
        return Random.nextInt(100).also {
            writeToLog("Метод getRandomInt() сгенерировал значение $it")
        }
    }

    val i = getRandomInt()
}
```

<!-- #### Lambda result -->
#### Результат лямбды

<!-- `let`, `run`, and `with` return the lambda result. So, you can use them when assigning the result to a variable, chaining operations on the result, and so on. -->
`let`, `run` и `with` возвращают результат лямбды. Таким образом, вы можете использовать их при присваивании переменной результата вычислений, либо использовать результат для последующего вызова цепочки операций и тд.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three")
    val countEndsWithE = numbers.run {
        add("four")
        add("five")
        count { it.endsWith("e") }
    }
    println("Элементы в $countEndsWithE, которые заканчиваются на e.")
}
```

<!-- Additionally, you can ignore the return value and use a scope function to create a temporary scope for variables. -->
Кроме того, вы можете игнорировать возвращаемое значение и использовать функцию для создания временной области видимости для переменной.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three")
    with(numbers) {
        val firstItem = first()
        val lastItem = last()        
        println("Первый элемент: $firstItem, последний элемент: $lastItem")
    }
}
```

<!-- ## Functions -->
## Функции

<!-- To help you choose the right scope function for your case, we'll describe them in detail and provide usage recommendations. Technically, functions are interchangeable in many cases, so the examples show the conventions that define the common usage style. -->
Чтобы помочь вам выбрать правильную функцию области видимости, ниже будет представлено их подробное описание и рекомендации по использованию. Во многих случаях они взаимозаменяемы, поэтому в примерах будет отражен общий стиль использования, а также соглашение по их применению.


### `let`

<!-- **The context object** is available as an argument (`it`). **The return value** is the lambda result. -->
**Контекстный объект** доступен в качестве аргумента (`it`). **Возвращаемое значение** - результат выполнения лямбды.

<!-- `let` can be used to invoke one or more functions on results of call chains. For example, the following code prints the results of two operations on a collection: -->
<!-- `let` может использоваться для вызова одной или нескольких функций в результатах цепочек вызовов. Например, следующий код печатает результаты двух операций над коллекцией: -->
Если значение переменной вычислялось при помощи цепочки операций, то `let` позволяет использовать полученный результат для вызова одной или нескольких функций в блоке кода. Например, в следующем коде выполняется цепочка из двух операций, результат записывается в отдельную переменную, после чего она выводится на печать.


```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four", "five")
    val resultList = numbers.map { it.length }.filter { it > 3 }
    println(resultList)    
}
```

<!-- With `let`, you can rewrite it: -->
С функцией `let` это код может быть переписан следующим образом:

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four", "five")
    numbers.map { it.length }.filter { it > 3 }.let {
        println(it)
        // при необходимости можно вызвать больше функций
    }
}
```

<!-- If the code block contains a single function with `it` as an argument, you can use the method reference (`::`) instead of the lambda: -->
Если блок кода содержит одну функцию, где `it` является аргументом, то лямбда-выражение может быть заменено ссылкой на метод (`::`):

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four", "five")
    numbers.map { it.length }.filter { it > 3 }.let(::println)
}
```

<!-- `let` is often used for executing a code block only with non-null values. To perform actions on a non-null object, use the safe call operator `?.` on it and call `let` with the actions in its lambda. -->
`let` часто используется для выполнения блока кода только с non-null значениями. Чтобы выполнить действия с non-null объектом, используйте оператор безопасного вызова `?.` совместно с функцией `let`.

```kotlin
fun processNonNullString(str: String) {}

fun main() {
    val str: String? = "Hello"   
    //processNonNullString(str)       // compilation error: str может быть null
    val length = str?.let {
        println("Вызов функции let() для $it")        
        processNonNullString(it)      // OK: 'it' не может быть null внутри конструкции '?.let { }'
        it.length
    }
}
```

<!-- Another case for using `let` is introducing local variables with a limited scope for improving code readability. To define a new variable for the context object, provide its name as the lambda argument so that it can be used instead of the default `it`. -->
Еще один вариант использования `let` - это введение локальных переменных с ограниченной областью видимости для улучшения читабельности кода. Чтобы определить новую переменную для контекстного объекта, укажите ее имя в качестве аргумента лямбды, чтобы ее можно было использовать вместо ключевого слова `it`.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    val modifiedFirstItem = numbers.first().let { firstItem ->
        println("Первый элемент в списке: '$firstItem'")
        if (firstItem.length >= 5) firstItem else "!" + firstItem + "!"
    }.toUpperCase()
    println("Первый элемент списка после изменений: '$modifiedFirstItem'")
}
```


### `with`

<!-- A non-extension function: **the context object** is passed as an argument, but inside the lambda, it's available as a receiver (`this`). **The return value** is the lambda result. -->
Не является функцией-расширением. **Контекстный объект** передается в качестве аргумента, а внутри лямбда-выражения он доступен как получатель (`this`). **Возвращаемое значение** - результат выполнения лямбды.

<!-- We recommend `with` for calling functions on the context object without providing the lambda result. In the code, `with` can be read as “_with this object, do the following._” -->
Функцию `with` рекомендуется использовать для вызова функций контекстного объекта без предоставления результата лямбды. В коде `with` может читаться как" _с этим объектом, сделайте следующее._ "

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three")
    with(numbers) {
        println("'with' вызывает с аргументом $this")
        println("Список содержит $size элементов")
    }
}
```

<!-- Another use case for `with` is introducing a helper object whose properties or functions will be used for calculating a value. -->
Другой вариант использования `with` - введение вспомогательного объекта, свойства или функции которые будут использоваться для вычисления значения.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three")
    val firstAndLast = with(numbers) {
        "Первый элемент списка - ${first()}," +
        " последний элемент списка - ${last()}"
    }
    println(firstAndLast)
}
```


### `run`

<!-- **The context object** is available as a receiver (`this`). **The return value** is the lambda result. -->
**Контекстный объект** доступен в качестве получателя (`this`). **Возвращаемое значение** - результат выполнения лямбды.

<!-- `run` does the same as `with` but invokes as `let` - as an extension function of the context object. -->
`run` делает то же самое, что и `with`, но вызывается как `let` - как функция расширения контекстного объекта.

<!-- `run` is useful when your lambda contains both the object initialization and the computation of the return value. -->
`run` удобен, когда лямбда содержит и инициализацию объекта, и вычисление возвращаемого значения.

```kotlin
class MultiportService(var url: String, var port: Int) {
    fun prepareRequest(): String = "Запрос по умолчанию"
    fun query(request: String): String = "Результат запроса: '$request'"
}

fun main() {
    val service = MultiportService("https://example.kotlinlang.org", 80)

    val result = service.run {
        port = 8080
        query(prepareRequest() + " порт - $port")
    }

    // аналогичный код с использованием функции let():
    val letResult = service.let {
        it.port = 8080
        it.query(it.prepareRequest() + " порт - ${it.port}")
    }
    println(result)
    println(letResult)
}
```


<!-- Besides calling `run` on a receiver object, you can use it as a non-extension function. Non-extension `run` lets you execute a block of several statements where an expression is required. -->
Помимо вызова `run` для объекта-получателя, вы можете использовать его как функцию без расширения. В этом случае `run` позволяет выполнить блок из нескольких операторов там, где это требуется.

```kotlin
fun main() {
    val hexNumberRegex = run {
        val digits = "0-9"
        val hexDigits = "A-Fa-f"
        val sign = "+-"

        Regex("[$sign]?[$digits$hexDigits]+")
    }

    for (match in hexNumberRegex.findAll("+1234 -FFFF not-a-number")) {
        println(match.value)
    }
}
```


### `apply`

<!-- **The context object** is available as a receiver (`this`). **The return value** is the object itself. -->
**Контекстный объект** доступен в качестве получателя (`this`). **Возвращаемое значение** - контекстный объект.

<!-- Use `apply` for code blocks that don't return a value and mainly operate on the members of the receiver object. The common case for `apply` is the object configuration. Such calls can be read as “_apply the following assignments to the object._” -->
Используйте `apply` для такого блока кода, который не возвращает значение и в основном работает с членами объекта-получателя. Типичный способ использования функции `apply` - настройка объекта-получателя. Это всеравно что мы скажем _“примени перечисленные настройки к объекту.”_

```kotlin
data class Person(var name: String, var age: Int = 0, var city: String = "")

fun main() {
    val adam = Person("Adam").apply {
        age = 32
        city = "London"        
    }
    println(adam)
}
```

<!-- Having the receiver as the return value, you can easily include `apply` into call chains for more complex processing. -->
Так как возвращаемое значение - это сам объект, то можно с легкостью включить `apply` в цепочки вызовов для более сложной обработки.


### `also`

<!-- **The context object** is available as an argument (`it`). **The return value** is the object itself. -->
**Контекстный объект** доступен в качестве аргумента (`it`). **Возвращаемое значение** - контекстный объект.

<!-- `also` is good for performing some actions that take the context object as an argument. Use `also` for actions that need a reference rather to the object than to its properties and functions, or when you don't want to shadow `this` reference from an outer scope. -->
`also` хорош для выполнения таких действий, которые принимают контекстный объект в качестве аргумента. То есть, эту функции следует использовать, когда требуется ссылка именно на объект, а не на его свойства и функции. Либо, когда вы хотите, чтобы была доступна ссылка на `this` из внешней области видимости.

<!-- When you see `also` in the code, you can read it as “_and also do the following with the object._” -->
Когда вы видите в коде `also`, то это можно прочитать как _"а также с объектом нужно сделать следующее."_

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three")
    numbers
        .also { println("Элементы списка перед добавлением нового: $it") }
        .add("four")
}
```


<!-- ## Function selection -->
## Выбор функции

<!-- To help you choose the right scope function for your purpose, we provide the table of key differences between them. -->
В таблице ниже приведены ключевые различия между функциями области видимости, что должно помочь вам сделать правильный выбор в пользу той или иной функции.

|Функция|Обращение к объекту|Возвращаемое значение|Является функцией-расширением|
|---|---|---|---|
|`let`|`it`|Результат лямбды|Да|
|`run`|`this`|Результат лямбды|Да|
|`run`|-|Результат лямбды|Нет: может быть вызвана без контекстного объекта|
|`with`|`this`|Результат лямбды|Нет: принимает контекстный объект в качестве аргумента.|
|`apply`|`this`|Контекстный объект|Да|
|`also`|`it`|Контекстный объект|Да|

<!-- Here is a short guide for choosing scope functions depending on the intended purpose: -->
Краткое руководство по выбору функции области видимости в зависимости от предполагаемого назначения:

<!-- * Executing a lambda on non-null objects: `let`
* Introducing an expression as a variable in local scope: `let`
* Object configuration: `apply`
* Object configuration and computing the result: `run`
* Running statements where an expression is required: non-extension `run`
* Additional effects: `also`
* Grouping function calls on an object: `with` -->
* Выполнение лямбды для non-null объектов: `let`
* Представление переменной в виде выражения со своей локальной областью видимости: `let`
* Настройка объекта: `apply`
* Настройка объекта и вычисление результата: `run`
* Выполнение операций, для которых требуется выражение: `run` без расширения
* Применение дополнительных значений: `also`
* Группировка всех функций, вызываемых для объекта: `with`

<!-- The use cases of different functions overlap, so that you can choose the functions based on the specific conventions used in your project or team. -->
Некоторые функции области видимости являются взаимозаменяемыми, поэтому вы можете выбирать функции исходя из соглашений, принятых в вашем проекте или команде.

<!-- Although the scope functions are a way of making the code more concise, avoid overusing them: it can decrease your code readability and lead to errors. Avoid nesting scope functions and be careful when chaining them: it's easy to get confused about the current context object and the value of `this` or `it`. -->
Несмотря на то, что функции области видимости предназначены для того, чтобы сделать код более кратким, избегайте их чрезмерного использования: это может снизить читабельность кода и привести к ошибкам. Избегайте вложенности функций и будьте осторожны при их объединении: можно легко запутаться в текущем значении контекстного объекта и в значениях `this` или `it`.

## `takeIf` и `takeUnless`

<!-- In addition to scope functions, the standard library contains the functions `takeIf` and `takeUnless`. These functions let you embed checks of the object state in call chains. -->
Помимо функций области видимости, стандартная библиотека содержит функции `takeIf` и `takeUnless`. Эти функции позволяют встроить проверку состояния объекта в цепочке вызовов.

<!-- When called on an object with a predicate provided, `takeIf` returns this object if it matches the predicate. Otherwise, it returns `null`. So, `takeIf` is a filtering function for a single object. In turn, `takeUnless` returns the object if it doesn't match the predicate and `null` if it does. The object is available as a lambda argument (`it`). -->
При вызове `takeIf` для объекта с предикатом этот объект будет возвращен, если он соответствует предикату. В противном случае возвращается `null`. В свою очередь, `takeUnless` возвращает объект, если он не соответствует предикату, и `null`, если  соответствует. Объект доступен как лямбда-аргумент (`it`).

```kotlin
import kotlin.random.*

fun main() {
    val number = Random.nextInt(100)

    val evenOrNull = number.takeIf { it % 2 == 0 }
    val oddOrNull = number.takeUnless { it % 2 == 0 }
    println("четный: $evenOrNull, нечетный: $oddOrNull")
}
```

<!-- When chaining other functions after `takeIf` and `takeUnless`, don't forget to perform the null check or the safe call (`?.`) because their return value is nullable. -->
При добавлении в цепочку вызовов других функций после `takeIf` и `takeUnless`, не забудьте выполнить проверку на null или используйте оператор безопасного вызова (`?.`), потому что их возвращаемое значение имеет тип **nullable**.

```kotlin
fun main() {
    val str = "Hello"
    val caps = str.takeIf { it.isNotEmpty() }?.toUpperCase()
   //val caps = str.takeIf { it.isNotEmpty() }.toUpperCase() //compilation error
    println(caps)
}
```

<!-- `takeIf` and `takeUnless` are especially useful together with scope functions. A good case is chaining them with `let` for running a code block on objects that match the given predicate. To do this, call `takeIf` on the object and then call `let` with a safe call (`?`). For objects that don't match the predicate, `takeIf` returns `null` and `let` isn't invoked. -->
`takeIf` и `takeUnless` особенно полезны при совместном использовании с функциями области видимости. Хорошим примером является объединение их в цепочку с `let` для выполнения блока кода для объектов, которые соответствуют заданному предикату. Для этого вызовите `takeIf` для объекта, а затем вызовите `let` с оператором безопасного вызова (`?`). Для объектов, которые не соответствуют предикату, `takeIf` возвращает `null`, а `let` не вызывается.

```kotlin
fun main() {
    fun displaySubstringPosition(input: String, sub: String) {
        input.indexOf(sub).takeIf { it >= 0 }?.let {
            println("Подстрока $sub находится в $input.")
            println("Начинается с индекса $it.")
        }
    }

    displaySubstringPosition("010000011", "11")
    displaySubstringPosition("010000011", "12")
}
```

<!-- This is how the same function looks without the standard library functions: -->
Тот же самый код, но без использования функций из стандартной библиотеки, выглядит следующим образом:

```kotlin
fun main() {
    fun displaySubstringPosition(input: String, sub: String) {
        val index = input.indexOf(sub)
        if (index >= 0) {
            println("Подстрок $sub находится в $input.")
            println("Начинается с индекса $index.")
        }
    }

    displaySubstringPosition("010000011", "11")
    displaySubstringPosition("010000011", "12")
}
```
