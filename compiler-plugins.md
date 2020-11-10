---
type: doc
layout: reference
title: "Плагины для компилятора"
---

# Плагины для компилятора

## All-open плагин для компилятора

Классы и методы в языке Kotlin имеют модификатор `final` по умолчанию, что приносит массу неудобств при использованиие фреймворков и библиотек, таких как Spring AOP, которые требуют классы с модификатором `open`. Плагин для компилятора *all-open* адаптирует Kotlin под требования фреймворков и добавляет классам специальную аннотацию, благодаря чему их члены открты для использования без приминения модификатора `open`.

Например, при использовании Spring, вам нужно чтобы открытыми были не все классы, а только те, которые имеют специальные аннотации, такие как `@Configuration` или `@Service`. *All-open* позволяет указывать эти аннотации.

Поддержка  *Аll-open* обеспечена для сборщиков Gradle и Maven в тесной интеграции с IDE. 

:point_up: Для Spring вы также можете использовать плагин `kotlin-spring`.

### Использование с Gradle

Добавьте плагин в зависимости buildscript и разрешите его использование:

``` groovy
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-allopen:$kotlin_version"
    }
}

apply plugin: "kotlin-allopen"
```

В качестве альтернативы вы можете разрешить использование в блоке `plugins`:

```groovy
plugins {
  id "org.jetbrains.kotlin.plugin.allopen" version "{{ site.data.releases.latest.version }}"
}
```

Затем укажите список аннтоаций, для которых нужно открыть классы:

```groovy
allOpen {
    annotation("com.my.Annotation")
    // annotations("com.another.Annotation", "com.third.Annotation")
}
```

Если класс (или любой из его суперклассов) имеет аннотацию `com.my.Annotation`, то сам класс и все его члены будут открытыми.

Плагин также работает с мета-аннотациями:

```kotlin
@com.my.Annotation
annotation class MyFrameworkAnnotation

@MyFrameworkAnnotation
class MyClass // will be all-open
```

`MyFrameworkAnnotation` имеет all-open мета-аннотацию `com.my.Annotation`, поэтому он становится открытым классом.

### Использование с Maven

Ниже вы можете увидеть использование all-open вместе с Maven:

``` xml
<plugin>
    <artifactId>kotlin-maven-plugin</artifactId>
    <groupId>org.jetbrains.kotlin</groupId>
    <version>${kotlin.version}</version>

    <configuration>
        <compilerPlugins>
            <!-- Или укажите "spring" для поддержки Spring -->
            <plugin>all-open</plugin>
        </compilerPlugins>

        <pluginOptions>
            <!-- Каждая аннотация размещена на отдельной строке -->
            <option>all-open:annotation=com.my.Annotation</option>
            <option>all-open:annotation=com.their.AnotherAnnotation</option>
        </pluginOptions>
    </configuration>

    <dependencies>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-maven-allopen</artifactId>
            <version>${kotlin.version}</version>
        </dependency>
    </dependencies>
</plugin>
```

Более подробную информацию о том, как работают all-open аннотации читайте в разделе "Использование с Gradle".

### Поддержка Spring

Если вы используете Spring, то вы можете включить плагин *kotlin-spring* вместо того, чтобы указывать аннотации Spring вручную. Плагин *kotlin-spring* - это обертка над all-open, которая работает точно также.

Как и при использовании all-open, добавьте плагин в зависимости buildscript:

``` groovy
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-allopen:$kotlin_version"
    }
}

apply plugin: "kotlin-spring" // instead of "kotlin-allopen"
```

Или используйте DSL для плагинов Gradle:

```groovy
plugins {
  id "org.jetbrains.kotlin.plugin.spring" version "{{ site.data.releases.latest.version }}"
}
```

В Maven, разрешите использование плагина `spring`:

```xml
<compilerPlugins>
    <plugin>spring</plugin>
</compilerPlugins>
```

Аннотации, поддерживаемые плагином: 
[`@Component`](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Component.html), [`@Async`](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/annotation/Async.html), [`@Transactional`](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Transactional.html), [`@Cacheable`](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/Cacheable.html) and [`@SpringBootTest`](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html). Thanks to meta-annotations support classes annotated with [`@Configuration`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html), [`@Controller`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Controller.html), [`@RestController`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html), [`@Service`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/stereotype/Service.html) or [`@Repository`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Repository.html) are automatically opened since these annotations are meta-annotated with [`@Component`](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Component.html).
 
Вы можете использовать `kotlin-allopen` и `kotlin-spring` в одном проекте.

