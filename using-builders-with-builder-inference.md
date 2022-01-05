---
type: doc
layout: reference
title: "Использование строителей с выводом типа строителя"
url: https://kotlinlang.ru/docs/using-builders-with-builder-inference.html
---

<!-- При переводе статьи оригинальная версия была от 16 November 2021 -->

<!-- # Using builders with builder type inference -->
# Использование строителей с выводом типа строителя

<!-- Kotlin supports _builder type inference_ (or builder inference), which can come in useful when you are working with
generic builders. It helps the compiler infer the type arguments of a builder call based on the type information
about other calls inside its lambda argument. -->
Kotlin поддерживает *определение типа строителя* (или вывод строителя), которое может оказаться полезным при работе с
универсальными строителями. Это помогает компилятору определять тип аргументов вызова строителя на основе информации
о типе других вызовов внутри его лямбда-аргумента.

<!-- Consider this example of [`buildMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/build-map.html)
usage: -->
Рассмотрим в кажестве примера использование [`buildMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/build-map.html).

```kotlin
fun addEntryToMap(baseMap: Map<String, Number>, additionalEntry: Pair<String, Int>?) {
   val myMap = buildMap {
       putAll(baseMap)
       if (additionalEntry != null) {
           put(additionalEntry.first, additionalEntry.second)
       }
   }
}
```

<!-- There is not enough type information here to infer type arguments in a regular way, but builder inference can
analyze the calls inside the lambda argument. Based on the type information about `putAll()` and `put()` calls,
the compiler can automatically infer type arguments of the `buildMap()` call into `String` and `Number`.
Builder inference allows to omit type arguments while using generic builders. -->
Здесь недостаточно информации о типе для обычного определения типа аргументов, но вывод строителя может анализировать
вызовы внутри аргумента лямбда. Основываясь на информации о типе вызовов `putAll()` и `put()`, компилятор может
автоматически выводить тип аргументов вызова `buildMap()` как `String` и `Number`. Вывод строителя позволяет опускать
аргументы типа при использовании универсальных строителей.

<a name="writing-your-own-builders"></a>

<!-- ## Writing your own builders -->
## Создание ваших собственных строителей

<a name="requirements-for-enabling-builder-inference"></a>

<!-- ### Requirements for enabling builder inference -->
### Требования для включения вывода строителя

<!-- > Before Kotlin 1.6.0, enabling builder inference for a builder function required the [`@BuilderInference`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-builder-inference/)
> annotation to be present on a builder lambda parameter. In 1.6.0, you can omit the annotation if both you and your
> builder's clients are using the compiler option `-Xenable-builder-inference`. -->
> До Kotlin 1.6.0 для включения вывода строителя для функции строителя требовалось аннотация
> [`@BuilderInference`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-builder-inference/) в лямбда-параметре
> строителя. В версии 1.6.0 вы можете её опустить, если и вы, и клиенты вашего строителя используете опцию компилятора
> `-Xenable-builder-inference`.

<!-- To let builder inference work for your own builder, make sure its declaration has a builder lambda parameter of a
function type with a receiver. There are also two requirements for the receiver type: -->
Чтобы вывод строителя работал для вашего собственного строителя, убедитесь, что в его объявлении есть лямбда-параметр
типа функции с получателем. Существует также два требования к типу приемника:

<!-- 1. It should use the type arguments that builder inference is supposed to infer. For example: -->
1. Он должен использовать тип аргументов, который должен определить вывод строителя. Например:

   ```kotlin
   fun <V> buildList(builder: MutableList<V>.() -> Unit) { ... }
   ```

   <!-- > Note that passing the type parameter's type directly like `fun <T> myBuilder(builder: T.() -> Unit)` is not yet supported. -->
   > Обратите внимание, что передача типов параметра напрямую, например `fun <T> myBuilder(builder: T.() -> Unit)`, еще не поддерживается.

<!-- 2. It should provide public members or extensions that contain the corresponding type parameters in their signature. For example: -->
2. Он должен предоставлять public члены или расширения, которые содержат соответствующие типы параметра в своей сигнатуре. Например:

   ```kotlin
   class ItemHolder<T> {
       private val items = mutableListOf<T>()

       fun addItem(x: T) {
           items.add(x)
       }

       fun getLastItem(): T? = items.lastOrNull()
   }
   
   fun <T> ItemHolder<T>.addAllItems(xs: List<T>) {
       xs.forEach { addItem(it) }
   }

   fun <T> itemHolderBuilder(builder: ItemHolder<T>.() -> Unit): ItemHolder<T> = 
       ItemHolder<T>().apply(builder)

   fun test(s: String) {
       val itemHolder1 = itemHolderBuilder { // Тип itemHolder1 - это ItemHolder<String>
           addItem(s)
       }
       val itemHolder2 = itemHolderBuilder { // Тип itemHolder2 - это ItemHolder<String>
           addAllItems(listOf(s)) 
       }
       val itemHolder3 = itemHolderBuilder { // Тип itemHolder3 - это ItemHolder<String?>
           val lastItem: String? = getLastItem()
           // ...
       }
   }
   ```

