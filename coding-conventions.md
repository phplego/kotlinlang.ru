---
type: doc
layout: reference
category: Basics
title: Стилистика кода
url: https://kotlinlang.ru/docs/coding-conventions.html
---

<!-- При переводе статьи оригинальная версия была от 14 January 2022 -->

<!-- # Coding conventions -->
# Соглашение о стилистике кода

<!-- Commonly known and easy-to-follow coding conventions are vital for any programming language.
Here we provide guidelines on the code style and code organization for projects that use Kotlin. -->
Общеизвестные и простые в использовании соглашения о стилистике кода жизненно важны для любого языка программирования.
Здесь мы предоставляем рекомендации по стилю кода и его организации для проектов, использующих Kotlin.

<a name="configure-style-in-ide"></a>

<!-- ## Configure style in IDE -->
## Настройка стиля в IDE

<!-- Two most popular IDEs for Kotlin - [IntelliJ IDEA](https://www.jetbrains.com/idea/) and [Android Studio](https://developer.android.com/studio/)
provide powerful support for code styling. You can configure them to automatically format your code in consistence with
the given code style. -->
Две самые популярные IDE для Kotlin - [IntelliJ IDEA](https://www.jetbrains.com/idea/) и [Android Studio](https://developer.android.com/studio/)
обеспечивают широкую поддержку стиля кода. Вы можете настроить их для автоматического форматирования вашего кода в соответствии с заданным стилем.

<a name="apply-the-style-guide"></a>

<!-- ### Apply the style guide -->
### Примените руководства по стилю

<!-- 1. Go to **Settings/Preferences | Editor | Code Style | Kotlin**.
2. Click **Set from...**.
3. Select **Kotlin style guide** . -->

1. Перейдите в раздел **Settings/Preferences | Editor | Code Style | Kotlin**.
2. Нажмите **Set from...**.
3. Выберите **Kotlin style guide**.

<a name="verify-that-your-code-follows-the-style-guide"></a>

<!-- ### Verify that your code follows the style guide -->
### Убедитесь, что ваш код соответствует руководству по стилю

<!-- 1. Go to **Settings/Preferences | Editor | Inspections | Kotlin**.
2. Open **Kotlin | Style issues**.
3. Switch on **File is not formatted according to project settings** inspection.
Additional inspections that verify other issues described in the style guide (such as naming conventions) are enabled by default. -->

1. Перейдите в раздел **Settings/Preferences | Editor | Code Style | Kotlin**.
2. Отройте **Kotlin | Style issues**.
3. Включите проверку **File is not formatted according to project settings**. Дополнительные проверки, которые проверяют другие проблемы,
описанные в руководстве по стилю (например, соглашения об именах), включены по умолчанию.

<a name="source-code-organization"></a>

<!-- ## Source code organization -->
## Организация кода

<a name="directory-structure"></a>

<!-- ### Directory structure -->
### Структура каталогов

<!-- In pure Kotlin projects, the recommended directory structure follows the package structure with
the common root package omitted. For example, if all the code in the project is in the `org.example.kotlin` package and its
subpackages, files with the `org.example.kotlin` package should be placed directly under the source root, and
files in `org.example.kotlin.network.socket` should be in the `network/socket` subdirectory of the source root. -->
В проектах, в которых используется только Kotlin, рекомендуемая структура каталогов соответствует структуре пакетов,
при этом общий корневой пакет опущен. Например, если весь код в проекте находится в пакете `org.example.kotlin` и его подпакетах,
файлы с пакетом `org.example.kotlin` должны размещаться непосредственно в корневом каталоге,
а файлы в `org.example.kotlin.network.socket` должны находиться в подкаталоге корневого каталога `network/socket`.

<!-- >On JVM: In projects where Kotlin is used together with Java, Kotlin source files should reside in the same 
>source root as the Java source files, and follow the same directory structure: each file should be stored in the 
>directory corresponding to each package statement. -->
> На JVM: в проектах, где Kotlin используется на ряду с Java, файлы Kotlin должны находиться в том же корне источника,
> что и исходные файлы Java, и следовать той же структуре каталогов:
> каждый файл должен храниться в каталоге, соответствующем каждой инструкции пакета.

<a name="source-file-names"></a>

<!-- ### Source file names -->
### Имена файлов

<!-- If a Kotlin file contains a single class (potentially with related top-level declarations), its name should be the same
as the name of the class, with the `.kt` extension appended. If a file contains multiple classes, or only top-level declarations,
choose a name describing what the file contains, and name the file accordingly.
Use [upper camel case](https://en.wikipedia.org/wiki/Camel_case) with an uppercase first letter (also known as Pascal case),
for example, `ProcessDeclarations.kt`. -->
Если Kotlin файл содержит один класс (возможно со связанными объявлениями верхнего уровня),
то его имя должно совпадать с именем этого класса с добавлением расширения `.kt`.
Если файл содержит несколько классов или только объявления верхнего уровня, выберите имя, описывающее содержимое файла,
и назовите файл соответствующим образом. Используйте [UpperCamelCase](https://en.wikipedia.org/wiki/Camel_case) (так же известный как Pascal case),
начиная с заглавной буквы, например, `ProcessDeclarations.kt`.

<!-- The name of the file should describe what the code in the file does. Therefore, you should avoid using meaningless
words such as `Util` in file names. -->
Имена файлов должны описывать, что в них делает код. Поэтому при наименовании файла вам следует избегать слов, не несущих смысла,
таких как `Util`.

<a name="source-file-organization"></a>

<!-- ### Source file organization -->
### Организация файла

<!-- Placing multiple declarations (classes, top-level functions or properties) in the same Kotlin source file is encouraged
as long as these declarations are closely related to each other semantically, and the file size remains reasonable
(not exceeding a few hundred lines). -->
Размещение нескольких объявлений (классов, функций верхнего уровня или свойств) в одном исходном файле Kotlin рекомендуется до тех пор,
пока эти объявления тесно связаны друг с другом семантически, а размер файла остается разумным (не более нескольких сотен строк).

<!-- In particular, when defining extension functions for a class which are relevant for all clients of this class,
put them in the same file with the class itself. When defining extension functions that make sense 
only for a specific client, put them next to the code of that client. Avoid creating files just to hold 
all extensions of some class. -->
В частности, при определении функций-расширения для класса, которые актуальны для всех клиентов этого класса,
поместите их в один файл с самим классом. При определении функций-расширения, которые имеют смысл только для конкретного клиента,
поместите их рядом с кодом этого клиента. Избегайте создания файлов только для хранения всех расширений какого-либо класса.

<a name="class-layout"></a>

<!-- ### Class layout -->
### Схема классов

<!-- The contents of a class should go in the following order: -->
Содержимое класса должно располагаться в следующем порядке:

<!-- 1. Property declarations and initializer blocks
2. Secondary constructors
3. Method declarations
4. Companion object -->
1. Объявления свойств и блоки инициализации
2. Дополнительные конструкторы
3. Объявления методов
4. Вспомогательный объект

<!-- Do not sort the method declarations alphabetically or by visibility, and do not separate regular methods
from extension methods. Instead, put related stuff together, so that someone reading the class from top to bottom can 
follow the logic of what's happening. Choose an order (either higher-level stuff first, or vice versa) and stick to it. -->
Не сортируйте объявления методов по алфавиту или по видимости и не отделяйте обычные методы от методов-расширения.
Вместо этого соберите связанный код вместе, чтобы тот, кто читает класс сверху вниз, мог следовать логике происходящего.
Выберите порядок (либо сначала материал более высокого уровня, либо наоборот) и придерживайтесь его.

<!-- Put nested classes next to the code that uses those classes. If the classes are intended to be used externally and aren't
referenced inside the class, put them in the end, after the companion object. -->
Поместите вложенные классы рядом с кодом, который их использует. Если эти классы предназначены для внешнего использования
и на них нет ссылок внутри класса, поместите их в конце, после сопутствующего объекта.

<a name="interface-implementation-layout"></a>

<!-- ### Interface implementation layout -->
### Схема реализации интерфейса

<!-- When implementing an interface, keep the implementing members in the same order as members of the interface (if necessary,
interspersed with additional private methods used for the implementation). -->
При реализации интерфейса располагайте элементы в том же порядке,
что и элементы интерфейса (при необходимости чередуйте их с дополнительными частными методами, используемыми для реализации).

<a name="overload-layout"></a>

<!-- ### Overload layout -->
### Расположение перегрузки

<!-- Always put overloads next to each other in a class. -->
Всегда помещайте перегрузки друг после друга в классе.

<a name="naming-rules"></a>

<!-- ## Naming rules -->
## Правила наименований

<!-- Package and class naming rules in Kotlin are quite simple: -->
Правила наименования пакетов и классов в Kotlin довольно просты:

<!-- * Names of packages are always lowercase and do not use underscores (`org.example.project`). Using multi-word
names is generally discouraged, but if you do need to use multiple words, you can either just concatenate them together
or use camel case (`org.example.myProject`). -->
* Имена пакетов всегда написаны в нижнем регистре и не содержат нижних подчеркиваний.
Использование имен, состоящих из нескольких слов, обычно не рекомендуется, но, если вы не можете их не использовать,
либо просто объедините их вместе, либо используйте при этом lowerCamelCase (`org.example.myProject`).

<!-- * Names of classes and objects start with an uppercase letter and use camel case: -->
* Имена классов и объектов начинаются с заглавной буквы и используют UpperCamelCase.

```kotlin
open class DeclarationProcessor { /*...*/ }

object EmptyDeclarationProcessor : DeclarationProcessor() { /*...*/ }
```

<a name="function-names"></a>

<!-- ### Function names -->
### Имена функций

<!-- Names of functions, properties and local variables start with a lowercase letter and use camel case and no underscores: -->
Имена функций, свойств и локальных переменных начинаются со строчной буквы и используют lowerCamelCase без нижнего подчеркивания.

```kotlin
fun processDeclarations() { /*...*/ }
var declarationCount = 1
```

<!-- Exception: factory functions used to create instances of classes can have the same name as the abstract return type: -->
Исключение: фабричные функции, используемые для создания экземпляров классов, могут иметь то же имя, что и абстрактный возвращаемый тип.

```kotlin
interface Foo { /*...*/ }

class FooImpl : Foo { /*...*/ }

fun Foo(): Foo { return FooImpl() }
```

<a name="names-for-test-methods"></a>

<!-- ### Names for test methods -->
### Имена тестовых методов

<!-- In tests (and **only** in tests), you can use method names with spaces enclosed in backticks.
Note that such method names are currently not supported by the Android runtime. Underscores in method names are
also allowed in test code. -->
В тестах (и **только** в тестах) вы можете использовать имена методов с пробелами, заключенными в обратный апостроф.
Обратите внимание, что такие имена методов в настоящее время не поддерживаются средой Android.
Подчеркивания в именах методов также разрешены в тестовом коде.

```kotlin
class MyTestCase {
     @Test fun `ensure everything works`() { /*...*/ }
     
     @Test fun ensureEverythingWorks_onAndroid() { /*...*/ }
}
```

<a name="property-names"></a>

<!-- ### Property names -->
### Имена свойств

<!-- Names of constants (properties marked with `const`, or top-level or object `val` properties with no custom `get` function
that hold deeply immutable data) should use uppercase underscore-separated ([screaming snake case](https://en.wikipedia.org/wiki/Snake_case))
names: -->
Имена констант (свойства, помеченные `const`, свойства верхнего уровня или объект `val` без функции `get`) должны использовать имена,
разделенные подчеркиванием и написанные в верхнем регистре ([SCREAMING_SNAKE_CASE](https://en.wikipedia.org/wiki/Snake_case)).

```kotlin
const val MAX_COUNT = 8
val USER_NAME_FIELD = "UserName"
```

<!-- Names of top-level or object properties which hold objects with behavior or mutable data should use camel case names: -->
Имена свойств верхнего уровня или объектов, которые содержат объекты с поведением или изменяемыми данными, должны использовать имена lowerCamelCase.

```kotlin
val mutableCollection: MutableSet<String> = HashSet()
```

<!-- Names of properties holding references to singleton objects can use the same naming style as `object` declarations: -->
Имена свойств, содержащих ссылки на одноэлементные объекты, могут использовать тот же стиль именования, что и объявления `object`.

```kotlin
val PersonComparator: Comparator<Person> = /*...*/
```

<!-- For enum constants, it's OK to use either uppercase underscore-separated names ([screaming snake case](https://en.wikipedia.org/wiki/Snake_case))
(`enum class Color { RED, GREEN }`) or upper camel case names, depending on the usage.  -->
Для констант перечисления можно использовать и имена, разделенные нижним подчеркиванием в верхнем регистре
([SCREAMING_SNAKE_CASE](https://en.wikipedia.org/wiki/Snake_case)) (`enum class Color { RED, GREEN }`),
и имена с использованием UpperCamelCase.

<a name="names-for-backing-properties"></a>

<!-- ### Names for backing properties -->
### Имена для вспомогательных свойств

<!-- If a class has two properties which are conceptually the same but one is part of a public API and another is an implementation
detail, use an underscore as the prefix for the name of the private property: -->
Если класс имеет два свойства, которые концептуально одинаковы, но одно из них является частью общедоступного API,
а другое - деталью реализации, используйте символ нижнего подчеркивания в начале имени частного свойства:

```kotlin
class C {
    private val _elementList = mutableListOf<Element>()
    
    val elementList: List<Element>
         get() = _elementList
}
```

<a name="choose-good-names"></a>

<!-- ### Choose good names -->
### Выбирайте хорошие имена

<!-- The name of a class is usually a noun or a noun phrase explaining what the class _is_: `List`, `PersonReader`. -->
Имя класса обычно представляет собой существительное или словосочетание, объясняющее, что это за класс: `List`, `PersonReader`.

<!-- The name of a method is usually a verb or a verb phrase saying what the method _does_: `close`, `readPersons`.
The name should also suggest if the method is mutating the object or returning a new one. For instance `sort` is
sorting a collection in place, while `sorted` is returning a sorted copy of the collection. -->
Имя метода обычно представляет собой глагол или фразу с глаголом, объясняющую, что делает этот метод: `close`, `readPersons`.
Имя также должно указывать, изменяет ли метод объект или возвращает новый. Так, `sort` сортирует коллекцию,
а `sorted` возвращает отсортированную копию коллекции.

<!-- The names should make it clear what the purpose of the entity is, so it's best to avoid using meaningless words
(`Manager`, `Wrapper`) in names. -->
Имена должны прояснять, какова цель того или иного элемента, поэтому лучше избегать использования бессмысленных слов
(`Manager`, `Wrapper`) в именах.

<!-- When using an acronym as part of a declaration name, capitalize it if it consists of two letters (`IOStream`);
capitalize only the first letter if it is longer (`XmlFormatter`, `HttpInputStream`). -->
При использовании аббревиатуры в качестве части имени объявления, пишите её в верхнем регистре, если она состоит из двух букв (`IOStream`);
если аббревиатура длиннее, заглавной следует оставить только первую букву (`XmlFormatter`, `HttpInputStream`).

<a name="formatting"></a>

<!-- ## Formatting -->
## Форматирование

<a name="indentation"></a>

<!-- ### Indentation -->
### Отступ

<!-- Use four spaces for indentation. Do not use tabs. -->
Используйте четыре пробела для отступа. Не используйте табуляцию.

<!-- For curly braces, put the opening brace in the end of the line where the construct begins, and the closing brace
on a separate line aligned horizontally with the opening construct. -->
Открывающую фигурную скобку поместите в конец строки, где начинается конструкция,
а закрывающую скобку на отдельной строке, выровненной по горизонтали с открывающей конструкцией.

```kotlin
if (elements != null) {
    for (element in elements) {
        // ...
    }
}
```

<!-- >In Kotlin, semicolons are optional, and therefore line breaks are significant. The language design assumes 
>Java-style braces, and you may encounter surprising behavior if you try to use a different formatting style. -->
> В Kotlin точка с запятой необязательна, и поэтому разрывы строк являются значимыми. Дизайн языка предполагает фигурные скобки в стиле Java,
> и вы можете столкнуться с неожиданным поведением, если попытаетесь использовать другой стиль форматирования.

<a name="horizontal-whitespace"></a>

<!-- ### Horizontal whitespace -->
### Пробелы

<!-- * Put spaces around binary operators (`a + b`). Exception: don't put spaces around the "range to" operator (`0..i`). -->
* Используйте пробелы вокруг двоичных операторов (`a + b`). Исключение: не ставьте пробелы вокруг оператора "диапазон до" (`0..i`).

<!-- * Do not put spaces around unary operators (`a++`). -->
* Не ставьте пробелы вокруг унарных операторов (`a++`).

<!-- * Put spaces between control flow keywords (`if`, `when`, `for`, and `while`) and the corresponding opening parenthesis. -->
* Ставьте пробелы между ключевыми словами (`if`, `when`, `for`, and `while`) и соответствующей открывающей скобкой.

<!-- * Do not put a space before an opening parenthesis in a primary constructor declaration, method declaration or method call. -->
* Не ставьте пробел перед открывающей скобкой в объявлении основного конструктора, объявлении метода или вызове метода.

```kotlin
class A(val x: Int)

fun foo(x: Int) { ... }

fun bar() {
    foo(1)
}
```

<!-- * Never put a space after `(`, `[`, or before `]`, `)` -->
* Никогда не ставьте пробел после `(`, `[` или перед `]`, `)`.

<!-- * Never put a space around `.` or `?.`: `foo.bar().filter { it > 2 }.joinToString()`, `foo?.bar()` -->
* Никогда не ставьте пробелы вокруг `.` или `?.`: `foo.bar().filter { it > 2 }.joinToString()`, `foo?.bar()`.

<!-- * Put a space after `//`: `// This is a comment` -->
* Ставьте пробел после `//`: `// Это комментарий`.

<!-- * Do not put spaces around angle brackets used to specify type parameters: `class Map<K, V> { ... }` -->
* Не ставьте пробелы вокруг угловых скобок, используемых для указания параметров типа: `class Map<K, V> { ... }`.

<!-- * Do not put spaces around `::`: `Foo::class`, `String::length` -->
* Не ставьте пробел вокруг `::`: `Foo::class`, `String::length`.

<!-- * Do not put a space before `?` used to mark a nullable type: `String?` -->
* Не ставьте пробел перед вопросительным знаком `?`, который используется для обозначения типа, допускающего обнуление: `String?`.

<!-- As a general rule, avoid horizontal alignment of any kind. Renaming an identifier to a name with a different length
should not affect the formatting of either the declaration or any of the usages. -->
Основное правило: избегайте любого горизонтального выравнивания.
Переименование идентификатора в имя с другой длиной не должно влиять на форматирование.

<a name="colon"></a>

<!-- ### Colon -->
### Двоеточие

<!-- Put a space before `:` in the following cases: -->
Ставьте пробел перед `:` в следующих случаях:

<!-- * when it's used to separate a type and a supertype
* when delegating to a superclass constructor or a different constructor of the same class
* after the `object` keyword -->
* когда оно используется для разделения типа и супертипа
* при передаче суперклассу конструктора или другого конструктора того же класса
* после ключевого слова `object`

<!-- Don't put a space before `:` when it separates a declaration and its type. -->
Не ставьте пробел перед `:`, когда оно разделяет объявление и его тип.

<!-- Always put a space after `:`. -->
Пробел после `:` ставится всегда.

```kotlin
abstract class Foo<out T : Any> : IFoo {
    abstract fun foo(a: Int): T
}

class FooImpl : Foo() {
    constructor(x: String) : this(x) { /*...*/ }

    val x = object : IFoo { /*...*/ } 
} 
```

<a name="class-headers"></a>

<!-- ### Class headers -->
## Заголовки классов

<!-- Classes with a few primary constructor parameters can be written in a single line: -->
Классы с небольшим количеством параметрами конструктора можно писать на одной строчке.

```kotlin
class Person(id: Int, name: String)
```

<!-- Classes with longer headers should be formatted so that each primary constructor parameter is in a separate line with indentation.
Also, the closing parenthesis should be on a new line. If you use inheritance, the superclass constructor call or 
the list of implemented interfaces should be located on the same line as the parenthesis: -->
Классы с более длинными сигнатурами должны быть отформатированы так, чтобы каждый параметр находится в отдельной строке с отступом.
Кроме того, закрывающая скобка должна быть в новой строке. Наследование, вызов конструктора суперкласса или список реализованных интерфейсов
должны располагаться в той же строке, что и скобка.

```kotlin
class Person(
    id: Int, 
    name: String,
    surname: String
) : Human(id, name) {
    // ...
}
```

<!-- For multiple interfaces, the superclass constructor call should be located first and then each interface should
be located in a different line: -->
Если класс расширяет несколько интерфейсов, конструктор суперкласса (если он есть) должен располагаться на первой строке,
а после него список расширяемых интерфейсов: каждый интерфейс с новой строки.

```kotlin
class Person(
    id: Int, 
    name: String,
    surname: String
) : Human(id, name),
    KotlinMaker {
    // ...
}
```

<!-- For classes with a long supertype list, put a line break after the colon and align all supertype names horizontally: -->
Для классов с длинным списком супертипов, начиная со следующей строки после двоеточия, расположите каждое имя супертипа с новой строки
и выровняйте их по горизонтали.

```kotlin
class MyFavouriteVeryLongClassHolder :
    MyLongHolder<MyFavouriteVeryLongClass>(),
    SomeOtherInterface,
    AndAnotherOne {

    fun foo() { /*...*/ }
}
```
<!-- To clearly separate the class header and body when the class header is long, either put a blank line
following the class header (as in the example above), or put the opening curly brace on a separate line: -->
Чтобы четко разделить заголовок класса и тело, когда заголовок класса длинный,
либо поместите пустую строку после заголовка класса (как в примере выше), либо поместите открывающую фигурную скобку в отдельную строку.

```kotlin
class MyFavouriteVeryLongClassHolder :
    MyLongHolder<MyFavouriteVeryLongClass>(),
    SomeOtherInterface,
    AndAnotherOne 
{
    fun foo() { /*...*/ }
}
```

<!-- Use regular indent (four spaces) for constructor parameters. This ensures that properties declared in the primary constructor have the same indentation as properties
declared in the body of a class. -->
Используйте обычный отступ (четыре пробела) для параметров конструктора.
Это гарантирует, что свойства, объявленные в основном конструкторе, имеют тот же отступ, что и свойства, объявленные в теле класса.

<a name="modifiers-order"></a>

<!-- ### Modifiers order -->
### Порядок модификаторов

<!-- If a declaration has multiple modifiers, always put them in the following order: -->
Если объявление содержит несколько модификаторов, всегда располагайте их в следующем порядке.

```kotlin
public / protected / private / internal
expect / actual
final / open / abstract / sealed / const
external
override
lateinit
tailrec
vararg
suspend
inner
enum / annotation / fun // модификатор в `fun interface` 
companion
inline / value
infix
operator
data
```

<!-- Place all annotations before modifiers: -->
Поместите все аннотации перед модификаторами.

```kotlin
@Named("Foo")
private val foo: Foo
```

<!-- Unless you're working on a library, omit redundant modifiers (for example, `public`). -->
Если вы не работаете с библиотекой, опускайте избыточные модификаторы (например, `public`).

<a name="annotations"></a>

<!-- ### Annotations -->
### Аннотации

<!-- Place annotations on separate lines before the declaration to which they are attached, and with the same indentation: -->
Размещайте аннотации на отдельных строках перед объявлением, к которому они прикреплены, и с тем же отступом:

```kotlin
@Target(AnnotationTarget.PROPERTY)
annotation class JsonExclude
```

<!-- Annotations without arguments may be placed on the same line: -->
Аннотации без аргументов могут быть размещены на одной строке.

```kotlin
@JsonExclude @JvmField
var x: String
```

<!-- A single annotation without arguments may be placed on the same line as the corresponding declaration: -->
Одна аннотация без аргументов может быть размещена в той же строке, что и соответствующее объявление.

```kotlin
@Test fun foo() { /*...*/ }
```

<a name="file-annotations"></a>

<!-- ### File annotations -->
### Аннотации к файлам

<!-- File annotations are placed after the file comment (if any), before the `package` statement, 
and are separated from `package` with a blank line (to emphasize the fact that they target the file and not the package). -->
Аннотации к файлам помещаются после комментария к файлу (если таковые имеются) перед инструкцией `package`
и отделяются от `package` пустой строкой (чтобы подчеркнуть тот факт, что они предназначены для файла, а не для пакета).

```kotlin
/** License, copyright and whatever */
@file:JvmName("FooBar")

package foo.bar
```

<a name="functions"></a>

<!-- ### Functions -->
### Функции

<!-- If the function signature doesn't fit on a single line, use the following syntax: -->
Если сигнатура функции не помещается в одной строке, используйте следующий синтаксис.

```kotlin
fun longMethodName(
    argument: ArgumentType = defaultValue,
    argument2: AnotherArgumentType,
): ReturnType {
    // body
}
```

<!-- Use regular indent (four spaces) for function parameters. It helps ensure consistency with constructor parameters. -->
Используйте обычный отступ (четыре пробела) для параметров функции. Это помогает обеспечить согласованность с параметрами конструктора.

<!-- Prefer using an expression body for functions with the body consisting of a single expression. -->
Для функции, состоящей из одного выражения, предпочтительно использовать выражение в качестве тела функции.

```kotlin
fun foo(): Int {     // плохо
    return 1 
}

fun foo() = 1        // хорошо
```

<a name="expression-bodies"></a>

<!-- ### Expression bodies -->
### Выражение вместо тела

<!-- If the function has an expression body whose first line doesn't fit on the same line as the declaration, put the `=` sign on the first line
and indent the expression body by four spaces. -->
Если функция имеет в качестве тела выражение, первая строка которого не помещается в ту же строку, что и объявление,
поставьте знак `=` в первой строке и сделайте отступ в теле выражения на четыре пробела.

```kotlin
fun f(x: String, y: String, z: String) =
    veryLongFunctionCallWithManyWords(andLongParametersToo(), x, y, z)
```

<a name="properties"></a>

<!-- ### Properties -->
### Свойства

<!-- For very simple read-only properties, consider one-line formatting: -->
Для очень простых свойств, доступных только для чтения, используйте форматирование в одну строку.

```kotlin
val isEmpty: Boolean get() = size == 0
```

<!-- For more complex properties, always put `get` and `set` keywords on separate lines: -->
Для более сложных свойств всегда помещайте ключевые слова `get` и `set` в отдельные строки.

```kotlin
val foo: String
    get() { /*...*/ }
```

<!-- For properties with an initializer, if the initializer is long, add a line break after the `=` sign
and indent the initializer by four spaces: -->
Для свойств с инициализатором, если инициализатор длинный, перейдите на следующую строку после знака `=`
и используйте отступ для инициализатора в четыре пробела.

```kotlin
private val defaultCharset: Charset? =
    EncodingRegistry.getInstance().getDefaultCharsetForPropertiesFiles(file)
```

<a name="control-flow-statements"></a>

<!-- ### Control flow statements -->
### Операторы управления потоком

<!-- If the condition of an `if` or `when` statement is multiline, always use curly braces around the body of the statement.
Indent each subsequent line of the condition by four spaces relative to statement begin. 
Put the closing parentheses of the condition together with the opening curly brace on a separate line: -->
Если условие операторов `if` или `when` многострочное, всегда используйте фигурные скобки вокруг тела оператора.
Делайте отступ в каждой последующей строке условия в четыре пробела.
Поместите закрывающую скобку условия вместе с открывающей фигурной скобкой в отдельной строке.

```kotlin
if (!component.isSyncing &&
    !hasAnyKotlinRuntimeInScope(module)
) {
    return createKotlinNotConfiguredPanel(module)
}
```

<!-- This helps align the condition and statement bodies. -->
Это поможет выровнять тела условия и инструкции.

<!-- Put the `else`, `catch`, `finally` keywords, as well as the `while` keyword of a `do-while` loop, on the same line as the 
preceding curly brace: -->
Поместите ключевые слова `else`, `catch`, `finally`, а также ключевое слово `while` цикла `do-while`, на ту же строку,
что и предыдущая фигурная скобка.

```kotlin
if (condition) {
    // body
} else {
    // else part
}

try {
    // body
} finally {
    // cleanup
}
```

<!-- In a `when` statement, if a branch is more than a single line, consider separating it from adjacent case blocks with a blank line: -->
Если ветвь оператора `when` состоит более чем из одной строки, рассмотрите возможность отделения её от остальных ветвей пустой строкой.

```kotlin
private fun parsePropertyValue(propName: String, token: Token) {
    when (token) {
        is Token.ValueToken ->
            callback.visitValue(propName, token.value)

        Token.LBRACE -> { // ...
        }
    }
}
```

<!-- Put short branches on the same line as the condition, without braces. -->
Поместите короткие ветви на ту же линию, что и условие, без скобок.

```kotlin
when (foo) {
    true -> bar() // хорошо
    false -> { baz() } // плохо
}
```

<a name="method-calls"></a>

<!-- ### Method calls -->
### Вызовы методов

<!-- In long argument lists, put a line break after the opening parenthesis. Indent arguments by four spaces. 
Group multiple closely related arguments on the same line. -->
В длинных списках аргументов после открывающей скобки переходите на следующую строку. Отступы аргументов в четыре пробела.
Сгруппируйте несколько тесно связанных аргументов в одной строке.

```kotlin
drawSquare(
    x = 10, y = 10,
    width = 100, height = 100,
    fill = true
)
```

<!-- Put spaces around the `=` sign separating the argument name and value. -->
Используйте пробелы вокруг знака `=`, разделяющего имя аргумента и значение.

<a name="wrap-chained-calls"></a>

<!-- ### Wrap chained calls -->
### Группировка цепочки вызовов

<!-- When wrapping chained calls, put the `.` character or the `?.` operator on the next line, with a single indent: -->
При группировке цепочки вызовов, расположите символ `.` или оператор `?.` на следующей строке с обычным отступом.

```kotlin
val anchor = owner
    ?.firstChild!!
    .siblings(forward = true)
    .dropWhile { it is PsiComment || it is PsiWhiteSpace }
```

<!-- The first call in the chain usually should have a line break before it, but it's OK to omit it if the code makes more sense that way. -->
Обычно перед первым вызовом в цепочке нужно перейти на новую строку, но можно пренебречь этим, если при этом код имеет больше смысла.

<a name="lambdas"></a>

<!-- ### Lambdas -->
## Лямбда-выражения

<!-- In lambda expressions, spaces should be used around the curly braces, as well as around the arrow which separates the parameters
from the body. If a call takes a single lambda, pass it outside of parentheses whenever possible. -->
В лямбда-выражениях фигурные скобки и стрелка, которая отделяет параметры от тела, отделяются пробелами.
Если вызов включает только одну лямбду, желательно передавать её за пределами скобок.

```kotlin
list.filter { it > 10 }
```

<!-- If assigning a label for a lambda, do not put a space between the label and the opening curly brace: -->
При назначении метки для лямбды не ставьте пробел между меткой и открывающей фигурной скобкой.

```kotlin
fun foo() {
    ints.forEach lit@{
        // ...
    }
}
```

<!-- When declaring parameter names in a multiline lambda, put the names on the first line, followed by the arrow and the newline: -->
При объявлении имен параметров в многострочной лямбде поместите имена в первую строку, за которой следует стрелка и новая строка.

```kotlin
appendCommaSeparated(properties) { prop ->
    val propertyValue = prop.get(obj)  // ...
}
```

<!-- If the parameter list is too long to fit on a line, put the arrow on a separate line: -->
Если список параметров слишком длинный, чтобы поместиться в строку, поместите стрелку в отдельную строку:

```kotlin
foo {
   context: Context,
   environment: Env
   ->
   context.configureEnv(environment)
}
```

<a name="trailing-commas"></a>

<!-- ### Trailing commas -->
### Завершающие запятые

<!-- A trailing comma is a comma symbol after the last item of a series of elements: -->
Завершающая запятая - это символ запятой после последнего элемента ряда элементов.

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    val age: Int, // завершающая запятая
)
```

<!-- Using trailing commas has several benefits: -->
Использование завершающих запятых имеет несколько преимуществ:

<!-- * It makes version-control diffs cleaner – as all the focus is on the changed value.
* It makes it easy to add and reorder elements – there is no need to add or delete the comma if you manipulate elements.
* It simplifies code generation, for example, for object initializers. The last element can also have a comma. -->
* Это делает различия в управлении версиями более понятными, так как все внимание сосредоточено на измененном значении.
* Это упрощает добавление и изменение порядка элементов – нет необходимости добавлять или удалять запятую.
* Это упрощает генерацию кода, например, для инициализаторов объектов. Последний элемент также может содержать запятую.

<!-- Trailing commas are entirely optional – your code will still work without them.
The Kotlin style guide encourages the use of trailing commas at the declaration site and leaves it at your discretion for the call site. -->
Завершающие запятые совершенно необязательны – ваш код все равно будет работать без них.
Руководство по стилю Kotlin рекомендует использовать конечные запятые при объявлениях и оставляет их использование на ваше усмотрение при вызовах.

<!-- To enable trailing commas in the IntelliJ IDEA formatter, go to **Settings/Preferences | Editor | Code Style | Kotlin**, 
open the **Other** tab and select the **Use trailing comma** option. -->
Чтобы включить завершающие запятые в IntelliJ IDEA, перейдите в раздел **Settings/Preferences | Editor | Code Style | Kotlin**,
откройте вкладку **Other** и выберите опцию **Use trailing comma**.

<a name="enumerations"></a>

<!-- #### Enumerations -->
#### Перечисления

```kotlin
enum class Direction {
    NORTH,
    SOUTH,
    WEST,
    EAST, // завершающая запятая
}
```

<a name="value-arguments"></a>

<!-- #### Value arguments -->
#### Аргументы

```kotlin
fun shift(x: Int, y: Int) { /*...*/ }
shift(
    25,
    20, // завершающая запятая
)
val colors = listOf(
    "red",
    "green",
    "blue", // завершающая запятая
)
```

<a name="class-properties-and-parameters"></a>

<!-- #### Class properties and parameters -->
#### Свойства и параметры класса

```kotlin
class Customer(
    val name: String,
    val lastName: String, // завершающая запятая
)
class Customer(
    val name: String,
    lastName: String, // завершающая запятая
)
```

<a name="function-value-parameters"></a>

<!-- #### Function value parameters -->
#### Параметры функции

```kotlin
fun powerOf(
    number: Int, 
    exponent: Int, // завершающая запятая
) { /*...*/ }
constructor(
    x: Comparable<Number>,
    y: Iterable<Number>, // завершающая запятая
) {}
fun print(
    vararg quantity: Int,
    description: String, // завершающая запятая
) {}
```

<a name="parameters-with-optional-type-including-setters"></a>

<!-- #### Parameters with optional type (including setters) -->
#### Параметры с необязательным типом (включая сеттеры)

```kotlin
val sum: (Int, Int, Int) -> Int = fun(
    x,
    y,
    z, // завершающая запятая
): Int {
    return x + y + x
}
println(sum(8, 8, 8))
```

<a name="indexing-suffix"></a>

<!-- #### Indexing suffix -->
#### Суффикс индексации

```kotlin
class Surface {
    operator fun get(x: Int, y: Int) = 2 * x + 4 * y - 10
}
fun getZValue(mySurface: Surface, xValue: Int, yValue: Int) =
    mySurface[
        xValue,
        yValue, // завершающая запятая
    ]
```

<a name="parameters-in-lambdas"></a>

<!-- #### Parameters in lambdas -->
#### Параметры в лямбдах

```kotlin
fun main() {
    val x = {
            x: Comparable<Number>,
            y: Iterable<Number>, // завершающая запятая
        ->
        println("1")
    }
    println(x)
}
```

<a name="when-entry"></a>

<!-- #### `when` entry -->
#### when

```kotlin
fun isReferenceApplicable(myReference: KClass<*>) = when (myReference) {
    Comparable::class,
    Iterable::class,
    String::class, // завершающая запятая
        -> true
    else -> false
}
```

<a name="collection-literals-in-annotations"></a>

<!-- #### Collection literals (in annotations) -->
#### Литералы коллекции

```kotlin
annotation class ApplicableFor(val services: Array<String>)
@ApplicableFor([
    "serializer",
    "balancer",
    "database",
    "inMemoryCache", // завершающая запятая
])
fun run() {}
```

<a name="type-arguments"></a>

#### Type arguments

```kotlin
fun <T1, T2> foo() {}
fun main() {
    foo<
            Comparable<Number>,
            Iterable<Number>, // завершающая запятая
            >()
}
```

<a name="type-parameters"></a>

#### Type parameters

```kotlin
class MyMap<
        MyKey,
        MyValue, // завершающая запятая
        > {}
```

<a name="destructuring-declarations"></a>

#### Destructuring declarations

```kotlin
data class Car(val manufacturer: String, val model: String, val year: Int)
val myCar = Car("Tesla", "Y", 2019)
val (
    manufacturer,
    model,
    year, // trailing comma
) = myCar
val cars = listOf<Car>()
fun printMeanValue() {
    var meanValue: Int = 0
    for ((
        _,
        _,
        year, // trailing comma
    ) in cars) {
        meanValue += year
    }
    println(meanValue/cars.size)
}
printMeanValue()
```

<a name="documentation-comments"></a>

<!-- ## Documentation comments -->
## Документация комментариями

<!-- For longer documentation comments, place the opening `/**` on a separate line and begin each subsequent line
with an asterisk: -->
Для длинных документационных комментариев поместите открывающий символ `/**` в отдельную строку
и начинайте каждую последующую строку со звездочки.

```kotlin
/**
 * Это документационный комментарий
 * на нескольких строках.
 */
```

<!-- Short comments can be placed on a single line: -->
Короткие комментарии могут быть размещены в одной строке.

```kotlin
/** Это короткий документационный комментарий. */
```

<!-- Generally, avoid using `@param` and `@return` tags. Instead, incorporate the description of parameters and return values
directly into the documentation comment, and add links to parameters wherever they are mentioned. Use `@param` and
`@return` only when a lengthy description is required which doesn't fit into the flow of the main text. -->
Следует избегать использования тегов `@param` и `@return`. Вместо этого включите описание параметров и возвращаемых значений
непосредственно в документационный комментарий и добавьте ссылки на параметры везде, где они упоминаются.
Используйте `@param` и `@return` только тогда, когда требуется длинное описание, которое не вписывается в основной текст.

```kotlin
// Не делайте так:

/**
 * Возвращает абсолютное значение заданного числа.
 * @param number Число, для которого будет найдено абсолютное значение.
 * @return Абсолютное значение.
 */
fun abs(number: Int): Int { /*...*/ }

// Делайте так:

/**
 * Возвращает абсолютное значение заданного [number].
 */
fun abs(number: Int): Int { /*...*/ }
```

<a name="avoid-redundant-constructs"></a>

<!-- ## Avoid redundant constructs -->
## Избегайте избыточных конструкций

<!-- In general, if a certain syntactic construction in Kotlin is optional and highlighted by the IDE
as redundant, you should omit it in your code. Do not leave unnecessary syntactic elements in code
just "for clarity". -->
В общем случае, если определенная синтаксическая конструкция в Kotlin является необязательной и выделена IDE как избыточная, вы должны опустить ее в своем коде. Не оставляйте ненужные синтаксические элементы в коде просто "для ясности".

<a name="unit-return-type"></a>

<!-- ### Unit return type -->
### Возвращаемый тип Unit

<!-- If a function returns Unit, the return type should be omitted: -->
Если функция возвращает `Unit`, тип возвращаемого значения следует опускать.

```kotlin
fun foo() { // ": Unit" опущено

}
```

<a name="semicolons"></a>

<!-- ### Semicolons -->
### Точки с запятой

<!-- Omit semicolons whenever possible. -->
Опускайте точки с запятой везде, где это возможно.

<a name="string-templates"></a>

<!-- ### String templates -->
### Строковые шаблоны

<!-- Don't use curly braces when inserting a simple variable into a string template. Use curly braces only for longer expressions. -->
Не используйте фигурные скобки при вставке простой переменной в строковый шаблон. Используйте фигурные скобки только для более длинных выражений.

```kotlin
println("У $name уже ${children.size} детей")
```

<a name="idiomatic-use-of-language-features"></a>

<!-- ## Idiomatic use of language features -->
## Идиоматическое использование функций языка

<a name="immutability"></a>

<!-- ### Immutability -->
### Неизменность

<!-- Prefer using immutable data to mutable. Always declare local variables and properties as `val` rather than `var` if
they are not modified after initialization. -->
Предпочтительнее использовать неизменяемые данные. Всегда объявляйте локальные переменные и свойства как `val`, а не `var`, если
они не будут изменены после инициализации.

<!-- Always use immutable collection interfaces (`Collection`, `List`, `Set`, `Map`) to declare collections which are not
mutated. When using factory functions to create collection instances, always use functions that return immutable
collection types when possible: -->
Всегда используйте неизменяемые коллекции интерфейсов (`Collection`, `List`, `Set`, `Map`) для объявления коллекций,
которые не изменяются. При использовании фабричных функций для создания экземпляров коллекции всегда используйте функции,
возвращающие неизменяемые типы коллекций, когда это возможно.

```kotlin
// Плохо: использование изменяемого типа коллекции для значения, которое не будет изменено
fun validateValue(actualValue: String, allowedValues: HashSet<String>) { ... }

// Хорошо: использование неизменяемого типа коллекции
fun validateValue(actualValue: String, allowedValues: Set<String>) { ... }

// Плохо: arrayListOf() возвращает ArrayList<T>, который является изменяемым типом
val allowedValues = arrayListOf("a", "b", "c")

// Хорошо: listOf() возвращает List<T>
val allowedValues = listOf("a", "b", "c")
```

<a name="default-parameter-values"></a>

<!-- ### Default parameter values -->
### Значения параметров по умолчанию

<!-- Prefer declaring functions with default parameter values to declaring overloaded functions. -->
Предпочтительнее объявлять функции со значениями параметров по умолчанию, чем перегружать функции.

```kotlin
// Плохо
fun foo() = foo("a")
fun foo(a: String) { /*...*/ }

// Хорошо
fun foo(a: String = "a") { /*...*/ }
```

<a name="type-aliases"></a>

<!-- ### Type aliases -->
### Псевдонимы типов

<!-- If you have a functional type or a type with type parameters which is used multiple times in a codebase, prefer defining
a type alias for it: -->
Если у вас есть функциональный тип или тип с параметрами, который используется несколько раз, предпочтительнее определить для него псевдоним.

```kotlin
typealias MouseClickHandler = (Any, MouseEvent) -> Unit
typealias PersonIndex = Map<String, Person>
```

<!-- If you use a private or internal type alias for avoiding name collision, prefer the `import … as …` mentioned in 
[Packages and Imports](packages.md). -->
Если вы используете псевдоним частного или внутреннего типа, чтобы избежать конфликта имен,
предпочтительнее использовать `import … as …`, упомянутый в [Пакеты и импорты](packages.html)

<a name="lambda-parameters"></a>

<!-- ### Lambda parameters -->
### Лямбда-параметры

<!-- In lambdas which are short and not nested, it's recommended to use the `it` convention instead of declaring the parameter
explicitly. In nested lambdas with parameters, always declare parameters explicitly. -->
В коротких лямбда-выражениях, не являющихся вложенными, рекомендуется использовать `it` вместо явного объявления параметра.
Во вложенных лямбдах с параметрами последние всегда должны быть объявлены.

<a name="returns-in-a-lambda"></a>

<!-- ### Returns in a lambda -->
### Возвращаемое значение в лямбде

<!-- Avoid using multiple labeled returns in a lambda. Consider restructuring the lambda so that it will have a single exit point.
If that's not possible or not clear enough, consider converting the lambda into an anonymous function. -->
Избегайте использования нескольких маркированных возвратов в лямбде. Рассмотрите возможность реструктуризации лямбды таким образом,
чтобы у нее была одна точка выхода. Если это невозможно или недостаточно очевидно, подумайте о преобразовании лямбды в анонимную функцию.

<!-- Do not use a labeled return for the last statement in a lambda. -->
Не используйте помеченный возврат для последнего оператора в лямбде.

<a name="named-arguments"></a>

<!-- ### Named arguments -->
### Именованные аргументы

<!-- Use the named argument syntax when a method takes multiple parameters of the same primitive type, or for parameters of `Boolean` type,
unless the meaning of all parameters is absolutely clear from context. -->
Используйте именованные аргументы, когда метод принимает несколько параметров одного и того же примитивного типа,
или для параметров типа `Boolean`, если значение всех параметров не является абсолютно ясным из контекста.

```kotlin
drawSquare(x = 10, y = 10, width = 100, height = 100, fill = true)
```

<a name="conditional-statements"></a>

<!-- ### Conditional statements -->
### Условные операторы

<!-- Prefer using the expression form of `try`, `if`, and `when`. -->
Предпочтительно использовать эту форму выражения `try`, `if` и `when`.

```kotlin
return if (x) foo() else bar()
```

```kotlin
return when(x) {
    0 -> "zero"
    else -> "nonzero"
}
```

<!-- The above is preferable to: -->
Выше изложенное предпочтительнее, чем:

```kotlin
if (x)
    return foo()
else
    return bar()
```

```kotlin
when(x) {
    0 -> return "zero"
    else -> return "nonzero"
}    
```

<a name="if-versus-when"></a>

<!-- ### if versus when -->
### if vs when

<!-- Prefer using `if` for binary conditions instead of `when`. 
For example, use this syntax with `if`: -->
Для двоичных условий лучше использовать `if`. Например, используйте этот синтаксис с `if`:

```kotlin
if (x == null) ... else ...
```

<!-- instead of this one with `when`: -->
вместо этого с `when`:

```kotlin
when (x) {
    null -> // ...
    else -> // ...
}
```

<!-- Prefer using `when` if there are three or more options. -->
Предпочтительнее использовать `when`, если есть три и более вариантов.

<a name="nullable-boolean-values-in-conditions"></a>

<!-- ### Nullable Boolean values in conditions -->
### Обнуляемые логические значения в условиях

<!-- If you need to use a nullable `Boolean` in a conditional statement, use `if (value == true)` or `if (value == false)` checks. -->
Если вам нужно использовать в условном операторе `Boolean`, допускающее значение `null`,
используйте проверки `if (value == true)` или `if (value == false)`.

<a name="loops"></a>

<!-- ### Loops -->
### Циклы

<!-- Prefer using higher-order functions (`filter`, `map` etc.) to loops. Exception: `forEach` (prefer using a regular `for` loop instead,
unless the receiver of `forEach` is nullable or `forEach` is used as part of a longer call chain). -->
Предпочтительнее использовать функции более высокого порядка (`filter`, `map` и т.д.), чем циклы.
Исключение: `forEach` (лучше использовать обычный цикл `for`) используется только, если получатель `forEach` не может быть обнулен
или `forEach` используется как часть более длинной цепочки вызовов.

<!-- When making a choice between a complex expression using multiple higher-order functions and a loop, understand the cost
of the operations being performed in each case and keep performance considerations in mind. -->
Делая выбор между циклом и сложным выражением, использующим несколько функций более высокого порядка, учитывайте стоимость операций,
выполняемых в каждом конкретном случае, исходите из соображений производительности.

<a name="loops-on-ranges"></a>

<!-- ### Loops on ranges -->
### Циклы для диапазонов

<!-- Use the `until` function to loop over an open range: -->
Используйте функцию `until`, чтобы использовать открытый диапазон в цикле.

```kotlin
for (i in 0..n - 1) { /*...*/ }  // плохо
for (i in 0 until n) { /*...*/ }  // хорошо
```

<a name="strings"></a>

<!-- ### Strings -->
### Строки

<!-- Prefer string templates to string concatenation. -->
Используйте строковые шаблоны вместо конкатенации строк.

<!-- Prefer multiline strings to embedding `\n` escape sequences into regular string literals. -->
Предпочитайте многострочные строки встраиванию escape-последовательностей `\n` в обычные строковые литералы.

<!-- To maintain indentation in multiline strings, use `trimIndent` when the resulting string does not require any internal
indentation, or `trimMargin` when internal indentation is required: -->
Чтобы сохранить отступ в многострочных строках, используйте `trimIndent`, когда результирующая строка не требует внутреннего отступа,
и `trimMargin`, когда требуется внутренний отступ.

```kotlin
println("""
    Not
    trimmed
    text
    """
       )

println("""
    Trimmed
    text
    """.trimIndent()
       )

println()

val a = """Trimmed to margin text:
          |if(a > 1) {
          |    return a
          |}""".trimMargin()

println(a)
```

<!-- Learn the difference between [Java and Kotlin multiline strings](java-to-kotlin-idioms-strings.md#use-multiline-strings). -->
См. [Многострочные строки Java и Kotlin](java-to-kotlin-idioms-strings.html#use-multiline-strings), чтобы узнать разницу между ними в Java и Kotlin.

<a name="functions-vs-properties"></a>

<!-- ### Functions vs properties -->
### Функции vs Свойства

<!-- In some cases functions with no arguments might be interchangeable with read-only properties. 
Although the semantics are similar, there are some stylistic conventions on when to prefer one to another. -->
В некоторых случаях, функции без аргументов могут быть взаимозаменяемы с неизменяемыми (read-only) свойствами.
Несмотря на схожую семантику, есть некоторые стилистические соглашения, указывающие на то, когда лучше использовать одно из этих решений.

<!-- Prefer a property over a function when the underlying algorithm: -->
Предпочтительно использовать свойства вместо функций, если лежащий в основе алгоритм:

<!--does not throw
has a `O(1)` complexity
is cheap to calculate (or caсhed on the first run)
returns the same result over invocations -->
* не выбрасывает исключений
* имеет `O(1)` сложность
* не требует больших затрат на выполнение (или результат вычислений кэшируется при первом вызове)
* возвращает одинаковый результат

<a name="extension-functions"></a>

<!-- ### Extension functions -->
### Функции-расширения

<!-- Use extension functions liberally. Every time you have a function that works primarily on an object, consider making it
an extension function accepting that object as a receiver. To minimize API pollution, restrict the visibility of
extension functions as much as it makes sense. As necessary, use local extension functions, member extension functions,
or top-level extension functions with private visibility. -->
Свободно используйте функции-расширения. Каждый раз, когда у вас есть функция, которая работает в основном с объектом,
подумайте о том, чтобы сделать её функцией расширения. Чтобы свести к минимуму загрязнение API, ограничьте видимость функций расширения настолько,
насколько это имеет смысл. При необходимости используйте функции локального расширения, функции расширения участника
или функции расширения верхнего уровня с закрытой видимостью.

<a name="infix-functions"></a>

<!-- ### Infix functions -->
### Инфиксные функции

<!-- Declare a function as `infix` only when it works on two objects which play a similar role. Good examples: `and`, `to`, `zip`.
Bad example: `add`. -->
Объявляйте функцию как `infix` только тогда, когда она работает с двумя объектами, которые играют аналогичную роль.
Хорошие примеры: `and`, `to`, `zip`. Плохой пример: `add`.

<!-- Do not declare a method as `infix` if it mutates the receiver object. -->
Не объявляйте метод как `infix`, если он изменяет объект.

<a name="factory-functions"></a>

<!-- ### Factory functions -->
### Фабричные функции

<!-- If you declare a factory function for a class, avoid giving it the same name as the class itself. Prefer using a distinct name
making it clear why the behavior of the factory function is special. Only if there is really no special semantics,
you can use the same name as the class. -->
Если вы объявляете фабричную функцию для класса, избегайте присвоения ей того же имени, что и самому классу.
Предпочтительнее использовать отдельное имя, дающее понять, почему поведение фабричной функции является особенным.
Только если на самом деле нет специальной семантики, вы можете использовать то же имя, что и класс.

```kotlin
class Point(val x: Double, val y: Double) {
    companion object {
        fun fromPolar(angle: Double, radius: Double) = Point(...)
    }
}
```

<!-- If you have an object with multiple overloaded constructors that don't call different superclass constructors and
can't be reduced to a single constructor with default argument values, prefer to replace the overloaded constructors with
factory functions. -->
Если у вас есть объект с несколькими перегруженными конструкторами, которые не вызывают разные конструкторы суперкласса
и не могут быть сведены к одному конструктору со значениями аргументов по умолчанию,
предпочтительнее заменить перегруженные конструкторы фабричными функциями.

<a name="platform-types"></a>

<!-- ### Platform types -->
### Типы платформ

<!-- A public function/method returning an expression of a platform type must declare its Kotlin type explicitly: -->
Публичная функция/метод, возвращающая выражение типа платформы, должна явно объявить свой тип Kotlin.

```kotlin
fun apiCall(): String = MyJavaApi.getProperty("name")
```

<!-- Any property (package-level or class-level) initialized with an expression of a platform type must declare its Kotlin type explicitly: -->
Любое свойство (на уровне пакета или класса), инициализированное выражением типа платформы, должно явно объявлять свой тип Kotlin.

```kotlin
class Person {
    val name: String = MyJavaApi.getProperty("name")
}
```

<!-- A local value initialized with an expression of a platform type may or may not have a type declaration: -->
Локальное значение, инициализированное выражением типа платформы, может и иметь, и не иметь объявление типа.

```kotlin
fun main() {
    val name = MyJavaApi.getProperty("name")
    println(name)
}
```

<a name="scope-functions-apply-with-run-also-let"></a>

<!-- ### Scope functions apply/with/run/also/let -->
### Функции области видимости apply/with/run/also/let

<!-- Kotlin provides a set of functions to execute a block of code in the context of a given object: `let`, `run`, `with`, `apply`, and `also`.
For the guidance on choosing the right scope function for your case, refer to [Scope Functions](scope-functions.md). -->
Kotlin предоставляет набор функций для выполнения блока кода в контексте данного объекта: `let`, `run`, `with`, `apply` и `also`.
Для получения рекомендаций по выбору правильной области видимости функции для вашего случая обратитесь к [Функции области видимости](scope-functions.html).

<a name="coding-conventions-for-libraries"></a>

<!-- ## Coding conventions for libraries -->
## Соглашения для библиотек

<!-- When writing libraries, it's recommended to follow an additional set of rules to ensure API stability: -->
При написании библиотек рекомендуется следовать дополнительному набору правил для обеспечения стабильности API:

<!--  * Always explicitly specify member visibility (to avoid accidentally exposing declarations as public API)
 * Always explicitly specify function return types and property types (to avoid accidentally changing the return type
   when the implementation changes)
 * Provide [KDoc](kotlin-doc.md) comments for all public members, with the exception of overrides that do not require any new documentation
   (to support generating documentation for the library) -->
* Всегда явно указывайте видимость участников (чтобы избежать случайного раскрытия объявлений в качестве общедоступного API)
* Всегда явно указывайте типы возвращаемых функций и типы свойств (чтобы избежать случайного изменения типа возвращаемого значения при изменении реализации)
* Предоставьте комментарии [KDoc](kotlin-doc.html) для всех общедоступных участников, за исключением переопределений, для которых не требуется никакой новой документации (для поддержки создания документации для библиотеки)
