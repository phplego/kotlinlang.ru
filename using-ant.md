---
type: doc
layout: reference
title: "Использование Ant"
description: В этом руководстве рассматриваются различные сценарии при использовании Ant для создания приложений, содержащих исходный код Kotlin
---
<!-- description: "This tutorial walks you through different scenarios when using Ant for building applications that contain Kotlin code" -->
# Использование Ant

## Получение задач Ant

<!-- Kotlin provides three tasks for Ant: -->
Kotlin предлагает три задачи для Ant:

<!-- * kotlinc: Kotlin compiler targeting the JVM; -->
* kotlinc: компилятор Kotlin для работы с JVM;

<!-- * kotlin2js: Kotlin compiler targeting JavaScript; -->
* kotlin2js: компилятор Kotlin для работы с JavaScript;

<!-- * withKotlin: Task to compile Kotlin files when using the standard *javac* Ant task. -->
* withKotlin: компиляция файлов Kotlin при использовании стандартной задачи javac Ant.

<!-- These tasks are defined in the *kotlin-ant.jar* library which is located in the *lib* folder for the [Kotlin Compiler]({{site.data.releases.latest.url}})
Ant version 1.8.2+ is required. -->
Эти задачи определены в библиотеке kotlin-ant.jar, которая находится в папке lib для [компилятора Kotlin]({{site.data.releases.latest.url}}) Ant версии 1.8.2+.

<!-- ## Targeting JVM with Kotlin-only source -->
## Использование в JVM только с исходным кодом Kotlin

<!-- When the project consists of exclusively Kotlin source code, the easiest way to compile the project is to use the *kotlinc* task: -->
Когда проект состоит исключительно из исходного кода Kotlin, самый простой способ скомпилировать проект - использовать задачу *kotlinc* :
``` xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <kotlinc src="hello.kt" output="hello.jar"/>
    </target>
</project>
```

<!-- where `${kotlin.lib}` points to the folder where the Kotlin standalone compiler was unzipped. -->
Где `${kotlin.lib}` указывает на папку, в которую был распакован автономный компилятор Kotlin.

<!-- ## Targeting JVM with Kotlin-only source and multiple roots -->
## Использование в JVM с исходным кодом Kotlin и несколькими корневыми директориями

<!-- If a project consists of multiple source roots, use *src* as elements to define paths: -->
Если проект состоит из нескольких корневых директорий с исходным кодом, используем *src* как элементы для определения путей:

``` xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <kotlinc output="hello.jar">
            <src path="root1"/>
            <src path="root2"/>
        </kotlinc>
    </target>
</project>
```

<!-- ## Targeting JVM with Kotlin and Java source -->
## Использование в JVM с исходным кодом Kotlin и Java

<!-- If a project consists of both Kotlin and Java source code, while it is possible to use *kotlinc*, to avoid repetition of task parameters, it is
recommended to use *withKotlin* task: -->
Если проект состоит из исходного кода Kotlin и Java, то для избежания повторения параметров задачи рекомендуется 
использовать задачу *withKotlin* везде, где возможно использовать *kotlinc*:

``` xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <delete dir="classes" failonerror="false"/>
        <mkdir dir="classes"/>
        <javac destdir="classes" includeAntRuntime="false" srcdir="src">
            <withKotlin/>
        </javac>
        <jar destfile="hello.jar">
            <fileset dir="classes"/>
        </jar>
    </target>
</project>
```

<!-- You can also specify the name of the module being compiled as the `moduleName` attribute: -->
Также можно указать имя модуля, скомпилированного как атрибут *moduleName*:

``` xml
<withKotlin moduleName="myModule"/>
```

<!-- ## Targeting JavaScript with single source folder -->
## Использование в JavaScript с с одной исходной папкой

``` xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <kotlin2js src="root1" output="out.js"/>
    </target>
</project>
```

<!-- ## Targeting JavaScript with Prefix, PostFix and sourcemap options -->
## Использование в JavaScript с Prefix, PostFix и исходными параметрами

