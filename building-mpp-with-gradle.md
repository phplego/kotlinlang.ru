---
type: doc
layout: reference
title: "Создание мультиплатформенных проектов с помощью Gradle"
---

<!-- # Building Multiplatform Projects with Gradle -->

# Создание мультиплатформенных проектов с помощью Gradle

<!-- > Multiplatform projects are an experimental feature in Kotlin 1.2 and 1.3. All of the language
and tooling features described in this document are subject to change in future Kotlin versions.
{:.note} -->

> Мультиплатформенные проекты являются экспериментальной особенностью в Kotlin 1.2 и 1.3. 
Все языки и инструменты, описанные в этом документе, могут быть изменены в будущих версиях Kotlin.

<!-- This document describes how [Kotlin multiplatform projects](multiplatform.html) are configured and built using 
Gradle. Only Gradle versions 4.7 and above can be used, older Gradle versions are not supported. -->

В этом документе описывается, как [мультиплатформенные проекты Kotlin](multiplatform.html) создаются и настраиваются с использованием Gradle. 
Могут использоваться только версии Gradle 4.7 и выше, более ранние версии Gradle не поддерживаются.

<!-- Gradle Kotlin DSL support has not been implemented yet for multiplatform projects, it will be added in the future 
updates. Please use the Groovy DSL in the build scripts. -->

Поддержка Gradle Kotlin DSL еще не реализована для мультиплатформенных проектов, но она будет добавлена в будущие обновления. 
Пожалуйста, используйте Groovy DSL в сценариях сборки.

## Настройка мультиплатформенного проекта

<!-- You can create a new multiplatform project in the IDE by selecting one of the multiplatform project templates in the 
New Project dialog under the "Kotlin" section. -->

Можно создать новый мультиплатформенный проект в IDE, выбрав один из шаблонов мультиплатформенных проектов в диалоговом окне «Новый проект» в разделе «Kotlin».

