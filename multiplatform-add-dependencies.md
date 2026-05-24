---
type: doc
layout: reference
title: "Добавление зависимостей на мультиплатформенные библиотеки"
description: "Добавление зависимостей на мультиплатформенные библиотеки"
url: https://kotlinlang.ru/docs/multiplatform-add-dependencies.html
---

<!-- При переводе статьи оригинальная версия была от 13 January 2026 -->

# Добавление зависимостей на мультиплатформенные библиотеки

Любой программе для успешной работы нужен набор библиотек. Проект Kotlin Multiplatform может зависеть от
мультиплатформенных библиотек, которые работают на всех целевых платформах, от платформенно-специфичных библиотек и от
других мультиплатформенных проектов.

Чтобы добавить зависимость на библиотеку, обновите файл `build.gradle(.kts)` в каталоге проекта, где находится общий
код. Добавьте зависимость нужного типа, например `implementation`, в блок `dependencies {}`:

```kotlin
kotlin {
    //...
    sourceSets {
        commonMain.dependencies {
            implementation("com.example:my-library:1.0") // библиотека общая для всех source set'ов
        }
    }
}
```

```groovy
kotlin {
    //...
    sourceSets {
        commonMain {
            dependencies {
                implementation 'com.example:my-library:1.0'
            }
        }
    }
}
```

## Зависимость на библиотеку Kotlin

### Стандартная библиотека

Зависимость на стандартную библиотеку (`stdlib`) добавляется в каждый source set автоматически. Версия стандартной
библиотеки совпадает с версией плагина `kotlin-multiplatform`.

Для платформенно-специфичных source set'ов используется соответствующий платформенный вариант библиотеки, а для
остальных добавляется общая стандартная библиотека. Kotlin Gradle plugin выбирает подходящую стандартную библиотеку JVM
в зависимости от compiler option `compilerOptions.jvmTarget` в Gradle-скрипте сборки.

