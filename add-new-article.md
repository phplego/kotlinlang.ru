---
type: doc
layout: inner
title: "Создание новой статьи"
url: https://kotlinlang.ru/docs/reference/add-new-article.html
---

## Создание новой статьи

Для создания новой статьи нужно выполнить два действия:

* Добавить пункт меню в файл `MENU.json`. То есть отредактировать `MENU.json` https://github.com/phplego/kotlinlang.ru/blob/master/MENU.json 
* Создать файл с соответствующим именем `<article-id>.md`. Или скопировать соответствующий файл из [английской документации](https://github.com/JetBrains/kotlin-web-site/tree/master/pages/docs/reference).

Оригинальные тексты документации на английском находятся тут:
https://github.com/JetBrains/kotlin-web-site/tree/master/pages/docs/reference

Ну и конечно же после любых изменений нужно:
* Сделать фиксацию изменений в git
* Если вы работаете с локальной копией, то сделать push
* Выгрузить файлы на сервер kotlinlang.ru перейдя по урлу: http://kotlinlang.ru/update


### У меня нет доступа на редактирование, что делать?

В этом случае есть два варианта:
* Попросить доступ в чате https://telegram.me/KotlinLangRu
* Сделать Pull Request ваших изменений, и мы их одобрим (или отклоним) в ближайшее время

.
