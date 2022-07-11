---
type: doc
layout: reference
category: "Syntax"
title: "Встроенные (inline) функции"
menuLabel: "Встроенные функции"
url: https://kotlinlang.ru/docs/inline-functions.html
---

<!-- При переводе статьи оригинальная версия была от 07 October 2021 -->

<!-- # Inline functions -->
# Встроенные (inline) функции

<!-- Using [higher-order functions](lambdas.md) imposes certain runtime penalties: each function is an object, and it captures
a closure. A closure is a scope of variables that can be accessed in the body of the function.
Memory allocations (both for function objects and classes) and virtual calls introduce runtime overhead. -->
Использование [функций высшего порядка](lambdas.html) влечёт за собой снижение производительности: во-первых, любая
функция является объектом, а во-вторых, происходит захват контекста замыканием, то есть функции становятся доступны
переменные, объявленные вне её тела. А выделения памяти (как для объекта функции, так и для её класса) и виртуальные
вызовы занимают системные ресурсы.

<!-- But it appears that in many cases this kind of overhead can be eliminated by inlining the lambda expressions.
The functions shown below are good examples of this situation. The `lock()` function could be easily inlined at call-sites.
Consider the following case: -->
Но во многих случаях эти дополнительные затраты можно устранить с помощью инлайнинга (встраивания) лямбда-выражений.
Например, функция `lock()` может быть легко встроена в то место, из которого она вызывается.

```kotlin
lock(l) { foo() }
```

<!-- Instead of creating a function object for the parameter and generating a call, the compiler could emit the following code: -->
Вместо создания объекта функции для параметра и генерации вызова, компилятор мог бы выполнить что-то подобное этому коду:

```kotlin
l.lock()
try {
    foo()
} finally {
    l.unlock()
}
```

<!--To make the compiler do this, mark the `lock()` function with the `inline` modifier:-->
Чтобы заставить компилятор поступить именно так, отметьте функцию `lock` модификатором `inline`.

```kotlin
inline fun <T> lock(lock: Lock, body: () -> T): T { /*...*/ }
```

<!-- The `inline` modifier affects both the function itself and the lambdas passed to it: all of those will be inlined
into the call site. -->
Модификатор `inline` влияет и на функцию, и на лямбду, переданную ей: они обе будут встроены вместо вызова.

<!-- Inlining may cause the generated code to grow. However, if you do it in a reasonable way (avoiding inlining large
functions), it will pay off in performance, especially at "megamorphic" call-sites inside loops. -->
Встраивание функций может увеличить количество сгенерированного кода, но если вы будете делать это в разумных пределах
(избегая встраивания больших функций), то получите прирост производительности, особенно при вызове функций с параметрами
разного типа внутри циклов.

<a name="noinline"></a>

## noinline

<!-- If you don’t want all of the lambdas passed to an inline function to be inlined, mark some of your function
parameters with the `noinline` modifier: -->
В случае, если вы хотите, чтобы только некоторые лямбды, переданные inline-функции, были встроены,
вам необходимо отметить модификатором `noinline` те функции-параметры, которые встроены не будут.

```kotlin
inline fun foo(inlined: () -> Unit, noinline notInlined: () -> Unit) { /*...*/ }
```

<!-- Inlinable lambdas can only be called inside inline functions or passed as inlinable arguments. `noinline` lambdas,
however, can be manipulated in any way you like, including being stored in fields or passed around. -->
Когда как встраиваемые лямбды могут быть вызваны только внутри inline-функций или переданы в качестве встраиваемых
аргументов, с `noinline`-функциями можно работать без ограничений: хранить внутри полей, передавать куда-либо и т.д.