<!-- For example, if you choose "Kotlin (Multiplatform Library)", a library project is created that has three [targets](#setting-up-targets), one 
for the JVM, one for JS, and one for the Native platform that you are using. These are configured in the `build.gradle`
script in the following way: -->

Например, если выбран «Kotlin (Multiplatform Library)», то создается проект библиотеки, ориентированный на 3 [цели](#настройка-целей): JVM, JS и используемую Native платформу. 
Они сконфигурированы в сценарии `build.gradle` следующим образом:

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '{{ site.data.releases.latest.version }}'
}

repositories {
    mavenCentral()
}

kotlin {
    targets {
        fromPreset(presets.jvm, 'jvm')
        fromPreset(presets.js, 'js')
        fromPreset(presets.mingwX64, 'mingw')
    }
    
    sourceSets { /* ... */ }
}
```

<!-- The three targets are created from the presets that provide some [default configuration](#default-project-layout). 
There are presets for each of the [supported platforms](#supported-platforms). -->

Три цели создаются из предустановок, которые предоставляют некоторую [конфигурацию по умолчанию](#макет-проекта-по-умолчанию). 
Здесь приведены предустановки для каждой поддерживаемой платформы.

<!-- The [source sets](#configuring-source-sets) and their [dependencies](#adding-dependencies) are then configured as follows: -->

Затем устанавливаются [наборы исходных кодов](#конфигурирование-наборов-исходных-кодов) и их [зависимости](#добавление-зависимостей):

```groovy
plugins { /* ... */ }

kotlin {
    targets { /* ... */ }

    sourceSets { 
        commonMain {
            dependencies {
                implementation 'org.jetbrains.kotlin:kotlin-stdlib-common'
            }
        }
        commonTest {
            dependencies {
                implementation 'org.jetbrains.kotlin:kotlin-test-common'
                implementation 'org.jetbrains.kotlin:kotlin-test-annotations-common'
            }
        }
        jvmMain {
            dependencies {
                implementation 'org.jetbrains.kotlin:kotlin-stdlib-jdk8'
            }
        }
        jvmTest {
            dependencies {
                implementation 'org.jetbrains.kotlin:kotlin-test'
                implementation 'org.jetbrains.kotlin:kotlin-test-junit'
            }
        }
        jsMain { /* ... */ }
        jsTest { /* ... */ }
        mingwMain { /* ... */ }
        mingwTest { /* ... */ }
    }
}
```

<!-- These are the [default source set names](#default-project-layout) for the production and test sources for the targets configured above. The source 
sets `commonMain` and `commonTest` are included into production and test compilations, respectively, of all targets. 
Note that the dependencies for common source sets `commonMain` and `commonTest` are the common artifacts, and the 
platform libraries go to the source sets of the specific targets. -->

<!-- The details on project structure and the DSL can be found in the following sections. -->

[Представленные по умолчанию имена наборов исходных кодов](#макет-проекта-по-умолчанию) предназначены для основных и тестовых исходных кодов, указанных выше. Наборы `commonMain` и `commonTest` включаются в основную и тестовую компиляции, соответственно, для всех целевых платформ. Обратите внимание на то, что зависимости для общих наборов исходных кодов `commonMain` и `commonTest` общие, а библиотеки платформ идут для конкретных платформ.

Подробные сведения о структуре проекта и DSL можно найти в следующих разделах.

## Плагин Gradle

<!-- To setup a multiplatform project from scratch, first, apply the `kotlin-multiplatform` plugin to the project by adding the following to the `build.gradle` file: -->

Для создания мультиплатформенного проекта с нуля применим к проекту плагин `kotlin-multiplatform` путем добавления следующего кода в 
`build.gradle`

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '{{ site.data.releases.latest.version }}'
}
```

<!-- This creates the `kotlin` extension at the top level. You can then access it in the build script for: -->
Так создается расширение `kotlin` на верхнем уровне. Можно получить к нему доступ в скрипте сборки для:

<!-- * [setting up the targets](#setting-up-targets) for multiple platforms (no targets are created by default);
* [configuring the source sets](#configuring-source-sets) and their [dependencies](#adding-dependencies); -->

* [Настройки целей](#настройка-целей) для нескольких платформ (по умолчанию не создается ни одной цели);
* [Конфигурирования наборов исходных кодов](#макет-проекта-по-умолчанию) и их [зависимостей](#добавление-зависимостей);

## Настройка целей

<!-- A target is a part of the build responsible for compiling, testing, and packaging a piece of software aimed for
one of the [supported platforms](#supported-platforms). -->

Цель - часть сборки, отвечающей за компиляцию, тестирование и упаковку части программного обеспечения, предназначенного для одной из [поддерживаемых платформ](#поддерживаемые-платформы).

<!-- As the platforms are different, targets are built in different ways as well and have various platform-specific 
settings. The Gradle plugin bundles a number of presets for the supported platforms. A preset can be used to 
create a target by just providing a name as follows: -->

Поскольку платформы различны, цели также построены по-разному и имеют разные настройки для конкретной платформы. Плагин Gradle объединяет ряд предустановок для поддерживаемых платформ. Предустановку можно использовать для создания цели, просто указывая имя, следующим образом:

``` groovy
kotlin {
    targets {
        fromPreset(presets.jvm, 'jvm6') // Create a JVM target by the name 'jvm6'
        
        fromPreset(presets.linuxX64, 'linux') { 
            /* You can specify additional settings for the 'linux' target here */
        } 
    }
}
``` 

<!-- Building a target requires compiling Kotlin once or multiple times. Each Kotlin compilation of a target may serve a 
different purpose (e.g. production code, tests) and incorporate different [source sets](#configuring-source-sets). The compilations
of a target may be accessed in the DSL, for example, to get the task names, dependency files and compilation outputs: -->

Создание цели требует компиляции Kotlin один или несколько раз. Каждая компиляция цели Kotlin может служить другой цели (например, производственный код, тесты) и включать различные [наборы исходных кодов](#макет-проекта-по-умолчанию). К компиляциям цели можно обратиться в DSL, например, чтобы получить имена задач, файлы зависимостей и выходы компиляции:

```groovy
kotlin {
    targets {
        fromPreset(presets.jvm, 'jvm6') { 
            def mainKotlinTaskName = compilations.main.compileKotlinTaskName
            def mainOutputs = compilations.main.output
            def testRuntimeClasspath = compilations.test.runtimeDependencyFiles            
        }
    }
}
```

Для изменения параметров компиляции компилятора Kotlin следует использовать задачу компиляции, которая может быть найдена по ее имени:

```groovy
kotlin {
    targets {
        fromPreset(presets.jvm, 'jvm8') {
            // Configure a single target's compilations (main and test)
            compilations.all {
                tasks[compileKotlinTaskName].kotlinOptions { 
                    jvmTarget = '1.8'
                }
            }
        }
        
        /* ... */
        
        // Configure all compilations of all targets:
        all { 
            compilations.all {
                tasks[compileKotlinTaskName].kotlinOptions {
                    allWarningsAsErrors = true
                }
            }
        }
    }
}
```

<!-- All of the targets may share some of the sources and may have platform-specific sources in their compilations as well. 
See [Configuring source sets](#configuring-source-sets) for details. -->

Все цели могут совместно использовать некоторые исходные коды и также могут иметь специфические для каждой платформы исходные коды в своих компиляциях. Подробнее см. [Настройка наборов исходных кодов](#макет-проекта-по-умолчанию) .

<!-- Some targets may require additional configuration. For Android and iOS examples, see the [Multiplatform Project: iOS and Android](/docs/tutorials/native/mpp-ios-android.html) tutorial. -->

Для некоторых целей может потребоваться дополнительная настройка. Примеры для Android и iOS см. в руководстве [Мультиплатформенный проект: iOS и Android](/docs/tutorials/native/mpp-ios-android.html).

### Поддерживаемые платформы

<!-- There are target presets that one can apply using `fromPreset(presets.<presetName>, '<targetName>')`, as described above, for the
following target platforms: -->

Существуют предустановки целей, которые можно применять `fromPreset(presets.<имяПредустановки>, '<имяЦели>')`, как описано выше, для следующих целевых платформ:

<!-- * `jvm` for Kotlin/JVM. Note: `jvm` targets do not compile Java; -->

* `jvm` для Kotlin/JVM. Примечание. цели `jvm` не скомпилируют Java;

* `js` для Kotlin/JS;
<!-- * `android` for Android applications and libraries. Note that one of the Android Gradle 
   plugins should be applied as well; -->
   
* `android` для приложений и библиотек Android. Обратите внимание на то, что также должен применяться один из плагинов Android Gradle;
  
<!-- *  Kotlin/Native target presets (see the [notes](#using-kotlinnative-targets) below): -->

* Целевые предустановки Kotlin/Native (см. [Примечания](#использование-целей-kotlinnative) ниже):
    * `androidNativeArm32` и `androidNativeArm64` для Android NDK;
    * `iosArm32`, `iosArm64`, `iosX64` для iOS;
    * `linuxArm32Hfp`, `linuxMips32`, `linuxMipsel32`, `linuxX64` для Linux
    * `macosX64` для MacOS
    * `mingwX64` для Windows
    
    <!-- Note that some of the Kotlin/Native targets require an [appropriate host machine](#using-kotlinnative-targets) to build on. -->
    
    Обратите внимание на то, что некоторые из целей Kotlin / Native требуют создания [соответствующей хост-машины](#использование-целей-kotlinnative).
    
## Конфигурирование наборов исходных кодов

<!-- A Kotlin source set is a collection of Kotlin sources, along with their resources, dependencies, and language settings, 
which may take part in Kotlin compilations of one or more [targets](#setting-up-targets). -->

Набор исходных кодов Kotlin - это коллекция исходных кодов Kotlin, а также их ресурсы, зависимости и языковые настройки, которые могут принимать участие в компиляции одной или нескольких [целей](#настройка-целей) Kotlin.

<!-- If you apply a target preset, some source sets are created and configured by default. See [Default Project Layout](#default-project-layout). -->

Если применяются целевые предустановки, то некоторые наборы исходных кодов создаются и настраиваются по умолчанию. См. [Макет проекта по умолчанию](#макет-проекта-по-умолчанию).

<!-- The source sets are configured within a `sourceSets { ... }` block of the `kotlin { ... }` extension: -->

Наборы источников настраиваются внутри блока `sourceSets { ... }` расширения `kotlin { ... }`:

```groovy
kotlin { 
    targets { /* ... */ }
    
    sourceSets { 
        foo { /* ... */ } // create or configure a source set by the name 'foo' 
        bar { /* ... */ }
    }
}
``` 

<!-- > Note: creating a source set does not link it to any target. Some source sets are [predefined](#default-project-layout) 
and thus compiled by default. However, custom source sets always need to be explicitly directed to the compilations. 
See: [Connecting source sets](#connecting-source-sets). {:.note} -->

> Примечание: создание набора исходных кодов не связывает его с какой-либо целью. Некоторые наборы исходных кодов предопределены и, таким образом, уже скомпилированы по умолчанию. Однако настраиваемые наборы исходных кодов всегда должны быть явно направлены на компиляцию. См. [Подключение наборов исходных кодов](#подключение-наборов-исходных-кодов).

Имена наборов исходных кодов чувствительны к регистру. При обращении к набору исходных кодов по умолчанию убедитесь, что префикс имени совпадает с именем цели, например, источником, заданным `iosX64Main` для цели `iosX64`.

<!--A source set by itself is platform-agnostic, but
it can be considered platform-specific if it is only compiled for a single platform. A source set can, therefore, contain either
common code shared between the platforms or platform-specific code. -->

Набор исходных кодов сам по себе является платформенно-независимым, но его можно рассматривать в качестве специфичного для платформы, если он скомпилирован только для одной этой платформы. Поэтому исходный набор может содержать либо общий код, разделяемый между платформами, либо код, специфичный для платформы.

<!-- To add Kotlin source directories and resources to a source set, use its `kotlin` and `resources` `SourceDirectorySet`s: -->

Чтобы добавить исходные каталоги и ресурсы Kotlin в исходный набор, используйте его `SourceDirectorySet`ы `kotlin` и `resources`:

```groovy
kotlin { 
    sourceSets { 
        commonMain {
            kotlin.srcDir('src')
            resources.srcDir('res')
        } 
    }
}
``` 

### Подключение наборов исходных кодов

<!-- Kotlin source sets may be connected with the 'depends on' relation, so that if a source set `foo`  depends on a source set `bar` then: -->

Наборы исходных кодов Kotlin могут быть связаны с отношением 'зависит от', поэтому, если набор исходных кодов 'foo' зависит от набора исходных кодов 'bar', тогда:

<!-- * whenever `foo` is compiled for a certain target, `bar` takes part in that compilation as well and is also compiled 
into the same target binary form, such as JVM class files or JS code; -->

* всякий раз, когда `foo` компилируется для определенной цели, `bar` также участвует в этой компиляции и также скомпилируется в одну и ту же целевую бинарную форму, такую как файлы классов JVM или JS-код;

<!-- * the resources of `bar` are always processed and copied along with the resources of `foo`; -->

* ресурсы `bar` всегда обрабатываются и копируются вместе с ресурсами `foo`;

<!-- * sources of `foo` 'see' the declarations of `bar`, including the `internal` ones, and the [dependencies](#adding-dependencies) of `bar`, even those
 specified as `implementation` dependencies; -->
 
 * исходный код `foo` «видит» объявления `bar`, в том числе и `внутренние`, а также [зависимости](#добавление-зависимостей) от `bar`, даже те, которые указаны в качестве зависимостей `implementation`;
  
<!--* `foo` may contain [platform-specific implementations](platform-specific-declarations.html) for the expected declarations of `bar`; -->

* `foo` может содержать реализации, специфичные для платформы, для ожидаемых объявлений `bar`;

<!-- * the [language settings](#language-settings) of `foo` and `bar` should be consistent; -->

* [Языковые настройки](#настройки-языка) `foo` и `bar` должны быть согласованы;

<!-- Circular source set dependencies are prohibited. -->

Рекурсивные зависимости в наборах исходных кодов запрещены.

<!-- The source sets DSL can be used to define these connections between the source sets: -->

DSL наборов исходных кодов может использоваться для определения соединений между наборами исходных кодов:

```groovy
 kotlin { 
     sourceSets { 
         commonMain { /* ... */ }
         allJvm {
             dependsOn commonMain
             /* ... */
         } 
     }
 }
```

<!-- Custom source sets created in addition to the [default ones](#default-project-layout) should be explicitly included into the dependencies hierarchy to be able to use declarations from other source sets and, most importantly, to take part in compilations. 
Most often, they need a `dependsOn commonMain` or `dependsOn commonTest` statement, and some of the default platform-specific source sets should depend on the custom ones, directly or indirectly: -->

Пользовательские наборы исходных кодов, созданные в дополнение к [стандартным](#макет-проекта-по-умолчанию), должны быть явно включены в иерархию зависимостей для того, чтобы иметь возможность использовать объявления из других наборов исходных кодов и, самое главное, принимать участие в компиляциях. Чаще всего им нужен оператор `dependsOn commonMain` или `dependsOn commonTest`, а некоторые из наборов исходных кодов для конкретной платформы должны напрямую или косвенно зависеть от пользовательских:

```groovy
kotlin { 
    targets {
        fromPreset(presets.mingwX64, 'windows')
        fromPreset(presets.linuxX64, 'linux')
        /* ... */
    }
    sourceSets {
        desktopTest { // custom source set with tests for the two targets
            dependsOn commonTest
            /* ... */
        }
        windowsTest { // default test source set for target 'windows'
            dependsOn desktopTest
            /* ... */
        }
        linuxTest { // default test source set for target 'linux'
            dependsOn desktopTest
        }
        /* ... */
    }
}
```

### Добавление зависимостей

<!-- To add a dependency to a source set, use a `dependencies { ... }` block of the source sets DSL. Four kinds of dependencies
are supported: -->

Для добавления зависимости к набору исходных кодов используем блок `dependencies { ... }` наборов исходных кодов DSL. Поддерживаются четыре вида зависимостей:

<!-- * `api` dependencies are used both during compilation and at runtime and are exported to library consumers. If any types 
  from a dependency are used in the public API of the current module, then it should be an `api` dependency; -->
  
* Зависимости `api` используются как во время компиляции, так и во время выполнения, и экспортируются в библиотеки. Если в публичном API текущего модуля используются какие-либо типы из зависимости, то это должна быть зависимость `api`;
  
<!-- * `implementation` dependencies are used during compilation and at runtime for the current module, but are not exposed for compilation 
  of other modules depending on the one with the `implementation` dependency. The`implementation` dependency kind should be used for 
  dependencies needed for the internal logic of a module. If a module is an endpoint application which is not published, it may
  use `implementation` dependencies instead of `api` ones. -->
  
* Зависимости `implementation` используются во время компиляции и во время выполнения текущего модуля, но не отображаются при компиляции других модулей в зависимости от того, какая зависимость `implementation` используется. Тип зависимости `implementation` должен использоваться для зависимостей, необходимых для внутренней логики модуля. Если модуль является конечным приложением, которое не публикуется, оно может использовать зависимости `implementation` вместо `api`.

<!-- * `compileOnly` dependencies are only used for compilation of the current module and are available neither at runtime nor during compilation
  of other modules. These dependencies should be used for APIs which have a third-party implementation available at runtime. -->
  
* Зависимости `compileOnly` используются только для компиляции текущего модуля и недоступны ни во время выполнения, ни при компиляции других модулей. Эти зависимости должны использоваться для API, имеющих стороннюю реализацию, доступную во время выполнения.
  
<!-- * `runtimeOnly` dependencies are available at runtime but are not visible during compilation of any module. -->

* Зависимости `runtimeOnly` доступны во время выполнения, но не отображаются во время компиляции никаких модулей.

<!-- Dependencies are specified per source set as follows:  -->

Зависимости для каждого набора исходных кодов указаны следующим образом:

```groovy
kotlin {
    sourceSets {
        commonMain {
            dependencies {
                api 'com.example:foo-metadata:1.0'
            }
        }
        jvm6Main {
            dependencies {
                api 'com.example:foo-jvm6:1.0'
            }
        }
    }
}
```

<!-- Note that for the IDE to correctly analyze the dependencies of the common sources, the common source sets need to have corresponding dependencies on the Kotlin 
metadata packages in addition to the platform-specific artifact dependencies of the platform-specific source sets. Usually, an artifact with a suffix 
`-common` (as in `kotlin-stdlib-common`) or `-metadata` is required. -->

Обратите внимание на то, что для того, чтобы IDE правильно анализировала зависимости общих исходных кодов, общие наборы исходных кодов должны иметь соответствующие зависимости от пакетов метаданных Kotlin в дополнение к зависимостям артефакта для конкретной платформы от  наборов исходных кодов для этой же платформы. Обычно при использовании опубликованной библиотеки требуется артефакт с суффиксом 
`-common` (как в случае `kotlin-stdlib-common`) или `-metadata` (если только он не опубликован с метаданными Gradle, как описано ниже).

<!--However, a `project('...')` dependency on another multiplatform project is resolved to an appropriate target automatically. It is enough to specify a single `project('...')` dependency in a source set's dependencies, and the compilations that include the source set will receive a corresponding platform-specific artifact of that project, given that it has a compatible target.-->

Однако зависимость `project('...')` от другого мультиплатформенного проекта автоматически настраивается для соответствующей цели. Достаточно указать одну зависимость `project('...')` в зависимостях набора исходных кодов, а компиляции, включающие в себя исходный набор, получат соответствующий артефакт для конкретной платформы для этого проекта, учитывая, что он имеет совместимую цель.

<!-- Likewise, if a multiplatform library is published in the experimental Gradle metadata publishing mode and the project is set up to consume the metadata as well, then it is enough to specify a dependency only once, for the common source set. Otherwise, each platform-specific source set should be provided with a corresponding platform module of the library, in addition to the common module, as shown above. -->

Аналогично, если многоплатформенная библиотека публикуется в [экспериментальном режиме публикации метаданных Gradle](#экспериментальный-режим-публикации-метаданных) и проект настроен так, чтобы потреблять метаданные, то достаточно указать зависимость только один раз для общего набора исходных кодов. В противном случае каждый набор ресурсов для конкретной платформы должен быть снабжен соответствующим платформенным модулем библиотеки в дополнение к общему модулю, как показано выше.

<!-- An alternative way to specify the dependencies is to use the Gradle built-in DSL at the top level with the configuration names following the pattern `<sourceSetName><DependencyKind>`: -->

Альтернативным способом указания зависимостей является использование встроенного DSL Gradle на верхнем уровне с именами конфигурации, следующими за шаблоном `<sourceSetName><DependencyKind>`:

```groovy
dependencies {
    commonMainApi 'com.example:foo-common:1.0'
    jvm6MainApi 'com.example:foo-jvm6:1.0'
}
```

### Настройки языка

<!-- The language settings for a source set can be specified as follows: -- >

Настройки языка для набора источников могут быть указаны следующим образом:

```groovy
kotlin {
    sourceSets {
        commonMain {
            languageSettings {
                languageVersion = '1.3' // possible values: '1.0', '1.1', '1.2', '1.3'
                apiVersion = '1.3' // possible values: '1.0', '1.1', '1.2', '1.3'
                enableLanguageFeatures('InlineClasses') 
                progressiveMode = true // false by default
            }
        }
    }
}
```

<!-- It is possible to configure the language settings of all source sets at once: -->

Можно настроить языковые настройки для всех наборов источников одновременно:

```groovy
kotlin.sourceSets.all {
    languageSettings {
        progressiveMode = true
    }
}
```

<!--Language settings of a source set affect how the sources are analyzed in the IDE. Due to the current limitations, in a Gradle build, only the language settings of the compilation's default source set are used.-->

Языковые настройки набора источников влияют на то, как исходные коды анализируются в IDE. Из-за существующих ограничений в сборке Gradle используются только настройки языка исходного набора компиляции.

<!-- The language settings are checked for consistency between source sets depending on each other. Namely, if `foo` depends on `bar`: -->

Настройки языка проверяются на согласованность между наборами исходных кодов в зависимости друг от друга. А именно, если `foo` зависит от `bar`:

<!-- * `foo` should set `languageVersion` that is greater than or equal to that of `bar`; -->

* `foo` должен установить значение `languageVersion`, большее или равное значению `bar`;

<!-- * `foo` should enable all unstable language features that `bar` enables (there's no such requirement for bugfix features); -->

* `foo` должен включить все нестабильные языковые функции, которые позволяет `bar` (к функциям исправления нет такого требования);

<!--foo should use all experimental annotations that bar uses; -->

* `foo` следует использовать все экспериментальные аннотации, которые использует `bar`;

<!-- * `apiVersion`, bugfix language features, and `progressiveMode` can be set arbitrarily; -->

* `apiVersion`, возможности исправления языка и `progressiveMode` могут быть установлены произвольно;

## Макет проекта по умолчанию

<!-- By default, each project contains two source sets, `commonMain` and `commonTest`, where one can place all the code that should be 
shared between all of the target platforms. These source sets are added to each production and test compilation, respectively. -->

По умолчанию каждый проект содержит два набора источников `commonMain` и `commonTest`, где можно разместить весь код, который должен быть общим для всех целевых платформ. Эти наборы исходных кодов добавляются, соответственно, к каждой сборке и тестовой компиляции.

<!-- Then, once a target is added, default compilations are created for it:
* `main` and `test` compilations for JVM, JS, and Native targets;
* a compilation per Android variant, for Android targets; -->

Затем, после добавления цели, для него создаются компиляции по умолчанию:

* `main` и `test` компиляции для JVM, JS и Native;
* компиляция на Android;

<!-- For each compilation, there is a default source set under the name composed as `<targetName><CompilationName>`. This default source
set participates in the compilation, and thus it should be used for the platform-specific code and dependencies, and for adding other source sets to the compilation by the means of 'depends on'. For example, a project with
targets `jvm6` (JVM) and `nodeJs` (JS) will have source sets: `commonMain`, `commonTest`, `jvm6Main`, `jvm6Test`, `nodeJsMain`, `nodeJsTest`. -->

Для каждой компиляции есть набор исходных кодов по умолчанию, заданный под именем, составленным как `<имяЦели><имяКомпиляции>`. Этот набор исходных кодов по умолчанию участвует в компиляции, и поэтому он должен использоваться для кода и зависимостей для платформы, а также для добавления других источников в компиляцию с помощью параметра 'depends on'. Например, проект с целями `jvm6` (JVM) и `nodeJs` (JS) будут иметь наборы исходных кодов: `commonMain`, `commonTest`, `jvm6Main`, `jvm6Test`, `nodeJsMain`, `nodeJsTest`.

<!-- Numerous use cases are covered by just the default source sets and don't require custom source sets. -->

Многочисленные варианты использования охватываются только стандартными наборами исходных кодов и не требуют настраиваемых наборов исходных кодов.
 
<!-- Each source set by default has its Kotlin sources under `src/<sourceSetName>/kotlin` directory and the resources under `src/<sourceSetName>/resources`. -->

Каждый установленный по умолчанию набор исходных кодов имеет свои исходные коды Kotlin в директории `src/<sourceSetName>/kotlin` и ресурсы под `src/<sourceSetName>/resources`.

<!-- In Android projects, additional Kotlin source sets are created for each Android source set. If the Android target has a name `foo`, the Android source set `bar` gets a Kotlin source set counterpart `fooBar`. The Kotlin compilations, however, are able to consume Kotlin sources from all of the directories `src/bar/java`, `src/bar/kotlin`, and `src/fooBar/kotlin`. Java sources are only read from the first of these directories. -->

В проектах Android для каждого набора файлов Android создаются дополнительные исходные коды Kotlin. Если у цели Android есть имя `foo`, то исходный набор Android `bar` получает экземпляр набора исходных кодов Kotlin `fooBar`. Компиляции Котлин, однако, способны потреблять источники Котлин из всех каталогов `src/bar/java`, `src/bar/kotlin` и `src/fooBar/kotlin`. Источники Java считываются только из первого из этих каталогов.

## Запуск тестов

<!-- A test task is created under the name `<targetName>Test` for each target that is suitable for testing.  Run the `check` task to run 
the tests for all targets. -->

Задача теста создается под именем `<имяЦели>Test` для каждой цели, подходящей для тестирования. Запустим задачу `check` для запуска тестов для всех целей.

<!-- As the `commonTest` [default source set](#default-project-layout) is added to all test compilations, tests and test tools that are needed on all target platforms may be placed there. -->

Поскольку [набор исходных кодов по умолчанию](#макет-проекта-по-умолчанию) `commonTest` по умолчанию добавляется ко всем тестовым компиляциям, там могут быть размещены тесты и инструменты тестирования, которые необходимы на всех целевых платформах.

<!-- The [`kotlin.test` API](https://kotlinlang.org/api/latest/kotlin.test/index.html) is availble for multiplatform tests. Add the `kotlin-test-common` and `kotlin-test-annotations-common` dependencies to `commonTest` to use `DefaultAsserter` and `@Test`/`@Ignore`/`@BeforeTest`/`@AfterTest` annotations in the common tests. -->

[`kotlin.test` API](https://kotlinlang.org/api/latest/kotlin.test/index.html) доступен для мультиплатформенных тестов. Добавьте зависимости `kotlin-test-common` и `kotlin-test-annotations-common` в `commonTest` для использования `DefaultAsserter` и аннотаций `@Test`/`@Ignore`/`@BeforeTest`/`@AfterTest` в общих тестах.

<!-- For JVM targets, use `kotlin-test-junit` or `kotlin-test-testng` for the corresponding asserter implementation and
annotations mapping. -->

Для JVM используем `kotlin-test-junit` или k`kotlin-test-testng` для соответствующего сопоставления исполнителей и составления аннотаций.

<!-- For Kotlin/JS targets, add kotlin-test-js as a test dependency. At this point, test tasks for Kotlin/JS are created but do not run tests by default; they should be manually configured to run the tests with a JavaScript test framework. -->

Для Kotlin/JS добавим `kotlin-test-js` в качестве тестовой зависимости. На этом этапе тестовые задачи для Kotlin/JS создаются, но не запускают тесты по умолчанию; они должны быть настроены вручную для запуска тестов с помощью тестового фреймворка JavaScript.

<!-- Kotlin/Native targets do not require additional test dependencies, and the `kotlin.test` API implementations are built-in. -->

Цели Kotlin/Native не требуют дополнительных тестовых зависимостей, а реализации API `kotlin.test` уже встроены.

## Публикация многоплатформенной библиотеки

<!-- > The set of target platforms is defined by a multiplatform library author, and they should provide all of the platform-specific implementations for the library. 
> Adding new targets for a multiplatform library at the consumer's side is not supported. -->

> Набор целевых платформ определяется автором мультиплатформенной библиотеки, и они должны предоставлять все реализации конкретной платформы для данной библиотеки. Добавление новых целей для многоплатформенной библиотеки со стороны потребителя не поддерживается.

<!-- A library built from a multiplatform project may be published to a Maven repository with the Gradle `maven-publish` plugin, which can be applied as follows: -->

Библиотека, созданная из многоплатформенного проекта, может быть опубликована в репозитории Maven с плагином Gradle `maven-publish`, который может применяться следующим образом:

```groovy
plugins {
    /* ... */
    id 'maven-publish'
}
```

<!-- Once this plugin is applied, default publications are created for each of the targets that can be built on the current host. This requires `group` and 
`version` to be set in the project. The default artifact IDs follow the pattern `<projectName>-<targetNameToLowerCase>`, for example `sample-lib-nodejs` for a target named `nodeJs` in a project 
`sample-lib`. -->

При применении данного плагина публикации по умолчанию создаются для каждой из целей, которая может быть создана на текущем хосте. Это требует задания в проекте `group` и `version`. Идентификаторы артефакта по умолчанию следуют шаблону `<имяПроекта>-<имяЦелиСтрочнымиБуквами>`, например, `sample-lib-nodejs` для цели, названной `nodeJs` в проекте `sample-lib`.

<!-- Also, an additional publication is added by default which contains serialized Kotlin declarations and is used by the IDE to analyze multiplatform libraries. -->

Кроме того, по умолчанию добавляется дополнительная публикация, содержащая сериализованные объявления Kotlin и используемая IDE для анализа мультиплатформенных библиотек.

<!-- By default, a sources JAR is added to each publication in addition to its main artifact. The sources JAR contains the sources used by the `main` compilation
of the target. -->

По умолчанию в каждую публикацию добавляется исходный код JAR, а также его главный артефакт. Исходные коды JAR содержат исходные коды, используемые при компиляции цели `main`.

<!-- The Maven coordinates can be altered and additional artifact files may be added to the publication within the `targets { ... }` block: -->

Координаты Maven могут быть изменены и в публикацию внутри блока `targets { ... }` могут быть добавлены дополнительные файлы артефактов :

```groovy
kotlin {
    targets {
        fromPreset(presets.jvm, 'jvm6') {
            /* ... */
            mavenPublication { 
                artifactId = 'sample-lib-jvm'
                artifact(jvmJavadocJar)
            }
        }
    }
}
```

### Экспериментальный режим публикации метаданных

<!-- An experimental publishing and dependency consumption mode can be enabled by adding 
`enableFeaturePreview('GRADLE_METADATA')` to the `settings.gradle` file. With Gradle metadata enabled,
 an additional publication is added which references the target publications as its variants. The artifact ID of this publication
 matches the project name. -->
 
Экспериментальный режим публикации и зависимости может быть включен путем добавления `enableFeaturePreview('GRADLE_METADATA')` в файл `settings.gradle`. При включении метаданных Gradle добавляется дополнительная публикация, которая ссылается на целевые публикации в качестве ее вариантов. Идентификатор артефакта этой публикации соответствует имени проекта.
 
<!-- > Gradle metadata publishing is an experimental Gradle feature which is not guaranteed to be backward-compatible. 
Future Gradle versions may fail to resolve a dependency to a library published with current versions of Gradle metadata.
Library authors are recommended to use it to publish experimental versions of the library alongside with the stable publishing mechanism
until the feature is considered stable. -->

> Публикация метаданных Gradle - это экспериментальная функция Gradle, не гарантирующая обратную совместимость. Будущие версии Gradle могут не разрешить зависимость от библиотеки, опубликованной с текущими версиями метаданных Gradle. Авторы библиотек рекомендуют использовать его для публикации экспериментальных версий библиотеки наряду со стабильным механизмом публикации до тех пор, пока эта функция не будет считаться стабильной.
 
<!-- If a library is published with Gradle metadata enabled and a consumer enables the metadata as well, the consumer may specify a
 single dependency on the library in a common source set, and a corresponding platform-specific variant will be chosen, if available, 
 for each of the compilations. Consider a `sample-lib` library built for the JVM and JS and published with experimental Gradle metadata.
 Then it is enough for the consumers to add `enableFeaturePreview('GRADLE_METADATA)` and specify a single dependency: -->
 
Если библиотека опубликована с включенными метаданными Gradle, а потребитель также включает метаданные, то потребитель может указать одну зависимость от библиотеки в общем наборе исходных кодов, и будет выбран соответствующий вариант для конкретной платформы для каждой из компиляций. Рассмотрим библиотеку `sample-lib`, созданную для JVM и JS, и опубликованную с экспериментальными метаданными Gradle. Тогда для потребителей достаточно добавить `enableFeaturePreview('GRADLE_METADATA)` и указать одну зависимость:
 
```groovy
kotlin {
    targets {
        fromPreset(presets.jvm, 'jvm6')
        fromPreset(presets.js, 'nodeJs')
    }
    sourceSets {
        commonMain {
            dependencies {
                api 'com.example:sample-lib:1.0' 
                // is resolved to `sample-lib-jvm` for JVM, `sample-lib-js` for JS 
            }
        }
    }
}
```

### Неоднозначные цели

<!-- It is possible to have more than one target for a single platform in a multiplatform library. For example, these targets
may provide the same API and differ in the libraries they cooperate with at runtime, like testing frameworks or logging solutions. 
However, dependencies on such a multiplatform library may be ambiguous and may thus fail to resolve under certain conditions: -->

В многоплатформенной библиотеке возможно иметь более одной цели для одной платформы. Например, эти цели могут предоставлять один и тот же API и различать библиотеки, с которыми они взаимодействуют во время выполнения, например, тестирование фреймворков или логирование.

Однако зависимости от такой многоплатформенной библиотеки могут быть неоднозначными и, таким образом, не могут быть разрешены при определенных условиях:

<!-- * A `project('...')` dependency on a multiplatform library is used. Replace it with a `project(path: '...', configuration: '...')` dependency. 
Use the appropriate target's runtime elements configuration, such as `jvm6RuntimeElements`. Due to the current limitations, this dependency should   be placed in a top-level `dependencies { ... }` block rather than in a source set's dependencies. -->

* Используется зависимость `project('...')` от многоплатформенной библиотеки. Заменим его зависимостью `project(path: '...', configuration: '...')`. Используем соответствующую настройку элементов времени выполнения, например `jvm6RuntimeElements`. Из-за существующих ограничений эта зависимость должна быть помещена в блок верхнего уровня `dependencies { ... }`, а не в зависимости от набора исходных кодов.
  
<!-- * A published library dependency is used. If a library is published with experimental Gradle metadata, one can still replace the single dependency with unambiguous dependencies on its separate target modules, as if it had no experimental Gradle metadata. -->

* Используется опубликованная библиотечная зависимость. Если библиотека опубликована с экспериментальными метаданными Gradle, все равно можно заменить одну зависимость однозначными зависимостями на отдельные целевые модули, как если бы у нее отсутствовали экспериментальные метаданные Gradle.
  
<!-- * In both of the cases above, another solution is to mark the targets with a custom attribute. This, however, must be done on both the library author  and the consumer sides, and it's the library author's responsibility to communicate the attribute and its values to the consumers; -->

* В обоих вышеописанных случаях другое решение заключается в том, чтобы пометить целевые объекты настраиваемым атрибутом. Это, однако, должно быть сделано как со стороны автора библиотеки, так и для сторон потребителей. Ответственность автора библиотеки заключается в том, чтобы сообщать об этом атрибуту и его значениям потребителям;
 
<!-- Add the following symmetrically, to both the library and the consumer projects. The consumer may only need to mark 
a single target with the attribute: -->

Добавим следующий код и к библиотеке, и к потребительским проектам. Потребителю может потребоваться только отметить одну цель с атрибутом:

```groovy
def testFrameworkAttribute = Attribute.of('com.example.testFramework', String)
      
kotlin {
     targets {
         fromPreset(presets.jvm, 'junit') {
             attributes {
                 attribute(testingFrameworkAttribute, 'junit')
             }
         }
         fromPreset(presets.jvm, 'testng') {
             attributes {
                 attribute(testingFrameworkAttribute, 'testng')
             }
         }
     }
 }
 ```

## Использование целей Kotlin/Native

<!-- It is important to note that some of the [Kotlin/Native targets](#supported-platforms) may only be built with an appropriate host machine:
* Linux targets may only be built on a Linux host;
* Windows targets require a Windows host;
* macOS and iOS targets can only be built on a macOS host;
* Android Native targets require a Linux or macOS host; -->

Важно отметить, что некоторые из целей [Kotlin/Native](#поддерживаемые-платформы) могут быть созданы только с помощью соответствующей хост-машины:

* Цели Linux могут быть построены только на хосте Linux;
* Для целей Windows требуется хост Windows;
* Цели macOS и iOS могут быть созданы только на хосте macOS;
* Для мобильных целей Android требуется хост Linux или macOS;

<!-- A target that is not supported by the current host is ignored during build and therefore not published. A library author may want to set up builds and publishing from different hosts as required by the library target platforms. -->

Цель,не поддерживаемая текущим хостом, игнорируется во время сборки и поэтому не публикуется. Автор библиотеки может захотеть настроить сборку и публикацию с разных хостов в соответствии с требованиями целевых платформ библиотеки.

### Виды выходных данных Kotlin/Native

<!-- By default, a Kotlin/Native target is compiled down to a `*.klib` library artifact, which can be consumed by Kotlin/Native itself as a dependency but cannot be run or used as a native library.
To link a binary in addition to the Kotlin/Native library, add one or more of the `outputKinds`, which can be:
* `executable` for an executable program;
* `dynamic` for a dynamic library;
* `static` for a static library;
* `framework` for an Objective-C framework (only supported for macOS and iOS targets) -->

По умолчанию цель Kotlin/Native скомпилирована до артефакта библиотеки `*.klib`, который может быть использован самим Kotlin/Native в качестве зависимости, но не может быть запущен или использован в качестве родной библиотеки.

Для того, чтобы привязать бинарный файл как дополнение к библиотеке Kotlin/Native, добавим один или несколько `outputKinds`, которые могут быть:

* `executable` для исполняемой программы;
* `dynamic` для динамической библиотеки;
* `static` для статической библиотеки;
* `framework` для фреймворка Objective-C (поддерживается только для macOS и iOS)

<!-- This can be done as follows: -->

Это можно сделать следующим образом:

```groovy
kotlin {
    targets {
        fromPreset(presets.linuxX64, 'linux') {
            compilations.main.outputKinds 'executable' // could be 'static', 'dynamic'
        }
    }
}
```

<!-- This creates additional link tasks for the debug and release binaries. The tasks can be accessed after project evaluation from the compilation as, for example, 
`getLinkTask('executable', 'release')` or `getLinkTaskName('static', 'debug')`. To get the binary file, use `getBinary`, for example, 
 as `getBinary('executable', 'release')` or `getBinary('static', 'debug')`. -->
 
Это создает дополнительные связанные задачи для бинарных файлов отладки и релиза. Доступ к задачам можно получить после оценки проекта из компиляции, например, `getLinkTask('executable', 'release')` или `getLinkTaskName('static', 'debug')`. Для получения бинарного файла используем `getBinary`, например, `getBinary('executable', 'release')` или `getBinary('static', 'debug')`.

### Поддержка CInterop

<!-- Since Kotlin/Native provides [interoperability with native languages](/docs/reference/native/c_interop.html),
there is a DSL allowing one to configure this feature for a specific compilation.
A compilation can interact with several native libraries. Interoperability with each of them can be configured in
the `cinterops` block of the compilation: -->

Поскольку Kotlin/Native обеспечивает [совместимость с родными языками](/docs/reference/native/c_interop.html), существует DSL, позволяющий настроить эту функцию для конкретной компиляции.

Компиляция может взаимодействовать с несколькими родными библиотеками. Взаимодействие с каждым из них можно настроить в блоке компиляции `cinterops`:

```groovy
// In the scope of a Kotlin/Native target's compilation:
cinterops {
    myInterop {
        // Def-file describing the native API.
        // The default path is src/nativeInterop/cinterop/<interop-name>.def
        defFile project.file("def-file.def")

        // Package to place the Kotlin API generated.
        packageName 'org.sample'

        // Options to be passed to compiler by cinterop tool.
        compilerOpts '-Ipath/to/headers'

        // Directories to look for headers.
        includeDirs {
            // Directories for header search (an analogue of the -I<path> compiler option).
            allHeaders 'path1', 'path2'

            // Additional directories to search headers listed in the 'headerFilter' def-file option.
            // -headerFilterAdditionalSearchPrefix command line option analogue.
            headerFilterOnly 'path1', 'path2'
        }
        // A shortcut for includeDirs.allHeaders.
        includeDirs "include/directory", "another/directory"
    }

    anotherInterop { /* ... */ }
}
```

<!-- Often it's necessary to specify target-specific linker options for a binary which uses a native library. It can be
done using the `linkerOpts` DSL method of a Kotlin/Native compilation: -->

Часто для бинарного файла, использующего собственную библиотеку, необходимо указать параметры компоновщика для конкретной платформы. Это можно сделать с использованием метода DSL `linkerOpts` компиляции Kotlin/Native:

```groovy
compilations.main {
    linkerOpts '-L/lib/search/path -L/another/search/path -lmylib'
}
```
