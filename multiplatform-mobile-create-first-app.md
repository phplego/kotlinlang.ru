---
type: doc
layout: reference
title: "Создание вашего первого кроссплатформенного мобильного приложения"
url: https://kotlinlang.ru/docs/multiplatform-mobile-create-first-app.html
---

<!-- При переводе статьи оригинальная версия была от 28 March 2022 -->

<!-- Create your first cross-platform mobile app – tutorial -->
# Создание вашего первого кроссплатформенного мобильного приложения

<!-- Here you will learn how to create and run your first Kotlin Multiplatform Mobile application. -->
Здесь вы узнаете, как создать и запустить ваше первое Kotlin Multiplatform Mobile приложение.

Вы также можете посмотреть видео этого урока, созданное Екатериной Петровой.

<iframe width="560" height="315" src="https://www.youtube.com/embed/GcqFhoUuNNI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<!-- 1. [Set up an environment](multiplatform-mobile-setup.md) for cross-platform mobile development by installing the necessary tools on a suitable operating system.  

    >You will need a Mac with macOS to complete certain steps in this tutorial, which include writing iOS-specific code and running an iOS application.  
    >These steps cannot be performed on other operating systems, such as Microsoft Windows. This is due to an Apple requirement.
    >
    {type="note"}

You can also watch a video of this tutorial created by Ekaterina Petrova, Kotlin Developer Advocate.

<video href="GcqFhoUuNNI" title="Kotlin Multiplatform Multiverse, Episode 2: Your First Kotlin Multiplatform Mobile App Tutorial"/>

2. In Android Studio, select **File** | **New** | **New Project**.
3. Select **Kotlin Multiplatform App** in the list of project templates, and click **Next**.  

    ![Mobile Multiplatform project template](multiplatform-mobile-project-wizard-1.png)
    
4. Specify a name for your first application, and click **Next**.  

    ![Mobile Multiplatform project - general settings](multiplatform-mobile-project-wizard-2.png)

5. In the window that opens, do the following:
   * Keep the default names for the application and shared folders.
   * Select the checkbox to generate sample tests for your project. 
   * Select **Regular framework** in the list of iOS framework distribution options. 
     
   Click **Finish** to create a new project.

    ![Mobile Multiplatform project - additional settings](multiplatform-mobile-project-wizard-3.png)
    
    > If you want to use Kotlin Multiplatform module as a CocoaPods dependency, select the **CocoaPods dependency manager** option.
    > To learn more about CocoaPods dependencies, see [CocoaPods integration](native-cocoapods.md).
    >
    {type="note"} -->

1. [Настройте среду](multiplatform-mobile-setup.html) для кроссплатформенной мобильной разработки, установив необходимые
    инструменты для подходящей операционной системы.

    > Вам понадобится Mac с macOS для выполнения определенных шагов в этом руководстве, которые включают написание
    > специфичного для iOS кода и запуск iOS-приложений.
    >
    > Работать с iOS в других операционных системах, таких как Microsoft Windows, нельзя. Это связано с требованием
    > Apple.

2. В Android Studio выберите **File** | **New** | **New Project**.

3. Выберите **Kotlin Multiplatform App** в списке шаблонов проектов и нажмите **Next**.

    <img src="https://kotlinlang.org/docs/images/multiplatform-mobile-project-wizard-1.png" alt="Шаблон мобильного мультиплатформенного проекта" title="Шаблон мобильного мультиплатформенного проекта">

4. Укажите имя для вашего первого приложения и нажмите **Next**.

    <img src="https://kotlinlang.org/docs/images/multiplatform-mobile-project-wizard-2.png" alt="Мобильный мультиплатформенный проект - основные настройки" title="Мобильный мультиплатформенный проект - основные настройки">

5. В открывшемся окне выполните следующие действия:

    * Не изменяйте имена по умолчанию для приложения и общих папок;
    * Установите флажок, чтобы сгенерировать образцы тестов для вашего проекта;
    * Выберите **Regular framework** в списке вариантов распространения iOS-фреймворка.

    Нажмите **Finish**, чтобы создать новый проект.

    <img src="https://kotlinlang.org/docs/images/multiplatform-mobile-project-wizard-3.png" alt="Мобильный мультиплатформенный проект - дополнительные настройки" title="Мобильный мультиплатформенный проект - дополнительные настройки">

    > Если вы хотите использовать мультиплатформенный модуль Kotlin в качестве зависимости CocoaPods, выберите опцию
    > **CocoaPods dependency manager**. Чтобы узнать больше о зависимостях CocoaPods, см. раздел [Интеграция CocoaPods](native-cocoapods.html).

