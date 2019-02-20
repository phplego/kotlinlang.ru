---
type: doc
layout: reference
category: "Syntax"
title: "Приведение и проверка типов"
url: https://kotlinlang.ru/docs/reference/typecasts.html
---

<!--# Type Checks and Casts-->
# Приведение и проверка типов

<!--## `is` and `!is` Operators-->
## Операторы `is` и `!is`

<!--We can check whether an object conforms to a given type at runtime by using the `is` operator or its negated form `!is`:-->
Мы можем проверить принадлежит ли объект к какому-либо типу во время исполнения с помощью оператора `is` или его отрицания `!is`:

``` kotlin
if (obj is String) {
    print(obj.length)
}

if (obj !is String) { // то же самое, что и !(obj is String)
    print("Not a String")
}
else {
    print(obj.length)
}
```

<a name="smart-casts"></a>

<!--## Smart Casts-->
## Умные приведения

<!--In many cases, one does not need to use explicit cast operators in Kotlin, because the compiler tracks the
`is`-checks for immutable values and inserts (safe) casts automatically when needed:-->
Во многих случаях в Kotlin вам не нужно использовать явные приведения, потому что компилятор следит за `is`-проверками для 
неизменяемых значений и вставляет приведения автоматически, там, где они нужны:

``` kotlin
fun demo(x: Any) {
    if (x is String) {
        print(x.length) // x автоматически преобразовывается в String
    }
}
```

<!--The compiler is smart enough to know a cast to be safe if a negative check leads to a return:-->
Компилятор достаточно умён для того, чтобы делать автоматические приведения в случаях, когда проверка на несоответствие типу (`!is`)
приводит к выходу из функции:

``` kotlin
    if (x !is String) return
    print(x.length) // x автоматически преобразовывается в String
```

<!--or in the right-hand side of `&&` and `||`:-->
или в случаях, когда приводимая переменная находится справа от оператора `&&` или `||`:

``` kotlin
    // x автоматически преобразовывается в String справа от `||`
    if (x !is String || x.length == 0) return

    // x автоматически преобразовывается в String справа от `&&`
    if (x is String && x.length > 0) {
        print(x.length) // x автоматически преобразовывается в String
    }
```


<!--Such _smart casts_ work for [*when*{: .keyword }-expressions](control-flow.html#when-expression)
and [*while*{: .keyword }-loops](control-flow.html#while-loops) as well:-->
Такие _умные приведения_  работают вместе с [*when*-выражениями](control-flow.html#when-expression) и [циклами *while*](control-flow.html#while-loops):

``` kotlin
when (x) {
    is Int -> print(x + 1)
    is String -> print(x.length + 1)
    is IntArray -> print(x.sum())
}
```

<!--Note that smart casts do not work when the compiler cannot guarantee that the variable cannot change between the check and the usage.
More specifically, smart casts are applicable according to the following rules:-->
Заметьте, что умные приведения не работают, когда компилятор не может гарантировать, что переменная не изменится между проверкой и использованием.
Более конкретно, умные приведения будут работать:

<!--  * *val*{: .keyword } local variables - always;
  * *val*{: .keyword } properties - if the property is private or internal or the check is performed in the same module where the property is declared. Smart casts aren't applicable to open properties or properties that have custom getters;
  * *var*{: .keyword } local variables - if the variable is not modified between the check and the usage and is not captured in a lambda that modifies it;
  * *var*{: .keyword } properties - never (because the variable can be modified at any time by other code).-->
  
* с локальными *val* переменными - всегда;
* с *val* свойствами - если поле имеет модификатор доступа `private` или `internal`, или проверка происходит в том же [модуле](visibility-modifiers.html#modules), в котором объявлено это свойство. 
Умные приведения неприменимы к публичным свойствам или свойствам, которые имеют переопределённые getter'ы;
* с локальными *var* переменными - если переменная не изменяется между проверкой и использованием и не захватывается лямбдой, которая её модифицирует;
* с *var* свойствами - никогда (потому что переменная может быть изменена в любое время другим кодом).


<!--## "Unsafe" cast operator-->
## Оператор "небезопасного" приведения

<!--Usually, the cast operator throws an exception if the cast is not possible. Thus, we call it *unsafe*.
The unsafe cast in Kotlin is done by the infix operator *as*{: .keyword } (see [operator precedence](grammar.html#precedence)):-->
Обычно оператор приведения выбрасывает исключение, если приведение невозможно, поэтому мы называем его *небезопасным*.
Небезопасное приведение в Kotlin выполняется с помощью инфиксного оператора *as* (см. [приоритеты операторов](grammar.html#precedence)):

``` kotlin
val x: String = y as String
```

<!--Note that *null*{: .keyword } cannot be cast to `String` as this type is not [nullable](null-safety.html),
i.e. if `y` is null, the code above throws an exception.
In order to match Java cast semantics we have to have nullable type at cast right hand side, like-->
Заметьте, что *null* не может быть приведен к `String`, так как `String` не является [nullable](null-safety.html),
т.е. если `y` - null, код выше выбросит исключение. Чтобы соответствовать семантике приведений в Java, нам нужно указать nullable тип в правой части приведения:

``` kotlin
val x: String? = y as String?
```

<!--## "Safe" (nullable) cast operator-->
## Оператор "безопасного" (nullable) приведения 

<!--To avoid an exception being thrown, one can use a *safe* cast operator *as?*{: .keyword } that returns *null*{: .keyword } on failure:-->
Чтобы избежать исключения, вы можете использовать оператор *безопасного* приведения *as?*, который возвращает *null* в случае неудачи:

``` kotlin
val x: String? = y as? String
```

<!--Note that despite the fact that the right-hand side of *as?*{: .keyword } is a non-null type `String` the result of the cast is nullable.-->
Заметьте, что несмотря на то, что справа от *as?* стоит non-null тип `String`, результат приведения является nullable.