<a name="supported-features"></a>

<!-- ### Supported features -->
### Поддерживаемые возможности

<!-- Builder inference supports: -->
Вывод строителя поддерживает:

<!-- * Inferring several type arguments -->
* определение нескольких типов аргументов,

  ```kotlin
  fun <K, V> myBuilder(builder: MutableMap<K, V>.() -> Unit): Map<K, V> { ... }
  ```

<!-- * Inferring type arguments of several builder lambdas within one call including interdependent ones -->
* определение типа аргументов нескольких лямбд строителя в одном вызове, включая взаимозависимые,

  ```kotlin
  fun <K, V> myBuilder(
      listBuilder: MutableList<V>.() -> Unit,
      mapBuilder: MutableMap<K, V>.() -> Unit
  ): Pair<List<V>, Map<K, V>> =
      mutableListOf<V>().apply(listBuilder) to mutableMapOf<K, V>().apply(mapBuilder)
  
  fun main() {
      val result = myBuilder(
          { add(1) },
          { put("key", 2) }
      )
      // result имеет типы Pair<List<Int>, Map<String, Int>>
  }
  ```

<!-- * Inferring type arguments whose type parameters are lambda's parameter or return types -->
* определение типа аргументов, типы параметров которых являются типами параметров лямбды или возвращаемых значений,

  ```kotlin
  fun <K, V> myBuilder1(
      mapBuilder: MutableMap<K, V>.() -> K
  ): Map<K, V> = mutableMapOf<K, V>().apply { mapBuilder() }
  
  fun <K, V> myBuilder2(
      mapBuilder: MutableMap<K, V>.(K) -> Unit
  ): Map<K, V> = mutableMapOf<K, V>().apply { mapBuilder(2 as K) }
  
  fun main() {
      // тип result1 определен как Map<Long, String>
      val result1 = myBuilder1 {
          put(1L, "value")
          2
      }
      val result2 = myBuilder2 {
          put(1, "value 1")
          // You can use `it` as "postponed type variable" type
          // See the details in the section below
          put(it, "value 2")
      }
  }
  ```

<a name="how-builder-inference-works"></a>

<!-- ## How builder inference works -->
## Как работает вывод строителя

<a name="postponed-type-variables"></a>

<!-- ### Postponed type variables -->
### Переменные отложенного типа

<!-- Builder inference works in terms of _postponed type variables_, which appear inside the builder lambda during builder
inference analysis. A postponed type variable is a type argument's type, which is in the process of inferring.
The compiler uses it to collect type information about the type argument. -->
Вывод строителя работает на основе *переменных отложенного типа*, которые появляются внутри лямбды строителя во время
анализа вывода строителя. Переменная отложенного типа - это тип аргумента, который находится в процессе вывода.
Компилятор использует его для сбора информации о типе аргумента.

<!-- Consider the example with [`buildList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/build-list.html): -->
Рассмотрим пример с [`buildList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/build-list.html):

```kotlin
val result = buildList {
    val x = get(0)
}
```

<!-- Here `x` has a type of postponed type variable: the `get()` call returns a value of type `E`, but `E` itself is not yet
fixed. At this moment, a concrete type for `E` is unknown. -->
Здесь `x` имеет тип переменной отложенного типа: вызов `get()` возвращает значение типа `E`, но само `E` еще не уточнено.
На данный момент конкретный тип для `E` неизвестен.

<!-- When a value of a postponed type variable gets associated with a concrete type, builder inference collects this information
to infer the resulting type of the corresponding type argument at the end of the builder inference analysis. For example: -->
Когда значение переменной отложенного типа связывается с конкретным типом, вывод строителя собирает эту информацию для
вывода результирующего типа соответствующего аргумента в конце анализа вывода строителя. Например:

```kotlin
val result = buildList {
    val x = get(0)
    val y: String = x
} // result имеет выведенный тип List<String>
```

<!-- After the postponed type variable gets assigned to a variable of the `String` type, builder inference gets the information
that `x` is a subtype of `String`. This assignment is the last statement in the builder lambda, so the builder inference
analysis ends with the result of inferring the type argument `E` into `String`. -->
После того, как переменная отложенного типа присваивается переменной типа `String`, вывод строителя получает информацию
о том, что `x` является подтипом `String`. Это присваивание является последним оператором в лямбде строителя, поэтому
анализ вывода строителя заканчивается результатом вывода аргумента `E` в `String`.

<!-- Note that you can always call `equals()`, `hashCode()`, and `toString()` functions with a postponed type variable as a
receiver. -->
Обратите внимание, что вы всегда можете вызвать функции `equals()`, `hashCode()` и `toString()` с переменной отложенного
типа в качестве получателя.

