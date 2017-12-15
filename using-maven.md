---
type: doc
layout: reference
title: "Использование Maven"
description: "В этом руководстве рассказывается о различных сценариях при использовании Maven для создания приложений, содержащих код Kotlin"
---

# Использование Maven

## Плагины и версии

*kotlin-maven-plugin* компилирует исходные коды и модули на языке Kotlin. В настоящее время поддерживается только Maven версии 3.

Для определения используемой версии Kotlin используйте свойство *kotlin.version*:

``` xml
<properties>
    <kotlin.version>{{ site.data.releases.latest.version }}</kotlin.version>
</properties>
```

## Зависимости

Kotlin имеет обширную стандартную библиотеку, которая может использоваться при разработке приложений. Настройте следущие зависимости в файле pom: 

``` xml
<dependencies>
    <dependency>
        <groupId>org.jetbrains.kotlin</groupId>
        <artifactId>kotlin-stdlib</artifactId>
        <version>${kotlin.version}</version>
    </dependency>
</dependencies>
```

Если вы используете JDK 7 или JDK 8, вы можете использовать расширенные версии стандартной библиотеки Kotlin, содержащие
дополнительные функции расширения API, добавленные в новые версии JDK. Вместо `kotlin-stdlib`, используйте` kotlin-stdlib-jdk7`
или `kotlin-stdlib-jdk8`, в зависимости от вашей версии JDK (для Kotlin 1.1.x,` kotlin-stdlib-jre7` и `kotlin-stdlib-jre8`).

Если ваш проект использует [Kotlin reflection](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect.full/index.html) или объекты для тестирования, то вам нужно добавить соответствующие зависимости.
Artifact ID для Kotlin refletcion - `kotlin-reflect`, `kotlin-test` и `kotlin-test-junit` для библиотек тестирования.

## Компиляция только исходного кода Kotlin

Для компиляции исходного кода на Kotlin укажите компилируемые каталоги в теге <build>:

``` xml
<build>
    <sourceDirectory>${project.basedir}/src/main/kotlin</sourceDirectory>
    <testSourceDirectory>${project.basedir}/src/test/kotlin</testSourceDirectory>
</build>
```

Для компиляции исходного кода необходимо ссылаться на Kotlin Maven Plugin:

``` xml
<build>
    <plugins>
        <plugin>
            <artifactId>kotlin-maven-plugin</artifactId>
            <groupId>org.jetbrains.kotlin</groupId>
            <version>${kotlin.version}</version>

            <executions>
                <execution>
                    <id>compile</id>
                    <goals> <goal>compile</goal> </goals>
                </execution>

                <execution>
                    <id>test-compile</id>
                    <goals> <goal>test-compile</goal> </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

## Компиляция исходных кодов на Kotlin и Java

Для компиляции исходных кодов на Kotlin и Java, компилятор языка Kotlin должен быть использован перед компилятором языка Java.
Согласно правилам разметки Maven, kotlin-maven-plugin должен запускаться до maven-compiler-plugin. Для этого расположите kotlin plugin до maven-compiler-plugin в файле pom.xml:

``` xml
<build>
    <plugins>
        <plugin>
            <artifactId>kotlin-maven-plugin</artifactId>
            <groupId>org.jetbrains.kotlin</groupId>
            <version>${kotlin.version}</version>
            <executions>
                <execution>
                    <id>compile</id>
                    <goals> <goal>compile</goal> </goals>
                    <configuration>
                        <sourceDirs>
                            <sourceDir>${project.basedir}/src/main/kotlin</sourceDir>
                            <sourceDir>${project.basedir}/src/main/java</sourceDir>
                        </sourceDirs>
                    </configuration>
                </execution>
                <execution>
                    <id>test-compile</id>
                    <goals> <goal>test-compile</goal> </goals>
                    <configuration>
                        <sourceDirs>
                            <sourceDir>${project.basedir}/src/test/kotlin</sourceDir>
                            <sourceDir>${project.basedir}/src/test/java</sourceDir>
                        </sourceDirs>
                    </configuration>
                </execution>
            </executions>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.5.1</version>
            <executions>
                <!-- Замена компиляции по умолчанию -->
                <execution>
                    <id>default-compile</id>
                    <phase>none</phase>
                </execution>
                <!-- Замена default-testCompile по умолчанию -->
                <execution>
                    <id>default-testCompile</id>
                    <phase>none</phase>
                </execution>
                <execution>
                    <id>java-compile</id>
                    <phase>compile</phase>
                    <goals> <goal>compile</goal> </goals>
                </execution>
                <execution>
                    <id>java-test-compile</id>
                    <phase>test-compile</phase>
                    <goals> <goal>testCompile</goal> </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

