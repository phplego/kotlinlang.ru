---
type: doc
layout: reference
category: "Syntax"
title: "Ключевое слово this"
url: https://kotlinlang.ru/docs/reference/this-expressions.html
---

<!--# This Expression-->
# Ключевое слово this

<!--To denote the current _receiver_, we use *this*{: .keyword } expressions:-->
Чтобы сослаться на объект, с которым мы работаем, используется ключевое слово *this*:

<!--* In a member of a [class](classes.html#inheritance), *this*{: .keyword } refers to the current object of that class
* In an [extension function](extensions.html) or a [function literal with receiver](lambdas.html#function-literals-with-receiver),
*this*{: .keyword } denotes the _receiver_ parameter that is passed on the left-hand side of a dot.-->
* Внутри [класса](classes.html#inheritance) ключевое слово *this* ссылается на объект этого класса
* В [функциях-расширениях](extensions.html) или в [литерале функции с принимающим объектом](lambdas.html#function-literals-with-receiver) *this* обозначает 
_принимающий объект_, который передаётся слева от точки. 

<!--If *this*{: .keyword } has no qualifiers, it refers to the _innermost enclosing scope_. 
To refer to *this*{: .keyword } in other scopes, _label qualifiers_ are used:-->
Если ключевое слово *this* не имеет определителей, то оно ссылается на _область самого глубокого замыкания_.
Чтобы сослаться на *this* в одной из внешних областей, используются _метки-определители_:

<!--## Qualified *this*{: .keyword }
{:#qualified}-->
## *this* с определителем

<!--To access *this*{: .keyword } from an outer scope (a [class](classes.html), or [extension function](extensions.html),
or labeled [function literal with receiver](lambdas.html#function-literals-with-receiver)) we write `this@label` where `@label` is a [label](returns.html)
on the scope *this*{: .keyword } is meant to be from:-->

Чтобы получить доступ к *this* из внешней области ([класса](classes.html), [функции-расширения](extensions.html),
или именованных [литералов функций с принимающим объектом](lambdas.html#function-literals-with-receiver)) мы пишем `this@label`, где `@label` - это [метка](returns.html) области, из которой нужно получить *this*:

```kotlin
class A { // неявная метка @A
    inner class B { // неявная метка @B
        fun Int.foo() { // неявная метка @foo
            val a = this@A // this из A
            val b = this@B // this из B

            val c = this // принимающий объект функции foo(), типа Int
            val c1 = this@foo // принимающий объект функции foo(), типа Int

            val funLit = lambda@ fun String.() {
                val d = this // принимающий объект литерала funLit
            }


            val funLit2 = { s: String ->
                // принимающий объект функции foo(), т.к. замыкание лямбды не имеет принимающего объекта
                val d1 = this
            }
        }
    }
}
```
