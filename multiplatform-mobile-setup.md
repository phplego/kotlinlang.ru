---
type: doc
layout: reference
title: "Настройка среды"
url: https://kotlinlang.ru/docs/multiplatform-mobile-setup.html
---

<!-- При переводе статьи оригинальная версия была от 16 February 2022 -->

<!-- Set up an environment -->
# Настройка среды

<!-- Before you begin [creating your first application](multiplatform-mobile-create-first-app.md) to work on both iOS and Android, start by setting up an environment for Kotlin Multiplatform Mobile development: -->
Прежде чем приступить к [созданию своего первого приложения](multiplatform-mobile-create-first-app.html) для работы как
на iOS, так и на Android, начните с настройки среды для Kotlin Multiplatform Mobile разработки:

<!-- 1. If you are going to work with shared code or Android-specific code, you can work on any computer with an operating 
   system supported by [Android Studio](https://developer.android.com/studio).  
   If you also want to write iOS-specific code and run an iOS application on a simulated or real device, use a Mac with a 
   macOS. These steps cannot be performed on other operating systems, such as Microsoft Windows. This is due to an Apple requirement.
2. Install [Android Studio](https://developer.android.com/studio) 4.2 or 2020.3.1 Canary 8 or higher.   
    You will use Android Studio for creating your multiplatform applications and running them on simulated or hardware devices.
3. If you need to write iOS-specific code and run an iOS application, install [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
    –  version 11.3 or higher.                                                                                                                                                                                                                                                                                                                          
    Most of the time, Xcode will work in the background. You will use it to add Swift or Objective-C code to your iOS application.
4. Make sure that you have a [compatible Kotlin plugin](multiplatform-mobile-plugin-releases.md#release-details) installed.  
    In Android Studio, select **Tools** | **Kotlin** | **Configure Kotlin Plugin Updates** and check the current Kotlin 
    plugin version. If needed, update to the latest version in the **Stable** update channel.
5. Install the *Kotlin Multiplatform Mobile* plugin.  
    In Android Studio, select  **Preferences** | **Plugins**, search for the plugin *Kotlin Multiplatform Mobile* in 
    **Marketplace** and install it.
    
    ![Kotlin Multiplatform Mobile plugin](mobile-multiplatform-plugin.png){width=500}
    
    Check out [Kotlin Multiplatform Mobile plugin release notes](multiplatform-mobile-plugin-releases.md).
    
6. Install the [JDK](https://www.oracle.com/java/technologies/javase-downloads.html) if you haven't already done so.  
    To check if it's installed, run the command `java -version` in the Terminal. -->

1. Если вы собираетесь работать с общим кодом или кодом, специфичным для Android, вы можете работать на любом компьютере
    с операционной системой, поддерживаемой [Android Studio](https://developer.android.com/studio).

    Если вы также хотите написать код, специфичный для iOS, и запускать приложение на моделируемом или реальном
    устройстве, используйте Mac с macOS. Работать с iOS в других операционных системах, таких как Microsoft Windows,
    нельзя. Это связано с требованием Apple.

2. Установите [Android Studio](https://developer.android.com/studio) 4.2 или 2020.3.1 Canary 8 или выше.

    Вы будете использовать Android Studio для создания своих мультиплатформенных приложений и запуска их на имитируемых
    или аппаратных устройствах.

3. Если вам нужно написать код для iOS и запускать приложения, установите [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
    версии 11.3 или выше.

    Большую часть времени Xcode будет работать в фоновом режиме. Вы будете использовать его для добавления кода на Swift
    или Objective-C в свое приложение.

4. Убедитесь, что у вас установлен [совместимый плагин Kotlin](multiplatform-mobile-plugin-releases.html#release-details).

    В Android Studio выберите **Tools** | **Kotlin** | **Configure Kotlin Plugin Updates** и проверьте текущую версию
    плагина Kotlin. При необходимости обновите до последней **Stable** версии.

5. Установите плагин *Kotlin Multiplatform Mobile*.

    В Android Studio выберите **Preferences** | **Plugins**, найдите плагин *Kotlin Multiplatform Mobile* в
    **Marketplace** и установите его.

    <img src="https://kotlinlang.org/docs/images/mobile-multiplatform-plugin.png" alt="Плагин Kotlin Multiplatform Mobile" title="Плагин Kotlin Multiplatform Mobile">

    Посмотрите [Kotlin Multiplatform Mobile plugin release notes](multiplatform-mobile-plugin-releases.html).

<!-- Now it's time to [create your first cross-platform mobile application](multiplatform-mobile-create-first-app.md). -->
Теперь вы можете [создать ваше первое кроссплатформенное мобильное приложение](multiplatform-mobile-create-first-app.html).