<a name="contributing-to-builder-inference-results"></a>

<!-- ### Contributing to builder inference results -->
### Вклад в результаты вывода строителя

<!-- Builder inference can collect different varieties of type information that contribute to the analysis result.
It considers: -->
Вывод строителя может собирать различную информацию о типе, которые влияют на результат анализа. Он рассматривает:

<!-- * Calling methods on a lambda's receiver that use the type parameter's type -->
* Вызов методов в приемнике лямбды, которые используют тип параметра;

  ```kotlin
  val result = buildList {
      // Тип аргумента выводится в String на основе переданного аргумента "value"
      add("value")
  } // result имеет выведенный тип List<String>
  ```

<!-- * Specifying the expected type for calls that return the type parameter's type -->
* Указание ожидаемого типа для вызовов, возвращающих тип параметра;

  ```kotlin
  val result = buildList {
      // Тип аргумента выводится в Float на основе ожидаемого типа
      val x: Float = get(0)
  } // result имеет тип List<Float>
  ```

  ```kotlin
  class Foo<T> {
      val items = mutableListOf<T>()
  }

  fun <K> myBuilder(builder: Foo<K>.() -> Unit): Foo<K> = Foo<K>().apply(builder)

  fun main() {
      val result = myBuilder {
          val x: List<CharSequence> = items
          // ...
      } // result имеет тип Foo<CharSequence>
  }
  ```

<!-- * Passing postponed type variables' types into methods that expect concrete types -->
* Передачу типов переменных отложенного типа в методы, которые ожидают конкретных типов;

  ```kotlin
  fun takeMyLong(x: Long) { /*...*/ }

  fun String.isMoreThat3() = length > 3

  fun takeListOfStrings(x: List<String>) { /*...*/ }

  fun main() {
      val result1 = buildList {
          val x = get(0)
          takeMyLong(x)
      } // result1 имеет тип List<Long>

      val result2 = buildList {
          val x = get(0)
          val isLong = x.isMoreThat3()
      // ...
      } // result2 имеет тип List<String>
  
      val result3 = buildList {
          takeListOfStrings(this)
      } // result3 имеет тип List<String>
  }
  ```

<!-- * Taking a callable reference to the lambda receiver's member -->
* Получение вызываемой ссылки на член лямбда-получателя.

  ```kotlin
  fun main() {
      val result = buildList {
          val x: KFunction1<Int, Float> = ::get
      } // result имеет тип List<Float>
  }
  ```

  ```kotlin
  fun takeFunction(x: KFunction1<Int, Float>) { ... }

  fun main() {
      val result = buildList {
          takeFunction(::get)
      } // result имеет тип List<Float>
  }
  ```

<!-- At the end of the analysis, builder inference considers all collected type information and tries to merge it into
the resulting type. See the example. -->
В конце анализа вывод строителя рассматривает всю собранную информацию о типе и пытается объединить ее в результирующий
тип. Смотрите пример.

```kotlin
val result = buildList { // Вывод переменной отложенного типа E
    // Учитывание, что E - это Number или подтип Number
    val n: Number? = getOrNull(0)
    // Учитывание, что E - это Int или супертип Int
    add(1)
    // E выводится в Int
} // result имеет тип List<Int>
```

<!-- The resulting type is the most specific type that corresponds to the type information collected during the analysis.
If the given type information is contradictory and cannot be merged, the compiler reports an error. -->
Результирующий тип является наиболее специфичным типом, который соответствует информации о типе, собранной в ходе
анализа. Если данная информация о типе противоречива и не может быть объединена, компилятор сообщает об ошибке.

<!-- Note that the Kotlin compiler uses builder inference only if regular type inference cannot infer a type argument.
This means you can contribute type information outside a builder lambda, and then builder inference analysis is not
required. Consider the example: -->
Обратите внимание, что компилятор Kotlin использует вывод строителя только в том случае, если обычный вывод типа не
может вывести аргумент типа. Это означает, что вы можете вносить информацию о типе за пределами лямбды строителя, и
тогда анализ вывода строителя не требуется. Рассмотрим пример:

```kotlin
fun someMap() = mutableMapOf<CharSequence, String>()

fun <E> MutableMap<E, String>.f(x: MutableMap<E, String>) { ... }

fun main() {
    val x: Map<in String, String> = buildMap {
        put("", "")
        f(someMap()) // Несоответствие типа (требуется String, найдено CharSequence)
    }
}
```

<!-- Here a type mismatch appears because the expected type of the map is specified outside the builder lambda.
The compiler analyzes all the statements inside with the fixed receiver type `Map<in String, String>`. -->
Здесь возникает несоответствие типов, поскольку ожидаемый тип map указан вне лямбды конструктора.
Компилятор анализирует все операторы внутри с фиксированным типом получателя `Map<in String, String>`.