<!-- Now wait while your project is set up. It may take some time to download and set up the required components when you 
do this for the first time. -->
Теперь подождите, пока ваш проект будет настроен. Загрузка и настройка необходимых компонентов может занять некоторое
время, когда вы делаете это в первый раз.

<!-- To view the complete structure of your mobile multiplatform project, switch the view from **Android** to **Project**. 
You can [understand the project structure](multiplatform-mobile-understand-project-structure.md) and how you can use this.  -->
Чтобы просмотреть полную структуру вашего мобильного мультиплатформенного проекта, переключите просмотр с **Android** на
**Project**. Вы можете [разобраться со структурой проекта](multiplatform-mobile-understand-project-structure.html) и
тем, как вы можете её использовать.

<img src="https://kotlinlang.org/docs/images/select-project-view.png" alt="Выбор вида проекта" title="Выбор вида проекта">

<a name="run-your-application"></a>

<!-- ## Run your application -->
## Запуск вашего приложения

<!-- You can run your multiplatform application on [Android](#run-your-application-on-android) or [iOS](#run-your-application-on-ios). -->
Вы можете запустить свое мультиплатформенное приложение на [Android](#run-your-application-on-android) или [iOS](#run-your-application-on-ios).

<a name="run-your-application-on-android"></a>

<!-- ### Run your application on Android -->
### Запуск вашего приложения на Android

<!-- * In the list of run configurations, select **androidApp** and then click **Run**.  
    
    ![Run multiplatform app on Android](run-android.png){width=400}
    
    ![First mobile multiplatform app on Android](first-multiplatform-project-on-android-1.png){width=300} -->

* В списке конфигураций запуска выберите **androidApp** и нажмите **Run**.

    <img src="https://kotlinlang.org/docs/images/run-android.png" alt="Запуск мультиплатформенного приложения на Android" title="Запуск мультиплатформенного приложения на Android">

    <img src="https://kotlinlang.org/docs/images/first-multiplatform-project-on-android-1.png" alt="Первое мультиплатформенное приложение на Android" title="Первое мультиплатформенное приложение на Android">

<a name="run-on-a-different-android-simulated-device"></a>

<!-- #### Run on a different Android simulated device -->
#### Запуск эмулятора другого Android-устройства

<!-- Learn how to [configure the Android Emulator and run your application on a different simulated device](https://developer.android.com/studio/run/emulator#runningapp). -->
Узнайте, как [настроить эмулятор Android и запустить ваше приложение на другом моделируемом устройстве](https://developer.android.com/studio/run/emulator#runningapp).

<a name="run-on-a-real-android-device"></a>

<!-- #### Run on a real Android device -->
#### Запуск на реальном Android-устройстве

<!-- Learn how to [configure and connect a hardware device and run your application on it](https://developer.android.com/studio/run/device). -->
Узнайте, как [настроить и подключить аппаратное устройство и запустить на нем свое приложение](https://developer.android.com/studio/run/device).

<a name="run-your-application-on-ios"></a>

<!-- ### Run your application on iOS -->
### Запуск вашего приложения на iOS

<!-- * In the list of run configurations, select **iosApp** and then click **Run**.  
    
    ![Run multiplatform app on iOS](run-ios.png){width=450}
    
    ![First mobile multiplatform app on Android](first-multiplatform-project-on-ios-1.png){width=300} -->

* В списке конфигураций запуска выберите **iosApp** и нажмите **Run**.

    <img src="https://kotlinlang.org/docs/images/run-ios.png" alt="Запуск мультиплатформенного приложения на iOS" title="Запуск мультиплатформенного приложения на iOS">

    <img src="https://kotlinlang.org/docs/images/first-multiplatform-project-on-ios-1.png" alt="Первое мультиплатформенное приложение на iOS" title="Первое мультиплатформенное приложение на iOS">

<a name="run-on-a-different-iphone-simulated-device"></a>

<!-- #### Run on a different iPhone simulated device -->
#### Запуск эмулятора другого iPhone

<!-- If you want to run your application on another simulated device, you can add a new run configuration. -->
Если вы хотите запустить свое приложение на другом симулируемом устройстве, вы можете добавить новую конфигурацию
запуска.

<!-- 1. In the list of run configurations, click **Edit Configurations**.

    ![Edit run configurations](ios-edit-configurations.png){width=450}

2. Click the **+** button above the list of configurations and select **iOS Application**.

    ![New run configuration for iOS application](ios-new-configuration.png)

4. Name your configuration.

5. Select a simulated device in the **Execution target** list, and then click **OK**.

    ![New run configuration with iOS simulator](ios-new-simulator.png)
    
6. Click **Run** to run your application on the new simulated device. -->

1. В списке конфигураций запуска нажмите **Edit Configurations**.

    <img src="https://kotlinlang.org/docs/images/ios-edit-configurations.png" alt="Редактирование конфигурации запуска" title="Редактирование конфигурации запуска">

2. Нажмите кнопку **+** над списком конфигураций и выберите **iOS Application**.

    <img src="https://kotlinlang.org/docs/images/ios-new-configuration.png" alt="Новая конфигурация запуска для iOS-приложения" title="Новая конфигурация запуска для iOS-приложения">

3. Укажите название вашей конфигурации.

4. Выберите симулируемое устройство в списке **Execution target**, а затем нажмите **OK**.

    <img src="https://kotlinlang.org/docs/images/ios-new-simulator.png" alt="Новая конфигурация запуска с симулятором iOS" title="Новая конфигурация запуска с симулятором iOS">

5. Нажмите **Run**, чтобы запустить ваше приложение на новом симулируемом устройстве.

<a name="run-on-a-real-iphone-device"></a>

<!-- #### Run on a real iPhone device -->
#### Запуск на реальном iPhone

<!-- 1. [Connect a real iPhone device to Xcode](https://developer.apple.com/documentation/xcode/running_your_app_in_the_simulator_or_on_a_device).
2. [Create a run configuration](#run-on-a-different-iphone-simulated-device) by selecting iPhone in the **Execution target** list.
3. Click **Run** to run your application on the iPhone device. -->

1. [Подключите iPhone к Xcode](https://developer.apple.com/documentation/xcode/running_your_app_in_the_simulator_or_on_a_device).
2. [Создайте конфигурацию запуска](#run-on-a-different-iphone-simulated-device), выбрав iPhone в списке **Execution
target**.
3. Нажмите **Run**, чтобы запустить ваше приложение на iPhone.

<!-- > If your build fails, follow the workaround described in [this issue](https://youtrack.jetbrains.com/issue/KT-40907). -->
> Если ваша сборка завершится неудачей, следуйте инструкциям, описанным в [этой issue](https://youtrack.jetbrains.com/issue/KT-40907).

<a name="run-tests"></a>

<!-- ## Run tests -->
## Запуск тестов

<!-- You can run tests to check that the shared code works correctly on both platforms. Of course, you can also write and run tests to check the 
platform-specific code. -->
Вы можете запустить тесты, чтобы проверить правильность работы общего кода на обеих платформах. Конечно, вы также можете
писать и запускать тесты для проверки кода, специфичного для конкретной платформы.

<a name="run-tests-on-ios"></a>

<!-- ### Run tests on iOS -->
### Запуск тестов на iOS

<!-- 1. Open the file `iosTest.kt` in `shared/src/iosTest/kotlin/com.example.kmmapplication.shared`.  
    Directories with `Test` in their name contain tests.  
    This file includes a sample test for iOS.  
    
    ![iOS test Kotlin file](ios-test-kt.png)
   
2. Click the **Run** icon in the gutter next to the test. -->

1. Откройте файл `iosTest.kt` в папке `shared/src/iosTest/kotlin/com.example.kmmapplication.shared`. Каталоги с именем
`Test` содержат тесты. Этот файл содержит пример теста для iOS.

    <img src="https://kotlinlang.org/docs/images/ios-test-kt.png" alt="Тестовый Kotlin-файл для iOS" title="Тестовый Kotlin-файл для iOS">

2. Нажмите на значок **Run** рядом с тестом.

<!-- Tests run on a simulator without UI. Congratulations! The test has passed – see test results in the console. -->
Тесты выполняются на симуляторе без UI. Поздравляем! Тест пройден – смотрите результаты теста в консоли.

<img src="https://kotlinlang.org/docs/images/ios-test-result.png" alt="Результаты iOS-теста" title="Результаты iOS-теста">

<a name="run-tests-on-android"></a>

<!-- ### Run tests on Android -->
### Запуск тестов на Android

<!-- For Android, follow a procedure that is very similar to the one for running tests on iOS. -->
Для Android процесс запуска тестов очень похож на этот же процесс для iOS.

<!-- 1. Open the file `androidTest.kt` in `shared/src/androidTest/kotlin/com.example.kmmapplication.shared`.

2. Click the **Run** gutter icon next to the test. -->

1. Откройте файл `androidTest.kt` в папке `shared/src/androidTest/kotlin/com.example.kmmapplication.shared`.

2. Нажмите на значок **Run** рядом с тестом.

<a name="update-your-application"></a>

<!-- ## Update your application -->
## Обновление вашего приложения

<!-- 1. Open the file `Greeting.kt` in `shared/src/commonMain/kotlin/com.example.kmmapplication.shared`.  
    This directory stores the shared code for both platforms – Android and iOS. If you make changes to the shared code, you will see
    changes in both applications.

    ![Common Kotlin file](common-kotlin-file.png)
    
2. Update the shared code – use the Kotlin standard library function that works on all platforms and reverts text: `reversed()`.

    ```kotlin
    class Greeting {
        fun greeting(): String {
            return "Guess what it is! > ${Platform().platform.reversed()}!"
        }
    }
    ```

3. Run the updated application on Android.

    ![Updated mobile multiplatform app on Android](first-multiplatform-project-on-android-2.png){width=300}
    
4. Run the updated application on iOS.  

    ![Updated mobile multiplatform app on iOS](first-multiplatform-project-on-ios-2.png){width=300}
    
5. Run tests on Android and iOS.  
    As you see, the tests fail. Update the tests to pass. You know how to do this, right? ;)
    
    ![iOS test failed](ios-test-failed.png) -->

1. Откройте файл `Greeting.kt` в папке `shared/src/commonmain/kotlin/com.example.kmmapplication.shared`. В этом каталоге
хранится общий код для обеих платформ – Android и iOS. Если вы внесете изменения в общий код, вы увидите изменения в
обоих приложениях.

    <img src="https://kotlinlang.org/docs/images/common-kotlin-file.png" alt="Общий Kotlin-файл" title="Общий Kotlin-файл">

2. Обновите общий код: используйте функцию стандартной Kotlin-библиотеки, которая работает на всех платформах и
возвращает текст: `reversed()`.

    ```kotlin
    class Greeting {
        fun greeting(): String {
            return "Guess what it is! > ${Platform().platform.reversed()}!"
        }
    }
    ```

3. Запустите обновленное приложение на Android.

    <img src="https://kotlinlang.org/docs/images/first-multiplatform-project-on-android-2.png" alt="Обновленное мобильное мультиплатформенное приложение на Android" title="Обновленное мобильное мультиплатформенное приложение на Android">

4. Запустите обновленное приложение на iOS.

    <img src="https://kotlinlang.org/docs/images/first-multiplatform-project-on-ios-2.png" alt="Обновленное мобильное мультиплатформенное приложение на iOS" title="Обновленное мобильное мультиплатформенное приложение на iOS">

5. Запустите тесты на Android и iOS.

    Как вы видите, тесты проваливаются. Обновите тесты для прохождения. Вы знаете, как это сделать, верно? ;)

    <img src="https://kotlinlang.org/docs/images/ios-test-failed.png" alt="iOS-тест провалился" title="iOS-тест провалился">

<a name="next-steps"></a>

<!-- ## Next steps -->
## Следующие шаги

<!-- Once you've played with your first cross-platform mobile application, you can: -->
Как только вы поиграете со своим первым кроссплатформенным мобильным приложением, вы сможете:

<!-- * [Understand the project structure](multiplatform-mobile-understand-project-structure.md)
* [Complete a tutorial on making your Android application work on iOS](multiplatform-mobile-integrate-in-existing-app.md) -->

* [Понимать структуру проекта](multiplatform-mobile-understand-project-structure.html),
* [Завершите руководство о запуске вашего Android-приложение на iOS](multiplatform-mobile-integrate-in-existing-app.html).
