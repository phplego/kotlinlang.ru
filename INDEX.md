---
type: doc
layout: index
title: "Язык программирования Kotlin"
menuLabel: "Главная страница"
url: https://kotlinlang.ru
---

## Руководство по языку Kotlin

Здесь мы собираем ресурсы по Котлину и переводим документацию. Сообщество открыто для новых участников - любого, кто может переводить и проверять перевод. Редактирование текста происходит по принципу Википедии, с той лишь разницей, что тексты и структура меню хранятся в [Git](https://github.com/phplego/kotlinlang.ru).

> ***Внимание.*** Данный сайт (kotlinlang.ru) поддерживается усилиями энтузиастов и не является официальным сайтом языка Kotlin.

## Источники

* [Официальный сайт языка Kotlin](https://kotlinlang.org) (англ.)
* [Исходный код компилятора на GitHub](https://github.com/JetBrains/kotlin)
* [Исходники англ. документации](https://github.com/JetBrains/kotlin-web-site/tree/master/docs/topics)
* [Раздел на reddit.com, посвященный языку Kotlin](https://www.reddit.com/r/Kotlin)

## Сообщество

* [@KotlinLangRu](https://t.me/KotlinLangRu) - Telegram-чат, посвященный языку Kotlin и переводу документации ([правила чата](https://kotlinlang.ru/etc/chat-rules.html)).
* [Одержимый ИИ](https://t.me/ObsessedAI) - Telegram-канал про ИИ-инструменты, обучение и рабочие практики, которые могут быть полезны Kotlin-разработчику.


## Актуальные задачи

Сейчас особенно важно закрывать незавершённые статьи, обновлять устаревшие рекомендации и сверять старые переводы с
актуальной документацией. Лучше всего начинать с этих задач.

* **Незавершённые страницы перевода.**
  В первую очередь нужна помощь со статьями, где вместо содержимого пока осталась заготовка. Пример:
  [Adding dependencies on multiplatform libraries](https://kotlinlang.ru/docs/multiplatform-add-dependencies.html).
* **Статьи с устаревшими рекомендациями.**
  В [Gradle](https://kotlinlang.ru/docs/gradle.html) и [Maven](https://kotlinlang.ru/docs/maven.html) до сих пор
  описана экспериментальная поддержка coroutines в Kotlin 1.2 и настройка через `experimental { coroutines 'enable' }`
  и `<experimentalCoroutines>enable</experimentalCoroutines>`. Такие места нужно находить и обновлять в первую очередь.
* **Раздел про корутины и связанные страницы.**
  В [Coroutines](https://kotlinlang.ru/docs/coroutines.html) всё ещё обсуждаются `kotlin.coroutines.experimental`,
  старый экспериментальный статус coroutines и прекращение поддержки experimental-версии в Kotlin 1.4. Это уже
  исторический материал, который сегодня требует аккуратной переработки или явной переупаковки.
* **Базовые статьи, переведённые по версиям 2021–2022 годов.**
  У ряда ключевых страниц в комментариях прямо указано, по какой версии делался перевод: например,
  [Basic syntax](https://kotlinlang.ru/docs/basic-syntax.html) от 13 September 2021,
  [Classes](https://kotlinlang.ru/docs/classes.html) от 29 November 2021,
  [Functions](https://kotlinlang.ru/docs/functions.html) от 10 November 2021 и
  [Null safety](https://kotlinlang.ru/docs/null-safety.html) от 08 July 2021. Их полезно регулярно сверять с текущей
  англоязычной документацией и обновлять по мере расхождений.

Если хотите подключиться, выбирайте любую из этих задач, правьте статью через GitHub и приходите в
[@KotlinLangRu](https://t.me/KotlinLangRu), если нужна координация или ревью.