Подробнее о том, как изменить поведение по умолчанию, см. в документации по
[иерархической структуре проекта](https://kotlinlang.org/docs/multiplatform/multiplatform-dsl-reference.html#hierarchical-project-structure).

### Библиотеки для тестов

Для мультиплатформенных тестов доступен API [`kotlin.test`](https://kotlinlang.org/api/core/kotlin-test/). При создании
мультиплатформенного проекта можно добавить тестовые зависимости во все source set'ы одной зависимостью в `commonTest`:

```kotlin
kotlin {
    //...
    sourceSets {
        commonTest.dependencies {
            implementation(kotlin("test")) // автоматически подключает платформенные зависимости
        }
    }
}
```

```groovy
kotlin {
    //...
    sourceSets {
        commonTest {
            dependencies {
                implementation kotlin("test") // автоматически подключает платформенные зависимости
            }
        }
    }
}
```

### Библиотеки kotlinx

Если вы используете мультиплатформенную библиотеку и хотите зависеть от общего кода, укажите зависимость только один раз
в общем source set'е. Используйте базовое имя артефакта библиотеки, например `kotlinx-coroutines-core`:

```kotlin
kotlin {
    //...
    sourceSets {
        commonMain.dependencies {
            implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.10.2")
        }
    }
}
```

```groovy
kotlin {
    //...
    sourceSets {
        commonMain {
            dependencies {
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.10.2'
            }
        }
    }
}
```

Если библиотека kotlinx нужна как платформенно-специфичная зависимость, в соответствующем платформенном source set'е
по-прежнему можно использовать базовое имя артефакта:

```kotlin
kotlin {
    //...
    sourceSets {
        jvmMain.dependencies {
            implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.10.2")
        }
    }
}
```

```groovy
kotlin {
    //...
    sourceSets {
        jvmMain {
            dependencies {
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.10.2'
            }
        }
    }
}
```

## Зависимость на библиотеки Kotlin Multiplatform

Вы можете добавлять зависимости на библиотеки, которые используют технологию Kotlin Multiplatform, например
[SQLDelight](https://github.com/sqldelight/sqldelight). Авторы таких библиотек обычно публикуют собственные инструкции
по подключению.

Искать мультиплатформенные библиотеки можно на поисковой платформе JetBrains [klibs.io](https://klibs.io/).

### Библиотека, общая для всех source set'ов

Если библиотека нужна во всех source set'ах, добавьте её только в общий source set. Kotlin Multiplatform Gradle plugin
автоматически добавит соответствующие части этой библиотеки в остальные source set'ы.

В общий source set нельзя добавлять зависимости на платформенно-специфичные библиотеки.

```kotlin
kotlin {
    //...
    sourceSets {
        commonMain.dependencies {
            implementation("io.ktor:ktor-client-core:3.4.1")
        }
        androidMain.dependencies {
            // зависимость на платформенную часть ktor-client будет добавлена автоматически
        }
    }
}
```

```groovy
kotlin {
    //...
    sourceSets {
        commonMain {
            dependencies {
                implementation 'io.ktor:ktor-client-core:3.4.1'
            }
        }
        androidMain {
            dependencies {
                // зависимость на платформенную часть ktor-client будет добавлена автоматически
            }
        }
    }
}
```

Общую библиотеку также можно настроить в верхнеуровневом блоке `dependencies {}`. См. раздел о настройке зависимостей
на верхнем уровне в актуальной документации Kotlin Multiplatform.

### Библиотека, используемая в конкретных source set'ах

Если мультиплатформенная библиотека нужна только в отдельных source set'ах, добавьте её только туда. В таком случае
объявления из этой библиотеки будут доступны только в указанных source set'ах.

В таких случаях используйте общее имя библиотеки, а не платформенно-специфичное. Например, для SQLDelight в примере ниже
нужно использовать `native-driver`, а не `native-driver-iosx64`. Точное имя смотрите в документации библиотеки.

```kotlin
kotlin {
    //...
    sourceSets {
        commonMain.dependencies {
            // kotlinx.coroutines будет доступна во всех source set'ах
            implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.10.2")
        }
        androidMain.dependencies {
        }
        iosMain.dependencies {
            // SQLDelight будет доступна только в iOS source set, но не в Android или common
            implementation("com.squareup.sqldelight:native-driver:2.3.2")
        }
        wasmJsMain.dependencies {
        }
    }
}
```

```groovy
kotlin {
    //...
    sourceSets {
        commonMain {
            dependencies {
                // kotlinx.coroutines будет доступна во всех source set'ах
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.10.2'
            }
        }
        androidMain {
            dependencies {}
        }
        iosMain {
            dependencies {
                // SQLDelight будет доступна только в iOS source set, но не в Android или common
                implementation 'com.squareup.sqldelight:native-driver:2.3.2'
            }
        }
        wasmJsMain {
            dependencies {}
        }
    }
}
```

## Зависимость на другой мультиплатформенный проект

Один мультиплатформенный проект можно подключить к другому как зависимость. Для этого добавьте project dependency в тот
source set, которому она нужна. Если зависимость должна использоваться во всех source set'ах, добавьте её в общий source
set. В этом случае остальные source set'ы автоматически получат свои версии зависимости.

```kotlin
kotlin {
    //...
    sourceSets {
        commonMain.dependencies {
            implementation(project(":some-other-multiplatform-module"))
        }
        androidMain.dependencies {
            // платформенная часть :some-other-multiplatform-module будет добавлена автоматически
        }
    }
}
```

```groovy
kotlin {
    //...
    sourceSets {
        commonMain {
            dependencies {
                implementation project(':some-other-multiplatform-module')
            }
        }
        androidMain {
            dependencies {
                // платформенная часть :some-other-multiplatform-module будет добавлена автоматически
            }
        }
    }
}
```

## Что дальше?

Изучите другие материалы о добавлении зависимостей в мультиплатформенных проектах:

* [Добавление Android-зависимостей](https://kotlinlang.org/docs/multiplatform/multiplatform-android-dependencies.html)
* [Добавление iOS-зависимостей](https://kotlinlang.org/docs/multiplatform/multiplatform-ios-dependencies.html)
* [Добавление зависимостей в проект Compose Multiplatform для iOS, Android, desktop и web](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-dependencies.html)