``` xml
<project name="Ant Task Test" default="build">
    <taskdef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <kotlin2js src="root1" output="out.js" outputPrefix="prefix" outputPostfix="postfix" sourcemap="true"/>
    </target>
</project>
```

<!-- ## Targeting JavaScript with single source folder and metaInfo option -->
## Использование в JavaScript с помощью папки с одним исходным кодом и метаинформацией


<!-- The `metaInfo` option is useful, if you want to distribute the result of translation as a Kotlin/JavaScript library.
If `metaInfo` was set to `true`, then during compilation additional JS file with
binary metadata will be created. This file should be distributed together with the
result of translation: -->

Параметр *metaInfo* окажется полезен, если требуется распространять результат перевода как
библиотеку Kotlin / JavaScript. Если в *metaInfo* было установлено значение true, то во время
компиляции будет создан дополнительный JS-файл с бинарными метаданными. Этот файл должен быть
распространен вместе с результатом перевода:

``` xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <!-- out.meta.js will be created, which contains binary metadata -->
        <kotlin2js src="root1" output="out.js" metaInfo="true"/>
    </target>
</project>
```

<!-- ## References -->
## Рекомендации

<!-- Complete list of elements and attributes are listed below: -->
Полный список элементов и атрибутов приведен ниже:

<!-- ### Attributes common for kotlinc and kotlin2js -->
### Общие для kotlinc и kotlin2js атрибуты

| Название | Описание | Необходимо | Значение по умолчанию |
|------|-------------|----------|---------------|
| `src`  | Исходный файл или каталог Kotlin для компиляции | Да |  |
| `nowarn` | Подавляет все предупреждения на этапе компиляции | Нет | false |
| `noStdlib` | Не включает стандартную библиотеку Kotlin в путь к классам | Нет | false |
| `failOnError` | Не дает завершить сборку, если во время компиляции обнаружены ошибки | Нет | true |

### Атрибуты kotlinc

| Название | Описание | Необходимо | Значение по умолчанию |
|------|-------------|----------|---------------|
| `output`  | Каталог назначения или имя jar-файла | Да |  |
| `classpath`  | Путь класса компиляции | Нет |  |
| `classpathref`  | Ссылка на путь класса компиляции | Нет |  |
| `includeRuntime`  | Если `output` является jar-файлом, то библиотека времени выполнения Kotlin будет включена в jar | Нет | true |
| `moduleName` | Название компилируемого модуля | Нет | Имя цели (если указано) или проекта |

### Атрибуты kotlin2js

| Название | Описание | Необходимо |
|------|-------------|----------|
| `output`  | 	Файл назначения | Да |
| `libraries`  | Пути к библиотекам Kotlin | Нет |
| `outputPrefix`  | Префикс для использования сгенерированных файлов JavaScript | Нет |
| `outputSuffix` | Суффикс для использования сгенерированных файлов JavaScript | Нет |
| `sourcemap`  | Должен ли быть создан файл sourcemap| Нет |
| `metaInfo`  | Должен быть создан файл метаданных с бинарными дескрипторами | Нет |
| `main`  | Должен ли сгенерированный компилятором код вызвать основную функцию | Нет |

### Передача исходных аргументов компилятора

<!-- To pass custom raw compiler arguments, you can use `<compilerarg>` elements with either `value` or `line` attributes.
This can be done within the `<kotlinc>`, `<kotlin2js>`, and `<withKotlin>` task elements, as follows: -->
Для выборочной передачи исходных аргументов компилятора используются элементы `<compilerarg>` с атрибутами `value` или `line`.
Это может быть сделано в рамках элементов задач `<kotlinc>`, `<kotlin2js>` и `<withKotlin>` следующим образом :

``` xml
<kotlinc src="${test.data}/hello.kt" output="${temp}/hello.jar">
    <compilerarg value="-Xno-inline"/>
    <compilerarg line="-Xno-call-assertions -Xno-param-assertions"/>
    <compilerarg value="-Xno-optimize"/>
</kotlinc>
```

<!-- The full list of arguments that can be used is shown when you run `kotlinc -help`. -->
Полный список используемых аргументов можно увидеть при запуске `kotlinc -help`.