<!-- > If an inline function has no inlinable function parameters and no
> [reified type parameters](#reified-type-parameters), the compiler will issue a warning, since inlining such functions
> is very unlikely to be beneficial (you can use the `@Suppress("NOTHING_TO_INLINE")` annotation to suppress the warning
> if you are sure the inlining is needed). -->
> Заметьте, что если inline-функция не имеет ни inline параметров, ни [параметров вещественного типа](#reified-type-parameters),
> компилятор выдаст предупреждение, так как встраивание такой функции вряд ли принесёт пользу (используйте
> `@Suppress("NOTHING_TO_INLINE")` для скрытия предупреждения, если вы уверены, что встраивание необходимо).

<a name="non-local-returns"></a>

<!-- ## Non-local returns -->
## Нелокальные return

<!-- In Kotlin, you can only use a normal, unqualified `return` to exit a named function or an anonymous function.
To exit a lambda, use a [label](returns.md#return-to-labels). A bare `return` is forbidden
inside a lambda because a lambda cannot make the enclosing function `return`: -->
В Kotlin вы можете использовать обыкновенный, безусловный `return` только для выхода из именованной или анонимной
функции. Это значит, что для выхода из лямбды вам нужно использовать [метку](returns.html#return-to-labels).
Обычный `return` запрещён внутри лямбды, потому что она не может заставить внешнюю функцию завершиться.

```kotlin
fun foo() {
    ordinaryFunction {
        return // ERROR: нельзя заставить `foo` завершиться здесь
    }
}
```

<!-- But if the function the lambda is passed to is inlined, the return can be inlined, as well. So it is allowed: -->
Но если функция, в которую передана лямбда, встроена, то `return` также будет встроен, поэтому так делать можно:

```kotlin
fun foo() {
    inlined {
        return // OK: лямбда встроена
    }
}
```

<!-- Such returns (located in a lambda, but exiting the enclosing function) are called *non-local* returns. This sort of
construct usually occurs in loops, which inline functions often enclose: -->
Такие return (находящиеся внутри лямбд, но завершающие внешнюю функцию) называются нелокальными (ориг.: *non-local*).
Такие конструкции обычно используются в циклах, которые являются inline-функциями:

```kotlin
fun hasZeros(ints: List<Int>): Boolean {
    ints.forEach {
        if (it == 0) return true // return из hasZeros
    }
    return false
}
```

<!-- Note that some inline functions may call the lambdas passed to them as parameters not directly from the function body,
but from another execution context, such as a local object or a nested function. In such cases, non-local control flow
is also not allowed in the lambdas. To indicate that the lambda parameter of the inline function cannot use non-local
returns, mark the lambda parameter with the `crossinline` modifier: -->
Заметьте, что некоторые inline-функции могут вызывать переданные им лямбды не напрямую в теле функции, а из иного
контекста, такого как локальный объект или вложенная функция. В таких случаях, нелокальное управление потоком выполнения
также запрещено в лямбдах. Чтобы указать это, параметр лямбды необходимо отметить модификатором `crossinline`.

```kotlin
inline fun f(crossinline body: () -> Unit) {
    val f = object: Runnable {
        override fun run() = body()
    }
    // ...
}
```


<!-- > `break` and `continue` are not yet available in inlined lambdas, but we are planning to support them, too. -->
> `break` и `continue` пока что недоступны во встроенных лямбдах, но мы планируем добавить их поддержку.

<a name="reified-type-parameters"></a>

<!-- ## Reified type parameters -->
## Параметры вещественного типа

<!--Sometimes you need to access a type passed as a parameter:-->
Иногда вам необходимо получить доступ к типу, переданному в качестве параметра:

```kotlin
fun <T> TreeNode.findParentOfType(clazz: Class<T>): T? {
    var p = parent
    while (p != null && !clazz.isInstance(p)) {
        p = p.parent
    }
    @Suppress("UNCHECKED_CAST")
    return p as T?
}
```

<!-- Here, you walk up a tree and use reflection to check whether a node has a certain type.
It’s all fine, but the call site is not very pretty: -->
В этом примере осуществляется проход по дереву и используется рефлексия, чтобы проверить узел на принадлежность к
определённому типу. Это прекрасно работает, но вызов выглядит не очень симпатично:

```kotlin
treeNode.findParentOfType(MyTreeNode::class.java)
```

<!-- A better solution would be to simply pass a type to this function. You can call it as follows: -->
Что мы на самом деле хотим, так это передать этой функции тип, то есть вызвать её вот так:

```kotlin
treeNode.findParentOfType<MyTreeNode>()
```

<!-- To enable this, inline functions support *reified type parameters*, so you can write something like this: -->
В таких случаях inline-функции могут принимать *параметры вещественного типа* (ориг.: *reified type parameters*).
Чтобы включить эту возможность, вы можете написать что-то вроде этого:

```kotlin
inline fun <reified T> TreeNode.findParentOfType(): T? {
    var p = parent
    while (p != null && p !is T) {
        p = p.parent
    }
    return p as T?
}
```

<!--The code above qualifies the type parameter with the `reified` modifier to make it accessible inside the function,
almost as if it were a normal class. Since the function is inlined, no reflection is needed and normal operators like `!is`
and `as` are now available for you to use. Also, you can call the function as shown above: `myTree.findParentOfType<MyTreeNodeType>()`.-->
В коде выше тип параметра определяется с помощью модификатора `reified`, но он доступен внутри функции почти так же, как
и обычный класс. Так как функция встроена, то для работы таких операторов как `!is` и `as` рефлексия не нужна. Также, вы
можете вызывать её таким же образом, как было упомянуто выше: `myTree.findParentOfType<MyTreeNodeType>()`.

<!-- Though reflection may not be needed in many cases, you can still use it with a reified type parameter: -->
Хотя рефлексия может быть не нужна во многих случаях, вы всё ещё можете использовать её с параметром вещественного типа.

```kotlin
inline fun <reified T> membersOf() = T::class.members

fun main(s: Array<String>) {
    println(membersOf<StringBuilder>().joinToString("\n"))
}
```

<!--Normal functions (not marked as inline) cannot have reified parameters.
A type that does not have a run-time representation (for example, a non-reified type parameter or a fictitious type like
`Nothing`) cannot be used as an argument for a reified type parameter.-->

Обычная функция (не отмеченная как встроенная) не может иметь параметры вещественного типа. Тип, который не имеет
представление во времени исполнения (например, параметр невещественного или фиктивного типа вроде `Nothing`), не может
использоваться в качестве аргумента для параметра вещественного типа.

<a name="inline-properties"></a>

<!-- ## Inline properties -->
## Встроенные свойства

<!-- The `inline` modifier can be used on accessors of properties that don't have backing fields.
You can annotate individual property accessors: -->
Модификатор `inline` можно применять к методам доступа свойств, у которых нет теневых полей. Вы можете аннотировать
отдельные методы доступа.

```kotlin
val foo: Foo
    inline get() = Foo()

var bar: Bar
    get() = ...
    inline set(v) { ... }
```

<!-- You can also annotate an entire property, which marks both of its accessors as `inline`: -->
Также можно аннотировать свойство. В этом случае оба его метода доступа будут отмечены как `inline`.

```kotlin
inline var bar: Bar
    get() = ...
    set(v) { ... }
```

<!-- At the call site, inline accessors are inlined as regular inline functions. -->
В месте вызова встроенные методы доступа встраиваются как обычные inline-функции.

<a name="restrictions-for-public-api-inline-functions"></a>

<!-- ## Restrictions for public API inline functions -->
## Ограничения для встроенных функций в public API

<!-- When an inline function is `public` or `protected` but is not a part of a `private` or `internal` declaration,
it is considered a [module](visibility-modifiers.md#modules)'s public API. It can be called in other modules and is
inlined at such call sites as well. -->
Если у встроенной функции модификатор доступа `public` или `protected`, при этом она не является частью объявления с
модификаторами доступа `private` или `internal`, то она считается public API [модуля](visibility-modifiers.html#modules).
Её можно вызывать в других модулях и где она встраивается в месте вызова.

<!-- This imposes certain risks of binary incompatibility caused by changes in the module that declares an inline function in
case the calling module is not re-compiled after the change. -->
Это может привести к появлению двоичной несовместимости, если модуль, который объявляет встроенную функцию, был изменён,
но не перекомпилировался после внесения этих изменений.

<!-- To eliminate the risk of such incompatibility being introduced by a change in a *non*-public API of a module, public
API inline functions are not allowed to use non-public-API declarations, i.e. `private` and `internal` declarations and
their parts, in their bodies. -->
Чтобы исключить риск двоичной несовместимости, вызванной изменением *non*-public API модуля, public API inline-функциям
не разрешается использовать объявления non-public-API, т.е. `private` и `internal`.

<!-- An `internal` declaration can be annotated with `@PublishedApi`, which allows its use in public API inline functions.
When an `internal` inline function is marked as `@PublishedApi`, its body is checked too, as if it were public. -->
Объявление с модификатором `internal` может быть аннотировано при помощи `@PublishedApi`, что позволит его использовать
в public API inline-функциях. Когда встроенная функция с модификатором доступа `internal` помечена как `@PublishedApi`,
её тело тоже проверяется, как если бы она была public.