## Инкрементальная компиляция

Чтобы ускорить сборку, вы можете использовать инкрементальную компиляцию сборщика Maven (поддерживается начиная с Kotlin 1.1.2).
Для этого добавите свойсто `kotlin.compiler.incremental`:

``` xml
<properties>
    <kotlin.compiler.incremental>true</kotlin.compiler.incremental>
</properties>
```

В качестве альтернативы, вы можете запустить сборку с опцией `-Dkotlin.compiler.incremental=true`.

## Обработка аннотаций

См. Описание [Инструмент обработки аннотации Kotlin] (kapt.html) (`kapt`).

## Поддержка корутинов

[Корутины](coroutines.html) экспериментально поддерживаются в Kotlin 1.2, поэтому компилятор Kotlin выдает предупреждения, когда находит использование корутинов в вашем проекте.
Чтобы отключить предупреждения, добавьте следующий тег в ваш файл `pom.xml`:

``` xml
<configuration>
    <experimentalCoroutines>enable</experimentalCoroutines>
</configuration>
```

## Jar файлы

To create a small Jar file containing just the code from your module, include the following under `build->plugins` in your Maven pom.xml file,
where `main.class` is defined as a property and points to the main Kotlin or Java class:

Для создания небольшого Jar файла, содержащего код вашего модуля, необходимо включить в раздел `build->plugins` файла pom.xml следующий код,
где свойство mainClass указывает на основной класс Kotlin или Java:

``` xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>2.6</version>
    <configuration>
        <archive>
            <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>${main.class}</mainClass>
            </manifest>
        </archive>
    </configuration>
</plugin>
```

## Самостоятельный Jar файл

Для создания самостоятельного Jar файла, содержащего код вашего модуля вместе с его зависимостями, необходимо включить в раздел `build->plugins` файла pom.xml следующий код,
где свойство mainClass указывает на основной класс Kotlin или Java:

``` xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>2.6</version>
    <executions>
        <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals> <goal>single</goal> </goals>
            <configuration>
                <archive>
                    <manifest>
                        <mainClass>${main.class}</mainClass>
                    </manifest>
                </archive>
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
            </configuration>
        </execution>
    </executions>
</plugin>
```

Самостоятельный Jar файл может быть передан JRE для запуска вашего приложения:

``` bash
java -jar target/mymodule-0.0.1-SNAPSHOT-jar-with-dependencies.jar
```

## Сборка JavaScript

Для компиляции JavaScript кода, вам необходимо использовать команды `js` и `test-js` для выполнения задачи `compile`:

``` xml
<plugin>
    <groupId>org.jetbrains.kotlin</groupId>
    <artifactId>kotlin-maven-plugin</artifactId>
    <version>${kotlin.version}</version>
    <executions>
        <execution>
            <id>compile</id>
            <phase>compile</phase>
            <goals>
                <goal>js</goal>
            </goals>
        </execution>
        <execution>
            <id>test-compile</id>
            <phase>test-compile</phase>
            <goals>
                <goal>test-js</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

Также необходимо изменить используемую стандартную библиотеку:

``` xml
<groupId>org.jetbrains.kotlin</groupId>
<artifactId>kotlin-stdlib-js</artifactId>
<version>${kotlin.version}</version>
```

Для поддержки модульного тестирования, вам необходимо добавить зависимость `kotlin-test-js`.

Для получения более подробной информации просмотрите урок [Getting Started with Kotlin and JavaScript with Maven](https://kotlinlang.org/docs/tutorials/javascript/getting-started-maven/getting-started-with-maven.html)

## Установка параметров компилятора

Дополнительные параметры и аргументы для компилятора могут быть указаны в теге `<configuration>` в разделе '<plugin>':

``` xml
<plugin>
    <artifactId>kotlin-maven-plugin</artifactId>
    <groupId>org.jetbrains.kotlin</groupId>
    <version>${kotlin.version}</version>
    <executions>...</executions>
    <configuration>
        <nowarn>true</nowarn>  <!-- Отключение предупреждений -->
        <args>
            <arg>-Xjsr305=strict</arg> <!-- Включение строго режима для аннотаций JSR-305-->
            ...
        </args>
    </configuration>
