---
type: doc
layout: reference
category: "Syntax"
title: "Приведение и проверка типов"
url: https://kotlinlang.ru/docs/typecasts.html
---

<!-- При переводе статьи оригинальная версия была от 25 November 2025 -->

<!-- Type checks and casts -->
# Приведение и проверка типов

В Kotlin во время выполнения можно делать с типами две вещи: проверять, относится ли объект к определённому типу,
или преобразовывать его в другой тип. **Проверки** типов помогают понять, с каким объектом вы работаете,
а **приведения** типов пытаются преобразовать объект к другому типу.

> О проверках и приведениях именно **обобщённых** типов, например `List<T>` или `Map<K, V>`, читайте в разделе
> [Проверки и приведения обобщённых типов](generics.html#generics-type-checks-and-casts).

<a name="is-and-is-operators"></a>

<!-- ## Checks with `is` and `!is` operators -->
## Проверки с операторами `is` и `!is`

Используйте оператор `is` или его отрицание `!is`, чтобы проверить, соответствует ли объект заданному типу
во время выполнения:

```kotlin
fun main() {
    val input: Any = "Hello, Kotlin"

    if (input is String) {
        println("Длина сообщения: ${input.length}")
        // Длина сообщения: 13
    }

    if (input !is String) { // то же самое, что и !(input is String)
        println("Ввод не является допустимым сообщением")
    } else {
        println("Обработка сообщения: ${input.length} символов")
        // Обработка сообщения: 13 символов
    }
}
```

Операторы `is` и `!is` также можно использовать, чтобы проверить, соответствует ли объект подтипу:

```kotlin
interface Animal {
    val name: String
    fun speak()
}

class Dog(override val name: String) : Animal {
    override fun speak() = println("$name говорит: Гав!")
}

class Cat(override val name: String) : Animal {
    override fun speak() = println("$name говорит: Мяу!")
}
//sampleStart
fun handleAnimal(animal: Animal) {
    println("Обработка животного: ${animal.name}")
    animal.speak()

    // Используйте оператор is для проверки подтипов
    if (animal is Dog) {
        println("Особые указания по уходу: это собака.")
    } else if (animal is Cat) {
        println("Особые указания по уходу: это кошка.")
    }
}
//sampleEnd
fun main() {
    val pets: List<Animal> = listOf(
        Dog("Buddy"),
        Cat("Whiskers"),
        Dog("Rex")
    )

    for (pet in pets) {
        handleAnimal(pet)
        println("---")
    }
    // Обработка животного: Buddy
    // Buddy говорит: Гав!
    // Особые указания по уходу: это собака.
    // ---
    // Обработка животного: Whiskers
    // Whiskers говорит: Мяу!
    // Особые указания по уходу: это кошка.
    // ---
    // Обработка животного: Rex
    // Rex говорит: Гав!
    // Особые указания по уходу: это собака.
    // ---
}
```

В этом примере оператор `is` используется, чтобы проверить, имеет ли экземпляр класса `Animal` подтип `Dog` или `Cat`,
и вывести соответствующие указания по уходу.

Можно проверить, является ли объект супертипом своего объявленного типа, но в этом нет смысла: ответ всегда будет
положительным. Экземпляр каждого класса уже является экземпляром своих супертипов.

> О том, как определить тип объекта во время выполнения, читайте в разделе [Рефлексия](reflection.html).

<!-- ## Type casts -->
## Приведения типов

Преобразование типа объекта в Kotlin к другому типу называется **приведением типов**.

В некоторых случаях компилятор автоматически приводит объекты за вас. Это называется умным приведением.

Если тип нужно привести явно, используйте операторы приведения `as?` или `as`
([подробнее ниже](#unsafe-cast-operator)).

<a name="smart-casts"></a>

<!-- ## Smart casts -->
## Умные приведения

Компилятор отслеживает проверки типов и [явные приведения](#unsafe-cast-operator) для неизменяемых значений
и автоматически вставляет неявные безопасные приведения:

```kotlin
fun logMessage(data: Any) {
    // data автоматически приводится к String
    if (data is String) {
        println("Получен текст: ${data.length} символов")
    }
}

fun main() {
    logMessage("Server started")
    // Получен текст: 14 символов
    logMessage(404)
}
```

Компилятор достаточно умён, чтобы понимать, что приведение безопасно, если отрицательная проверка приводит к выходу
из функции:

```kotlin
fun logMessage(data: Any) {
    // data автоматически приводится к String
    if (data !is String) return

    println("Получен текст: ${data.length} символов")
}

fun main() {
    logMessage("User signed in")
    // Получен текст: 14 символов
    logMessage(true)
}
```

<!-- ### Control flow -->
### Поток управления

Умные приведения работают не только в условных выражениях `if`, но и в
[`when`-выражениях](control-flow.html#when-expressions-and-statements):

```kotlin
fun processInput(data: Any) {
    when (data) {
        // data автоматически приводится к Int
        is Int -> println("Log: назначен новый ID ${data + 1}")
        // data автоматически приводится к String
        is String -> println("Log: получено сообщение \"$data\"")
        // data автоматически приводится к IntArray
        is IntArray -> println("Log: обработаны баллы, сумма = ${data.sum()}")
    }
}

fun main() {
    processInput(1001)
    // Log: назначен новый ID 1002
    processInput("System rebooted")
    // Log: получено сообщение "System rebooted"
    processInput(intArrayOf(10, 20, 30))
    // Log: обработаны баллы, сумма = 60
}
```

Они также работают в [циклах `while`](control-flow.html#while-loops):

```kotlin
sealed interface Status
data class Ok(val currentRoom: String) : Status
data object Error : Status

class RobotVacuum(val rooms: List<String>) {
    var index = 0

    fun status(): Status =
        if (index < rooms.size) Ok(rooms[index])
        else Error

    fun clean(): Status {
        println("Завершена уборка: ${rooms[index]}")
        index++
        return status()
    }
}

fun main() {
    //sampleStart
    val robo = RobotVacuum(listOf("Living Room", "Kitchen", "Hallway"))

    var status: Status = robo.status()
    while (status is Ok) {
        // Компилятор умно приводит status к типу Ok,
        // поэтому свойство currentRoom доступно.
        println("Уборка комнаты ${status.currentRoom}...")
        status = robo.clean()
    }
    // Уборка комнаты Living Room...
    // Завершена уборка: Living Room
    // Уборка комнаты Kitchen...
    // Завершена уборка: Kitchen
    // Уборка комнаты Hallway...
    // Завершена уборка: Hallway
    //sampleEnd
}
```

В этом примере запечатанный интерфейс `Status` имеет две реализации: data-класс `Ok` и data-объект `Error`.
Только у data-класса `Ok` есть свойство `currentRoom`. Когда условие цикла `while` истинно, компилятор умно приводит
переменную `status` к типу `Ok`, делая свойство `currentRoom` доступным в теле цикла.

Если перед использованием в условии `if`, `when` или `while` объявить переменную типа `Boolean`, вся информация,
которую компилятор собрал об этой переменной, будет доступна в соответствующем блоке для умного приведения.

Это полезно, например, когда вы хотите вынести булевы условия в переменные. Тогда переменной можно дать осмысленное имя,
что улучшает читаемость кода и позволяет повторно использовать переменную позже:

```kotlin
class Cat {
    fun purr() {
        println("Мур-мур")
    }
}
//sampleStart
fun petAnimal(animal: Any) {
    val isCat = animal is Cat
    if (isCat) {
        // Компилятор может использовать информацию об isCat,
        // поэтому он знает, что animal был умно приведён
        // к типу Cat.
        // Следовательно, можно вызвать функцию purr().
        animal.purr()
    }
}

fun main() {
    val kitty = Cat()
    petAnimal(kitty)
    // Мур-мур
}
//sampleEnd
```

<!-- ### Logical operators -->
### Логические операторы

Компилятор может выполнять умные приведения справа от операторов `&&` или `||`, если слева находится проверка типа
(обычная или отрицательная):

```kotlin
// x автоматически приводится к String справа от `||`
if (x !is String || x.length == 0) return

// x автоматически приводится к String справа от `&&`
if (x is String && x.length > 0) {
    print(x.length) // x автоматически приводится к String
}
```

Если объединить проверки типов для объектов с помощью оператора «или» (`||`), умное приведение будет выполнено
к их ближайшему общему супертипу:

```kotlin
interface Status {
    fun signal() {}
}

interface Ok : Status
interface Postponed : Status
interface Declined : Status

fun signalCheck(signalStatus: Any) {
    if (signalStatus is Postponed || signalStatus is Declined) {
        // signalStatus умно приводится к общему супертипу Status
        signalStatus.signal()
    }
}
```

> Общий супертип — это **аппроксимация** [объединённого типа](https://en.wikipedia.org/wiki/Union_type).
> Объединённые типы [сейчас не поддерживаются в Kotlin](https://youtrack.jetbrains.com/issue/KT-13108/Denotable-union-and-intersection-types).

<!-- ### Inline functions -->
### Inline-функции

Компилятор может умно приводить переменные, захваченные в лямбда-функциях, которые передаются в
[inline-функции](inline-functions.html).

Inline-функции считаются имеющими неявный контракт
[`callsInPlace`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.contracts/-contract-builder/calls-in-place.html).
Это означает, что любые лямбда-функции, переданные в inline-функцию, вызываются на месте. Так как лямбда-функции
вызываются на месте, компилятор знает, что лямбда-функция не может утечь со ссылками на переменные,
которые находятся в её теле.

Компилятор использует это знание вместе с другими видами анализа, чтобы решить, безопасно ли умно приводить
захваченные переменные. Например:

```kotlin
interface Processor {
    fun process()
}

inline fun inlineAction(f: () -> Unit) = f()

fun nextProcessor(): Processor? = null

fun runProcessor(): Processor? {
    var processor: Processor? = null
    inlineAction {
        // Компилятор знает, что processor — локальная переменная,
        // а inlineAction() — inline-функция, поэтому ссылки на processor
        // не могут утечь. Следовательно, умное приведение processor безопасно.

        // Если processor не равен null, он умно приводится
        if (processor != null) {
            // Компилятор знает, что processor не равен null,
            // поэтому безопасный вызов не нужен.
            processor.process()
        }

        processor = nextProcessor()
    }

    return processor
}
```

<!-- ### Exception handling -->
### Обработка исключений

Информация об умном приведении передаётся в блоки `catch` и `finally`. Это делает код безопаснее, потому что
компилятор отслеживает, имеет ли объект nullable-тип. Например:

```kotlin
//sampleStart
fun testString() {
    var stringInput: String? = null
    // stringInput умно приводится к типу String
    stringInput = ""
    try {
        // Компилятор знает, что stringInput не равен null
        println(stringInput.length)
        // 0

        // Компилятор отбрасывает прежнюю информацию об умном приведении
        // для stringInput. Теперь stringInput имеет тип String?.
        stringInput = null

        // Вызвать исключение
        if (2 > 1) throw Exception()
        stringInput = ""
    } catch (exception: Exception) {
        // Компилятор знает, что stringInput может быть null,
        // поэтому stringInput остаётся nullable.
        println(stringInput?.length)
        // null
    }
}
//sampleEnd
fun main() {
    testString()
}
```

<!-- ### Smart cast prerequisites -->
### Предварительные условия для умных приведений

Умные приведения работают только тогда, когда компилятор может гарантировать, что переменная не изменится между
проверкой и использованием. Их можно применять в следующих условиях:

* локальные переменные `val` — всегда, за исключением [локальных делегированных свойств](delegated-properties.html);
* свойства `val` — если свойство имеет модификатор `private` или `internal`, либо если проверка выполняется
в том же [модуле](visibility-modifiers.html#modules), где объявлено это свойство. Умные приведения нельзя использовать
для свойств `open` или свойств с пользовательскими getter'ами;
* локальные переменные `var` — если переменная не изменяется между проверкой и использованием, не захватывается
лямбдой, которая её изменяет, и не является локальным делегированным свойством;
* свойства `var` — никогда, потому что переменная может быть изменена другим кодом в любой момент.

<a name="unsafe-cast-operator"></a>

<!-- ## `as` and `as?` cast operators -->
## Операторы приведения `as` и `as?`

В Kotlin есть два оператора приведения: `as` и `as?`. Оба можно использовать для приведения типов, но их поведение
отличается.

Если приведение с помощью оператора `as` завершается неудачно, во время выполнения выбрасывается `ClassCastException`.
Поэтому этот оператор также называют **небезопасным**. `as` можно использовать при приведении к non-null типу:

```kotlin
fun main() {
    val rawInput: Any = "user-1234"

    // Успешно приводит к String
    val userId = rawInput as String
    println("Вход пользователя с ID: $userId")
    // Вход пользователя с ID: user-1234

    // Вызывает ClassCastException
    val wrongCast = rawInput as Int
    println("wrongCast содержит: $wrongCast")
    // Exception in thread "main" java.lang.ClassCastException
}
```

<a name="safe-nullable-cast-operator"></a>

Если вместо этого использовать оператор `as?`, и приведение завершится неудачно, оператор вернёт `null`.
Поэтому его также называют **безопасным** оператором:

```kotlin
fun main() {
    val rawInput: Any = "user-1234"

    // Успешно приводит к String
    val userId = rawInput as? String
    println("Вход пользователя с ID: $userId")
    // Вход пользователя с ID: user-1234

    // Присваивает wrongCast значение null
    val wrongCast = rawInput as? Int
    println("wrongCast содержит: $wrongCast")
    // wrongCast содержит: null
}
```

Чтобы безопасно привести nullable-тип, используйте оператор `as?`: он не вызовет `ClassCastException`, если приведение
завершится неудачно.

Оператор `as` _можно_ использовать с nullable-типом. Это позволяет результату быть `null`, но всё равно вызывает
`ClassCastException`, если приведение неуспешно. Поэтому `as?` — более безопасный вариант:

```kotlin
fun main() {
    val config: Map<String, Any?> = mapOf(
        "username" to "kodee",
        "alias" to null,
        "loginAttempts" to 3
    )

    // Небезопасно приводит к nullable String
    val username: String? = config["username"] as String?
    println("Username: $username")
    // Username: kodee

    // Небезопасно приводит null-значение к nullable String
    val alias: String? = config["alias"] as String?
    println("Alias: $alias")
    // Alias: null

    // Не может привести к nullable String и выбрасывает ClassCastException
    // val unsafeAttempts: String? = config["loginAttempts"] as String?
    // println("Login attempts (unsafe): $unsafeAttempts")
    // Exception in thread "main" java.lang.ClassCastException

    // Не может привести к nullable String и возвращает null
    val safeAttempts: String? = config["loginAttempts"] as? String
    println("Login attempts (safe): $safeAttempts")
    // Login attempts (safe): null
}
```

<!-- ### Up and downcasting -->
### Восходящее и нисходящее приведение

В Kotlin можно приводить объекты к супертипам и подтипам.

Приведение объекта к экземпляру его суперкласса называется **восходящим приведением**. Для восходящего приведения
не нужен специальный синтаксис или операторы приведения. Например:

```kotlin
interface Animal {
    fun makeSound()
}

class Dog : Animal {
    // Реализует поведение makeSound()
    override fun makeSound() {
        println("Собака говорит: гав!")
    }
}

fun printAnimalInfo(animal: Animal) {
    animal.makeSound()
}

fun main() {
    val dog = Dog()
    // Приводит экземпляр Dog к Animal
    printAnimalInfo(dog)
    // Собака говорит: гав!
}
```

В этом примере, когда функция `printAnimalInfo()` вызывается с экземпляром `Dog`, компилятор приводит его к `Animal`,
потому что именно этот тип ожидается в параметре. Фактический объект всё ещё остаётся экземпляром `Dog`, поэтому
компилятор динамически разрешает функцию `makeSound()` из класса `Dog` и выводит `"Собака говорит: гав!"`.

Явное восходящее приведение часто встречается в API Kotlin, где поведение зависит от абстрактного типа. Это также
распространено в Jetpack Compose и UI-инструментариях, которые обычно рассматривают все UI-элементы как супертипы,
а затем работают с конкретными подклассами:

```kotlin
val textView = TextView(this)
textView.text = "Hello, View!"

// Восходящее приведение от TextView к View
val view: View = textView

// Использование функций View
view.setPadding(20, 20, 20, 20)
// Activity ожидает тип View
setContentView(view)
```

Приведение объекта к экземпляру подкласса называется **нисходящим приведением**. Поскольку нисходящее приведение
может быть небезопасным, нужно использовать явные операторы приведения. Чтобы не выбрасывать исключения при
неудачном приведении, рекомендуется использовать безопасный оператор приведения `as?`, который возвращает `null`,
если приведение не удалось:

```kotlin
interface Animal {
    fun makeSound()
}

class Dog : Animal {
    override fun makeSound() {
        println("Собака говорит: гав!")
    }

    fun bark() {
        println("ГАВ!")
    }
}

fun main() {
    // Создаёт animal как экземпляр Dog
    // с типом Animal
    val animal: Animal = Dog()

    // Безопасно приводит animal к типу Dog
    val dog: Dog? = animal as? Dog

    // Использует безопасный вызов, чтобы вызвать bark(),
    // если dog не равен null
    dog?.bark()
    // "ГАВ!"
}
```

В этом примере `animal` объявлен с типом `Animal`, но содержит экземпляр `Dog`. Код безопасно приводит `animal`
к типу `Dog` и использует [безопасный вызов](null-safety.html#safe-call-operator) (`?.`), чтобы обратиться
к функции `bark()`.

Нисходящее приведение используется в сериализации при десериализации базового класса в конкретный подтип.
Оно также распространено при работе с Java-библиотеками, которые возвращают объекты супертипов: в Kotlin их может
потребоваться привести к нужному подтипу.
