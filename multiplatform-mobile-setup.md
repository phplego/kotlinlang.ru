---
type: doc
layout: reference
title: "Настройка среды"
url: https://kotlinlang.ru/docs/multiplatform-mobile-setup.html
---

<!-- Статья актуализирована по Kotlin Multiplatform quickstart от 15 May 2026: https://kotlinlang.org/docs/multiplatform/quickstart.html -->

<!-- Set up an environment -->
# Настройка среды

Старая документация Kotlin Multiplatform Mobile заменена общим руководством Kotlin Multiplatform. Прежде чем создавать
первый KMP-проект, настройте IDE и необходимые инструменты.

1. Выберите и установите IDE. Kotlin Multiplatform полностью поддерживается в
   [IntelliJ IDEA](https://www.jetbrains.com/idea/) и [Android Studio](https://developer.android.com/studio).

   Для установки JetBrains рекомендует [JetBrains Toolbox App](https://www.jetbrains.com/toolbox-app/): он позволяет
   управлять несколькими версиями IDE, включая EAP- и Nightly-сборки.

   Плагинам Kotlin Multiplatform нужна как минимум IntelliJ IDEA 2025.2.2 или Android Studio Otter 2025.2.1.

2. Установите [Kotlin Multiplatform IDE plugin](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform).

   Плагин также установит зависимости, которых ещё нет в вашей IDE.

3. Если переменная окружения `ANDROID_HOME` не задана, настройте её так, чтобы система находила Android SDK.

   Для macOS/Linux добавьте команду в `.profile` или `.zprofile`:

   ```bash
   export ANDROID_HOME=~/Library/Android/sdk
   ```

   Для PowerShell можно задать постоянную переменную окружения так:

   ```powershell
   [Environment]::SetEnvironmentVariable('ANDROID_HOME', '<path to the SDK>', 'Machine')
   ```

   Для CMD используйте `setx`:

   ```cmd
   setx ANDROID_HOME "<path to the SDK>"
   ```

4. Для создания iOS-приложений нужен macOS-хост с установленным [Xcode](https://apps.apple.com/us/app/xcode/id497799835).
   IDE будет запускать Xcode под капотом, чтобы собирать iOS-фреймворки.

   Запустите Xcode хотя бы один раз до работы с KMP-проектами, чтобы он прошёл первичную настройку. После обновлений
   Xcode его также нужно запускать вручную, чтобы загрузить обновлённые инструменты. Kotlin Multiplatform IDE plugin
   выполняет предварительные проверки и сообщает, если Xcode не готов к работе.

5. После настройки окружения создайте проект через мастер IDE: выберите **File | New | Project** и шаблон
   **Kotlin Multiplatform**.

   Для первого демо-проекта можно выбрать Android, iOS, Desktop, Web и Server. Там, где доступна опция **Share UI**,
   оставьте её включённой, чтобы использовать Compose Multiplatform как UI-фреймворк.

Проверить проблемы окружения можно в окне **Project Environment Preflight Checks**. Оно показывает результаты проверок,
позволяет перезапустить их и изменить настройки.

Дальше можно перейти к [созданию Compose Multiplatform-приложения](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-create-first-app.html)
или к [приложению с общей логикой и нативным UI](https://kotlinlang.org/docs/multiplatform/multiplatform-create-first-app.html).
