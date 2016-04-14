---
type: doc
layout: reference
category: "Syntax"
title: "Пакеты"
---

# Пакеты

Файл с исходным кодом может начинаться с объявления пакета:

``` kotlin
package foo.bar

fun baz() {}

class Goo {}

// ...
```

Всё содержимое файла с исходниками (например, классы и функции) располагается в объявленном пакете.
Таким образом, в приведённом выше примере полное имя функции `baz()` 
So, in the example above, the full name of `baz()` is `foo.bar.baz`, а полное имя класса `Goo` - `foo.bar.Goo`. 
 
Если файл не содержит явного объявления пакета, то его содержимое находится в безымянном "пакете по умолчанию".

## Импорт

Apart from the default imports, each file may contain its own import directives.
Syntax for imports is described in the [grammar](https://kotlinlang.org/docs/reference/grammar.html#imports).

We can import either a single name, e.g.

``` kotlin
import foo.Bar // Bar is now accessible without qualification
```

or all the accessible contents of a scope (package, class, object etc):

``` kotlin
import foo.* // everything in 'foo' becomes accessible
```

If there is a name clash, we can disambiguate by using **as**<!--keyword--> keyword to locally rename the clashing entity:

``` kotlin
import foo.Bar // Bar is accessible
import bar.Bar as bBar // bBar stands for 'bar.Bar'
```

The `import` keyword is not restricted to importing classes; you can also use it to import other declarations:

  * top-level functions and properties;
  * functions and properties declared in [object declarations](object-declarations.html#object-declarations);
  * [enum constants](enum-classes.html)

Unlike Java, Kotlin does not have a separate "import static" syntax; all of these declarations are imported using the regular `import` keyword.

## Visibility of Top-level Declarations

If a top-level declaration is marked **private**<!--keyword-->, it is private to the file it's declared in (see [Visibility Modifiers](visibility-modifiers.html)).
