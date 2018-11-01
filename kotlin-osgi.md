---
type: doc
layout: reference
title: "Kotlin и OSGi"
---

# Kotlin и OSGi

<!-- To enable Kotlin OSGi support you need to include `kotlin-osgi-bundle` instead of regular Kotlin libraries.
It is recommended to remove `kotlin-runtime`, `kotlin-stdlib` and `kotlin-reflect` dependencies as `kotlin-osgi-bundle`
already contains all of them. You also should pay attention in case when external Kotlin libraries are included.
Most regular Kotlin dependencies are not OSGi-ready, so you shouldn't use them and should remove them from your project. -->

Для включения поддержки Kotlin OSGi необходимо включить `kotlin-osgi-bundle` вместо обычных библиотек Kotlin. 
Рекомендуется удалить зависимости `kotlin-runtime`, `kotlin-stdlib` и `kotlin-reflect`, т.к. `kotlin-osgi-bundle` уже содержит их все. 
Следует обратить внимание на то, подключены ли внешние библиотеки Kotlin. Большинство обычных зависимостей Kotlin не готовы для использования
с OSGi, поэтому их не следует использовать и нужно удалить из проекта.

## Maven

<!-- To include the Kotlin OSGi bundle to a Maven project: -->

Включим комплект Kotlin OSGi в проект Maven:

```xml
   <dependencies>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-osgi-bundle</artifactId>
            <version>${kotlin.version}</version>
        </dependency>
    </dependencies>
```

<!-- To exclude the standard library from external libraries (notice that "star exclusion" works in Maven 3 only): -->

Исключим стандартную библиотеку из внешних библиотек (обратите внимание, что «звездное исключение» работает только в Maven 3):

```xml
        <dependency>
            <groupId>some.group.id</groupId>
            <artifactId>some.library</artifactId>
            <version>some.library.version</version>

            <exclusions>
                <exclusion>
                    <groupId>org.jetbrains.kotlin</groupId>
                    <artifactId>*</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

## Gradle

<!-- To include `kotlin-osgi-bundle` to a gradle project: -->

Включим `kotlin-osgi-bundle` в проект Gradle:

```groovy
compile "org.jetbrains.kotlin:kotlin-osgi-bundle:$kotlinVersion"
```

<!-- To exclude default Kotlin libraries that comes as transitive dependencies you can use the following approach: -->

Чтобы исключить стандартные библиотеки Kotlin, входящие в состав транзитивных зависимостей, можно использовать следующий подход:

```groovy
dependencies {
 compile (
   [group: 'some.group.id', name: 'some.library', version: 'someversion'],
   .....) {
  exclude group: 'org.jetbrains.kotlin'
}
```

## Часто задаваемые вопросы

<!-- #### Why not just add required manifest options to all Kotlin libraries -->

#### Почему бы просто не добавить необходимые параметры манифеста ко всем библиотекам Kotlin?

<!-- Even though it is the most preferred way to provide OSGi support, unfortunately it couldn't be done for now due to so called
["package split" issue](http://wiki.osgi.org/wiki/Split_Packages) that couldn't be easily eliminated and such a big change is
not planned for now. There is `Require-Bundle` feature but it is not the best option too and not recommended to use.
So it was decided to make a separate artifact for OSGi. -->

Несмотря на то, что это самый предпочтительный способ предоставить поддержку OSGi, к сожалению, на данный момент это невозможно сделать 
из-за так называемой [проблемы "разделения пакета"](http://wiki.osgi.org/wiki/Split_Packages), которая не может быть легко устранена, и такие большие изменения пока не планируются. 
Есть функция `Require-Bundle`, но она не является лучшим вариантом и не рекомендована к использованию. Поэтому было принято решение 
сделать отдельный артефакт для OSGi.