Обратите внимание, если вы сгенерировали проект при помощи сервиса [start.spring.io](http://start.spring.io/#!language=kotlin), то плагин `kotlin-spring` будет включен по умолчанию.

### Использование в CLI

JAR-файл с плагином all-open доступен в бинарном дистрибутиве компилятора Kotlin. Вы можете подключить плагин, указав путь к его JAR-файлу с помощью опции "Xplugin" в kotlinc:

```bash
-Xplugin=$KOTLIN_HOME/lib/allopen-compiler-plugin.jar
```

Вы можете напрямую указывать все аннотации, для которых нужны открытые классы, с помощью опции `annotation`, или с помощью включения "preset". Единственный пресет, доступный в данный момент - `spring`.

```bash
# The plugin option format is: "-P plugin:<plugin id>:<key>=<value>". 
# Options can be repeated.

-P plugin:org.jetbrains.kotlin.allopen:annotation=com.my.Annotation
-P plugin:org.jetbrains.kotlin.allopen:preset=spring
```

## No-arg плагин для компилятора

Плагин *no-arg* создает конструктор без параметров для классов, помеченных специальной аннотацией. 

Сгенерированный конструктор создается искусственно, поэтому его нельзя вызвать напрямую из исходного кода Kotlin или Java, но он может быть вызван с помощью рефлексии.

Использование этого плагина позволяет Java Persistence API (JPA) создавать экземпляр класса `data`, при этом не предоставляя возможности вызова конструктора без параметров из Kotlin или Java.
(Смотрите описание плагина `kotlin-jpa` ниже).

### Использование с Gradle

Использование очень похоже на применение all-open.

Добавьте плагин и укажите аннотации, при виде которых плагин no-arg должен создать для класса конструкторы без параметров.
 
```groovy
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-noarg:$kotlin_version"
    }
}

apply plugin: "kotlin-noarg"
```

Или используйте DSL для плагинов Gradle:

```groovy
plugins {
  id "org.jetbrains.kotlin.plugin.noarg" version "{{ site.data.releases.latest.version }}"
}
```

Затем укажите список аннотаций для no-arg:

```groovy
noArg {
    annotation("com.my.Annotation")
}
```

Включите опцию `invokeInitializers`, если вы хотите, чтобы плагин выполнял инициализацию искусственного конструктора. Начиная с Kotlin 1.1.3-2, опция отключена по умолчанию в связи с ошибками [`KT-18667`](https://youtrack.jetbrains.com/issue/KT-18667) и [` KT-18668`](https://youtrack.jetbrains.com/issue/KT-18668), которые будут рассмотрены в будущем.

```groovy
noArg {
    invokeInitializers = true
}
```

### Использование в Maven

``` xml
<plugin>
    <artifactId>kotlin-maven-plugin</artifactId>
    <groupId>org.jetbrains.kotlin</groupId>
    <version>${kotlin.version}</version>

    <configuration>
        <compilerPlugins>
            <!-- Или используйте "jpa" для поддержки JPA -->
            <plugin>no-arg</plugin>
        </compilerPlugins>

        <pluginOptions>
            <option>no-arg:annotation=com.my.Annotation</option>
            <!-- Разрешить вызов искусственных инициализаторов -->
            <!-- <option>no-arg:invokeInitializers=true</option> -->
        </pluginOptions>
    </configuration>

    <dependencies>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-maven-noarg</artifactId>
            <version>${kotlin.version}</version>
        </dependency>
    </dependencies>
</plugin>
```

### Поддержка JPA

Также как и в случае с плагином *kotlin-spring*, *kotlin-jpa* - это обертка над плагином *no-arg*. Плагин поддерживает 
[`@Entity`](http://docs.oracle.com/javaee/7/api/javax/persistence/Entity.html), [`@Embeddable`](http://docs.oracle.com/javaee/7/api/javax/persistence/Embeddable.html) and [`@MappedSuperclass`](https://docs.oracle.com/javaee/7/api/javax/persistence/MappedSuperclass.html) 
*no-arg* аннотации по умолчанию.

Добавьте плагин в Gradle следующим способом: 

``` groovy
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-noarg:$kotlin_version"
    }
}

apply plugin: "kotlin-jpa"
```

Или используйте DSL для плагинов Gradle:

```groovy
plugins {
  id "org.jetbrains.kotlin.plugin.jpa" version "{{ site.data.releases.latest.version }}"
}
```

В Maven, включите плагин `jpa`:

```xml
<compilerPlugins>
    <plugin>jpa</plugin>
</compilerPlugins>
```

### Использование в CLI

Также как и в случае с all-open, добавьте JAR-файл плагина в classpath плагина компилятора и укажите нужные аннотации или пресеты:

```bash
-Xplugin=$KOTLIN_HOME/lib/noarg-compiler-plugin.jar
-P plugin:org.jetbrains.kotlin.noarg:annotation=com.my.Annotation
-P plugin:org.jetbrains.kotlin.noarg:preset=jpa
```
