---
type: doc
layout: reference
category: Collections
title: "Map: специфичные операции"
url: https://kotlinlang.ru/docs/map-operations.html
---

<!-- # Map-specific operations -->
# Map: специфичные операции

<!-- In [maps](collections-overview.md#map), types of both keys and values are user-defined.
Key-based access to map entries enables various map-specific processing capabilities from getting a value by key to separate
filtering of keys and values.
On this page, we provide descriptions of the map processing functions from the standard library. -->
В [`Map`](collections-overview.html#map) типы ключей и значений определяются пользователем. При этом благодаря доступу к значениям по их ключу открываются различные возможности для обработки записей: от получения значения по ключу, до фильтрации ключей и значений по отдельности.
В этом разделе находится описание функций из стандартной библиотеки для обработки ассоциативных списков.


<a name="retrieve-keys-and-values"></a>
<!-- ## Retrieve keys and values -->
## Получение ключей и значений

<!-- For retrieving a value from a map, you must provide its key as an argument of the [`get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/get.html) function.
The shorthand `[key]` syntax is also supported. If the given key is not found, it returns `null`.
There is also the function [`getValue()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-value.html)
which has slightly different behavior: it throws an exception if the key is not found in the map.
Additionally, you have two more options to handle the key absence:
* [`getOrElse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-else.html) works the same way as for lists: the values for non-existent keys are returned from the given lambda function.
* [`getOrDefault()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-default.html) returns the specified default value if the key is not found. -->
Для того чтобы получить значение из `Map`, вы должны передать его ключ в качестве аргумента функции
[`get()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/get.html). Также поддерживается сокращённый синтаксис - `[key]`. Если такой ключ не найден, то вернётся `null`.
Помимо этого существует функция [`getValue()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-value.html), которая в случае отсутствия ключа бросит исключение.
Также есть еще две функции для решения проблемы отсутствия ключа:
* [`getOrElse()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-else.html) - работает так же, как и для списков: если ключ не найден, то вернётся результат выполнения лямбды.
* [`getOrDefault()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-default.html) - если ключ не найден, то вернёт заданное значение по умолчанию.

```kotlin
fun main() {
    val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
    println(numbersMap.get("one")) // 1
    println(numbersMap["one"]) // 1
    println(numbersMap.getOrDefault("four", 10)) // 10
    println(numbersMap["five"]) // null
    //numbersMap.getValue("six") // exception!
}
```

<!-- To perform operations on all keys or all values of a map, you can retrieve them from the properties `keys` and `values` accordingly.
`keys` is a set of all map keys and `values` is a collection of all map values. -->
Если для выполнения операций вам требуются все ключи или все значения из `Map`, то воспользуйтесь свойствами `keys` и `values`. `keys` - это набор (`Set`) всех ключей, а `values` - коллекция всех значений.

```kotlin
fun main() {
    val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
    println(numbersMap.keys) // [one, two, three]
    println(numbersMap.values) // [1, 2, 3]
}
```


<a name="filter"></a>
<!-- ## Filter -->
## Фильтрация

<!-- You can [filter](collection-filtering.md) maps with the [`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html) function as well as other collections.
When calling `filter()` on a map, pass to it a predicate with a `Pair` as an argument.
This enables you to use both the key and the value in the filtering predicate. -->
Вы можете [фильтровать](collection-filtering.html) ассоциативный список с помощью функции
[`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html) как и другие коллекции. Для этого передайте `filter()` предикат с `Pair` в качестве аргумента, что позволит использовать и ключ, и значение в предикате.

```kotlin
fun main() {
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
    println(filteredMap) // {key11=11}
}
```

<!-- There are also two specific ways for filtering maps: by keys and by values.
For each way, there is a function: [`filterKeys()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-keys.html) and [`filterValues()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-values.html).
Both return a new map of entries which match the given predicate.
The predicate for `filterKeys()` checks only the element keys, the one for `filterValues()` checks only values. -->
Помимо этого существует две функции фильтрации, специфичные именно для ассоциативного списка:
[`filterKeys()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-keys.html) для фильтра по ключам и [`filterValues()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-values.html) для фильтра по значениям. Обе возвращают новый ассоциативный список, в котором все записи соответствуют заданному предикату. Предикат функции `filterKeys()` проверяет только ключи элементов, а предикат функции `filterValues()` проверяет только значения.

```kotlin
fun main() {
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    val filteredKeysMap = numbersMap.filterKeys { it.endsWith("1") }
    val filteredValuesMap = numbersMap.filterValues { it < 10 }

    println(filteredKeysMap) // {key1=1, key11=11}
    println(filteredValuesMap) // {key1=1, key2=2, key3=3}
}
```


<a name="plus-and-minus-operators"></a>
<!-- ## Plus and minus operators -->
## Операторы `plus` и `minus`

<!-- Due to the key access to elements, [`plus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus.html) (`+`) and [`minus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus.html)
(`-`) operators work for maps differently than for other collections.
`plus` returns a `Map` that contains elements of its both operands: a `Map` on the left and a `Pair` or another `Map` on the right.
When the right-hand side operand contains entries with keys present in the left-hand side `Map`, the result map contains
the entries from the right side. -->
Из-за наличия доступа к элементам по их ключам операторы
[`plus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus.html) (`+`) и
[`minus`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus.html) (`-`) работают с ассоциативными списками иначе, чем с другими коллекциями.
Оператор `plus` возвращает `Map`, которая содержит в себе элементы обоих операндов: первым операндом должен быть `Map`, а вторым может быть `Pair` или другая `Map`. Если второй операнд содержит в себе записи с ключами, которые есть в первом операнде, то в результирующую `Map` попадут записи из второго операнда.

```kotlin
fun main() {
    val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
    println(numbersMap + Pair("four", 4)) // {one=1, two=2, three=3, four=4}
    println(numbersMap + Pair("one", 10)) // {one=10, two=2, three=3}
    println(numbersMap + mapOf("five" to 5, "one" to 11)) // {one=11, two=2, three=3, five=5}
}
```

<!-- `minus` creates a `Map` from entries of a `Map` on the left except those with keys from the right-hand side operand.
So, the right-hand side operand can be either a single key or a collection of keys: list, set, and so on. -->
Оператор `minus` создаёт `Map` на основе первого операнда, исключая те записи, ключи которых есть во втором операнде. Таким образом, второй операнд может быть либо одним ключом, либо коллекцией ключей: списком, множеством и так далее.

```kotlin
fun main() {
    val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
    println(numbersMap - "one") // {two=2, three=3}
    println(numbersMap - listOf("two", "four")) // {one=1, three=3}
}
```

<!-- For details on using [`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) (`+=`) and [`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html)
(`-=`) operators on mutable maps, see [Map write operations](#map-write-operations) below. -->
Подробнее об использовании операторов
[`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) (`+=`) и
[`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) (`-=`) с изменяемыми ассоциативными списками см. ниже [Операции записи](#map-write-operations).


<a name="map-write-operations"></a>
<!-- ## Map write operations -->
## Операции записи

<!-- [Mutable](collections-overview.md#collection-types) maps offer map-specific write operations.
These operations let you change the map content using the key-based access to the values. -->
[Изменяемые](collections-overview.html#collection-types) ассоциативные списки предлагают специфичные для них операции записи. Такие операции позволяют изменять содержимое ассоциативного списка, используя доступ к значениям по ключу.

<!-- There are certain rules that define write operations on maps:
* Values can be updated. In turn, keys never change: once you add an entry, its key is constant.
* For each key, there is always a single value associated with it. You can add and remove whole entries. -->
Все операции записи придерживаются следующих правил:
* Значения можно обновлять. В свою очередь, ключи никогда не меняются: как только вы добавили запись, её ключ остаётся неизменным.
* Для каждого ключа всегда есть одно значение, связанное с ним. Вы можете добавлять и удалять записи целиком.

<!-- Below are descriptions of the standard library functions for write operations available on mutable maps. -->
Ниже описаны функции для операций записи из стандартной библиотеки, доступные для использования с изменяемыми ассоциативными списками.

<a name="add-and-update-entries"></a>
<!-- ### Add and update entries -->
### Добавление и обновление записи

<!-- To  add a new key-value pair to a mutable map, use [`put()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/put.html).
When a new entry is put into a `LinkedHashMap` (the default map implementation), it is added so that it comes last when
iterating the map. In sorted maps, the positions of new elements are defined by the order of their keys.  -->
Для добавления в изменяемый ассоциативный список новой пары "ключ-значение" используйте функцию
[`put()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/put.html). Когда новая запись помещается в `LinkedHashMap` (реализация `Map` по умолчанию), она добавляется в неё таким образом, что при итерации `Map` она отображается последней. В отсортированных ассоциативных списках положение новых элементов определяется порядком их ключей.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2)
    numbersMap.put("three", 3)
    println(numbersMap) // {one=1, two=2, three=3}
}
```

<!-- To add multiple entries at a time, use [`putAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/put-all.html).
Its argument can be a `Map` or a group of `Pair`s: `Iterable`, `Sequence`, or `Array`. -->
Для добавления нескольких записей за раз, используйте функцию
[`putAll()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/put-all.html). В качестве аргумента она принимает `Map` или группу из `Pair`: `Iterable`, `Sequence`, или `Array`.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
    numbersMap.putAll(setOf("four" to 4, "five" to 5))
    println(numbersMap) // {one=1, two=2, three=3, four=4, five=5}
}
```

<!-- Both `put()` and `putAll()` overwrite the values if the given keys already exist in the map. Thus, you can use them to update
values of map entries. -->
И `put()`, и `putAll()` перезаписывают значения, если указанные ключи уже существуют в `Map`. Поэтому вы можете использовать их, чтобы обновить значения записей.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2)
    val previousValue = numbersMap.put("one", 11)
    println("value associated with 'one', before: $previousValue, after: ${numbersMap["one"]}") // 11
    println(numbersMap) // {one=11, two=2}
}
```

<!-- You can also add new entries to maps using the shorthand operator form. There are two ways:
* [`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) (`+=`) operator.
* the `[]` operator alias for `set()`.  -->
Помимо этого, вы можете использовать упрощённый вариант для добавления записей в ассоциативный список. Есть два способа:
* Оператор [`plusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) (`+=`).
* `[]`.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2)
    numbersMap["three"] = 3     // calls numbersMap.put("three", 3)
    numbersMap += mapOf("four" to 4, "five" to 5)
    println(numbersMap) // {one=1, two=2, three=3, four=4, five=5}
}
```

<!-- When called with the key present in the map, operators overwrite the values of the corresponding entries.  -->
Если вызвать эти операторы с ключом, который уже существует в ассоциативном списке, то его значение будет перезаписано.

<a name="remove-entries"></a>
<!-- ### Remove entries -->
### Удаление записей

<!-- To remove an entry from a mutable map, use the [`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/remove.html) function.
When calling `remove()`, you can pass either a key or a whole key-value-pair.
If you specify both the key and value, the element with this key will be removed only if its value matches the second argument.  -->
Для удаления записи из изменяемой `Map`, используйте функцию
[`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/remove.html). При вызове `remove()`, вы можете передать ей либо только ключ, либо и ключ, и его значение. При этом, если вы передадите и ключ, и значение, то элемент с этим ключом будет удалён только в том случае, если его значение совпадает с переданным значением.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
    numbersMap.remove("one")
    println(numbersMap) // {two=2, three=3}
    numbersMap.remove("three", 4) // ничего не удаляет
    println(numbersMap) // {two=2, three=3}
}
```

<!-- You can also remove entries from a mutable map by their keys or values.
To do this, call `remove()` on the map's keys or values providing the key or the value of an entry.
When called on values, `remove()` removes only the first entry with the given value. -->
Также вы можете удалять записи из ассоциативного списка используя свойства `keys` или `values`: просто вызовите функцию `remove()` для них. При вызове `remove()` с `values`, будет удалена только первая запись с таким значением.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3, "threeAgain" to 3)
    numbersMap.keys.remove("one")
    println(numbersMap) // {two=2, three=3, threeAgain=3}
    numbersMap.values.remove(3)
    println(numbersMap) // {two=2, threeAgain=3}
}
```

<!-- The [`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) (`-=`) operator
is also available for mutable maps. -->
Оператор [`minusAssign`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) (`-=`) для изменяемых `Map` тоже доступен.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
    numbersMap -= "two"
    println(numbersMap) // {one=1, three=3}
    numbersMap -= "five" // ничего не удаляет
    println(numbersMap) // {one=1, three=3}
}
```
