---
type: doc
layout: reference
title: "Создание кроссплатформенного приложения"
url: https://kotlinlang.ru/docs/multiplatform-mobile-create-first-app.html
---

<!-- Статья актуализирована по Create your Kotlin Multiplatform app от 21 May 2026: https://kotlinlang.org/docs/multiplatform/multiplatform-create-first-app.html -->

<!-- Create your Kotlin Multiplatform app -->
# Создание кроссплатформенного приложения

В этом руководстве вы создадите и запустите первое Kotlin Multiplatform-приложение с общей логикой и нативным UI для
Android и iOS. Kotlin Multiplatform позволяет разделять код между платформами и писать platform-specific-код только там,
где нужен нативный UI или доступ к API конкретной платформы.

Перед началом выполните шаги из [quickstart](https://kotlinlang.org/docs/multiplatform/quickstart.html), чтобы
[настроить окружение](multiplatform-mobile-setup.html).

<a name="create-a-project"></a>

## Создание проекта

1. В IntelliJ IDEA выберите **File | New | Project**.
2. В панели слева выберите **Kotlin Multiplatform**.
3. Укажите имя проекта, например `GreetingKMP`, и Project ID, например `com.jetbrains.greetingkmp`.
4. Выберите цели **Android** и **iOS**.
5. Для iOS выберите вариант **Do not share UI**, чтобы сохранить UI нативным.
6. После заполнения полей нажмите **Create**.

IDE может предложить обновить Android Gradle plugin до последней версии. Для KMP-проектов сначала проверьте
[таблицу совместимости](https://kotlinlang.org/docs/multiplatform/multiplatform-compatibility-guide.html), потому что
не каждая новая версия AGP сразу совместима с Kotlin Multiplatform.

<a name="examine-the-project-structure"></a>

## Структура проекта

Проект с общей логикой и нативным UI обычно содержит такие модули:

* `androidApp` - Kotlin-модуль, который собирается в Android-приложение и использует общий модуль как обычную
  Android-библиотеку.
* `iosApp` - Xcode-проект для iOS-приложения. Он зависит от общего модуля, который экспортируется как iOS framework.
* `sharedLogic` - мультиплатформенный модуль с общей логикой для Android и iOS.
* `sharedUI` - модуль с Compose Multiplatform UI-кодом. В проекте с нативным iOS UI он может использоваться только
  Android-приложением, но остаётся мультиплатформенным модулем.

Все модули, кроме `iosApp`, используют Gradle.

Исходные наборы `commonMain`, `androidMain` и `iosMain` позволяют разделять общий и platform-specific-код.
`commonMain` содержит код, независимый от платформы. В `androidMain` можно использовать Kotlin/JVM и Android API, а в
`iosMain` - Kotlin/Native и iOS API.

<a name="write-common-declarations"></a>

## Общие объявления

Общий код находится в `sharedLogic/src/commonMain`. Он должен быть platform-independent. Если в общем исходном наборе
попытаться использовать platform-specific API, IDE покажет предупреждение.

Для доступа к platform-specific-функциям используйте механизм `expect`/`actual`. Например, общий исходный набор может
объявить интерфейс платформы:

```kotlin
interface Platform {
    val name: String
}
```

Платформенные исходные наборы предоставляют реализации:

```kotlin
// Platform.android.kt в androidMain
import android.os.Build

class AndroidPlatform : Platform {
    override val name: String = "Android ${Build.VERSION.SDK_INT}"
}
```

```kotlin
// Platform.ios.kt в iosMain
import platform.UIKit.UIDevice

class IOSPlatform : Platform {
    override val name: String =
        UIDevice.currentDevice.systemName() + " " + UIDevice.currentDevice.systemVersion
}
```

Общая функция может быть объявлена как `expect`, а платформенные реализации - как `actual`:

```kotlin
// Platform.kt в commonMain
expect fun getPlatform(): Platform
```

```kotlin
// Platform.android.kt в androidMain
actual fun getPlatform(): Platform = AndroidPlatform()
```

```kotlin
// Platform.ios.kt в iosMain
actual fun getPlatform(): Platform = IOSPlatform()
```

`expect` и `actual` объявления должны находиться в одном пакете. При сборке для конкретной платформы компилятор
объединяет их в одно объявление с нужной реализацией.

<a name="run-your-application"></a>

## Запуск приложения

Вы можете запускать Android- и iOS-приложения из IntelliJ IDEA.

<a name="run-your-application-on-android"></a>

### Android

1. В списке run configurations выберите `androidApp`.
2. Выберите Android Virtual Device рядом со списком конфигураций и нажмите **Run**.

Если устройства нет в списке, создайте
[новый Android Virtual Device](https://developer.android.com/studio/run/managing-avds#createavd).

<a name="run-your-application-on-ios"></a>

### iOS

Если вы ещё не запускали Xcode после установки или обновления, сделайте это перед запуском iOS-приложения.

В IntelliJ IDEA выберите `iosApp` в списке run configurations, выберите симулятор рядом с конфигурацией и нажмите
**Run**. Если доступной iOS-конфигурации нет, запустите Xcode, чтобы обновить список симуляторов, и перезапустите IDE.

<a name="run-on-a-real-ios-device"></a>

### Реальное iOS-устройство

Для запуска на реальном iPhone нужен Team ID, связанный с вашим Apple ID.

Сначала настройте команду в Xcode:

1. Откройте проект в Xcode через **File | Open Project in Xcode**.
2. В Project navigator выберите `iosApp`.
3. Выберите target `iosApp` и откройте вкладку **Signing & Capabilities**.
4. В списке **Team** выберите свою команду.
5. Убедитесь, что Bundle Identifier уникален и Signing Certificate назначен успешно.

После настройки команды можно выбрать или изменить её в IntelliJ IDEA в настройках run configuration для `iosApp`.

Подключите iPhone кабелем. Если устройство уже зарегистрировано в Xcode, оно появится в списке доступных устройств для
конфигурации `iosApp`. Если нет, следуйте
[рекомендациям Apple](https://developer.apple.com/documentation/xcode/running-your-app-in-simulator-or-on-a-device):
включите Developer Mode на устройстве, откройте **Window | Devices and Simulators** в Xcode и завершите pairing.

<a name="next-step"></a>

## Следующий шаг

Дальше можно перейти к обновлению UI с помощью platform-specific-библиотек:
[Update the user interface](https://kotlinlang.org/docs/multiplatform/multiplatform-update-ui.html).

См. также:

* [Создание и запуск мультиплатформенных тестов](https://kotlinlang.org/docs/multiplatform/multiplatform-run-tests.html).
* [Структура Kotlin Multiplatform-проекта](https://kotlinlang.org/docs/multiplatform/multiplatform-discover-project.html).
* [Миграция существующего Android-приложения в Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform/multiplatform-integrate-in-existing-app.html).
