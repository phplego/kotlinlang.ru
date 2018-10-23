---
type: doc
layout: reference
title: "Использование kapt"
url: "https://kotlinlang.ru/docs/reference/kapt.html"
---

# Обработка аннотаций с Kotlin

Обработчики аннотаций (см. [JSR 269](https://jcp.org/en/jsr/detail?id=269)) поддерживаются в Kotlin с помощью плагина компилятора *kapt*.

Если коротко, то в проектах Kotlin можно использовать такие библиотеки, как [Dagger](https://google.github.io/dagger/) или [Data Binding](https://developer.android.com/topic/libraries/data-binding/index.html) .

Ниже демонстрируется применение плагина *kapt* к сборкам Gradle/Maven.

## Использование в Gradle

Применим плагин `kotlin-kapt` в Gradle:

```groovy
apply plugin: 'kotlin-kapt'
```
Или с помощью плагинов предметно-ориентированного языка:

```groovy
plugins {
    id "org.jetbrains.kotlin.kapt" version "{{ site.data.releases.latest.version }}"
}
```
Затем добавим соответствующие зависимости, используя конфигурацию `kapt` в нашем блоке `dependencies`:

```groovy
dependencies {
    kapt 'groupId:artifactId:version'
}
```
Если для обработчиков аннотаций ранее использовался [Android support](https://developer.android.com/studio/build/gradle-plugin-3-0-0-migration.html#annotationProcessor_config), то необходимо заменить  `annotationProcessor` на `kapt`. Если наш проект содержит классы Java, `kapt` также позаботится и о них.

Если обработчики аннотаций использовались для исходного кода `androidTest` или `test`, конфигурации `kapt` будут называться соответственно `kaptAndroidTest` и `kaptTest`. Обратите внимание на то, что `kaptAndroidTest` и `kaptTest` расширяют `kapt`, поэтому достаточно предоставить только зависимость `kapt`, и она будет доступна как для исходного кода, так и для тестов.

## Аргументы обработчика аннотации

Используем блок `arguments {}` для передачи аргументов обработчикам аннотаций:

``` groovy
kapt {
    arguments {
        arg("key", "value")
    }
}
```
## Параметры компилятора Java

Kapt использует компилятор Java для запуска обработчиков аннотаций. 
Так можно передавать произвольные параметры в javac:

``` groovy
kapt {
    javacOptions {
        // Increase the max count of errors from annotation processors.
        // Default is 100.
        option("-Xmaxerrs", 500)
    }
}
```

## Коррекция несуществующего типа

Некоторые обработчики аннотаций (например, `AutoFactory`) полагаются на известные типы в объявлениях сигнатур. По умолчанию Kapt заменяет каждый неизвестный тип (включая типы для сгенерированных классов) на `NonExistentClass`, но это поведение можно изменить. Добавим в `build.gradle` дополнительный флаг для включения данного типа ошибки в заглушки:

``` groovy
kapt {
    correctErrorTypes = true
}
```

## Использование в Maven

Добавим выполнение цели `kapt` от плагина kotlin-maven до `compile`: 

```xml
<execution>
    <id>kapt</id>
    <goals>
        <goal>kapt</goal>
    </goals>
    <configuration>
        <sourceDirs>
            <sourceDir>src/main/kotlin</sourceDir>
            <sourceDir>src/main/java</sourceDir>
        </sourceDirs>
        <annotationProcessorPaths>
            <!-- Specify your annotation processors here. -->
            <annotationProcessorPath>
                <groupId>com.google.dagger</groupId>
                <artifactId>dagger-compiler</artifactId>
                <version>2.9</version>
            </annotationProcessorPath>
        </annotationProcessorPaths>
    </configuration>
</execution>
```
 
Полный образец проекта, показывающий использование Kotlin, Maven и Dagger, можно найти в [репозитории примеров Kotlin](https://github.com/JetBrains/kotlin-examples/tree/master/maven/dagger-maven-example).
 
Обратите внимание на то, что kapt по-прежнему не поддерживается собственной системой сборки IntelliJ IDEA. Запустите сборку с панели инструментов Maven Projects, если требуется повторный запуск обработки аннотаций.


## Использование в командной строке

Плагин компилятора Kapt доступен в бинарном дистрибутиве компилятора Kotlin.

Плагин можно подключить, указав его путь к JAR-файлу с помощью параметра `Xplugin` kotlinc:

```bash
-Xplugin=$KOTLIN_HOME/lib/kotlin-annotation-processing.jar
```

Список доступных параметров:

* `sources` (*обязательно*): Путь вывода для сгенерированных файлов.
* `classes` (*обязательно*): Путь вывода для сгенерированных файлов и ресурсов.
* `stubs` (*обязательно*): Путь вывода для файлов-заглушек. Другими словами, какая-то временная директория.
* `incrementalData`: Путь вывода для бинарных заглушек.
* `apclasspath` (*повторяется*): путь к обработчику JAR для аннотации. Передайте столько вариантов `apclasspath`, сколько у вас имеется JARов.
* `apoptions`: Кодированный в base64 список параметров обработчика аннотаций. Для получения дополнительной информации см. [Кодировка параметров AP / javac .](#Кодировка-параметров-ap--javac)
* `javacArguments`: Кодированный в base64 список параметров, переданных в javac. Для получения дополнительной информации см. [Кодировка параметров AP / javac .](#Кодировка-параметров-ap--javac)
* `processors`: Список идущих через запятую имен классов обработчиков аннотаций. Если данный список определен, то kapt не будет пытаться искать обработчики аннотаций в `apclasspath`.
* `verbose`: Включить подробный вывод.
* `aptMode` (*обязательно*)
    * `stubs` – генерировать только заглушки, необходимые для обработки аннотаций;
    * `apt` – только обрабатывать аннотации;
    * `stubsAndApt` – генерировать заглушки и обрабатывать аннотации;
* `correctErrorTypes`: Смотрите [здесь](#Использование-в-gradle). По умолчанию отключено.

Формат параметров плагина: `-P plugin:<plugin id>:<key>=<value>`. Параметры могут повторяться.

Пример:

```bash
-P plugin:org.jetbrains.kotlin.kapt3:sources=build/kapt/sources
-P plugin:org.jetbrains.kotlin.kapt3:classes=build/kapt/classes
-P plugin:org.jetbrains.kotlin.kapt3:stubs=build/kapt/stubs

-P plugin:org.jetbrains.kotlin.kapt3:apclasspath=lib/ap.jar
-P plugin:org.jetbrains.kotlin.kapt3:apclasspath=lib/anotherAp.jar

-P plugin:org.jetbrains.kotlin.kapt3:correctErrorTypes=true
```


## Создание исходного кода Kotlin

Kapt может генерировать исходный код Kotlin. Просто поместим сгенерированные исходные файлы Kotlin в указанную директорию `processingEnv.options["kapt.kotlin.generated"]`,  и эти файлы будут скомпилированы вместе с основными исходниками.

Полный образец можно найти в [kotlin-examples](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/kotlin-code-generation) репозитория Github.

Обратите внимание на то, что Kapt не поддерживает множественные циклы для сгенерированных файлов Kotlin.


## Кодировка параметров AP / javac

Параметры интерфейса командной строки `apoptions` и `javacArguments` принимают кодированные пары "ключ-значение" параметров.  
Так можно кодировать параметры самостоятельно:

```kotlin
fun encodeList(options: Map<String, String>): String {
    val os = ByteArrayOutputStream()
    val oos = ObjectOutputStream(os)

    oos.writeInt(options.size)
    for ((key, value) in options.entries) {
        oos.writeUTF(key)
        oos.writeUTF(value)
    }

    oos.flush()
    return Base64.getEncoder().encodeToString(os.toByteArray())
}
```
