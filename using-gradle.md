---
type: doc
layout: reference
category: "tools"
title: "Использование Gradle"
url: https://kotlinlang.ru/docs/reference/using-gradle.html
---


<!--
# Using Gradle

In order to build Kotlin with Gradle you should [set up the *kotlin-gradle* plugin](#plugin-and-versions), [apply it](#targeting-the-jvm) to your project and [add *kotlin-stdlib* dependencies](#configuring-dependencies). Those actions may also be performed automatically in IntelliJ IDEA by invoking the Tools \| Kotlin \| Configure Kotlin in Project action. -->

# Использование Gradle

<!-- In order to build Kotlin with Gradle you should [set up the *kotlin-gradle* plugin](#plugin-and-versions), [apply it](#targeting-the-jvm) to your project and [add *kotlin-stdlib* dependencies](#configuring-dependencies). Those actions may also be performed automatically in IntelliJ IDEA by invoking the Tools \| Kotlin \| Configure Kotlin in Project action. -->

Для сборки с помощью Gradle необходимо [настроить *kotlin-gradle* плагин](#plugin-and-versions), [применить его](#targeting-the-jvm) к своему проекту и [добавить *kotlin-stdlib* зависимость](#configuring-dependencies). Эти действия могут быть сделаны автоматически с помощью IntelliJ IDEA во вкладке Tools | Kotlin | Configure Kotlin.


## Плагин и версии

<!--The `kotlin-gradle-plugin` compiles Kotlin sources and modules.

The version of Kotlin to use is usually defined as the `kotlin_version` property:

``` groovy
buildscript {
    ext.kotlin_version = '{{ site.data.releases.latest.version }}'

    repositories {
        mavenCentral()
    }

    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}
```

This is not required when using Kotlin Gradle plugin 1.1.1 and above with the [Gradle plugins DSL](https://docs.gradle.org/current/userguide/plugins.html#sec:plugins_block). -->

`kotlin-gradle-plugin` создает Kotlin ресурсы и модули.

Версия Kotlin обычно указывается в `kotlin_version` свойстве:

``` groovy
buildscript {
    ext.kotlin_version = '{{ site.data.releases.latest.version }}'

    repositories {
        mavenCentral()
    }

    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}
```

Это не требуется когда версия Kotlin Gradle плагина 1.1.1 и выше с [Gradle plugins DSL](https://docs.gradle.org/current/userguide/plugins.html#sec:plugins_block).

## Поддержка JVM

<!-- To target the JVM, the Kotlin plugin needs to be applied:

``` groovy
apply plugin: "kotlin"
```

Or, starting with Kotlin 1.1.1, the plugin can be applied using the [Gradle plugins DSL](https://docs.gradle.org/current/userguide/plugins.html#sec:plugins_block):

```groovy
plugins {
    id "org.jetbrains.kotlin.jvm" version "{{ site.data.releases.latest.version }}"
}
```
The `version` should be literal in this block, and it cannot be applied from another build script.

Kotlin sources can be mixed with Java sources in the same folder, or in different folders. The default convention is using different folders:

``` groovy
project
    - src
        - main (root)
            - kotlin
            - java
```

The corresponding *sourceSets* property should be updated if not using the default convention:

``` groovy
sourceSets {
    main.kotlin.srcDirs += 'src/main/myKotlin'
    main.java.srcDirs += 'src/main/myJava'
}
``` -->

Чтобы настроить JVM необходимо использовать плагин Kotlin:

``` groovy
apply plugin: "kotlin"
```

Начиная с Kotlin 1.1.1 плагин может быть применен автоматически с использованием [Gradle plugins DSL](https://docs.gradle.org/current/userguide/plugins.html#sec:plugins_block):

```groovy
plugins {
    id "org.jetbrains.kotlin.jvm" version "{{ site.data.releases.latest.version }}"
}
```

`version` должен быть в данном блоке(как на примере), и не может быть использован из других скриптов сборки.

Kotlin ресурсы могут сочетаться с Java ресурсами из одной папки или разных папок. По умолчанию используются разные папки:

``` groovy
project
    - src
        - main (root)
            - kotlin
            - java
```

Соответствующее свойство *sourceSets* должно быть обновлено, если не использовано значение по умолчанию:

``` groovy
sourceSets {
    main.kotlin.srcDirs += 'src/main/myKotlin'
    main.java.srcDirs += 'src/main/myJava'
}
```

## Поддержка JavaScript

<!-- When targeting JavaScript, a different plugin should be applied:

``` groovy
apply plugin: "kotlin2js"
```

This plugin only works for Kotlin files so it is recommended to keep Kotlin and Java files separate (if it's the case that the same project contains Java files). As with
targeting the JVM, if not using the default convention, we need to specify the source folder using *sourceSets*:

``` groovy
sourceSets {
    main.kotlin.srcDirs += 'src/main/myKotlin'
}
```

In addition to the output JavaScript file, the plugin by default creates an additional JS file with binary descriptors.
This file is required if you're building a re-usable library that other Kotlin modules can depend on, and should be distributed together with the result of translation.
The generation is controlled by the  `kotlinOptions.metaInfo` option:

``` groovy
compileKotlin2Js {
    kotlinOptions.metaInfo = true
}
``` -->

Для использования JavaScript должен применяться другой плагин:

``` groovy
apply plugin: "kotlin2js"
```

Данный плагин работает только для Kotlin файлов, таким образом рекомендуется хранить Kotlin и Java файлы раздельно (Это в случае если данный проект содержит Java файлы). Как и с поддержкой JVM, если не используются значения по умолчанию, необходимо определить папки ресурсов в *sourceSets*:

``` groovy
sourceSets {
    main.kotlin.srcDirs += 'src/main/myKotlin'
}
```

В дополнение к выходному файлу JavaScript плагин по умолчанию создает дополнительный JS-файл с двоичными дескрипторами.
Эти файлы требуются в случае создании библиотеки, чтобы Kotlin модули могли наследоваться от нее, и должны распространяться вместе с библиотекой.

Процесс контролируется  `kotlinOptions.metaInfo` свойством:

``` groovy
compileKotlin2Js {
	kotlinOptions.metaInfo = true
}
```

## Поддержка Android

<!-- Android's Gradle model is a little different from ordinary Gradle, so if we want to build an Android project written in Kotlin, we need
*kotlin-android* plugin instead of *kotlin*:

``` groovy
buildscript {
    ext.kotlin_version = '{{ site.data.releases.latest.version }}'

    ...

    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'
```

Don't forget to configure the [standard library dependency](#configuring-dependencies). -->

Android's Gradle модель отличается от обычной Gradle модели, поэтому если необходимо собрать Android проект использующий Kotlin, необходимо применить
*kotlin-android* плагин вместо *kotlin*:

``` groovy
buildscript {
    ext.kotlin_version = '{{ site.data.releases.latest.version }}'

    ...

    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'
```

Не забудьте настроить [standard library dependency](#configuring-dependencies).

### Android Studio

<!-- 
If using Android Studio, the following needs to be added under android:

``` groovy
android {
  ...

  sourceSets {
    main.java.srcDirs += 'src/main/kotlin'
  }
}
```

This lets Android Studio know that the kotlin directory is a source root, so when the project model is loaded into the IDE it will be properly recognized. Alternatively, you can put Kotlin classes in the Java source directory, typically located in `src/main/java`.
 -->

При использовании Android Studio, необходимо добавить следующее под блоком android:

``` groovy
android {
  ...

  sourceSets {
    main.java.srcDirs += 'src/main/kotlin'
  }
}
```

Это позводит Android Studio находить kotlin директорию в папке с ресурсами, поэтому, когда проект будет загружен в IDE, он будет правильно распознан.
Другой способ - это добавить Kotlin классы в Java директорию, расположенную по пути `src/main/java`.


## Настройка зависимостей

<!-- 
In addition to the `kotlin-gradle-plugin` dependency shown above, you need to add a dependency on the Kotlin standard library:

``` groovy
repositories {
    mavenCentral()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib"
}
```

If you target JavaScript, use `compile "org.jetbrains.kotlin:kotlin-stdlib-js"` instead.

If you're targeting JDK 7 or JDK 8, you can use extended versions of the Kotlin standard library which contain
additional extension functions for APIs added in new JDK versions. Instead of `kotlin-stdlib`, use one of the
following dependencies:

``` groovy
compile "org.jetbrains.kotlin:kotlin-stdlib-jdk7"
compile "org.jetbrains.kotlin:kotlin-stdlib-jdk8"
```

In Kotlin 1.1.x, use `kotlin-stdlib-jre7` and `kotlin-stdlib-jre8` instead.

If your project uses [Kotlin reflection](/api/latest/jvm/stdlib/kotlin.reflect.full/index.html) or testing facilities, you need to add the corresponding dependencies as well:

``` groovy
compile "org.jetbrains.kotlin:kotlin-reflect"
testCompile "org.jetbrains.kotlin:kotlin-test"
testCompile "org.jetbrains.kotlin:kotlin-test-junit"
```

Starting with Kotlin 1.1.2, the dependencies with group `org.jetbrains.kotlin` are by default resolved with the version
taken from the applied plugin. You can provide the version manually using the full dependency notation like
`compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"`. -->

В дополнение к зависимостям `kotlin-gradle-plugin`, показанным выше, вам нужно добавить зависимость от стандартной библиотеки Kotlin:

``` groovy
repositories {
    mavenCentral()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib"
}
```

Для поддержки JavaScript используйте `compile "org.jetbrains.kotlin:kotlin-stdlib-js"`.

Если вы собираетесь включить поддержку JDK 7 или JDK 8, вы можете использовать расширенную версию Kotlin standard library, которая содержит расширенный API, добавленный в новой JDK версии. Вместо `kotlin-stdlib`, используйте одну из следующих зависимостей:

``` groovy
compile "org.jetbrains.kotlin:kotlin-stdlib-jdk7"
compile "org.jetbrains.kotlin:kotlin-stdlib-jdk8"
```

В Kotlin 1.1.x, используйте `kotlin-stdlib-jre7` вместо `kotlin-stdlib-jre8`.

Если проект содержит [Kotlin reflection](/api/latest/jvm/stdlib/kotlin.reflect.full/index.html) или средства тестирования, вам также необходимо добавить соответствующие зависимости:

``` groovy
compile "org.jetbrains.kotlin:kotlin-reflect"
testCompile "org.jetbrains.kotlin:kotlin-test"
testCompile "org.jetbrains.kotlin:kotlin-test-junit"
```

Начиная с Kotlin 1.1.2 зависимости из группы `org.jetbrains.kotlin` по умолчанию разрешены. Вы можете указать версию вручную, используя полную зависимость, например
`compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"`.

## Аннотации

<!-- See the description of [Kotlin annotation processing tool](kapt.html) (`kapt`).
 -->

См. Описание [Kotlin annotation processing tool](kapt.html) (`kapt`).

## Пошаговая компиляция

<!-- 
Kotlin supports optional incremental compilation in Gradle.
Incremental compilation tracks changes of source files between builds so only files affected by these changes would be compiled.

Starting with Kotlin 1.1.1, incremental compilation is enabled by default.

There are several ways to override the default setting:

  1. add `kotlin.incremental=true` or `kotlin.incremental=false` line either to a `gradle.properties` or a `local.properties` file;

  2. add `-Pkotlin.incremental=true` or `-Pkotlin.incremental=false` to gradle command line parameters. Note that in this case the parameter should be added to each subsequent build, and any build with disabled incremental compilation invalidates incremental caches.

When incremental compilation is enabled, you should see the following warning message in your build log:
```
Using kotlin incremental compilation
```

Note, that the first build won't be incremental. -->

Kotlin поддерживает пошаговую компиляцию в Gradle.
Пошаговая компиляция отслеживает изменения исходных файлов между сборками, поэтому будут скомпилированы только файлы, затронутые этими изменениями.

Начиная с Kotlin 1.1.1 пошаговая компиляция доступна по умолчанию.

Существует несколько способов переопределить настройку по умолчанию:

  1. Добавить `kotlin.incremental=true` или `kotlin.incremental=false` либо в `gradle.properties`, либо в `local.properties` файл;

  2. Добавить `-Pkotlin.incremental=true` или `-Pkotlin.incremental=false` как параметр командной строки. Обратите внимание, что в этом случае параметр должен быть добавлен к каждой последующей сборке, а любая сборка с отключенной пошаговой компиляцией делает недействительными пошаговые кеши.

Когда пошаговая компиляция включена, в журнале сборки должно появиться следующее сообщение:

```
Using kotlin incremental compilation
```

Обратите внимание, что первая сборка не будет пошаговой.

## Поддержка Coroutines(сопрограммы)

<!-- 
[Coroutines](coroutines.html) support is an experimental feature in Kotlin 1.2, so the Kotlin compiler reports a warning when you use coroutines in your project.
To turn off the warning, add the following block to your `build.gradle` file:

``` groovy
kotlin {
    experimental {
        coroutines 'enable'
    }
}
```
 -->

[Coroutines](coroutines.html) добавлены как эксперементальная функция в Kotlin 1.2, поэтому компилятор Kotlin сообщает об использовании сопрограмм в вашем проекте.
Чтобы отключить предупреждение, добавьте следующий блок в свой файл `build.gradle`:

``` groovy
kotlin {
    experimental {
        coroutines 'enable'
    }
}
```

## Названия модулей

<!-- The Kotlin modules that the build produces are named accordingly to the `archivesBaseName` property of the project. If a project has a broad name like `lib` or `jvm`, which is common for subprojects, the Kotlin output files related to the module (`*.kotlin_module`) might clash with those from third-party modules with the same name. This causes problems when a project is packaged into a single archive (e.g. APK).

To avoid this, consider setting a unique `archivesBaseName` manually:

``` groovy
archivesBaseName = 'myExampleProject_lib'
``` -->

Модули Kotlin, создаваемые сборкой, называются соответственно свойству «archivesBaseName» в проекте. Если проект имеет широкое имя, например `lib` или `jvm`, что является общим для подпроектов, выходные файлы Kotlin, относящиеся к модулю (`* .kotlin_module`), могут столкнуться с модулями из сторонних модулей с тем же именем, это вызывает проблемы, когда проект упаковывается в один архив (например, APK).

Чтобы этого избежать, рассмотрите возможность установки уникального `archivesBaseName` вручную:

``` groovy
archivesBaseName = 'myExampleProject_lib'
```

## Поддержка кеша Gradle Build Cache (начиная с версии 1.2.20)

<!-- The Kotlin plugin supports [Gradle Build Cache](https://guides.gradle.org/using-build-cache/) (Gradle version 4.3 and above is required; caching is disabled with lower versions).

The kapt annotation processing tasks are not cached by default since annotation processors run arbitrary code that may not necessarily transform the task inputs into the outputs, might access and modify the files that are not tracked by Gradle etc. To enable caching for kapt anyway, add the following lines to the build script:

``` groovy
kapt {
    useBuildCache = true
}
```

To disable the caching for all Kotlin tasks, set the system property flag `kotlin.caching.enabled` to `false` (run the build with the argument `-Dkotlin.caching.enabled=false`). -->

Kotlin плагин поддерживает [Gradle Build Cache](https://guides.gradle.org/using-build-cache/) (Требуется версия 4.3 и выше; кеширование отключено в более низких версиях).

Задачи обработки аннотаций Kapt не кэшируются по умолчанию, поскольку обработчики аннотаций запускают произвольный код, который не обязательно преобразовывает входящие задачи в выходящие и может обращаться к файлам, которые не отслеживаются Gradle, и изменять их. Чтобы включить кэширование для kapt в любом случае, добавьте следующие строки в сценарий сборки:

``` groovy
kapt {
    useBuildCache = true
}
```

Чтобы отключить кеширование для всех задач Kotlin, установите для параметра системного свойства `kotlin.caching.enabled` значение `false` (запустите сборку с аргументом `-Dkotlin.caching.enabled=false`).

## Опции компилятора

<!-- 
To specify additional compilation options, use the `kotlinOptions` property of a Kotlin compilation task.

When targeting the JVM, the tasks are called `compileKotlin` for production code and `compileTestKotlin`
for test code. The tasks for custom source sets of are called accordingly to the `compile<Name>Kotlin` pattern. 

The names of the tasks in Android Projects contain the [build variant](https://developer.android.com/studio/build/build-variants.html) names and follow the pattern `compile<BuildVariant>Kotlin`, for example, `compileDebugKotlin`, `compileReleaseUnitTestKotlin`.

When targeting JavaScript, the tasks are called `compileKotlin2Js` and `compileTestKotlin2Js` respectively, and `compile<Name>Kotlin2Js` for custom source sets.

To configure a single task, use its name. Examples:

``` groovy
compileKotlin {
    kotlinOptions.suppressWarnings = true
}

compileKotlin {
    kotlinOptions {
        suppressWarnings = true
    }
}
```

It is also possible to configure all Kotlin compilation tasks in the project:

``` groovy
tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).all {
    kotlinOptions {
        // ...
    }
}
```

A complete list of options for the Gradle tasks follows: -->

Чтобы указать дополнительные параметры компиляции используйте свойство `kotlinOptions`.

При поддержки JVM опции называются `compileKotlin` для релизного кода и `compileTestKotlin`
для тестового кода. Задачи для настраиваемых исходных ресурсов вызываются по шаблону `compile <Name> Kotlin`.

Имена задач в проектах Android содержат [build variant](https://developer.android.com/studio/build/build-variants.html) и следуют шаблону `compile <BuildVariant> Kotlin`, например, `compileDebugKotlin`, `compileReleaseUnitTestKotlin`.

При поддержки JavaScript задачи называются `compileKotlin2Js` и `compileTestKotlin2Js` соответственно, и `compile <Name> Kotlin2Js` для настраиваемых ресурсов.

Чтобы настроить задачу, используйте ее имя. Примеры:

``` groovy
compileKotlin {
    kotlinOptions.suppressWarnings = true
}

compileKotlin {
    kotlinOptions {
        suppressWarnings = true
    }
}
```

Также возможно сконфигурировать все задачи компиляции Kotlin:

``` groovy
tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).all {
    kotlinOptions {
        // ...
    }
}
```

Ниже приведен полный список параметров для задач Gradle:

### Атрибуты, общие для JVM, JS и JS DCE

<!-- | Name | Description | Possible values |Default value |
|------|-------------|-----------------|--------------|
| `allWarningsAsErrors` | Report an error if there are any warnings |  | false |
| `suppressWarnings` | Generate no warnings |  | false |
| `verbose` | Enable verbose logging output |  | false |
| `freeCompilerArgs` | A list of additional compiler arguments |  | [] | -->

| Имя | Описание | Возможные значения | Значение по умолчанию |
|------|-------------|-----------------|--------------|
| `allWarningsAsErrors` | Сообщить об ошибке если есть какие-либо предупреждения (warnings) |  | false |
| `suppressWarnings` | Не создавать предупреждения (warnings) |  | false |
| `verbose` | Включить подробный вывод журнала |  | false |
| `freeCompilerArgs` | Список дополнительных аргументов компилятора |  | [] |

### Атрибуты, общие для JVM и JS

<!-- | Name | Description | Possible values |Default value |
|------|-------------|-----------------|--------------|
| `apiVersion` | Allow to use declarations only from the specified version of bundled libraries | "1.0", "1.1", "1.2", "1.3 (EXPERIMENTAL)" |  |
| `languageVersion` | Provide source compatibility with specified language version | "1.0", "1.1", "1.2", "1.3 (EXPERIMENTAL)" |  | -->

| Имя | Описание | Возможные значения | Значение по умолчанию |
|------|-------------|-----------------|--------------|
| `apiVersion` | Разрешить использование объявлений (declarations) только из указанных версий зависимых библиотек | "1.0", "1.1", "1.2", "1.3 (EXPERIMENTAL)" |  |
| `languageVersion` | Обеспечить совместимость источника с указанной языковой версией | "1.0", "1.1", "1.2", "1.3 (EXPERIMENTAL)" |  |

### Атрибуты, специфичные для JVM

<!-- | Name | Description | Possible values |Default value |
|------|-------------|-----------------|--------------|
| `javaParameters` | Generate metadata for Java 1.8 reflection on method parameters |  | false |
| `jdkHome` | Path to JDK home directory to include into classpath, if differs from default JAVA_HOME |  |  |
| `jvmTarget` | Target version of the generated JVM bytecode (1.6 or 1.8), default is 1.6 | "1.6", "1.8" | "1.6" |
| `noJdk` | Don't include Java runtime into classpath |  | false |
| `noReflect` | Don't include Kotlin reflection implementation into classpath |  | true |
| `noStdlib` | Don't include Kotlin runtime into classpath |  | true | -->

| Имя | Описание | Возможные значения | Значение по умолчанию |
|------|-------------|-----------------|--------------|
| `javaParameters` | Генерировать метаданные для reflection Java 1.8 по параметрам метода |  | false |
| `jdkHome` | Путь к домашнему каталогу JDK для включения в путь классов, если отличается от стандартного JAVA_HOME |  |  |
| `jvmTarget` | Целевая версия сгенерированного байт-кода JVM (1.6 или 1.8), по умолчанию - 1.6 | "1.6", "1.8" | "1.6" |
| `noJdk` | Не включать Java в classpath |  | false |
| `noReflect` | Не включайте реализацию Kotlin reflection в classpath |  | true |
| `noStdlib` | Не включать Kotlin runtime в classpath |  | true |

### Атрибуты, специфичные для JS

<!--  | Name | Description | Possible values |Default value |
|------|-------------|-----------------|--------------|
| `friendModulesDisabled` | Disable internal declaration export |  | false |
| `main` | Whether a main function should be called | "call", "noCall" | "call" |
| `metaInfo` | Generate .meta.js and .kjsm files with metadata. Use to create a library |  | true |
| `moduleKind` | Kind of a module generated by compiler | "plain", "amd", "commonjs", "umd" | "plain" |
| `noStdlib` | Don't use bundled Kotlin stdlib |  | true |
| `outputFile` | Output file path |  |  |
| `sourceMap` | Generate source map |  | false |
| `sourceMapEmbedSources` | Embed source files into source map | "never", "always", "inlining" |  |
| `sourceMapPrefix` | Prefix for paths in a source map |  |  |
| `target` | Generate JS files for specific ECMA version | "v5" | "v5" |
| `typedArrays` | Translate primitive arrays to JS typed arrays |  | true |-->

| Имя | Описание | Возможные значения | Значение по умолчанию |
|------|-------------|-----------------|--------------|
| `friendModulesDisabled` | Отключить экспорт внутренних описаний (declaration) |  | false |
| `main` | Должна ли быть вызвана основная функция | "call", "noCall" | "call" |
| `metaInfo` | Создание файлов .meta.js и .kjsm с метаданными. Использование их для создания библиотеки |  | true |
| `moduleKind` | Тип модуля, сгенерированного компилятором | "plain", "amd", "commonjs", "umd" | "plain" |
| `noStdlib` | Не использовать Kotlin stdlib |  | true |
| `outputFile` | Выходной путь файла |  |  |
| `sourceMap` | Создание источника (source map) |  | false |
| `sourceMapEmbedSources` | Вставлять исходные файлы (source map) в источник | "never", "always", "inlining" |  |
| `sourceMapPrefix` | Префикс для путей в источнике (source map) |  |  |
| `target` | Создание JS-файлов для конкретной версии ECMA | "v5" | "v5" |
| `typedArrays` | Перевести примитивные массивы на массивы в JS |  | true |


## Создание документации

<!-- To generate documentation for Kotlin projects, use [Dokka](https://github.com/Kotlin/dokka);
please refer to the [Dokka README](https://github.com/Kotlin/dokka/blob/master/README.md#using-the-gradle-plugin)
for configuration instructions. Dokka supports mixed-language projects and can generate output in multiple
formats, including standard JavaDoc. -->

Чтобы создать [документацию](https://kotlinlang.ru/docs/reference/kotlin-doc.html) для проектов Kotlin, используйте [Dokka](https://github.com/Kotlin/dokka);
обратитесь к [Dokka README](https://github.com/Kotlin/dokka/blob/master/README.md#using-the-gradle-plugin)
для инструкций по настройке. Dokka поддерживает проекты на смешанном языке и может генерировать выходные данные в нескольких
форматов, включая стандартный JavaDoc.

## OSGi

<!-- For OSGi support see the [Kotlin OSGi page](kotlin-osgi.html).
 -->

Для поддержки OSGi см. Страницу [Kotlin OSGi](kotlin-osgi.html).

## Примеры

<!-- The following examples show different possibilities of configuring the Gradle plugin:
 -->

В следующих примерах показаны различные возможности настройки плагина Gradle:

* [Kotlin](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/hello-world)
* [Mixed Java and Kotlin](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/mixed-java-kotlin-hello-world)
* [Android](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/android-mixed-java-kotlin-project)
* [JavaScript](https://github.com/JetBrains/kotlin/tree/master/libraries/tools/kotlin-gradle-plugin-integration-tests/src/test/resources/testProject/kotlin2JsProject)
