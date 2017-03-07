---
type: doc
layout: reference
category: "Syntax"
title: "Ключевое слово this"
---

<!--# This Expression-->
# Ключевое слово this

<!--To denote the current _receiver_, we use *this*{: .keyword } expressions:-->
Чтобы сослаться на текущий объект, мы используем ключевое слово *this*:

<!--* In a member of a [class](classes.html#inheritance), *this*{: .keyword } refers to the current object of that class
* In an [extension function](extensions.html) or a [function literal with receiver](lambdas.html#function-literals-with-receiver),
*this*{: .keyword } denotes the _receiver_ parameter that is passed on the left-hand side of a dot.-->
* Внутри [класса](classes.html) ключевое слово *this* ссылается на объект этого класса
* В [функциях-расширениях](extensions.html) или в [литерале функции с принимающим объектом](lambdas.html) *this* обозначает 
_принимающий объект_, который передаётся слева от точки. 

<!--If *this*{: .keyword } has no qualifiers, it refers to the _innermost enclosing scope_. 
To refer to *this*{: .keyword } in other scopes, _label qualifiers_ are used:-->
Если ключевое слово *this* не имеет определителей, то оно ссылается на _область самого глубокого замыкания_.
Чтобы сослаться на *this* в других областях, используются _метки-определители_:

## Qualified *this*{: .keyword }
{:#qualified}

To access *this*{: .keyword } from an outer scope (a [class](classes.html), or [extension function](extensions.html),
or labeled [function literal with receiver](lambdas.html#function-literals-with-receiver)) we write `this@label` where `@label` is a [label](returns.html)
on the scope *this*{: .keyword } is meant to be from:

``` kotlin
class A { // implicit label @A
    inner class B { // implicit label @B
        fun Int.foo() { // implicit label @foo
            val a = this@A // A's this
            val b = this@B // B's this

            val c = this // foo()'s receiver, an Int
            val c1 = this@foo // foo()'s receiver, an Int

            val funLit = lambda@ fun String.() {
                val d = this // funLit's receiver
            }


            val funLit2 = { s: String ->
                // foo()'s receiver, since enclosing lambda expression
                // doesn't have any receiver
                val d1 = this
            }
        }
    }
}
```
