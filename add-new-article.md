---
type: doc
layout: inner
title: "Создание новой статьи"
url: https://kotlinlang.ru/docs/add-new-article.html
---

## Создание новой статьи

Для создания новой статьи нужно выполнить два действия:

* Добавить пункт меню в файл `MENU.yaml`. То есть отредактировать `MENU.yaml` https://github.com/phplego/kotlinlang.ru/blob/master/MENU.yaml 
* Создать файл с соответствующим именем `<article-id>.md`. Или скопировать соответствующий файл из [английской документации](https://github.com/JetBrains/kotlin-web-site/tree/master/docs/topics).

Оригинальные тексты документации на английском находятся тут:
https://github.com/JetBrains/kotlin-web-site/tree/master/docs/topics

Ну и конечно же после любых изменений нужно:
* Сделать фиксацию изменений в git
* Если вы работаете с локальной копией, то сделать push
* Выгрузить файлы на сервер kotlinlang.ru перейдя по урлу: http://kotlinlang.ru/update


### У меня нет доступа на редактирование, что делать?

В этом случае есть два варианта:
* Попросить доступ в чате https://telegram.me/KotlinLangRu
* Сделать Pull Request ваших изменений, и мы их одобрим (или отклоним) в ближайшее время
