---
type: doc
layout: reference
category: FAQ
title: "Сравнение с Java"
url: "https://kotlinlang.ru/docs/reference/comparison-to-java.html"
---

<!--# Comparison to Java Programming Language-->
# Сравнение с языком программирования Java

<!--## Some Java issues addressed in Kotlin-->
##Некоторые проблемы Java, решённые в Kotlin

<!--Kotlin fixes a series of issues that Java suffers from-->
Kotlin решает целый ряд проблем, от которых страдает Java:
<!--
 Null references are [controlled by the type system](null-safety.html).
 [No raw types](java-interop.html)
 Arrays in Kotlin are [invariant](basic-types.html#arrays)
 Kotlin has proper [function types](lambdas.html#function-types), as opposed to Java's SAM-conversions
 [Use-site variance](generics.html#use-site-variance-type-projections) without wildcards
 Kotlin does not have checked [exceptions](exceptions.html)
 -->

* Ссылки на null [контролируются системой типов](null-safety.html).
* [Нет сырых (raw) типов](java-interop.html)
* Массивы в Kotlin [инвариантны](basic-types.html#arrays)
* Kotlin имеет правильные [функциональные типы](lambdas.html#function-types) и поддерживает их использование вместо SAM-типов из Java
* [Вариативность на месте использования](generics.html#use-site-variance-type-projections) без подстановочных символов (или масок, ориг.: _wildcards_)
* В Kotlin нет проверяемых [исключений](exceptions.html)

<!--## What Java has that Kotlin does not-->
## Что есть в Java, но нет в Kotlin
<!--[Checked exceptions](exceptions.html)
* [Primitive types](basic-types.html) that are not classes
* [Static members](classes.html)
* [Non-private fields](properties.html)
* [Wildcard-types](generics.html)-->

* [Проверяемые исключения](exceptions.html)
* [Примитивные типы](basic-types.html), которые не являются классами
* [Статичные члены](classes.html)
* [Не-приватные поля](properties.html)
* [Подстановочные символы](generics.html) (маски, wildcards)

<!--## What Kotlin has that Java does not-->
## Что есть в Kotlin, но нет в Java
<!--
* [Lambda expressions](lambdas.html) + [Inline functions](inline-functions.html) = performant custom control structures
* [Extension functions](extensions.html)
* [Null-safety](null-safety.html)
* [Smart casts](typecasts.html)
* [String templates](basic-types.html#strings)
* [Properties](properties.html)
* [Primary constructors](classes.html)
* [First-class delegation](delegation.html)
* [Type inference for variable and property types](basic-types.html)
* [Singletons](object-declarations.html)
* [Declaration-site variance & Type projections](generics.html)
* [Range expressions](ranges.html)
* [Operator overloading](operator-overloading.html)
* [Companion objects](classes.html#companion-objects)
* [Data classes](data-classes.html)
* [Separate interfaces for read-only and mutable collections](collections.html)-->

* [Лямбда-выражения](lambdas.html) + [inline-функции](inline-functions.html) = производительные и контролируемые пользовательские структуры
* [Функции-расширения](extensions.html)
* [Null-безопасность](null-safety.html)
* [Умные приведения](typecasts.html)
* [Строковые шаблоны](basic-types.html#strings)
* [Свойства](properties.html)
* [Первичный конструктор](classes.html)
* [Делегирование на уровне языка](delegation.html)
* [Выведение типа для переменных и свойств](basic-types.html)
* [Синглтоны на уровне языка](object-declarations.html)
* [Вариативность на уровне объявления и Проекции типов](generics.html)
* [Интервалы](ranges.html)
* [Перегрузка операторов](operator-overloading.html)
* [Вспомогательные объекты](classes.html#companion-objects)
* [Классы данных](data-classes.html)
* [Раздельные интерфейсы для изменяемых и неизменяемых коллекций](collections.html)
* [Сопрограммы (корутины)](coroutines.html)
