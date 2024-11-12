---
type: doc
layout: reference
category: "Syntax"
title: "Ключевое слово this"
url: https://kotlinlang.ru/docs/this-expressions.html
---

<!-- При переводе статьи оригинальная версия была от 09 July 2021 -->

<!-- # This expressions -->
# Ключевое слово this

<!-- To denote the current _receiver_, you use `this` expressions: -->
Чтобы сослаться на объект, с которым вы работаете, используется ключевое слово `this`:

<!-- * In a member of a [class](classes.md#inheritance), `this` refers to the current object of that class.
* In an [extension function](extensions.md) or a [function literal with receiver](lambdas.md#function-literals-with-receiver)
`this` denotes the _receiver_ parameter that is passed on the left-hand side of a dot. -->

* Внутри [класса](classes.html#inheritance) ключевое слово `this` ссылается на объект этого класса;
* В [функциях-расширениях](extensions.html) или в
[литерале функции с объектом-приёмником](lambdas.html#function-literals-with-receiver) `this` обозначает
*объект-приёмник*, который передаётся слева от точки.

<!-- If `this` has no qualifiers, it refers to the _innermost enclosing scope_. To refer to `this` in other scopes, _label qualifiers_ are used: -->
Если ключевое слово `this` не имеет определителей, то оно ссылается на *область самого глубокого замыкания*. Чтобы
сослаться на `this` в одной из внешних областей, используются *метки-определители*.

<a name="qualified-this"></a>

<!-- ## Qualified this -->
## this с определителем

<!-- To access `this` from an outer scope (a [class](classes.md), [extension function](extensions.md),
or labeled [function literal with receiver](lambdas.md#function-literals-with-receiver)) you write `this@label`,
 where `@label` is a [label](returns.md) on the scope `this` is meant to be from: -->

Чтобы получить доступ к `this` из внешней области ([класса](classes.html), [функции-расширения](extensions.html) или
именованных [литералов функций с объектом-приёмником](lambdas.html#function-literals-with-receiver)), используйте
`this@label`, где `@label` - это [метка](returns.html) области, из которой нужно получить `this`.

```kotlin
class A { // неявная метка @A
    inner class B { // неявная метка @B
        fun Int.foo() { // неявная метка @foo
            val a = this@A // this класса A
            val b = this@B // this класса B

            val c = this // объект-приёмник функции foo(), типа Int
            val c1 = this@foo // объект-приёмник функции foo(), типа Int

            val funLit = lambda@ fun String.() {
                val d = this // объект-приёмник литерала funLit
            }


            val funLit2 = { s: String ->
                // объект-приёмник функции foo(), т.к. замыкание лямбды не имеет объекта-приёмника
                val d1 = this
            }
        }
    }
}
```

<a name="implicit-this"></a>

<!-- ## Implicit this -->
## Подразумеваемое this

<!-- When you call a member function on `this`, you can skip the `this.` part.
If you have a non-member function with the same name, use this with caution because in some cases it can be called instead: -->
Когда вы вызываете функцию-член для `this`, вы можете пропустить `this` часть. Если у вас есть функция, не являющаяся
членом, с тем же именем, используйте ее с осторожностью, потому что в некоторых случаях может быть вызвана она.

```kotlin
fun printLine() { println("Функция верхнего уровня") }

class A {
    fun printLine() { println("Функция-член") }

    fun invokePrintLine(omitThis: Boolean = false)  { 
        if (omitThis) printLine()
        else this.printLine()
    }
}

A().invokePrintLine() // Функция-член
A().invokePrintLine(omitThis = true) // Функция верхнего уровня
```