</plugin>
```

Многие параметры могут быть настроены через свойства:

``` xml
<project ...>
    <properties>
        <kotlin.compiler.languageVersion>1.0</kotlin.compiler.languageVersion>
    </properties>
</project>
```

В настоящий момент поддерживаются следующие атрибуты:

### Общие атрибуты для JVM и JS

| Имя | Название свойства | Описание | Доступные значения | Значение по умолчанию |
|------|---------------|-------------|-----------------|--------------|
| nowarn | | Generate no warnings | true, false | false |
| languageVersion | kotlin.compiler.languageVersion | Provide source compatibility with specified language version | "1.0", "1.1" | "1.1"
| apiVersion | kotlin.compiler.apiVersion | Allow to use declarations only from the specified version of bundled libraries | "1.0", "1.1" | "1.1"
| sourceDirs | | The directories containing the source files to compile | | The project source roots
| compilerPlugins | | Enabled [compiler plugins](compiler-plugins.html)  | | []
| pluginOptions | | Options for compiler plugins  | | []
| args | | Additional compiler arguments | | []


### Специальные аттрибуты для JVM

| Имя | Название свойства | Описание | Доступные значения | Значение по умолчанию |
|------|---------------|-------------|-----------------|--------------|
| jvmTarget | kotlin.compiler.jvmTarget | Target version of the generated JVM bytecode | "1.6", "1.8" | "1.6" |
| jdkHome | kotlin.compiler.jdkHome |  	Path to JDK home directory to include into classpath, if differs from default JAVA_HOME | | |

### Специальные аттрибуты для JS

| Имя | Название свойства | Описание | Доступные значения | Значение по умолчанию |
|------|---------------|-------------|-----------------|--------------|
| outputFile | | Output file path | | |
| metaInfo |  | Generate .meta.js and .kjsm files with metadata. Use to create a library | true, false | true
| sourceMap | | Generate source map | true, false | false
| sourceMapEmbedSources | | Embed source files into source map | "never", "always", "inlining" | "inlining" |
| sourceMapPrefix | | Prefix for paths in a source map |  |  |
| moduleKind | | Kind of a module generated by compiler | "plain", "amd", "commonjs", "umd" | "plain"

## Генерация документации

The standard JavaDoc generation plugin (`maven-javadoc-plugin`) does not support Kotlin code.
To generate documentation for Kotlin projects, use [Dokka](https://github.com/Kotlin/dokka);
please refer to the [Dokka README](https://github.com/Kotlin/dokka/blob/master/README.md#using-the-maven-plugin)
for configuration instructions. Dokka supports mixed-language projects and can generate output in multiple
formats, including standard JavaDoc.

Плагин (`maven-javadoc-plugin`) для генерации документации согласно стандарту JavaDoc не поддерживает Kotlin.
Для создания документации в проектах на Kotlin используйте [Dokka](https://github.com/Kotlin/dokka);
обращайтесь к [Dokka README](https://github.com/Kotlin/dokka/blob/master/README.md#using-the-maven-plugin)
для получения инструкций по настройке. Dokka поддерживает проекты, состоящие из различных языков, 
и может создавать документацию в различных форматах, включая стандарт JavaDoc.

## OSGi

Для поддержки OSGi смотрите [Kotlin OSGi](https://kotlinlang.org/docs/reference/kotlin-osgi.html).

## Примеры

Пример проекта Maven может быть [загружен из репозитория на GitHub](https://github.com/JetBrains/kotlin-examples/archive/master/maven.zip)
