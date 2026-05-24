---
type: doc
layout: reference
title: "Kotlin для анализа данных"
url: https://kotlinlang.ru/docs/data-science-overview.html
---

<!-- При переводе статьи оригинальная версия была от 13 May 2026 -->

<!-- # Kotlin for data analysis -->
# Kotlin для анализа данных

<!-- Exploring and analyzing data is something you may not do every day, but it's a crucial skill you need as a software developer. -->
Исследовать и анализировать данные приходится не каждый день, но это важный навык для разработчика.

<!-- Let's think about software development duties where data analysis is key: analyzing what's actually inside collections when debugging,
digging into memory dumps or databases, or receiving JSON files with large amounts of data when working with REST APIs, to mention some. -->
В разработке программного обеспечения анализ данных нужен во многих задачах: например, чтобы разобраться,
что на самом деле находится внутри коллекций во время отладки, изучить дампы памяти или базы данных,
или обработать JSON-файлы с большим объемом данных при работе с REST API.

<!-- With Kotlin's Exploratory Data Analysis (EDA) tools, such as [Kotlin notebooks](#notebooks), [Kotlin DataFrame](#kotlin-dataframe), and [Kandy](#kandy), you
have at your disposal a rich set of capabilities to enhance your analytics skills and support you across different scenarios: -->
Инструменты Kotlin для исследовательского анализа данных (Exploratory Data Analysis, EDA), такие как
[Kotlin notebooks](#notebooks), [Kotlin DataFrame](#kotlin-dataframe) и [Kandy](#kandy), дают богатый набор
возможностей, которые помогают развивать аналитические навыки и решать разные задачи:

<!-- * **Load, transform, and visualize data in various formats:** with our Kotlin EDA tools, you can perform tasks like filtering, sorting, and aggregating data. Our tools can seamlessly
read data right in the IDE from different data sources, such as CSV, JSON, SQL Databases, or Parquet files.
See all supported formats in the [DataFrame documentation](https://kotlin.github.io/dataframe/data-sources.html). -->
* **Загружать, преобразовывать и визуализировать данные в разных форматах:** с инструментами Kotlin EDA можно
  фильтровать, сортировать и агрегировать данные. Эти инструменты позволяют читать данные прямо в IDE из разных
  источников, таких как CSV, JSON, SQL-базы данных или файлы Parquet. Все поддерживаемые форматы перечислены в
  [документации DataFrame](https://kotlin.github.io/dataframe/data-sources.html).

  <!-- Kandy, our plotting tool, allows you to create a wide range of charts to visualize and gain insights from the dataset. -->
  Kandy, инструмент для построения графиков, позволяет создавать широкий набор диаграмм, визуализировать данные
  и находить закономерности в датасетах.

<!-- * **Efficiently analyze data stored in relational databases:** Kotlin DataFrame seamlessly integrates with databases and provides capabilities similar to SQL queries.
You can retrieve, manipulate, and visualize data directly from various databases. -->
* **Эффективно анализировать данные, хранящиеся в реляционных базах данных:** Kotlin DataFrame легко интегрируется
  с базами данных и предоставляет возможности, похожие на SQL-запросы. Вы можете получать, обрабатывать
  и визуализировать данные напрямую из разных баз данных.

<!-- * **Fetch and analyze real-time and dynamic datasets from web APIs:** the EDA tools' flexibility allows integration with external APIs via protocols like OpenAPI.
This feature helps you fetch data from web APIs, to then clean and transform the data to your needs. -->
* **Получать и анализировать динамические наборы данных и данные в реальном времени из веб-API:** гибкость
  инструментов EDA позволяет интегрироваться с внешними API через протоколы вроде OpenAPI. Так можно получать
  данные из веб-API, а затем очищать и преобразовывать их под свои задачи.

<!-- Our Kotlin data analysis tools let you smoothly handle your data from start to finish. Effortlessly
retrieve your data with simple drag-and-drop functionality in our Kotlin Notebook. Clean, transform, and visualize it with just a few lines of code.
Additionally, export your output charts in a matter of clicks. -->
Инструменты Kotlin для анализа данных помогают работать с данными на всех этапах. В Kotlin Notebook можно легко
загрузить данные простым перетаскиванием. Очистка, преобразование и визуализация выполняются всего несколькими
строками кода, а готовые графики можно экспортировать в несколько кликов.

![Kotlin Notebook](https://kotlinlang.org/docs/images/data-analysis-notebook.gif){width=700}

<!-- ## Notebooks -->
## Ноутбуки

<!-- A _notebook_ is an interactive document where you can mix executable Kotlin code with text, visualizations, and results.
Think of it as a Kotlin REPL extended with the ability to organize your code into cells, document it with Markdown,
and immediately display outputs (from text to plots) alongside the code that produced them. -->
_Ноутбук_ - это интерактивный документ, в котором можно сочетать исполняемый Kotlin-код с текстом, визуализациями
и результатами. Его можно представить как Kotlin REPL, дополненный возможностью разбивать код на ячейки,
документировать его с помощью Markdown и сразу показывать результаты - от текста до графиков - рядом с кодом,
который их создал.

<!-- Kotlin offers different notebook solutions, such as [Kotlin Notebook](#kotlin-notebook), [Datalore](#kotlin-notebooks-in-datalore),
and [Kotlin-Jupyter Notebook](#jupyter-notebook-with-kotlin-kernel), providing convenient features for data retrieving, transformation, exploration, modeling, and more.
These Kotlin notebook solutions are based on our [Kotlin Kernel](https://github.com/Kotlin/kotlin-jupyter). -->
Kotlin предлагает разные решения для ноутбуков: [Kotlin Notebook](#kotlin-notebook),
[Datalore](#kotlin-notebooks-in-datalore) и [Kotlin-Jupyter Notebook](#jupyter-notebook-with-kotlin-kernel).
Они предоставляют удобные возможности для получения, преобразования, исследования и моделирования данных.
Эти решения основаны на [Kotlin Kernel](https://github.com/Kotlin/kotlin-jupyter).

<!-- You can seamlessly share your code among Kotlin Notebook, Datalore, and Kotlin-Jupyter Notebook. Create a project in one of our Kotlin notebooks
and continue working in another notebook without compatibility issues. -->
Код можно без проблем переносить между Kotlin Notebook, Datalore и Kotlin-Jupyter Notebook. Создайте проект
в одном из Kotlin-ноутбуков и продолжайте работу в другом без проблем совместимости.

![Kotlin Notebook](https://kotlinlang.org/docs/images/kotlin-notebook.png){width=700}

<!-- ### Kotlin Notebook -->
### Kotlin Notebook

<!-- The [Kotlin Notebook](kotlin-notebook-overview.md) is a plugin for IntelliJ IDEA that allows you to create notebooks in Kotlin. It provides our IDE experience with all common IDE features,
offering real-time code insights and project integration. -->
[Kotlin Notebook](https://kotlinlang.org/docs/kotlin-notebook-overview.html) - это плагин для IntelliJ IDEA,
который позволяет создавать ноутбуки на Kotlin. Он переносит привычные возможности IDE в работу с ноутбуками:
подсказки и анализ кода в реальном времени, а также интеграцию с проектами.

<!-- ### Kotlin notebooks in Datalore -->
### Kotlin-ноутбуки в Datalore

<!-- With [Datalore](https://datalore.jetbrains.com/), you can use Kotlin in the browser straight out of the box without additional installation.
You can also share your notebooks and run them remotely, collaborate with other Kotlin notebooks in real-time,
receive smart coding assistance as you write code, and export results through interactive or static reports. -->
С [Datalore](https://datalore.jetbrains.com/) можно использовать Kotlin в браузере сразу, без дополнительной установки.
Также можно делиться ноутбуками и запускать их удаленно, совместно работать с другими Kotlin-ноутбуками в реальном
времени, получать умные подсказки при написании кода и экспортировать результаты в виде интерактивных или статических
отчетов.

<!-- ### Jupyter Notebook with Kotlin Kernel -->
### Jupyter Notebook с Kotlin Kernel

<!-- [Jupyter Notebook](https://jupyter.org/) is an open-source web application
that allows you to create and share documents containing code,
visualizations, and Markdown text.
[Kotlin-Jupyter](https://github.com/Kotlin/kotlin-jupyter) is an open-source project that brings Kotlin
support to Jupyter Notebook to harness Kotlin's power within the Jupyter environment. -->
[Jupyter Notebook](https://jupyter.org/) - это веб-приложение с открытым исходным кодом, которое позволяет создавать
документы с кодом, визуализациями и Markdown-текстом, а также делиться ими.
[Kotlin-Jupyter](https://github.com/Kotlin/kotlin-jupyter) - это проект с открытым исходным кодом, который добавляет
поддержку Kotlin в Jupyter Notebook и позволяет использовать возможности Kotlin в среде Jupyter.

<!-- ## Kotlin DataFrame -->
## Kotlin DataFrame

<!-- The [Kotlin DataFrame](https://kotlin.github.io/dataframe/overview.html) library lets you manipulate structured data in your Kotlin projects. From data creation and
cleaning to in-depth analysis and feature engineering, this library has you covered. -->
Библиотека [Kotlin DataFrame](https://kotlin.github.io/dataframe/overview.html) позволяет работать со структурированными
данными в Kotlin-проектах. Она подходит для разных этапов: от создания и очистки данных до глубокого анализа
и подготовки признаков.

<!-- With the Kotlin DataFrame library, you can work with different file formats, including CSV, JSON, XLS, and XLSX. This library also facilitates the data retrieval process
with its ability to connect with SQL databases or APIs.
See all supported formats in the [DataFrame documentation](https://kotlin.github.io/dataframe/data-sources.html). -->
С Kotlin DataFrame можно работать с разными файловыми форматами, включая CSV, JSON, XLS и XLSX. Библиотека также
упрощает получение данных благодаря возможности подключаться к SQL-базам данных и API. Все поддерживаемые форматы
перечислены в [документации DataFrame](https://kotlin.github.io/dataframe/data-sources.html).

![Kotlin DataFrame](https://kotlinlang.org/docs/images/data-analysis-dataframe-example.png){width=700}

<!-- ## Kandy -->
## Kandy

<!-- [Kandy](https://kotlin.github.io/kandy/welcome.html) is an open-source Kotlin library that provides a powerful and flexible DSL for plotting charts of various types.
This library is a simple, idiomatic, readable, and type-safe tool to visualize data. -->
[Kandy](https://kotlin.github.io/kandy/welcome.html) - это Kotlin-библиотека с открытым исходным кодом, которая
предоставляет мощный и гибкий DSL для построения диаграмм разных типов. Это простой, идиоматичный, читаемый
и типобезопасный инструмент для визуализации данных.

<!-- Kandy has seamless integration with Kotlin Notebook, Datalore, and Kotlin-Jupyter Notebook. You can also easily combine the Kandy and
Kotlin DataFrame libraries to complete different data-related tasks. -->
Kandy легко интегрируется с Kotlin Notebook, Datalore и Kotlin-Jupyter Notebook. Кроме того, Kandy удобно сочетать
с Kotlin DataFrame для решения разных задач, связанных с данными.

![Kandy](https://kotlinlang.org/docs/images/data-analysis-kandy-example.png){width=700}

<!-- ## What's next -->
## Что дальше

<!-- * [Retrieve and transform data using the Kotlin DataFrame library](data-analysis-work-with-data-sources.md)
* [Visualize data using the Kandy library](data-analysis-visualization.md)
* [Learn more about Kotlin and Java libraries for data analysis](data-analysis-libraries.md) -->
* [Получение и преобразование данных с помощью библиотеки Kotlin DataFrame](https://kotlinlang.org/docs/data-analysis-work-with-data-sources.html)
* [Визуализация данных с помощью библиотеки Kandy](https://kotlinlang.org/docs/data-analysis-visualization.html)
* [Подробнее о библиотеках Kotlin и Java для анализа данных](https://kotlinlang.org/docs/data-analysis-libraries.html)
